---
title : "VPC"
date : "2025-11-05"
weight : 3
chapter : false
pre : " <b> 5.3 </b> "
---

#### Intro
**VPC (Virtual Private Cloud)** is a logically isolated virtual private network space in AWS Cloud. It acts as your personal data center in the cloud, giving you complete control over the network environment.
- Core components:
  - Route Table
  - Subnets
  - Internet Gateway
  - Security Groups
#### Create VPC
1. Open the [Amazon VPC console](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#vpcs:)
2. Choose **Your VPCs**, then click **Create VPC**
![overview](/images/VPC/VPC1.png)
3. In the create VPC console:
   - Specify name of the Name tag: my-vpc-01
   - IPv4 CIDR : 10.0.0.0/16
   - Then click **Create VPC**
![overview](/images/VPC/VPC2.png)
#### Content
- [Create Route Table & Internet Gateway](3.1-create-route-table/)
- [Create Subnets](3.2-create-subnets/)
- [Create Security Groups](3.3-create-security-groups/)
- [Create VPC Endpoints](3.3-create-vpc-endpoints/)