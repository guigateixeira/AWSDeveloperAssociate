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




