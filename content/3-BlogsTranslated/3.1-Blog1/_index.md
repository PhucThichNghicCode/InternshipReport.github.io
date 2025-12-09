---
title: "Blog 1"
date: "2025-11-05"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# How Volkswagen and AWS Built a Complete MLOps Process for the Digital Production Platform

**By:** Gabriel Zylka, Chandana Keswarkar, and Sandro Zangiacomi
**Date:** March 03, 2025

> **Keywords:** Amazon API Gateway, Amazon DataZone, Amazon EventBridge, Amazon SageMaker, Amazon Simple Storage Service (S3), Automotive, AWS CodeArtifact, AWS CodeCommit, AWS Lake Formation, AWS Service Catalog, AWS Step Functions, AWS Well-Architected Framework, Industries

## Background

In 2019, Volkswagen AG (VW) and Amazon Web Services (AWS) formed a strategic collaboration to develop the Digital Production Platform (DPP). This strategy was designed to improve VW's manufacturing and logistics efficiency by 30% while simultaneously reducing production costs by a similar margin. The DPP facilitates access to data from VW's manufacturing devices and systems, making the building and deployment of new applications 2-3 times simpler and faster, while reducing testing costs and facilitating the sharing of solutions across VW businesses. A key solution adopted was a complete MLOps process for Machine Learning (ML) related problems. This article presents the architecture used to standardize the entire lifecycle of an ML project, best practices, and how to implement a similar MLOps solution in your organization.

The VW team has deployed over 100 use cases across VW companies and brands. The majority of these are ML-based solutions in areas such as predictive maintenance, quality control, and process optimization. For example, predictive maintenance for welding gun robots involves sensors detecting motor or welding circuit faults. These ML models are required to predict these faults early from thousands of robots across multiple different factories. Many data science teams worked to create operation-ready ML solutions, complying with corporate security standards. However, this decentralized approach revealed several issues:

* **Inconsistent Development:** Each factory developed solutions independently, leading to fragmented operational results and the lack of a unified strategy. This created a collection of disjointed and disconnected solutions.
* **Inefficient Development:** Redundant efforts arose from VW teams having to recreate similar infrastructure components, with each component requiring a different security assessment, increasing complexity.
* **Impact on Time and Resources:** Initial deployments required 2 full-time employees working for 2 months per workstream. Training new members and completing security assessments also took longer because each location had its own implementation method.
* **Process Management Issues:** Without a standard process, VW teams struggled to manage, trace, and version control models. This affected transparency.
* **Maintenance and Quality Challenges:** Diverse implementations led to uneven quality, wasted resources, impacted testing standards, and made it difficult to adopt best practices.

These issues resulted in financial impacts, slowed time-to-market, increased maintenance costs, and created additional security risks. Knowledge sharing became difficult, leading to wasted effort and resources on non-differentiating tasks, adding to the burden of operations and maintenance. To address these issues, VW partnered with AWS Professional Services to build a more secure, scalable MLOps solution for enterprises using ML to deploy on the DPP.

## MLOps Architecture

The architecture implemented at VW demonstrated how MLOps automates every stage, creating an efficient process to manage the entire machine learning model lifecycle. For a deeper dive, you can read the article *MLOps foundation roadmap for enterprises with Amazon SageMaker*.

![Multi-account MLOps Architecture](/images/2-Proposal/Blog1.png)

A multi-account strategy helps manage multiple models. Here is how each account functions:

* **Data Account:** This account acts as a hub for data management, monitoring all data ingestion processes from sources like on-premises systems or other cloud environments. Administrators control and restrict access to specific data columns to meet use case requirements, ensuring compliance through anonymization when necessary. To learn more about how VW manages access and data security using Amazon DataZone, refer to the blog post.
* **EXP Account (Experimentation):** This account provides a dedicated environment for VW's data science team to perform data exploration, experimentation, and model training. The EXP account deploys all resources within an isolated VPC without internet access. To use third-party libraries, an AWS CodeArtifact repository provides secure access to public repositories like PyPI. Data Scientists commit code changes to an AWS CodeCommit repository (or other providers like GitLab, as AWS CodeCommit access for new users has ended). When training or inference requires custom images, Data Scientists commit code that is pushed to a repository. Then, a CI/CD pipeline scans, tests, and builds the images before pushing them to a central Amazon ECR registry in the RES account.
* **RES Account (Resources):** This account manages all infrastructure and ML model deployments. It houses the CodeCommit repositories for Infrastructure as Code (IaC) and AWS CodePipeline CI/CD workflows, enabling deployment across all RES, EXP, DEV, INT, and PROD accounts. Additionally, this account hosts Amazon ECR repositories where scientists publish Docker containers with custom images used during model inference. Finally, it creates AWS Service Catalog products in the EXP account to execute model training pipelines and model deployment pipelines in the RES account.
* **DEV Account (Development):** This account serves as the development environment where VW teams initially deploy ML models to Amazon SageMaker endpoints. Here, models undergo complete testing by VW for all model metrics such as performance and infrastructure parameters like response time and availability. In the DEV account, administrators typically grant manual access to data scientists and DevOps teams to test and troubleshoot deployments. Once testing is complete, a manual approval step in the CI/CD pipeline in the RES account promotes the deployment to the INT stage.
* **INT Account (Integration):** This account acts as a staging environment for ML model deployment to validate that the deployment and infrastructure integration are successful before proceeding to the PROD environment. Unlike the DEV environment, deployments in the INT account can generally only be accessed via read-only permissions. After passing all testing procedures, the DevOps team approves via the CI/CD pipeline in the RES account to deploy the model to production.
* **PROD Account (Production):** This account hosts the ML model version on an Amazon SageMaker endpoint. In the production environment, you can configure the SageMaker endpoint with an auto scaling group to automatically scale the endpoint up or down based on demand.

