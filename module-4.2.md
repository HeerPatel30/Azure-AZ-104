# AZ-104: Network Traffic Control & Monitoring — Revision Notes

Covers 4 Microsoft Learn modules:
1. Manage and control traffic flow with Routes (UDR + NVA)
2. Azure Load Balancer
3. Azure Application Gateway
4. Azure Network Watcher

---

## 1. Routing in Azure Virtual Networks

### 1.1 System Routes (Default Routes)
Azure automatically creates **system routes** and assigns them to every subnet. You can't create system routes yourself, but some can be overridden.

Default system routes for every subnet:

| Source | Address Prefix | Next Hop Type |
|---|---|---|
| Default | Unique to VNet (e.g., 10.0.0.0/16, includes all subnets) | Virtual network (`VNetLocal`) |
| Default | 0.0.0.0/0 | Internet |
| Default | 10.0.0.0/8 | None |
| Default | 172.16.0.0/12 | None |
| Default | 192.168.0.0/16 | None |
| Default | 100.64.0.0/10 | None |

- `None` as next hop = traffic is **dropped** (these RFC1918 ranges are reserved unless overridden by your own address space or a route).
- Additional system routes are added automatically when you: peer VNets, add a VPN Gateway/ExpressRoute Gateway, or enable a Service Endpoint.

### 1.2 Next Hop Types
When defining routes (system or custom), the **Next hop type** determines where traffic is sent:

| Next Hop Type | Description |
|---|---|
| **Virtual network gateway** | Route to a VPN or ExpressRoute gateway |
| **Virtual network (VNetLocal)** | Route within the VNet's address space |
| **Internet** | Route to the internet |
| **None** | Traffic is dropped |
| **Virtual appliance (NVA)** | Route to a Network Virtual Appliance — specify a private IP address as next hop (usually the NIC of a firewall/VM); can also be an Internal Load Balancer's private IP |
| **VirtualNetworkServiceEndpoint** | Route to a specific Azure service via service endpoint |

### 1.3 User-Defined Routes (UDR / Custom Routes)
- Created in a **Route Table** resource, then **associated with one or more subnets**.
- Used to override Azure's default system routing — e.g., force traffic through a firewall/NVA, or force traffic between subnets that Azure would otherwise send directly.
- A route table can contain multiple routes; each route has: **Address prefix** (destination, CIDR) + **Next hop type**.
- **Custom routes always take priority over default system routes** for the same/more specific prefix, but BGP routes learned from on-prem (via gateway) can override in certain cases (see route selection order below).
- One subnet → one route table (a route table can be linked to many subnets, but a subnet can only have one route table attached at a time).

### 1.4 Route Selection Order (most specific wins)
When multiple routes could apply, Azure picks based on **longest prefix match** first; if prefixes are equally specific, priority order is:
1. **User-defined route (custom route)**
2. **BGP route** (from VPN Gateway/ExpressRoute)
3. **System route**

> Exam tip: A more specific (longer prefix) route always wins regardless of type. Only when prefixes tie does the UDR > BGP > System priority apply.

