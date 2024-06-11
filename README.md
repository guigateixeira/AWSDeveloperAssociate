# AWS Developer Associate

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
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0f2fd7bb-0f32-48ac-81ce-42f052fe0619)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/03f1f196-4aa7-4220-a894-19c22517515d)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/cedea1b3-479a-4240-89b6-169fd1c304cc)
--------
#### Scaling Policies
* Dynamic Scaling
  * Target Tracking Scaling
    * Simple to set-up.
    * Ex: want the average ASG CPU to stay around 40%.
  * Simple / Step Scaling
    * When a CloudWatch alarm is triggered (ex. CPU > 70%), then add 2 units.
    * When a CloudWatch alarm is triggered (ex. CPU < 30%), then remove 1 unit.
* Schedule Scaling
  * Anticipate a scaling based on known usage patterns.
  * Ex: increse the min capacity to 10 at 5pm on Fridays.
* Predictive Scaling: continuiusly forecast load and schedule scaling ahead.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/af539482-da6f-495e-b02c-067a2d212fd5)

Best metrics to scale on:
* CPU Utilization.
* Request Count Per Target.
* Average Network In/Out.
* Custom metrics from CloudWatch.
 
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5481c39c-2e80-4ca5-aee3-3551197027b8)


Scaling Cooldowns:
* After a scaling activity happens, you are in the cooldown period (default 300s).
* During the cooldown period, the ASG will not launch or terminate additional instance (to allow the metrics to stabilize).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2838cf1a-9f31-47e7-ac81-ef5660455960)

Use a ready-to-use AMI to reduce configuration time in order to be serving requests faster and reduce the cooldown period.

-------
<img width="1700" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/19bb35a8-91f9-4918-aa8f-01867bf3b087">
<img width="855" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/077050f5-b743-4508-bb33-d54349b2a81b">

----

<img width="1231" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fa208737-9244-4cf8-8d89-f90d11429543">

----
<img width="1308" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2fa3ebc1-1e45-473b-8695-f6a387497f84">
<img width="1098" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/53af6413-2aca-467d-9ab1-65b26450af60">

-------
#### Auto Scaling - Instance Refresh
* Update launch template and then re-launch EC2 instances.
* Use the native Instance Refresh.
* Setting minimum healthy percentage.
* Specify warm-up time (how long until the instances are ready to use).

<img width="746" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/64b728c3-7a80-4c08-a0a7-960f94a48912">

-----
## Section 5: AWS Fundamentals - RDS + Aurora + ElastCache
### RDS Overview
* RDS stands for Relational Database Service.
* It's a managed DB service for DB use SQL as a query language.
* It allows you to create DBs in the cloud that are managed by AWS (Postgres, MySQL, Oracle, ...)

RDS is a managed service:
* Automated provisioning, OS patching.
* Countinuos backup and restore to specific timestamp (Point in Time Restore).
* Monitoring dashboards.
* Read replicas for improved read performance.
* Multi AZ setup for disaster recovery.
* Maintance windows for upgrades.
* Scaling capability (vertical and horizontal).
* Storage backed bt EBS (gp2 or io1).
**Can't access via SSH.**

#### RDS - Storage Auto Scaling
* Helps you increase storage om your RDS DB instance dynamically.
* When RDS detects you are running out of free database storage, it scales automatically.
* Avoid manually scaling your database storage.
* You have to set Maximum Storage Threshod (maximum limit for DB storage).
* Automatically modify storage if:
  * Free storage is less than 10% of allocated storage.
  * Low-storage lasts at least 5 minutes.
  * 6 hours have passed since last modification.
* Useful for applications with unpredictable workloads.
* Supports all RDS databases engines.

 ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d484c20e-7146-4859-b9b1-dba770f11330)

#### RDS Read Replicas for read scalability
* Up to 15 Read Replicas.
* Within AZ, Cross AZ or Cross Region.
* Replication is ASYNC, so reads are eventually consistent (you can be reading old data).
* Replicas can be promoted to their own DB.
* Applications must update the connection string to leverage read replicas.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e09c232a-0157-430f-9e5b-024ef1264f97)

**Example use case**
* You have a production DB that is taking normal load.
* You want to run a reporting application to run some analytics.
* You create a Read Replica to run the new workload there.
* The production application is unaffected.
* Read replicas are only used for SELECT statements.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c8bff1c7-111a-4b28-99a7-1d33912cb1c1)

##### RDS Read Replicas - Network Cost
* In AWS there's a network cost when data goes from one AZ to another.
* **For RDS Read Replicas within the same region, you don't pay taht fee.**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/85259561-b55c-4e63-a1b4-d6b87bc1ee30)

##### RDS Multi AZ (Disaster Recovery)
* SYNC Replication.
* One DNS name - automatic app failover to standby.
* Increase availability.
* Failover in case of loss of AZ, loss of network, instance or storage failure.
* No manual intervention in apps.
* Not used for scaling.
* Note: The Read Replicas be setup as Multi AZ for Disaster Recovery (DR).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f336b84f-9c34-4c50-b0d3-5e4054eaf5d9)

#### RDS - From Single-AZ to Multi-AZ
* Zero downtime operation (no need to stop the DB).
* Just click in "modify" for the database.
* The following happens internally:
  * A snapshot is taken.
  * A new DB is restored from the snapshot in a new AZ.
  * Synchronization is established between the two databases.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/81c76da2-4c14-47db-9813-d49e62ba6478)

-------

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7985353f-a2ab-408a-a461-19a518c4b49f)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4e025806-31c7-4521-9bc4-5cd499cb7f14)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b1c8fe43-fa97-47e6-ac46-b36f2482d4d3)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1513ebb4-3639-4626-b011-8aad412e3021)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/bf5c6306-6d46-4817-9eac-8805a6085d6d)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a11291bc-78c8-4673-8614-b695c3617643)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7bf85bab-b30d-46d9-a0b3-4610e3594dff)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/db822d4b-2707-4342-bce6-bda0439bc495)

-----------
### Amazon Aurora
* Aurora is a proprietary tech from AWS (not open source).
* Postgres and MySQL are both supported as Aurora DB (that means your drivers will wokr as if Aurora was a Postgres or MySQL DB).
* Aurora is "AWS cloud optimized" and claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS.
* Aurora storage automatically grows in increments of 10GB, up to 128TB.
* Aurora can have up to 15 replicas and the replication process is faster than MySQL (sub 10ms replica lag).
* Failover in Aurora is instantaneous. It's High Availability native.
* Aurora costs more than RDS (20% more) - but it's more efficient.

#### Aurora - High Availability and Read Scaling
* 6 copies of your data across 3 AZ:
  * 4 copies out of 6 needed for writes.
  * 3 copies out of 6 needed for reads.
  * Self healing with peer-to-peer replication.
  * Storage is striped across 100s of volumes.
* One Aurora Instance takes writes (master).
* Automated failover for master in less than 30 seconds.
* Master + up to 15 Aurora Read Replicas serve reads.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4f8aae52-b7a3-4ee1-818d-5c7cff8e0659)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e0dd5c7a-99f1-434f-9915-47c5a9864b6b)

**Features of Aurora**
* Automatic fail-over.
* Backup and Recovery.
* Isolation and security.
* Industry compliance.
* Push-button scaling.
* Automated Patching with Zero Downtime.
* Advanced Monitoring.
* Routine Maintenance.
* Backtrack: restore data at any point of time without using backups.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/556a53ac-904e-4b0b-ad7e-c331c40bf2db)

### RDS & Aurora Security
* At-rest encryption:
  * Database master & replicas encryption using AWS KMS - must be defined as launch time.
  * If the master is not encrypted, the read replicas cannot be encrypted.
  * To encrypt and un-encrypt databse, go through a DB snapshot & restore as encrypted.
* In-flight encryption: TLS-ready by default, use the AWS TLS root certificates client-side.
* IAM Authenication: IAM roles to connect to your database (intead of username/pw).
* Security Groups: Controll Netwrok access to your RDS/Aurora DB.
* No SSH available except on RDS Custom.
* Audit Logs can be enabled and sent to CloudWatch Logs for longer retention.

### RDS Proxy
* Fully managed database proxy for RDS.
* Allows apps to pool and share DB connections established with the database.
* Improving database efficiency by reducing the stress on database resources (e.g., CPU, RAM) and minimize open connections (and timeouts).
* Serveless, autoscaling, highly available (multi-AZ).
* Reduce RDS & Aurora failover time by up 66%.
* Supports RDS (MySQL, Postgres, MariaDB, MS SQL Server) and Aurora.
* No code change required for most apps.
* Enforce IAM Authentication for DB, and securely store credentials in AWS Secret Manager.
* RDS Proxy is never publicly accesible (must be accessed from VPC).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8408b6af-5d50-4bb6-9e4f-031a19b65369)

### ElastiCache
* The same way RDS is to get managed Relational Databases
* ElastiCache is to get managed by Redis or Memcached.
* Caches are in-memory databases with really high performance, low latency.
* Help reduce load off of databases for read intensive workloads.
* Helps make your application stateless.
* AWS takes care of OS maintance/patching, optimizations, setup, configuration, monitoring, failure recovery and backups.
* **Using ElastiCache involves heavy application code changes.**

##### DB Cache - Architecture
* Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
* Helps relieve load in RDS.
* Cache must have an invalidation strategy to make sure only the most current data is used in there.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/12a2f778-fd99-43f1-8292-a32f605d7273)

##### User Session Store - Architecture
* User logs into any of the application.
* The application writes the session data into ElastiCache.
* The user hit another instance of our application.
* The instance retrieves the data and the user is already logged in.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f47ab57d-0594-4af0-8562-24597aa20975)

#### Redis vs Memcached
* **Redis:**
  * Multi AZ with Auto-Failover.
  * Read Replicas to scale reads and have high availability.
  * Data Durability using AOF persistence.
  * Backup and restore features.
  * Supports Sets and Sorted Sets.
  * ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/76a9f094-8a79-4b36-b907-e8995720871c)
* **Memcached:**
  * Multi-node for partitioning of data (sharding).
  * No high availability (replication).
  * Non persistent.
  * No backup and restore.
  * Multi-threaded architecture.
  * ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/60ee8f8c-3606-404c-85d8-9495cdb2bbca)
 
---------

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/adc4767f-7ad4-4a29-a07f-b66f1b718473)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c0ae2fc5-ddf4-47e4-a4dd-f476f9f035ce)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/10dcc78a-227b-4716-8652-8c5ad8503080)

-------
### Cache Implementations:
* Data in cache may be out of date, eventually is consistent.
* Caching is efective for data changing slowly, few keys are frequently needed.
* Data structured for cache, for example a key value caching, or caching of aggregations results.

#### Lazy Loading:
* Pros:
  * Only requested data is cached (the cache isn't filled up with unused data).
  * Node failures are not fatal (just increase latency to warm the cache).
* Cons:
  * Cache miss penalty that results in 3 round trips, noticeable delay for that request.
  * Stale data: data can be updated in the database and outdated in cache.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6c466b22-dd5e-4119-a7aa-60f3534f1fde)

**Example code for Lazy Loading:**
```
async getUser(userId: string): Promise<User> {
  let user = await cache.get(userId);

  if(user === null || user === undefined) {
    user = await db.query("SELECT * FROM users WHERE id = ?", userId);
    if(user === null || user === undefined) {
      return null;
    }
    await cache.set(userId, user);
  }
  return user;
}
```

#### Write Through
* Pros:
  * Data in cache is never stale, reads are quick.
  * Write penalty vs Read penalty (each write require 2 calls).
* Cons:
  * Missing data until it is added/updated in the DB. Mitigation is to implement Lazy Loading strategy as well.
  * Cache churn - a lot of the data will never be read.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9bdcca47-c7ff-4599-bd89-28bb327861e7)

**Example code for Write Through:**
```
async saveUser(userRequest: UserRequest): Promise<User> {
  let user = await db.query("INSERT INTO users, ....", userRequest);
  await cache.set(user.id, user);
  return user;
}
```

#### Cache Eviction and Time-to-live (TTL)
* Cache eviction can occur in 3 ways:
  * You delete the item explicitly in the cache.
  * Items is evicted because the memory is full and it's not recently used (LRU).
  * You set an item time-to-live (TTL),
* TTL are helpful for any kind of data:
  * Leaderboards.
  * Comments.
  * Activity Streams.
* TTL can reange from fwew seconds to days.
* If too many evictions happen due to memory, you should scale up or out.

### Amazon MemoryDb for Redis:
* Redis-compatible, durable, in-memory database service.
* Ultra-fast performance with over 160 millions request/second.
* Durable in-memory data storage with Multi-AZ transactional log.
* Scale seamlessly from 10s GBs to 100s TBs of storage.
* Can be used in web and mobile apps, online gaming, ...

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f3f8edd5-2e30-4e4a-8431-98ec38a5c044)

---------

## Section 6: Route 53
### DNS
* Domain Name System (DNS) which transalates the humain friendly hostnames into the machine IP addresses (www.google.com -> 108.207.17.36).
* DNS uses hierarchical naming structure.
* Domain Registrar: Amazon Route 53, ...
* DNS Records: A, AAAA, CNAME, ...
* Zone File: contains DNS records.
* Name Server: resoplves DNS queries (Authoritative or Non-Authoritative).
* Top Level Domain (TLD): .com, .in, .gov, .org, ...
* Second Level Domain (SLD): amazon.com, ...

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/78217cbc-32fe-49bd-b34a-da5b0db5c054)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6b2e28cd-59be-4b6c-8652-3e3da3637583)

