# AWS RDS Cheat Sheet

## What is Amazon RDS?

Amazon Relational Database Service (RDS) is a managed service for running relational databases in AWS.

Amazon Relational Database Service (RDS) simplifies setting up, operating, and scaling relational databases. It supports multiple database engines, including PostgreSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server, and IBM Db2.

In addition, Amazon Aurora is available as a high-performance, fully managed database service that is compatible with MySQL and PostgreSQL. Aurora offers enhanced scalability, reliability, and performance compared to traditional engines, making it a popular choice for demanding workloads

Managed service, i.e. **AWS manages**:

- Hardware
- Operating System
- Database patching
- Backups
- High availability

**Customer manages**:

- Database contents
- Users
- Permissions
- Application connections

Supported Database Engines:

- MySQL
- PostgreSQL
- MariaDB
- Oracle
- Microsoft SQL Server
- Amazon Aurora

Without RDS you would need to:

- Install database software
- Patch servers
- Configure backups
- Manage replication
- Replace failed hardware

With RDS AWS handles most of this automatically, including:

- Automated backups
- Automatic patching
- High availability
- Monitoring
- Scalability
- RDS Components
- DB Instance
- The actual database server.

You choose:

- Database engine
- Instance size (e.g. db.t3.micro)
- Storage size
- Storage

Database data is stored on EBS volumes. Volume types include:

- General Purpose SSD (gp3)
- Provisioned IOPS SSD

**Faster storage = better database performance.**

### Multi-AZ Deployment

Achieve **High Availability** with **Multi-AZ**

Primary Database --> Synchronous Replication --> Standby Database

Standby DB is usually placed in another Availability Zone.

Benefits:

- Automatic failover
- Increased availability
- Protection from AZ failure

Multi-AZ is for High Availability, **NOT** for performance scaling

### Read Replicas

Used to improve database read performance for read heavy workloads.

Key Point:

**Read Replica = Performance**
**Multi-AZ = Availability**

Multi-AZ vs Read Replica:

|Feature|Multi-AZ|Read Replica|
|---|---|---|
|High Availability|✅|❌|
|Read Scaling|❌|✅|
|Automatic Failover|✅|❌|
|Copy of Data|✅|✅|
|Disaster Recovery|✅|Limited|

### Automated Backups

RDS automatically backs up databases.

Benefits:

- Point-in-time recovery
- Recover from accidental changes
- Recover deleted data

Backup retention: 1 - 35 days

### Manual Snapshots

User-created backups.

Characteristics:

- Remain until deleted
- Can be restored at any time
- Useful before upgrades

Think:

Automated backups expire. Snapshots stay forever until removed.

### Scaling RDS

Vertical Scaling: Increase instance size.

Benefits:

- More CPU
- More memory
- Better performance

Read Scaling:

- Add read replicas.

Useful when:

- Many customers reading data
- Reporting workloads

### Security

RDS typically deployed in Private Subnets

Security Groups:

- Control database access.

Example: **Allow**: TCP 3306 (MySQL) **Source**: Application Server SG

Don't Allow from 0.0.0.0/0 unless specifically required.

Most production databases should not be publicly accessible.

### Encryption at Rest

Protects stored data.

Encrypts:

- Database files
- Snapshots
- Backups

### Encryption in Transit

Protects data traveling over the network.

Uses:

- SSL/TLS
- RDS Networking

### Amazon Aurora

AWS's cloud-native relational database.

Aurora is also built for high availability and durability. It maintains six copies of your data spread across three Availability Zones, aiming for an uptime of over 99.99%.

Compatible with:

- MySQL
- PostgreSQL

Advantages:

- Faster performance
- Highly available
- Automatic replication
- Managed by AWS

### Database Migration Service (DMS)

Used to migrate databases into AWS.

Example:

On-Premise SQL Server --> AWS DMS --> RDS

Benefits:

- Minimal downtime
- Supports ongoing replication

### Monitoring

Amazon CloudWatch

Monitor:

- CPU Utilization
- Memory
- Storage
- Connections
- Read/Write activity

Use CloudWatch alarms to notify administrators of issues.

### RDS Failure Scenario

If Primary DB fails and Multi-AZ is enabled AWS automatically promotes the standby database.

Result:

- Minimal downtime
- No manual intervention

Quick Exam Summary

If the question mentions:

|Requirement|Answer|
|---|---|
|Managed relational database|RDS|
|High availability|Multi-AZ|
|Read scaling|Read Replica|
|Restore to specific time|Automated Backup|
|Long-term backup|Snapshot|
|Database migration|DMS|
|AWS high-performance relational database|Aurora|
|Database encryption|KMS|
|Monitoring|CloudWatch|

