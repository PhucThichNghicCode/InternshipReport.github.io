---
title: "Blog 3"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Improve PostgreSQL Performance using pgstattuple extension

*by **Vivek Singh**, **Kiran Singh**, and **Sagar Patel** | March 02, 2025 | at Advanced (300), Amazon Aurora, Amazon RDS, PostgreSQL compatible, RDS for PostgreSQL, Technical How-to*

---

As businesses continue to generate and store massive amounts of data, the need for efficient and reliable database management systems becomes increasingly critical. PostgreSQL, an open-source relational database management system (RDBMS), has established itself as a powerful solution for handling complex data requirements. One of PostgreSQL's key strengths lies in its **extensibility**. Through a rich ecosystem of extensions and plugins, developers can enhance the database's functionality to meet specific requirements. These extensions range from spatial data support and full-text search capabilities to advanced data types and performance optimization tools. While PostgreSQL offers a wide array of features and capabilities, one often overlooked extension is **pgstattuple**—a tool that can provide significant value in gaining insights into the inner workings of a PostgreSQL database.

In this post, we dive deep into **pgstattuple**—what insights it provides, how to use it to diagnose issues in Amazon Aurora PostgreSQL-Compatible Edition and Amazon Relational Database Service (Amazon RDS) for PostgreSQL, and best practices for harnessing its capabilities.

## Overview of pgstattuple

The **pgstattuple** extension provides a set of functions to query detailed statistics at the tuple (record) level in PostgreSQL tables and indexes. This allows for a deep look into the physical storage layer that standard PostgreSQL statistical views cannot provide.

Some table and index level metrics that pgstattuple provides include:

* **tuple_count** – Number of live tuples
* **dead_tuple_count** – Number of dead tuples not yet cleaned up
* **tuple_len** – Average length of live tuples in bytes
* **free_space** – Total free space available in bytes
* **free_percent** – Percentage of free space; higher values indicate more bloat
* **dead_tuple_len** – Total length of dead tuples in bytes
* **dead_tuple_percent** – Percentage of space occupied by dead tuples

These metrics are not just mere numbers—they are an early warning system for database health and performance issues. By monitoring these statistics, you can proactively identify storage issues that might be silently affecting your database performance. Whether it is excessive table bloat consuming disk space, or index fragmentation slowing down queries, pgstattuple helps detect these problems before they become critical incidents.

## Using pgstattuple in Aurora and Amazon RDS

Both Aurora and Amazon RDS support the use of the pgstattuple extension. To enable it, you first need to create the extension in your database using the command `CREATE EXTENSION pgstattuple;`. Once enabled, you can use functions like `pgstattuple(relation)` to get details about the physical memory used by a table, including the number of pages, live tuples, dead tuples, and more. The function `pgstattuple_approx(relation)` provides a faster estimate of these metrics. You can also get index statistics using `pgstatindex(index)`. Analyzing this low-level data can help identify bloated tables that need vacuuming, find tables with high dead tuple rates that could benefit from being rewritten, and optimize your database's physical storage usage.

 The output of pgstattuple provides actionable insights for monitoring, maintenance, and performance tuning, as discussed in the following sections.

## Detecting and managing table bloat

Identifying and managing bloat is one of the most useful applications of pgstattuple for PostgreSQL tables. Bloat arises when UPDATE and DELETE operations leave behind unused space that is not automatically reclaimed.

PostgreSQL maintains data consistency through a Multiversion Concurrency Control (MVCC) model, where each SQL statement sees a snapshot of data from a previous point in time, regardless of the current state of the underlying data. This prevents statements from viewing inconsistent data caused by concurrent transactions updating the same row, providing transaction isolation for each database session. Unlike traditional locking methods, MVCC minimizes lock contention, allowing for reasonable multi-user performance.

When deleting a row in MVCC systems like PostgreSQL, that row is not immediately removed from data pages. Instead, it is marked as deleted or expired for the current transaction but remains visible to transactions viewing an older snapshot, avoiding conflicts. When transactions complete, these dead or expired tuples are expected to be vacuumed and the space reclaimed. In PostgreSQL, an UPDATE operation is equivalent to a combination of DELETE and INSERT. When a row is updated, PostgreSQL marks the old version as expired (like a DELETE) but keeps it visible to older transaction snapshots. It then inserts a new version of the row with updated values (like an INSERT). Over time, expired row versions accumulate until the VACUUM process removes them, reclaiming the space. This approach enables PostgreSQL's MVCC model, providing snapshot isolation without explicit locking during updates.