## MLOps for Data Scientists

The machine learning lifecycle is an iterative process, starting with defining a business problem and deciding if ML is an applicable solution. Once confirmed, the process involves defining the ML problem, followed by the data phase, where Data Engineers collect, explore, prepare, and analyze data through visualization. Next is Feature Engineering, involving specific techniques like encoding, normalization, and handling missing data. Then comes the model development phase, which includes selecting a suitable algorithm, training the model, hyperparameter tuning, and evaluating performance using predefined metrics. Once the model meets the desired performance criteria, it is deployed to the production environment. Over time, model performance may degrade, requiring continuous monitoring, debugging, retraining, and redeployment to maintain effectiveness. The following steps describe the workflow of a Data Scientist for MLOps solutions across different accounts:

1.  **Data Collection and Preparation:** Data Engineers create Extract, Transform, and Load (ETL) pipelines combining multiple data sources and prepare the necessary datasets for ML problems in the DATA account. Data is cataloged using AWS Glue Data Catalog and shared with other users and accounts via AWS Lake Formation for governance. Data Scientists are granted secure access to specific datasets from the DATA account.
2.  **Data Exploration and Model Development:** Each Data Scientist receives an Amazon SageMaker Studio user profile with an IAM role and Security Group to access SageMaker Studio and their specific datasets in Amazon S3. In their personal workspace, Data Scientists perform tasks such as data exploration, model training, hyperparameter tuning, data processing, and model evaluation using Jupyter Notebooks or SageMaker services. This can be extended with Amazon SageMaker Feature Store for reuse. For more information, you can refer to *Enable feature reuse across accounts and teams using Amazon SageMaker Feature Store*.
3.  **Model Training and Retraining:** After the experimentation phase, the Data Scientist launches the "Model Building Product" from AWS Service Catalog. This initiates a CloudFormation stack to set up a SageMaker Pipeline to orchestrate data tasks such as processing, training, and evaluation. Successfully trained and evaluated ML models are registered in the SageMaker Model Registry, which stores version history and deployment metadata, such as container images and artifact locations. For subsequent retraining, Data Scientists trigger the Training Pipeline, which registers a new model version into the Model Registry upon successful execution. When a new model version is registered, an Amazon EventBridge event is triggered and sent to the RES account, initiating the deployment process.
4.  **Model Deployment and Redeployment:** To create a model deployment pipeline, the DevOps engineer launches the "Model Deployment" product from Service Catalog, referencing the trained model in the Model Registry. This product provisions a CodeCommit repository for IaC, a CodePipeline, and an EventBridge rule to listen for new model versions from the EXP account. The CI/CD pipeline is triggered by changes in the CodeCommit repository and by events coming from EventBridge. It queries the Model Registry to retrieve the latest version and deploys the model along with related resources to the DEV stage. After manual approval steps, the model continues to be promoted through the INT and PROD stages.

## Benefits

This new MLOps process brings several key benefits:

* **Standardization:** By replacing multiple custom solutions with a unified common process, VW eliminated redundant work and established consistent practices across all ML activities.
* **Operational Efficiency:** A structured account architecture divided into different environments clarifies responsibilities and optimizes the entire ML lifecycle from experimentation to deployment.
* **Security and Governance:** Built-in security guardrails, including dedicated IAM roles, isolated VPCs, and encryption, ensure ML activities meet enterprise security standards while maintaining flexibility.
* **Scalability:** The solution currently supports 8 projects across 5 factories, serving 16 Data Scientists, with an architecture designed to easily scale in the future and accommodate more new projects.
* **Reduced Time to Market:** An automated and standardized process can now complete work in just a few days, whereas it previously took 2 full-time employees 2 months for a single project. This significantly accelerates model deployment.

