# Create, Configure, and Manage Identities (Part 3 - Manage Licenses)

> **Certification:** AZ-104 - Microsoft Identity and Access Administrator Associate
>
> **Module:** Create, Configure, and Manage Identities
>
> **Part 3:** Microsoft Entra ID Licensing & Group-Based Licensing

---

# Table of Contents

- Microsoft Licensing Overview
- Types of License Assignment
- Direct Licensing
- Group-Based Licensing
- License Requirements
- License Processing
- Usage Location
- Multiple Group Membership
- License Inheritance
- Common Licensing Errors
- License Reprocessing
- Migrating to Group-Based Licensing
- Best Practices
- AZ-104 Exam Tips
- Quick Revision

---

# Microsoft Licensing Overview

Microsoft cloud services require licenses before users can access them.

Common Microsoft licenses include:

- Microsoft 365
- Office 365
- Enterprise Mobility + Security (EMS)
- Dynamics 365
- Microsoft Intune
- Microsoft Defender

Without a valid license:

```
User

↓

Attempts to access Microsoft 365

↓

❌ Access Denied
```

Licenses can be assigned individually or automatically through groups.

---

# Types of License Assignment

Microsoft Entra supports two methods.

## 1. Direct License Assignment

License assigned directly to a user.

```
Administrator

↓

User

↓

Microsoft 365 E3
```

Advantages

- Easy for small organizations
- Simple to configure

Disadvantages

- Difficult to manage at scale
- Manual work
- Error-prone

---

## 2. Group-Based Licensing

Assign licenses to a group instead of individual users.

```
Sales Department

↓

Microsoft 365 E3

↓

All Members Receive License
```

Whenever a new employee joins the Sales group:

```
Join Group

↓

License Automatically Assigned
```

When the employee leaves:

```
Removed From Group

↓

License Automatically Removed
```

This is Microsoft's recommended approach for medium and large organizations.

---

# Why Use Group-Based Licensing?

Without Group Licensing

```
Admin

↓

Assign License

↓

User 1

↓

User 2

↓

User 3

↓

User 500
```

With Group Licensing

```
Admin

↓

Assign License

↓

Sales Group

↓

500 Users Licensed Automatically
```

Benefits

- Automatic license assignment
- Automatic license removal
- Less administrative effort
- Easier onboarding
- Easier offboarding
- Reduced scripting

---

# License Requirements

Group-based licensing requires one of the following:

- Microsoft Entra ID Premium P1
- Microsoft Entra ID Premium P2
- Microsoft 365 Enterprise E3 or higher

---

# License Count Requirement

Microsoft requires enough licenses for every unique user.

Example

Purchased

```
Microsoft 365 E3

100 Licenses
```

Users

```
Sales Group

120 Members
```

Result

```
100 Users

↓

Licensed

20 Users

↓

License Assignment Failed
```

---

# License Assignment Process

```mermaid
flowchart LR

A(User Joins Group)

B(Group Has License)

C(License Assigned)

D(User Accesses Microsoft 365)

A --> B
B --> C
C --> D
```

---

# Service Plans

A Microsoft license contains multiple services called Service Plans.

Example

Microsoft 365 E3

Contains

- Exchange Online
- SharePoint Online
- Microsoft Teams
- OneDrive
- Viva Engage

An administrator can disable individual services.

Example

```
Microsoft 365 E3

↓

Disable

Viva Engage

↓

Assign Remaining Services
```

---

# Usage Location

Usage Location determines where Microsoft services can legally be assigned.

Every licensed user should have:

```
Usage Location

↓

India

USA

UK

Germany
```

If Usage Location is blank

↓

License Assignment Fails

---

# Multiple Group Membership

A user can belong to multiple licensed groups.

Example

```
Sales Group

↓

Microsoft 365 E3
```

AND

```
IT Group

↓

Enterprise Mobility + Security
```

Result

```
User Receives

Microsoft 365

+

EMS
```

---

# Duplicate Licenses

Example

```
Sales Group

↓

Microsoft 365 E3
```

AND

```
Managers Group

↓

Microsoft 365 E3
```

User belongs to both.

Result

```
Only ONE license consumed
```

Microsoft never consumes duplicate licenses.

---

# License Inheritance

Licenses assigned through groups are called inherited licenses.