### 1.5 Border Gateway Protocol (BGP)
- Used with **VPN Gateways** (route-based) and **ExpressRoute** to exchange routes between on-premises networks and Azure VNets dynamically.
- Requires an **Autonomous System Number (ASN)** — Azure default ASN is 65515 (can't be changed on VPN Gateway route-based; can differ with ExpressRoute).
- Allows automatic propagation of on-prem routes into Azure and vice versa without manual UDR configuration for every prefix.
- BGP routes have lower priority than UDRs, so you can still override with a custom route if needed.

### 1.6 Creating Custom Routes — Key Steps
1. Create a **Route Table** resource (choose region).
2. Add **Routes** to it (Address prefix + Next hop type + Next hop address if NVA).
3. **Associate** the Route Table with one or more **subnets**.
4. Optionally toggle **"Propagate gateway routes"** — Yes/No — controls whether BGP-learned routes from a gateway propagate into subnets using this route table.

### 1.7 Effective Routes
- Every NIC has a set of **Effective Routes** = combination of system routes + custom routes + BGP routes, resolved by priority.
- View via NIC → Networking → **Effective routes** (or via Network Watcher). Useful for troubleshooting unexpected traffic drops.

---

## 2. Network Virtual Appliances (NVA)

### 2.1 What is an NVA?
- A **VM** that performs a network function: firewall, WAN optimizer, application-delivery controller, router, proxy, etc.
- Typically deployed from Azure Marketplace (e.g., Barracuda, Cisco, Check Point, Fortinet) or Azure's own **Azure Firewall**.
- NVAs sit in a dedicated subnet and inspect/route traffic between subnets, or between the VNet and the internet/on-prem.

### 2.2 Why use an NVA + UDR together
- By default, Azure routes traffic directly between subnets and to the internet (system routes) — bypassing any security appliance.
- To force inspection, you create a UDR with **Next hop type = Virtual appliance**, pointing to the **private IP address of the NVA's NIC**, and associate that route table with the subnets whose outbound traffic should be inspected.

### 2.3 IP Forwarding Requirement
- The NVA's NIC must have **IP forwarding enabled** (`Enable IP forwarding` = On) in Azure — otherwise Azure drops packets not addressed to the NIC itself.
- Inside the guest OS, IP forwarding/routing must also be enabled (e.g., Linux: `net.ipv4.ip_forward=1`; Windows: enable Routing and Remote Access / RRAS).
- Both Azure-level AND OS-level forwarding must be enabled for the NVA to route traffic.

### 2.4 High Availability for NVAs
- A single NVA is a single point of failure. HA options:
  - Deploy NVAs in an **Availability Set** or across **Availability Zones** with an **Internal Load Balancer** in front, and set the route table's next hop to the **ILB's frontend private IP**.
  - Some vendor NVAs support active/passive clustering with their own failover mechanism.

### 2.5 Typical NVA Traffic Flow Scenario (hub-and-spoke / DMZ)
1. Traffic from Subnet A destined for Subnet B (or internet) is forced via UDR to the NVA subnet.
2. NVA inspects/filters traffic.
3. NVA forwards allowed traffic onward (its own routing/NAT).
4. Return traffic follows the reverse path (may need symmetric routing considerations).

---

## 3. Azure Load Balancer

### 3.1 What It Does
- Operates at **Layer 4 (TCP/UDP)** — distributes inbound/outbound flows among healthy backend instances (VMs, VMSS instances) based on a 5-tuple hash (source IP, source port, dest IP, dest port, protocol).
- Provides high availability and scale-out for applications; not application-aware (doesn't inspect HTTP headers, cookies, etc. — that's Application Gateway's job).

### 3.2 SKUs
| Feature | Basic (retiring) | Standard |
|---|---|---|
| Backend pool size | Up to 300 instances | Up to 1000 instances |
| Availability Zones | Not supported | Zone-redundant & zonal frontends supported |
| SLA | None | 99.99% |
| Security | Open by default | **Secure by default** — NSG required to allow traffic |
| HA Ports | No | Yes |
| Outbound rules | No | Yes (explicit outbound SNAT config) |
| Diagnostics | Basic | Multi-dimensional metrics via Azure Monitor |

> **Basic Load Balancer is being retired (Sept 2025)** — Standard is now the exam/real-world default; know Standard SKU features well.

### 3.3 Load Balancer Types
- **Public Load Balancer**: has a public IP frontend; load-balances internet traffic into your VNet; also provides outbound internet connectivity (SNAT) for backend instances.
- **Internal (Private) Load Balancer (ILB)**: frontend IP is private, only accessible within the VNet (or from peered VNets/on-prem via VPN/ExpressRoute) — used for internal tier-to-tier traffic (e.g., web → app tier), and as NVA next hop.

### 3.4 Core Components
| Component | Purpose |
|---|---|
| **Frontend IP configuration** | The IP address (public or private) clients connect to |
| **Backend pool** | Group of VMs/VMSS instances that receive traffic (defined by NIC, IP, or VMSS) |
| **Health probes** | TCP, HTTP, or HTTPS probes that check instance health; unhealthy instances are removed from rotation |
| **Load-balancing rules** | Map a frontend IP:port to a backend pool:port, with a protocol (TCP/UDP) and distribution mode |
| **Inbound NAT rules** | Forward traffic from a specific frontend port to a specific backend instance:port (e.g., RDP/SSH to individual VMs) |
| **Outbound rules** | Explicitly define SNAT behavior for outbound internet connectivity (Standard SKU only) |

### 3.5 Health Probes
- **TCP probe**: checks if a TCP connection can be established.
- **HTTP/HTTPS probe**: checks for a **200 OK** response from a specified path.
- Configurable: Interval, Unhealthy threshold (# consecutive failures before marked down).
- If a VM fails the probe, LB stops sending new flows to it (existing flows may continue until they end).

### 3.6 Distribution Modes
- **5-tuple hash (default)**: source IP, source port, dest IP, dest port, protocol → same source generally goes to same backend for the life of a flow.
- **Source IP affinity (2-tuple / 3-tuple, "session persistence")**: ensures requests from the same client IP (optionally + protocol) go to the same backend — useful for stateful apps without a shared session store.

### 3.7 HA Ports (Standard SKU, Internal LB only)
- A single load-balancing rule that load-balances **all ports, all protocols** simultaneously — commonly used for NVA scenarios (firewalls) needing to inspect all traffic types, and for high-availability NVA clusters.

### 3.8 Outbound Connectivity (SNAT)
- Standard LB: backend instances get **no implicit outbound internet access** unless: (a) they have a Public IP directly, or (b) explicit **outbound rules** on the Standard Public LB, or (c) via NAT Gateway.
- Basic LB: outbound SNAT is automatic/implicit (not configurable).
- SNAT port exhaustion is a real concern at scale — mitigate with outbound rules specifying more frontend IPs/ports, or use **Azure NAT Gateway**.

### 3.9 Load Balancer vs Application Gateway (quick contrast — commonly tested)
| | Load Balancer | Application Gateway |
|---|---|---|
| OSI Layer | Layer 4 (transport) | Layer 7 (application) |
| Traffic type | Any TCP/UDP | HTTP/HTTPS/WebSocket |
| Routing decisions | IP + port | URL path, host header, cookies |
| SSL offload | No | Yes |
| WAF | No | Yes (WAF SKU/tier) |
| Use case | General-purpose, cross-region, non-HTTP | Web app front-ending, path-based routing |

---

## 4. Azure Application Gateway

### 4.1 What It Does
- A **Layer 7 (application layer) load balancer** for web traffic (HTTP, HTTPS, HTTP/2, WebSocket).
- Makes routing decisions based on **URL path**, **host header**, or other HTTP attributes — not just IP/port.
- Deployed into its **own dedicated subnet** within a VNet (cannot share subnet with VMs, historically; v2 relaxed some restrictions but a dedicated subnet is still best practice/required).

### 4.2 Key Features
| Feature | Description |
|---|---|
| **URL path-based routing** | Route requests to different backend pools based on URL path (e.g., `/images/*` → image servers, `/api/*` → API servers) |
| **Multi-site hosting** | Host multiple websites/domains on the same App Gateway using host header-based routing |
| **SSL/TLS termination (offload)** | Decrypts HTTPS at the gateway, so backend servers handle plain HTTP — reduces backend CPU load |
| **End-to-end SSL** | Re-encrypts traffic from gateway to backend for full encryption in transit |
| **Web Application Firewall (WAF)** | Optional SKU/add-on — protects against OWASP Top 10 threats (SQL injection, XSS, etc.) |
| **Autoscaling** (v2 SKU) | Automatically scales instance count based on traffic |
| **Zone redundancy** (v2 SKU) | Can span Availability Zones |
| **Session affinity (cookie-based)** | Keeps a client's requests routed to the same backend server |
| **Connection draining** | Gracefully removes backend members from rotation, allowing in-flight requests to complete |
| **Custom health probes** | Similar concept to LB but HTTP-aware (checks status codes, response body match) |
| **Rewrite HTTP headers/URL** | Modify request/response headers or rewrite URLs |
| **Redirection** | e.g., HTTP → HTTPS redirect |

### 4.3 SKUs
| | v1 (Standard/WAF) | v2 (Standard_v2/WAF_v2) |
|---|---|---|
| Autoscaling | No | Yes |
| Zone redundancy | No | Yes |
| Static VIP | No | Yes |
| Header rewrite | No | Yes |
| Performance | Lower | Higher (up to 5x faster SSL offload) |
| Pricing model | Fixed instance-based | Consumption-based (capacity units) + fixed minimum |

> v1 is legacy; **v2 is the recommended/exam-current SKU**.

### 4.4 Core Components
| Component | Purpose |
|---|---|
| **Frontend IP** | Public and/or private IP that receives client traffic |
| **Listeners** | Checks for incoming requests on a protocol/port/hostname combo (Basic listener = single site; Multi-site listener = host-header based) |
| **Routing rules** | Bind a listener to a backend target (basic or path-based); associates HTTP settings |
| **HTTP settings** | Defines backend protocol/port, session affinity, connection draining, custom probe, request timeout |
| **Backend pools** | Target servers — can be NICs, VMSS, public IPs, FQDNs, or App Service |
| **Health probes** | HTTP-level checks against backend pool members |

### 4.5 WAF (Web Application Firewall)
- Available as a SKU tier (WAF_v2) or add-on.
- Based on **OWASP Core Rule Set (CRS)** — versions like CRS 3.x, 3.1, 3.2.
- Two modes:
  - **Detection mode**: logs threats, doesn't block.
  - **Prevention mode**: blocks/logs requests matching rules.
- Can create **custom rules** (allow/block based on conditions like IP, size, headers) in addition to the managed rule set.

### 4.6 Typical Use Case
- Front-end for a web application where you need SSL termination, path-based routing to microservices, and WAF protection — commonly paired with backend **VM Scale Sets** or **App Services**.

---

## 5. Azure Network Watcher

### 5.1 What It Does
- A regional service providing tools to **monitor, diagnose, and gain insight into** network performance and health in Azure — enabled automatically (or manually) per region.
- **Network Watcher itself is a container** for a set of diagnostic and monitoring tools; some tools work regardless of whether Network Watcher is "enabled" in a region (varies by tool/updates over time — know the general toolset).

### 5.2 Key Tools/Capabilities (exam favorites)

| Tool | Function |
|---|---|
| **Topology** | Generates a visual diagram of resources in a VNet and their relationships |
| **IP flow verify** | Tests whether a packet is allowed or denied to/from a VM, based on 5-tuple (source/dest IP, port, protocol, direction) — tells you **which NSG rule** allowed/denied it |
| **Next hop** | Shows the next hop type/IP for traffic from a VM to a specific destination IP — helps validate/troubleshoot routing (UDR) issues |
| **Effective security rules** | Shows the combined/effective NSG rules applied to a NIC (from both subnet-level and NIC-level NSGs) |
| **Effective routes** | Shows all effective routes (system + custom + BGP) applied to a NIC |
| **NSG diagnostics / NSG flow logs** | Logs allowed/denied traffic through an NSG — used with **Traffic Analytics** to visualize traffic patterns |
| **Connection Monitor** | Monitors connectivity between a source and destination (VM-to-VM, VM-to-FQDN, VM-to-URL, on-prem-to-Azure) over time — tracks latency, hops, and connectivity state; replaced older "Connection Troubleshoot" for ongoing monitoring |
| **Connection troubleshoot** | One-time (on-demand) connectivity test between source and destination — reports latency and hop-by-hop route |
| **Packet capture** | Remotely starts/stops packet capture on a VM (can be triggered by alerts) — saved to storage account or local disk for analysis (e.g., in Wireshark) |
| **VPN Diagnostics / VPN troubleshoot** | Diagnoses issues with VPN Gateways and connections |
| **Diagnostic logs** | Enables/manages resource logs for NSGs, LB, App Gateway, Public IPs, etc. |

### 5.3 NSG Flow Logs & Traffic Analytics
- **NSG Flow Logs**: record information about IP traffic flowing through an NSG (allowed/denied, 5-tuple, direction, bytes/packets) — written to a Storage Account.
- **Version 2** flow logs include flow state and throughput info.
- **Traffic Analytics**: analyzes flow logs (built on Log Analytics) to provide visualizations — most communicating hosts, most-used ports/protocols, malicious/blocked traffic, traffic distribution by geography, etc. Requires a Log Analytics workspace.

### 5.4 IP Flow Verify — Deep Dive
- Input: VM, NIC, protocol (TCP/UDP), direction (inbound/outbound), local IP:port, remote IP:port.
- Output: **Access = Allow/Deny** + the **specific NSG rule name** responsible for the decision.
- Great for troubleshooting "why can't my VM reach X" — quickly pinpoints NSG misconfiguration without manually reading every rule.

### 5.5 Next Hop — Deep Dive
- Input: VM/NIC + destination IP address.
- Output: **next hop type** (e.g., Internet, VNet, VirtualAppliance, None, VirtualNetworkGateway) and the next hop IP if applicable.
- Useful to confirm that a UDR is actually being applied and traffic isn't silently dropped (`None`) or misrouted.

### 5.6 Connection Monitor vs Connection Troubleshoot
| | Connection Troubleshoot | Connection Monitor |
|---|---|---|
| Nature | One-time/on-demand test | Continuous/scheduled monitoring |
| Output | Single snapshot: latency, hops, status | Time-series data, topology map, alerting over time |
| Use case | Quick ad-hoc check | Ongoing SLA/health monitoring |

### 5.7 Diagnostic Workflow (typical exam scenario)
1. VM can't reach another VM/service → run **IP Flow Verify** to check NSG rules.
2. If allowed by NSG but still fails → check **Effective Routes** / **Next Hop** to confirm traffic isn't being routed to `None` or wrong NVA.
3. For ongoing/intermittent issues → set up **Connection Monitor**.
4. For deep packet-level analysis → use **Packet Capture**.
5. For traffic pattern/security analysis over time → enable **NSG Flow Logs + Traffic Analytics**.

---

## 6. Quick Comparison Table — All Four Topics

| Service | Purpose | OSI Layer | Key Exam Concept |
|---|---|---|---|
| **Routes (UDR/NVA)** | Control path of traffic within/out of VNet | L3 | UDR > BGP > System route priority; IP forwarding needed for NVA |
| **Load Balancer** | Distribute L4 traffic across VM instances | L4 | Standard SKU secure-by-default, HA Ports, health probes |
| **Application Gateway** | L7 web traffic routing + WAF + SSL offload | L7 | Path-based/multi-site routing, WAF modes, v2 autoscale |
| **Network Watcher** | Diagnose/monitor network | N/A (tooling) | IP Flow Verify (NSG issues), Next Hop (routing issues), Flow Logs + Traffic Analytics |

---

## 7. Common Exam Gotchas
- A **route table's "Propagate gateway routes"** setting controls whether BGP routes flow into a subnet — set to **No** if you want to force all traffic through an NVA regardless of on-prem BGP advertisements.
- NVA needs **both** Azure-level IP forwarding **and** OS-level IP forwarding enabled — missing either breaks routing.
- **Standard Load Balancer + Standard Public IP** must be used together; you cannot mix Basic and Standard resources.
- Standard LB backend VMs have **no default outbound internet access** — must add outbound rules, a public IP per VM, or a NAT Gateway.
- Application Gateway requires a **dedicated subnet** (no other resource types typically deployed there, and it needs sufficient IP space, especially for v2 autoscaling — Microsoft recommends /24 or larger for v2 headroom, minimum /26... check current docs for exact sizing at exam time).
- Application Gateway operates at **Layer 7**, Load Balancer at **Layer 4** — this distinction is one of the most frequently tested facts.
- **IP Flow Verify** tells you the NSG rule that allowed/denied traffic — **Next Hop** tells you the routing decision — don't confuse the two tools.
- NSG Flow Logs require a **Storage Account**; Traffic Analytics additionally requires a **Log Analytics workspace**.
