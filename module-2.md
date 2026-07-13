# Microsoft Entra ID

Active Directory Domain Services (AD DS) is a directory service that provides methods for storing directory data, such as user accounts and passwords, and makes this data available to network users, administrators, devices, and services. It runs as a service on Windows Server as a **Domain Controller (DC)**.

Microsoft Entra ID (formerly Azure Active Directory) is part of Microsoft's **Platform as a Service (PaaS)** offering and operates as a Microsoft-managed directory service in the cloud. Unlike AD DS, it isn't part of the core infrastructure that customers own and manage, nor is it an Infrastructure as a Service (IaaS) offering.

Although this means you have less control over the underlying infrastructure, it also means you don't have to deploy, patch, or maintain domain controllers.

Microsoft Entra ID can be used to:

- Configure access to applications
- Configure Single Sign-On (SSO) for cloud-based SaaS applications
- Manage users and groups
- Provision users automatically
- Enable federation between organizations
- Provide an identity management solution
- Detect irregular sign-in activity
- Configure Multi-Factor Authentication (MFA)
- Extend existing on-premises Active Directory to the cloud
- Configure Application Proxy for cloud and on-premises applications
- Configure Conditional Access policies for users and devices

---

# What is a Microsoft Entra Tenant?

A **Microsoft Entra tenant** is a dedicated instance of Microsoft Entra ID that represents an organization or company.

When an organization signs up for Microsoft cloud services such as:

- Azure
- Microsoft 365
- Microsoft Intune

a Microsoft Entra tenant is created to store and manage the organization's identities.

A tenant contains:

- Users
- Groups
- Devices
- Applications
- Service Principals

Important points:

- An Azure subscription must be associated with **one and only one** Microsoft Entra tenant.
- A single Microsoft Entra tenant **can manage multiple Azure subscriptions**.
- The tenant acts as the **security boundary** for identities and authentication.
- Azure RBAC uses identities stored in the Microsoft Entra tenant to grant permissions to Azure resources.

### Default Domain

Every Microsoft Entra tenant receives a default domain in the following format:

```
companyname.onmicrosoft.com
```

Organizations can later add and verify their own custom domains, for example:

```
company.com
```

---

# Microsoft Entra Schema

A **schema** is the blueprint of a directory. It defines the types of objects that can exist and the attributes they contain.

The Microsoft Entra schema is much simpler than the Active Directory Domain Services (AD DS) schema because it is designed for cloud identity management instead of managing on-premises Windows infrastructure.

## Device Objects Instead of Computer Objects

Unlike AD DS, Microsoft Entra ID does **not** contain a **Computer** object.

Instead, it uses **Device** objects to represent:

- Windows devices
- macOS devices
- Android devices
- iPhones and iPads

This is because Microsoft Entra ID supports modern device management across multiple operating systems rather than traditional Windows domain management.

---

## Microsoft Entra Join vs Domain Join

In AD DS, Windows computers perform a **Domain Join** to become members of a Windows domain.

In Microsoft Entra ID, devices perform a **Microsoft Entra Join**, allowing users to authenticate with their organizational accounts and access cloud resources securely.

---

## No Group Policy Objects (GPOs)

Traditional Active Directory uses **Group Policy Objects (GPOs)** to configure Windows computers and users.

Examples include:

- Installing software
- Enforcing password policies
- Configuring desktop settings
- Restricting Control Panel access

Microsoft Entra ID does **not** support GPOs.

Instead, Microsoft recommends **Modern Management** using **Microsoft Intune**, which can manage Windows, macOS, Android, and iOS devices from the cloud.

---

## No Organizational Units (OUs)

AD DS uses **Organizational Units (OUs)** to organize users and computers into a hierarchy.

Example:

```
Company
 ├── HR
 ├── Sales
 ├── IT
 └── Finance
```

Microsoft Entra ID does **not** support Organizational Units.

Instead, objects are organized using **Groups**, and permissions or policies are assigned to those groups.

---

## Extensible Schema

Microsoft Entra ID supports **schema extensions**, allowing organizations to add custom attributes to directory objects when needed.

Unlike traditional AD DS schema modifications, these extensions are designed to be easier to manage and can be removed when no longer required.

---

## Applications and Service Principals

Microsoft Entra ID separates applications into two different objects.

### Application

An **Application** object is the global definition (blueprint) of an application.

It contains information such as:

