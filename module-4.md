# AZ-104: Configure Virtual Networks & Network Security Groups

> Modules: *Configure virtual networks* + *Configure network security groups*

---

## 1. Configure Virtual Networks

### 1.1 What is an Azure Virtual Network (VNet)?

- A **logical isolation** of Azure cloud resources — your own private network in Azure.
- Used to provision and manage **VPNs** in Azure.
- Each VNet has its own **CIDR block** (address space).
- Can be linked to other VNets and on-premises networks — **as long as CIDR blocks don't overlap**.
- You control **DNS settings** and **subnetting** for the VNet.

**Use cases:**

| Scenario | Description |
|---|---|
| Private cloud-only VNet | Resources communicate directly & securely within the cloud, no cross-premises needed |
| Extend datacenter | Site-to-site VPN using IPSEC between on-prem VPN gateway and Azure |
| Hybrid cloud | Connect cloud apps to on-prem systems (mainframes, Unix, etc.) |

---

### 1.2 Subnets

- Used to **segment** a VNet → better security, performance, and manageability.
- Each subnet = a range of IPs **within** the VNet address space.
- Subnet address range must be **unique** and **cannot overlap** with other subnets in the same VNet.
- Address space defined using **CIDR notation**.
- Each subnet can have **0 or 1** associated Network Security Group.

#### 🔑 Reserved IP addresses per subnet (Azure reserves 5)

For subnet `192.168.1.0/24`:

| Address | Reason |
|---|---|
| `192.168.1.0` | Network address |
| `192.168.1.1` | Default gateway |
| `192.168.1.2`, `192.168.1.3` | Mapped to Azure DNS |
| `192.168.1.255` | Broadcast address |

> ⚠️ **Exam tip**: A `/24` subnet has 256 addresses, but only 251 are usable due to these 5 reserved addresses.

#### Things to consider with subnets
- **Service requirements** – some services need a dedicated subnet (e.g., VPN Gateway needs `GatewaySubnet`).
- **Network virtual appliances (NVAs)** – Azure routes traffic between subnets by default; you can override this via **UDRs (User Defined Routes)** to force traffic through an NVA.
- **NSGs** – 0 or 1 NSG per subnet.
- **Private Link** – enables private connectivity from VNet to Azure PaaS/partner/customer services, avoiding the public internet.

---

### 1.3 Creating Virtual Networks

- Must define an **IP address space** when creating a VNet.
- Use an address space **not already in use** elsewhere in your org.
- Address space can be **on-premises OR cloud** — not both.
- Must define **at least one subnet** when creating a VNet.
- Created in Azure portal by specifying: **Subscription, Resource Group, Name, Region**.

---

### 1.4 IP Addressing

Two types of IP addresses in Azure:

| Type | Purpose |
|---|---|
| **Private IP** | Communication within a VNet and with on-prem network (via VPN Gateway/ExpressRoute) |
| **Public IP** | Communication with the internet |

**Static vs Dynamic:**
- IPs can be **statically** or **dynamically** assigned.
- Use **static** IPs for:
  - DNS name resolution
  - IP-based security models
  - TLS/SSL certs tied to an IP
  - Firewall rules based on IP ranges
  - Role-based VMs (Domain Controllers, DNS servers)

---

### 1.5 Public IP Addresses

Configurable settings:

| Setting | Details |
|---|---|
| **IP Version** | IPv4 / IPv6 — both charged the same rate |
| **SKU** | Must **match** the SKU of the Load Balancer it's used with |
| **Tier** | Must match LB tier — *Global* (cross-region) vs *Regional* |
| **Assignment** | Static IPs are assigned at creation and held until the resource is deleted |

**Association table:**

| Top-level resource | IP configured at |
|---|---|
| Virtual machine | Network interface configuration |
| VPN Gateway / ER Gateway / NAT Gateway | Gateway IP configuration |
| Public Load Balancer, App Gateway, Azure Firewall, Route Server, API Management | Front-end configuration |
| Bastion host | Public IP configuration |

**Standard SKU Public IP:**
- Allocation: **Static only**
- **Secure by default** (must explicitly allow traffic via NSG)
- Supports **zone-redundant**, zonal, or non-zonal deployment

---

### 1.6 Private IP Addresses

| Resource | Associated at | Dynamic | Static |
|---|---|---|---|
| Virtual machine | NIC | ✅ | ✅ |
| Internal load balancer | Front-end config | ✅ | ✅ |
| Application gateway | Front-end config | ✅ | ✅ |

- **Dynamic (default)**: Azure assigns the **next available** IP in the subnet range.
  - e.g., if `.4`–`.9` are taken → next resource gets `.10`
- **Static**: You manually pick any unassigned/unreserved IP in the subnet's range.

---

## 2. Configure Network Security Groups (NSGs)

### 2.1 What is an NSG?

- Used to **filter/limit network traffic** to resources in a VNet.
- Contains a list of **security rules** that **allow/deny inbound or outbound** traffic.
- Can be associated with:
  - A **subnet**
  - A **network interface (NIC)**
  - **Both at once** (evaluated independently)
- An NSG can be associated **multiple times** (to multiple subnets/NICs).

**Limits:**
- Each **subnet**: max **1** associated NSG.
- Each **NIC**: **0 or 1** associated NSG.

> 💡 NSGs on a subnet can create a **DMZ (screened subnet)** — a buffer zone between your VNet and the internet.

---

### 2.2 NSG Security Rules

Azure creates **default rules** automatically in every NSG (cannot be deleted, but **can be overridden** with a higher-priority custom rule).

#### Rule properties you can configure:

