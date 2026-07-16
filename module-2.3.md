# Azure RBAC Study Guide
## Secure Your Azure Resources with Azure Role-Based Access Control (Azure RBAC)

> **Target Exam:** AZ-104 Azure Administrator

---

# Table of Contents

1. Introduction to Azure RBAC
2. Why Azure RBAC?
3. Authentication vs Authorization
4. How Azure RBAC Works
5. Key Components of Azure RBAC
6. Azure RBAC Scope
7. Azure Built-in Roles
8. Custom Roles
9. Role Assignments
10. Azure RBAC Inheritance
11. Azure RBAC Conditions
12. Azure RBAC and Microsoft Entra ID
13. Managed Identities in Azure RBAC
14. Privileged Identity Management (PIM)
15. Azure RBAC vs Azure Policy
16. Best Practices
17. Real-World Examples
18. AZ-104 Exam Tips
19. Quick Revision
20. Summary

---

# 1. Introduction to Azure RBAC

**Azure Role-Based Access Control (Azure RBAC)** is Azure's authorization system used to control access to Azure resources.

It enables organizations to determine:

- **Who** can access Azure resources.
- **What** actions they can perform.
- **Where** those permissions apply.

Azure RBAC follows the **Principle of Least Privilege**, ensuring users receive only the permissions required to perform their jobs.

---

## Example

A developer should be able to:

- Create virtual machines
- Restart virtual machines
- View resource groups

But should **not** be able to:

- Delete subscriptions
- Assign roles to other users
- Change billing settings

Azure RBAC allows administrators to grant exactly these permissions.

---

# 2. Why Azure RBAC?

Without Azure RBAC:

- Users may receive excessive permissions.
- Resources become vulnerable to accidental or malicious changes.
- Security risks increase.
- Compliance requirements become difficult to maintain.

Azure RBAC provides:

- Fine-grained access control
- Centralized permission management
- Improved security
- Compliance with organizational policies
- Principle of Least Privilege

---

# 3. Authentication vs Authorization

Authentication and authorization are different concepts.

## Authentication

Authentication verifies **who you are**.

Azure uses **Microsoft Entra ID** (formerly Azure Active Directory) to authenticate users.

Examples:

- Username and password
- Multi-Factor Authentication (MFA)
- Certificate
- Service Principal
- Managed Identity

---

## Authorization

Authorization determines **what you are allowed to do** after authentication.

Azure RBAC performs authorization.

Example:

A user successfully signs in using Microsoft Entra ID.

Azure RBAC determines whether that user can:

- Create a virtual machine
- Delete a storage account
- View a Key Vault

---

## Authentication vs Authorization

| Authentication | Authorization |
|---------------|---------------|
| Confirms identity | Determines permissions |
| Performed by Microsoft Entra ID | Performed by Azure RBAC |
| Happens first | Happens after authentication |
| "Who are you?" | "What can you do?" |

---

# 4. How Azure RBAC Works

Azure RBAC grants permissions using **Role Assignments**.

A role assignment consists of three components:

```
Security Principal
        +
Role Definition
        +
      Scope
        =
Role Assignment
```

When a user attempts to access a resource:

1. Microsoft Entra ID authenticates the user.
2. Azure RBAC checks role assignments.
3. Azure determines whether the requested action is allowed.
4. Access is granted or denied.

---

## Azure RBAC Workflow

```
User Requests Access
          │
          ▼
Authenticate with Microsoft Entra ID
          │
          ▼
Azure RBAC Evaluates Role Assignments
          │
          ▼
Permission Available?
      │             │
     Yes            No
      │             │
      ▼             ▼
Allow Access    Deny Access
```

---

# 5. Key Components of Azure RBAC

Every Azure RBAC role assignment contains three essential components.

---

## 1. Security Principal

A security principal is the identity receiving permissions.

Types include:

- User
- Group
- Service Principal
- Managed Identity

---

## 2. Role Definition

A role definition is a collection of permissions.

It specifies:

- Allowed actions
- Not allowed actions
- Data actions
- Not data actions

