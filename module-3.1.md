# Azure Storage & Azure Files — Notes

---

## 1. Configure Azure Storage Security

Azure Storage provides a comprehensive set of security capabilities that work together to enable developers to build secure applications.

### Azure Storage Security Strategies

Common approaches include **encryption, authentication, authorization, and user access control** with credentials, file permissions, and private signatures.

| Strategy | Details |
|---|---|
| **Encryption at rest** | Storage Service Encryption (SSE) with a **256-bit AES** cipher encrypts all data written to Azure Storage. Data is automatically decrypted when read. No extra charge, no performance hit. Includes encrypting VHDs via **Azure Disk Encryption** — BitLocker for Windows, dm-crypt for Linux. |
| **Encryption in transit** | Enable **"Secure transfer required"** to only accept requests over HTTPS. Existing accounts should explicitly disallow **TLS 1.0 and 1.1** (deprecated). |
| **Encryption models** | Server-side encryption with service-managed keys, customer-managed keys (Key Vault), or customer-managed keys on customer-controlled hardware. Client-side encryption lets you manage/store keys on-premises or elsewhere. |
| **Authorize requests** | Microsoft recommends using **Microsoft Entra ID with managed identities** to authorize requests against blob, queue, and table data — more secure and easier than Shared Key authorization. |
| **RBAC** | Ensures storage account resources are accessible only to the users/apps you grant access to. Assign RBAC roles scoped to the storage account. |
| **Storage analytics** | Performs logging for a storage account — used to trace requests, analyze usage trends, and diagnose issues. |

### Authorization Strategies

| Strategy | Description |
|---|---|
| **Microsoft Entra ID** | Cloud-based identity and access management service. Assign fine-grained access to users, groups, or applications via RBAC. |
| **Shared Key** | Access authorized using the primary or secondary account access key. Disable Shared Key at the account level to enforce Entra ID authorization. |
| **Shared Access Signatures (SAS)** | Delegates access to a specific resource with specified permissions and a time interval. |
| **Anonymous access** | Disabled by default on new storage accounts. Recommended to keep disabled for accounts with sensitive data. |

---

## 2. Recommendations for Managing SAS Risks

| Recommendation | Description |
|---|---|
| **Always use HTTPS** | HTTP-transmitted SAS tokens can be intercepted (man-in-the-middle), compromising data or allowing corruption. |
| **Reference stored access policies** | Lets you revoke permissions without regenerating account keys. Set the account key expiration far in the future. |
| **Set near-term expiry for unplanned SAS** | Limits damage if compromised; also limits how much data can be uploaded in the available window. |
| **Require clients to auto-renew SAS** | Renew well before expiration to allow retries if the SAS-issuing service is temporarily unavailable. |
| **Plan the SAS start time carefully** | Due to clock skew, set start time ~15 minutes in the past (or omit it entirely for immediate validity). Same applies to expiry — expect up to 15 minutes of skew either way. REST API versions before 2012-02-12 cap SAS duration at 1 hour. |
| **Define minimum access permissions** | Grant only what's needed (e.g., read-only to a single entity) to limit blast radius if compromised. |
| **Validate data written via SAS** | Validate/authorize data after it's written but before it's used, to guard against corrupt or malicious writes. |
| **Don't assume SAS is always correct** | For higher-risk operations, use a middle-tier service with business rule validation, authentication, and auditing. For public read scenarios, consider making the container Public instead. |

---

## 3. SAS URI & Parameters

Example URI:
```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=...
```