PostgreSQL's Autovacuum is an automated maintenance process that helps reclaim memory occupied by dead tuples and updates statistics used by the query planner. The autovacuum process triggers when the maximum age (in number of transactions) crosses `autovacuum_freeze_max_age`, or when a threshold is reached: `autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * number of tuples`. In this formula, `autovacuum_vacuum_threshold` represents the minimum number of updated or deleted tuples required to trigger cleanup, while `autovacuum_vacuum_scale_factor` is a fraction of the table size added to the threshold calculation to determine when maintenance should occur. If autovacuum fails to clean up dead tuples for certain reasons, you may need to handle severely bloated tables manually.

Dead tuples are stored alongside live tuples in data pages. Bloat can also be due to free space within pages, for example, after autovacuum has cleaned up dead tuples. During query execution, PostgreSQL scans more pages filled with dead tuples, causing increased I/O and slower queries. Severely bloated tables cause database workloads to consume unnecessary read I/O, impacting application performance. Cleaning up bloat may be necessary if autovacuum fails.

Before we dive into analyzing table bloat with pgstattuple, ensure you have everything set up to follow along. You will need access to an Amazon RDS or Aurora PostgreSQL instance, as well as a client with psql installed and properly configured to connect to your database. Make sure you have the necessary permissions to create tables and install extensions in your PostgreSQL environment. For this demonstration, we will use the `pgbench_accounts` table. If you don't have this table yet, you can easily create it using the pgbench utility. Run the command `pgbench -i -s 10` to initialize a pgbench schema with a scale factor of 10, which will create the `pgbench_accounts` table along with other necessary tables. This will provide us with sample data to work with during the analysis. Additionally, you should install the pgstattuple extension on your database instance. If you haven't installed it yet, you can do so by running `CREATE EXTENSION pgstattuple;` as a user with sufficient privileges. With these prerequisites, you will be ready to explore table bloat analysis using real data in a controlled environment.

While pgstattuple provides comprehensive analysis of table bloat, it can be resource-intensive. We recommend first using lighter bloat estimation queries noted here. If more detailed analysis is needed, here is how to use pgstattuple. The following example illustrates how to use pgstattuple to analyze bloat information in a table.

Create the table `pgbench_accounts_test` with 10,000 records:
![](/images/2-Proposal/Blog3_1.png)

In this example, the pgstattuple query returns a dead tuple count of 0 and a table size of 1672kB:
![](/images/2-Proposal/Blog3_2.png)

To demonstrate pgstattuple usage, we disable autovacuum (not recommended in production) and update 2,500 records:
![](/images/2-Proposal/Blog3_3.png)

Now, the pgstattuple data for this table shows 2,500 old version tuples moved to dead tuples.
![](/images/2-Proposal/Blog3_4.png)

**bloat_percentage** in PostgreSQL refers to the ratio of space that can be reclaimed in a table or index relative to its total size. It can be calculated using data from pgstattuple as follows:
![](/images/2-Proposal/Blog3_5.png)

A `bloat_percentage` value exceeding 30%–40% generally indicates a bloat issue that needs to be addressed. To clean up bloat, use the VACUUM command:
![](/images/2-Proposal/Blog3_6.png)

Let's check the pgstattuple data after the VACUUM operation:
![](/images/2-Proposal/Blog3_7.png)

The **VACUUM** operation resets `dead_tuple_count` to 0. Free space remains attached to the table and is available for **INSERT** or **UPDATE** operations within the same table. This causes `table_len` (table length) to remain unchanged even after performing the VACUUM operation.

To reclaim disk space occupied by bloat, there are two options:

* **VACUUM FULL** – **VACUUM FULL** can reclaim more disk space but runs much slower. It requires an `ACCESS EXCLUSIVE` lock on the table it is processing, and therefore cannot be performed in parallel with other uses of the table. While **VACUUM FULL** operations are generally not recommended in production environments, they may be acceptable during scheduled maintenance windows where downtime is planned and approved.

* **pg_repack** – **pg_repack** is a PostgreSQL extension that effectively removes bloat from tables and indexes while maintaining online availability. Unlike `CLUSTER` and `VACUUM FULL`, it minimizes exclusive lock times during processing, delivering performance comparable to `CLUSTER`. Although **pg_repack** allows for online table and index reorganization with minimal application downtime, it is important to consider its limitations. The extension still requires short exclusive locks during operation and may struggle to complete on high-transaction tables, potentially impacting database performance. For heavily used tables where full repacking is difficult, consider the alternative of index-only repacking. Best practices include thorough testing in non-production environments, scheduling during low traffic periods, and having a monitoring and rollback plan in place. Despite the benefits, users should be aware of potential risks and plan accordingly when implementing **pg_repack** in their PostgreSQL environments.

VACUUM FULL operation reduces `table_len`:

![](/images/2-Proposal/Blog3_8.png)

