# Configure Storage Accounts & Configure Azure Blob Storage — Notes

---

# PART 1: Configure Storage Accounts

## 1. Implement Azure Storage

[Azure Storage](https://learn.microsoft.com/en-us/azure/storage/common/storage-introduction) is Microsoft's cloud storage solution for modern data storage scenarios — a massively scalable object store, a file system service for the cloud, a messaging store for reliable messaging, and a NoSQL store. It's AI-ready and used for file shares, working data (websites, mobile/desktop apps), IaaS virtual machines, and PaaS cloud services.

### Categories of Data

| Category | Description | Storage Examples |
|---|---|---|
| **Virtual machine data** | Disks (persistent block storage for IaaS VMs) and files. Number of data disks depends on VM size. | Azure managed disks — used for database files, static website content, custom app code. |
| **Unstructured data** | Least organized; *nonrelational* format. | **Azure Blob Storage** (highly scalable REST-based object store) and **Azure Data Lake Storage** (HDFS as a service). |
| **Structured data** | Relational format with a shared schema — rows, columns, keys. | **Azure Table Storage** (autoscaling NoSQL), **Azure Cosmos DB** (globally distributed DB), **Azure SQL Database** (fully managed DBaaS). |

### Key Features to Consider

- **Durability & availability** — redundancy protects against transient hardware failures; replicate across datacenters/regions for protection from local or regional disasters.
- **Secure access** — all data is encrypted; fine-grained access control.
- **Scalability** — designed to be massively scalable for modern application needs.
- **Manageability** — Microsoft handles hardware maintenance, updates, and critical issues.
- **Data accessibility** — accessible worldwide over HTTP/HTTPS; SDKs available for .NET, Java, Node.js, Python, PHP, Ruby, Go, plus REST API, PowerShell, and CLI. Azure portal and Storage Explorer provide visual management.
- **SFTP support** — Blob Storage can use SFTP if **hierarchical namespace (HNS)** is enabled (set at account creation under Advanced, or later under Settings → Configuration).
- **NFSv3 protocol support** — lets Linux clients mount a Blob container like an NFS share; simplifies migration from Linux file workloads.
- **Default authorization preferences** — enabling **"Default to Microsoft Entra authorization"** makes RBAC the default over shared access keys, improving security.

---

## 2. Explore Azure Storage Services

### Azure Blob Storage
Microsoft's object storage solution for the cloud — optimized for massive amounts of unstructured/nonrelational data (text or binary). Ideal for:
- Serving images/documents directly to a browser
- Storing files for distributed access
- Streaming video and audio
- Backup, restore, disaster recovery, and archiving
- Data analysis by on-premises or Azure-hosted services

Accessible worldwide via HTTP/HTTPS, URLs, REST API, PowerShell, CLI, or client libraries (.NET, Java, Node.js, Python, PHP, Ruby).

### Azure Files
Highly available network file shares accessible via **SMB** and **NFS** protocols. Multiple VMs can share the same files with read/write access; also accessible via REST or client libraries.

Common uses:
- Migrating on-premises apps that use file shares (minimal changes if mounted to the same drive letter)
- Storing configuration files, shared tools/utilities across multiple VMs
- Storing diagnostic logs, metrics, and crash dumps

> Storage account credentials authenticate access — all users with the share mounted get full read/write access.

### Azure Queue Storage
Stores and retrieves messages (up to **64 KB** each; a queue can hold millions of messages). Used for asynchronous processing — e.g., a customer uploads a picture, a message is queued, and an Azure Function retrieves it later to generate thumbnails. Each processing part scales independently.

### Azure Table Storage
Stores nonrelational structured data (NoSQL) — a key/attribute store with a schemaless design, making it easy to adapt data as application needs evolve. Fast and cost-effective compared to traditional SQL at similar volumes. A newer **Azure Cosmos DB Table API** offers throughput-optimized tables, global distribution, and automatic secondary indexes.

---

## 3. Determine Storage Account Types

General-purpose Azure storage accounts have two basic types: **Standard** and **Premium**.

- **Standard** — backed by HDDs; lowest cost per GB; good for bulk storage or infrequently accessed data.
- **Premium** — backed by SSDs; consistent low latency; good for VM disks and I/O-intensive apps like databases.

> You **cannot convert** a Standard account to Premium (or vice versa) — you must create a new account and migrate data. All account types are encrypted at rest via SSE.

| Storage Account | Supported Services | Redundancy Options | Recommended Usage |
|---|---|---|---|
| **Standard general-purpose v2** | Blob (incl. Data Lake Storage), Queue, Table, Azure Files | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS | Most scenarios — blobs, file shares, queues, tables, disks (page blobs). |
| **Premium block blobs** | Blob Storage (incl. Data Lake Storage) | LRS, ZRS | High transaction rates, smaller objects, consistently low latency. Scales with your app. |
| **Premium file shares** | Azure Files | LRS, ZRS | Enterprise/high-performance apps needing both SMB and NFS support. |
| **Premium page blobs** | Page blobs only | LRS only | Index-based/sparse data structures — OS disks, VM data disks, databases. |

> Legacy account types (GPv1, legacy BlobStorage) still exist on some subscriptions — Microsoft recommends upgrading to GPv2 (supported in-place via portal, CLI, or PowerShell).

---

## 4. Determine Replication Strategies

Data in a storage account is always replicated for durability and high availability, protecting against transient hardware failures, network/power outages, and natural disasters — while helping meet the Azure Storage SLA.

| Strategy | Description |
|---|---|
| **Locally Redundant Storage (LRS)** | Lowest cost, least durable — replicates within a single datacenter. All replicas could be lost in a datacenter-level disaster. Suitable for easily reconstructible data, constantly changing/non-essential data (e.g. live feeds), or where governance restricts replication to one location. |
| **Zone Redundant Storage (ZRS)** | Synchronously replicates across **three storage clusters** in separate availability zones within a single region. Each zone is autonomous with separate utilities/networking. Provides access even if a zone is unavailable. Not available in all regions; migrating to ZRS requires physical data movement. |
| **Geo-Redundant Storage (GRS)** | Replicates to a secondary region hundreds of miles away — **16 9's durability**. Data is first replicated locally (LRS) in the primary region, then asynchronously to the secondary region (also as LRS there). **GRS**: secondary data is readable only after a Microsoft-initiated failover. **RA-GRS**: adds the ability to read from the secondary region at any time, regardless of failover. |
| **Geo-Zone-Redundant Storage (GZRS)** | Combines ZRS (3 zones in primary region) with geo-replication to a secondary paired region — **16 9's durability**. Keeps working during a zone outage *and* protects against regional disaster. **RA-GZRS** adds optional read access to the secondary region. Recommended for apps needing consistency, durability, high availability, and disaster resilience. |

### Durability/Availability Comparison

| Node unavailable | Entire datacenter unavailable | Region-wide outage | Read access during region-wide outage |
|---|---|---|---|
| LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS | ZRS, GRS, RA-GRS, GZRS, RA-GZRS | GRS, RA-GRS, GZRS, RA-GZRS | RA-GRS, RA-GZRS |

---

## 5. Access Storage

Every object in Azure Storage has a unique URL. The **storage account name** forms the subdomain; combined with the service-specific domain, this forms the account's endpoint.

Example — account named `mystorageaccount`:

| Service | Default Endpoint |
|---|---|
| **Container (Blob) service** | `mystorageaccount.blob.core.windows.net` |
| **Table service** | `mystorageaccount.table.core.windows.net` |
| **Queue service** | `mystorageaccount.queue.core.windows.net` |
| **File service** | `mystorageaccount.file.core.windows.net` |

Object URL example — blob `myblob` in container `mycontainer`:
```
https://mystorageaccount.blob.core.windows.net/mycontainer/myblob
```

### Custom Domains
You can map a custom domain (e.g., `www.contoso.com`) to the blob/web endpoint so users access blob data through your own domain.

- **Direct mapping** — create a `CNAME` DNS record pointing your subdomain to the storage account endpoint.
  - Example: subdomain `blobs.contoso.com` → `CNAME` → `contosoblobs.blob.core.windows.net`

---

## 6. Secure Storage Endpoints

In the Azure portal, use the **Firewalls and virtual networks** settings to configure service endpoints and restrict network access to specific subnets or public IPs.

- Service endpoints provide the base URL for any blob, queue, table, or file object.
- You can allow access to one or more public IP ranges.
- Subnets/virtual networks must be in the **same Azure region or region pair** as the storage account.
- Always test the endpoint to confirm it restricts access as expected.

### Private Endpoints
Azure Storage also supports **private endpoints** for enhanced security and network isolation — the recommended approach for **production workloads**.

- Assigns a **private IP address** from your VNet to the storage account.
- All traffic between the VNet and the storage service travels over the **Microsoft backbone network**, with no exposure to the public internet.

| | Service Endpoints | Private Endpoints |
|---|---|---|
| **Storage account address** | Stays on its public endpoint | Gets a private IP from your VNet |
| **Traffic path** | Restricted to specific VNets/subnets, some public internet exposure | Entirely within the Microsoft backbone |
| **Best for** | Development scenarios, simpler configuration | Production workloads needing full network isolation / compliance |

---

# PART 2: Configure Azure Blob Storage

## 1. Implement Azure Blob Storage

**Blob** = Binary Large Object. Azure Blob Storage stores unstructured data in the cloud as objects/blobs — also called *object storage* or *container storage*. Can store any type of text or binary data (documents, images, video, installers).

### Three Core Resources
1. **Azure storage account**
2. **Containers** in the storage account
3. **Blobs** within a container

### Configuration Settings
- Blob container options
- Blob types and upload options
- Blob Storage access tiers
- Blob lifecycle rules
- Blob object replication options

### Common Use Cases
- Serving images/documents directly to a browser
- Distributed access to files (e.g., during installs)
- Streaming video and audio
- Backup, restore, disaster recovery, archiving
- Data analysis by on-premises or Azure-hosted services

---

## 2. Create Blob Containers

A blob **cannot exist on its own** — it must live inside a container.

### Things to Know
- All blobs must be in a container.
- Containers organize your blob storage.
- A container can hold **unlimited blobs**.
- A storage account can hold **unlimited containers**.
- You must create a container before uploading data.

### Container Configuration

**Name rules:**
- Must be unique within the storage account.
- Lowercase letters, numbers, and hyphens only.
- Must start with a letter or number.
- Length: **3–63 characters**.

**Public access levels:**

| Level | Description |
|---|---|
| **Private** *(default)* | No anonymous access to the container or blobs. |
| **Blob** | Anonymous public **read** access to blobs only. |
| **Container** | Anonymous public **read and list** access to the entire container, including blobs. |

> Blob/Container access levels only take effect if the storage account's **"Allow Blob anonymous access"** setting is enabled. If disabled, all containers stay private regardless of their individual setting. Microsoft recommends keeping anonymous access disabled at the account level unless serving public content.

---

## 3. Assign Blob Access Tiers

Azure Storage supports four access tiers, each optimized for a usage pattern: **Hot, Cool, Cold, and Archive**.

| Tier | Optimized For | Notes |
|---|---|---|
| **Hot** | Frequent reads/writes | Highest storage cost, lowest access cost. Good for actively processed data. |
| **Cool** | Infrequently accessed data, kept ≥ **30 days** | Lower storage cost, higher access cost than Hot. Good for short-term backup/DR data, older media that must stay immediately available. |
| **Cold** | Infrequently accessed data, kept ≥ **90 days** | Lower storage cost, higher access cost than Cool. |
| **Archive** | Offline data tolerating hours of retrieval latency, kept ≥ **180 days** (early deletion charge otherwise) | Most cost-effective for storage; most expensive to access. Good for secondary backups, raw data, compliance records. |

### Rehydrating Archived Blobs
To access Archive-tier content, rehydrate it to Hot, Cool, or Cold using:
- **Copy Blob** *(recommended)* — creates a new blob in an online tier.
- **Set Blob Tier** — changes the tier in place.

Both support:
- **Standard priority** — up to 15 hours.
- **High priority** — within 1 hour for objects under 10 GB (higher cost) — useful for urgent DR retrieval.

### Tier Comparison

| Comparison | Hot | Cool | Cold | Archive |
|---|---|---|---|---|
| **Availability** | 99.9% | 99% | 99% | 99% |
| **Availability (RA-GRS reads)** | 99.99% | 99.9% | 99.9% | 99.9% |
| **Latency (time to first byte)** | milliseconds | milliseconds | milliseconds | hours |
| **Minimum storage duration** | N/A | 30 days | 90 days | 180 days |

---

## 4. Add Blob Lifecycle Management Rules

Every dataset has a lifecycle — heavily accessed early on, then progressively less as it ages. Azure Blob Storage supports rule-based **lifecycle management** for GPv2 and Premium block blob accounts (legacy accounts supported too, but GPv2 is recommended) to automatically transition data between tiers or expire it.

### What Lifecycle Rules Can Do
- Transition blobs to a cooler tier: Hot→Cool, Hot→Cold, Hot→Archive, Cool→Cold, Cool→Archive, Cold→Archive.
- Delete current versions, previous versions, or snapshots of a blob at end of life.
- Auto-transition blobs from **Cool back to Hot** when accessed — optimizes for unpredictable access patterns without early deletion charges.
- Apply rules to an entire storage account, select containers, or a subset of blobs (via name prefixes or blob index tags).

### Business Scenario Example
Data accessed frequently early on, occasionally after 2 weeks, and rarely after a month → Hot tier initially, Cool tier for occasional access, Archive tier once aged past a month. Lifecycle rules automate this transition.

### Configuring a Rule (If–Then Logic)

- **If** clause: sets the evaluation condition.
  - **More than (days ago)** — number of days since last access/modification to trigger the rule.
- **Then** clause: sets the resulting action.
  - **Move to cool storage**
  - **Move to cold storage**
  - **Move to archive storage**
  - **Delete the blob**

Well-designed rules based on data age let you minimize storage costs automatically.

---

## 5. Determine Blob Object Replication

**Object replication** asynchronously copies blobs in a container between storage accounts based on policy rules you configure — including blob content, metadata properties, and versions.

### Things to Know
- Requires **Blob versioning** enabled on **both** the source and destination accounts — this lets you access/recover earlier blob versions.
- Does **not** support blob snapshots — snapshots on the source aren't replicated.
- Supported when source and destination are in **Hot, Cool, or Cold** tiers — they can be in different tiers from each other.
- You configure a **replication policy** specifying the source and destination storage accounts.
- A policy includes one or more **rules**, each specifying a source container, destination container, and which blobs to replicate.

### Why Use Object Replication
- **Reduce latency** — let clients read from a region closer to them.
- **Improve compute efficiency** — process the same blob sets in different regions.
- **Optimize data distribution** — analyze data in one location, replicate only the results elsewhere.
- **Reduce costs** — move replicated data to the Archive tier via lifecycle management after replication.

---

## 6. Manage Blobs

A blob can be any file type/size. Azure Storage offers three blob types:

| Type | Description | Use Case |
|---|---|---|
| **Block blob** | Made of assembled data blocks. Default blob type. | Most scenarios — storing text/binary data like files, images, videos. |
| **Append blob** | Also block-based, but optimized for **append** operations. | Logging scenarios where data keeps growing. |
| **Page blob** | Up to **8 TB**; efficient for frequent read/write operations. | Used by Azure VMs for OS disks and data disks. |

> Once a blob is created, its type **cannot be changed**.

### Upload/Management Tools

- **Azure portal** — good for uploading/managing a small number of files; set blob type, block size, container folder, access tier, and encryption scope.
- **Azure Storage Explorer** — upload, download, and manage blobs, files, queues, tables, Data Lake Storage entities, and managed disks; view/edit resources, preview data, configure permissions.
- **AzCopy** — command-line tool (Windows/Linux) for copying data to/from Blob Storage, across containers and storage accounts.
- **Azure Data Box Disk** — for transferring large on-premises datasets to Blob Storage when uploading over the network isn't practical; request SSDs from Microsoft, copy data locally, ship back for upload.

---

## 7. Determine Blob Storage Pricing

Use the **Azure Pricing Calculator** to estimate migration, monthly, and future costs based on your workload.

### Cost Depends On
- Volume of data stored per month.
- Quantity/type of operations performed, plus data transfer costs.
- Data redundancy option selected.

### Billing Considerations

| Factor | Description |
|---|---|
| **Performance tiers** | Cooler tiers = lower per-GB storage cost. |
| **Data access costs** | Increase as the tier gets cooler — Cool, Cold, and Archive incur a per-GB charge for read actions. |
| **Transaction costs** | Per-transaction charge on all tiers, increasing as the tier gets cooler. |
| **Geo-replication data transfer costs** | Applies only to accounts with geo-replication configured — per-GB charge. |
| **Outbound data transfer costs** | Billed per GB for bandwidth usage — consistent with standard storage accounts. |
| **Changing storage tier** | Cool → Hot: charged as if reading all existing data. Hot → Cool: charged as if writing all data into Cool (GPv2 accounts only). |

---