- Client ID
- Redirect URIs
- API permissions
- Application configuration

One application definition can be used across multiple Microsoft Entra tenants.

### Service Principal

A **Service Principal** is the application's identity inside a specific Microsoft Entra tenant.

It represents an instance of the application and is used for:

- Authentication
- Authorization
- Azure RBAC
- Azure DevOps service connections
- Automation
- CI/CD deployments

Each tenant that uses an application has its own Service Principal.

---

## AD DS vs Microsoft Entra ID

| Active Directory Domain Services | Microsoft Entra ID |
|----------------------------------|--------------------|
| Runs on Windows Server | Microsoft-managed cloud service |
| Uses Computer objects | Uses Device objects |
| Domain Join | Microsoft Entra Join |
| Uses Group Policy (GPO) | Uses Microsoft Intune for modern management |
| Supports Organizational Units (OUs) | Uses Groups instead of OUs |
| Manages on-premises Windows infrastructure | Manages cloud identities, applications, and devices |
| Computer-centric | Identity-centric |


---

# Microsoft Entra ID Licensing (Free, P1, and P2)

Microsoft Entra ID is available in multiple editions. While the **Free** and **Office 365** editions provide basic identity management features, **Microsoft Entra ID P1** and **P2** offer advanced security, identity protection, and administration capabilities.

The Premium editions require additional licensing per user and can be purchased separately or as part of the **Microsoft Enterprise Mobility + Security (EMS)** suite, which also includes Microsoft Intune and Azure Information Protection.

## Microsoft Entra ID Free

The Free edition provides the core identity management features required for Azure and Microsoft cloud services.

Features include:

- User and group management
- Azure authentication
- Single Sign-On (SSO) for Microsoft cloud services
- Basic Role-Based Access Control (RBAC)
- Device registration and Microsoft Entra Join

This edition is suitable for learning, small organizations, and basic Azure deployments.

---

## Microsoft Entra ID P1

Microsoft Entra ID P1 includes all Free edition features and adds advanced identity management and security capabilities.

### Self-Service Group Management

Allows users to create and manage security groups, request membership in existing groups, and lets group owners approve or reject membership requests without administrator involvement.

### Advanced Security Reports

Provides machine learning-based reports and alerts that detect unusual sign-in activities and suspicious access patterns to help administrators identify potential security threats.

### Multi-Factor Authentication (MFA)

Adds enterprise-grade Multi-Factor Authentication for Azure, Microsoft 365, Dynamics 365, VPNs, RADIUS, and many third-party applications, improving account security beyond passwords alone.

### Microsoft Identity Manager (MIM)

Supports hybrid identity environments by synchronizing identities between on-premises directories (such as AD DS, LDAP, Oracle, etc.) and Microsoft Entra ID.

### Enterprise SLA

Provides a **99.9% Service Level Agreement (SLA)**, ensuring high availability of Microsoft Entra ID services.

### Password Writeback

Allows users to reset their passwords through Self-Service Password Reset (SSPR), with the updated password automatically synchronized back to the on-premises Active Directory.

### Cloud App Discovery

Discovers cloud applications used within an organization, helping administrators identify unauthorized or risky applications (Shadow IT).

### Conditional Access

Allows administrators to create access policies based on:

- User or group
- Device
- Location
- Application
- Risk level

Examples include:

- Requiring MFA when users sign in from an unknown location.
- Blocking access from unmanaged devices.
- Allowing access only from company-managed devices.

### Microsoft Entra Connect Health

Monitors the health of Microsoft Entra Connect synchronization and provides:

- Health monitoring
- Performance metrics
- Synchronization alerts
- Usage reports
- Configuration insights

---

## Microsoft Entra ID P2

Microsoft Entra ID P2 includes **all P1 features** and adds advanced identity protection and privileged access management.

### Microsoft Entra ID Protection

Uses machine learning and Microsoft's threat intelligence to detect identity-related risks, including:

- Suspicious sign-in attempts
- Leaked credentials
- Anonymous IP usage
- Impossible travel scenarios
- High-risk user behavior

Administrators can configure automated responses such as:

- Block sign-in
- Force Multi-Factor Authentication
- Require password reset

### Microsoft Entra Privileged Identity Management (PIM)

Provides Just-In-Time (JIT) administrative access for privileged accounts.

Instead of assigning permanent administrator privileges, users activate elevated permissions only when required.