### Amazon Route 53
* Highly available, scalable, fully managed and Authoritative (the customer can update the DNS records) DNS.
* Route 53 is also a Domain Registrar.
* Ability to check teh health of your resources.
* The only AWS service which provides 100% availability SLA.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/34373225-21cb-4edb-acf1-cb4c2b085e1d)

#### Records
* How you want to route traffic for a domain.
* Each record contains:
  * Domain/subdomain Name - ex. example.com
  * Record Type: ex. A or AAAA
  * Value: ex. 127.213.12.1
  * Routing Policy: how Route 53 responds to queries.
  * TTL: amount of time the record cached at DNS Resolvers.
* Route 53 suppports the following DNS types:
  * A / AAAA / CNAME / NS
  * CAA / DS / MX / NAPTR / PTR / ... (advanced)

**Record Types:**
 * A - maps a hostname to IPv4
 * AAAA - maps a hostname to IPv6
 * CNAME - maps a hostname to another hostname
   * The target is a domain name which must have an A or AAAA recors.
   * Can't create a CNAME record for the top node of a DNS namespace (Zone Apex).
   * Example: you can't create for example.com, but you can create for www.example.com
* NS - Name Servers for the Hosted Zone
  * Controll how traffic is roputed for a domain.
 
**Hosted Zones**
* A container for records that define how to route traffic to a domain and it's subdomains.
* Public Hosted Zones - contains records that specify how to route traffic on the internet (public domain names).
* Private Hosted Zones - contains records that specify how you route traffic within one or more VPCs (private domain names).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b457c3ff-f9a0-46f9-9920-ccd48e568fed)
------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1aac525e-9385-4cdc-b957-f9b056dd2f36)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e06147ae-0ac1-4dbc-954a-f257d06a4627)

#### Records TTL
* High TTL - ex. 24hrs
  * Less trafic on Route 53.
  * Possibily outdated records.
* Low TTL - ex. 60sec
  * More traffic on Route 53 ($$$).
  * Records are outdated for less time.
  * Easy to change records.
* Except for Alias records, TTL is mandatory for each DNS record. 

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e6375c04-9e4d-41d7-a11b-5ebb2a027788)

#### CNAME vs Alias
* AWS Resources (Load Balancer, CloudFront ...) expose an AWS hostname:
  * 1b1-123.us-east-1.elb.amazonaws.com and you want myapp.mydomain.com

* CNAME:
  * Points a hostname to any other hostname. (app.mydomain.com => anything.something.com).
  * ONLY FOR NON ROOT DOMAIN (aka something.mydomain.com).
* Alias:
  * Points a hostname to an AWS Resource (app.mydomain.com => anything.amazonaws.com).
  * WORKS for ROOT DOMAIN and NON ROOT DOMAIN (aka mydomain.com).
  * Free of charge.
  * Native health check.

##### Alias Records
* Maps a hostname to an AWS resource.
* An extension to DNS functionality.
* Automatically recognizes changes in the resource's IP addresses.
* Unlike CNAME, it can be used for top node of a DNS namespace.
* Alias Record is always type A/AAAA for AWS resources (IPv4 / IPv6).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/14eb8920-d3d1-4eb5-b077-38a25de675f2)

**Aliad Records Targets:**
* Elastic Load Balancers.
* CloudFront Distribution.
* API Gateway.
* Elastic Beanstalk environments.
* S3 Websites.
* VPC Interfaces Endpoints.
* Global Accelerator.
* Route 53 records in the same hosted zone.
**YOU CANNOT SET AN ALIAS RECORS FOR AN EC2 DNS NAME**

#### Routing Policies
* Define how Route 53 responds to DNS queries.
* It's not the same as Load balancer routing which routes the traffic.
  * DNS does not route any traffic, it only responds to the DNS queries.
* Route 53 supports the following Routing Policies:
  * Simple
  * Weighted
  * Failover
  * Latency based
  * Geolocation
  * Multi-Value Answer
  * Geoproximity

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ffc0d18d-5f51-4fea-a997-d87d24bca629)

##### Routing Policies - Simple:
* Typically, route traffic to a single resource.
* Can specify multiple values in the same record.
* If multiple values are returned, a random one is chosen by the client.
* When Alias enabled, specify only one AWS resource.
* Can't be associated with Health Checks.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5d00b514-0666-47c2-b3ad-59d20ca4ab86)

----
##### Routing Policies - Weighted:
* Controll the % of the requests that go to each specific resource.
* Assign each record a relative weight:
  * traffic(%) = (weight for specif record) / (Sum of all the weights for all records)
  * Weights don't need to sum up to 100.
* DNS records must have the same name and type.
* Can be associated with Health Checks.
* Use cases: load balancing between regions, testing new application versions, ...
* Assign a weight of 0 to a record to stop sending traffic to a resource.
* If all records have weight of 0, then all records will be returned equally.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/41e52897-9c1f-4d4c-9442-77d0f464f941)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1e5408e1-d550-4670-bcca-ddf486d95f2b)

----
##### Routing Policies - Latency:
* Redirects to the resource that has the least latency close to us.
* Super helpful when latency for users is a priority.
* Latency is based on traffic between users and AWS Regions.
* UK users may be redirected to the US (if that's the lowest latency).
* Can be associated with Health Checks (has a failover capability).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2b0be82e-7fc2-4cdd-bcd4-4c67ec3135df)

----
##### Routing Policies - Health Checks:
* HTTP health Checks are only for public resources.
* Health Check => Automated DNS Failover.
  1. Health checks that monitor an endpoint (application, server, other AWS resource).
  2. Health checks that monitor other health checks (Calculated Health Checks).
  3. Health checks tha monitor CloudWatch Alarms (full control)
* Health checks are integrated with CW metrics.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b2a12944-aca8-4211-9623-49244f778b04)

###### Health Checks - Monitor an Endpoint:
* About 15 global health checkers will check the endpoint health.
  * Healthy/Unhealthy Threashold - 3 (default).
  * Interval - 30 sec (can set to 10 sec - higher cost).
  * Supported protocol: HTTP, HTTPS and TCP.
  * If > 18% of health checkers report the endpoint is healthy, Route 53 considers it Healthy. Otherwise, it's Unhealthy
  * Ability to choose which locations you want Route 53 to use.
* Health Checks pass only when the endpoint responds with the 2xx and 3xx status codes.
* Health Checks can be setup to pass/fail based on the text in the first 5120 bytes of the response.
* Configure you router/firewall to allow incoming requests from Route 53 Health Checkers.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/90cd4fe7-4240-432d-9445-18c0d4b1f527)

###### Health Checks - Calculated Health Checks:
* Combine the results of multiple Health Checks into a single Health Check.
* You can use OR, AND, or NOT.
* Can monitor up to 256 Child Health Checks.
* Specify how many of the health checks need to pass to make the parent pass.
* Usage: perform maintanance to your website without causing all health checks to fail.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9f7b68bc-55b6-4754-9cdb-308cb45f9709)

###### Health Checks - Private Hosted Zones:
* Route 53 health checkers are outside the VPC.
* They can't access private endpoints (private VPC or on-premises resources).
* You can create a CloudWatch metric and associate a CloudWatch Alarm, then create a Health Check that checks the alarm itself.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5c9bfa1e-f823-4b44-a285-14ee63a2a2a0)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b5ffb807-5cb9-47b6-a5be-6370b297a6c2)

--------
##### Routing Policies - Failover:
 * Choose a primary instance, when the clinet makes a request it will return this first instance.
 * Choose a failover instance, and if the primary fails heakth check, the DNS response will be this second (failover) instance.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/242cb176-eff8-49e8-bd8b-950033d2a493)

----
##### Routing Policies - Geolocation:
* Different from Latency-based.
* This routing is based on user location.
* Specify location by Continent, Country, or by US State (if there's overlapping, most precise location is selected).
* Should create a Default record (in case there's no match on location).
* Use cases: website location, restrict content distribution, load balancing.
* Can be associated with Health Checks.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/347ee929-5b3b-49a6-b38b-9504845a3eaa)

----
##### Routing Policies - Geoproximity:
* Route traffic to your resources based on the geographic location of users and resources.
* Ability to shift more traffic to resources based on the defined bias.
* To cahnge the size of the geographic regions, specify bias values:
  * To expand (1 to 99) - more traffic to the resource.
  * To shrink (-1 to -99) - less traffic to the resource.
* Resources can be:
  * AWS resources (specify AWS region).
  * Non-AWS resources (specify Lagitude and Longitude).
* You must use Route 53 Traffic Flow (advanced) to use this feature.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6e07a624-fed2-4446-a97f-1e6597083d62)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5bd1abcd-be28-44d4-8af9-6fa0257f176b)

----
##### Routing Policies - Traffic flow:
* Simplify the process of creating and maintaining records in large and complex configurations.
* Visual editor to manage complex routing decision trees.
* Configuraions can be saved as Traffic Flow Policy
  * Can be applied to different Route 53 Hosted Zones (different domain names).
  * Supports versioning.
 
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2935a811-71e2-4782-8fa3-afd61e789c56)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/51c37272-e2fc-4075-88de-c464027bd5e3)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/657020b8-132c-4b5a-ba5c-dca46a023ab7)

**Adding bias to regions:**
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ed08b6e9-56b8-48ca-8c38-058d301be640)

----
##### Routing Policies - IP-based:
* Routing is based on clinets IP addresses.
* You provide a list of CIDRs for your clients and the coprresponding endpoints/locations (user-IP-to-endpoint mappings).
* Use cases: Optimize performance, reduce network costs.
* Ex: route end users from a particular ISP to a specific endpoint.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/258491dc-587b-474a-ac4a-46a4271b62f9)

----
##### Routing Policies - Multi-Value:
* Use when routing traffic to multiple resources.
* Route 53 return multiple values/resources.
* Can be associated with Health Checks (return only values for healthy resources).
* Up to 8 healthy records are returned for eac Multi-Value query.
* Multi-Value is not a subistitute for having an ELB.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0400397f-2718-4508-9e0c-5ef72f2a4f56)

-----
* If you buy your domain on a 3rd party registrar, you can still use Route 53 as DNS service provider.


-----
## Section 7: VPC
* VPC: private network to deploy your resources (reginal resource).
* Subnets allow you to partition your network inside your VPC (Availability Zone resource).
* A public subnet is a subnet that is accessible from the internet.
* A private subnet is a subnet that is not accessible from the internet.
* To define access to the internet and between subnets, we use Route Tables.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/af88023e-27bb-42d7-9d25-66175bf1e56d)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7c97bdb3-dfff-4b9e-acae-f2a0c4455b19)

### Internet Gateway & NAT Gateways
* Internet Gateways helps our VPC instances connect with the internet.
* Public Subnets have a route to the internet gateway.
* NAT Gateways (AWS-managed) & NAT Inatnces (self-managed) allow your instances in your Private Subnets to access the internet while remaining private.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c0a64c93-930b-430e-a233-6844c3ac9286)

### Network ACL & Security Groups
* NACL (Network ACL)
  * A firewall which controls traffic from and to subnet.
  * Can have ALLOW and DENY rules.
  * Are attached at the Subnet level.
  * Rules only include IP addresses.
* Security Groups
  * A firewall that controll traffic to and from an ENI / an EC2 instance.
  * Can have only ALLOW rules.
  * Rules include IP addresses and other security groups.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f7442f35-6b69-41e5-abfe-258990bdae98)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d409e521-8c72-42af-b46f-a795a4d0f160)

### VPC Flow Logs
* Capture information about IP traffic going into your interfaces:
  * VPC Flow Logs.
  * Subnet Flow Logs.
  * Elastic Network Interface Flow Logs.
* Helps to monitor & troubleshoot connectivity issues. Ex:
  * Subnets to internet.
  * Subnets to subnets.
  * Internet to subnets.
* Captures network information from AWS managed interfaces too: Elastic Load Balancers, ElastiCache, RDS, Aurora.
* VPC Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose.

### VPC Peering
* Connect 2 VPC, privately using AWS' network.
* Make them behave as if they were in the same network.
* Must not have overlapping CIDR (IP address range).
* VPC Peering connection is not transitive (must be established for each VOC that need to communicate with one another).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5a2d0483-606b-4a67-984a-dba36152df75)

### VPC Endpoints
* Endpoints allow you to connect to AWS Services using a private network instead of the public www network.
* This gives you enhanced security and lower latency to access AWS services.
* VPC Endpoint Gateway: S3 & DynamoDB.
* VPC Endpoint Interface: the rest.
* Only used within your VPC.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9b2e2060-057f-440d-99d9-85c273edeea1)

**Site to Site VPN & DIrect Connection**
* Site to Site VPN
  * Connect an on-premises VPN to AWS.
  * The connection is automatically encrypted.
  * Goes over the public internet.
* Direct Connect (DX)
  * Establish a physical connection between on-premises and AWS.
  * The connection is private, secure and fast.
  * Goes over a private network.
  * Takes at least a month to establish.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fd073daa-aa3e-4863-9198-585d6accab17)