The VACUUM FULL operation reclaims wasted space to disk and reduces `table_len`. The following query identifies bloat in the 10 largest tables in your database using pgstattuple. pgstattuple performs a full table scan and can consume more instance resources like CPU and I/O. This makes pgstattuple operations slower for large tables. Alternatively, the function `pgstattuple_approx(relation)` provides a faster estimate of these metrics. While less resource-intensive than pgstattuple, it can still be challenging for very large tables or busy systems. Consider running it during off-peak hours or on a replica if available.

## Automating manual vacuum
Regularly monitoring for bloat allows you to proactively identify maintenance needs before performance is impacted. Bloat metrics can also help tune autovacuum settings to clean up space more aggressively if needed. Once you identify the top 10 most bloated tables, you can automate the VACUUM operation using the `pg_cron` extension. `pg_cron` is a cron-based job scheduler for PostgreSQL that runs inside the database as an extension. It uses syntax similar to standard cron but allows you to schedule PostgreSQL commands directly from the database. The following code snippet is an example of using pg_cron's `cron.schedule` function to set up a job that runs VACUUM on a specific table at 23:00 (GMT) daily:
![](/images/2-Proposal/Blog3_9.png)

## Diagnosing and resolving index bloat
Just like tables, indexes in PostgreSQL can also get bloated, wasting space and impacting performance. pgstattuple allows detection of index bloat using `pgstatindex`.

The following query displays the index identifier, total index size in bytes, and average leaf density:

![](/images/2-Proposal/Blog3_10.png)

Average leaf density is the percentage of useful data in index leaf pages. Significantly bloated indexes can be rebuilt using the `REINDEX` command or `pg_repack` to remove dead space and restore optimal performance. It is recommended to periodically check for bloat on busy indexes with high churn rates.

## Assessing index fragmentation
Another valuable use of pgstattuple is identifying index fragmentation issues. Fragmentation occurs when index pages become scattered due to deletes, updates, and page splits. Heavily fragmented indexes have many dead tuples occupying space inefficiently.
We can check the fragmentation level using `leaf_fragmentation`:
![](/images/2-Proposal/Blog3_11.png)

If `leaf_fragmentation` is high, the index is likely fragmented and a `REINDEX` should be considered. Rebuilding will remove fragmentation and associated performance overheads.

## Best practices when using pgstattuple

Consider the following best practices when using **pgstattuple** for PostgreSQL monitoring and maintenance:

* To estimate bloat in PostgreSQL tables, use the `check_postgres` query mentioned on the PostgreSQL wiki.
* Use the `pgstattuple` extension to analyze the physical storage of database tables, providing detailed statistics on space usage in the database, including space wasted due to bloat.
* Rebuild significantly bloated tables and indexes to reclaim dead space.
* Monitor high `dead_tuple_percent` to identify fragmentation issues.
* Focus maintenance on tables and indexes critical to workload performance.
* Avoid running `pgstattuple` on highly active tables to prevent interference.
* Use `pgstattuple` metrics to fine-tune `autovacuum` settings.
* Combine `pgstattuple` with query analysis and logs for a comprehensive view of the database.

## Conclusion

The **pgstattuple** extension acts as a powerful tool for discovering key diagnostic metrics in PostgreSQL databases, revealing detailed storage statistics that help teams identify and resolve performance-impacting issues like bloat and index fragmentation. Working seamlessly with Aurora and RDS PostgreSQL, this extension provides necessary visibility into storage patterns and maintenance requirements.

Following pgstattuple best practices is key to maintaining efficient, high-performance PostgreSQL databases, and organizations can further enhance their database management through AWS support options – **AWS Enterprise Support**, **Enterprise On-Ramp**, and **Business Support** customers can leverage **AWS Countdown Premium** engagements for optimization guidance, allowing teams to confidently implement best practices and maintain optimal performance while focusing on their core business goals.

## Author Information

### Vivek Singh
Vivek Singh is a Principal Database Specialist, Technical Account Manager at AWS, focusing on Amazon RDS for PostgreSQL and Amazon Aurora PostgreSQL engines. He works with enterprise customers, providing technical support on PostgreSQL operational performance and sharing database best practices. He has over 17 years of experience in open-source database solutions and enjoys working with customers to help design, deploy, and optimize relational database workloads on AWS.

### Kiran Singh
Kiran Singh is a Senior Partner Solutions Architect and an expert on Amazon RDS and Amazon Aurora at AWS, focusing on relational databases. She helps customers and partners build highly optimized, scalable, and secure solutions; modernize their architectures; and migrate their database workloads to AWS.

### Sagar Patel
Sagar Patel is a Principal Database Specialty Architect for the Professional Services team at Amazon Web Services. He works as a database migration expert, providing technical guidance and support to Amazon customers migrating their on-premises databases to AWS.