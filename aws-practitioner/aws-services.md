# AWS Services

These are all general AWS services which may come up in the practitioner exam.

### CloudFront
CloudFront is a content delivery network and is part of the AWS global infrastructure. It can be used to accelerate the delivery of static content (HTML files, CSS, images) to by serving the content to the client form the location nearest to them

### CloudFormation
CloudFormation is a way to create AWS services using a template based language. Instead of manually creating the EC2 instances, RDS deployment and VPC configuration you can define the components in the CloudFormation file and then deploy and CloudFormation stack. This is a type of Infrastructure as Code.

### CloudWatch
CloudWatch is the AWS solution for observability. It ingest logs and metrics from AWS when can then be viewed and have alerts created. It also can trigger events which the application can react to.

### CloudTrail
CLoudTrail is a log of what actions have been taken by users inside AWS from the management console, the SDK or CLI. The logs are stored in an S3 bucket and can be shipped to CloudWatch. This service can be used for audit purposes.

### AWS Macie
Macie is an ML tool for discovering PII in S3 buckets. It will audit the buckets and report which are public or unprotected. It will then sort through and match all documents against it's ruleset for PII. It has a UI for viewing the findings and can raise events to EventBridge and send notifications to Security Hub.

### AWS Config
AWS Config is a tool used to ensure that allows an org to define policies and requirement for configuring resources across AWS. It can then be used to raise alerts if a resource does not meet the policy and provide automatic remediation. 

### AWS Organizations
AWS Orgs are a way to manage multiple accounts along with creating OU to replicate the hierarchy of the org. They also have the benefit of being able to share reserved instances and centralise billing for the entire org.

### AWS Shield
AWS Shield protects against DDOS attacks. Everyone who uses CloudFront or S3 has access to Shield Standard. Shield Advanced is available for use with other AWS resources like EC2 or Global Accelerator. It also integrates with WAF for more features.

### AWS WAF
WAF is a traffic analysis tool which blocks known bots and has rules based on the OWASP top 10 to protect web sites. It is run on the ALB or in CloudFront

### AWS GuardDuty
GuardDuty is an ML tool like Macie but for analyzing the events from all AWS resources to discover any threats in the traffic. 

### AWS Inspector
Inspector is a code vulnerability scanning tool with a bit of network analysis. It scans what is running in Lambda, EC2 and if it finds vulnerable code (maybe a package with a SEV?) it raises an alert. Sends messages to EventBridge and can be viewed in Security Hub

### Security Hub
A single view for security data in AWS. Has reports based on AWS Foundational Security Best Practices (AFSBP), NIST and PCI DSS. Also ingest data from Macie, Inspector and Guard Duty so acts an an aggregation tool for security information.

### Trusted Advisor

Audits an AWS Organisation and reports a status against a set of checks for; Cost, Performance, Security and Service Limits. Basic and Developer on get the Service Limits and some Security. Business and Enterprise get all of them, plus access to the API and CloudWatch integration.

### CodeBuild
Build Server which can integrate with Jenkins

### CodeDeploy
Deployment system which is integrated with AWS services. Can deploy to EC2, ECS or Lambda. Manages traffic routing during deployment

### CodeCommit
Source control service - appears to be shutdown

## Well Architecture Framework

* Operational Excellence
    * Running and monitoring systems
    * Includes automation
* Security
    * Make sure that systems are protected. A little bit about identity management
* Reliability
    * Sacaling, handling failure.
* Performance Efficiency
    * Selecting the right type of instances
    * Scaling up and down - Elasticity
* Cost Optimization
    * Choose the right resources for the job. Spot, On demand, reserved. Serverless for some workflows
* Sustainability
    * Environmental impact of services

## Cloud Adoption Framework (CAF)
* Business
* People
* Governance
* Platform
* Security
* Operations

## Databases

### RDS
Managed service for Aurora, PostgresSQL, MySql, MariaDB, SQL Server, Oracle and Db2. Aurora is a special case as it's a fully managed offering compatible with MySql or PostgresSQL. It includes six copies of all data and can be used for global distribution

### DynamoDB
Fully managed global NoSQL DB. Very fast massively scalable multi-region. 

### Redshift
Data warehouse - uses SQL to analyze structured and un-structured data. Use columnar storage instead of row storage. Used for BI and big data analysis.

## Compute

### EC2

### Lambda

## Container

### ECS

### EKS

### Fairgate

### Beanstalk

## Messaging

### SQS
Message Queues for services running in AWS. 

### SNS

### EventBridge
EventBridge is similar to SNS in that it is a messaging service based around topics and can deliver messages to multiple clients. It is different from SNS in that it has integrations with third party SasS which produce EventBridge events along with AWS applications. It also allows for InputEvent re-writes. It can target 17 AWS service whereas SNS can only target Lambda, S3 and notifications.

### Kenesis
Real time data processing platform or stream processing. Can be used for Analytics, Log and event data collection, IoT device data processing. This is similar to Kafka as a service.

### MKS
Fully manages Kafka service 