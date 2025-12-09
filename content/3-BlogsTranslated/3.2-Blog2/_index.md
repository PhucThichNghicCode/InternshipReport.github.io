---
title: "Blog 2"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Implement recovery testing to validate recovery with AWS Backup

*By **Gabe Contreras** and **Sabith Venkitachalapathy** | March 28, 2025*
*Category: Advanced (300), AWS Backup, Storage, Technical*

---

Critical applications are the foundation for everything from e-commerce to healthcare, making a solid backup strategy not just a best practice but a necessity. Threats like **ransomware** are becoming increasingly sophisticated, and for AWS users, backups alone are not enough. Organizations need confidence that these protections will work effectively when disaster strikes.

Manual testing, while necessary, consumes Information Technology resources. Through scheduled automated recovery testing, organizations achieve more than just efficiency: they establish a system that verifies backup integrity, streamlines compliance reporting, and frees up valuable human resources—all while building certainty that their protection strategy will deliver results when needed.

In our previous post, *Validating recovery readiness with AWS Backup restore testing*, we explored why AWS Backup testing and recovery are crucial for meeting internal Disaster Recovery (DR) policies and regulatory requirements. In a digital landscape shaped by regulations like the **European Union’s Digital Operational Resilience Act (DORA)** and the **New York Department of Financial Services (NYDFS)**, resilience is not just a goal but a mandatory requirement. AWS Backup restore testing can provide the evidence required by these regulations; meanwhile, testing and auditing features offer capabilities to help organizations validate and report on their resilience enhancement efforts.

In this article, you will learn how to configure **AWS Backup restore testing** and some best practices to consider when creating your own plan. You will also get an example to see how end-to-end recovery testing works in practice.

## How AWS Backup restore testing works

**AWS Backup restore testing** allows users to test data recovery on a predetermined schedule and validate the restored data. The ability to set schedules and create automated processes to check data recovery helps reduce manual effort and meet compliance requirements.

Without automated recovery testing, personnel might have to select systems and recovery points, complete the restore manually, and ask application teams to validate the restored data. This consumes time and resources across multiple teams, which could be used more effectively to improve applications. By automating this process, we can create data validation workflows to validate restores on a regular basis.

An **AWS Backup restore testing plan** is built in two stages:

1. First, you create the restore testing plan.
2. Second, you create the protected resource selections that will be restored by that testing plan.

When AWS Backup completes a restore, you can build a validation process for the restore test using **AWS Lambda** functions triggered by **Amazon EventBridge**. Lambda functions can perform various validation activities—including checking connectivity, retrieving objects from Amazon S3, or getting the status of encryption keys to validate actual data—then report back to AWS Backup whether that validation was successful or failed. After the restore test is complete, you can use **AWS Backup Audit Manager** reports to demonstrate compliance when needed.

## Building a restore testing plan

The first step of implementing recovery testing is building the restore testing plan. Since there are many factors to consider regarding test frequency and what to test, we will focus on best practices and recommendations. A restore testing plan consists of three parts:

* **Test frequency**
* **Start within time**
* **Recovery point selection criteria**

For *Test frequency*, you should test critical resources daily or weekly. You should test resources within their retention period, meaning if we keep recovery points for 14 days, you should run tests more frequently than that.

The *Start within time* depends on the number of recovery points you will test and how long each restore takes to complete. Each service has a **maximum concurrent restores** limit allowed, and you need to ensure your plans are spaced out so as not to exceed that limit.
![Figure 1: Example of testing plan configuration](/images/2-Proposal/Blog2_1.png)

Recovery point selection can include all or specific vaults, the time frame for eligible recovery points, and whether to include Point-In-Time Recovery (PITR) resource types. You might have one vault per account, or multiple vaults based on application types or tiers. If you are replicating data to a central backup account, an optimal design would be to create a logically air-gapped (LAG) vault for each source account.
![Figure 2: AWS Backup recovery point selection criteria sample](/images/2-Proposal/Blog2_2.png)

