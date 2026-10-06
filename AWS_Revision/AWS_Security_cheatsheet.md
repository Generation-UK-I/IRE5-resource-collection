# AWS Security Services Cheat Sheet

## AWS Shared Responsibility Model

One of the most important exam concepts.

The AWS Shared Responsibility Model defines how compliance, governance, and security duties are divided between AWS and the customer, with the primary focus on security. At a high level, AWS secures the cloud itself; Customers are responsible for securing what they put into the cloud.

AWS is responsible for security **OF** the cloud:

- Data centres
- Physical servers
- Networking infrastructure
- Hypervisors

Customers are responsible for security **IN** the cloud:

- User accounts
- Permissions
- Data
- Applications
- Encryption choices
- Operating systems (for EC2)

The balance of responsibilities depends on the type of AWS service being used:

- With **IaaS** offerings like Amazon EC2, customers manage more, including the operating system, network controls, and applications. 
- With **PaaS** models, such as Amazon RDS, AWS takes on more of the heavy lifting, handling the OS and underlying platform, while customers focus mainly on data and access.
- With **SaaS** or **fully managed** services like AWS Lambda, AWS covers nearly everything below the application code itself, leaving customers primarily responsible for their code, business logic, and data governance.

## Identity and Access Management (IAM)

AWS' key Authentication and Authorization Service

IAM controls:

- Who can access AWS
- What they can do
- Which resources they can access

### IAM Entities

IAM includes 3x key entities:

**IAM Users**: Represents an individual person.

Each user can have:

- Password
- Access Keys
- Permissions

**IAM Groups**: A collections of users.

Example:

- Developers
- Administrators
- Support Team

Benefits:

- Easier permission management
- Assign permissions once

**IAM Roles**: A role in IAM is an identity with specific permissions that can be assumed by trusted entities, such as AWS services, users, or applications. Unlike IAM users, IAM roles do not have long-term credentials. Instead, they provide temporary security credentials for the duration of the role session.

Examples:

- EC2 accessing S3
- Lambda accessing DynamoDB

### Policies

Policies are JavaScript Object Notation (JSON) documents that define permissions—what actions are allowed or denied, on which AWS resources, and under what conditions.

Example:

```JSON
{
    "Effect": "Allow",
    "Action": "s3:GetObject",
    "Resource": "*"
}
```

AWS lets you customize this policy with a few key settings:

- **Minimum password length**: You decide how long passwords need to be. Having 8 or 12 characters is common.
- **Character requirements**: You can require a mix of uppercase and lowercase letters, numbers, and special characters.
- **Expiration rules**: Want users to reset passwords every 90 days? You can set that up easily. It helps reduce the chance of long-term exposure if a password gets compromised.
- **Password reuse prevention**: AWS can remember up to 24 past passwords and block users from recycling them.
- **User control**: You can choose whether users are allowed to change their own passwords. In most cases, especially with expiration policies in place, you’ll probably want to let them.

### Multifactor Authentication (MFA)

Using MFA is critical with IAM. It’s one of the simplest, most effective ways to stop unauthorized access—especially if a password ever gets exposed. This is why AWS treats MFA as a best practice, and in many cases, it’s mandatory for high-risk actions like changing IAM policies or managing billing.

Factors fall into three categories:

- **Something you know**: Usually a password or passphrase. Sometimes it’s an answer to a security question.
- **Something you have**: A device you physically possess, like a phone that receives a one-time password or passcode (OTP) via an authenticator app (Google Authenticator, Authy, and so on). AWS also supports hardware tokens that generate time-based codes.
- **Something you are**: Biometrics like fingerprints or facial recognition. AWS doesn’t directly collect biometric data, but it can work with identity providers (like AWS IAM Identity Center or other IdPs) that support biometric logins.

## Data and Identity Safeguards

### AWS Key Management Service (KMS)

Managed encryption key service used with many AWS services.

KMS supports both **symmetric keys** (the same key is used to encrypt and decrypt) and **asymmetric keys** (separate public and private keys), giving you flexibility depending on your use case—whether it’s encrypting data or verifying signatures. Every time a key is used, KMS records the event in CloudTrail, so you get a complete, timestamped audit trail that shows exactly who accessed what, and when.

### CloudHSM

If you need even tighter control over your cryptographic operations—say, for regulatory compliance or internal security requirements—custom key stores backed by CloudHSM let you take control. With CloudHSM, you manage your own HSMs inside your VPC. You get full ownership of the keys, including the ability to generate, store, and use them entirely within a device you control. AWS takes care of the physical security and maintenance, but you handle the keys directly. This is useful for scenarios where trust boundaries or legal frameworks require customer-controlled encryption infrastructure.

### AWS Secrets Manager

Credentials are sensitive data like database passwords, API keys, or third-party tokens. Secrets Manager keeps them secure. It encrypts every secret with KMS, supports fine-grained access control, and offers automatic rotation for supported services.

Examples:

- Database passwords
- API keys
- Credentials

Benefits:

- Secure storage
- Automatic rotation
- Encryption

### AWS Certificate Manager (ACM)

Encryption in transit needs TLS certificates, and AWS Certificate Manager (ACM) takes the complexity out of managing them.

Used to secure:

- Websites
- Load Balancers
- APIs
- CloudFront

Benefits:

- Free AWS certificates
- Automatic renewal

## Additional Security Services

### AWS Network Firewall

Sitting at the edge of your VPC—near internet gateways, VPNs, or Direct Connect links, AWS Network Firewall adds a much deeper layer of inspection. It combines stateful and stateless filtering. **Stateful** means it understands the context of a connection, like a conversation that started with a handshake and continues with replies. Once it allows this session, return traffic flows automatically. **Stateless** rules work faster but more simply, blocking or allowing individual packets without tracking the full session.

