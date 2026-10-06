# Storage in AWS

Think of storage in three categories:

|Storage Type|Service|
|---|---|
|Object Storage|Amazon S3|
|Block Storage|Amazon EBS|
|File Storage|Amazon EFS|

## Amazon S3

Amazon S3 is AWS's highly scalable object storage service.

Used for:

- Images
- Videos
- Documents
- Backups
- Static websites
- Data lakes

**Key Features**:

- Virtually unlimited storage
- Highly durable
- Highly available
- Accessible over the internet
- Pay only for what you use

**Objects**:

- Objects are files stored in S3.
- Objects are stored inside buckets.
- Bucket names must be globally unique.

Every object contains:

- Data
- Metadata
- Unique key (name)

Simple Storage Service (S3) is AWS’s service for object storage. This is available through the AWS console, the CLI, SDKs, or REST APIs.

There’s no cap on how much you can store overall, and each individual object can be as large as 5 terabytes.

Durability is one of S3’s biggest advantages. It’s built to keep your data safe with 99.999999999% durability referred to as “11 nines” by automatically distributing copies of your files across multiple Availability Zones. You also get 99.99% availability, which is backed by a service-level agreement (SLA).

In terms of security, S3 supports encrypted data in transit (using SSL/TLS) and at rest (server-side or client-side encryption). You can also control who has access with IAM roles, bucket policies, and features like Object Lock, which is useful for compliance needs like write-once-read-many (WORM) storage.

Common use cases for S3:

- **Primary storage for cloud apps**: S3 is useful for hosting website assets, mobile app backends, gaming content, and files for distribution.
- **Data lakes and analytics**: S3 integrates with AWS tools like Athena, Redshift, and Elastic MapReduce (EMR) for large-scale data analysis.
- **Backup and disaster recovery**: S3 has features such as lifecycle policies that can be used for off-site backups. Lifecycle policies consist of rules that shift older backups to cheaper storage tiers or delete them when they’re no longer needed.
- **Serverless and event-driven workflows**: You can trigger Lambda functions on file uploads, integrate with the API Gateway, or route messages through SNS and Simple Queue Service (SQS) for automation.

### S3 Storage Classes

Different storage classes are used to optimise cost.

S3 Lifecycle Policies automatically move data between storage classes.

#### S3 Standard

Best for frequently accessed data, e.g.

- Websites
- Applications
- Active business files

Features:

- Highest availability
- Low latency
- Multi-AZ storage

#### S3 Standard-IA (Infrequent Access)

Best for data accessed occasionally, e.g.

Examples:

- Older project files
- Disaster recovery copies

Features

- Lower cost storage
- Retrieval fee applies

#### S3 One Zone-IA

Data stored in a single AZ.

Benefits:

- Cheaper than Standard-IA

Trade-off:

- Less resilient

#### S3 Intelligent-Tiering

Designed for when access to your data is unpredictable. The service automatically moves your data between tiers

### Glacier Tiers

When you need to store data long-term but access it rarely, S3’s archive classes provide flexible retrieval options depending on how fast you need the data back.

#### S3 Glacier Instant Retrieval

Used for archives that still need quick access, e.g.

- Medical records
- Archived reports

#### S3 Glacier Flexible Retrieval

Formerly "Glacier"

Low cost storage used for long-term archiving.

Retrieval in **Minutes to hours**

#### S3 Glacier Deep Archive

Lowest cost S3 storage.

Examples:

- Regulatory records
- Compliance archives

Retrieval in **Hours**

### Data Residency and Isolation

When data needs to stay within a specific geographic boundary—whether for compliance, sovereignty, or performance—Amazon S3 offers options designed to meet those strict requirements.

### Global Data Transfers

For scenarios where data must move quickly across regions, S3 Transfer Acceleration uses Amazon CloudFront’s globally distributed edge locations to speed up uploads to S3 buckets.

### S3 Static Website Hosting

S3 can host simple websites, suitable for:

- HTML
- CSS
- JavaScript

Not suitable for:

- Databases
- Server-side code

## Amazon Elastic Block Store (EBS)

Amazon EBS is the main persistent block storage for EC2 instances.

It’s a durable, high-performance block storage system that automatically replicates data within its Availability Zone. This means if the underlying hardware fails, your data remains protected, making them reliable for everything from development workloads to production databases.

Different volume types offer varying levels of performance and resilience, letting you balance cost, throughput, and availability based on your needs.

### Volume Types

EBS offers several volume types tuned for different needs:

- **General Purpose SSD (solid-state drive)**: These work well for boot volumes, development environments, and most general-purpose applications. Gp3, in particular, provides a baseline performance of 3,000 IOPS and 125 MiB/s throughput at any volume size, with the ability to provision additional performance up to 80,000 IOPS and 2,000 MiB/s throughput for an additional cost. This delivers a strong balance of predictable performance, flexibility, and cost efficiency.
- **Provisioned IOPS SSD**: If you’re running mission-critical database workloads, these volumes are a good option. They scale up to 256,000 IOPS and 4,000 MB/s throughput, with sub-millisecond latency and sizes up to 64 terabytes. This combination supports heavy database transactions without bottlenecks.
- **Throughput Optimized HDD**: This is suitable for big data workloads and data warehouses where high throughput (MB/s) is more important than IOPS.
- **Cold HDD**: This is built for infrequently accessed data with moderate throughput requirements.

