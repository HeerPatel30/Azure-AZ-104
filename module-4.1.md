# AZ-104: Host Domain on Azure DNS & Configure VNet Peering

> Modules: *Host your domain on Azure DNS* + *Configure Azure Virtual Network peering*

---

## 1. Host Your Domain on Azure DNS

### 1.1 DNS Fundamentals

- **DNS (Domain Name System)**: protocol within TCP/IP that translates human-readable domain names (e.g., `www.wideworldimports.com`) into IP addresses.
- A DNS server = **DNS name server / name server**.

**Core DNS server functions:**
1. **Caching** – keeps recently resolved names for faster lookups; if not found, forwards the request to another DNS server (repeats until match or timeout).
2. **Authority** – maintains the key-value database of IPs for the domains/hosts it's authoritative over.

**Domain lookup process:**
1. Check local cache → resolve if found.
2. If not cached, query other DNS servers on the web → update cache when matched.
3. If still not found after reasonable attempts → **"domain cannot be found"** error.

### 1.2 IPv4 vs IPv6

| Type | Format | Example |
|---|---|---|
| IPv4 | 4 sets of numbers 0–255, dot-separated | `127.0.0.1` |
| IPv6 | 8 groups of hex numbers, colon-separated | `fe80:11a1:ac15:e9gf:e884:edb0:ddee:fea3` |

> IPv4 is most common today but running out of space with IoT growth; IPv6 is the eventual replacement.

### 1.3 DNS Record Types

| Record | Purpose |
|---|---|
| **A** | Most common record — maps domain/host name → **IPv4** address |
| **AAAA** | Maps domain/host name → **IPv6** address |
| **CNAME** | Canonical Name — alias from one domain name to another |
| **MX** | Mail exchange — maps mail requests to a mail server |
| **TXT** | Text record — associates text strings with a domain; used by Azure/M365 for **domain ownership verification** |
| NS | Name server record |
| SOA | Start of Authority — reference point for the domain |
| SRV | Server location record |
| CAA | Certificate authority record |
| SPF | Sender policy framework |

> ⚠️ **Exam tip**: **SOA** and **NS** records are created **automatically** when you create a DNS zone in Azure DNS.

**Record sets**: allow multiple resources under a single record name (e.g., one A record name → multiple IPs). **SOA and CNAME records cannot contain record sets.**

```
www.wideworldimports.com.   3600   IN   A   127.0.0.1
www.wideworldimports.com.   3600   IN   A   127.0.0.2
```

---

### 1.4 What is Azure DNS?

- Hosts and manages domains using a **globally distributed name-server infrastructure**.
- Uses your existing **Azure credentials**, billing, and support.
- Azure DNS acts as the **SOA (start of authority)** for the domain.
- ❌ **Cannot register domain names** — you must use a **third-party registrar**; Azure DNS only **hosts** the DNS records.
- ❌ Does **not support DNSSEC** (DNS Security Extensions) — use a third-party provider if you need it.

#### Why use Azure DNS?
Built on **Azure Resource Manager**, offering:
- Improved security
- Ease of use
- Private DNS domains
- Alias record sets

#### Security features
- **RBAC** – fine-grained control over user access to DNS resources.
- **Activity logs** – track changes and pinpoint faults.
- **Resource locking** – restrict/remove access to resource groups, subscriptions, or resources.

#### Ease of use
- Manage via **Azure portal, PowerShell, CLI**, or **REST API/SDK** for automation.

---

### 1.5 Configuring a Public DNS Zone (steps)

1. **Create a DNS zone** in Azure — provide Subscription, Resource Group, Name (domain name), Region (defaults to RG location).
2. **Get Azure DNS name servers** — retrieved from the auto-created **NS record** (4 name servers).
3. **Update the domain registrar** — point the registrar's NS record to the 4 Azure DNS name servers. This is called **domain delegation**. *(Must use all 4 name servers provided.)*
4. **Verify delegation** — takes as little as ~10 minutes but can take longer. Verify using the **SOA record**, e.g.:
   ```
   nslookup -type=SOA wideworldimports.com
   ```
5. **Configure custom DNS records** — add **A** or **CNAME** records for web servers, load balancers, etc.