## Open Source Repository

A Github repository provides a solution template to deploy the MLOps infrastructure discussed in this blog post. This solution deploys a total of 13 configurable AWS CDK stacks across 5 AWS accounts, allowing you to quickly bootstrap an MLOps platform. The template is flexible and can be customized to meet your specific requirements, such as adding Service Catalog Products to refine training and deployment pipelines. To perform the deployment, you will need access to 5 AWS accounts for the following environments:

* EXP (Experimentation)
* RES (Resources)
* DEV (Development)
* INT (Integration)
* PROD (Production)

For prerequisites and deployment instructions, please refer to the `README.md` file in the repository.

## Possible Extensions

The following extensions can enhance the MLOps solution to meet specific use case needs:

### Batch Processing

While online inference provides low-latency real-time predictions, batch inference is ideal for scenarios where data arrives in batches at regular intervals and immediate results are not required. Batch inference is particularly suitable for periodic inference needs. By using Amazon SageMaker, you can perform batch inference by running a batch transform job on a SageMaker Model or orchestrating a batch inference workflow with AWS Step Functions. Results are automatically stored in Amazon S3.

### API Gateway

To serve models via custom API endpoints, use Amazon API Gateway, a fully managed service to create, publish, maintain, monitor, and secure APIs at any scale. Requests are routed through API Gateway to AWS Lambda, which invokes the SageMaker endpoint and returns responses to API Gateway for testing and serving predictions.

### Model Monitoring

Amazon SageMaker Model Monitor enables continuous monitoring of model performance after deployment in the production environment. This tool collects input data samples and model predictions at scheduled intervals, monitoring metrics such as data quality, model quality, bias, and explainability. If Model Monitor detects any deviation or degradation, it generates alerts, allowing you to take corrective actions such as collecting new training data, retraining the model, or auditing upstream systems. Learn more about *Monitoring in-production ML models at large scale using Amazon SageMaker Model Monitor*.

## Security Guardrails

VW's MLOps solution follows AWS security best practices from the AWS Well-Architected Framework Security pillar and the AWS whitepaper *Build a Secure Enterprise Machine Learning Platform on AWS*. In addition to security features implemented in each VW AWS account, the following best practices were observed:

* **Infrastructure Protection:**
    * All resources are segregated within self-managed VPC subnets.
    * All AWS services are accessed via VPC Service Endpoints.
* **Data Protection:**
    * All stored data is encrypted using customer-managed encryption keys.
    * SageMaker Studio environments are encrypted at rest.
* **Identity and Access Management:**
    * Dedicated IAM roles are assigned to SageMaker Studio users, pipelines, training jobs, and model endpoints.
    * IAM roles are created using Amazon SageMaker Role Manager personas to enforce least privilege access.
    * Explicit IAM Deny policies to restrict model training or model deployment outside the VPC or without encryption.
* **Auditing:**
    * Regular security audits are performed to identify and mitigate vulnerabilities.
    * AWS Config is used to track configuration changes and ensure compliance with security policies.
    * AWS Security Hub aggregates security findings from multiple AWS services for centralized management and remediation. Learn more about *VW secures landing zone with automated remediation of security findings*.

## Conclusion

The collaboration between VW and AWS successfully transformed a fragmented MLOps landscape into a standardized, efficient, and more secure ML production process. By implementing a comprehensive MLOps solution built on Amazon SageMaker, VW addressed the challenges of decentralized development to establish a streamlined, scalable, and secure ML lifecycle through a multi-account MLOps architecture. This implementation can serve as a blueprint for other enterprises looking to standardize their MLOps operations at scale. If you are interested in exploring similar solutions or need guidance on building your own MLOps solution, visit the AWS for automotive page or contact your AWS team today.

## Authors

* **Gabriel Zylka:** An ML Engineer at AWS Professional Services. He works closely with customers to accelerate their cloud transformation journey. Specializing in MLOps, he focuses on productizing ML workloads by automating the end-to-end ML lifecycle and helping achieve desired business outcomes.
* **Chandana Keswarkar:** A Senior Solution Architect at AWS, specializing in guiding automotive customers on their digital transformation journey using cloud technology. She helps organizations develop and refine platform and product architectures while making informed design decisions. In her free time, she enjoys traveling, reading, and practicing yoga.
* **Sandro Zangiacomi:** An AWS Professional Services AI expert based in Paris. In his current role, he assists customers in orchestrating machine learning workflows and building machine learning platforms for various use cases, including GenAI implementation. In his free time, he enjoys spending quality moments with friends in Paris and learning about almost everything, from personal development to fashion.