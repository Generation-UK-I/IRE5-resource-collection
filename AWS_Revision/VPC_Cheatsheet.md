# AWS VPC Cheat Sheet

## What is a VPC?

Amazon Virtual Private Cloud (VPC) allows you to create an isolated virtual network within AWS where you can launch and manage resources securely.

Think of it as:

- AWS Account = Office Building
- VPC = Floor/Department
- Subnet = Dept' resources
- EC2 Instance = Individual Computers/Servers

## Core Components

### VPC

A logically isolated network.

Features:

- Custom IP addressing
- Routing control
- Security controls
- Public/private subnets

Example:

- VPC: 10.0.0.0/16

Available IPs:

- 10.0.0.0 - 10.0.255.255

### CIDR Notation

Defines IP ranges.

|CIDR|Approx Hosts|
|---|---|
|/24|251|
|/23|507|
|/22|1,019|
|/16|65,531|

**AWS reserves 5 IPs per subnet.**

Example:

10.0.1.0/24

Reserved:

- 10.0.1.0: Network name
- 10.0.1.1: Gateway (router)
- 10.0.1.2: DNS
- 10.0.1.3: Experiement
- 10.0.1.255: Broadcast

Usable:

- 10.0.1.4 - 10.0.1.254

### Subnets

A subnet is a smaller network within a VPC.

#### Public Subnet

Has a route to an Internet Gateway.

Resources in Public Subnets:

- Web servers
- Load balancers
- Bastion hosts
- 0.0.0.0/0 → Internet Gateway
- NAT Gateway

#### Private Subnet

No direct route to an Internet Gateway.

Resources in Private Subnets::

- Databases
- Application servers
- Internal services

Internet access provided via NAT Gateway

### Internet Gateway (IGW)

Provides Internet access for public resources.

Requirements:

- Attached to VPC
- Public IP assigned
- Route table entry exists

Example:

0.0.0.0/0 → igw-12345

### NAT Gateway

Allows private instances to access the Internet without being reachable from it.

Use cases:

- OS updates
- Installing software
- Downloading packages

#### Traffic flow:

Private EC2 > NAT Gateway > Internet Gateway > Internet

Key fact:

- Outbound only
- No inbound initiation

### Route Tables

Control packet routing.

Example Route Table:

|Destination|Target|
|---|---|
|10.0.0.0/16|local|
|0.0.0.0/0|igw-12345|

Private Route Table:

|Destination|Target|
|---|---|
|10.0.0.0/16|local|
|0.0.0.0/0|nat-12345|

### Security Groups

Virtual firewalls at resource level.

Characteristics:

- Stateful
- Allow rules only
- Instance level

Default:

- Inbound: Deny
- Outbound: Allow

Example Web Server SG:

|Type|Port|Source|
|---|---|---
|HTTP|80|0.0.0.0/0|
|HTTPS|443|0.0.0.0/0|
|SSH|22|Your IP|

### Network ACLs (NACLs)

Subnet-level firwalls.

Characteristics:

- Stateless
- Allow and Deny rules
- Applied to subnets

Default NACL:

- Allow All Inbound
- Allow All Outbound

Example:

|Rule|Type|Action|
|---|---|---|
|100|HTTP|Allow|
|110|HTTPS|Allow|
|120|SSH|Deny|

#### Security Groups vs NACLs

|Feature|Security Group|NACL|
|---|---|---|
|Stateful|✅|❌|
|Stateless|❌|✅|
|Allow Rules|✅|✅|
|Deny Rules|❌|✅|
|Applied To|Instance/ENI|Subnet|
|Rule Processing|All Rules|Number Order|

### Elastic IP (EIP)

Static public IPv4 address.

Use when:

- Hosting public services
- Fixed whitelisted IP needed

Example:

203.0.113.25

Commonly attached to:

- NAT Gateways
- Bastion Hosts

### VPC Endpoints

Private connection to AWS services without Internet access.

Examples:

- S3
- DynamoDB
- Systems Manager
- Secrets Manager

Benefits:

- More secure
- No NAT costs
- Traffic stays on AWS network

### VPC Peering

Connects two VPCs.

VPC A ←→ VPC B

Features:

- Private communication
- Uses private IPs
- No transitive routing

Example:

With:  
VPC-A → VPC-B
VPC-B → VPC-C

You do not have:  
VPC-A → VPC-C

### Transit Gateway

Hub-and-spoke (star topology) networking.

Provides centralised management of multiple connected VPCs

Benefits:

- Simplifies routing
- Supports transitive routing
- Scales better

### VPC Flow Logs

Capture network traffic information.

Useful for:

- Troubleshooting
- Security investigations
- Auditing

Destinations:

- CloudWatch Logs
- Amazon S3

### Site-to-Site VPN

