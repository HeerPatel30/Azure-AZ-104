# Configure Storage Accounts (AZ-104)

## Module Overview

Azure Storage provides secure, scalable, durable, and highly available cloud storage services. A Storage Account acts as a container that groups Azure Storage services together and provides a unique namespace for your data.

In this module, you'll learn how to:

- Understand Azure Storage fundamentals
- Implement Azure Storage accounts
- Explore different Azure Storage services
- Choose the correct storage account type
- Configure replication strategies
- Secure access to storage
- Protect storage endpoints

---

# 1. Introduction

## What is Azure Storage?

Azure Storage is Microsoft's cloud storage platform designed to store massive amounts of data while providing high availability, durability, scalability, and security.

Azure Storage is used for:

- Storing files
- Virtual machine disks
- Application data
- Backups
- Logs
- Images and videos
- Big data analytics

### Key Benefits

- Highly Available
- Durable
- Secure
- Scalable
- Accessible from anywhere
- Fully managed by Microsoft

---

# 2. Implement Azure Storage

## Storage Account

A Storage Account is the top-level Azure resource that provides a unique namespace for storing data.

Example:

https://mystorageaccount.blob.core.windows.net

Everything stored inside Azure Storage belongs to a Storage Account.

A storage account provides:

- Authentication
- Authorization
- Billing
- Replication
- Networking
- Encryption
- Monitoring

---

## Storage Account Components

Each storage account can contain multiple storage services:

- Blob Containers
- File Shares
- Queues
- Tables

---

## Create a Storage Account

When creating a storage account, you must choose:

- Subscription
- Resource Group
- Storage Account Name
- Region
- Performance Tier
- Replication Option
- Security Settings
- Networking Configuration

---

## Storage Account Name Rules

- Must be globally unique
- Between 3 and 24 characters
- Lowercase letters only
- Numbers allowed
- No spaces
- No special characters

Example:

```
storageprod001
```

---

# 3. Explore Azure Storage Services

Azure Storage contains four primary services.

---

## Blob Storage

Blob Storage stores unstructured data.

Examples:

- Images
- Videos
- PDFs
- Backups
- Logs
- Documents

Blob Storage is ideal when file structure isn't important.

### Blob Types

### Block Blob

Used for:

- Documents
- Images
- Videos
- Backups

Most common blob type.

---

### Append Blob

Optimized for appending data.

Commonly used for:

- Logging
- Audit files

---

### Page Blob

Stores random-access files.

Mainly used for:

- Azure Virtual Machine disks

---

## Azure Files

Azure Files provides fully managed network file shares.

Supports:

- SMB protocol
- NFS protocol

Can be mounted by:

- Windows
- Linux
- macOS

Common Uses:

- Shared company files
- Lift-and-shift applications
- Home directories

---

## Queue Storage

Queue Storage stores messages.

Used for communication between application components.

Supports asynchronous processing.

Maximum message size:

64 KB

Examples:

- Order processing
- Background jobs
- Microservices communication

---

## Table Storage

NoSQL key-value database.

Stores structured but non-relational data.

Characteristics:

- Schema-less
- Fast
- Low cost
- Massive scalability

Examples:

- User profiles
- Device information
- Metadata

---

# 4. Determine Storage Account Types

Azure offers different storage account types.

---

## General-purpose v2 (GPv2)

Recommended for almost all workloads.

Supports:

- Blob Storage
- Azure Files
- Queues
- Tables
- Azure Data Lake Storage Gen2

Supports:

- Lifecycle Management
- Blob Versioning
- Soft Delete
- Hot/Cool/Cold/Archive tiers

Microsoft recommends GPv2.

---

## General-purpose v1 (GPv1)

Legacy storage account.

Supports fewer features.

Avoid using GPv1 for new deployments.

---

## Blob Storage Account

Legacy account.

Supports only Blob Storage.

Mostly replaced by GPv2.

---

## Premium Block Blob

SSD-based storage.

Best for:

- High transaction workloads
- AI
- Media
- Analytics

---

## Premium FileStorage

Premium Azure Files.

SSD-backed.

Low latency.

---

## Premium Page Blob

Optimized for Azure VM disks.

---

## Performance Tiers

### Standard

Uses HDD storage.

Advantages:

- Lower cost
- Suitable for backups
- General-purpose workloads

---

### Premium

Uses SSD storage.

Advantages:

- Low latency
- High throughput
- High IOPS

Best for production workloads.

---

# 5. Determine Replication Strategies

Azure automatically replicates your data to protect against failures.

---

## LRS (Locally Redundant Storage)

Copies:

3

Location:

Single datacenter

Protects against:

- Disk failure
- Server failure

Does NOT protect against datacenter failure.

Lowest cost.

---

## ZRS (Zone-Redundant Storage)

Copies:

3

Across:

Multiple Availability Zones

Protects against:

Zone failures.

---

## GRS (Geo-Redundant Storage)

Copies:

6

Primary Region:

3 copies

Secondary Region:

3 copies

Protects against:

Regional disasters.

Secondary region cannot be read.