After creating the restore testing plan, you move to stage 2: creating protected resource selections. For each resource selection, you must choose a single resource type, such as Amazon S3 or **Amazon Relational Database Service (Amazon RDS)**. After selecting a resource type, restore testing allows you to further customize which specific resources will be selected. Each restore testing plan allows up to 30 protected resource selections. When creating the resource assignment, you can select the default AWS Backup IAM role. If the default role does not exist, it will be created with the appropriate permissions.
![Figure 3: Sample resource allocation for S3](/images/2-Proposal/Blog2_3.png)

You can filter resources by individual selection or by tags, which allows you to select specific resources to test based on your requirements. In Figure 4, we select S3 as the resource type, then filter by tag to select specific buckets.

Each service has its own set of possible recovery metadata, providing default values to perform a successful restore test, where AWS Backup will infer a minimum set of recovery metadata. There is also overrideable metadata that you can change to override the default values. You can read more about inferred and overrideable metadata in the AWS Backup documentation.
![Figure 4: Example of protected resource selection filtered by tags](/images/2-Proposal/Blog2_4.png)

After defining the overall plan and resource selection, we have a fully operational plan as shown in Figure 5.
![Figure 5: Example of complete AWS Backup restore testing plan configuration](/images/2-Proposal/Blog2_5.png)

## Implementing restore validation
Configuring a restore testing plan is only half the work; you also have to verify that your restored data is usable. AWS Backup sends Amazon EventBridge events for changes in restore job status. We can use these events to trigger an AWS Lambda function when a restore test job transitions to a completed state, allowing application teams to create code to test their data. The testing code depends on the service you are protecting but could include retrieving objects from an S3 bucket or querying Amazon DynamoDB. Once your Lambda function finishes running, it can report back to AWS Backup regarding success or failure. Figure 6 illustrates this sample restore validation workflow.
![Figure 6: AWS Backup restore validation testing workflow](/images/2-Proposal/Blog2_6.png)

If you have multiple restore testing plans, you can customize the EventBridge rule to send certain events to corresponding functions (as illustrated in Figure 7) by including the **Amazon Resource Name (ARN)** of the restore testing plan. Using the restore testing plan ARN also allows you to filter out manual restores.
![Figure 7: Example of EventBridge event pattern for restore jobs](/images/2-Proposal/Blog2_7.png)

After creating the event pattern, you select a target to send the event to. If you have multiple resource types requiring distinct testing criteria, an orchestrator Lambda function can help send events for different resource types to the correct validation process. This orchestrator Lambda function will check the resource type and route the event to the corresponding data validation Lambda function. If you have multiple protected resource types, you would select this orchestrator Lambda function as the target for EventBridge events, as seen in Figure 8.
![Figure 8: Target selection sample for EventBridge rule](/images/2-Proposal/Blog2_8.png)

Here is sample code for the Restore Orchestrator Lambda:

```python
import json
import boto3
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)
lambda_client = boto3.client('lambda')

def handler(event, context):
    logger.info("Handling event: %s", json.dumps(event))
    resource_type = event.get('detail', {}).get('resourceType', '')
    function_name = None

    try:
        if resource_type == "RDS":
            function_name = "RDSRestoreValidation"
            logger.info("Resource is an RDS instance. Invoking Lambda function: %s", function_name)
        elif resource_type == "S3":
            function_name = "S3RestoreValidation"
            logger.info("Resource is an S3 bucket. Invoking Lambda function: %s", function_name)
        else:
            raise ValueError(f"Unsupported resource type: {resource_type}")

        # Invoke the appropriate Lambda function
        response = lambda_client.invoke(
            FunctionName=function_name,
            Payload=json.dumps(event),
            InvocationType="RequestResponse"
        )
        logger.info("Lambda invoke response: %s", response)

    except Exception as e:
        logger.error("Error during Lambda invocation: %s", str(e))
        raise e

    logger.info("Finished processing event for resource type: %s", resource_type)
```
In the code of the recovery orchestration Lambda function, you can see that if the resource type matches S3, it forwards the entire event to another Lambda function called S3RestoreValidation. This S3RestoreValidation function then performs the validation of the restore on an S3 resource and reports back to AWS Backup whether the validation was successful or failed.