AWS Network Firewall can dive deep: decrypting TLS traffic, scanning protocols, and acting as an intrusion prevention system (IPS).

### Amazon Inspector

Amazon Inspector is a vulnerability management service that scans your compute resources—like EC2, container images in the Amazon Elastic Container Registry (ECR), or Lambda functions—for known software flaws and unintended exposure.

It checks for issues like outdated OS packages, misconfigurations, and open network paths that shouldn’t exist, flagging any known common vulnerabilities and exposures (CVEs).

Inspector works behind the scenes continuously, kicking off assessments automatically when something changes—like a new version of an application or a new image pushed to the ECR. You can also connect it to Security Hub or EventBridge for automated workflows, alerts, or remediation pipelines.

### Amazon Detective?

Amazon Detective is a fully managed AWS Security Service that automatically collects and analyzes log data from your cloud environment.

It uses machine learning, statistical analysis, and graph theory to build a unified, interactive behavior graph. This makes it easy to find the root cause of security findings or suspicious activities without needing to script custom queries.

### Amazon GuardDuty

GuardDuty is AWS’s built-in threat detection service. It’s fully managed, constantly running, and uses ML to sift through massive streams of data. This includes CloudTrail events, VPC flow logs, DNS queries, and things like Elastic Kubernetes Service (EKS) audit logs, RDS login attempts, S3 data access, Lambda activity, and more.

The service looks for signs of trouble, such as unauthorized API calls, malware in your workloads, data exfiltration, cryptomining, and runtime threats inside containers or EC2 instances. All of it gets analyzed using behavioral modeling, anomaly detection, and threat intelligence from AWS and partners.

### AWS Shield

A managed threat protection service that defends applications and networks against Distributed Denial of Service (DDoS) attacks.

Protection Tiers:

- **AWS Shield Standard**: Automatically enabled for all AWS customers at no extra cost. It provides always-on, baseline protection against common Layer 3 (network) and Layer 4 (transport) infrastructure attacks like SYN floods and UDP reflections.
- **AWS Shield Advanced**: An optional, paid subscription tier for mission-critical workloads. It adds comprehensive Layer 7 (application) protection, real-time metrics, integration with AWS WAF, and 24/7 access to the Shield Response Team.

### AWS Trusted Advisor

AWS Trusted Advisor acts as a best-practice guide. It continuously scans your AWS environment and offers automated recommendations to improve security, performance, fault tolerance, and cost efficiency. On the security front, it flags critical issues such as whether MFA is enabled on the root account, whether unused security group ports remain open, or if IAM access keys are old and unused.

### AWS WAF (Web Application Firewall)

AWS WAF (Web Application Firewall) focuses on application-layer traffic. Specifically, this is for HTTP and HTTPS requests. You can define custom rules to fit your environment, such as blocking based on IP address, request size, headers, patterns like SQL injection, or geographic origin.

It can block:

- SQL Injection
- Cross-Site Scripting (XSS)
- Malicious traffic

Works with:

- CloudFront
- Application Load Balancer
- API Gateway

### AWS Config

AWS Config is a service that continuously monitors and records the configuration of AWS resources. This allows you to track changes and evaluate them against compliance requirements

By maintaining a detailed history of resource states, Config helps organizations identify misconfigurations, enforce internal policies, and meet external audit requirements.

For example, it can automatically check whether S3 buckets are publicly accessible, confirm that IAM policies follow least privilege, or verify that encryption settings are enabled.

This continuous visibility not only strengthens security but also makes it easier to troubleshoot operational issues and prove compliance with regulatory standards.

### AWS Security Hub

The Security Hub aggregates and prioritizes security findings from across multiple AWS services—such as GuardDuty, Inspector, and Config—and presents them in a single, unified dashboard. This makes it easier to monitor your AWS environment for threats, misconfigurations, and compliance gaps.

### Amazon Macie

Macie focuses on protecting sensitive data stored in Amazon S3. It’s built to discover, classify, and monitor personal and financial information automatically, using ML and pattern matching.

Once enabled Macie starts scanning your S3 buckets—checking both data contents and access configurations. It flags issues like publicly exposed data or misconfigured permissions and generates findings labeled as “SensitiveData” or “Policy.”

You can also send alerts to Security Hub or your existing pipelines via SNS.

For audit and compliance workflows, Macie can export detailed discovery reports to an encrypted S3 bucket using KMS. This makes it easier to document findings, analyze trends, and prepare for audits.

### Service Comparison

|Service|Main Purpose|
|---|---|
|IAM|Permissions|
|MFA|Extra Login Security|
|Cognito|Application Authentication|
|KMS|Encryption Keys|
|Secrets Manager|Password Storage|
|ACM|SSL Certificates|
|Inspector|Vulnerability Scanning|
|GuardDuty|Threat Detection|
|Shield|DDoS Protection|
|WAF|Web Application Protection|
|Security Hub|Central Security Dashboard|
|CloudTrail|Audit Logging|
|CloudWatch|Monitoring|
|Config|Configuration Tracking|

### Frequently Tested Scenarios

|Requirement|Relevant Service|
|---|---|
|A company needs encryption keys.|KMS|
|A company needs to store database credentials securely.|Secrets Manager|
|A company needs DDoS protection.|Shield|
|A company needs protection from SQL injection attacks.|WAF|
|A company needs application users to log in.|Cognito|
|A company needs to detect suspicious account activity.|GuardDuty|
|A company needs to track API calls.|CloudTrail|
|A company needs vulnerability scanning.|Inspector|
|A company needs a central view of security findings.|Security Hub|
|A company needs configuration change history.|AWS Config|