#### A record fields
- **Name** (e.g., `webserver1`)
- **Type**: A
- **TTL** (time-to-live, in seconds — how long it's cached before expiring)
- **IP address**

#### CNAME record fields
- **Name** (e.g., `www`)
- **TTL**
- **Record type**: CNAME
- Used when multiple domain names should resolve to the same site (e.g., `www.wideworldimports.com` → `wideworldimports.com`).

---

### 1.6 Configuring a Private DNS Zone

- **Not visible on the internet**; no domain registrar required.
- Used for **name resolution of VMs** within/between Azure VNets.

**Steps:**
1. **Create a private DNS zone** (e.g., `private.wideworldimports.com`) — needs Resource Group + zone name.
2. **Identify the virtual networks** needing name-resolution support.
3. **Link the VNet(s)** to the private zone via a **virtual network link** (Azure portal → private zone → *Virtual network links* → *Add*). One link per VNet needing support.

**Benefits of private zones:**
- No need to build a custom DNS solution — it's built into Azure infra.
- Supports all record types: A, CNAME, TXT, MX, SOA, AAAA, PTR, SRV.
- VM hostnames are **automatically maintained**.
- **Split-horizon DNS** — same domain name can exist in both private & public zones; resolves differently based on where the request originates.

---

### 1.7 Alias Records & the Zone Apex

- **Apex domain** (a.k.a. *zone apex* / *root apex*) = the domain's highest level (e.g., `wideworldimports.com`), represented by **`@`** in DNS zone records.
- The apex automatically has **NS** and **SOA** records.
- ❌ **CNAME records are NOT supported at the zone apex.**
- ✅ **Alias records ARE supported at the zone apex.**

#### What are alias records?
- Let a zone apex domain **reference other Azure resources** directly (no complex redirect policies needed).
- Can point to:
  - **Traffic Manager profile**
  - **Azure CDN endpoint**
  - **Public IP resource**
  - **Front Door profile**
- Supported in record types: **A, AAAA, CNAME**.

#### Benefits of alias records
- **Prevents dangling DNS records** — tightly couples DNS record lifecycle with the Azure resource lifecycle.
- **Auto-updates** the DNS record set when the underlying resource's IP changes.
- **Hosts load-balanced apps at the zone apex** (e.g., routes to Traffic Manager).
- **Points the zone apex to a CDN endpoint** directly.

> 💡 Example: Point `wideworldimports.com` (apex) directly to a **load balancer** using an alias record — if the load balancer's IP changes, the apex record keeps working automatically.

---

## 2. Configure Azure Virtual Network Peering

### 2.1 What is VNet Peering?

- Seamlessly **connects two Azure VNets** — after peering, they function as a **single network** for connectivity purposes.
- The two VNets **remain separately managed resources**.
- Can peer **across subscriptions and tenants**.

### 2.2 Types of Peering

| Type | Connects |
|---|---|
| **Regional peering** | VNets in the **same** region (same Azure public cloud region, China cloud region, or Gov cloud region) |
| **Global peering** | VNets in **different** regions (any Azure public cloud or China cloud regions) |

> ⚠️ **Exam tip**: **Global peering between different Azure Government cloud regions is NOT permitted.**

### 2.3 Benefits of VNet Peering

| Benefit | Description |
|---|---|
| **Private connections** | Traffic stays on the Microsoft Azure **backbone network** — no public internet, gateways, or encryption needed |
| **Strong performance** | Low-latency, high-bandwidth |
| **Simplified communication** | Resources in peered VNets communicate directly |
| **Seamless data transfer** | Across subscriptions, deployment models, and regions |
| **No resource disruption** | No downtime required to create or maintain peering |

### 2.4 Requirements & Limitations

| Constraint | Detail |
|---|---|
| **Non-overlapping address spaces** | Peering **fails** if VNets have overlapping IP ranges |
| **Address space changes** | Must **delete peering first**, update the address space, then re-create peering |
| **Basic Load Balancer** | Resources in one VNet **can't** reach **Basic Internal LB** IPs across regionally-peered VNets — use **Standard LB** for cross-region |
| **DNS resolution** | Azure's built-in name resolution **does NOT work across peered VNets** by default — need **Azure Private DNS zones** or custom DNS servers |

---

### 2.5 Gateway Transit & Connectivity

- Lets a peered VNet use another VNet's **VPN Gateway** as a **transit point** to reach external resources (on-prem, another VNet, clients).
- Example: Hub VNet has the VPN Gateway; Spoke VNet (peered with hub) uses the hub's gateway via **remote gateway transit** — no need to deploy its own gateway.

#### 4 key peering settings (Azure portal)

| Setting | Purpose |
|---|---|
| **Traffic to remote virtual network** | Allow/block traffic flowing *to* the remote VNet |
| **Traffic forwarded from remote virtual network** | Allow/block forwarded (non-originating) traffic from the peer |
| **Virtual network gateway or Route Server** | Enables **gateway transit** — lets the *peer* use *this* VNet's gateway/Route Server |
| **Remote virtual network gateway or Route Server** | Lets *this* VNet use the *peer's* gateway/Route Server |

#### Facts about VPN Gateway + peering
- A VNet can have **only one VPN Gateway**.
- **Gateway transit is supported** for both **regional** and **global** peering.
- With gateway transit enabled, the hub's gateway can:
  - Site-to-site VPN → on-premises
  - VNet-to-VNet connection
  - Point-to-site VPN → client
- **NSGs** can be applied to open/close access between peered VNets/subnets.

---

### 2.6 Creating VNet Peering

- Configurable via **PowerShell, Azure CLI, or Azure portal**.
- Requires the **`Network Contributor`** RBAC role (or a custom role with equivalent peering permissions).
- Requires **two VNets** — the second is called the **remote network**.
- Before peering: VMs in the two VNets **cannot communicate**. After peering + config: they can.

#### Peering status
| Status | Meaning |
|---|---|
| **Initiated** | Peering created from VNet A → VNet B, but not yet confirmed from B's side |
| **Connected** | Peering fully established (**both** VNets must show Connected) |

> ⚠️ Peering is **not successfully established** until **both sides show "Connected."**

---

### 2.7 Extending Peering: UDRs & Service Chaining

- **VNet peering is non-transitive**: if A↔B and B↔C are peered, **A cannot automatically reach C**.

| Mechanism | Description |
|---|---|
| **Hub-and-spoke network** | Hub VNet hosts shared infra (NVA, VPN Gateway); all spokes peer with the hub; traffic flows through the hub's NVA/gateway |
| **User-Defined Route (UDR)** | Peering allows the **next hop** of a UDR to be a VM's IP (or VPN gateway) in the **peered** VNet |
| **Service chaining** | Directs traffic from one VNet to a virtual appliance/gateway in another, via UDRs pointing to VMs or gateways in peered VNets as next hop |
| **Azure Virtual Network Manager** | Centrally manages hub-and-spoke or mesh peering topologies at scale; automates peering creation |

---

## 🎯 Quick Revision — Key Exam Facts

**Azure DNS:**
- Azure DNS **hosts** DNS zones — it does **NOT register domains** (use a 3rd-party registrar).
- **NS** and **SOA** records are auto-created with every DNS zone.
- **A** = IPv4, **AAAA** = IPv6, **CNAME** = alias, **MX** = mail, **TXT** = text/verification.
- **CNAME not allowed at zone apex** — use an **Alias record** instead.
- Alias records can point to: Traffic Manager, CDN endpoint, Public IP, Front Door.
- Private DNS zones need a **virtual network link** to resolve names for VMs in a VNet.
- Domain delegation = updating registrar's NS records to point to Azure DNS's 4 name servers.
- Azure DNS does **not support DNSSEC**.

**VNet Peering:**
- **Regional** = same region; **Global** = different regions.
- Global peering **not allowed** across different **Azure Government** regions.
- Peering traffic stays on Azure's **private backbone** — no public internet/gateway/encryption needed.
- **Non-overlapping address spaces** required; must delete & recreate peering to change address space.
- **Non-transitive** — peering doesn't chain automatically (A-B-C ≠ A-C).
- Only **one VPN Gateway per VNet**; **gateway transit** works for both regional & global peering.
- Peering status must be **"Connected"** on **both sides** to be active.
- Requires **Network Contributor** role.
- Use **hub-and-spoke + UDR/service chaining** or **Azure Virtual Network Manager** to extend connectivity beyond direct peers.

---
*Source: Microsoft Learn — [Host your domain on Azure DNS](https://learn.microsoft.com/en-us/training/modules/host-domain-azure-dns/), [Configure Azure Virtual Network peering](https://learn.microsoft.com/en-us/training/modules/configure-vnet-peering/)*