### VPC Overview:
* VPC: Virtual Private Cloud.
* Subnets: Tied to an AZ, network partition of the VPC.
* Internet Gateway: at the VPC level, provide Internet Access.
* NAT Gateway / Instances: give internet access to private subnets.
* NACL: Stateless, subnet rules for inbound and outbound.
* Secuyrity Groupd: Stateful, operate at the EC2 instance level or ENI.
* VPC Peering: Connect 2 VPC with non overlapping IP ranges, non transitive.
* VPC Endpoints: Provide private access to AWS Services within VPC.
* VPC FLow Logs: network traffic logs.
* Site to SIte VPN: VPN over public internet between on-premises DC and AWS.
* Direct Connect: direct private connection to a AWS.

### Three Tier Architecture
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/09cba0a8-2cf9-4e41-a369-a8da877082b5)

-----
## Section 8: S3
### S3 - Use Cases
* Backup and storage.
* Disaster Recovey.
* Archive.
* Hybrate Cloud Storage.
* Application hosting.
* Media hosting.
* Data lakes & Big Data analysis.
* Software delivery.
* Static website.

### S3 - Buckets
* Amazon S3 allows people to store objects (files) in "buckets" (directories).
* Buckets must have a globally unique name (across all regions all accounts).
* Buckets are defined at the region level.
* S3 looks like a global service but buckets are created in a region.
* Naming convention
  * No uppercasing, No underscore.
  * 3-63 characters long.
  * Not an IP.
  * Must start with lowercase letter or number.
  * Must NOT start with the prefix `xn--`.
  * Must NOT end with the suffix `-s3alias`.
 
### S3 - Objects
* Object (files) have a Key.
* The ${\color{blue}key}$ is the FULL path
  * s3://my-bucket/ ${\color{blue}file.txt}$
  * s3://my-bucket/ ${\color{blue}folder1/another_folder/my_file.txt}$
* The key is composed of ${\color{green}prefix}$ + ${\color{yellow}object name}$
  * s3://my-bucket/ ${\color{green}folder1/folder2/}$ ${\color{yellow}file.txt}$
* There's no concept of "directories" within buckets.
* Just keys very long names that contain slashes ("/").
* Object values are the content of teh body:
  * Max. Object Size is 5TB.
  * If uploading more than 5GB, must use "multi-part upload".
* Metadata (list of the text key / value pairs - system or user metadata).
* Tags (Unicode key / value pair - up to 10) - useful for security/lifecycle.
* Version ID (if versioning is enabled).

-------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0870d2bb-873c-4c20-b4b4-cea6671fff4f)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/70b85cfd-9845-4065-a209-29d784be07f5)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a80a2f3b-2387-4659-894b-7ee599d36a3d)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d9d94f61-9d0e-4602-863f-eafe7baba537)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/84bbcfd6-99ab-42fd-92ec-aa221500d338)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fcee4b6e-61d3-4b6f-84b8-3c3976ac7046)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/66d0f070-0892-4048-8317-08c5084bf5c5)

-------
### S3 - Security
* User-Based
  * IAM Policies - which API calls should be allowed for a specific user from IAM.
* Resource-Based
  * Buckets Policies - buckets wide rule from the S3 console - allows cross account.
  * Object Access Control List (ACL) - finer grain (can be disabled).
  * Bucket Access Control List (ACL) - less common (can be disabled).
* Note: an IAM principal can access an S3 object if:
  * The user IAM permissions ALLOW it OR the resource policy ALLOWS it.
  * AND there's no explicit DENY.
* Encryption: encrypt objects in Amazon S3 using encryption keys.

**S3 Bucket Policies**
* JSON based policies
  * Resources: bucket and objects.
  * Effect: Allow/Deny
  * Actions: Set of API to Allow or Deny.
  * Principal: The account or the user to apply the policy to.
* Use S3 bucket for policy to:
  * Grant public access to the bucket.
  * Force objects to be encrypted at upload.
  * Grant access to another account (Cross Account).

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicListObjects",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::YourBucketName"
        }
    ]
}

```

- Public Access - Use Bucket Policy

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e310d3bf-8cbf-4a71-beca-bcd0c5f93d78)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/728a62b1-e6ee-4474-864c-45f3a22b0271)

- User Access to S3 - IAM permissions

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4b20fc61-1dd5-47b8-a402-9efd60e325cc)

- EC2 instance access - Use IAM Roles

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/148d00aa-41e9-40cd-b4f9-c8d78e4964c1)

--------
### S3 - Static Website Hosting
* S3 can host static websites and have them accessible on the Internet.
* The website URL will be (depending on the region).
  * `http://bucket-name.s3-website-aws-region.amazonaws.com`
   OR
  *  `http://bucket-name.s3-website.aws-region.amazonaws.com`
* If you get a 403 Forbidden error, make sure the bucket policy allows public reads!

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/cc13fbf6-149c-447d-b853-a0727a1a8641)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/562331af-fb5c-43d1-bee1-cafefa42f217)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b56c6b9c-0d82-4ed8-921e-d8e53d402046)

------------
### S3 - Versioning
* You can version your files Amazon S3.
* It is enabled at the buckets level.
* Same key overwrite will change the "version": 1, 2, 3 ...
* It is best practice to version your buckets
  * Protect against unintended deleted (ability to restore a version).
  * Easy roll back to previous version.
* Notes:
  * Any file that is not versioned prior to enabling versioning will have version "null".
  * Suspending versioning does not delete the previous versions.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ac8a0456-ec71-4863-8f29-b592970a65ba)


![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d3499b21-8729-449a-a9f1-e2df8f63eba6)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/44261d2e-3036-43f3-8af4-5f9b32d90ca4)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a5f3e39d-691d-462b-896d-870271bd9513)

The ones that have a versionId === null are the ones that were uploaded before versioning was enabled in the bucket.

------------
### S3 - Replication
* Must enable Versioning in source and destination buckets.
* Cross-Region Replication (CRR).
* Same-Region Replication (SRR).
* Buckets can be in different AWS accounts.
* Copying is asynchronous.
* Must give proper IAM permissions to S3.
* Use cases:
  * CRR - compliance, lower latency access, replication across accounts.
  * SRR - log aggregation, live replication between production and test accounts.

* Agter you enable Replication, only new objects are replicated.
* Optionally, you can replicate existing objects using S3 Batch Replication.
  * Replicates existing objects and objects that failed replication.
* For DELETE operations
  * Can replicate delete markers from source to target (optional setting).
  * Deletions with a version ID are not replicated (to avoid malicious deletes).
* There is no "chaining" of replication
  * If bucket 1 has replication into bucket 2, which has replication into bucket 3.
  * Then objects created in bucket 1 are not replicated to bucket 3.

------------
### S3 - Storage Classes
* Amazon S3 Standard - General Purpose.
* Amazon S3 Standard - Infrequent Access (IA).
* Amazon S3 One-Zone - Infrequent Access.
* Amazon S3 Glacier Instant Retrieval.
* Amazon S3 Glacier Deep Archive.
* Amazon S3 Intelligent Tiering.

#### S3 Durability and Availability
* Durability:
  * High Durability (99.99999999999%) of object across multiple AZ.
  * Same for all storage classes.
* Availability
  * Mesures how readily available a service is.
  * Varies depending on storage class.
 
#### S3 Standard - General Purpose
* 99,99% Availability.
* Used for frequently accessed data.
* Low latency and high throughput.
* Sustain 2 concurrent facility failures.
* Use Cases: Big Data analitics, mobile & gaming apps, ...

#### S3 Infrequent Access
* For data that is less frequently accessed, but requires rapid access when needed.
* Lower cost than S3 Standard.
* Amazon S3 Standard-Infrequent Access (S3 Standard-IA)
  * 99,9% Availability.
  * Use cases: Disaster recovery, backups.
* Amazon S3 One Zone-IA
  * High durability (99,9999999999%) in a single AZ; data lost when Az is destroyed.
  * 99,5% Availability
  * Use cases: storing secondary backup copies of on-premises data.
 
#### S3 Glacier
* Low-cost storage meant for archioving/backup
* Pricing: price for storage + object retrival cost.
* Amazon S3 Glacier Instant Retrieval
  * Millisecond retrival, great for data accessed once a quarter.
  * Minimum storage duration of 90 days.
* Amazon S3 Glacier Flexible Retrival
  * Expedited (1 to 5 min), Standard (3 to 5 hours)m Bulk (5 to 12 hours) - free.
  * Minimum storage duration of 90 days.
* Amazon S3 Glacier Deep Archive - for long term storage:
  * Standard (12 hours), Bulk (48 hours).
  * Minimum storage duration of 180 days.

#### S3 Intelligent Tiering
* Small monthly monitoring and auto-tiering fee.
* Moves objects automatically between Access Tiears based on usage.
* There are no retrieval charges in S3 Intelligent-Tiering.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/91f5f4fe-75cb-4e12-b672-539f985882e8)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8195163c-5ebf-4fc8-896a-c070618037ce)


--------------
## Section 9 - AWS CLI, SDK, IAM Roles & Policies
### EC2 Intances Metadata (IMDS)
* AWS EC2 Instances Metadata (IMDS) is powerful but one of the least known features.
* It allows ASW EC2 instances to "learn about themselves" without using an IAM Role for that purpose.
* You can retrieve the IAM Role name from the metadata, but you cannot retrieve the IAM Policy.
* Metadata = INfo about the EC2 instance.
* Userdata = launch script of the EC2 instance.

There is V1 and V2.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/852a69cc-7c1e-40e0-9781-e2a9f0bb7b37)

### MFA with CLI
* To use MFA with the CLI, you must create a temporary session.
* To do so, you must run the STS GetSessionToken API call.
* aws sts get-session-token --serial-number arn-of-the-mfa-device --token-code code-from-token --duration-seconds 3600

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/15c26181-9b71-4fb4-9176-33f7466e76ea)

### AWS SDK
* We use AWS SDK when coding against AWS Services.
* If you dont't specify or configure a default region, the us-east-1 will be chosen by default.

### AWS Limits (Quotas)
* API Rate Limits
  * DescribeIntances API for EC2 has a limit of 100 calls per seconds.
  * GetObject on S3 has a limit of 5500 GET per second per prefix.
  * For Intermittent Errors: implement Exponential Backoff.
  * For Consistent Errors: request an API throttling limit increase.
* Service Quotas (Service Limits)
  * Running On-Demand Standard Instances: 1152 vCPU.
  * You can request a service limit increase by opening a ticket.
  * You can request a service quota increase by using the Service Quotas API.

#### Exponencial Backoff (any AWS service)
* If you get ThrottlingException intermittently, use exponencial backoff.
* Retry mecainsm already included in AWS SDK API calls.
* Must implement yourself using the AWS API as-is or in specific cases.
  * Must only implement the retries onm 5xx server errors and throttling.
  * Do not implement in the 4xx client errors.
 
### AWS CLI Credential Provider Chain
* The CLI will look for credentials in this order:
  1. Command line options: --region, --output, and --profile.
  2. Env variables: AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_SESSION_TOKEN.
  3. CLI credentials file: aws configure.
  4. CLI configuration file: aws configure.
  5. Container credentials: for ECS tasks.
  6. Intances profile credential: for EC2 Intances Profiles.

* The SDK will look for credentials in this order:
  1. Java system properties: aws.accessKeyId and aws.secretKey.
  2. Env variables:  AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.
  3. The default credentials profile file, shared by many SDKs.
  4. Amazon ECS container credentials: for ECS containers.
  5. Intance profile credentials: used on EC2 instances.
 
**Best Practices**
* Overall, NEVER STORE AWS CREDENTIALS IN YOUR CODE.
* Best practice is for credentials to be inherited from the credenmtials chain.
* If using working within AWS use IAM roles:
  * EC2 Instances roles for EC2 intances.
  * ECS Roels for ECS Tasks.
  * Lambda Roles for Lambda functions.
* If working outside of AWS, use env variables / named profiles.

### AWS Signatures
* When you call the AWS HTTP API, you sign the request so that AWS can identify you, using your AWS credentials (access key & secret key).
* If you use the SDK or CLI, the HTTP request are signed for you.
* You should sign an AWS JTTP request using Signature v4 (SigV4).

You can send the signature in the header or in a query string.

------------------

## Section 10: Advanced Amazon S3
### Moving between Storage Classes
* You can trasition objects between storage classes.
* For infrequently accessed object, move them to Standard IA.
* For archive objects that you don't need fast access to, move them to Glacier or Glacier Deep Archive.
* Moving objects can be automated using Lifecycle Rules.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/770d1cc5-91ff-4bfe-add1-66ff69bf2243)

### Lifecycle Rules
* Transition Actions - configure objects to transition to another storage class
  * Move objects to Standard IA class 60 days after creation.
  * Move to Glacier for archiving after 6 months.
* Expiration actions - configure objects to expire (delete) after some time
  * Access log files can be set to delete after a 365 days.
  * Can be used to delete old versions of files (if versioning is enabled).
  * Can be used to delete incomplete Multi-Part uploads.
* Rules can be created for a certain prefix (example:s3://mybucket/mp3/*)
* Rules can be created for certain objects Tags (example:Department:Finance).

### Analytics - Storage Class Analysis
* Help you decide when to transition objects to the right storage class.
* Recommendations for Standard and Standard IA (Does not work for One-Zone IA or Glacier).
* Report is updated daily.
* 24 to 48 hours to start seeing data analysis.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0c1e6132-7e44-402a-bf8b-f3fcea2bad52)

