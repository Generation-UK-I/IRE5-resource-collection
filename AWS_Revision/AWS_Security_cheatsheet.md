# AWS Security Services Cheat Sheet

## AWS Shared Responsibility Model

One of the most important exam concepts.

The AWS Shared Responsibility Model defines how compliance, governance, and security duties are divided between AWS and the customer—with the primary focus on security. At a high level, AWS secures the cloud itself: the physical infrastructure, networking, and foundational services. Customers are responsible for securing what they put into the cloud: their applications, configurations, and data.

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

Manages SSL/TLS certificates.

Used to secure:

- Websites
- Load Balancers
- APIs

Benefits:

- Free AWS certificates
- Automatic renewal