Example

```
User

↓

Member of HR Group

↓

Microsoft 365 License

↓

Inherited
```

Inherited licenses cannot be removed individually.

Remove the user from the group instead.

---

# Direct + Group License

A user can have both.

Example

```
Direct

↓

Microsoft 365 E3
```

AND

```
Inherited

↓

EMS
```

Result

User receives both licenses.

---

# License Processing

Whenever:

- User joins a group
- User leaves a group
- License changes
- Group changes

Microsoft Entra automatically recalculates licensing.

Usually completed within minutes.

---

# Common Licensing Errors

---

# 1. CountViolation

Cause

Not enough licenses available.

Example

```
100 Licenses

↓

150 Users
```

Result

```
50 Users

↓

License Failed
```

Solution

- Purchase more licenses
- Remove unused licenses

---

# 2. MutuallyExclusiveViolation

Cause

Conflicting service plans.

Example

```
Office 365 E1

+

Office 365 E3
```

Both contain incompatible service configurations.

Solution

- Remove conflicting license
- Disable conflicting service plans

---

# 3. DependencyViolation

Some licenses require another license first.

Example

```
Workplace Analytics

↓

Requires

Exchange Online
```

Assign prerequisite first.

---

# 4. ProhibitedInUsageLocationViolation

Cause

Usage Location missing or unsupported.

Example

```
Usage Location

↓

Blank
```

Result

```
License Failed
```

Solution

Set:

```
Usage Location

↓

India
```

---

# 5. Duplicate Proxy Address

Occurs mostly with Exchange Online.

Example

```
Heer

↓

heer@contoso.com
```

John accidentally has

```
heer@contoso.com
```

Mailbox creation fails.

Solution

Correct duplicate SMTP address.

---

# 6. LicenseAssignmentAttributeConcurrencyException

Occurs when multiple licensing changes happen simultaneously.

Microsoft automatically retries.

Usually no administrator action required.

---

# License Reprocessing

After fixing licensing issues,

select

```
User

↓

Licenses

↓

Reprocess
```

or

```
Group

↓

Licenses

↓

Reprocess
```

Microsoft attempts license assignment again.

---

# Migrating from Direct Licensing to Group Licensing

Recommended process

Step 1

Keep existing direct licenses.

↓

Step 2

Assign license to group.

↓

Step 3

Verify users receive inherited license.

↓

Step 4

Remove direct license.

This prevents service interruption.

---

# Best Practices

✅ Use Group-Based Licensing whenever possible.

✅ Always configure Usage Location.

✅ Monitor licensing errors regularly.

✅ Remove unused licenses.

✅ Avoid assigning duplicate direct licenses.

✅ Disable unused service plans instead of buying unnecessary products.

---

# Real-World Scenario

A company hires 100 new employees.

Instead of manually assigning:

- Microsoft 365
- Intune
- Defender

Administrator performs:

```
Add Users

↓

All Employees Group

↓

Licenses Assigned Automatically
```

No manual licensing is required.

---

# AZ-104 Exam Tips

### Direct Licensing

✔ Small organizations

✔ Manual

---

### Group Licensing

✔ Automatic

✔ Recommended

✔ Scalable

---

### Usage Location

Always required before assigning licenses.

---

### Duplicate License

One user

Two groups

Same license

↓

Only ONE license consumed.

---

### License Errors

| Error | Cause |
|---------|------|
| CountViolation | Not enough licenses |
| MutuallyExclusiveViolation | Conflicting licenses |
| DependencyViolation | Missing prerequisite |
| UsageLocationViolation | Missing Usage Location |
| Duplicate Proxy Address | Duplicate SMTP |
| Concurrency Exception | Automatic retry |

---

# Quick Revision

| Concept | Remember |
|----------|----------|
| Direct Licensing | Manual assignment |
| Group Licensing | Automatic assignment |
| Usage Location | Required before licensing |
| Service Plan | Individual service inside a license |
| Duplicate License | Consumed only once |
| Inherited License | Assigned through groups |
| CountViolation | Not enough licenses |
| DependencyViolation | Missing prerequisite license |
| MutuallyExclusiveViolation | Conflicting service plans |
| Reprocess | Retry license assignment |
| Best Practice | Use Group-Based Licensing |