| Parameter | Example | Description |
|---|---|---|
| **Resource URI** | `.../?restype=service&comp=properties` | Azure Storage endpoint + parameters defining the operation scope (e.g., service-level GET/SET). |
| **Storage version** | `sv=2015-04-05` | REST API version to use (for version 2012-02-12+). |
| **Storage service** | `ss=bf` | Which service(s) the SAS applies to (Blob, File, etc.). |
| **Start time** | `st=2015-04-29T22:18:26Z` | *(Optional)* SAS start time in UTC. Omit for immediate validity. |
| **Expiry time** | `se=2015-04-30T02:23:26Z` | SAS expiration time in UTC. |
| **Resource** | `sr=b` | Which resource type is accessible (e.g., `b` = blob). |
| **Permissions** | `sp=rw` | Permissions granted (e.g., read + write). |
| **IP range** | `sip=168.1.5.60-168.1.5.70` | Restricts requests to a specific IP range. |
| **Protocol** | `spr=https` | Restricts to HTTPS-only requests. |
| **Signature** | `sig=...` | HMAC-SHA256 signature, Base64-encoded, authenticating the SAS. |

---

## 4. Azure Storage Encryption

Azure Storage encryption for data at rest protects data automatically — **no code or application changes needed.**

- When a storage account is created, Azure generates **two 512-bit storage account access keys**.
- These keys authorize access via **Shared Key** authorization or via **SAS tokens** signed with the shared key.
- **Best practice:** store keys in **Azure Key Vault** and rotate them regularly.

### Encryption Configuration Options

| Option | Description |
|---|---|
| **Infrastructure encryption** | Can be enabled account-wide or per encryption scope. Data is encrypted **twice** — once at the service level, once at the infrastructure level — using two different algorithms and keys. |
| **Platform-managed keys (PMK)** | Generated, stored, and managed entirely by Azure. Default for Azure Data Encryption-at-Rest. |
| **Customer-managed keys (CMK)** | Created/read/updated/deleted by the customer, stored in a customer-owned Key Vault or HSM. **Bring Your Own Key (BYOK)** is a CMK scenario where keys are imported from an external location. |

---

## 5. Configure Azure Files

Azure Files offers **fully managed file shares in the cloud**, accessible via **SMB**, **NFS**, and **HTTP** protocols, from Windows, Linux, and macOS devices.

### Key Characteristics

| Feature | Details |
|---|---|
| **Serverless deployment** | Fully managed PaaS — no VMs, OS, or updates to manage. |
| **Almost unlimited storage** | A share can hold up to **100 TiB**; a single file can be up to **4 TiB**. Organized hierarchically like on-prem file servers. |
| **Data encryption** | Encrypted at rest in the Azure datacenter and in transit over the network. |
| **Access from anywhere** | Accessible with internet connectivity by default. |
| **Environment integration** | Access control via Microsoft Entra ID or AD DS identities synced to Entra ID — mirrors an on-prem file server experience. |
| **Previous versions & backups** | Snapshots integrate with File Explorer's "Previous Versions"; also supports Azure Backup. |
| **Data redundancy** | Replicates per the storage account's redundancy setting (LRS, ZRS, GRS, etc.). |

### Common Scenarios for Azure Files

- **Replace/supplement** traditional on-prem file servers or NAS devices.
- **Global access** from Windows, macOS, and Linux, anywhere in the world.
- **Lift and shift** apps that expect a traditional file share.
- **Azure File Sync** — replicate shares to Windows Servers for local performance/caching (see Section 9).
- **Shared application settings** — store config files centrally.
- **Diagnostic data** — store logs, metrics, and crash dumps in a shared location.
- **Tools and utilities** needed for developing/administering Azure VMs or cloud services.

### Azure Files vs. Azure Blob Storage

| | **Azure Files (File Shares)** | **Azure Blob Storage (Blobs)** |
|---|---|---|
| Protocols | SMB, NFS, client libraries, REST | Client libraries, REST |
| Structure | True directory objects | Flat namespace |
| Access | File shares across multiple VMs | Accessed through a container |
| Best for | Lift-and-shift apps using native file system APIs; sharing dev/debug tools across VMs | Massive-scale unstructured data via block blobs |

### Types of Azure File Shares

Azure Files supports two protocols — **SMB** and **NFS** — but a single file share cannot support both at once (though a storage account can host both SMB and NFS shares).

