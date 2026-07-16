# Create, Configure, and Manage Identities (Part 4 - Custom Security Attributes & Module Revision)

> **Certification:** AZ-104 - Microsoft Identity and Access Administrator Associate
>
> **Module:** Create, Configure, and Manage Identities
>
> **Part 4:** Custom Security Attributes, Attribute-Based Access Control (ABAC), Module Summary & Exam Revision

---

# Table of Contents

- Custom Security Attributes
- Why Use Custom Security Attributes?
- Attribute Sets
- Attribute Definitions
- Data Types
- Value Types
- Assigning Custom Security Attributes
- Attribute-Based Access Control (ABAC)
- Supported Objects
- Limitations
- Best Practices
- AZ-104 Exam Tips
- Module Summary
- Comparison Tables
- Quick Revision
- Practice Questions

---

# Custom Security Attributes

## What are Custom Security Attributes?

Custom Security Attributes are **business-specific key-value pairs** that administrators create in Microsoft Entra ID.

Unlike built-in attributes such as:

- Display Name
- Department
- Job Title

Custom Security Attributes allow organizations to create their own properties.

Example

```
User

↓

Project = Apollo

SecurityLevel = Level-3

EmployeeType = Contractor

SalaryBand = Band-5
```

Think of them as **custom fields** that you add to Microsoft Entra objects.

---

# Why Use Custom Security Attributes?

Organizations often need to store business information that Microsoft Entra ID doesn't provide by default.

Examples

- Hourly Salary
- Cost Center
- Project Name
- Security Clearance
- Office Region
- Employee Category
- Business Unit

Example

```
User

↓

Project = Finance

↓

Used for Access Control
```

---

# Benefits

Custom Security Attributes help organizations:

- Extend user profiles
- Categorize users
- Categorize enterprise applications
- Simplify searching
- Simplify filtering
- Support Attribute-Based Access Control (ABAC)

---

# Real-World Example

Company Storage Accounts

```
Finance Storage

HR Storage

Engineering Storage
```

Users

```
Heer

↓

Department = Engineering
```

Policy

```
IF

Department = Engineering

↓

Allow Engineering Storage
```

John

```
Department = HR

↓

Denied
```

---

# Attribute Sets

Attributes are organized into **Attribute Sets**.

Example

```
Employee

↓

Project

↓

Security Level

↓

Cost Center
```

Instead of creating unrelated attributes,

Microsoft recommends grouping them logically.

Example

```
EmployeeInfo

├── Project

├── EmployeeType

├── CostCenter

└── SecurityLevel
```

---

# Attribute Definitions

Each attribute contains:

- Name
- Description
- Data Type
- Allowed Values

Example

```
Attribute

Project

Description

Employee Project Assignment
```

---

# Supported Data Types

Microsoft supports three primary data types.

| Data Type | Example |
|------------|---------|
| Boolean | True / False |
| Integer | 5 |
| String | Apollo |

---

## Boolean

Stores

```
True

False
```

Example

```
Manager

↓

True
```

---

## Integer

Stores numbers.

Example

```
SecurityLevel

↓

3
```

---

## String

Stores text.

Example

```
Project

↓

Apollo
```

---

# Value Types

Custom Security Attributes support:

## Single Value

```
Department

↓

IT
```

Only one value.

---

## Multiple Values

```
Skills

↓

Azure

Docker

PowerShell

Terraform
```

Multiple values allowed.

---

# Free-form Values

Users can enter any value.

Example

```
Project

↓

Apollo

Mercury

Phoenix

Anything
```

---

# Predefined Values

Administrator creates allowed values.

Example

```
EmployeeType

↓

Intern

Contractor

Permanent

Vendor
```

Users cannot enter random values.

---

# Assigning Custom Security Attributes

Attributes can be assigned to:

- Users
- Enterprise Applications (Service Principals)

Example

```
User

↓

Project = Apollo

SecurityLevel = High
```

Application

```
Payroll App

↓

Environment = Production
```

---

# Searching with Custom Attributes

Large organizations may have thousands of users.

Instead of searching manually,

filter using attributes.

Example

```
Project = Apollo
```

Returns only Apollo employees.

---

# Attribute-Based Access Control (ABAC)

## What is ABAC?

Attribute-Based Access Control grants access based on attributes rather than only roles.

Traditional RBAC

```
Role

↓

Storage Contributor

↓

Access Granted
```

ABAC

```
Project = Finance

↓

Access Finance Storage
```

---

# Example

Storage

```
Finance Storage
```

User

```
Department = Finance
```

Condition

```
Department == Finance

↓

Allow Access
```

Another User

```
Department = HR
```

↓

Access Denied

---

# Supported Objects

Custom Security Attributes can be assigned to:

✅ Users

✅ Enterprise Applications (Service Principals)

---

# Unsupported Scenarios

Custom Security Attributes are **NOT supported** in:

- Microsoft Entra Domain Services
- SAML Token Claims
- JWT Token Claims

---

# Best Practices

✅ Create meaningful Attribute Sets.