### Don't Forget

- Multi-AZ = Availability
- Read Replica = Performance

There's a good chance they'll try and catch you out on these two concepts.

## Amazon Redshift

Amazon Redshift is a fully managed cloud **data warehouse** service designed to handle massive, petabyte-scale datasets. It allows organizations to run complex **analytical** queries using **standard SQL** without the need to manage infrastructure.

With Redshift, data can be quickly ingested from multiple sources—such as operational databases, data lakes, and streaming services—and then transformed into a format optimized for analytics. By leveraging columnar storage and parallel query execution, Redshift ensures high performance for large-scale workloads, making it well-suited for BI, reporting, and data-driven decision making.

A key strength of Amazon Redshift is its integration with AWS services and BI tools. It works seamlessly with **Amazon QuickSight** for visualization, supports connections with popular third-party tools like Tableau and Looker, and integrates with **Amazon S3** through Redshift Spectrum for querying data directly in data lakes. Its managed nature also means automatic scaling, backups, patching, and security features like encryption and network isolation are handled by AWS, reducing the operational burden.

## NoSQL

### Amazon DynamoDB

One of the most widely used **NoSQL** databases on AWS is Amazon DynamoDB, a fully managed **key-value** and **document** database. It delivers single-digit **millisecond response times** even at massive scale. For example, during Amazon Prime Day, DynamoDB processed trillions of API calls, peaking at nearly 90 million requests per second, while maintaining consistent low latency.

Key capabilities include the following:

- **Flexible data modeling**: Supports both key-value and document-style schemas, including nested data such as lists and maps.
- **Consistency and transactions**: DynamoDB supports ACID transactions for multi-item operations, providing strong consistency for critical workloads like order processing or financial systems.
- **SQL-like querying**: Supports PartiQL, a SQL-compatible query language for reading, updating, and inserting data without requiring DynamoDB’s native API syntax.
- **Caching**: For read-heavy workloads, DynamoDB Accelerator (DAX) offers in-memory caching that delivers sub-millisecond performance.

### Amazon Neptune

Amazon Neptune is AWS’s fully managed **NoSQL graph database**, designed for workloads where relationships between data points are critical.

This makes Neptune a strong choice for applications such as fraud detection, knowledge graphs, recommendation engines, and social networking platforms.

### Amazon ElastiCache

Amazon ElastiCache is a fully managed **in-memory caching** and database service designed to simplify deploying, operating, and scaling high-speed data layers in the cloud. It supports two open source engines: **Redis** and **Memcached**, letting you plug into existing tools.

## Database Migration Tools: DMS and SCT

A database migration tool moves your data, schemas, and database-specific behaviors from one system to another. You might use it when shifting from on-premises infrastructure to the cloud, switching to a different database engine, or consolidating several databases into a single system.

One of the key benefits of these tools is that they minimize downtime. They keep your source database running while syncing changes over to the target.

While **AWS DMS** handles moving your data, the **AWS Schema Conversion Tool** (SCT) takes care of **transforming** your schemas and database code. The SCT generates a detailed report highlighting what it converted automatically and what requires manual adjustments, which can save months of work compared to rewriting everything by hand.

DMS is a fully managed AWS service that you configure through the AWS Management Console or CLI. It enables both homogeneous and heterogeneous database migrations with minimal downtime.

## Chapter Quiz

What is a key advantage of using a self-managed database on Elastic Compute Cloud (EC2)?

1. Full control and flexibility over configuration and management
2. Automated backups and patching
3. Managed scaling with minimal downtime
4. Single-digit millisecond latency at scale

What does Amazon DynamoDB primarily provide?

1. Managed relational database
2. In-memory caching
3. Fully managed NoSQL key-value and document database
4. Schema conversion

What type of scaling does DynamoDB support to handle massive workloads?

1. Horizontal scaling
2. Vertical scaling only
3. No scaling capabilities
4. Manual scaling with downtime

What are Redis and Memcached supported by?

1. Amazon Relational Database Service (RDS)
2. Amazon DynamoDB
3. Amazon ElastiCache
4. Amazon Aurora

Which tool would you use to convert embedded SQL in applications during migration?

1. Amazon RDS
2. Amazon DynamoDB
3. AWS Database Migration Service (DMS)
4. AWS Schema Conversion Tool (SCT)

<details><summary>Answers:</summary>

1 / 3 / 1 / 3 / 4

</details>