| Storage Tier | Performance | Storage Account Type | Redundancy Options | Billing Model | Use Cases |
|---|---|---|---|---|---|
| **Premium** | SSD-backed, consistent low latency | FileStorage | LRS, ZRS | Provisioned (pay for reserved capacity) | High-performance, low-latency workloads |
| **Transaction Optimized** | HDD-backed, standard | General-purpose v2 (GPv2) | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS | Pay-as-you-go | High-transaction, frequently accessed data |
| **Hot** | HDD-backed, standard | GPv2 | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS | Pay-as-you-go | General-purpose team shares, collaboration |
| **Cool** | HDD-backed, standard | GPv2 | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS | Pay-as-you-go | Cost-efficient archive/backup |

> **Note:** Transaction Optimized, Hot, and Cool are all Standard (HDD) tiers with different pricing for different access patterns. Premium uses SSD with provisioned billing.

### Types of Authentication

| Method | Description |
|---|---|
| **Identity-based authentication over SMB** | Supports three AD sources: **on-premises AD DS**, **Microsoft Entra Domain Services**, and **Microsoft Entra Kerberos**. Assign Azure RBAC roles to users once the AD source is selected. |
| **Access key** | Older, less flexible. Two static account keys grant **full control** — bypass all access restrictions, so secure them and avoid sharing. Prefer identity-based auth. |
| **Shared Access Signature (SAS) token** | Dynamically generated URI based on the account key, with restricted permissions, time window, IP range, and protocol. For Azure Files, used only for **REST API access from code**. |

### Creating SMB Azure File Shares (Classic)

- Classic file shares live inside a storage account and inherit its limits.
- Choose between **SSD (Premium)** — fast, consistent, single-digit ms latency — and **HDD (Standard)** — more budget-friendly, general purpose.
- SMB shares must be created inside a storage account and can use the **Transaction Optimized, Hot, or Cool** access tiers.
- SMB traffic uses **port 445** — many ISPs block this outbound, the most common cause of connectivity issues when mounting shares from on-premises.
- **File shares (preview)** are now generally available **without requiring a storage account**, simplifying management for file-share-only scenarios.

---

## 6. Manage Azure File Shares

Covers creating and configuring SMB/NFS file shares and choosing the right performance tier and authentication method for your workload (see Section 5 above for tier and authentication details, which apply directly to share management).

---

## 7. Create File Share Snapshots

Azure Files supports **share snapshots** — point-in-time, read-only copies of a file share that protect against accidental deletion and enable recovery from application errors.

### Things to Know

- Snapshots are **incremental**, read-only, point-in-time copies at the **share level**.
- Only captures changes since the last snapshot — reduces time and cost.
- Same experience for SMB and NFS shares across all Azure public regions.
- Each snapshot adds a unique timestamp to the share URI.
- Uses the share's existing redundancy settings.
- Up to **200 snapshots per file share** for low-RPO recovery points.
- Snapshots persist until explicitly deleted; **deleting the share deletes all its snapshots**.
- **Azure Backup** can lease snapshots to help prevent accidental deletion.
- You can restore a single file, a folder, or the full share — a full restore only requires the latest snapshot.

### Why Use Snapshots

| Benefit | Description |
|---|---|
| **Protect against application error/corruption** | Roll back to a known-good point in time if a bad deployment or bug corrupts data. Good practice: snapshot before releasing new code. |
| **Protect against accidental deletion/changes** | Quickly restore an earlier version if a file is changed unexpectedly. |
| **Support backup and recovery** | Scheduled snapshots build a backup history — useful for audits and recovering from mistakes or outages. |

> PowerShell and Azure CLI support programmatic snapshot creation, including metadata, and can be scheduled via Azure Automation, GitHub Actions, or any CI system.

---

## 8. Implement Soft Delete for Azure Files

**Soft delete** lets you recover deleted files and file shares instead of losing them permanently.

### Things to Know