| Setting | Value options |
|---|---|
| **Source** | Any, IP Addresses, My IP address, Service Tag, Application Security Group |
| **Source port ranges** | Ports the rule applies to |
| **Destination** | Any, IP Addresses, Service Tag, Application Security Group |
| **Protocol** | TCP, UDP, ICMP, ESP, AH (ESP/AH only via JSON templates/PowerShell), or Any (default) |
| **Action** | Allow / Deny |
| **Priority** | 100 – 4096, unique per NSG. **Lower number = higher priority** |

> ⚠️ **Exam tip**: Lower priority number = processed first = higher precedence.
> Best practice: leave gaps (100, 200, 300...) so you can insert rules later without renumbering.

#### Default Inbound Rules
- **Deny all inbound traffic**, EXCEPT:
  - Traffic from within the **VNet**
  - Traffic from **Azure Load Balancers**

#### Default Outbound Rules
- **Allow outbound traffic** only to:
  - The **internet**
  - The **VNet**

Common default rule names: `AllowVnetInBound`, `AllowAzureLoadBalancerInBound`, `DenyAllInBound`, `AllowVnetOutBound`, `AllowInternetOutBound`, `DenyAllOutBound`.

---

### 2.3 Effective Security Rules (Evaluation Order)

- Each NSG + its rules is evaluated **independently**.
- **Inbound traffic**: Azure processes **subnet NSG rules first**, then **NIC NSG rules**.
- **Outbound traffic**: reversed — **NIC NSG rules first**, then **subnet NSG rules**.
- Azure also evaluates rules for **intra-subnet traffic** (VMs talking to each other within the same subnet).

#### Key considerations:
- **No NSG associated** → all traffic allowed per Azure's default rules (no restriction at that level).
- **Must define an Allow rule** at every level (subnet AND/OR NIC) where you want traffic to pass — otherwise it's denied.
- **Intra-subnet traffic** can be blocked by explicitly denying it in the NSG.
- **Rule priority** — always assign the lowest possible number to critical rules; leave gaps for future rules.

#### Viewing effective rules
- Use **"Effective security rules"** in the Azure portal (under a VM/NIC's Networking blade) to see which rules actually apply.
- **Network Watcher** gives a consolidated view of NSG rules + Azure Virtual Network Manager security admin rules.
  - **IP flow verify** feature checks traffic against both NSG rules and security admin rules.

---

### 2.4 Creating NSG Rules (Portal)

| Field | Purpose |
|---|---|
| **Source** | Controls **inbound** traffic — Any / IP range / ASG / default tag |
| **Destination** | Controls **outbound** traffic — Any / IP range / ASG / default tag |
| **Service** | Destination protocol + port (predefined like RDP/SSH, or custom) |
| **Priority** | Determines processing order (lower = higher priority) |

#### Augmented Security Rules
- A **single rule** can include **multiple** IP addresses, port ranges, and mix Service Tags + ASGs + IPs together.
- Benefit: **reduces total rule count**, simplifies management, prevents "NSG rule sprawl."
- Example: instead of 4 rules for ports 80, 443, 8080, 8090 → **1 rule** covering all four.

---

### 2.5 Application Security Groups (ASGs)

- Let you **logically group VMs by workload/application role** (e.g., "WebServers", "AppServers") instead of by IP or subnet.
- You assign VM **NICs** to an ASG, then reference the ASG as **source/destination** in NSG rules.

#### Example scenario: 2-tier app (web + app servers)

| Rule | Priority | Action |
|---|---|---|
| Rule 1 | 100 | **Allow** internet → Web ASG on ports 80 (HTTP) & 443 (HTTPS) |
| Rule 2 | 110 | **Allow** Web ASG → App ASG on port 1433 (SQL) |
| Rule 3 | 120 | **Deny** anywhere → App ASG on ports 80/443 |

→ Result: only web servers can reach the database tier; app servers are shielded from direct internet access.

#### Benefits of ASGs
- **No need to track individual IPs** — easier as VM count changes.
- **No need to align with subnets** — group by purpose, not network layout.
- **Simplifies rule management** — one rule set applies to all VMs in the ASG; new VMs added to the ASG automatically inherit the rules.
- Good for **workload-based** organization (clear, maintainable design).
- **ASG vs Service Tag**: Service Tags = predefined groups of IP prefixes for **Azure services**; ASGs = **your own VM groupings** for security policy.

---

## 🎯 Quick Revision — Key Exam Facts

- 5 reserved IPs per subnet (network, gateway, 2x DNS, broadcast).
- NSG: **1 max per subnet**, **0–1 per NIC**.
- NSG priority: **100–4096**, lower number = higher priority.
- Default NSG rules: **deny all inbound** (except VNet + Load Balancer); **allow outbound** to VNet + internet.
- Inbound evaluation order: **Subnet NSG → NIC NSG**.
- Outbound evaluation order: **NIC NSG → Subnet NSG**.
- Default rules **cannot be deleted**, only **overridden** with higher priority custom rules.
- Standard SKU Public IP = **static only**, secure by default.
- Public IP SKU **must match** Load Balancer SKU.
- Private IP dynamic = next available address; static = manually chosen from subnet range.
- Augmented rules = combine multiple IPs/ports/ASGs into one rule.
- ASGs group VMs by role, not by subnet/IP — simplifies NSG rule management.

---
*Source: Microsoft Learn — [Configure virtual networks](https://learn.microsoft.com/en-us/training/modules/configure-virtual-networks/), [Configure network security groups](https://learn.microsoft.com/en-us/training/modules/configure-network-security-groups/)*
