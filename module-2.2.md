# Azure Policy Study Guide – Part 1
## Introduction, Architecture, Policy Definitions, Effects, and Scope

> **Target Exam:** AZ-104 Azure Administrator

---

# Table of Contents

1. What is Azure Policy?
2. Why Azure Policy?
3. Azure Policy Architecture
4. Azure Policy Workflow
5. Azure Policy Resources
6. Policy Definitions
7. Built-in vs Custom Policies
8. Policy Definition Structure
9. Policy Effects
10. Scope in Azure Policy
11. Policy Inheritance
12. Real-World Example
13. AZ-104 Exam Tips

---

# 1. What is Azure Policy?

**Azure Policy** is an Azure governance service that helps organizations enforce standards, assess compliance, and automatically manage Azure resources according to organizational requirements.

Azure Policy evaluates Azure resources against business rules (called **policy definitions**) and ensures resources remain compliant throughout their lifecycle.

It can:

- Audit resources
- Deny non-compliant deployments
- Modify resource properties
- Deploy required configurations automatically
- Report compliance status

---

# 2. Why Azure Policy?

Without governance:

- Resources are deployed inconsistently.
- Security standards vary across teams.
- Costs become difficult to control.
- Regulatory compliance becomes harder to maintain.
- Manual administration increases.

Azure Policy provides **centralized governance**, helping organizations enforce standards consistently across Azure environments.

---

# 3. Azure Policy Architecture

```
Administrator
      │
      ▼
Creates Policy Definition
      │
      ▼
Assigns Policy
      │
      ▼
Azure Policy Engine
      │
      ▼
Evaluates Azure Resources
      │
      ▼
Determines Compliance State
      │
      ▼
Applies Policy Effect
(Audit / Deny / Modify / DeployIfNotExists / etc.)
```

---

# 4. Azure Policy Workflow

The Azure Policy workflow consists of the following steps:

1. Create a **Policy Definition**.
2. Assign the policy to a scope.
3. Azure Policy evaluates existing and newly created resources.
4. Resources are marked as:
   - Compliant
   - Non-compliant
5. The configured **policy effect** is applied.
6. Compliance results are displayed in Azure Policy.

---

# 5. Azure Policy Resources

| Resource | Description |
|----------|-------------|
| **Policy Definition** | A single governance rule that defines compliance requirements. |
| **Initiative (Policy Set Definition)** | A collection of multiple policy definitions grouped together. |
| **Assignment** | Applies a policy definition or initiative to a specific scope. |
| **Exemption** | Excludes specific resources or scopes from policy enforcement. |
| **Attestation** | Allows manual confirmation of compliance for exempted resources. |
| **Remediation** | Corrects existing non-compliant resources using managed identities and remediation tasks. |

---

# 6. Policy Definitions

A **Policy Definition** contains the logic that Azure uses to evaluate resources.

A policy definition typically includes:

- Display Name
- Description
- Mode
- Parameters
- Policy Rule
- Effect

### Example

**Condition**

```
Location != Central India
```

**Effect**

```
Deny
```

This policy blocks deployments outside the Central India region.

---

# 7. Built-in vs Custom Policies

## Built-in Policies

Built-in policies are created and maintained by Microsoft.

Examples:

- Allowed locations
- Allowed virtual machine sizes
- Require resource tags
- Require storage encryption

### Advantages

- Ready to use
- Microsoft-supported
- Regularly updated
- Best practices included

---

## Custom Policies

Custom policies are created by your organization when built-in policies do not satisfy business requirements.

### Example

```
All virtual machine names must begin with "HP-".
```

Custom policies provide flexibility for organization-specific governance requirements.

---

# 8. Policy Definition Structure

A policy definition is generally composed of the following elements:

| Component | Purpose |
|-----------|---------|
| Display Name | Friendly name shown in Azure Portal |
| Description | Explains the purpose of the policy |
| Mode | Specifies which resource types are evaluated |
| Parameters | Makes the policy reusable by accepting user-defined values |
| Policy Rule | Contains the conditions that Azure evaluates |
| Effect | Specifies the action Azure takes when conditions are met |

