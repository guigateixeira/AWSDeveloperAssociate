# AWSDeveloperAssociate

## Section 3: EC2 Instance Storage

### EBS Volume:

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/67bfc707-8178-4e6d-9e60-1301ed525e91)


* A EBS (Elastic Block Storage) Volume is a network drive that you can attach to your instances while they run.
* It Allows your instances to persist data, even after termination.
* They can only be mounted to one instance at a time.
* They are bounded to a specific availability zone (Can't put a us-east-1a EBS to a instance in us-east-1b).

It's mainly used to get data back from a instance that stoped into a new instance. (It's almost like a "network USB stick)

* It's a network drive (not a physical drive).
  * It uses the network to communicate the instance, which means there might be a bit of latency.
  * It can be detached from a EC2 instance and attached to another one quickly. (Makes its handy to do fails-over)
 
* It's locked to an AZ, and to move a volume across, you need to snapshopt it.

* Have a provisioned capacity (size in GBs and IOPS).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9491a634-4c74-4724-8244-00c1a3b46bae)

  Delete on Terminate:

  Controls the EBS behaviour when an EC2 instance terminates
  * By default, the root EBS volume is deleted (enabled).
  * By default, any other attached EBS volume is not deleted.
 
  If you want to preser the data from root when it's terminated we need to disable this attribute.

  #### EBS Snapshots

  * Make a backup (snapshot) of your EBS volume at a point in time.
  * Not necessary to detach volume to do snapshot, but it is recommended.
  * Can copy snapshots across AZ or Regions.
 
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5f9223c3-408d-499f-8f06-cc786baa7573)

  EBS Snapshots Features:
  * EBS Snapshot Archive:
    * Move Snapshot to an "archive tier" that is 75% cheaper.
    * Takes 24 to 72 hours for restoring the archive.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1177116e-935d-42c9-b217-c5f4dc1380a7)

  * Recycle Bin for EBS Snapshots
    * Setup rules to retain deleted snapshots so you can recover them afyer an accidental deletion.
    * Specific retation (from 1 day to 1 year).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/710514aa-5cee-4a72-a570-009d4f3c23bf)

   * Fast Snapshot Restore (FSR)
     * Force full initialization of snapshot to have no latency on the first use (very expensive $$$).
    
   **We can create volume from snapshots.**

* Creating a snapshot:
  ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/83aeab4b-6d76-4268-855a-bd10484c99f4)

  ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/31504424-2cbf-4a87-bc56-96f273d60779)

* Copying a snapshot:
  ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a787a17e-91c4-48ea-a05a-2b469104a5e7)

#### EBS Volume Types

* EBS Volume come in 6 types:
  * **gp2/gp3 (SSD)**: General porpose SSD volume that balances price and performance for a wide variety of workloads.
  * **io1/io2 Block Express (SSD)**: Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads.
  * **st 1 (HDD)**: Low cost HDD volume designed for frequently accessed, throughput-intensive workloads.
  * **sc 1 (HDD)**: Lowest cost HDD volume designed for less frequently accessed workloads.
 
* EBS Volumes are caracterized in Size | Throughput | IOPS (I/O Ops Per Sec)
* **Only gp2/gp3 amd io1/io2 Block Express can be usedas boot volumes.**

##### GP SSD
* Cost effective stogae, low-latency.
* System boot volumes, virtual desktops, development and test envs.
* 1GiB - 16GiB.
* gp3:
  * Baseline of 3000 IOPS and throughput of 125 MiB/s.
  * Can increase IOPS up to 16000 and throughput to 1000 MiB/s.
* gp2:
  * Small gp2 volumes can burst IOPS to 3000.
  * Size of the volumes and IOPS are linked, max IOPS is 16000.
  * 3 IOPS per GB.
 
##### Provisioned IOPS (PIOPS) SSD
* Critical business applications with sustained IOPS performance.
* Or application that need more than 16000 IOPS.
* Great for databases workloads
* io 1 (4GiB - 16 GiB):
  * Max PIOPS: 64000 for NItro EC2 instances and 32000 for others.
  * Can increase PIOPS for independently from storage size.