Benefits include:

- Temporary administrator access
- Approval workflows
- Time-limited role activation
- Reduced risk of privileged account compromise
- Audit logs for privileged operations

---

# Comparison of Microsoft Entra ID Editions

| Feature | Free | P1 | P2 |
|---------|:----:|:--:|:--:|
| User & Group Management | ✅ | ✅ | ✅ |
| Single Sign-On (SSO) | ✅ | ✅ | ✅ |
| Azure Authentication | ✅ | ✅ | ✅ |
| Basic RBAC | ✅ | ✅ | ✅ |
| Multi-Factor Authentication (MFA) | Basic | ✅ | ✅ |
| Self-Service Group Management | ❌ | ✅ | ✅ |
| Advanced Security Reports | ❌ | ✅ | ✅ |
| Conditional Access | ❌ | ✅ | ✅ |
| Password Writeback | ❌ | ✅ | ✅ |
| Cloud App Discovery | ❌ | ✅ | ✅ |
| Microsoft Entra Connect Health | ❌ | ✅ | ✅ |
| Microsoft Identity Manager (MIM) | ❌ | ✅ | ✅ |
| Microsoft Entra ID Protection | ❌ | ❌ | ✅ |
| Privileged Identity Management (PIM) | ❌ | ❌ | ✅ |

---

## Summary

- **Free**: Provides core identity management features such as user management, authentication, Single Sign-On (SSO), and basic Azure access.
- **P1**: Adds enterprise identity management, Conditional Access, Multi-Factor Authentication, password writeback, monitoring, and hybrid identity capabilities.
- **P2**: Includes all P1 features plus advanced identity protection, risk-based authentication, and Privileged Identity Management (PIM) for securing administrator accounts.

- ---

# Microsoft Entra Domain Services (Microsoft Entra DS)

Many organizations have **Line-of-Business (LOB)** applications that rely on **Active Directory Domain Services (AD DS)** for authentication and authorization. These applications typically use protocols such as **LDAP**, **Kerberos**, or **NTLM**, and the computers running them are usually joined to an Active Directory domain.

When migrating these applications to Azure, they still require traditional Active Directory services for authentication.

## Traditional Approaches

Before Microsoft Entra Domain Services, organizations generally had two options:

### Option 1: Site-to-Site VPN

Create a **Site-to-Site VPN** between the on-premises network and Azure.

In this approach:

- Applications running in Azure send authentication requests to the on-premises Domain Controllers through the VPN.
- Authentication traffic continuously travels across the VPN.

**Advantages**

- Simple if an existing on-premises Active Directory already exists.
- No additional Domain Controllers required in Azure.

**Disadvantages**

- Authentication depends on VPN availability.
- Increased network latency.
- Authentication traffic constantly crosses the VPN.

---

### Option 2: Deploy Domain Controllers in Azure

Deploy replica **Active Directory Domain Controllers** as Azure Virtual Machines.

In this approach:

- Authentication occurs locally inside Azure.
- Only Active Directory replication traffic crosses the VPN.

**Advantages**

- Faster authentication.
- Reduced authentication traffic across the VPN.

**Disadvantages**

- Administrators must deploy, patch, monitor, back up, and manage additional Domain Controllers.
- Increased infrastructure and licensing costs.

---

# What is Microsoft Entra Domain Services?

**Microsoft Entra Domain Services (Microsoft Entra DS)** is a **managed domain service** provided by Microsoft that offers many traditional Active Directory features without requiring organizations to deploy or manage Domain Controllers.

It is available with **Microsoft Entra ID Premium P1 and P2**.

Microsoft manages:

- Domain Controllers
- Replication
- Patching
- Monitoring
- Availability

Administrators simply consume the service without maintaining the underlying infrastructure.

---

# Features of Microsoft Entra Domain Services

Microsoft Entra Domain Services provides many of the same capabilities as traditional Active Directory, including:

- Domain Join
- LDAP
- Kerberos authentication
- NTLM authentication
- Group Policy support (limited)

This allows many legacy Windows and enterprise applications to run in Azure without requiring customer-managed Domain Controllers.

---

# Integration with Microsoft Entra ID

Microsoft Entra Domain Services integrates directly with **Microsoft Entra ID**.

If an organization also has an on-premises Active Directory, **Microsoft Entra Connect** can synchronize identities from AD DS to Microsoft Entra ID.

