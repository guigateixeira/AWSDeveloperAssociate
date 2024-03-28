# AWSDeveloperAssociate

## Section 6: EC2 Instance Storage

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

* Copying a sanpshot:
  ![image](https://github.com/guigateixeira/AWSDeveloperAssociate/assets/50753240/a787a17e-91c4-48ea-a05a-2b469104a5e7)

#### EBS Volume Types

* EBS Volume come in 6 types:
  * **gp2/gp3 (SSD)**: General porpose SSD volume that balances price and performance for a wide variety of workloads.
  * **io1/io2 Block Express (SSD)**: Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads.
  * **st 1 (HDD)**: Low cost HDD volume designed for frequently accessed, throughput-intensive workloads.
  * **sc 1 (HDD)**: Lowest cost HDD volume designed for less frequently accessed workloads.
 
* EBS Volumes are caracterized in Size | Throughput | IOPS (I/O Ops Per Sec)
* **Only gp2/gp3 amd io1/io2 Block Express can be usedas boot volumes.**

###### GP SSD
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