* io 2 Block Express (4GiB - 64 GiB):
  * Sub-millisecond latency.
  * Max PIOPS: 256000 with an IOPS:GiB ration of 1000:1.
* Supports EBS Multi-Attach.

##### HDD
* Can't be a boot volume.
* 125 GiB to 16 TiB.
* Throughput Optimized HDD (st1):
  * Big Data, Data Warehouses, Log Processing.
* Cold HDD (sc1):
  * For data that is infrequently accesses.
  * Scenarios where lowest cost is important. 

#### EBS Multi-Attach - io1/io2
* Attach the same EBS volume to multiple EC2 instances in the same AZ.
* Each instance has full read & write permissions to the high-performance volume.
* Use cases:
  * Achive higher application availability in clustered Linux apps.
  * Apps must manage concurrent write operations.
* Up to 16 EC2 instances at a time.
* Must use a file system that's cluster-aware (not XFS, EXT4, ...)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/267ffcaf-9050-4938-8719-445e6b73a9f0)

### EFS - Elastic File System
* Managed NFS (network file system) that can be mounted on many EC2.
* EFS works with EC2 instance in multi-AZ.
* Highly available, scalable, expensive (3x gp2), pay per use.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f404dddd-9b18-4f1c-b26e-805e266116cd)

* Use cases: content management, web serving, data sharing, ...
* Uses NFSv4.1 protocol.
* Uses security group to controll access to EFS.
* Compatible with Linux based AMI (not Windows).
* Encryption at rest using KMS.
* POSIX file system.
* Scales automatically, pay-per-use, no capacity planning.

#### EFS - Performance & Storage Classes
* EFS Scale
  * 1000s of concurrent NFS clients, 10GB+/s throughput.
  * Grow to Petabyte-scale network file system, automatically.
* Performance Mode (set as EFS creation time)
  * General Purpose (default) - latency-sensitive use cases (web server, CMS, ...)
  * Max I/O - higher latency, throughput, high parallel (big data, media proccesing)
* Throughput Mode
  * Bursting - 1TB = 50MiB/s + burst of up to 100MiB/s
  * Provisioned - set your throughput regardless of storage size.
  * Elastic - automatically scales throughput up or down based on your workloads.
    * Up to 3GiB/s for reads and 1GiB/s for writes.
    * Used for unpredictable workloads.
* Storage Tiers (lyfecycle management feature - move files after N days)
  * Standard: for frequently accessed files.
  * Infrequent access (EFS-IA): cost to retrive files, lower price to store. Enable EFS-IA with a Lifecycle Policy.
*  Availability and durability
  * Standard: Multi-AZ, great for prod.
  * One Zone: One AZ, great for dev, backup enabled by default, compatible with IA (EFS One Zone-IA).
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/3874ebb4-7374-4616-979f-828f8c663b53)

### EBS vs EFS
* EBS Volumes:
  * One instance at a time (except multi-attach io1/io2).
  * Are locked at AZ level.
  * gp2: IO increases if the disk size increases.
  * gp3 & io1:  can increase IO independently.
* To migrate an EBS volume across AZ:
  * Take a snapshot.
  * Restore the snapshot to another AZ.
  * EBS backup use IO abd you shouldn't run them while the application is handling a lot of traffic.
* Root EBS Volumes of instances get terminated by default if the EC2 instance gets terminated (can be disabled).
  ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/26a2f0b1-a34d-4011-b953-a27de81742a2)

* EFS:
  * Mounting in 100s of instances across AZ.
  * EFS shares website files.
  * Only for Linux Instances (POSIX).
  * EFS has a higher price point than EBS.
  * Can leverage EFS-IA for cost savings.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6f9af980-cf16-4c17-899f-d0059f20a87f)


 ### AMI (Amazon Machine Image)
 * AMI are customization of an EC2 instance.
   * You add your own software, configuration, OS, monitoring.
   * Faster boot / configuration time because all of the software is pre-package.