```python
import json
import boto3
import logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)
s3_client = boto3.client('s3')
backup_client = boto3.client('backup')
def handler(event, context):
    logger.info("Handling event: %s", json.dumps(event))
    restore_job_id = event.get('detail', {}).get('restoreJobId', '')
    resource_type = event.get('detail', {}).get('resourceType', '')
    created_resource_arn = event.get('detail', {}).get('createdResourceArn', '')
    validation_status = "SUCCESSFUL"
    validation_status_message = "Restore validation completed successfully"
    try:
        if resource_type == "S3":
            bucket_name = get_bucket_name_from_arn(created_resource_arn)
            # List objects in the bucket
            response = s3_client.list_objects_v2(Bucket=bucket_name)
            # Check if the bucket contains more than 1 object
            object_count = response.get('KeyCount', 0)
            if object_count > 1:
                logger.info(f"Bucket {bucket_name} contains more than 1 object. Validation successful.")
            else:
                logger.info(f"Bucket {bucket_name} contains 1 or fewer objects. Validation failed.")
                validation_status = "FAILED"
                validation_status_message = f"Bucket {bucket_name} contains only       {object_count} object(s)."
        else:
            validation_status = "FAILED"
            validation_status_message = f"Unsupported resource type: {resource_type}"
        # Report validation result to AWS Backup
        backup_client.put_restore_validation_result(
            RestoreJobId=restore_job_id,
            ValidationStatus=validation_status,
            ValidationStatusMessage=validation_status_message
        )
        logger.info("Restore validation result sent successfully")
    except Exception as e:
        logger.error("Error during restore validation: %s", str(e))
        validation_status = "FAILED"
        validation_status_message = f"Restore validation encountered an error: {str(e)}"
        # Report failure result to AWS Backup
        backup_client.put_restore_validation_result(
            RestoreJobId=restore_job_id,
            ValidationStatus=validation_status,
            ValidationStatusMessage=validation_status_message
        )
    logger.info("Finished processing restore validation for job ID: %s", restore_job_id)
def get_bucket_name_from_arn(arn):
    arn_parts = arn.split(":")
    resource_parts = arn_parts[-1].split("/")
    return resource_parts[-1]
```
The `S3RestoreValidation` code validates the S3 restore by verifying that the bucket contains more than one object. After the check, it reports back to AWS Backup whether the restore completed successfully. A fully successful restore and validation will result in a summary like Figure 9. The job status will be **Completed**, and the validation status will be **Successful**. When setting the validation status in your Lambda code, you can optionally add a validation message, which will appear in the console and AWS Backup APIs. You can read more about restore validation and examples in the documentation.

![Figure 9: Example of AWS Backup restore testing completion](/images/2-Proposal/Blog2_9.png)

AWS Backup automatically initiates the deletion of the restored resources once the validation is submitted or when the cleanup period expires. Deletion times can vary depending on the resource type. Most resources are deleted quickly, but some may take longer. For example, deleting an S3 bucket is a two-step process: first adding lifecycle rules to delete objects, and then deleting the bucket when it is empty. These lifecycle rules can take a few days to execute.

## Considerations for AWS Backup restore testing

Now that you understand the best practices for creating restore testing and validation plans, there are still some implementation details you should consider.

## Cost Optimization

Cost optimization plays a critical role throughout the backup lifecycle, including recovery testing. Here is how to manage costs effectively:

* **Select resources wisely:** Use tags or selections to test only critical resources, avoiding non-production resources unless compliance requires it.
* **Schedule tests by criticality:** Schedule tests according to criticality (daily or weekly for critical resources, quarterly or semi-annually for others) aligned with policies and retention periods (e.g., test within 14 days if the retention period is 14 days).
* **Optimize retention time:** Minimize data recovery time to reduce costs. Set deletion times based on automated tests.

Reference pricing for restore testing can be found on the **AWS Backup pricing page**.

## AWS Backup Audit and Reporting

**AWS Backup Audit Manager** helps you ensure your backup policies and resources comply with internal standards or regulations. This tool tracks whether resources are backed up, backup frequency, whether vaults are logically isolated, and whether recovery times meet objectives. AWS Backup Audit Manager audit frameworks allow this by providing pre-built controls or custom options to ensure resource policy compliance.