```
On-Premises AD DS
        │
        ▼
Microsoft Entra Connect
        │
        ▼
Microsoft Entra ID
        │
        ▼
Microsoft Entra Domain Services
```

This enables users to use the **same organizational credentials** both on-premises and in Azure.

---

# Cloud-Only Deployment

Organizations without an on-premises Active Directory can still use Microsoft Entra Domain Services.

In this scenario:

- Create a Microsoft Entra tenant.
- Enable Microsoft Entra Domain Services.
- Deploy Azure Virtual Machines into a virtual network.
- Join those virtual machines to the managed domain.

This provides Active Directory-like functionality without deploying any Domain Controllers.

---

# Benefits of Microsoft Entra Domain Services

- No need to deploy or manage Domain Controllers.
- Microsoft automatically manages replication.
- No Windows Server patching or maintenance.
- No need to manage Domain Admin or Enterprise Admin infrastructure.
- Supports legacy applications that require LDAP, Kerberos, or NTLM.
- Simplifies migration of traditional enterprise applications to Azure.

---

# Limitations of Microsoft Entra Domain Services

Although Microsoft Entra Domain Services provides many Active Directory features, it has several limitations.

### Limited Computer Objects

Only the base Active Directory computer object is supported.

---

### No Schema Extensions

Unlike traditional Active Directory, administrators **cannot extend the Active Directory schema** by adding custom attributes or object types.

---

### Flat Organizational Unit (OU) Structure

Microsoft Entra Domain Services supports only a **flat OU hierarchy**.

Nested Organizational Units are **not supported**.

---

### Limited Group Policy Support

Microsoft Entra Domain Services includes built-in Group Policy Objects (GPOs) for users and computers.

However, it does **not** support:

- OU-based GPO targeting
- Windows Management Instrumentation (WMI) filtering
- Security Group filtering

Group Policy functionality is therefore more limited than in traditional AD DS.

---

# Supported Applications

Microsoft Entra Domain Services enables organizations to migrate applications that depend on traditional Active Directory protocols without modifying the applications.

Examples include:

- Microsoft SQL Server
- Microsoft SharePoint Server
- Legacy .NET applications
- Java applications using LDAP
- Applications using Kerberos authentication
- Applications using NTLM authentication

These applications can run on Azure Virtual Machines without requiring customer-managed Domain Controllers or a VPN back to the on-premises network.

---

# Enabling Microsoft Entra Domain Services

Microsoft Entra Domain Services can be enabled directly from the Azure portal.

During deployment, administrators choose:

- The Microsoft Entra tenant
- The Azure Virtual Network (VNet)
- The deployment region

Microsoft automatically provisions and manages the domain service.

---

# Pricing

Microsoft Entra Domain Services is billed **per hour**.

The cost depends on the selected service SKU and the size of the managed directory (number of directory objects).

---

# AD DS vs Microsoft Entra ID vs Microsoft Entra Domain Services

| Feature | AD DS | Microsoft Entra ID | Microsoft Entra Domain Services |
|----------|-------|--------------------|---------------------------------|
| Deployment | Customer-managed | Microsoft-managed | Microsoft-managed |
| Domain Controllers | Required | Not required | Managed by Microsoft |
| Domain Join | ✅ | ❌ | ✅ |
| LDAP | ✅ | ❌ | ✅ |
| Kerberos | ✅ | ❌ | ✅ |
| NTLM | ✅ | ❌ | ✅ |
| Group Policy | Full support | Not supported | Limited support |
| Organizational Units | Full hierarchy | Not supported | Flat hierarchy |
| Azure & Microsoft 365 Authentication | ❌ | ✅ | Uses Microsoft Entra ID identities |
| Typical Use Case | Traditional on-premises environments | Cloud identity and authentication | Legacy applications requiring AD features in Azure |

---

# Summary

- **Active Directory Domain Services (AD DS)** is the traditional Windows Server directory service that organizations deploy and manage themselves.
- **Microsoft Entra ID** is Microsoft's cloud-based identity and access management service used for authentication, authorization, Single Sign-On (SSO), and Azure resource access.
- **Microsoft Entra Domain Services (Microsoft Entra DS)** is a Microsoft-managed domain service that provides traditional Active Directory capabilities such as **Domain Join**, **LDAP**, **Kerberos**, **NTLM**, and limited **Group Policy** without requiring organizations to deploy or manage Domain Controllers.
