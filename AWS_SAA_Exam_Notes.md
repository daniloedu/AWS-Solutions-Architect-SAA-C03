# Facts to remember from failed tests.

## Route 53

- Dynamically alter the size of geographic area → Geoproximity routing (**bias**).
- You can create an alias record at the top node of a DNS namespace, also known as the zone apex, however, you cannot create a CNAME record for the top node of the DNS namespace.
- Amazon Route 53 doesn't charge for alias queries to AWS resources but Route 53 does charge for CNAME queries.
    - An Alias record can only redirect queries to selected AWS resources such as Amazon S3 buckets, Amazon CloudFront distributions, and another record in the same Amazon Route 53 hosted zone.
    - A CNAME record can redirect DNS queries to any DNS record. So, you can create a CNAME record that redirects queries from [app.covid19survey.com](http://app.covid19survey.com/) to [app.covid19survey.net](http://app.covid19survey.net/).
- Route 53 and On premises DNS:
    
    ![Resolver-outbound-endpoint.png](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/Resolver-outbound-endpoint.png)
    
    ![Resolver-inbound-endpoint.png](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/Resolver-inbound-endpoint.png)
    
- All Amazon‐specific request headers begin with x-amz. This is important to remember as it will help eliminate lots of incorrect answers. This leaves only x-amz-mfa.

## S3

- Storage class analysis only provides recommendations for Standard to Standard IA classes.

![lifecycle-transitions-v4.png](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/lifecycle-transitions-v4.png)

- AWS does not provide automatic recommendations for S3 ONEZONE_IA or Glacier storage classes due to data durability and archival considerations. You must manually configure Lifecycle Policies to transition data to these classes.
- For S3 Standard-IA. The minimum storage duration charge is 30 days, so this option is NOT cost-effective for less than a month.
- MFA Delete applies to delete objects, not buckets.
- MFA affects changing the versioning state of a bucket or permanently deleting any object (or a version of that object).
- Metadata in S3:
    - Deleting an object’s metadata while leaving the object intact does not require MFA Delete.
    - Metadata, which can be included with the object, is not encrypted while being stored on Amazon S3. Therefore, AWS recommends that customers not place sensitive information in Amazon S3 metadata.
- Only the root account that owns the bucket can enable MFA.
- The bucket owner, root account, and all authorized IAM users of a bucket are allowed to enable versioning.
- Versioning is a mandatory prerequisite for Cross-Region Replication (CRR). Both source and destination buckets must have versioning enabled. This is a fundamental requirement that cannot be bypassed
- **Amazon S3 Object Lock** is an Amazon S3 feature that allows you to store objects using a write once, read many (WORM) model. You can use WORM protection for scenarios where it is imperative that data is not changed or deleted after it has been written.
- AWS S3 console cannot be used to copy 1 petabyte of data from one bucket to another as it's not feasible.
- The AWS S3 sync command uses the CopyObject APIs to copy objects between Amazon S3 buckets.
- **Amazon S3 Batch Replication** provides you a way to replicate objects that existed before a replication configuration was in place, objects that have previously been replicated, and objects that have failed replication.
- While S3 is more available, all the S3‐based storage classes provide the same first byte latency: milliseconds. Remember that performance is identical; availability is not.
    - With the exception of Glacier, retrieving data from the various S3 storage classes should be virtually identical
    - Glacier data retrieval, using the standard class, takes 3–5 hours on average.
- An RTMP distribution is the Adobe Real‐Time Messaging Protocol and is suitable for using S3 buckets as an origin server to serve streaming media.
- Only Standard Amazon SQS queue is allowed as an Amazon S3 event notification destination, whereas FIFO SQS queue is not allowed.
- To better support short-lived, lightweight messaging destinations, AWS recommends Amazon SQS Temporary Queue Client. This client makes it easy to create and delete many temporary messaging destinations without inflating your AWS bill.
- Capacity for SQS:
    - Standard Queue - Unlimited (Best effort)
    - FIFO - 300 TPS - 3000 TPS with batching.
- S3 supports two styles of bucket URLs:
    - virtual‐hosted‐style and path‐style URLs. Virtual‐hosted‐style URLs are of the form
        - http://bucket.s3-*aws-region*.amazonaws.com
    - Path‐style URLs are the traditional URLs you’ve seen:
        - https://s3-*aws-region*.amazonaws.com/*bucket-name*.
- Depending on your Region, your Amazon S3 website endpoints follow one of these two formats:
    - s3-website dash (-) Region ‐ [http://bucket-name.s3-website.Region.amazonaws.com](http://bucket-name.s3-website.region.amazonaws.com/)
    - s3-website dot (.) Region ‐ [http://bucket-name.s3-website-Region.amazonaws.com](http://bucket-name.s3-website-region.amazonaws.com/)
- A byte-range request is a perfect way to get the beginning of a file and ensuring we remain efficient during our scan of our Amazon S3 bucket.
- To encrypt an object at the time of upload, add a header called **x-amz-server-side-encryption** to the request to tell S3 to encrypt the object using **SSE-C, SSE-S3, or SSE-KMS**.:
    - There are two possible values for the x-amz-server-side-encryption header:
        - AES256, → to use S3-managed keys,
        - aws:kms, → to use AWS KMS–managed keys.

## Storage

- AWS DataSync is an online data transfer service that simplifies, automates, and accelerates copying large amounts of data to and from AWS storage services over the internet or AWS Direct Connect.
    - Up to 10 times faster than command-line tools. It is natively integrated with Amazon S3, Amazon EFS, Amazon FSx for Windows File Server, Amazon CloudWatch, and AWS CloudTrail, which provides seamless and secure access to your storage services, as well as detailed monitoring of the transfer.
    - AWS DataSync uses a purpose-built network protocol and scale out architecture to transfer data. A single DataSync agent is capable of saturating a 10 Gbps network link.
- The AWS Transfer Family provides fully managed support for file transfers directly into and out of Amazon S3 and Amazon EFS. **Not for Amazon FSx for Windows File Server**.
- **Not all EBS volume types** support **Multi-Attach**—only **Provisioned IOPS SSD (io1/io2) does!**
- Amazon EFS Throughput Modes Table

| **Mode** | **Best Use-Case** | **Key Features** | **Cost Considerations** |
| --- | --- | --- | --- |
| ✅ Bursting Throughput Mode | Best for variable workloads that have peaks of activity | Throughput scales dynamically based on the file system size | ✅ Cost-effective for smaller workloads (no fixed charges) |
| ✅ Provisioned Throughput Mode | Best for consistent, high-throughput workloads (e.g., ML data pipelines) | Allows you to set a fixed throughput regardless of file system size | ❌ More expensive, as you pay for provisioned capacity even if not used |
| ✅ Max I/O Mode (for performance) | Best for large-scale, high-concurrency workloads (e.g., big data analytics) | Higher throughput, higher latency | 🚀 Optimized for parallel processing but not ideal for small ECS tasks |
- FSx for ONTAP is the only AWS-managed service that supports both NFS (Linux, Mac) and SMB (Windows) simultaneously.
- Amazon EFS with Intelligent-Tiering:
    - EFS only supports NFS (for Linux/macOS)
- When securing EFS access, always combine VPC security groups + IAM policies for best practice security
- EFS is a POSIX compliant file storage system

## CloudFront

- Signed URLs and Signed cookies (when you don’t want to change the URL) are the best way to restrict the users accessing an app.
- A CloudFront distribution is a collection of edge locations across the world.
    - While edge locations are typically read from by clients, they are also writeable. You can store objects on edge locations as well as read from them.
- OAI (Currently OAC) is specifically designed to:
    - Allow CloudFront to access S3 content
    - Block all other direct access to the S3 bucket
    - Create a secure connection between CloudFront and S3
- **Use field-level encryption in Amazon CloudFront to protect sensitive data for specific content →** Field-level encryption allows you to enable your users to securely upload sensitive information to your web servers. This instead of KMS.
    - You can encrypt up to 10 data fields in a request. (You can’t encrypt all of the data in a request with field-level encryption; you must specify individual fields to encrypt.)
- CloudFront automatically provides AWS Shield (standard) to protect from DDoS, and it also can integrate with AWS WAF and AWS Shield Advanced
    - Shield is for network-level protection, while WAF is for HTTP request filtering.
- **If you need to filter or limit web traffic, AWS WAF is the best choice!**
- CloudFront supports a variety of origin servers, including a non‐AWS origin server
- AWS provides Rate-Based Rules in AWS WAF, which can block excessive requests from attackers while allowing normal traffic.
- For CloudFront, the invalidation API is the fastest way to remove a file or object, although it will typically incur additional cost.

| Use Case | Best Choice |
| --- | --- |
| **Serving static & dynamic content globally (HTML, images, videos, APIs)** | ✅ **CloudFront** |
| **Accelerating website load times** | ✅ **CloudFront** |
| **Caching API responses for lower latency** | ✅ **CloudFront** |
| **Reducing latency for non-HTTP traffic (gaming, VoIP, VPN, financial apps)** | ✅ **Global Accelerator** |
| **Providing automatic failover across AWS regions** | ✅ **Global Accelerator** |
| **Speeding up TCP/UDP-based applications** | ✅ **Global Accelerator** |
| **Handling DDoS protection & security for web apps** | ✅ **CloudFront (WAF & Shield)** |

## Computing

- To get the meta-data of an EC2 instance:
    - [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)
- You can change the tenancy of an instance from dedicated to host and vice versa. You CAN’T go to the default tenancy.
- If either Launch Template Tenancy or VPC Tenancy is set to dedicated, then the instance tenancy is also dedicated.
- **Launch Templates vs. Launch Configurations for Auto Scaling Groups**

| **Feature** | **Launch Templates (✅ Newer, Recommended)** | **Launch Configurations (❌ Older, Deprecated)** |
| --- | --- | --- |
| **AWS Recommendation** | ✅ **Preferred method** for Auto Scaling Groups (ASG) | ❌ **Legacy method** (No new feature updates) |
| **Flexibility** | ✅ Allows multiple instance types & purchase options | ❌ Only supports a **single instance type** |
| **IAM Permissions** | ✅ Supports **instance profiles** at launch | ❌ Must be manually assigned after creation |
| **User Data Updates** | ✅ Supports **modification** without needing to recreate ASG | ❌ Must create a **new launch configuration** every time |
| **Spot Instances** | ✅ Supports **Spot Fleets & Mixed Instance Policies** | ❌ Doesn't support spot instance diversification |
| **Elastic GPUs & T2/T3 Unlimited** | ✅ Fully supported | ❌ Not supported |
| **Availability** | ✅ Fully available and recommended by AWS | ❌ Being phased out (No longer updated) |
- Termination Priority:

| **Step** | **Termination Criteria** | **Notes** |
| --- | --- | --- |
| **1️⃣** | Remove an instance from an overcrowded Availability Zone | Ensures balanced distribution across AZs |
| **2️⃣** | Terminate instances with the oldest Launch Configuration | AWS prioritizes removing Launch Configurations first, since they are deprecated. |
| **3️⃣** | Terminate instances with the oldest Launch Template version | If all instances use Launch Templates, the oldest template version is removed first. |
| **4️⃣** | Check instance weight priority (if ASG uses mixed instance types) | If configured, AWS considers weight-based termination. |
| **5️⃣** | Terminate the instance closest to the next billing hour | Helps minimize cost for On-Demand instances. |
- You can choose reserved instances for EC2 and RDS
- AWS Cloud Map is used for service discovery: no matter how Amazon ECS tasks scale, AWS Cloud Map service names would point to the right set of Amazon ECS tasks.
- You can host multiple TLS secured applications, each with its own TLS certificate, behind a single load balancer. To use SNI, all you need to do is bind multiple certificates to the same secure listener on your load balancer. ALB will automatically choose the optimal TLS certificate for each client.
- EC2 root volumes are terminated on instance deletion, and *by default*, additional EBS volumes attached to an instance are not.
- Instances can have up to 28 attachments. One of those attachments is the network interface attachment, leaving 27 attachments available for EBS volumes
- Placement Groups:
    - Partition Placement Groups are **designed for big data applications like Hadoop, Spark, and HDFS**, which use distributed computing.
        - **Each partition is mapped to a different rack in the data center**, ensuring fault isolation.
    - **Cluster Placement Groups** are meant for **low-latency workloads** rather than ETL, and they do not improve availability.
    - Spread Placement Groups are limited to 7 instances per AZ
        - Used for high availability but NOT for large-scale distributed workloads.
        - Not scalable for big data workloads.
    - Spread placement groups can span availability zones and support up to seven instances per zone . Like cluster groups, this results in increased throughput for network activity. You must specify the distinct underlying hardware for spread placement groups.
- For Partition Placement groups:
    - Up to 7 partitions per AZ, but each partition can hold many instances (no strict per-partition limit).
- An SSD volume is best for transactional workloads with a large number of small I/O sized read/write operations.
- An HDD‐backed volume is best for streaming workloads where throughput needs to be maximized over IOPS.
- Internet gateways scale horizontally, not vertically. They are also redundant and highly available automatically.
- You can copy snapshots across accounts, but the default permissions do not allow this.
    - Amazon EBS volumes are Availability Zone (AZ) locked
    - So you have to modify those permissions, and then the snapshot can be copied to any other AWS account, regardless of the account owner.
- SPOTs:
    - A SPOT instance pool is made up of unused EC2 instances.
    - A SPOT fleet is a collection, or a fleet, of SPOT instances, and optionally On-Demand instances
    - The SPOT Fleet attempts to launch the number of SPOT instances and On-Demand instances to meet the target capacity specified in the SPOT fleet request.
    - A SPOT capacity pool is a set of unused EC2 instances with the same instance type, operating system, availability zone, and network platform.
- There are three request types for SPOT Request, Request And Maintain, and Reserve For Duration.
- **By default, cross-zone load balancing is enabled for Application Load Balancer and disabled for Network Load Balancer**
- A spot instance interruption occurs when the spot price rises above your maximum.
- A spot fleet is a group of spot instances launched together.
- A recovered instance by CloudWatch is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata.
    - Only supports EBS for EC2 not instance store.

**📌 Difference Between Simple Scaling & Other Scaling Policies**

| **Feature** | **Simple Scaling** | **Step Scaling** | **Target Tracking** | **Scheduled Scaling** | **Predictive Scaling** |
| --- | --- | --- | --- | --- | --- |
| **Trigger Type** | CloudWatch alarm | CloudWatch alarm | Continuous monitoring | Fixed schedule | Machine Learning-based prediction |
| **Response Time** | 🚫 Slow (waits for cool-down) | ⏩ Faster (multi-step changes) | ✅ Real-time adjustments | ⏳ Time-based | 🤖 AI-driven |
| **Scalability** | ❌ One instance at a time | ✅ Multiple instances per step | ✅ Keeps metric stable | ✅ Predefined schedule | ✅ Pre-scales for expected load |
| **Best for** | Basic workloads | Sudden spikes | Consistent load management | Predictable daily loads | Seasonal trends (e.g., holiday sales, live TV) |
- When rebalancing, Amazon EC2 Auto Scaling launches new instances before terminating the old ones, so that rebalancing does not compromise the performance or availability of your application
- You can pass two types of user data to Amazon EC2 shell scripts and cloud-init directives.

## Database and Warehouse:

- Redshift is the prime example of AWS providing an OLAP service.
- EMR, or Elastic Map Reduce, is most commonly used with Hadoop and Spark.
- Aurora actually stores a whopping six copies of your data, across three availability zones, to ensure failover and disaster recovery.
- RDS allows backup retention periods up to 35 days.
- It’s possible to configure Amazon RDS to use SSL for Data Transit:
    - Amazon RDS creates an SSL certificate and installs the certificate on the DB instance
        - For MySQL → —ssl-ca parameter
        - SQL Server → Download and install in Windows.
        - Oracle → Use the Oracle native network encryption.
- Amazon Neptune is a fast, reliable, fully managed graph database service that makes it easy to build and run applications that work with highly connected datasets
- About Read Replica and Multi AZ:
    - Read Replica:
        - Improves network performance and improves read performance.
        - Provides asynchronous replication
        - Associated with high **scalability.**
    - Multi‐AZ:
        - Setup does not provide improved performance
        - Provides Data redundancy and Disaster Recovery
        - It helps with **availability.**
        - Setups use synchronous replication to back up data to the secondary instance for the purposes of disaster recovery.
- AWS DMS enables you to seamlessly migrate data from supported sources to relational databases, data warehouses, streaming platforms, and other data stores in AWS cloud.
- DynamoDB offers push-button scaling
- Although it’s not particularly common, you can set up a read replica in an on‐premises instance.
- With Read Replicas you *can* manually promote a read replica instance to a stand‐alone instance if you have to.
- For the selection of storage for a customized database, it is better to use an EC2 instance with proper disks.
- When failing over, Amazon RDS simply flips the canonical name record (CNAME) for your DB instance to point at the standby, which is in turn promoted to become the new primary.
- AWS RDS Custom Supports:
    - Oracle
    - SQL Server
- The backup feature of Amazon RDS in a multi-AZ deployment that creates backups across multiple Regions
- You can quickly create clones of an Aurora DB by using the database cloning feature.
    - Database cloning uses a copy-on-write protocol, in which data is copied only at the time the data changes, either on the source database or the clone database.
    - Cloning is much faster than a manual snapshot of the DB cluster.
- Upgrades to the database engine level require downtime. Even if your Amazon RDS DB instance uses a Multi-AZ deployment, both the primary and standby DB instances are upgraded at the same time.

## Misc:

- Athena can’t be used in real-time.
- Recovery Options
    
    ![pt3-q24-i1.jpg](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/pt3-q24-i1.jpg)
    
- AWS CloudFormation StackSet extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation. A stack set lets you create stacks in AWS accounts across regions by using a single AWS CloudFormation template
- Cognito Pools:
    - User → Federated Login with Google, FB, Amazon
    - Identity → Temporary access to your AWS resources.

## Serverless

- **Amazon EventBridge** is the only event-based service that integrates directly with third-party SaaS partners
    - Amazon EventBridge also automatically ingests events from over 90 AWS services without requiring developers to create any resources in their account. Further, Amazon EventBridge uses a defined JSON-based structure for events
- AWS Lambda currently supports 1000 concurrent executions per AWS account per region
- **Since AWS Lambda functions can scale extremely quickly, it's a good idea to deploy a Amazon CloudWatch Alarm that notifies your team when function metrics such as `ConcurrentExecutions` or `Invocations exceeds the expected threshold`**
- Example to calculate a Queue:
    - Let's say that the current ApproximateNumberOfMessages is 1500 and the fleet's running capacity is 10. If the average processing time is 0.1 seconds for each message and the longest acceptable latency is 10 seconds, then the acceptable backlog per instance is 10 / 0.1, which equals 100. This means that 100 is the target value for your target tracking policy. If the backlog per instance is currently at 150 (1500 / 10), your fleet scales out, and it scales out by five instances to maintain proportion to the target value.
- Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. APIs act as the "front door" for applications to access data, business logic, or functionality from your backend services. Using API Gateway, you can create RESTful APIs and WebSocket APIs that enable real-time two-way communication applications. API Gateway supports containerized and serverless workloads, as well as web applications.
    - You can enable Amazon API caching in Amazon API Gateway to cache your endpoint's responses. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of requests to your API.
- SWF
    - SWF provides an API, but it is neither the AWS‐specific API nor language specific. Instead, SWF supports standard HTTP requests and responses.
    - SWF is typically thought of as an asynchronous service, but it also supports synchronous tasking when needed.
    - SWF tasks are assigned once and only once.
    - A SWF domain is a collection of related workflows.
- Eventbridge connects with external and synchronous APIs or SaaS

## Networking:

- Transit Gateway allows you to have multiple VPN connections.
    - AWS Transit Gateway also enables you to scale the IPsec VPN throughput with equal cost multi-path (ECMP) routing support over multiple VPN tunnels. A single VPN tunnel still has a maximum throughput of 1.25 Gbps.
- The default VPC has a CIDR block of /16, but the default subnet in each AZ is a /20.
- The default VPC has an Internet gateway, and instances are given public IP addresses, so it’s public.
- AWS accounts allow you five (5) elastic IP addresses per region by default.
- Both ALBs and ELBs offer SSL termination. While an ALB is a better choice when considering the management of SSL certificates—due to its ACM integration—both ELBs and ALBs are correct when considering *just* SSL termination.
- ElastiCache uses shards as a grouping mechanism for individual REDIS nodes. So a single node is part of a shard, which in turn is part of a cluster
- Amazon ElistiCache requires changes in the application code to be implemented. A read replica doesn’t require changes in the code.
    
    ![pt5-q16-i2.jpg](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/pt5-q16-i2.jpg)
    

- A simple routing policy allows single and multiple resources for both the primary and secondary resources
- All outbound traffic is allowed to pass out of a VPC by default to the default VPC, although no inbound traffic is allowed.
- The default NACL allows in and out all traffic, which is somewhat unintuitive. Keep in mind that the default *security group* disallows inbound traffic, but the default NACL allows that traffic in.
- Although sticky sessions enable each user to interact with one server and one server only, however, in case of an unhealthy server, all the session data is gone as well.
- To enable private hosted zones:
    - enableDnsHostnames
    - enableDnsSupport
- EFS Performance Modes:
    - General Purpose mode → Has the lowest per-operation latency and it’s the default mode for file systems.
    - Max I/O mode is perfect for highly parallelized workloads that can tolerate higher latencies.
- Facts:
    - You can add additional secondary CIDR blocks to a VPC (up to four).
    - Subnets must have CIDR blocks, and the block must be the same as or smaller than the CIDR block for the VPC within which it exists.
    - An elastic network interface is virtual and can have multiple IPv4 and IPv6 addresses as well as security groups, a MAC address, and a source/destination check flag.
    - Internet gateways attach to VPCs and serve multiple subnets (if needed).
    - You can move ENI across regions and VPCs.

**Summary: Routing Policies & Failover Support**

| **Routing Type** | **Supports Failover?** | **Best Alternative if Failover is Needed** |
| --- | --- | --- |
| **Simple Routing** | ✅ Yes | - |
| **Latency-Based Routing** | ✅ Yes | - |
| **Weighted Routing** | ✅ Yes | - |
| **Geolocation Routing** | ✅ Yes | - |
| **Failover Routing** | ✅ Yes | - |
| **Geoproximity Routing** | ❌ No | Use **Geolocation Routing + Failover** |
| **Multivalue Answer Routing** | ❌ No | Use **Weighted Routing + Health Checks** |
- ALB **cannot use EC2 health checks**—it only supports ALB-level health checks.
- GuardDuty sources
    - AWS CloudTrail events,
    - CloudTrail S3 data events.
    - Amazon VPC Flow Logs,
    - DNS logs.
- VPC sharing (part of Resource Access Manager) allows multiple AWS accounts to create their application resources such as Amazon EC2 instances, Amazon RDS databases, Amazon Redshift clusters, and AWS Lambda functions, into shared and centrally-managed Amazon Virtual Private Clouds (VPCs).
    - To set this up, the account that owns the VPC (owner) shares one or more subnets with other accounts (participants) that belong to the same organization from AWS Organizations. After a subnet is shared, the participants can view, create, modify, and delete their application resources in the subnets shared with them.
    - Participants cannot view, modify, or delete resources that belong to other participants or the VPC owner.
- Connection Draining in Elastic Load Balancer enables the load balancer to complete in-flight requests made to instances that are de-registering or unhealthy.
    - The maximum timeout value can be set between 1 and 3,600 seconds (the default is 300 seconds).
- NAT Intances can:
    - Support Port Forwarding
    - Be used as bastion server
    - Can be associated with security groups.
- For Security Groups you can use:
    - IP address for a custom source of an inbound rule
    - A range of IP addresses in CIDR notation as an inbound rule
    - A security group as the custom source of an inbound rule
- CloudHub → If you have multiple AWS Site-to-Site VPN connections, you can provide secure communication between sites using the AWS VPN CloudHub.
    - Your remote sites to communicate with each other, and not just with the VPC. Sites that use AWS Direct Connect connections to the virtual private gateway can also be part of the AWS VPN CloudHub.
- **AWS Shield vs. AWS WAF**

| **Feature** | **AWS Shield** | **AWS WAF** |
| --- | --- | --- |
| **Primary Purpose** | Protects against **DDoS attacks** | Protects against **web exploits (SQL injection, XSS, bots)** |
| **Default Version** | **Shield Standard** (free, always enabled) | ❌ **Not enabled by default, must be configured** |
| **Paid Version** | **Shield Advanced** (enhanced DDoS protection) | **WAF (custom rules for HTTP/S traffic)** |
| **DDoS Protection?** | ✅ Yes, at both **network layer (L3/L4) and application layer (L7)** | ❌ No |
| **Web Application Protection?** | ❌ No | ✅ Yes |
| **Best For:** | **Mitigating large-scale DDoS attacks** | **Blocking common web vulnerabilities & bad bot traffic** |
| **Use With CloudFront?** | ✅ Yes, for global DDoS protection | ✅ Yes, for filtering unwanted traffic |
- **🚀 AWS Firewall Manager: Overview & Capabilities:** AWS Firewall Manager is a centralized security management tool that helps organizations enforce firewall policies across multiple AWS accounts within an AWS Organizations setup.

| **Feature** | **Description** |
| --- | --- |
| **Controls** | AWS WAF, AWS Shield Advanced, VPC Security Groups |
| **Operates On** | AWS Organizations (multiple accounts) |
| **Use Case** | Centrally manage firewall policies and enforce security rules |
| **Automates** | Deployment of firewall rules, DDoS protection, and security group policies |
| **Interacts With** | AWS WAF, AWS Shield Advanced, AWS Security Groups |
| **Cannot Control** | Amazon GuardDuty, Amazon Inspector, Network ACLs |

## Savings

- AWS Cost Explorer helps you identify under-utilized Amazon EC2 instances that may be downsized on an instance by instance basis within the same instance family, and also understand the potential impact on your AWS bill by taking into account your Reserved Instances and Savings Plans.
- AWS Compute Optimizer recommends optimal AWS Compute resources for your workloads to reduce costs and improve performance by using machine learning to analyze historical utilization metrics.
- Tags can take up to 24 hours to appear and they can’t be applied to legacy resources. You’re actually allowed only two free budgets per account. Cost allocation tags are managed from the Cost Allocation Tags page.
- Snowball versions
    
    ![pt3-q56-i1.jpg](Facts%20to%20remember%20from%20failed%20tests%2018533216076580479d3fccd63d2712b5/pt3-q56-i1.jpg)
    

## Security

- There are four types of policies in IAM:
    - Identity based
    - Resource based
    - Organization SCPs
    - Access control lists (ACLs).
- Security groups work for launch configurations just as they do with instances: you may use as many as you like.
- Security groups only contain allow rules, not deny rules. Then, you can create both inbound and outbound rules.
- For client‐side encryption, you’ll need a master key, which can either be a KMS‐managed key or a client‐side master key. You’ll also need an SDK for encrypting the client‐side data.
- There are four types of data encrypted when an EBS volume is encrypted:
    - Data at rest on the volume
    - Data moving between the volume and the instance
    - Snapshots created from the volume
    - Volumes created from those snapshots.

## The Well‐Architected Framework

- Recommends automating security best practices and responses to security events.
- Includes four areas for security in the cloud:
    - data protection
    - Infrastructure protection
    - Privilege management
    - Defective controls.
- AWS infrastructure operates at the VPC layer and is almost entirely virtual.
- AWS uses the term *managed services* in lots of areas, that term is *not* used in the shared responsibility model as one of the core types of services.
- By default, newly created S3 buckets are private. They can only be accessed by a user that has been granted explicit access.

## Analytics

- **Internal applications can consume raw transactions from Kinesis Data Streams** before any transformations.
- **Kinesis Data Firehose is for near real-time delivery but does not allow multiple consumers to read the raw transactions.**
    - It can only have as many consumers as shards in Kinesis (which is in practice, much less than the number of producers).
- Unlike **Kinesis Data Streams**, **Firehose does not allow internal applications to consume raw transaction data before processing**.
- By default, the 2MB/second/shard output is shared between all of the applications consuming data from the stream. You should use enhanced fan-out if you have multiple consumers retrieving data from a stream in parallel. With enhanced fan-out developers can register stream consumers to use enhanced fan-out and receive their own 2MB/second pipe of read throughput per shard, and this throughput automatically scales with the number of shards in a stream.