# AWS Services

These are all general AWS services which may come up in the practioner exam.

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
AWS Orgs are a way to manage multiple accounts along with creating OU to replicate the hierachry of the org. They also have the benefit of being able to share reserved instances and centralise billing for the entire org.

### AWS Shield
AWS Shield protects against DDOS attacks. Everyone who uses CloudFront or S3 has access to Shield Standard. Shield Advanced is available for use with other AWS resources like EC2 or Global Accelarator. It also integrates with WAF for more features.

### AWS WAF
WAF is a traffice analysis tool which blocks known bots and has rules based on the OWASP top 10 to protect web sites. It is run on the ALB or in CloudFront

### AWS GuardDuty
GuardDuty is an ML tool like Macie but for analyzing the events from all AWS resources to discover any threats in the traffic. 

### AWS Inspector
Inspector is a code vulnerability scanning tool with a bit of network analysis. It scans what is running in Lambda, EC2 and if it finds vunerable code (maybe a package with a SEV?) it raises an alert. Sends messages to EventBridge and can be viewed in Security Hub

### Event Bridge

### Security Hub

### AWS CodeDeploy

### AWS CodeCommit

## Databases

### RDS

### DynamoDB

### Kenesis

### Redshift

## Compute

### EC2

### Lambda

## Container

### ECS

### EKS

### Fairgate

### Beanstalk