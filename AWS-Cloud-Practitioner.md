# AWS Certified Cloud Practitioner (CLF-C02) — AWS Services Study Guide

> GitHub-ready notes for learning AWS services, their use cases, and common exam comparisons. AWS services and exam coverage can change; verify the latest official exam guide before testing.

## Table of Contents
1. [Cloud Fundamentals](#1-cloud-fundamentals)
2. [Global Infrastructure](#2-global-infrastructure)
3. [Compute](#3-compute)
4. [Storage](#4-storage)
5. [Databases](#5-databases)
6. [Networking and Content Delivery](#6-networking-and-content-delivery)
7. [Security Identity and Compliance](#7-security-identity-and-compliance)
8. [Management Monitoring and Governance](#8-management-monitoring-and-governance)
9. [Application Integration](#9-application-integration)
10. [Analytics and AI](#10-analytics-and-ai)
11. [Migration and Transfer](#11-migration-and-transfer)
12. [Developer and DevOps Tools](#12-developer-and-devops-tools)
13. [Billing Pricing and Support](#13-billing-pricing-and-support)
14. [Well-Architected Framework](#14-well-architected-framework)
15. [Service Comparison Cheat Sheet](#15-service-comparison-cheat-sheet)
16. [Practice Questions](#16-practice-questions)
17. [Hands-on Labs](#17-hands-on-labs)
18. [Official References](#18-official-references)

## 1. Cloud Fundamentals

**Cloud computing** is on-demand access to computing resources over the internet with pay-as-you-go pricing.

### Benefits
- **Agility:** Provision resources quickly.
- **Elasticity:** Scale capacity up or down with demand.
- **Economies of scale:** Cloud providers aggregate demand across customers.
- **Global reach:** Deploy workloads in multiple geographic locations.
- **Managed operations:** Offload some infrastructure maintenance.

### Service models
| Model | You manage | Typical AWS example |
|---|---|---|
| IaaS | OS, applications, data | Amazon EC2 |
| PaaS-like managed platform | Application and data | AWS Elastic Beanstalk |
| Serverless | Code and data; AWS manages underlying servers | AWS Lambda |
| SaaS | Configuration and use of application | Amazon QuickSight (managed BI service) |

**Shared responsibility model:** AWS is responsible for security **of** the cloud (physical facilities, hardware, underlying infrastructure). Customers are responsible for security **in** the cloud (data, identity, permissions, configurations, and—on EC2—guest OS patching). Responsibilities vary by service.

## 2. Global Infrastructure

- **Region:** Geographic area containing multiple isolated Availability Zones (AZs). Choose based on latency, compliance, availability, and cost.
- **Availability Zone:** One or more discrete data centers with independent infrastructure in a Region; spread production workloads across AZs for resilience.
- **Edge location:** Point of presence used by services such as Amazon CloudFront to cache content close to users.
- **AWS Local Zones:** Bring select services closer to large population/industry centers.
- **AWS Outposts:** AWS infrastructure and services operated on-premises.

**Example:** Run an application across two AZs behind an Application Load Balancer; use Multi-AZ database deployment to reduce impact of an AZ failure.

## 3. Compute

### Amazon EC2 — Elastic Compute Cloud
Virtual servers with control over operating system and installed software.

**Use cases:** Web servers, custom applications, legacy workloads, batch jobs.

**Core concepts:** AMI (machine image), instance type (CPU/RAM/network profile), EBS volume, security group, key pair, user data, Auto Scaling.

**Pricing options:**
- **On-Demand:** Flexible, no long-term commitment.
- **Savings Plans:** Commit to eligible compute usage for discounts.
- **Reserved Instances:** Pricing discount with term commitment for qualifying instances.
- **Spot Instances:** Spare capacity at discounts; can be interrupted.
- **Dedicated Hosts/Instances:** Isolation options for compliance or licensing needs.

### AWS Lambda
Run code in response to events without managing servers. Common triggers: S3 uploads, API Gateway requests, EventBridge schedules.

```python
# Example AWS Lambda handler (Python runtime)
def lambda_handler(event, context):
    return {"statusCode": 200, "body": "Hello from Lambda"}
```

**Exam clue:** Event-driven, short-running functions with automatic scaling → Lambda. Check current runtime and duration limits in AWS docs.

### Amazon ECS / Amazon EKS / AWS Fargate
- **ECS:** AWS-native container orchestration.
- **EKS:** Managed Kubernetes control plane.
- **Fargate:** Serverless compute capacity for ECS/EKS containers; no EC2 host management.
- **Amazon ECR:** Managed container image registry.

### AWS Elastic Beanstalk
Deploy web applications with automated provisioning and deployment orchestration; you retain control of application code and configuration.

### Amazon Lightsail
Simplified virtual private servers, databases, and networking for smaller applications.

### AWS Batch
Schedule and run batch computing jobs at scale.

## 4. Storage

### Amazon S3 — Simple Storage Service
Highly durable **object storage** for files, backups, static website assets, and data lakes. Data is stored as objects in buckets.

**Concepts:** Bucket, object, key, versioning, lifecycle rules, replication, encryption, bucket policy, Block Public Access.

**Storage classes (examples):**
- S3 Standard: Frequent access.
- S3 Intelligent-Tiering: Unpredictable access patterns.
- S3 Standard-IA: Infrequent access; rapid retrieval.
- S3 One Zone-IA: Infrequent access in one AZ; less resilient than multi-AZ options.
- S3 Glacier Instant Retrieval: Archive data requiring millisecond retrieval.
- S3 Glacier Flexible Retrieval: Archive with flexible retrieval times.
- S3 Glacier Deep Archive: Lowest-cost long-term archive with longer retrieval times.

**CLI example:**
```bash
aws s3 cp report.csv s3://my-example-bucket/reports/report.csv
aws s3 ls s3://my-example-bucket/reports/
```

> Replace the example bucket name with one you own; configure credentials and permissions first.

### Amazon EBS — Elastic Block Store
Persistent **block storage** typically attached to EC2; useful for boot volumes and databases. EBS volumes are AZ-scoped, with snapshots stored separately.

### Amazon EFS — Elastic File System
Elastic, shared **file storage** using NFS for Linux-based workloads; multiple EC2 instances can mount the same file system.

### Amazon FSx
Managed file systems such as FSx for Windows File Server and FSx for Lustre.

### AWS Storage Gateway
Hybrid storage connection between on-premises environments and AWS.

### AWS Backup
Centralized backup policy and management across supported AWS services.

**Remember:** S3 = object; EBS = block; EFS/FSx = file.

## 5. Databases

### Amazon RDS — Relational Database Service
Managed relational databases with automated backups, patching options, and Multi-AZ availability features. Engines include PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, and Db2 (availability varies).

### Amazon Aurora
AWS-built relational engine compatible with MySQL and PostgreSQL, designed for performance and availability.

### Amazon DynamoDB
Serverless, managed NoSQL key-value/document database with low-latency access at scale.

### Amazon ElastiCache
Managed in-memory caching for supported engines, commonly used to reduce database load and latency.

### Amazon Redshift
Managed cloud data warehouse for analytical SQL queries over large datasets.

### Amazon DocumentDB
Managed document database with MongoDB compatibility.

### Amazon Neptune
Managed graph database for relationships and connected data.

### Amazon Timestream
Managed time-series database for time-stamped data.

**Decision examples:**
- SQL transactions and joins → RDS/Aurora.
- Key-value lookups at massive scale → DynamoDB.
- Analytics over large datasets → Redshift.
- Cache frequently requested results → ElastiCache.

## 6. Networking and Content Delivery

### Amazon VPC — Virtual Private Cloud
Logically isolated network in AWS. Components:
- **Subnet:** IP range within one AZ; public/private depends on routing.
- **Route table:** Determines traffic destinations.
- **Internet Gateway:** Enables internet routing for suitable public resources.
- **NAT Gateway:** Lets resources in private subnets initiate outbound connections without accepting unsolicited inbound internet connections.
- **Security group:** Stateful allow rules for resource network interfaces.
- **Network ACL:** Stateless subnet-level allow/deny rules.
- **VPC endpoint:** Private connectivity to supported AWS services without traversing the public internet.
- **VPC peering:** Private routing between VPCs (non-transitive).
- **Transit Gateway:** Hub for connecting VPCs and on-premises networks.

### Elastic Load Balancing (ELB)
- **Application Load Balancer (ALB):** Layer 7 HTTP/HTTPS routing.
- **Network Load Balancer (NLB):** Layer 4 TCP/UDP/TLS high-performance traffic.
- **Gateway Load Balancer:** Deploy and scale network appliances.

### Amazon Route 53
Managed DNS, domain registration, and health-check-based routing.

### Amazon CloudFront
Content delivery network (CDN) caching content near users at edge locations.

### AWS Direct Connect vs Site-to-Site VPN
- **Direct Connect:** Dedicated network connection to AWS.
- **Site-to-Site VPN:** Encrypted connection over the internet.

### AWS Global Accelerator
Uses AWS global network and static anycast IP addresses to improve availability/performance of supported applications.

### Amazon API Gateway
Create, publish, secure, and monitor APIs, often paired with Lambda.

## 7. Security Identity and Compliance

### AWS IAM — Identity and Access Management
Control who can access what. Understand **users, groups, roles, policies**, and least privilege.

- **IAM user:** Identity with long-term credentials (prefer federation/temporary credentials where possible).
- **IAM role:** Assumed identity that provides temporary credentials.
- **IAM policy:** JSON document defining permissions.
- **MFA:** Extra sign-in verification; enable for privileged identities.

**Example read-only S3 policy (scope to one example bucket):**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["s3:ListBucket"],
    "Resource": "arn:aws:s3:::my-example-bucket"
  }, {
    "Effect": "Allow",
    "Action": ["s3:GetObject"],
    "Resource": "arn:aws:s3:::my-example-bucket/*"
  }]
}
```

### IAM Identity Center
Central workforce access and single sign-on to AWS accounts and supported applications.

### AWS Organizations / Control Tower
- **Organizations:** Multi-account management, consolidated billing, service control policies (SCPs).
- **Control Tower:** Set up and govern a multi-account landing zone with guardrails.

### Amazon Cognito
Authentication, authorization, and user management for customer-facing applications.

### AWS KMS / Secrets Manager / ACM
- **KMS:** Create and control cryptographic keys for encryption.
- **Secrets Manager:** Store, retrieve, and rotate supported secrets.
- **Certificate Manager (ACM):** Provision and manage TLS certificates.

### Security monitoring and protection
- **AWS CloudTrail:** Record account/API activity for audit and investigation.
- **Amazon GuardDuty:** Threat detection from signals and logs.
- **Amazon Inspector:** Vulnerability scanning for supported workloads.
- **AWS Security Hub:** Centralized security findings and posture management.
- **Amazon Macie:** Discover sensitive data such as PII in S3.
- **AWS WAF:** Filter malicious HTTP(S) web requests.
- **AWS Shield:** DDoS protection; Shield Standard protection is automatically included for supported services.
- **AWS Firewall Manager:** Central management of security rules across accounts.
- **AWS Artifact:** Access compliance reports and agreements.

**Exam comparisons:** CloudTrail = who did what; CloudWatch = metrics/logs/alarms; Config = configuration history/compliance; GuardDuty = threats.

## 8. Management Monitoring and Governance

### Amazon CloudWatch
Collect metrics, logs, alarms, and dashboards for operational monitoring.

### AWS CloudTrail
Track API actions and account activity for governance and audit.

### AWS Config
Record configuration changes and evaluate resources against compliance rules.

### AWS CloudFormation
Infrastructure as Code (IaC): declare resources in YAML or JSON templates.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Example S3 bucket
Resources:
  StudyBucket:
    Type: AWS::S3::Bucket
```

### AWS Systems Manager
Operations tools such as Session Manager, Patch Manager, Automation, and Parameter Store.

### AWS Trusted Advisor
Best-practice checks across categories such as cost optimization, security, fault tolerance, performance, and service limits. Check plan-specific access.

### AWS Health
Information about AWS events affecting your services and resources.

### AWS Service Catalog
Govern approved infrastructure products for internal users.

## 9. Application Integration

- **Amazon SQS:** Managed message queues; decouple services; consumers poll messages. Standard queues offer at-least-once delivery and best-effort ordering; FIFO queues support ordering and deduplication behavior.
- **Amazon SNS:** Pub/sub notifications; push messages to subscribers such as SQS, Lambda, and HTTP endpoints.
- **Amazon EventBridge:** Event bus and routing rules for event-driven applications; supports scheduled rules/scheduler functionality.
- **AWS Step Functions:** Coordinate multi-step workflows with state machines.
- **Amazon MQ:** Managed message broker for compatible messaging protocols.
- **Amazon SES:** Send transactional/marketing emails.
- **Amazon Connect:** Cloud contact center service.

**Pattern:** Order placed → EventBridge routes event → SQS queues work → Lambda processes it → SNS notifies stakeholders.

## 10. Analytics and AI

### Data and analytics
- **Amazon Athena:** Query data in S3 using SQL without managing servers.
- **AWS Glue:** Data integration and ETL; Glue Data Catalog stores metadata.
- **Amazon Kinesis:** Stream data ingestion/processing services.
- **Amazon EMR:** Managed big-data frameworks such as Spark and Hadoop.
- **Amazon QuickSight:** Business intelligence dashboards and visualizations.
- **AWS Lake Formation:** Set up and govern data lakes.
- **Amazon OpenSearch Service:** Search, log analytics, and related workloads.

### AI and machine learning
- **Amazon SageMaker AI:** Build, train, and deploy ML models.
- **Amazon Bedrock:** Build generative AI applications using foundation models via managed APIs.
- **Amazon Rekognition:** Image and video analysis.
- **Amazon Textract:** Extract text and structured data from documents.
- **Amazon Comprehend:** Natural-language analysis.
- **Amazon Transcribe:** Speech to text.
- **Amazon Polly:** Text to speech.
- **Amazon Translate:** Language translation.
- **Amazon Lex:** Conversational interfaces/chatbots.

**Exam clue:** OCR from scanned forms → Textract; speech-to-text → Transcribe; text-to-speech → Polly; generative AI foundation models → Bedrock.

## 11. Migration and Transfer

- **AWS Migration Hub:** Central view of migration progress.
- **AWS Application Migration Service (MGN):** Lift-and-shift server migrations.
- **AWS Database Migration Service (DMS):** Migrate/replicate databases with reduced downtime where supported.
- **AWS DataSync:** Move large datasets between on-premises and AWS storage or between AWS storage services.
- **AWS Transfer Family:** Managed SFTP/FTPS/FTP and AS2 transfers.
- **AWS Snow Family:** Physical devices for data transfer/edge computing where available; verify current device offerings.
- **AWS Application Discovery Service:** Gather server configuration and usage data for migration planning.

**Migration strategies (7 Rs):** Retire, Retain, Relocate, Rehost, Replatform, Repurchase, Refactor/Re-architect.

## 12. Developer and DevOps Tools

- **AWS CodeBuild:** Managed build and test service.
- **AWS CodeDeploy:** Automate application deployments.
- **AWS CodePipeline:** CI/CD workflow orchestration.
- **AWS Cloud9:** Cloud IDE (availability and new-customer access can change; verify current service status).
- **AWS X-Ray:** Distributed tracing and request analysis.
- **AWS Cloud Development Kit (CDK):** Define infrastructure using programming languages and synthesize CloudFormation.
- **AWS CLI:** Command-line management of AWS resources.

```bash
# Requires configured AWS credentials and permissions
aws sts get-caller-identity
aws ec2 describe-regions --query 'Regions[].RegionName' --output table
```

## 13. Billing Pricing and Support

### Cost tools
- **AWS Pricing Calculator:** Estimate costs before deployment.
- **AWS Cost Explorer:** Visualize and analyze historical/forecast costs.
- **AWS Budgets:** Track budget thresholds and trigger alerts/actions.
- **AWS Cost and Usage Report / Data Exports:** Detailed billing and usage data.
- **Cost allocation tags:** Categorize spending by project/team/environment.
- **Consolidated billing:** Combine billing across accounts in AWS Organizations.

### Pricing concepts
- Pay for consumption; many services charge separately for compute, storage, requests, and outbound data transfer.
- Inbound data transfer is often free, but exceptions and regional/service pricing apply.
- Reserved capacity/commitments can reduce cost for predictable usage; Spot is best for interruption-tolerant compute.
- **AWS Free Tier:** Eligibility and allowances vary by account creation date, service, and program; always inspect current terms and set budget alerts.

### Support
AWS offers multiple support plans with different technical support and response entitlements. Plan names and features change; consult current AWS Support pricing and plan pages rather than memorizing old tiers.

## 14. Well-Architected Framework

Six pillars:
1. **Operational Excellence:** Run and improve operations.
2. **Security:** Protect systems, data, and assets.
3. **Reliability:** Recover from failures and meet demand.
4. **Performance Efficiency:** Use resources efficiently.
5. **Cost Optimization:** Avoid unnecessary spending.
6. **Sustainability:** Reduce environmental impact.

**Example architecture:** CloudFront → ALB → EC2 Auto Scaling across two AZs → RDS Multi-AZ; S3 for static objects; CloudWatch for monitoring; IAM least privilege; AWS Backup for recovery.

## 15. Service Comparison Cheat Sheet

| Need | Usually choose | Distinguish from |
|---|---|---|
| Virtual server | EC2 | Lambda = event-driven serverless functions |
| Run containers | ECS / EKS | Fargate = serverless container compute |
| Store photos/backups | S3 | EBS = block volume; EFS = shared files |
| SQL relational data | RDS / Aurora | DynamoDB = NoSQL |
| Analytical data warehouse | Redshift | Athena = query data directly in S3 |
| Content delivery | CloudFront | Route 53 = DNS |
| DNS | Route 53 | Global Accelerator = global network traffic acceleration |
| Filter web requests | WAF | Shield = DDoS protection |
| Audit API calls | CloudTrail | CloudWatch = monitoring |
| Track configuration compliance | Config | Security Hub = aggregate security findings |
| Detect suspicious activity | GuardDuty | Inspector = vulnerability scanning |
| Queue tasks | SQS | SNS = fan-out notifications |
| Route events | EventBridge | Step Functions = workflow orchestration |
| Dedicated hybrid network | Direct Connect | VPN = encrypted tunnel over internet |
| Manage encryption keys | KMS | Secrets Manager = secrets lifecycle |
| Cost trends | Cost Explorer | Budgets = thresholds/alerts |
| Infrastructure as code | CloudFormation | Systems Manager = operations automation |

## 16. Practice Questions

**Q1.** Which service provides object storage?  
**A:** Amazon S3.

**Q2.** Which service is designed to record API activity for auditing?  
**A:** AWS CloudTrail.

**Q3.** What is the main difference between a security group and a network ACL?  
**A:** Security groups are stateful and attached to network interfaces; NACLs are stateless and apply at the subnet level.

**Q4.** Which service should host a relational PostgreSQL database with managed backups?  
**A:** Amazon RDS for PostgreSQL (Aurora PostgreSQL-Compatible is another option depending on requirements).

**Q5.** Which service executes code in response to S3 events without managing servers?  
**A:** AWS Lambda.

**Q6.** Which service helps set spending alerts?  
**A:** AWS Budgets.

**Q7.** Which service caches website assets near users worldwide?  
**A:** Amazon CloudFront.

**Q8.** Which service provides a managed Kubernetes control plane?  
**A:** Amazon EKS.

**Q9.** Which service helps detect threats in an AWS account?  
**A:** Amazon GuardDuty.

**Q10.** Which service is best for asynchronous message queues between microservices?  
**A:** Amazon SQS.

**Q11.** Which service helps centrally manage multiple AWS accounts?  
**A:** AWS Organizations.

**Q12.** Which service is used for managed DNS?  
**A:** Amazon Route 53.

**Q13.** Which service is suited for extracting text and form fields from scanned documents?  
**A:** Amazon Textract.

**Q14.** Which service supports SQL queries directly over data stored in S3?  
**A:** Amazon Athena.

**Q15.** Who patches the guest OS on a self-managed EC2 instance?  
**A:** The customer, under the shared responsibility model.

## 17. Hands-on Labs

> **Cost caution:** AWS resources can incur charges. Check pricing and Free Tier eligibility, create a budget, and delete resources after each lab.

### Lab 1: Explore identity and billing
1. Enable MFA for your root user and avoid using root for everyday work.
2. Set up appropriate IAM Identity Center or role-based access.
3. Create an AWS Budget alert.
4. Inspect Cost Explorer and the Billing console.

### Lab 2: S3 bucket and versioning
1. Create a uniquely named private S3 bucket.
2. Keep Block Public Access enabled.
3. Upload a test file and enable versioning.
4. Upload a modified version; inspect object versions.
5. Delete test objects and bucket when finished.

### Lab 3: CloudWatch and CloudTrail
1. Explore default EC2/other service metrics in CloudWatch.
2. Inspect CloudTrail Event history for management events.
3. Identify the actor, event name, time, and resource for an event.
4. Compare operational metrics vs audit activity.

### Lab 4: Serverless API (optional)
1. Create a minimal Lambda function.
2. Test with a sample event.
3. Inspect logs in CloudWatch Logs.
4. Optionally add API Gateway after reviewing its pricing/security settings.
5. Remove all test resources.

### Lab 5: VPC diagram
Draw: VPC → public subnet with ALB → private subnets with app servers → private database subnets. Annotate route tables, Internet Gateway, NAT Gateway, security groups, and NACLs. Explain which traffic is allowed.

## 18. Official References

- [AWS Certified Cloud Practitioner exam page](https://aws.amazon.com/certification/certified-cloud-practitioner/)
- [AWS Certification exam guides](https://aws.amazon.com/certification/certification-prep/)
- [AWS Cloud Essentials](https://aws.amazon.com/getting-started/cloud-essentials/)
- [AWS documentation](https://docs.aws.amazon.com/)
- [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [AWS pricing](https://aws.amazon.com/pricing/)
- [AWS Free Tier](https://aws.amazon.com/free/)

---

**Study tip:** For every service, remember **what it does, when to use it, how it differs from alternatives, and who manages which security responsibilities**.
