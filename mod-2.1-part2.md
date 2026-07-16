# Create, Configure, and Manage Identities (Part 2 - Administrative Units, Device Registration & Device Management)

> **Certification:** AZ-104 - Microsoft Identity and Access Administrator Associate
>
> **Module:** Create, Configure, and Manage Identities
>
> **Part 2:** Administrative Units, Device Registration, Microsoft Entra Joined Devices, Hybrid Microsoft Entra Joined Devices, Microsoft Intune & Device Management

---

# Table of Contents

- Administrative Units
- Device Identity
- Microsoft Entra Registered Devices
- Microsoft Entra Joined Devices
- Hybrid Microsoft Entra Joined Devices
- Device Registration Methods
- Device Management
- Microsoft Intune
- Conditional Access
- Windows Hello for Business
- Cloud Kerberos Trust
- Device Comparison
- Best Practices
- AZ-104 Exam Tips
- Quick Revision

---

# Administrative Units

## What are Administrative Units?

Administrative Units (AUs) allow organizations to delegate administrative tasks to specific administrators without giving them access to the entire Microsoft Entra tenant.

Instead of making someone a Global Administrator, you can give them administrative rights over only a subset of users or groups.

Example:

```
Contoso Ltd.

│

├── India Office
│      └── India Administrator
│
├── USA Office
│      └── USA Administrator
│
└── UK Office
       └── UK Administrator
```

Each administrator can only manage users in their assigned Administrative Unit.

---

# Why use Administrative Units?

Without Administrative Units:

```
Global Administrator

↓

Manages Entire Organization
```

With Administrative Units:

```
Global Administrator

↓

India Admin

↓

Only India Users
```

Benefits

- Delegated administration
- Reduced security risk
- Least Privilege Access
- Easier management for large organizations

---

# Administrative Unit Components

An Administrative Unit can contain:

- Users
- Groups
- Devices (supported scenarios)

Administrators assigned to the AU can manage only those objects.

---

# Real-World Example

A multinational company has offices in:

- India
- USA
- Germany

Instead of giving every country's IT team Global Administrator permissions,

they create:

```
Administrative Unit

↓

India

↓

India Helpdesk Administrator
```

Now the India administrator cannot reset passwords for users in Germany.

---

# Device Identity

Every device accessing Microsoft cloud resources has an identity.

Device identities help organizations:

- Verify trusted devices
- Enforce Conditional Access
- Apply Intune policies
- Enable Single Sign-On (SSO)

Microsoft Entra supports three device identity types.

---

# Microsoft Entra Registered Devices

## Definition

A Microsoft Entra Registered device is a **personal (BYOD)** device that is registered with Microsoft Entra ID.

The user signs in using a **local account**, but adds their work account to access company resources.

Architecture

```
Personal Laptop

↓

Local Windows Account

↓

Add Work Account

↓

Microsoft Entra Registered
```

---

## Characteristics

| Feature | Registered Device |
|----------|------------------|
| Device Owner | User |
| Sign-in | Local Account |
| BYOD | ✅ Yes |
| Organization Owned | ❌ No |
| Supports Intune | ✅ Yes |
| Conditional Access | ✅ Yes |

---

## Supported Operating Systems

- Windows 10/11
- macOS
- iOS
- Android
- Linux (Supported distributions)

---

## Registration Process

```
Settings

↓

Accounts

↓

Access Work or School

↓

Connect

↓

Microsoft Entra Registered
```

---

## Common Use Cases

- Personal Laptop
- Personal Mobile Phone
- Contractor Device
- Consultant Device

---

## Benefits

- Single Sign-On
- Intune Enrollment
- Conditional Access
- Secure BYOD

---

# Microsoft Entra Joined Devices

## Definition

Microsoft Entra Joined devices belong to the organization and are joined directly to Microsoft Entra ID.

Users sign in using their organizational account.

Architecture

```
Company Laptop

↓

Microsoft Entra Join

↓

Login

↓

heer@contoso.com
```

---

## Characteristics

| Feature | Entra Joined |
|----------|-------------|
| Device Owner | Organization |
| Sign-in | Microsoft Entra Account |
| Local Account Required | ❌ No |
| Cloud Only | ✅ Yes |
| Intune Management | ✅ Yes |

---

## Benefits

- Single Sign-On
- Passwordless Authentication
- Windows Hello
- Intune Management
- Conditional Access
- Cloud-native deployment

---

## Registration Methods

- Out-of-Box Experience (OOBE)
- Windows Autopilot
- Bulk Enrollment

---

## Best For

- Cloud-only organizations
- Remote workforce
- Microsoft 365 environments

---

# Hybrid Microsoft Entra Joined Devices

## Definition

A Hybrid Microsoft Entra Joined device is joined to both:

- On-premises Active Directory
- Microsoft Entra ID

Architecture

```
Company Laptop

↓

Active Directory

↓

Microsoft Entra Connect

↓

Microsoft Entra ID
```

---

## Characteristics