- Enabled at the **storage account level**.
- Deleted content transitions to a **soft-deleted state** rather than being permanently erased.
- You configure a **retention period** — how long soft-deleted shares are recoverable.
- Retention period range: **1 to 365 days**.
- Can be enabled on **new or existing** file shares.

### When to Use It

- **Recover from accidental data loss** — restore deleted or corrupted data.
- **Upgrade scenarios** — roll back to a known good state after a failed upgrade.
- **Ransomware protection** — recover data without paying a ransom.
- **Long-term retention** — meet data retention/compliance requirements.
- **Business continuity** — keep critical workloads highly available.

---

## 9. Use Azure Storage Explorer

**Azure Storage Explorer** is a standalone app (Windows, macOS, Linux) for working with Azure Storage data — access multiple accounts/subscriptions and manage all storage content from one place.

### Things to Know

- Requires both **management (Azure Resource Manager)** and **data layer** permissions — you need Microsoft Entra ID permissions for the account, its containers, and the data within.
- You can connect to:
  - Storage accounts associated with your own Azure subscriptions.
  - Storage accounts/services **shared from other subscriptions**.
  - **Local storage** via the Azure Storage Emulator.

### Common Scenarios

| Scenario | Description |
|---|---|
| **Connect to an Azure subscription** | Manage storage resources belonging to your own subscription. |
| **Work with local development storage** | Manage local storage via the Azure Storage Emulator. |
| **Attach to external storage** | Manage another subscription's storage (or storage under national clouds) using account name, key, and endpoints. |
| **Attach a storage account with a SAS** | Manage another subscription's storage resources using a SAS. |
| **Attach a service with a SAS** | Manage one specific service (blob, queue, or table) belonging to another subscription via a SAS. |

### Attach to External Storage Account

- Requires the external storage's **Account name** and **Account key** (called **key1** in the Azure portal).
- To connect using an account name/key from a **national Azure cloud**, select **Other** under **Storage endpoints domain** and enter the custom endpoint.

### Access Keys

- Access keys grant access to the **entire storage account**.
- Two keys are provided so you can rotate one while the other maintains active connections.
- **Best practice:** store keys securely and regenerate them regularly.
- After regenerating a key, update all resources/applications using it — this does **not** interrupt VM disk access.

---

## 10. Consider Azure File Sync

**Azure File Sync** caches Azure file shares on an **on-premises Windows Server or cloud VM**, centralizing file shares in Azure Files while retaining the flexibility, performance, and compatibility of a local file server.

### Core Components

| Component | Description |
|---|---|
| **Storage Sync Service** | The primary Azure resource managing synchronization. Supports up to **100 sync groups**, operates within a single Azure region, and allows up to **99 registered Windows Servers**. |
| **Sync group** | Defines the sync topology — **one cloud endpoint** + up to **50 server endpoints**. |
| **Cloud endpoint** | An Azure file share participating in the sync group — only **one per sync group**. |
| **Server endpoint** | A path on a registered Windows Server that syncs with the cloud endpoint. Must be an **NTFS-formatted volume**; cannot be a system volume; cloud tiering isn't supported on it. |
| **Azure File Sync Agent** | Installed on each Windows Server; runs as a background Windows service handling sync operations and management tasks. |

### Things to Know

- Turns a Windows Server into a **fast local cache** of your Azure file shares.
- Supports any protocol available on Windows Server for local access — **SMB, NFS, and FTPS**.
- Supports as many caches (servers) as needed, worldwide.
- Limits: **100 sync groups** per Storage Sync Service, **50 server endpoints** per sync group.

### When to Use Azure File Sync

- **Application lift and shift** — provide write access to the same data across both Windows Servers and Azure Files.
- **Branch office support** — set up new servers that connect to Azure storage for backup needs.
- **Backup and disaster recovery** — Azure Backup protects on-prem data once File Sync is set up; restore file metadata instantly and recall data as needed for fast DR.
- **File archiving with cloud tiering** — keep only recently accessed data locally; older data automatically moves to Azure Files.

---