Examples:

- Read
- Write
- Delete
- Start virtual machines
- Restart virtual machines

---

## 3. Scope

Scope determines where the role applies.

Possible scopes:

- Management Group
- Subscription
- Resource Group
- Individual Resource

---

# 6. Azure RBAC Scope

Azure RBAC permissions can be assigned at different levels.

```
Management Group
        │
        ▼
Subscription
        │
        ▼
Resource Group
        │
        ▼
Resource
```

Permissions assigned at higher levels are inherited by lower levels.

---

## Scope Comparison

| Scope | Applies To |
|--------|------------|
| Management Group | Multiple subscriptions |
| Subscription | Entire subscription |
| Resource Group | Resources inside the resource group |
| Resource | Single Azure resource |

---

# 7. Azure Built-in Roles

Azure provides more than **100 built-in roles**.

The most common AZ-104 roles are:

---

## Owner

The **Owner** has full access to all resources.

Can also:

- Assign Azure RBAC roles
- Delete resources
- Create resources
- Modify resources

Highest level of access.

---

## Contributor

The **Contributor** can manage resources but **cannot assign Azure RBAC roles**.

Can:

- Create resources
- Update resources
- Delete resources

Cannot:

- Manage access

---

## Reader

The **Reader** role provides read-only access.

Can:

- View resources
- View settings
- View configurations

Cannot:

- Create resources
- Modify resources
- Delete resources

---

## User Access Administrator

Can manage access to Azure resources.

Can:

- Assign roles
- Remove role assignments
- Manage Azure RBAC permissions

Cannot:

- Manage Azure resources unless another role grants those permissions.

---

## Common Built-in Roles

| Role | Create | Modify | Delete | Assign Roles |
|------|:------:|:------:|:------:|:------------:|
| Owner | ✅ | ✅ | ✅ | ✅ |
| Contributor | ✅ | ✅ | ✅ | ❌ |
| Reader | ❌ | ❌ | ❌ | ❌ |
| User Access Administrator | ❌ | ❌ | ❌ | ✅ |

---

# 8. Custom Roles

Built-in roles may not always meet organizational requirements.

Azure allows administrators to create **Custom Roles**.

Custom roles define:

- Allowed actions
- Denied actions
- Assignable scopes

---

## Example

A custom role allows:

- Start virtual machines
- Restart virtual machines

But does **not** allow:

- Delete virtual machines

---

# 9. Role Assignments

A **Role Assignment** grants a role to a security principal at a specific scope.

A role assignment contains:

- Security Principal
- Role Definition
- Scope

Example:

```
User:
John

Role:
Contributor

Scope:
Production Resource Group
```

John can manage resources only within the Production Resource Group.

---

# 10. Azure RBAC Inheritance

Permissions assigned at higher scopes automatically flow down.

```
Management Group
        │
        ▼
Subscription
        │
        ▼
Resource Group
        │
        ▼
Resource
```

Example:

Contributor assigned at the Subscription level automatically applies to:

- All Resource Groups
- All Resources within the subscription

---

# 11. Azure RBAC Conditions

Azure RBAC Conditions provide additional control over role assignments.

Conditions allow administrators to restrict permissions based on specific attributes.

Examples:

- Resource type
- Resource name
- Blob container
- Storage object

Conditions provide **attribute-based access control (ABAC)** for supported services.

---

## Example

Allow a user to upload blobs only to a specific storage container.

Without conditions:

User can upload anywhere.

With conditions:

User can upload only to:

```
Container = invoices
```

---

# 12. Azure RBAC and Microsoft Entra ID

Microsoft Entra ID and Azure RBAC work together.

Microsoft Entra ID:

- Authenticates identities.

Azure RBAC:

- Determines permissions.

Workflow:

```
User Sign-In
      │
      ▼
Microsoft Entra ID Authentication
      │
      ▼
Azure RBAC Authorization
      │
      ▼
Access Granted or Denied
```

---

# 13. Managed Identities in Azure RBAC