| Feature | Hybrid Joined |
|----------|--------------|
| Device Owner | Organization |
| Sign-in | Active Directory |
| Cloud Access | ✅ |
| On-Prem Access | ✅ |
| Group Policy | ✅ |
| Intune | ✅ (Co-management) |

---

## Benefits

- Access cloud resources
- Access on-premises resources
- Existing Group Policy support
- Existing imaging support
- Gradual cloud migration

---

## Common Scenarios

Organizations with:

- Active Directory
- Group Policy
- Configuration Manager
- Legacy applications

---

# Device Registration Comparison

| Feature | Registered | Joined | Hybrid Joined |
|----------|------------|---------|---------------|
| Device Owner | User | Organization | Organization |
| Login | Local Account | Microsoft Entra | Active Directory |
| BYOD | ✅ | ❌ | ❌ |
| Cloud-only | ❌ | ✅ | ❌ |
| Active Directory | ❌ | ❌ | ✅ |
| Intune | ✅ | ✅ | ✅ |
| Conditional Access | ✅ | ✅ | ✅ |
| Group Policy | ❌ | ❌ | ✅ |

---

# Device Registration Methods

Devices can be registered using:

- Windows Settings
- Windows Autopilot
- OOBE
- Intune Enrollment
- Bulk Enrollment

---

# Microsoft Intune

## What is Microsoft Intune?

Microsoft Intune is Microsoft's cloud-based Mobile Device Management (MDM) and Mobile Application Management (MAM) solution.

It allows organizations to manage:

- Windows
- macOS
- Android
- iOS

---

## Intune Capabilities

- Device Enrollment
- Compliance Policies
- Configuration Profiles
- App Deployment
- Remote Wipe
- BitLocker Management
- Endpoint Security

---

## Example

Company Policy

```
↓

Require Encryption

↓

Require PIN

↓

Require Antivirus

↓

Device Becomes Compliant
```

---

# Device Compliance

Compliance policies determine whether a device meets organizational security requirements.

Examples

- Device encrypted
- Password enabled
- Antivirus installed
- Latest updates installed
- Device not rooted
- Device not jailbroken

If compliant:

```
Access Granted
```

Otherwise

```
Access Blocked
```

---

# Conditional Access

Conditional Access evaluates:

- User
- Device
- Location
- Risk
- Application

Example

```
IF

User = Employee

AND

Device = Compliant

THEN

Allow Microsoft 365
```

Otherwise

```
Access Denied
```

---

# Windows Hello for Business

Windows Hello replaces passwords using:

- PIN
- Fingerprint
- Face Recognition

Benefits

- Passwordless authentication
- More secure
- Better user experience

---

# Cloud Kerberos Trust

## What is it?

Cloud Kerberos Trust enables Microsoft Entra Joined and Hybrid Joined devices to authenticate to on-premises resources without requiring Device Writeback.

---

## Why is it Important?

Older deployments used:

```
Device Writeback
```

This is no longer recommended.

Microsoft recommends:

```
Cloud Kerberos Trust
```

Benefits

- Simpler deployment
- Better security
- Supports Windows Hello for Business
- Enables SSO to on-premises resources

---

# Device Writeback

Device Writeback was previously used to write Microsoft Entra device objects back into Active Directory.

Status:

❌ No longer recommended

Replacement:

✅ Cloud Kerberos Trust

---

# Best Practices

✅ Use Microsoft Entra Registered for BYOD devices.

✅ Use Microsoft Entra Joined for cloud-only organizations.

✅ Use Hybrid Joined when Active Directory is still required.

✅ Manage devices using Microsoft Intune.

✅ Use Conditional Access together with compliance policies.

✅ Deploy Windows Hello for Business for passwordless authentication.

✅ Use Cloud Kerberos Trust instead of Device Writeback.

---

# AZ-104 Exam Tips

### Microsoft Entra Registered

✔ Personal Device

✔ Local Account

✔ BYOD

---

### Microsoft Entra Joined

✔ Company Device

✔ Cloud-only

✔ Microsoft Entra Login

---

### Hybrid Microsoft Entra Joined

✔ Active Directory

✔ Microsoft Entra

✔ Group Policy

---

### Microsoft Intune

Responsible for:

- Device Management
- Compliance
- App Deployment
- Security Policies

---

### Conditional Access

Requires conditions such as:

- Compliant Device
- MFA
- Trusted Location
- User Risk

---

### Cloud Kerberos Trust

✔ Recommended

❌ Device Writeback

---

# Quick Revision

| Concept | Remember |
|----------|----------|
| Administrative Unit | Delegated administration for a subset of users/groups/devices |
| Entra Registered | Personal (BYOD) device using a local account |
| Entra Joined | Organization-owned, cloud-only device |
| Hybrid Joined | Joined to both Active Directory and Microsoft Entra ID |
| Intune | Mobile Device Management (MDM) and Mobile Application Management (MAM) |
| Compliance Policy | Determines if a device meets security requirements |
| Conditional Access | Grants or blocks access based on conditions |
| Windows Hello for Business | Passwordless authentication using PIN or biometrics |
| Cloud Kerberos Trust | Modern replacement for Device Writeback |
| Device Writeback | Legacy approach; no longer recommended |