* AMI are built for a specif region (and can be copied across regions).
* Can launch EC2 instances from:
  * Public AMI: AWS provided.
  * Your own AMI: you make and maitain yourself.]
  * an AWS marketplace AMI: an AMI someone else made.

 AMI Process
  - Start an EC2 instance and customize it.
  - Stop the instance (for data integrity).
  - Build an AMI - this will also create EBS snapshots.
  - Launch instances from other AMIs.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e2311ed4-bfe2-4e43-933a-10f5b969b934)

----

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f25eed95-7132-4b9c-a7c4-6e5da1fa0732)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/42655e2a-b88e-4a60-a9d4-6a4a28390700)

------------------
------------------

## Section 4: ELB (Elastic Load Balancer) + ASG (Auto Scaling Group)

* Scalability means that an application/system can handle greater loads by adapting.
* There are 2 kinds o scalability:
  * Vertical Scalability.
  * Horizontal Scalability (= elasticity).
* Scalability is linked, but it's different to High Availability.

**Vertical Scalability**
* If your application runs in a t2.micro, scalling vertically menas runninf in a t2.large.
* Vertical scalability is very common for non distributed systems, such as databases (RDS, ElastiCache).
* The limits of how much you can scale is up to a hardware limit.

**Horizontal Scalability**
* Means increasing the number of instances/systems for your application.
* Horizontal scaling implies distributed systems.
* Common for web apps and modern apps.
* Easy to do because of Amazon EC2.

**High Availability**
* High availability menas running your app in at least 2 sata centers (AZs),
* The goal is to survive a data center loss.
* Can be passive (for RDS Multi AZ).
* Can be active (for horizontal scaling).

**Load Balance**
* Load Balances are servers that fowards traffic to multiple servers (EC2 instances, ...) downstream.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c3a9734f-964e-4a2d-b718-2d31fac6c62a)
The users MUST NOT know the difference of instances that they are connected.

Why use load balance?
* Spread load across multiple downstream instances.
* Expose a single point of access (DNS) to your application.
* Seamleslly handle failures of downstream instances.
* Do regular helath check to your instances.
* Provide SSL termination (HTTPS) for your websites.
* Enforce stickiness with cookies.
* High availability across zones.
* Separate public traffic from private traffic.

**ELB**
* Elastic Load Balancer is a managed load balancer.
  * AWS guarantees that it will be working.
  * AWS takes care of upgrades, maintenance, high availability.
  * AWS provides only a few configuration knobs.
* It cost less to setup your own load balance, but it's a lot of effort to maintain.
* It's integrated with many AWS services (EC2, EC2 ASG, CloudWatch, Route 53, WAF, ...).

 Health Checks:
 * Health Checks are crutial for Load Balancers.
 * They enable the LB to know if instances it fowards traffic to are available to reply to requests.
 * The check is done on a port and route (`/health` is common).
 * If the response is not `200`, then the instance is unhealthy, and will not send traffic to that instance.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/3e7dbd44-308a-4eed-9a94-e56dfff8475c)


Types of LB on AWS:
* Classic Load Balancer - CLB: (v1 - depricated).
* Application Load Balancer - ALB: (v2 - new generation)
  * HTTP, HTTPS, WebSocket.
* Network Load Balancer - NLB: (v2 - new generation)
  * TCP, TLS, UDP.
* Gateway Load Balancer - GWLB
  * IP Protocol.
Some load balancers can be setup as internal (private) or external (public) ELBs.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/50bfa335-f3ea-459c-8795-7ce0c11bdca0)