---

## RA-GRS

Same as GRS.

Difference:

Secondary region is readable.

Useful for:

- Disaster Recovery
- Reporting

---

## GZRS

Combines:

- Zone replication
- Geo replication

Provides very high availability.

---

## RA-GZRS

Adds read access to GZRS secondary region.

Highest redundancy option.

---

## Replication Comparison

| Replication | Copies | Zone Protection | Region Protection | Read Secondary |
|-------------|--------|-----------------|------------------|----------------|
| LRS | 3 | No | No | No |
| ZRS | 3 | Yes | No | No |
| GRS | 6 | No | Yes | No |
| RA-GRS | 6 | No | Yes | Yes |
| GZRS | 6 | Yes | Yes | No |
| RA-GZRS | 6 | Yes | Yes | Yes |

---

# 6. Access Storage

Azure provides several methods to access storage securely.

---

## Microsoft Entra ID

Recommended authentication method.

Benefits:

- Azure RBAC
- MFA
- Conditional Access
- Least privilege

Best option for users and applications.

---

## Access Keys

Each storage account has:

- Key1
- Key2

Provide full administrative access.

Use only when necessary.

Rotate keys regularly.

---

## Shared Access Signature (SAS)

A SAS provides temporary, limited access to storage resources.

You can specify:

- Permissions
- Start time
- Expiry time
- Allowed IP addresses
- HTTPS only

Types:

- Service SAS
- Account SAS
- User Delegation SAS

User Delegation SAS is the most secure because it uses Microsoft Entra ID.

---

# 7. Secure Storage Endpoints

Azure Storage endpoints should always be secured.

---

## HTTPS Only

Always enable Secure Transfer Required.

Prevents unencrypted HTTP traffic.

Recommended for production.

---

## Storage Firewall

Restrict access by:

- Public IP addresses
- Azure Virtual Networks
- Selected Networks

Blocks unauthorized traffic.

---

## Service Endpoint

Allows secure communication between Azure Virtual Networks and Azure Storage.

Traffic stays on Microsoft's backbone network.

Storage account still uses its public endpoint.

---

## Private Endpoint

Provides a private IP address inside a Virtual Network.

Traffic never leaves the Azure private network.

Highest level of security.

Ideal for sensitive workloads.

---

## Encryption

Azure Storage encrypts all data at rest automatically.

Encryption Algorithm:

AES-256

Enabled by default.

---

## Microsoft-managed Keys

Default encryption option.

Microsoft manages key lifecycle.

---

## Customer-managed Keys

Keys stored in Azure Key Vault.

Provides:

- Greater control
- Compliance
- Key rotation

---

## Soft Delete

Protects data from accidental deletion.

Supported for:

- Blob Storage
- File Shares
- Containers

Deleted data can be restored within the retention period.

---

## Blob Versioning

Automatically creates previous versions whenever a blob changes.

Allows rollback to earlier versions.

---

## Lifecycle Management

Automatically moves blobs between access tiers.

Example Policy:

30 Days → Cool

90 Days → Cold

180 Days → Archive

365 Days → Delete

Helps reduce storage costs.

---

# AZ-104 Exam Tips

## Remember these:

### Storage Services

| Service | Stores |
|----------|--------|
| Blob | Unstructured data |
| Files | SMB/NFS file shares |
| Queue | Messages |
| Table | NoSQL structured data |

---

### Storage Account Type

GPv2 = Recommended

---

### Performance

Standard = HDD

Premium = SSD

---

### Replication

LRS = Single datacenter

ZRS = Availability Zones

GRS = Secondary Region

RA-GRS = Readable Secondary

GZRS = Zone + Geo

RA-GZRS = Zone + Geo + Read

---

### Authentication

Microsoft Entra ID → Best

SAS → Temporary access

Access Keys → Full account access

---

### Networking

Service Endpoint

- Public endpoint
- Azure backbone

Private Endpoint

- Private IP
- Highest security

---

### Security Best Practices

✔ Enable HTTPS only

✔ Disable anonymous access

✔ Use Microsoft Entra ID

✔ Rotate access keys

✔ Use SAS instead of sharing keys

✔ Use Private Endpoints

✔ Enable Soft Delete

✔ Enable Blob Versioning

✔ Enable Lifecycle Management

✔ Use least privilege RBAC

---

# Quick Revision

Azure Storage → Cloud storage platform

Storage Account → Management boundary

Blob → Unstructured data

Files → SMB/NFS shares

Queue → Messaging

Table → NoSQL

GPv2 → Recommended storage account

Standard → HDD

Premium → SSD

LRS → Same datacenter

ZRS → Availability Zones

GRS → Secondary region

RA-GRS → Read secondary

Service Endpoint → Public endpoint over Azure backbone

Private Endpoint → Private IP

SAS → Temporary delegated access

Microsoft Entra ID → Recommended authentication

Encryption → AES-256 enabled by default

Soft Delete → Recover deleted data

Blob Versioning → Restore previous versions

Lifecycle Management → Automatically move/delete blobs based on age