---

# 9. Policy Effects

Policy effects determine what Azure does when a resource matches the policy rule.

## Audit

- Reports non-compliant resources.
- Does **not** block deployments.

---

## Deny

- Prevents deployment or updates.
- Resource creation fails if it violates the policy.

---

## Append

- Adds additional properties during deployment.
- Commonly used to append tags or settings.

---

## Modify

- Automatically updates resource properties.
- Often used to add or correct tags.

---

## DeployIfNotExists

- Automatically deploys required resources after deployment.
- Frequently used to deploy monitoring agents or diagnostic settings.

---

## AuditIfNotExists

- Checks whether a required related resource exists.
- Reports non-compliance if the resource is missing.

---

## Disabled

- Turns the policy off.
- No evaluation or enforcement occurs.

---

## Policy Effects Comparison

| Effect | Blocks Deployment | Reports Compliance | Automatic Action |
|---------|------------------|--------------------|------------------|
| Audit | ❌ No | ✅ Yes | ❌ No |
| Deny | ✅ Yes | ✅ Yes | ❌ No |
| Append | ❌ No | ✅ Yes | ✅ Yes |
| Modify | ❌ No | ✅ Yes | ✅ Yes |
| DeployIfNotExists | ❌ No | ✅ Yes | ✅ Yes |
| AuditIfNotExists | ❌ No | ✅ Yes | ❌ No |
| Disabled | ❌ No | ❌ No | ❌ No |

---

# 10. Scope in Azure Policy

Azure Policy can be assigned at different levels within the Azure hierarchy.

Supported scopes include:

1. Management Group
2. Subscription
3. Resource Group
4. Individual Resource

---

# 11. Policy Inheritance

Policies assigned at higher levels automatically apply to lower levels.

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

For example:

- A policy assigned at the **Management Group** level applies to all subscriptions, resource groups, and resources beneath it.
- A policy assigned at the **Subscription** level applies to all resource groups and resources within that subscription.

---

# 12. Real-World Example

### Scenario

An organization wants to allow deployments **only in Central India**.

Policy:

```
Allowed Location = Central India
Effect = Deny
```

### Deployment Attempt

A developer attempts to deploy a virtual machine in **West Europe**.

### Result

The deployment fails because it violates the assigned Azure Policy.

---

# 13. AZ-104 Exam Tips

Remember these key concepts:

- **Policy Definition** = A single governance rule.
- **Initiative** = A collection of multiple policy definitions.
- **Assignment** = Applies a policy or initiative to a scope.
- **Scope** determines where the policy is enforced.
- **Deny** blocks deployments.
- **Audit** only reports non-compliance.
- **Modify** automatically updates resource properties.
- **DeployIfNotExists** deploys required resources automatically.
- **AuditIfNotExists** checks whether required related resources exist.
- **Built-in Policies** are created and maintained by Microsoft.
- **Custom Policies** are created by organizations to meet specific business requirements.

---

# Quick Revision

| Concept | Remember |
|----------|----------|
| Policy Definition | A single rule |
| Initiative | Collection of policies |
| Assignment | Applies policies |
| Scope | Where the policy applies |
| Audit | Report only |
| Deny | Block deployment |
| Modify | Update resources automatically |
| DeployIfNotExists | Deploy missing resources |
| Built-in Policy | Created by Microsoft |
| Custom Policy | Created by the organization |

---

# Summary

Azure Policy is a governance service that helps organizations enforce standards, maintain compliance, and automate resource management across Azure environments. By defining policies, assigning them to appropriate scopes, and using policy effects such as **Audit**, **Deny**, **Modify**, and **DeployIfNotExists**, administrators can ensure Azure resources comply with organizational and regulatory requirements.

---

**End of Part 1**

### Next Part

- Policy Initiatives (Policy Set Definitions)
- Policy Assignments
- Policy Exemptions
- Policy Attestations
- Remediation Tasks
- Policy Evaluation Cycle
- Compliance Dashboard
- Azure Policy Best Practices