----------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/caa61b58-2462-4ec1-b7ef-5c543ad252b3)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9c370fb4-a7fb-4b99-9f34-52498b458ae8)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/46e97026-08bb-45da-965c-e860f227abe4)
----------
### S3 Event Notifications
* S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication, ...
* Object name filtering possible (*.jpg)
* Use case: generate thumbnails of images uploaded to S3.
* Create as many S3 events as desired.
* S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2cee9fb6-8a24-4888-b467-fad19d1fcecd)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/548172de-5618-48a1-b540-e3280eb61735)

### S3 Event Notifications with Amazon EventBridge
* Advanced filtering options with JSON rules (metadata, object size, name, ...).
* Multiple Destinations - ex Step Functions, Kinesis Streams / Firehose ...
* EventBridge Capabilities - Archive, Replay events, Reliable delivery.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b685d703-125c-4439-a592-f1613c184f37)

-----------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b23a75ca-60b0-403d-bbe1-61253f5117c2)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/31c66cf4-b2bd-4c2f-ac72-040f099e051d)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9e307aef-d6e1-42f7-99ce-6181804dcad8)
-----------
### S3 - Baseline Performance
* Amazon S3 automatically scales to high request rates, latency 100-200ms.
* Your application can achieve at least 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per seconds per prefix in a bucket.
* There are no limits to the number of prefixes in a bucket.

### S3 Performance
* Multi-part upload:
  * Recommended for files > 100MB, must use for files > 5GB.
  * Can help parallelize uploads (speed up transfers)
 
 ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e2ff6dc5-0bb8-40eb-9042-73172f495e99)

* S3 Transfer Acceleration:
  * Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region.
  * Compatible with multi-part upload
 
 ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b85f2c9f-5679-47b2-a611-9113c56aa317)

#### S3 Performance - S3 Byte-Range Fetches
* Parallelize GETs by requesting specific byte ranges.
* Better resilience in case of failures.
* Can be used to speed up downloads.
* ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fd36574f-0b8f-4820-bf9d-fceff83ca106)
* Can be used to retrieve only partial data (for example the head of a file).
* ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e7a80b30-a546-4207-8cd3-96665b471920)

### S3 Select & Glacier Select
* Retrieve less data using SQL by performing server-side filtering
* Can filter by rows & columns (simple SQL statements).
* Less network transfer, less CPU cost client-side.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f843977f-fd93-4170-a0a1-28646a2e6993)

### S3 User-Defined Object Metadata & S3 Object Tags
* S3 User-Defined Object Metadata
  * When uploading an object, you can also assign metadata.
  * Name-value (key-value) pairs.
  * User defined metadata must begin with "x-amz-meta-".
  * Amazon S3 stores user-defined metadata keys in lowercase.
  * Metadata can be retrieved while retrieving the object.
* S3 Object Tags
  * Keu-value pairs for objects in Amazon S3.
  * Useful for fine-grained permissions (only access specific objects woth specific tags).
  * Useful for analytics purpose (using S3 Analytics to group by tags).
* **YOU CANNOT SEARCH THE OBJECT METADATA OR OBJECT TAGS**
* Instead, you must use an external DB as a search index such as DynamoDB.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b840109f-48a1-4e0b-a516-dc58e74cf19a)

------
## Section 11: Amazon S3 Security
### Encryption
There are 4 main methods to encrypt objects in S3:
* Server-Side Encryption (SSE)
  * Server-side encryption with Amazon S3-Managed Keys (SSE-S3) - Enabled by default.
    * Encrypts S3 objects using keys handled, managed and owned by AWS.
  * Serve-side encryption with KMS Key stored in AWS (SSE-KMS).
    * Leverage AWS Key Management System (KMS) to manage encryption keys.
  * Server-side encryption with customer-provided keys (SSE-C).
    * When you want to manage your own encryption keys.
* Client side encryption

### Encryption - SSE-S3
* Encryption using keys handled, managed, and owned by AWS.
* Object is encrypted server-side.
* Encryption type is AES-256.
* Must set header "X-amz-server-side-encryption": "AES256".
* Enabled by default for new buckets & new obiects.

<img width="813" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9661c584-7200-409a-b196-d52dbd188a91">

### Encryption - SSE-KMS
* Encryption using keys handled and managed by AWS KMS (Key Management Service).
* KMS advantages: user control + audit key usage using CloudTrail.
* Object is encrypted server side.
* Must set header "x-amz-server-side-encryption": "aws:kms".

<img width="824" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/38cae1c7-1a43-4d3a-beb0-84161d6758ce">

#### SSE-KMS Limitations
* If you use SSE-KMS, you may be impacted by the KMS limits.
* When you upload, it calls the GenerateDataKey KMS API.
* When you download, it calls the Decrypt KMS API.
* Count towards the KMS quota per second (5500, 10000, 30000 req/s based on region).
* You can request a quota increase using Service Quotas Console.

<img width="307" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d35039dc-518d-4751-bf87-3fa342796f41">

### Encryption - SSE-C
* Server-side encryption using keys fully managed by the customer outside of AWS.
* Amazon S3 does NOT store the encryption key you provide.
* HTTPS must be used.
* Encryption key must be provided in HTTP headers, for every HTTP request made.

<img width="818" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c3caa0cb-1d12-4b89-a722-4dd6fff50939">

### Encryption - Client-Side Encryption
* Use client libraries such as Amazon S3 Client-Side Encryption Library.
* Clients must encrypt data themselves before sending to Amazon S3.
* Clients must decrypt data themselves after retrieving from Amazon S3.
* Customer fully manages the keys and encryption cycle.

<img width="776" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/767017ec-1bf1-42ff-8e4d-3d768ad61784">

### Encryption in Transit (SSL/TLS)
* Amazon S3 exposes 2 endpoints:
  * HTTP Endpoint - non encrypted
  * HTTPS Endpoint - encryption in flight
* HTTPS is recomended.
* HTTPS is mandatory for SSE-C.
* You can force encryption in flight with a bucket policy.

------
<img width="1036" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/cc1f9f33-5626-4574-9766-f9196f2bd3ce">
under bucket creation

<img width="1019" alt="image" src="https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5f0ab8ac-84bc-4dd4-9cf7-3d5ff4dddacf">
kms encryption


Bucket policies will always be evaluated before default encryption.
SSE-S3 encryption is automatically applied to new objects stored in S3 bucket.

-------
#### S3 - CORS
CORS:
* Cross-Origin Resource Sharing (CORS).
* Origin = scheme (protocol) + host (domain) + port
  * example: https://www.example.com (implied port is 443 for HTTPS, 80 for HTTP)
* Web Browser based mechanism to allow requests to other origins while visiting the main origin.
* Same origin: http://example.com/app1 & http://example.com/app2
* Different origins: http://www.example.com & http://other.example.com
* The requests won't be fullfiled unless the other origin allows for the requests, using CORS Headers.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8b167a43-5f1c-4d93-b737-ed955a4a7586)

* If a client makes a cross-origin request on our S3 bucket, we need to enable the correct CORS headers.
* You can allow for a specific origin or for * (all origins).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c4d9e398-fbed-4637-aae3-afa71a099e6a)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1a58e3a4-5256-4ef0-a761-fd4cb4ee9a9c)

-------
#### S3 - MFA Delete
* MFA (Multi-Factor Authentication) - forces users to generate a code on a device before doing important operations on S3.
* MFA will be required to:
  * Permanently delete an object version.
  * Suspend Versioning on the bucket.
* MFA won't be required to:
  * Enable Versioning
  * List deleted versions
* To use MFA Delete, Versioning must be enabled on the bucket.
* Only the bucket owner (root) can enable/disable MFA Delete.

-------
#### S3 - Access Logs
* For audit purpose, you may want to log all access to S3 buckets,
* Any request made to S3, frm any account, authorized or denied, will be logged into another S3 bucket.
* That data can be analyzed using data analysis tools.
* The target logging bucket must be in the same AWS region.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6382e348-2402-4504-91df-0db06ed78132)

**Never ser your logging bucket to be the monitored bucket, this will cause a logging loop and it will grow exponentially.**

-----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/effefff3-e238-4c6b-b420-8aad9201a888)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9302efad-8a12-4dd7-80d4-49b9dce45571)

-----
#### S3 - Pre-Signed URLs
* Generate a pre-signed URLs using the S3 Console, AWS CLI or SDK.
* URL Expiration:
  * S3 Console - 1 min up to 720 min.
  * AWS CLI - configure expiration with --expires-in param in seconds.
* Users given a pre-signed URL inherit the permissions of the user that generated the URL for GET/PUT.
* Examples:
  * Allow only logged-in users to download a premium vidfeo from your S3 bucket.
  * Allow an ever-changing list of users to download files by generating URLs dynamically.
  * Allow temporarily a user to upload a file to a precise location in your S3 bucket.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a2b22c1d-3267-4345-925d-57e39c5e27a2)

-----
#### S3 - Access Points
* Access Points simplify security management for S3 Buckets.
* Each Access Point has:
  * its own DNS name (Internet Origin or VPC Origin).
  * an access point policy (similar to bucket policy) - manage security at scale.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/56a4d699-80c6-4c53-bd1a-94dc5c6745e7)

##### S3 - Access Points: VPC Origin
* We can define the access point to be accessible only from within the VPC.
* You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint).
* The VPC Endpoint Policy must allow access to the target bucket and Access Point.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/07a8a3d2-8f4b-42c1-bd66-55e84240573e)

-----
#### S3 - Object Lambda
* Use AWS Lambda Functions to change the object before it it retrieved by the caller application.
* Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Points.
* Use Cases:
  * Redacting personally identifiable information for analytics or non-production environments.
  * Converting across data formats, such as converting XML to JSON.
  * Resizing and watermarking images on the fly using caller-specific details, such as the user who requested the object.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/3a4e80a1-b6d7-4e0f-b8fc-38c884de3e8a)


------
## Section 12: CloudFront
* Content Delivery Network (CDN).
* Improves read performance, content is cached at the edge.
* Improves users experience.
* 216 Points of Presence globally (edge locations).
* DDoS protection (because worldwide), integration with Shield, AWS Web Application Firewall.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/479f0ac6-4529-4228-8d41-245b7c0705e8)

### CloudFront - Origins
* S3 bucket:
  * For distributing files and caching them at the edge.
  * Enhanced security with CloudFront Origins Access Control (OAC).
  * OAC is replacing Origin Access Identity (OAI).
  * CloudFront can be used as an ingress (to upload files to S3).
* Custom Origin (HTTP):
  * Application Load Balancer.
  * EC2 instance.
  * S3 website.
  * Any HTTP backend you want.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c28e9460-c544-4a8b-b665-21df28547011)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/92184ec8-d4d7-49eb-ae70-28755fa34461)

### CloudFront vs S3 Cross Region Replication
* CloudFront:
  * Global Edge network.
  * Files are cached for a TTL.
  * Great for static content that must be available everywhere.
* S3 Cross Region Replication:
  * Must be setup for each region you want replication to happen.
  * Files are updated in near real-time.
  * Read only.
  * Great for dynamic content that need to be available at low-latency in few regions.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8fc59537-25b1-42a3-9bc5-fc12b66895b7)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a2cacf08-4343-4ce5-a2c4-fb8746029eb5)

### CloudFront - Caching and Caching Policies
* The cache lives at each CloudFront Edge Location.
* CloudFront identifies each object in the cache using the Cache Key.
* You want to maximize the Cache Hit ratio to minimize requests to the origin.
* You can invalidate part of the cache using the CreateInvalidation API.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ac3520d5-821c-46af-b961-29673f191ab5)

#### What is CloudFront Cache Key?
* A unique identifier for every object in the cache.
* By default, consists of hostname + resource portion of the URL.
* If you have an application that serves up content that varies based on user, device, language, location, ...
* You can add other elements (HTTP headers, cookies, query strings) to the cache key using Cloudfront Cache Policies.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/17fe5fe0-f72e-47b8-85c6-3a86873bf246)

#### CloudFront Policies - Cache Policy
* Cache based on:
  * HTTP Headers: None - Whitelist
  * Cookies: None - Whitelist - Include All-Except - All
  * Query Strings: None - Whitelist - Include All-Except — All
* Control the TTL (0 seconds to | year), can be set by the origin using the Cache-Control header, Expires.
* Create your own policy or use Predefined Managed Policies.
* All HTTP headers, cookies, and query strings that you include in the Cache Key are automatically included in origin.

##### HTTP Headers
* None:
  * Don't include any headers in the Cache Key (except default).
  * Headers are not forwarded (except default).
  * Best caching performance.
* Whitelist:
  * Only specified headers included in the Cache Key.
  * Specified headers are also fowarded to Origin.

##### Query strings
* None:
  * Don't include any query strings in the Cache Key.
  * Query strings are not fowarded.
* Whitelist:
  * Only specified query strings included in the Cache Key.
  * Only specified query strings are forwarded.
* Include All-Except:
  * Include all query strings in the Cache Key except the specified list.
  * All query strings are forwarded except the specified list.
* All:
  * Include all query strings in the Cache Key.
  * All query strings are forwarded.
  * Worst performance.