A **Managed Identity** is an automatically managed identity for Azure resources.

Managed identities eliminate the need to store credentials in applications.

Azure RBAC can assign roles directly to managed identities.

Example:

A Virtual Machine accesses Azure Key Vault using a managed identity instead of storing secrets.

---

# 14. Privileged Identity Management (PIM)

**Microsoft Entra Privileged Identity Management (PIM)** provides just-in-time privileged access.

Features:

- Temporary role activation
- Approval workflows
- MFA before activation
- Audit logs
- Time-limited permissions

Benefits:

- Reduces standing administrative privileges.
- Improves security.

---

# 15. Azure RBAC vs Azure Policy

These services are commonly confused.

| Azure RBAC | Azure Policy |
|------------|--------------|
| Controls access | Enforces governance |
| Authorization | Compliance |
| Determines who can perform actions | Determines whether resources meet organizational standards |
| Grants permissions | Evaluates and enforces rules |
| Uses roles | Uses policy definitions |

---

## Example

Azure RBAC:

Developer can create a virtual machine.

Azure Policy:

Developer can create a virtual machine **only in Central India**.

Both services work together.

---

# 16. Best Practices

Microsoft recommends:

- Follow the Principle of Least Privilege.
- Assign roles to groups instead of individual users.
- Use built-in roles whenever possible.
- Create custom roles only when necessary.
- Use PIM for privileged roles.
- Regularly review role assignments.
- Remove unused permissions.
- Avoid assigning the Owner role unless absolutely necessary.

---

# 17. Real-World Examples

### Example 1

Assign the **Reader** role to the Finance team.

Result:

They can view resources but cannot modify them.

---

### Example 2

Assign the **Contributor** role to developers.

Result:

They can manage virtual machines but cannot grant access to others.

---

### Example 3

Assign the **Owner** role to the cloud administrator.

Result:

The administrator has complete control over the subscription.

---

# 18. AZ-104 Exam Tips

Remember these important concepts:

- Azure RBAC controls **authorization**, not authentication.
- Microsoft Entra ID performs authentication.
- A role assignment requires:
  - Security Principal
  - Role Definition
  - Scope
- Permissions inherit from higher scopes.
- Owner can assign roles.
- Contributor cannot assign roles.
- Reader is read-only.
- User Access Administrator manages access.
- Managed Identities eliminate stored credentials.
- PIM provides temporary privileged access.
- Azure RBAC controls **who can perform actions**.
- Azure Policy controls **what resources are allowed**.

---

# 19. Quick Revision

| Concept | Remember |
|----------|----------|
| Azure RBAC | Authorization |
| Microsoft Entra ID | Authentication |
| Security Principal | User, Group, Service Principal, Managed Identity |
| Role Assignment | Principal + Role + Scope |
| Owner | Full access + Manage access |
| Contributor | Manage resources only |
| Reader | View only |
| User Access Administrator | Manage access only |
| Custom Role | Organization-defined permissions |
| PIM | Just-in-Time access |
| Managed Identity | Passwordless Azure identity |
| Azure Policy | Governance |
| Azure RBAC | Permissions |

---

# 20. Summary

Azure Role-Based Access Control (Azure RBAC) is Azure's authorization system that enables organizations to securely manage access to Azure resources. By combining **Security Principals**, **Role Definitions**, and **Scopes**, administrators can implement the Principle of Least Privilege and ensure users have only the permissions necessary to perform their jobs.

Azure RBAC works closely with Microsoft Entra ID, supports built-in and custom roles, integrates with Managed Identities and Privileged Identity Management (PIM), and forms the foundation of secure access management in Azure. When used together with Azure Policy, Azure RBAC helps organizations build secure, compliant, and well-governed Azure environments.

---

**End of Azure RBAC Study Guide**

### Related Topics

- Microsoft Entra ID (Identity & Access Management)
- Azure Policy
- Resource Locks
- Azure Blueprints (Legacy)
- Azure Managed Identities
- Privileged Identity Management (PIM)
- Azure Key Vault Access Control