Audit reports provide evidence of compliance that can be shared. There are two types of reports:
1.  **Job Reports:** Show completed and active jobs in the last 24 hours (e.g., restore job reports for recent restores).
2.  **Compliance Reports:** Monitor resource status or framework controls.

Management accounts gain visibility across multiple accounts to generate organization-wide reports. See the **AWS Backup documentation** for steps to create reports and details on using frameworks.

## Restore testing plan scale

When creating restore testing plans, ensure that your plans meet testing requirements and complete on time. Each resource type has a limit on the number of concurrent restore jobs from testing plans (not on-demand restores).

The **"Start within"** window from stage 1 is a key factor. The "Start within" configuration means that all resource selections for a restore testing plan must start within this window, and you need to be careful not to exceed concurrency limits.

*Example: Amazon S3 allows 30 concurrent restores, so selecting 90 buckets with a one-hour window risks causing delays.*

To plan effectively, use a longer "start within" window or create multiple plans with staggered start times—especially when frequent (daily/weekly) and periodic (monthly/quarterly) tests run simultaneously. Check adjustable limits in the **documentation** and request limit increases if needed.

## Visualizing the complete testing process

To see how comprehensive recovery testing works and how to implement and integrate testing plans, we have included a sample restore testing plan. This sample plan helps you visualize each step of the process and see how restore and validation interact.

This is a pre-configured **AWS CloudFormation** stack that runs automatically on a daily schedule.

## Prerequisites

The following prerequisites are required to complete this solution:
* AWS Backup configured in your account.
* Amazon S3 and/or Amazon RDS recovery points.

## Launch AWS CloudFormation stack

This AWS CloudFormation template deploys everything needed to automatically test recovery for both Amazon S3 and Amazon RDS.

[https://awsstorageblogresources.s3.us-west-2.amazonaws.com/blog1418/CFAWSBackupRestoreTestingV15.yaml](https://awsstorageblogresources.s3.us-west-2.amazonaws.com/blog1418/CFAWSBackupRestoreTestingV15.yaml)

### Run the restore testing plan

Once deployed, no manual intervention is needed to run the plan. The restore testing plan runs once daily on all resources selected by that plan. As noted in **Figure 6**, AWS Backup completes the restore process, then runs Lambda functions to validate the restores.

When the validation process completes successfully, the validation will appear as shown in **Figure 10**.
![Figure 10: Example of AWS Backup restore testing completion](/images/2-Proposal/Blog2_10.png)

## Cleanup

All resources restored from the restore testing plan will be automatically deleted after four hours. If you are using restore testing for Amazon S3 resources, deleting S3 buckets containing data will take longer. This is because lifecycle rules take a few days to execute. To avoid incurring additional costs, delete the CloudFormation stack, which will remove the restore testing plans and stop future tests. For instructions, refer to Deleting a stack on the CloudFormation console.

## Conclusion

AWS Backup restore testing is a versatile and scalable feature that allows you to tailor a solution to your organization's needs. You can start by understanding your organizational policies, then explore AWS Backup restore testing capabilities in the AWS Management Console and learn how to integrate automated testing into your Disaster Recovery (DR) and cyber resilience strategies. To implement AWS Backup restore testing in your environment, visit the AWS Backup documentation. You can also work with AWS Solutions Architects to design a comprehensive backup validation strategy tailored to your organization's needs.

The message is clear: automated backup validation is no longer an optional choice; it is a fundamental requirement to ensure business continuity in the modern era. Regular testing helps meet internal policies, regulatory requirements, and cyber resilience mandates, while AWS Backup restore testing provides an efficient, scalable solution to ensure recovery readiness.

## Author Information

### Gabe Contreras
Gabe Contreras is a Senior Storage Solutions Architect for the Strategic Accounts division. He always looks forward to deep discussions with customers to find the best solutions for their needs. He enjoys understanding how things operate and solving complex problems.

### Sabith Venkitachalapathy
Sabith Venkitachalapathy is an expert in designing AWS recovery solutions, ensuring disaster recovery and high availability for critical workloads. Focusing on Financial Services (FSI) and Healthcare & Life Sciences (HCLS), Sabith leverages AWS to solve industry challenges and drive innovation. He shares practical insights to help organizations build secure and resilient cloud architectures.
