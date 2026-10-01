# EBS and EC2 Storage

> CLF-C02 focus: choosing between block, temporary local, and shared file storage for EC2 workloads.

## Storage choices at a glance

| Service | Storage model | Persistence and scope | Common use |
| --- | --- | --- | --- |
| Amazon EBS | Block storage | Persistent; volume resides in one Availability Zone | Boot volumes, databases, and application data for EC2 |
| EC2 instance store | Block storage physically attached to the host | Temporary; tied to the host and instance lifecycle | Buffers, caches, scratch data, and temporary high-I/O work |
| Amazon EFS | Managed NFS file storage | Persistent; Regional file systems store data across multiple AZs | Shared Linux file storage for many compute resources |
| Amazon FSx | Managed file systems | Persistent; deployment depends on the selected FSx service | Windows file shares or high-performance Lustre workloads |

> [!TIP]
> A common exam pattern is to ask whether the workload needs a disk for one server, temporary local speed, or a shared file system.

## Amazon Elastic Block Store (EBS)

Amazon EBS provides durable block storage volumes for EC2. The operating system sees an attached EBS volume as a disk that can be partitioned and formatted.

### Core properties

- An EBS volume is created in one Availability Zone and can attach to an EC2 instance in that same AZ.
- A volume persists independently of the instance unless it is configured to be deleted when the instance terminates.
- Volumes can be attached, detached, and reattached where the volume and instance configuration permits it.
- EBS data is replicated within its Availability Zone to protect against individual hardware failure.
- EBS supports encryption for data at rest, data moving between the volume and instance, and snapshots created from the volume.
- Volume type and provisioned performance can be changed as workload needs change.

Most EC2 root volumes use EBS. By default, a root EBS volume is commonly configured for deletion when its instance terminates, while separately attached data volumes commonly persist. Always check the **delete on termination** setting.

### Volume categories

| Category | General fit |
| --- | --- |
| SSD-backed | Transactional workloads, boot volumes, databases, and workloads measured mainly by IOPS |
| HDD-backed | Large sequential workloads, streaming, logs, and workloads measured mainly by throughput |

Exact volume types, limits, and pricing can change. For CLF-C02, focus first on the SSD-versus-HDD workload distinction.

## EBS snapshots

An EBS snapshot is a point-in-time backup of an EBS volume.

- Snapshots are incremental after the first snapshot: only changed blocks are saved.
- AWS stores snapshots in Amazon S3 infrastructure, but you do not manage them through an S3 bucket.
- A snapshot can create a new EBS volume.
- A snapshot can be copied to another AWS Region or shared with another account when permissions and encryption settings allow it.
- Creating snapshots is the normal path for backing up EBS volumes and for creating EBS-backed AMIs.

Snapshot lifecycle policies and centralized backup operations belong in the later Backup and Disaster Recovery material.

## EC2 instance store

An instance store provides temporary block storage on disks physically attached to the host running the EC2 instance.

### When to use it

- Buffers and caches
- Scratch space
- Temporary processing data
- Replicated data that can be rebuilt elsewhere
- Workloads that benefit from very high local disk performance

### Important limitation

Instance-store data is **ephemeral**. It can be lost when the instance stops, hibernates, terminates, or the underlying host fails. A reboot normally preserves it, but important data still needs durable storage or replication elsewhere.

Use EBS or a managed file/object service for data that must survive the instance lifecycle.

## Amazon Elastic File System (EFS)

Amazon EFS is a fully managed, elastic file system that uses the Network File System (NFS) protocol. Multiple compute resources can mount the same EFS file system concurrently.

### Key characteristics

- Designed primarily for Linux workloads and NFS clients
- Automatically grows and shrinks as files are added and removed
- Supports shared access from multiple EC2 instances and other supported compute services
- **Regional** file systems store data across multiple Availability Zones for resilience
- **One Zone** file systems store data in one AZ for workloads that do not need multi-AZ resilience
- Uses security groups and network configuration to control access to mount targets

### EFS storage classes and lifecycle management

| Storage class | Intended access pattern |
| --- | --- |
| EFS Standard | Frequently accessed data needing multi-AZ resilience |
| EFS Infrequent Access | Data accessed less often |
| EFS Archive | Long-lived data accessed only a few times per year |
| EFS One Zone classes | Data that can remain in a single AZ |

EFS Lifecycle Management can move files between storage classes based on access patterns. This reduces storage cost without requiring an application to copy files manually.

## Amazon FSx

Amazon FSx offers fully managed file systems built for particular operating systems and workloads. AWS handles much of the infrastructure provisioning, maintenance, and backups, while you control access and how the file system is used.

### FSx for Windows File Server

- Provides managed Windows file systems using the SMB protocol.
- Supports Windows features and integration with Microsoft Active Directory.
- Fits Windows home directories, business applications, content repositories, and shared file storage.

### FSx for Lustre

The official CLF-C02 guide explicitly lists FSx for Lustre as out of scope. These source-note details are background; prioritize FSx for Windows File Server when studying managed file storage.

- Provides a managed high-performance Lustre file system.
- Fits machine learning, high performance computing, video processing, and financial modeling.
- Can link with Amazon S3 so large datasets can be processed through a high-performance file interface.
- Offers scratch and persistent deployment choices for different durability needs.

Other FSx file system choices can appear in AWS, but Windows File Server and Lustre are the ones emphasized in the source notes.

## Choosing the right service

| Requirement | Best starting answer |
| --- | --- |
| Persistent boot disk for one EC2 instance | EBS |
| Point-in-time backup of an EC2 disk | EBS snapshot |
| Temporary local scratch data | Instance store |
| Shared elastic Linux file system | EFS |
| Managed Windows SMB file share | FSx for Windows File Server |
| Very high-performance shared file system for HPC or ML | FSx for Lustre |

## Shared responsibility

AWS manages the underlying storage hardware and service infrastructure. You remain responsible for access controls, data classification, encryption choices where configurable, backup requirements, file permissions, and the operating systems or applications that use the storage.

The general shared responsibility model remains in [Cloud Computing](01-cloud-computing.md); later Security and Backup chapters cover those controls in greater depth.

## Exam memory checks

1. **Which service provides a persistent block volume for EC2?** Amazon EBS.
2. **Can an EBS volume attach to an instance in another Availability Zone?** No. The instance and volume must be in the same AZ.
3. **What is an EBS snapshot?** An incremental point-in-time backup that can create a new EBS volume.
4. **Should the only copy of important data be kept in an instance store?** No. Instance-store data is temporary and tied to the instance and host lifecycle.
5. **Which service provides a shared NFS file system for Linux workloads?** Amazon EFS.
6. **Which service is a managed Windows SMB file system?** FSx for Windows File Server.

## Avoiding duplication in later chapters

- S3 object storage and S3 storage classes: Amazon S3
- Backup plans, Recovery Point Objective, and Recovery Time Objective: Disaster Recovery
- Detailed KMS key management: Security and Encryption
- Exact service pricing: Billing and Pricing

## References

- [Amazon EBS features](https://docs.aws.amazon.com/ebs/latest/userguide/EBSFeatures.html)
- [Amazon EBS snapshots](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-snapshots.html)
- [Amazon EC2 instance store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)
- [Amazon EFS features](https://docs.aws.amazon.com/efs/latest/ug/features.html)
- [Amazon FSx for Windows File Server](https://docs.aws.amazon.com/fsx/latest/WindowsGuide/what-is.html)
- [Amazon FSx for Lustre](https://docs.aws.amazon.com/fsx/latest/LustreGuide/what-is.html)