For security, EBS volumes support encryption both at rest and in transit. They use Advanced Encryption Standard (AES)-256 encryption with AWS KMS.

### EBS Snapshots

Snapshots in EBS act as point-in-time copies of your volumes, used for:

- Backup
- Disaster recovery
- Restore volumes

These are incremental, meaning only changed data blocks are stored. This reduces both backup time and storage costs.

You can use snapshots to create new volumes, replicate data across AZs or regions, share data between AWS accounts, or enable fast restores. Other advanced snapshot features include locking for compliance, cross-region copies, archiving, and automated lifecycle management with Data Lifecycle Manager.

## Amazon EFS (Elastic File System)

EFS is a managed file storage service, supporting connections from multiple EC2 instances simultaneously, as well as on-premises servers.

Characteristics:

- Shared storage
- File storage
- Elastic growth
- Linux workloads

### Key Features

- EFS supports standard NFS; Multiple EC2 instances, or even your Linux servers running on-premises, can read from and write to the same file system at the same time. This makes it a good choice for workloads that need shared file access.

Similar to S3’s tiering, EFS can move files you don’t use often into lower-cost storage classes like Infrequent Access or Archive. This happens automatically.

When you create a regional file system, EFS stores your data redundantly across multiple AZs. Offering 11 nines’ (99.999999999%) durability and up to four nines’ (99.99%) availability (just like S3). Throughput and IOPS scale automatically to meet your workload demands.

### Storage Comparison

|Feature|S3|EBS|EFS|
|---|---|---|---|
|Storage Type|Object|Block|File|
|Used With|Many AWS Services|EC2|Multiple EC2|
|Shared Across EC2|No|No|Yes|
|Internet Accessible|Yes|No|No|
|Scales Automatically|Yes|Limited|Yes|

## AWS FSx
Amazon FSx is a fully managed, high-performance file system that offers multiple options to fit different workload needs:

- **FSx for Windows File Server**: Built specifically for Windows environments. It supports the Server Message Block (SMB) protocol, integrates smoothly with Active Directory, and includes features like file restore, quotas, access controls, and automatic backups.
- **FSx for Lustre**: Designed for compute-heavy workloads such as high-performance computing (HPC) and big data processing. It delivers ultra-fast, scalable storage with millions of IOPS and high throughput.

## AWS Storage Gateway

AWS Storage Gateway allows you to connect your on-premises data center to AWS storage without having to rip and replace your existing setup.

Benefits:

- Hybrid cloud
- Backup to AWS
- Disaster recovery

- **S3 File Gateway**: This gateway lets you present Amazon S3 as a file share via NFS or SMB protocols. Files are stored in S3 as objects, so you can apply S3 lifecycle policies for archiving or tiering. It’s a good choice for backups, archives, and hybrid data workflows.
- **Volume Gateway**: If you need block storage, Volume Gateway provides it using iSCSI, a network protocol that allows you to send commands for local hard drives over IP networks. You can run it in cached mode, where your main data sits in S3 and frequently accessed data is cached locally, or in stored mode, where your full data set remains on-premises and backups are sent to S3. This flexibility makes it a good option for hybrid storage needs and disaster recovery plans.
- **Tape Gateway**: This replaces physical tape libraries with a virtual tape library (VTL). Your existing backup software treats it like a normal tape system, but behind the scenes, virtual tapes are stored in S3 and can be archived to Glacier tiers.

## AWS Backup
AWS Backup is a fully managed service that centralizes backup management across your AWS resources and hybrid environments. Instead of juggling separate scripts or tools for each service, you can set up everything from a single console, the CLI, or APIs.

With AWS Backup, you can create backup plans that define what gets backed up, how often, and for how long. You can set schedules, retention rules, and lifecycle transitions to cold storage, and specify vault locations.

Can back up:

- EBS
- RDS
- EFS
- DynamoDB

Benefits:

- Manage backups from one place
- Automated backups
- Compliance

## Snow Family

Used for transferring large amounts of data into AWS.

**Snowcone**:

- Smallest device.
- Portable.

Suitable for:

- Remote locations
- Edge computing

**Snowball Edge**:

- Larger device.

Suitable for:

- Data migrations
- Large transfers

**Snowmobile**:

Physical truck-sized device.

Suitable for:

- Extremely large migrations

## Exam quick tips

|When you see...|Think...|
|---|---|
|Files, images, backups, websites|S3|
|Storage for one EC2 instance|EBS|
|Shared storage for multiple EC2 instances|EFS|
|Archive storage|Glacier|
|Move massive datasets into AWS|Snow Family|