#### CloudFront Policies - Origin Request Policy
* Specify values that you want to include in origin requests without including them in the Cache Key (no duplicated cached content)
* You can include:
  * НТТР headers: None - Whitelist - All viewer headers options
  * Cookies: None - Whitelist - All
  * Query Strings: None - Whitelist - All
* Ability to add CloudFront HTTP headers and Custom Headers to an origin request that were not included in the viewer request
* Create your own policy or use Predefined Managed Policies

**Cache Policy vs Origin Request Policy**
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f40ec38f-918a-4ca6-9e59-521d637c822a)

#### CloudFront Policies - Cache Invalidations
* In case you update the backend origin, CloudFront doesn't know about it and will only get the refreshed content after the TTL has expired.
* However, you can force an entire or partial cache refresh (bypassing the TTL) by performing a CloudFront Invalidation.
* You can invalidate all files (*) or a specific path (/images/*)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/35884d31-d477-41ac-82cc-88b071192e48)

------
#### CloudFront - Cache Behaviors
* Configure different settings for a given URL path pattern.
* Example: one specific cache behaviour to images/*.jpg files on your web server.
* Route to different kind of origins groups based on the content type or path pattern
  * /images/*
  * /api/*
  * /* (default)
* When adding additional Cache Behaviours, the Default Cache Behaviour is always the last to be processed an is always /*.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/90201f22-62d7-4c28-845d-e0514f014c4c)

Examples:
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/81c75a32-1b85-40e9-9409-a17183c38533)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/efe89b8d-ccaa-47d6-bef1-61732bc42541)

-------
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f33f7f41-f23c-4c19-984c-5a0e1fd5564f)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1268e820-7d16-4927-9eff-898564d4165a)

------
### CloudFront - ABL as an Origin
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9a773385-b758-4cb5-9c5e-88380e0fb22e)

------
### CloudFront - Geo Restriction
* You can restrict who can access your distribution
  * Allowlist: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
  * Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.
* The "country" is determined using a 3rd party Geo-IP

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d216bd3e-7faf-48d9-bb05-38216f1a63b6)

------
### CloudFront - Signed URLs/Cookies
* You want to distribute paid shared content to premium users over the world
* We can use CloudFront Signed URL / Cookie. We attach a policy with:
  * Includes URL expiration
  * Includes IP ranges to access the data from
  * Trusted signers (which AWS accounts can create signed URLs)
* How long should the URL be valid for?
  * Shared content (movie, music): make it short (a few minutes)
  * Private content (private to the user): you can make it last for years
* Signed URL = access to individual files (one signed URL per file)
* Signed Cookies = access to multiple files (one signed cookie for many

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c9651eba-1418-47f3-bfa8-3fcb13f2a7e5)

#### Signed URLs vs S3 Pre-Signed URL:
* CloudFront Signed URL:
  * Allow access to a path, no matter the origin.
  * Account wide key-pair, only the root can manage it.
  * Can filter by IP, path, date, expiration.
  * Can leverage caching features.
* S3 Pre-Signed URL:
  * Issue a request as the person who pre-signed the URL.
  * Uses the IAM key of the signing IAM principal.
  * Limited lifetime.
 
#### CloudFront Signed URL Process
* Two types of signers:
  * Either a trusted key group (recommended)
    * Can leverage APIs to create and rotate keys (and IAM for APL security)
  * An AWS Account that contains a CloudFront Key Pair
    * Need to manage keys using the root account and the AWS console
    * Not recommended because you shouldn't use the root account for this
* In your CloudFront distribution, create one or more trusted key groups
* You generate your own public / private key
  * The private key is used by your applications (e.g. EC2) to sign URLs
  * The public key (uploaded) is used by CloudFront to verify URLs

-------
### CloudFront - General/Advanced Concepts
* The pricing will vary depending on the location (country).
* You can reduce the number of edge locations for cost reduction
* Three price classes:
  1. Price Class All: all regions - best performance
  2. Price Class 200: most regions, but excludes the most expensive regions
  3. Price Class 100: only the least expensive regions
 
##### Origin Groups
* To increase high-availability and do failover.
* Origin Group: one primary and one secondary origin.
* If the primary origin fails, the second one is used.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/711aa528-228f-490b-bb2b-23bdb2a6186f)

##### Field Origin Encryption
* Protect user sensitive information through application stack.
* Adds an additional layer of security along with HTTPS.
* Sensitive information encrypted at the edge close to user.
* Uses asymmetric encryption.
* Usage:
  * Specify set of fields in POST requests that you want to be encrypted (up to 10 fields).
  * Specify the public key to the encryption them.
 
------
### CloudFront - Real Time Logs
* Get real time requests received by CloudFront sent to Kinesis Data Streams.
* Monitor, analyze, and take actions based on content delivery performance.
* Allows you to choose:
  * Sampling Rate - percentage of requests for which you want to receive.
  * Specific fields and specific Cache Behaviors (path patterns).



------
## Section 13: ECS, ECR & Fargate
### Amazon ECS - EC2 Launch Type
* ECS = Elastic Container Service.
* Launch Docker containers on AWS = Launch ECS Tasks on CS Clusters.
* EC2 Launch Type: you must provision & mantain the infrastructure (the EC2 instances).
* Each EC2 Instance must run the ECS Agent to register in the ECS Cluster.
* AWS takes care of starting/stopping containers

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/36c4dfe2-3857-474d-b9f6-503a793986ee)

-----
### Amazon ECS - Fargate Launch Type
* Launch Docker containers on AWS.
* You don't provision the infrastructure (no EC2 instances to manage).
* It's all serverless.
* You just create task definitions.
* AWS just runs ECS Tasks for you based on the CPU/RAM you need.
* To scale, just increase the number of tasks. Simple - no mopre EC2 instances.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/831f64b3-6698-482d-bb61-ac5017a785c9)

-----
### Amazon ECS - IAM Roles for ECS
* EC2 Instances Profile (EC2 Launch Type only):
  * Used by the ECS agent.
  * Makes API call to ECS service.
  * Send container logs to CLoudWatch Logs.
  * Pull Docker image from ECR.
  * Reference sensitive data in Secrets Manager or SSM Parameter Store
* ECS Task Role:
  * Allows each task to have a specific role.
  * Use different roles for the different ECS Services you run.

 ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f09333a8-8fbd-4171-8170-0c195a453e17)

-----
### Amazon ECS - Load Balancer Integrations
* Application Load Balancer supported and works for most use cases.
* Network Load Balancer recommended only for high throughput/high performance use cases, or to pair ir with AWS Private Link.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d3390672-d52b-41d1-ace4-452f1f418e47)

### Amazon ECS - Data Volumes (EFS)
* Mount EFS file systems onto ECS tasks.
* Works for both EC2 and Fargate launch types.
* Tasks running in any AZ will share the same data in the EFS file system.
* Fargate + EFS = Serverless.
* Use cases: persistent multi-AZ shared storage for your containers.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9d9b78d1-8715-42ae-81ad-93a4abbc89ed)
-----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/82188748-b450-4e10-9ede-adb5ef47db8f)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fb2f21ba-6ba9-4016-b1ab-824739102468)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/73cb1e64-246f-43fb-aed0-bc25c9c687df)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c4ed55c3-616a-4f12-b81f-889c66639b69)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6e104f4d-90e3-4aeb-9d4f-3e47ca7a6438)

----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e0a33a09-9cd7-471a-bbf6-00550e40d4ac)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/73da391d-9366-48aa-8450-313306ef7471)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7b7aabcb-5dfd-41fd-ab81-e7bc179e72d6)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e47ecf9f-865a-4e7a-bf1e-0dcd1ef12442)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/af6f3e6e-0d28-4dc0-b02d-509ab1482044)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/13199219-75cf-4dc6-a9a4-f017f417f8d1)

-----
### Amazon ECS - Auto Scaling
* Automatically increase/decrease the desired number of CS tasks.
* Amazon ECS Auto Scaling uses AWS Application Auto Scaling
  * ECS Service Average CPU Utilization.
  * ECS Service Average Memory Utilization - Scale on RAM.
  * ALB Request Count Per Target - metric coming from the ALB.
* Target Tracking - scale based on target value for a specific CloudWatch metric.
* Step Scaling - scale based on a specified CloudWatch Alarm.
* Scheduled Scaling - scale based on a specified date/time (predictable changes).
* ECS Service Auto Scaling (task level) != ECS Auto Scaling (EC2 instance level).
* Fargate Auto Scaling is much easier to setup (because Serverless).

#### EC2 Launch Type - Auto Scaling EC2 Instances
* Accommodate ECS Service Scaling by adding underlying EC2 Instances.
* Auto Scaling Group Scaling
  * Scale your ASG based on CPU utilization.
  * Add EC2 instances over time.
* ECS Cluster Capacity Provider
  * USed to automatically provision and scale the infrastructure for your ECS Task.
 
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a3e3dd3f-4d57-4f82-bf00-7350f12cab98)

-----
### Amazon ECS - Rolling Updates
* When updating from v1 to v2, we can control how many tasks can be started and stopped and which order.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7e6d9415-2965-47a9-a8ca-07b72d034e5a)

###### Min 50%, Max 100%:
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/172353df-1634-4a3b-8fc4-07c99ade3a90)

###### Min 100%, Max 150%:
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/dde43b74-cc88-463f-b2ae-b7e0cf666341)

-----
### Amazon ECS - Solutions Architectures
**ECS Tasks involving Event Bridge**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e22611cc-0053-48c7-ab93-4cde2327b96d)

**ECS Tasks involving Event Bridge Schedule**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2ef9a32b-108b-4b1a-ae07-496c9b48d15b)

**ECS SQS Queue Example**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/60189841-0e2d-4bfa-af2d-0c6c380e1fed)

-----
### Amazon ECS - Task Definitions (In Depth)
* Task definitions are metadata in JSON form to tell ECS how to run a Docker container.
* It contains crucial informations, such as:
  * Image name.
  * Port biding for container and host.
  * Memory and CPU required.
  * Environment variables.
  * Networking information.
  * IAM Role.
  * Logging configuration (ex. CloudWatch).

#### Load Balancing (EC2 Launch Type)
* We get a Dynamic Host Port Mapping if you define only the container port in the task definition.
* The ALB finds the right port on your EC2 Instances.
* You must allow on the EC2 instance's Security Group any port from the ALB's Security Group.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d070ff73-058f-4d1c-afff-eaad41829a08)

#### Load Balancing (Fargate)
* Each task has a unique private IP.
* Only define the container port (host port is not applicable).
* Example:
  * ECS ENI Security Group (Allow port 80 from the ALB).
  * ALB Security Group (Allow port 80/443 from the web).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/973203b9-9936-4cb6-970d-8356cf148a22)

**Amazon ECS -> One IAM Role per task definition**
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/308dde5f-d155-402d-9163-71df9e629b01)

#### Amazon ECS - Environment Variables
* Environment Variables
  * Hardcoded - e.g., URLs
  * SSM Parameter Store - sensitive variables (e.g., API keys, shared configs).
  * Secret Managet - sensitive variables (e.g., DB password)
* Environment Files (bulk) - Amazon S3.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/90727bc0-19e3-4c28-a40a-be364dc67a8d)

#### Amazon ECS - Data Volumes (Bind Mounts)
* Share data between multiple containers in the same Task Definition.
* Works for both EC2 and Fargate tasks.
* EC2 Tasks - using EC2 instances storage
  * Data are tied to the lifecycle of the EC2 instance.
* Fargate Tasks - using epheral storage
  * Data are tied to the container(s) using them
  * 20 GiB - 200 GiB (default to 20 GiB).
* Use cases:
  * Share spheral data between multiple containers.
  * "Sidecar" container pattern, where the "sidecar" container used to send metrics/logs to other destinations (separation of concerns).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/78f7ffc5-c792-4d3c-a4d6-2a0c68466dd4)


![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7be16316-1762-4e72-9200-b222dd1b0abb)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7504ea07-4447-4bb8-a8a8-cd9fabfc9a52)

-----
### Amazon ECS - Task Placements
* When a task of type EC2 is launched ECS must determine where to place it, with the constraints of CPU, memory, and available port.
* Similarly, when a service scales in, ECS needs to determine which task to terminate.
* To assist with this, you can define a task placement strategy and task placement constraints.
* **this is only for ECS with EC2, not Fargate.**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/966afcaf-3651-42b5-8df4-cb8558216225)

#### ECS Task Placement Process
* Task placement strategies are a best effort
* When Amazon ECS places tasks, it uses the following process to select container instances:
  1. Identify the instances that satisfy the CPU, memory, and port requirements in the task definition.
  2. Identify the instances that satisfy the task placement constraints.
  3. Identify the instances that satisfy the task placement strategies.
  4. Select the instances for task placement.

#### ECS Task Placement Strategies
##### Binpack
* Place tasks based on the least available amount of CPU or memory.
* This minimizes the number of instances in use (cost savings).

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c05e434c-88e7-447a-8642-26a08d1b34fc)

##### Random
* Places the task randomly.

##### Spread
* Place the task evenly based on the specified value.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/2cd7800a-d498-4071-aca9-84d6fffff59c)

**You can mix and match these.**

#### ECS Task Placement Constraints
distinctInstance: place each task on a different container instance.
memberOf: places task on instances that satisfy an expression.

------
### Amazon ECR
* ECR = Elastic Container Registry.
* Store and manage Docker images on AWS.
* Private and public repository.
* Fully integrated with ECS.
* Access is controlled by IAM.
* Supports image vulnerability scanning, versioning, image tags, ...

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0dedb913-2fd3-40fa-b3c9-52c3ab56d2b9)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/17c6bbcc-da7c-4bab-b55f-d8006c0e330d)

------
### AWS Copilot
* CLI tool to build, release, and operate production-ready containerized apps.
* Run your apps on AppRunner, ECS and Fargate.
* Helps you focus on building apps rather than setting up infrastructure.
* Provisions all required infrastructure for containerized apps (ECS, VPC, ELB, ECR, ...)
* Automated deployments with one command using CodePipeline.
* Deploy to multiple environments.
* Troubleshooting, logs, health status, ...

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/775aedd3-9993-488b-807d-a0fc01e33277)


------
### Amazon EKS
* Amazon EKS = Amazon Elastic Kubernetes Service.
* It is a way to launch managed Kubernetes clusters on AWS
* Kubernetes is an open-source system for automatic deployment, scaling and management of containerized (usually Docker) application
* It's an alternative to ECS, similar goal but different API
* EKS supports EC2 if you want to to deploy worker nodes or Fargate to deploy serverless containers
* Use case: if your company is already using Kubernetes on-premises or in another cloud, and wants to migrate to AWS using Kubernetes
* Kubernetes is cloud-agnostic (can be used in any cloud - Azure, GCP, ...)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ab998fc1-9ad9-4de5-bcf1-4205e615e3b1)

#### Amazon EKS - Node Types
* Managed Node Groups:
  * Creates and manages Nodes (EC2 instances) for you
  * Nodes are part of an ASG managed by EKS
  * Supports On-Demand or Spot Instances
* Self-Managed Nodes:
  * Nodes created by you and registered to the EKS cluster and managed by an ASG
  * You can use prebuilt AMI - Amazon EKS Optimized AMI
  * Supports On-Demand or Spot Instances
* AWS Fargate
  * No maintenance required; no nodes
 
#### Amazon EKS - Data Volumes
* Need to specify StorageClass manifest on your EKS cluster.
* Leverages a Container Storage Interface (CSI) compliant driver.

---------
## Section 14: AWS Elastic Beanstalk
### Elastic Beanstalk - Overview
Elastic Beanstalk is a developer centric view of deploying an application on AWS
* It uses all the component's we've seen before: EC2, ASG, ELB, RDS, ...
* Managed service
  * Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration, ...
  * Just the application code is the responsibility of the developer
* We still have full control over the configuration
* Beanstalk is free but you pay for the underlying instances

#### Components
* Application: collection of Elastic Beanstalk components (environments, versions, configurations, ...)
* Application Version: an iteration of your application code
* Environment
  * Collection of AWS resources running an application version (only one application version at a time)
  * Tiers: Web Server Environment Tier & Worker Environment Tier
  * You can create multiple environments (dev, test, prod, ...)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/50492353-40cd-445a-82bf-f31715481650)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/dbe80473-bc35-4e82-82a1-b8de413c6d0f)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/df48b16b-0d57-4c04-81c4-7447ec709fb8)

-----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fc258f3e-8932-4bd5-9a3a-7ab008d57aa9)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e48aab4e-684f-4fac-ba41-7848d6348d79)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/93a27453-ee52-4542-bfa1-61193fce2dda)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/918bfd2f-4a9d-43b8-92f9-406085bebe1f)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b62c49b4-3125-4861-a2f6-b914c5eda349)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/49405aa9-6352-4340-a83d-1d70c477d5c7)

Creating a second env
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c01cd010-45f1-462b-9af7-8328a64cb60a)

----------------
### Beanstalk Deployment Modes
#### Beanstalk Deployment Option for Updates
* All at once (deploy all in one go) - fastest, but instances aren't available to serve traffic for a bit (downtime)
* Rolling: update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy
* Rolling with additional batches: like rolling, but spins up new instances to move the batch (so that the old application is still available)
* Immutable: spins up new instances in a new ASG, deploys version to these instances, and then swaps all the instances when everything is healthy
* Blue Green: create a new environment and switch over when ready
* Traffic Splitting: canary testing - send a small % of traffic to new deployment.

1. All at once:
   * Fastest deployment
   * Application has downtime
   * Great for quick iterations in development environment
   * No additional cost

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b73211d1-5089-449b-92f9-2537bb1cdd0b)

2. Rolling:
   * Application is running below capacity.
   * Can set the bucket size.
   * Application is running both versions simultaneously.
   * No additional cost.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/366eb219-94e9-4024-9a62-e85f89a65d8c)

3. Rolling with additional batches:
   * Application is running at capacity.
   * Can set the bucket size.
   * Application is running both versioons simultaneously.
   * Small additional cost.
   * Additional batch is removed at the end of the deployment.
   * Longer deployment.
   * Good for prod.
  
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/d580295c-25aa-4fb1-b8fe-a24f3db915ab)

4. Immutable:
   * Zero downtime.
   * New code is deployed to ne instances on a temporary ASG.
   * High cost, double capacity.
   * Longest deployment.
   * Quick rollback in case of failures
   * Great for prod.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6751a35c-6985-4410-ad23-2594fa331842)

5. Blue/Green:
   * Not a "direct feature" of Elastic Beanstalk.
   * Zero downtime and release facility.
   * Create a new "stage" environment and deploy v2 there.
   * The new environment (green) can be validated independently and roll back if issues.
   * Route 53 can be setup using weighted policies to redirect a little bit of traffic to the stage env.
   * Using beanstalk, "swap URLs" when done with the env test.
  
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b2dfe1d4-7e0a-4f17-9a4d-c15ba67ae275)

6. Traffic Splitting:
   * Canary testing.
   * New application version is deployed to a temporary ASG with the same capacity.
   * A small % of traffic is sent to the temporary ASG for a canfigurable amount of time.
   * Deployment health is monitored.
   * If there's a deployment failure, this triggers an automated rollback (very quick).
   * No app downtime.
   * New instances are migrated from the temporary to the original ASG.
   * Old app version is then terminated.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/34d9b528-6850-4164-a494-b905a80719a1)


![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/65dbc994-6e3f-4443-a811-649820310265)

------
### Beanstalk Lifecycle Policy
* Elastic Beanstalk can store at most 1000 application versions.
* If you don't remove old versions, you won't be able to deploy anymore.
* To phase out old application versions, use a lifecycle policy.
  * Based on time (old versions are removed)
  * Based on space (when you have too many versions)
* Versions that are currenttly used won't be deleted.
* Option not to delete the source bundle in S3 to prevent data loss.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a0ca61c4-f5ea-494f-9f66-4b8ea5723ca4)

------
### Beanstalk Extentions
* A zip file containing our code must be deployed to Elastic Beanstalk.
* All the parameters set in the UI can be configured woth the code using files.
* Requirements:
  * in the .ebextentions/ directory in the root of the source code.
  * YAML/JSON format.
  * .config extentions
  * Able to modify some default settings using: option_settings.
  * Ability to add resources such as RDS, ElastiCache, DynamoDB, etc, ...
 * Resources managed by .ebextentions get deleted if the env goes away.

 **Under the hood Elastic Beanstalk relies on CloudFormation, which is used to provision other AWS services.**

#### Elastic Beanstalk - Cloning
* Clone an environment with the exact same configuration
* Useful for deploying a "test" version of your application
* All resources and configuration are preserved:
  * Load Balancer type and configuration
  * RDS database type (but the data is not preserved)
  * Environment variables
* After cloning an environment, you can change settings

-------
### Elastic Beanstalk Migrations
#### Elastic Beanstalk Migrations - Load Balancer
* After creating an Elastic Beanstalk env, you cannot change the Elastic Load Balancer type (only the configuration).
* To migrate:
  1. create a new environment with the same configuration except LB (can't clone).
  2. deploy your application onto the new environment.
  3. perform a CNAME swap or Route 53 update.
 
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ffa10637-9c90-4d87-a44f-cdb025351b9a)

#### RDS with Elastic Beanstalk
* RDS can be provisioned with Beanstalk, which is greate for dev/test.
* This is not great for prod as the DB lifecycle is tied to the Beanstalk env lifecycle.
* The best for prod is to separately create an RDS database and provide our EB application with the connection stribng.

#### Elastic Beanstalk Migrations - Decouple RDS
1. Create a snapshot of RDS DB (as a safeguard)
2. Go to the RDS console and protect the RDS database from deletion
3. Create a new Elastic Beanstalk env, without RDS, point your application to existing RDS
4. perform a CNAME swap. (blue/green) or Route 53 update, confirm working
5. Terminate the old environment (RDS won't be deleted)
6. Delete CloudFormation stack (in DELETE_FAILED state)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e184ce25-16b4-4922-90ac-6d489090b133)

---------
## Section 15: AWS CloudFormation
* CloudFormation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported)
* For example, within a CloudFormation template, you say:
  * I want a security group
  * I want two EC2 instances using this security group
  * I want two Elastic IPs for these EC2 instances
  * I want an S3 bucket
  * I want a load balancer (ELB) in front of these EC2 instances
* Then Cloudformation creates those for you, in the right order, with the exact configuration that you specify.

#### Benefits of CloudFormation
* Infrastructure as code
  * No resources are manually created, which is excellent for control
  * The code can be version controlled for example using Git
  * Changes to the infrastructure are reviewed through code
* Cost
  * Each resources within the stack is tagged with an identifier so you can easily see how much a stack costs you
  * You can estimate the costs of your resources using the CloudFormation template
  * Savings strategy: In Dev, you could automation deletion of templates at 5 PM and recreated at 8 AM, safely
* Productivity
  * Ability to destroy and re-create an infrastructure on the cloud on the fly
  * Automated generation of Diagram for your templates!
  * Declarative programming (no need to figure out ordering and orchestration)
* Separation of concern: create many stacks for many apps, and many layers. Ex:
  * VPC stacks
  * Network stacks
  * App stack

### How CloudFormation works
* Templates must be uploaded in S3 and then referenced in CloudFormation.
* To update a template, we can't edit previous ones. We have to re-upload a new version of the template to AWS.
* Stacks are identified by a name.
* Deleting a stack deletes every single artifact that was created by CloudFormation.

### Deploying CloudFormation Templates
* Manual way
  * Editing templates in Application Composer or code editor
  * Using the console to input parameters, etc...
  * We'll mostly do this way in the course for learning purposes
* Automated way
  * Editing templates in a YAML file
  * Using the AWS CLI (Command Line Interface) to deploy the templates, or using a Continuous Delivery (CD) tool
  * Recommended way when you fully want to automate your flow
 
### CloudFront - Building Blocks
* Template's Components
  * AWSTemplateFormatVersion - identifies the capabilities of the template "2010-09-09"
  * Description - comments about the template
  * Resources (MANDATORY) - your AWS resources declared in the template
  * Parameters - the dynamic inputs for your template
  * Mappings - the static variables for your template
  * Outputs - references to what has been created
  * Conditionals - list of conditions to perform resource creation
* Template's Helpers
  * References
  * Functions

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7f313b6c-048f-41a4-aaf9-b02cf04be448)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5fd48ed1-2cc3-4ed9-bbad-c651ccd4b018)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ba6c3edd-6507-4d4e-bb6f-d59c63cee12b)

------
### CloudFront - Resources
* Resources are the core of your CloudFormation template (MANDATORY)
* They represent the different AS Components that will be created and configured
* Resources are declared and can reference each other
* AWS figures out creation, updates and deletes of resources for us
* There are over 700 types of resources (!)
* Resource types identifiers are of the form:
**service-provider:: service-name::data-type-name**

------
### CloudFront - Parameters
* Parameters are a way to provide inputs to your AWS CloudFormation template
* They're important to know about if:
  * You want to reuse your templates across the company
  * Some inputs can not be determined ahead of time
* Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates, thanks to types

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ce0b681a-0474-4bcb-bf70-7eb48fc26d4d)

**YOU SHOULD USE A PARAM IF THE RESOURCE CONFIGURATION IS LIKELY TO CHANGE IN THE FUTURE, SO YOU WON'T HAVE TO RE-UPLOAD A TEMPLATE TO CHANGE THE CONTENT**

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/69bae39f-0454-417a-8eca-5c32ba1f9bca)

#### Reference a Patameter
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f0f22387-0991-4284-a904-f134e7f2e3a9)
* The Fn::Ref function can be levereged to reference parameters.
* Parameters can be used anywher in a template.
* The shorthand for this in YAML is !Ref
* This function can also reference other elements within the template.

#### Pseudo Parameters
* AWS offers us Pseudo Parameters in any CloudFormation template.
* These can be used at any time and are enabled by default.

------
### CloudFront - Mappings
* Mappings are fixed variables within your CloudFormation template
* They're very handy to differentiate between different environments (dev vs prod), regions (AWS regions), AMI types...
* All the values are hardcoded within the template

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/dd88b485-f41c-4233-961e-9f4ae8dea38b)

* To access we use Fn::FindInMap to return a named value from a specific key.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c9d60caa-7260-411f-8ff4-c8afa885bb69)

**Mappings vs Parameters**
 Mappings are great when you know in advance all the values that can be taken and that they can be deduced from variables such as
   Region
   Availability Zone
   AWS Account
   Environment (dev vs prod)
 They allow safer control over the template
 Use parameters when the values are really user specific

------
### CloudFront - Outputs & Exports
* The Outputs section declares optional outputs values that we can import into other stacks (if you export them first!
* You can also view the outputs in the AWS Console or in using the AWS CLI
* They're very useful for example if you define a network CloudFormation, and output the variables such as VPC ID and your Subnet IDs
* It's the best way to perform some collaboration cross stack, as you let expert handle their own part of the stack

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/22d61d98-ee87-4967-8d61-84c5680c75b5)

* Creating a SSH Security Group as part of one template.
* We create an output that references that security group.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/464ced2c-550d-4489-8a4c-2be1ed70381d)

* We then create a second template that leverages that security group.
* For this, we use the Fn::ImportValue function.
* You can't delete the underlying stack until all the references are deleted.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8126fe5d-014b-4b1a-8ab6-6259b56d4aa5)

------
### CloudFront - Conditions
* Conditions are used to control the creation of resources or outputs based on a condition
* Conditions can be whatever you want them to be, but common ones are.
  * Environment (dev / test / prod)
  * AWS Region
  * Any parameter value
* Each condition can reference another condition, parameter value or mapping

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ea96a7b5-8786-4fd8-81ac-7908f62cbaed)

#### Define a Condition

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f385083d-b1ea-4ced-a357-1d9a10939f27)

* The logical ID is for you to choose. It's how you name condition
* The intrinsic function (logical) can be any of the following:
  * Fn::And
  * Fn::Equals
  * Fn:lf
  * Fn::Not
  * Fn::Or

#### Apply a Condition
* Conditions can eb applied to resources/outputs/ect

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/25a1ae95-758e-40d4-aba6-8659ab1907b6)

------
### CloudFront - Intrinsic Functions
* Ref
* Fn::Base64
* Fn::GetAtt
* En::Cidr
* Fn::FindInMap
* Fn: GetAZs
* Fn::ImportValue
* Fn::Select
* Fn::Join
* Fn::Split
* Fn::Sub
* Fn::Transform
* Fn::ForEach
* Fn::TojsonString
* Fn::Length
* Condition Functions (Fn::lf, Fn::Not, Fn:: Equals, etc).

#### Fn::Ref
* The Fn::Ref function can be leveraged to reference
  * Parameters - returns the value of the parameter
  * Resources - returns the physical ID of the underlying resource (e.g., EC2 ID)
* The shorthand for this in YAML is !Ref

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/aeda30fa-c247-450c-8616-935e79764ca1)

#### Fn::GetAtt
* Attributes are attached to any resources you create
* Example: the AZ of an EC2 instance

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b2f1d3b3-8b98-4374-b61c-dcf059a0d929)

#### Fn:FindInMap
* We use Fn::FindInMap to return a named value from a specific key
* !FindInMap [ MapName, TopLevelKey, SecondLevelKey ]

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/7089e629-8b5d-48e4-b0e2-1465133552b3)

#### Fn::ImportValue
* Import values that are exported in other stacks
* For this, we use the Fn::ImportValue function

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4ff7b59f-b23c-4db8-a4ac-e6628d71820c)

#### Fn::Base64
* Convert String to Base64 representation.

------
### CloudFront - Rollbacks
* Stack Creation Fails:
  * Default: everything rolls back (gets deleted). We can look at the log
  * Option to disable rollback and troubleshoot what happened
* Stack Update Fails:
  * The stack automatically rolls back to the previous known working state
  * Ability to see in the log what happened and error messages
* Rollback Failure? Fix resources manually then issue ContinueUpdateRollback API from Console

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/24d6a808-0bff-4246-8ca8-b7c1d6ece18d)

------
### CloudFront - Service Role
* IAM role that allows CloudFormation to create/update/delete stack resources on your behalf
* Give ability to users to create/update/delete the stack resources even if they don't have permissions to work with the resources in the stack
* Use cases:
  * You want to achieve the least privilege principle
  * But you don't want to give the user all the required permissions to create the stack resources
* User must have iam:PassRole permissions

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/58513d25-4040-4319-a4d3-fbfdfeacce3b)

------
### CloudFront - Capabilities
* CAPABILITY_NAMED_IAM and CAPABILITY_IAM
  * Necessary to enable when you CloudFormation template is creating or updating IAM resources (IAM User, Role, Group, Policy, Access Keys, Instance Profile...)
  * Specify CAPABILITY_NAMED_IAM if the resources are named
* CAPABILITY_AUTO_EXPAND
  * Necessary when your CloudFormation template includes Macros or Nested Stacks (stacks within stacks) to perform dynamic transformations
  * You're acknowledging that your template may change before deploying
* InsufficientCapabilitiesException
  * Exception that will be thrown by CloudFormation if the capabilities haven't been acknowledged when deploying a template (security measure)

------
### CloudFront - Deletion Policy
#### Delete
* DeletionPolicy:
  * Control what happens when the CloudFormation template is deleted or when a resource is removed from a CloudFormation template
  * Extra safety measure to preserve and backup resources
* Default DeletionPolicy=Delete
  * ⚠️ Delete won't work on an S3 bucket if the bucket is not empty

#### Retain
* DeletionPolicy=Retain:
  * Specify on resources to preserve in case of CloudFormation deletes
* Works with any resources

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e7ac49b9-0b68-47bc-9ecb-53cf169e7e92)

#### Snapshot
* DeletionPolicy=Snapshot
* Create one final snapshot before deleting the resource
* Examples of supported resources:
  * EBS Volume, ElastiCache Cluster, ElastiCache ReplicationGroup
  * RDS DBInstance, RDS DBCluster, Redshift Cluster, Neptune DBCluster, DocumentDB DBCluster

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/204d9e44-9ad4-42ca-ab8d-3f22ddcb911a)

------
### CloudFront - Stack Policies
* During a CloudFormation Stack update, all update actions are allowed on all resources (default).
* A Stack Policy is a JSON document that defines the update actions that are allowed on specific resources during Stack updates.
* Protect resources from unintentional updates.
* When you set a Stack Policy, all resources in the Stack are protected by default.
* Specify an explicit ALLOW for the resources you want to be allowed to be updated.

**To prevent accidental deletes on a CloudFormation Stack, use TerminationProtection**
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c7fc97e4-ce5d-4b0e-9ce7-95b78a1b5682)

------
### CloudFront - Custom Resources
* Used to
  * define resources not yet supported by CloudFormation
  * define custom provisioning logic for resources can that be outside of CloudFormation (on-premises resources, 3ra party resources...)
  * have custom scripts run during create / update / delete through Lambda functions (running a Lambda function to empty an S3 bucket before being deleted)
* Defined in the template using AWS::CloudFormation::CustomResource or Custom::MyCustomResource TypeName (recommended)
* Backed by a Lambda function (most common) or an SNS topic.

#### Defining a Custom Resource
* Service Token specifies where CloudFormation sends requests to, such as Lambda ARN or SINS ARN (required & must be in the same region)
* Input data parameters (optional)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6f82c773-e7a6-44e1-bbf1-1635087df49c)

#### Use case - Delete content from an S3 bucket
* You can't delete a non-empty S3 bucket
* To delete a non-empty 53 bucket, you must first delete all the objects inside it
* We can use a custom resource to empty an 53 bucket before it gets deleted by CloudFormation

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b0f51161-c199-4241-940c-fdda865d1a86)

------
### CloudFront - StackSets
* Create, update, or delete stacks across multiple accounts and regions with a single operation/template
* Target accounts to create, update, delete stack instances from StackSets
* When you update a stack set, all associated stack instances are updated throughout all accounts and regions
* Can be applied into all accounts of an AWS Organization
* Only Administrator account (or Delegated Administrator) can create StackSets

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4290c723-7412-435d-b42d-606ad8892a13)

---------
## Section 16: AWS Integration & Messaging - SQS, SNS & Kinesis
### Queues Overview

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/af334add-6c21-4fbd-b6d6-800f498fdf53)

### Amazon SQS - Standard Queue
* Fully managed service, used to decouple applications
* Attributes:
  * Unlimited throughput, unlimited number of messages in queue
  * Default retention of messages: 4 days, maximum of 14 days
  * Low latency (<10 ms on publish and receive)
  * Limitation of 256KB per message sent
* Can have duplicate messages (at least once delivery, occasionally)
* Can have out of order messages (best effort ordering)

#### SQS - Producing Messages
* Produced to SQS using the SDK (SendMessage API)
* The message is persisted in SQS until a consumer deletes it
* Message retention: default 4 days, up to 14 days
* Example: send an order to be processed
  * Order id
  * Customer id
  * Any attributes you want
* SQS standard has unlimited throughput

#### SQS - Consuming Messages
* Consumers (running on EC2 intances, servers, or AWS Lambda)...
* Poll SQS for messages (receive up to 10 messages at a time)
* Process the messages (example: insert the message into an RDS database)
* Delete the messages using the DeleteMessage API

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/9f874049-d370-4c4b-8e23-47f666c28a81)

#### SQS - Multiple EC2 Intances Consumers
* Consumers receive and process messages in parallel
* At least once delivery
* Best-effort message ordering
* Consumers delete messages after processing them
* We can scale consumers horizontally to improve throughput of processing

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/cc04acd5-fcb3-42c6-94d2-531e9d1d2a93)

#### SQS with Auto Scaling Group
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c9be2553-6c65-4f88-b32c-757136ea4e9e)

#### Amazon SQS - Security
* Encryption:
  * In-flight encryption using HTTPS API
  * At-rest encryption using KMS keys
  * Client-side encryption if the client wants to perform encryption/decryption itself
* Access Controls: AM policies to regulate access to the SQS API
* SQS Access Policies (similar to S3 bucket policies)
  * Useful for cross-account access to SQS queues
  * Useful for allowing other services (SNS, S3...) to write to an SQS queue

-----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e7502e13-97b9-432b-9a62-dc49b28a6eba)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f0c6c1fd-4f8b-4694-806f-b484dc19b09c)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/573c9ef9-2727-4311-9fe3-c1cea9e93694)
-----
### SQS - Queue Access Policy
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/70cb4b05-d293-4295-9920-23d4d49806ea)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ecfdbe59-e13e-4bbb-9ede-6b3bd880169e)

-----
### SQS - Message Visibility Timeout
* After a message is polled by a consumer, it becomes invisible to other consumers
* By default, the "message visibility timeout" is 30 seconds
* That means the message has 30 seconds to be processed
* After the message visibility timeoyt is over, the message is "visible" in SQS
* If a message is not processed within the visibility timeout, it will be processed twice
* A consumer could call the ChangeMessage Visibility APl to get more time
* If visibility timeout is high (hours), and consumer crashes, re-processing will take time
* If visibility timeout is too low (seconds), we may get duplicates

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/c9d5934e-9a7c-4633-87ed-05b68bf71c67)

-----
### SQS - Dead Letter Queues (DLQ)
* If a consumer fails to process a message within the Visibility Timeout, the message goes back to the queue
* We can set a threshold of how many times a message can go back to the queue
* After the MaximumReceives threshold is exceeded, the message goes into a dead letter queue (DLQ)
* Useful for debugging!
* DLQ of a FIFO queue must also be a FIFO queue
* DLQ of a Standard queue must also be a Standard queue
* Make sure to process the messages in the DLQ before they expire:
  * Good to set a retention of 14 days in the DQL

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6b660691-8e7c-47cb-853a-fafa5ce4ddcb)

#### SQS DLQ - Redrive to Source
* Feature to help consume messages in the DLQ to understand what is wrong with them.
* When our code is fixed, we can redrive the messages from the DLQ back into the source queue (or any other queue) in batches without writing custom code.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/6c0504c5-6940-4063-99c8-936bb67c8e8c)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f692c917-3eb1-4c05-90e5-157e3406d434)

-----
### SQS - Delay Queues
* Delay a message (consumers don't see it immediately) up to 15 minutes
* Default is 0 seconds (message is available right away)
* Can set a default at queue level
* Can override the default on send using the DelaySeconds parameter

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f0b54873-98ca-4270-b20b-fcc23ab69623)

------
### SQS - Long Polling
* When a consumer requests messages from the queue, it can opitionally "wait" for messages to arrive if there are none in the queue
* This is called Long Polling
* LongPolling decreases the number of APl calls made to SQS while increasing the efficiency and decreasing the latency of your application.
* The wait time can be between I sec to 20 sec (20 sec preferable)
* Long Polling is preferable to Short Polling
* Long polling can be enabled at the queue level or at the API level using ReceiveMessageWaitTimeSeconds

### SQL Extended Client
* Message size limit is 256KB, using the SQS Extended Client you can send larger messages.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/531fa0b1-54a0-4824-89e3-04b5eb284aff)

### Must know API
* CreateQueue (MessageRetentionPeriod), DeleteQueue
* PurgeQueue: delete all the messages in queue
* SendMessage (DelaySeconds), ReceiveMessage, DeleteMessage
* MaxNumberOfMessages: default 1, max 10 (for ReceiveMessage API)
* ReceiveMessage Wait TimeSeconds: Long Polling
* ChangeMessage Visibility: change the message timeout

* Batch APIs for SendMessage, DeleteMessage, ChangeMessage Visibility helps decrease your costs

------
### SQS - FIFO Queues
* FIFO = First In First Out
* Limited throughput: 300 msg/s without batching, 3000 msg/s with
* Exactly-once send capability (by removing duplicates)
* Messages are processed in order by the consumer

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/37e108ce-0a4d-4a48-900a-4ff9bec1d376)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/62a47a5d-c9f4-4992-aa73-46386bd4ab82)

#### SQS FIFO - Deduplication
* De-duplication interval is 5 minutes
* Two de-duplication methods:
  * Content-based deduplication: will do a SHA-256 hash of the message body
  * Explicitly provide a Message Deduplication ID

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/3cc44da1-96a4-4f8a-8555-40a48c529e74)

#### SQS FIFO - Message Grouping
* If you specify the same value of MessageGroupD in an SQS FIFO queue, you can only have one consumer, and all the messages are in order
* To get ordering at the level of a subset of messages, specify different values for Message GroupID
  * Messages that share a common Message Group ID will be in order within the group
  * Each Group ID can have a different consumer (parallel processing!)
  * Ordering across groups is not guaranteed

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4f717a1e-caf3-43e6-adcd-66999b86b754)

------
### Amazon SNS
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4a73fda9-a7e3-46b4-87ba-673eb42a0a40)

* The "event producer" only sends message to one SNS topic.
* As many "event receivers" (subscriptions) as we want to listen to the SINS topic notifications.
* Each subscriber to the topic will get all the messages (note: new feature to filter messages).
* Up to 12,500,000 subscriptions per topic.
* 100,000 topics limit.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/e8a6b8b8-f856-47c6-8452-d736e4c9c6f4)

Many AWS Services can send data directly to SNS for notifications.


#### How to publish
* Topic Publish (using the SDK)
  * Create a topic
  * Create a subscription (or many)
  * Publish to the topic
* Direct Publish (for mobile apps SDK)
  * Create a platform application
  * Create a platform endpoint
  * Publish to the platform endpoint
  * Works with Google GCM, Apple APNS, Amazon ADM ...

#### SNS - Security
* Encryption:
  * In-flight encryption using HTTPS API
  * At-rest encryption using KMS keys
  * Client-side encryption if the client wants to perform encryption/decryption itself
* Access Controls: IAM policies to regulate access to the SNS API
* SNS Access Policies (similar to S3 bucket policies)
  * Useful for cross-account access to SNS topics
  * Useful for allowing other services ( S3...) to write to an SNS topic
 
------
### SNS + SQS: Fan Out
* Push once in SNS, receive in all SQS queues that are subscribers
* Fully decoupled, no data loss
* SQS allows for: data persistence, delayed processing and retries of work
* Ability to add more SQS subscribers over time
* Make sure your SQS queue access policy allows for SNS to write
* Cross-Region Delivery: works with SQS Queues in other regions

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/8d08d104-e367-4e95-898a-21197c06170b)

#### S3 Events to multiple queues
* For the same combination of: event type (e.g, object create) and prefix (e.g. images/) you can only have one S3 Event rule
* If you want to send the same 53 event to many SQS queues, use fan-out

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/bc84cd4e-c3dc-48c2-9d29-eb6dbc34b798)

#### SNS Fifo + SQS Fifo: Fan Out
* In case you need fan out + ordering + deduplication
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/78fe4ded-0af9-4309-be48-2010251bb094)

#### SNS - Message Filtering
* JSON policy used to filter messages sent to SNS topic's subscriptions.
* If a subscription doens't have a filter policy, it receives every message.

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/73d78ba1-b0c0-4b30-9b6d-a67e5f4ed765)


![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/aa632e80-134b-486e-8f7d-97def6d2a877)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/dfcbc4bb-62ae-494f-8c90-244accd1378a)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/bf80b175-30b3-4a3a-b011-72a4a64e38e5)

------
### Kinesis
* Makes it easy to collect, process, and analyze streaming data in real-time
* Ingest real-time data such as: Application logs, Metrics, Website clickstreams, IoT telemetry data...
* Kinesis Data Streams: capture, process, and store data streams
* Kinesis Data Firehose: load data streams into AWS data stores
* Kinesis Data Analytics: analyze data streams with SQL or Apache Flink
* Kinesis Video Streams: capture, process, and store video streams

------
### Kinesis Data Streams

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5c29208b-3f62-4a1a-8f22-0c1406273c5f)

* Retention between | day to 365 days
* Ability to reprocess (replay) data
* Once data is inserted in Kinesis, it can't be deleted (immutability)
* Data that shares the same partition goes to the same shard (ordering)
* Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
* Consumers:
  * Write your own: Kinesis Client Library (KCL), AWS SDK
  * Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics

### Kinesis Data Streams - Capacity Modes
* Provisioned mode:
  * You choose the number of shards provisioned, scale manually or using API
  * Each shard gets IMB/s in (or 1000 records per second)
  * Each shard gets 2MB/s out (classic or enhanced fan-out consumer)
  * You pay per shard provisioned per hour
* On-demand mode:
  * No need to provision or manage the capacity
  * Default capacity provisioned (4 MB/s in or 4000 records per second)
  * Scales automatically based on observed throughput peak during the last 30 days
  * Pay per stream per hour & data in/out per GB

### Kinesis Data Streams Security
* Control access / authorization using IAM policies
* Encryption in flight using HTTPS endpoints
* Encryption at rest using KMS
* You can implement encryption/decryption of data on client side (harder)
* VPC Endpoints available for Kinesis to access within VPC
* Monitor API calls using CloudTrail

------
### Kinesis Producers
* Puts data records into data streams
* Data record consists of:
  * Sequence number (unique per partition-key within shard)
  * Partition key (must specify while put records into stream)
  * Data blob (up to | MB)
* Producers:
  * AWS SDK: simple producer
  * Kinesis Producer Library (KPL): C++, Java, batch, compression, retries
  * Kinesis Agent: monitor log files
* Write throughput: 1 MB/sec or 1000 records/sec per shard
* PutRecord API
* Use batching with PutRecords API to reduce cost & increase throughput

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/236683db-620a-4d95-b2cf-0b6b20a95574)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fcd01175-3c58-4b33-8dfc-e6457c64b1bc)

------
### Kinesis Consumers
* Get data records from data streams and process them
* AWS Lambda
* Kinesis Data Analytics
* Kinesis Data Firehose
* Custom Consumer (AWS SDK) - Classic or Enhanced Fan-Out
* Kinesis Client Library (KCL): library to simplify reading from data stream

#### Kinesis Consumers - Custom Consumer
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4072b9a9-22c1-43a7-8e7b-15f6dbe8b415)

* Shared (Classic) Fan-out Consumer - pull
  * Low number of consuming applications
  * Read throughput: 2 MB/sec per shard across all consumers
  * Max. 5 GetRecords API calls/sec
  * Latency ~200 ms
  * Minimize cost ($)
  * Consumers poll data from Kinesis using GetRecords APl call
  * Returns up to 10 MB (then throttle for 5 seconds) or up to 10000 records
* Enhanced Fan-out Consumer - push
  * Multiple consuming applications for the same stream
  * 2 MB/sec per consumer per shard
  * Latency ~ 70 ms
  * Higher costs ($$$)
  * Kinesis pushes data to consumers over HTTP/2 (SubscribeToShard API)
  * Soft limit of 5 consumer applications (KCL) per data stream (default)

#### Kinesis Consumers - Lambda
* Supports Classic & Enhanced fan-out consumers
* Read records in batches
* Can configure batch size and batch window
* If error occurs, Lambda retries until succeeds or data expired
* Can process up to 10 batches per shard simultaneously

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/1c2e0633-4699-46c8-b9e1-4d5b001f2482)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/33a68411-3225-442c-b402-06ddb5d93517)

-----
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/383f7aa2-98b1-4633-8ae2-a1be7128befa)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/4885c601-e734-429c-9280-e0c1dab3e1cb)
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/5e27cbcf-bd7b-4547-8a4f-f64a15dbcd74)

------
### Kinesis Client Library (KCL)
* A Java library that hekps read record from a Kinesis Data Stream with distributed applications sharing the read workload
* Each shard is to be read by only one KCL instance
  * 4 shards = max. 4 KCL instances
  * 6 shards = max. 6 KCL instances
* Progress is checkpointed into DynamoDB (needs IAM access)
* Track other workers and share the work amongst shards using DynamoDB
* KCL can run on EC2, Elastic Beanstalk, and on-premises
* Records are read in order at the shard level
* Versions:
  * KCL 1.x (supports shared consumer)
  * KCL 2.x (supports shared & enhanced fan-out consumer)

#### KCL - 4 Shards
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/b4643996-bc9c-4f63-920d-efe9b65acd0e)

#### KCL - 6 Shards, Scaling Kinesis
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a8dd4f7e-0c13-48ef-9c7d-b548cb5ff4cf)

------
### Kinesis Operations
#### Shard Splitting
* Used to increase the Stream capacity (1 MB/s data in per shard)
* Used to divide a "hot shard"
* The old shard is closed and will be deleted once the data is expired
* No automatic scaling (manually increase/decrease capacity)
* Can't split into more than two shards in a single operation

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/f5540835-eec0-4089-aa4f-51993ee93b26)

#### Merging Shards
* Decrease the Stream capacity and save costs
* Can be used to group two shards with low traffic (cold shards)
* Old shards are closed and will be deleted once the data is expired
* Can't merge more than two shards in a single operation

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/87702e09-a95c-489a-8035-827f435b3634)

------
### Kinesis Data Firehose
* Fully Managed Service, no administration, automatic scaling, serverless
  * AWS: Redshift / Amazon S3 / OpenSearch
  * 3rd party partner: Splunk / MongoDB / DataDog / NewRelic / ...
  * Custom: send to any HT TP endpoint
* Pay for data going through Firehose
* Near Real Time
  * Buffer interval: 0 seconds (no buffering) to 900 seconds
  * Buffer size: minimum IMB
* Supports many data formats, conversions, transformations, compression
* Supports custom data transformations using AWS Lambda
* Can send failed or all data to a backup S3 bucket

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/55ddd9d3-71fa-4f7d-8a38-66ad4543a2f3)

#### Kinesis Data Streams vs Firehose
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/fd95ccd9-6302-43db-9566-bf09f4763c0f)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/ebb384c0-1a91-4ac1-aebd-bb76eaac88ad)

------
### Kinesis Data Analytics
* Real-time analytics on Kinesis Data Streams & Firehose using SQL
* Add reference data from Amazon S3 to enrich streaming data
* Fully managed, no servers to provision
* Automatic scaling
* Pay for actual consumption rate
* Output:
  * Kinesis Data Streams: create streams out of the real-time analytics queries
  * Kinesis Data Firehose: send analytics query results to destinations
* Use cases:
  * Time-series analytics
  * Real-time dashboards
  * Real-time metrics

#### Kinesis Data Analytics for Apache Flink
* Use Flink (Java, Scala or SQL) to process and analyze streaming data
* Run any Apache Flink application on a managed cluster on AWS
  * provisioning compute resources, parallel computation, automatic scaling
  * application backups (implemented as checkpoints and snapshots)
  * Use any Apache Flink programming features
  * Flink does not read from Firehose (use Kinesis Analytics for SQL instead)

-----
### Data Ordering
#### Ordering data into Kinesis
* Imagine you have 100 trucks the road sending heroes positions regularly into AWS.
* You want to consume the data in order for each truck, so that you can track their movement accurately.
* How should you send that data into Kinesis?
* Answer: send using a "Partition Key" value of the "truck_id"
* The same key will always go to the same shard

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/104a730c-42b8-4559-a4fa-61f00528297d)

#### Ordering data into SQS
* For SQS standard, there is no ordering.
* For SQS FIFO, if you don't use a Group ID, messages are consumed in the order they are sent, with only one consumer

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/20e1c9e7-59c6-48a2-9746-fa2164bd56e3)

* You want to scale the number of consumers, but you want messages to be "grouped" when they are related to each other
* Then you use a Group ID (similar to Partition Key in Kinesis)

![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/0a15db93-c291-41dd-997d-288a1b9885d8)

#### Kinesis vs SQS ordering
* Let's assume 100 trucks, 5 kinesis shards, I SQS FIFO
* Kinesis Data Streams:
  * On average you'll have 20 trucks per shard
  * Trucks will have their data ordered within each shard
  * The maximum amount of consumers in parallel we can have is 5
  * Can receive up to 5 MB/s of data
* SQS FIFO
  * You only have one SQS FIFO queue
  * You will have 100 Group ID
  * You can have up to 100 Consumers (due to the 100 Group ID)
  * You have up to 300 messages per second (or 3000 if using batching)

-----
### SQS vs SNS vs Kinesis
![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/eff1a3de-c964-4810-b1a9-aa16b7872733)


---------
## Section 17: AWS Monitoring - CloudWatch, X-Ray and CloudTrail
### Monitoring in AWS
* AWS CloudWatch:
  * Metrics: Collect and track key metrics
  * Logs: Collect, monitor, analyze and store log files
  * Events: Send notifications when certain events happen in your AWS
  * Alarms: React in real-time to metrics / events
* AWS X-Ray:
  * Troubleshooting application performance and errors
  * Distributed tracing of microservices
* AWS CloudTrail:
  * Internal monitoring of API calls being made
  * Audit changes to AWS Resources by your users

----
### CloudWatch Metrics