- Creates an encrypted tunnel over the public internet
- Quick setup with redundant tunnels for failover
  - On-premise deploy **Customer Gateway**
  - In VPC deploy **Virtual Private Gateway**
- Uses IPsec encryption for secure connectivity

## VPC Checklist

Must Haves:

- Route to IGW
- Public IP
- Security Group permits access

### Private Subnet Checklist

Must have:

- No direct IGW route
- NAT Gateway for outbound Internet
- Security Group

Remember:

- SG = Stateful and Allow Only
- NACL = Stateless and Allow/Deny

### NAT Gateway

Remember:

Allow private resources out to the Internet, Internet cannot initiate connections back.

### Troubleshooting Order

When connectivity fails, check:

1. Resource running?
2. Correct Subnet?
3. Route Table?
4. Security Group?
5. NACL?
6. Public IP?
7. Internet Gateway/NAT Gateway attached?
8. VPC Flow Logs?

Questions:

What does a virtual private cloud (VPC) allow you to do in AWS?

1. Control your virtual network setup, including subnets and route tables
2. Automatically encrypt all data at rest
3. Create EC2 instances without security groups
4. Connect directly to on-premises data centers without any setup

What is a function of a network address translation (NAT) gateway in a VPC?

1. It provides inbound internet access to private subnets.
2. It acts as a firewall.
3. It allows outbound internet connections from private subnets.
4. It stores database snapshots.

What is the purpose of a subnet in a VPC?

1. It provides Domain Name System (DNS) services.
2. It encrypts traffic in transit.
3. It segments IP ranges to organize resources.
4. It creates virtual private network (VPN) tunnels.

What is the primary use of AWS Site-to-Site VPN?

1. Creating an encrypted tunnel between on-premises and AWS
2. Direct physical fiber connection
3. Hosting DNS services
4. Caching web content globally

What is the purpose of an internet gateway in a VPC?

1. It encrypts traffic in transit.
2. It creates private subnets.
3. It allows resources to connect to the internet.
4. It provides DNS resolution.

<details><summary>Answers:</summary>

1 / 3 / 3 / 1 / 3

</details>


## AWS Direct Connect

AWS Direct Connect provides a dedicated, private fiber link from your premises directly into AWS, skipping the public internet altogether.

- **Setup and cost**: Direct Connect requires physical setup, either in your own facility or through a co-location partner. It usually takes anywhere from 4 to 12 weeks to establish, and upfront costs are higher compared to a VPN.
- **Performance**: Because it’s a private line, you get consistent bandwidth ranging from 1 Gbps up to 100 Gbps or more, with stable low latency and no fluctuations from internet congestion.
- **Security**: The connection is private, and you can also overlay an IPsec VPN for end-to-end encryption when needed.

## Route 53

Amazon Route 53 acts as a traffic director, sending users to where they need to go. That could be an EC2 instance, an S3 bucket, a server in your data center, or a third-party endpoint.

If you’re running a hybrid setup with resources split between AWS and on-premises, Route 53 routes traffic to the right place.

One of its most useful features is health checks:

- Every minute, Route 53 sends out quick probes using HTTP, HTTPS, TCP, or CloudWatch alarms to check that each endpoint is up and running.
- These results feed straight into CloudWatch, giving you near-real-time visibility.
- If a server fails, Route 53 automatically reroutes traffic to a healthy endpoint.

Route 53 gives you various options for routing traffic.

- **Simple Routing**: Maps a domain name to a single resource or IP address. Use this for a standard setup with one server.
- **Weighted Routing**: Splits traffic across multiple resources based on assigned numerical weights (e.g., 80% to server A, 20% to server B)
- **Latency-Based Routing**: Sends user requests to the AWS region that provides the lowest network latency.
- **Failover Routing**: Directs traffic to a primary resource, switching automatically to a secondary resource if a health check fails.
- **Geolocation Routing**: Routes traffic based on the geographic location (country or continent) of the user.
- **Geo-proximity Routing**: Directs traffic based on the physical distance between users and resources, allowing optional traffic biasing
- **Multivalue Answer Routing**: Responds to DNS queries with up to eight randomly selected healthy IP addresses, adding basic availability.
- **IP-Based Routing**: Routes traffic based on the client's source IP address network range

You can combine these routing rules however you need. Plus, Domain Name System Security Extensions (DNSSEC) adds an extra layer of security by cryptographically signing your DNS records to protect against tampering.

AWS also offers PrivateLink, which lets you securely access AWS services (like S3 or DynamoDB) and third-party SaaS applications directly through VPC endpoints. With PrivateLink, traffic never traverses the public internet, which reduces latency and significantly improves security for sensitive workloads.

Finally, Route 53 keeps detailed logs of every DNS query. You’ll see what was queried, when it happened, where it came from, and which firewall rule or policy applied.
