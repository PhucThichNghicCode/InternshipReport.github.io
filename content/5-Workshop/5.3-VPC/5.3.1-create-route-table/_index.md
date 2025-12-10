---
title : "Create Route table and Internet Gateway"
date :  "2025-11-05" 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---
{{% notice note %}}
A Route Table is a set of rules (routes) used by a router to determine the best path for data packets to reach their destination.
{{% /notice %}}

1. Open the [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#Home:)
2. Choose **Route tables**, then click **Create route table**
![endpoint](/images/VPC/route1.png)
3. In the Create route table console:
   1. Specify name of Route table
   2. Choose VPC created
   3. Then click **Create route table**
![endpoint](/images/VPC/route2.png)
4. In Route table console, click Route table created
![endpoint](/images/VPC/route3.png)
5. Choose Route in navbar -> click Edit routes
![endpoint](/images/VPC/route4.png)
6. Edit routes console -> choose Target local -> Save changes
![endpoint](/images/VPC/route5.png)
7. Choose **Internet Gateway** in left navbar -> click **Create internet gateway**
![endpoint](/images/VPC/igw1.png)
8. In Create Internet Gateway
   - Specify name of Internet Gateway
   - Click **Create internet gateway**
![endpoint](/images/VPC/igw2.png)
9. Back to Route table -> Create route table like step 3
   - Click this Route table -> Edit routes
   - In Target choose Internet Gateway created
   - Then click ***Save changes*
![endpoint](/images/VPC/igw3.png)