✅ Use predefined values whenever possible.

✅ Use ABAC for fine-grained authorization.

✅ Follow consistent naming conventions.

✅ Limit permissions to modify attributes.

---

# AZ-104 Exam Tips

Remember

Custom Security Attributes

↓

Business-specific information

---

Assigned To

- Users
- Enterprise Applications

---

Supported Data Types

- Boolean
- Integer
- String

---

Supports

- Single Values
- Multiple Values

---

Can be used for

- Searching
- Filtering
- ABAC

---

Cannot be used with

- JWT Claims
- SAML Claims
- Entra Domain Services

---

# Complete Module Comparison

## User Types

| User Type | Managed In | Best For |
|------------|------------|-----------|
| Cloud User | Microsoft Entra | Cloud Organizations |
| Synced User | Active Directory | Hybrid Organizations |
| Guest User | External Tenant | Collaboration |

---

## Group Types

| Group | Purpose |
|---------|----------|
| Security Group | Permissions |
| Microsoft 365 Group | Collaboration |

---

## Membership Types

| Type | Description |
|--------|-------------|
| Assigned | Manual |
| Dynamic | Automatic Rule-Based |

---

## Device Types

| Device | Sign-in | Ownership |
|----------|---------|-----------|
| Registered | Local Account | User |
| Entra Joined | Microsoft Entra | Organization |
| Hybrid Joined | Active Directory | Organization |

---

## Licensing

| Type | Description |
|---------|-------------|
| Direct | Manual |
| Group-Based | Automatic |

---

## Licensing Errors

| Error | Cause |
|----------|-------|
| CountViolation | Not Enough Licenses |
| MutuallyExclusiveViolation | Conflicting Plans |
| DependencyViolation | Missing Prerequisite |
| UsageLocationViolation | Missing Usage Location |
| Duplicate Proxy | Duplicate SMTP |
| Concurrency | Automatic Retry |

---

## Custom Security Attributes

| Feature | Supported |
|----------|------------|
| Users | ✅ |
| Enterprise Applications | ✅ |
| Boolean | ✅ |
| Integer | ✅ |
| String | ✅ |
| Multiple Values | ✅ |
| JWT Claims | ❌ |
| SAML Claims | ❌ |

---

# Module Best Practices

✅ Use Dynamic Groups whenever possible.

✅ Use Security Groups for permissions.

✅ Use Microsoft 365 Groups for collaboration.

✅ Register BYOD devices.

✅ Join company-owned devices to Microsoft Entra.

✅ Manage devices using Microsoft Intune.

✅ Use Group-Based Licensing.

✅ Always configure Usage Location.

✅ Use Custom Security Attributes for ABAC.

---

# Quick Revision

| Concept | Remember |
|----------|----------|
| Cloud User | Exists only in Entra |
| Synced User | Managed from Active Directory |
| Guest User | External User |
| Security Group | Permissions |
| Microsoft 365 Group | Collaboration |
| Administrative Unit | Delegated Administration |
| Registered Device | Personal Device (BYOD) |
| Entra Joined | Cloud-Only Company Device |
| Hybrid Joined | AD + Entra |
| Intune | Device Management |
| Conditional Access | Grant/Block Access |
| Group-Based Licensing | Automatic |
| Usage Location | Required for Licensing |
| Reprocess | Retry License Assignment |
| Custom Security Attribute | Business-Specific Property |
| ABAC | Access Based on Attributes |

---

# Practice Questions

### Question 1

Which Microsoft Entra device type is designed for Bring Your Own Device (BYOD)?

- A. Microsoft Entra Joined
- B. Hybrid Microsoft Entra Joined
- C. Microsoft Entra Registered
- D. Active Directory Joined

✅ **Answer:** C

---

### Question 2

Which group type is primarily used for assigning permissions?

- A. Microsoft 365 Group
- B. Security Group
- C. Distribution List
- D. Mail Contact

✅ **Answer:** B

---

### Question 3

What is required before assigning Microsoft licenses?

- A. Security Group
- B. Usage Location
- C. Conditional Access
- D. Administrative Unit

✅ **Answer:** B

---

### Question 4

Which Microsoft Entra feature allows business-specific attributes to be assigned to users?

- A. Dynamic Groups
- B. Administrative Units
- C. Custom Security Attributes
- D. Identity Protection

✅ **Answer:** C

---

### Question 5

Which access model uses user attributes instead of only roles?

- A. RBAC
- B. DAC
- C. ABAC
- D. ACL

✅ **Answer:** C

---

# Module Conclusion

After completing this module, you should be able to:

- Create and manage users
- Manage Microsoft Entra groups
- Configure Administrative Units
- Register and join devices
- Manage devices using Microsoft Intune
- Configure Group-Based Licensing
- Troubleshoot licensing issues
- Implement Custom Security Attributes
- Use Attribute-Based Access Control (ABAC)

This knowledge forms the foundation for identity management in Microsoft Entra ID and is heavily tested in the **AZ-104: Microsoft Identity and Access Administrator** certification exam.