#### Application Load Balancer (v2)
* Application load balancers ins Layer 7 (HTTP).
* Load balancing to multiple HTTP applications across machines (target groups).
* Load balancing to multiple applications on the same machine (containers).
* Support for HTTP/2 and WebSocket.
* Support redirects (from HTTP to HTTPS example).
* Routing tables to different target groups:
  * Routing based on path in URL (ex: example.com/**users** & example.com/**posts**).
  * Routing based on hostname in URL (ex: one.example.com & other.example.com).
  * Routing based on Query String, Headers (example.com/users?**id=123&order=false**).
* ALB are a great fit for micro services & container-based application (Docker, Amazon ECS, ...).
* Has a port mapping feature to redirect to a dynamic port in ECS.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9a8bea97-3a59-4d5c-81e3-a7a3e81d6878)

ALB - Target Grouprs:
* EC2 instances (can be managed by an Auto Scaling Group) - HTTP.
* ECS tasks (managed by ECS itself) - HTTP.
* Lambda functions - HTTP request is translated into a JSON event.
* IP Addresses - must be private IPs.
* ABL can route to multiple target groups.
* Health checks are the target group level.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/52c0b41a-ab55-4ae8-b34c-83c2ccf55034)

The application servers don't see the IP of the client directly.
* The true IP of the client is inserted in the header `X-Forwarded-For`.
* We can also get the port (`X-Forwarded-Port`) and proto (`X-Forwarded-Proto`).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0e759a53-7d2e-448d-8f79-24baf9aac173)

-------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4c86fd7c-a6dc-408d-876c-5c02003c1cf3)

**When creating a APL we should change the security groups of the instances itself, to only accept traffic from the APL and not from the whole internet.**

#### Network Load Balancer (v2)
* Network load balancers (Layer 4) allow to:
  * Dowards TCP & UDP traffic to your instances.
  * Handle millions of request per seconds.
  * Less latency ~100 ms (vs 400 ms for ALB).
* NLB has one static IP per AZ, and supports assigning Elastic IP.
* NLB are used for extreme performance, TCP or UDP traffic.
* Not included in AWS free tier.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a43dc823-09f7-4a0c-bfae-ec710fd9ab27)

Target Groups:
* EC2 instances.
* IP Addresses - must be private IPs.
* Health Checks support the TCP, HTTP and HTTPS Protocols.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/54a068ac-90e6-46ff-a681-c8b669f30d7f)

-------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b3cfbf5d-de76-4be2-bc9f-baeb7acb0a59)
----

#### Gateway Load Balance
* Deploy, scale and manage a fleet of 3rd party network virtual appliances in AWS.
* Examples: Firewalls, Intrusion Detection and Prevention Systems, payload manipulation, ...
* Operates at Layer 3 (IP Packets).
* Combines the following functions:
  * Transparent Network Gateway - single entry/exit for all traffic.
  * Load Balancer - distributes traffic to your virtual appliances.
* Use the GENEVE protocol on port 6081.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/34984a83-4002-4ec4-87eb-6e35820428e5)

Target Groups:
* EC2 instances.
* IP Addresses - must be private IPs.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a4cc08a5-bbe0-4f5c-90ac-9bb51671ff5a)

-------
##### Stick Sessions (Session Afinity).
* It is possible to implement stickness so that the same client is always redirected to the same instance behind a load balancer.
* This works for ALB and NLB, also for the CLB (depricated).
* The cookie used for stickness has an expiration date you control.

This is done to make sure the user doesn't lose his session data, but it's good to keep in mind that enabling this may bring imbalance to the load over the backend EC2 instances.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8a2eb120-d516-4212-957c-f6c2855118a5)

###### Cookie Names:
* Application-based Cookies
  * Custom cookie
    * Generated by the target.
    * Can include any custom attributes required by the application.
    * Cookie name must be specified individually for each target group.
    * Don't use AWSALB, AWSALBAPP, or AWSALBTG (reserved for use by the ELB).
  * Application cookie
    * Generated by the load balancer.
    * Cookie name is AWSALBAPP.
* Duration based cookies
  * Cookie generated by the load balancer.
  * Cookie name is AWSALB for ALB, AWSELB for CLB.

---------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6db7f3c4-c7d9-489c-af66-c7a490acec31)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1ac7a088-7898-4a12-8557-85c39721b4e3)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d9ebf6c3-fd75-4c9c-9c16-e8c60cb9bb13)
---------
##### Cross-Zone Load Balancer
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/040b1c39-cbee-4d84-a8d8-d70ef3f8bba7)

* Application Load Balancer
  * Enabled by default (can be disabled at the Target Group Level).
  * No charges for inter AZ data.
* Network Load Balancer & Gateway Load Balancer
  * Disabled by default.
  * You pay charges for inter AZ data if enabled.

#### SSL/TLS
* A SSL certificate allows traffic between your clients and your load balancer to be encrypted in transit.
* SSL refers to Secure Sockets Layer, used to encrypt connections.
* TLS refers to Transport Layer Security, which is a newer version.
* Public SSL certificates are issued by Certificate Authorities (CA).
* SSL certificates have a expiration date and must be renewed.

**Load Balancer - SSL Certificates**
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/afc18141-1d45-4113-9d9a-4ce8bb74feb4)
* The load balancer uses an X.509 certificate (SSL/TLS server certificate).
* You can manage certificates using ACM (AWS Certificate Manager).
* You can create upload yur own certificates alternatively.
* HTTPS listener:
  * Must specify a default certificate.
  * You can add an optional list of certs to support multiple domains.
  * Clients can use SNI (Server Name Indication) to specify the hostname they reach.
  * Ability to specify a security policy to support older version of SSL/TLS.
 
##### Server Name Indication (SNI)
* SNI solves the problem of loading mutiple SSL certificates onto one web server (to serve multiple websites).
* It's a newer protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake.
* The server will the find the correct certificate, or return the dafault one.

**This only wokrs for ALB, NBL and ClouldFront**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/25faf464-1533-42f4-924c-8de9d4e402cd)

---------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7dd26c61-2a8b-47c8-bebe-d69ed320d0c6)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d7341a4c-fc89-4548-a7e4-5c0772e5639d)
----------
##### Connection Draining or Deregistration Delay
* Time to complete "in-flight requests" while the instance is de-registering or unhealthy.
* Stop sending new requests to the RC2 instances which is de-registering.
* Between 1 to 3600 seconds (default: 300).
* Can be disabled if set to 0.
* Set to a low value if your requests are short.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8b3e87f3-a5b7-4a14-97c1-b5c2badd993d)

---------
### Auto Scaling Group (ASG)
* In real-fife, the load on your websites and app can change.
* In the cloud, you can create and get rid of servers very quickly.

The goal of an ASG is:
* Scale out (add EC2 instances) to match an increased load.
* Scale in (remove EC2 instances) to match a decreases load.
* Ensure we have a minimum ans a maximum number of EC2 instances running.
* Automatically register new instances to a load balancer.
* Re-create an EC2 instance in case a previous one is terminated (ex. if unhealthy).

ASG are free, you only pay for the EC2 instances.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/85c71994-d58a-4f6b-b93e-12ccd0b7cdcd)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2a1ae54a-5fdb-4495-86e1-20122b24189f)

**Launch Template:**
* AMI + Instance Type
* EC2 User Data
* EBS Volumes
* Security Groups
* SSH Key Pair
* IAM Roles for your EC2 Instances
* Network + Subnets Information
* Load Balancer Information
Min size / Max size / Initial Capacity.
Scaling policies.
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/696a01e1-da22-4f1e-aaea-15d6927501ef)

Scaling:
* It's possible to scale an ASG based on CloudWatch alarms.
* An alarm monitors a metric (such as CPU, or a custom metric).
* Metrics such as Average CPU are computed for the overall ASG instances.
* Based on alarms:
  * We can create scale-out policies (increase the number of instances)
  * We can create scale-in policies (decrease the number of instances)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/19734d67-7481-46c4-a230-120bc17e251d)
--------
