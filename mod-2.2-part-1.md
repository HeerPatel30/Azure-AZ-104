# Azure Policy Study Guide – Part 2
## Initiatives, Assignments, Exemptions, Attestations, and Remediation

> **Target Exam:** AZ-104 Azure Administrator

---

# Table of Contents

1. Azure Policy Initiatives
2. Built-in vs Custom Initiatives
3. Policy Assignments
4. Assignment Properties
5. Enforcement Mode
6. Policy Exemptions
7. Policy Attestations
8. Policy Remediation
9. End-to-End Azure Policy Workflow
10. AZ-104 Exam Tips
11. Quick Revision
12. Summary

---

# 1. Azure Policy Initiatives

An **Initiative**, also known as a **Policy Set Definition**, is a collection of multiple policy definitions managed as a single unit.

Instead of assigning several individual policies separately, administrators can group related policies into one initiative and assign it once.

This simplifies policy management and helps organizations enforce multiple governance requirements consistently.

---

## Example

### Security Initiative

An organization creates a **Security Initiative** that includes the following policies:

- Require resource tags
- Allow deployments only in approved regions
- Enable diagnostic settings
- Require storage encryption
- Restrict virtual machine sizes

Once the initiative is assigned, **all included policies are evaluated and enforced automatically**.

---

## Benefits of Initiatives

- Simplifies policy management
- Reduces administrative effort
- Provides consistent governance
- Improves compliance reporting
- Supports regulatory frameworks
- Enables centralized management of related policies

---

# 2. Built-in vs Custom Initiatives

## Built-in Initiatives

Built-in initiatives are created and maintained by Microsoft.

They are designed to help organizations meet common security, governance, and regulatory requirements.

### Examples

- ISO 27001
- NIST
- PCI-DSS
- Azure Security Benchmark
- CIS Controls

### Advantages

- Ready to use
- Microsoft-maintained
- Updated automatically
- Align with industry standards

---

## Custom Initiatives

Custom initiatives are created by your organization.

They can include:

- Built-in policies
- Custom policies
- A combination of both

### Example

**Company Governance Initiative**

Contains policies such as:

- Allowed locations
- Mandatory resource tags
- Backup required
- HTTPS required
- Storage encryption enabled

This allows the organization to enforce all governance requirements using a single assignment.

---

# 3. Policy Assignments

Creating a policy definition or initiative **does not enforce anything** until it is assigned.

A **Policy Assignment** applies a policy or initiative to a specific scope within Azure.

An assignment specifies:

- Which policy or initiative to apply
- Where it applies (scope)
- Parameter values
- Enforcement mode
- Excluded scopes
- Managed identity (if required)

---

## Supported Assignment Scopes

Policies and initiatives can be assigned at:

1. Management Group
2. Subscription
3. Resource Group
4. Individual Resource

Policies assigned at higher levels are inherited by lower levels.

---

# 4. Assignment Properties

A policy assignment includes several important properties.

---

## Scope

Defines where the policy is applied.

Possible scopes include:

- Management Group
- Subscription
- Resource Group
- Resource

---

## Parameters

Parameters make policy definitions reusable by allowing administrators to supply different values during assignment.

### Example

```
Allowed Location = Central India
```

The same policy definition can later be assigned using different regions.

---

## Resource Selectors

Resource selectors allow administrators to gradually roll out policies.

Policies can target resources based on:

- Resource type
- Resource location
- Resource characteristics

This enables phased deployments instead of applying policies to every resource immediately.

---

## Excluded Scopes

Excluded scopes prevent specific resources from being evaluated by a policy assignment.

Examples include:

- Subscription
- Resource Group
- Individual Resource

Excluded scopes are configured during assignment creation.

---

## Overrides

Overrides temporarily change how a policy behaves without modifying the original policy definition.

For example:

- Change **Deny** to **Audit**
- Disable one policy within an initiative

Overrides are useful during testing or phased deployments.

---

## Noncompliance Messages

Administrators can define custom messages that users see when a deployment violates a policy.

### Example

```
Virtual machines must be deployed only in Central India.
```

Custom messages help users understand why a deployment failed.

---

## Managed Identity

Some policy effects require Azure Policy to modify resources automatically.

These include:

- Modify
- DeployIfNotExists

For these effects, Azure creates or uses a **Managed Identity** to perform remediation actions on behalf of Azure Policy.

---

# 5. Enforcement Mode

Enforcement Mode determines whether policy effects are enforced.

---

## Enabled (Default)

When enforcement mode is **Enabled**:

- Resources are evaluated.
- Policy effects are enforced.
- Non-compliant deployments may be denied.
- Automatic modifications can occur.

---

## DoNotEnforce

When enforcement mode is **DoNotEnforce**:

- Resources are still evaluated.
- Compliance information is collected.
- No enforcement occurs.
- Deployments are not blocked.
- No automatic modifications are performed.

This mode is useful for testing policies before enforcing them in production.

---

## Comparison

| Enforcement Mode | Evaluate Resources | Apply Policy Effects |
|------------------|-------------------|----------------------|
| Enabled | ✅ Yes | ✅ Yes |
| DoNotEnforce | ✅ Yes | ❌ No |

---

# 6. Policy Exemptions

Policy Exemptions allow specific resources or scopes to be exempt from policy enforcement **after a policy has already been assigned**.

Unlike excluded scopes, exemptions are tracked and included in compliance reporting.

This provides visibility into why a resource is intentionally non-compliant.

---

## Types of Exemptions

### Waiver

A **Waiver** temporarily accepts non-compliance.

### Example

A legacy virtual machine cannot currently meet encryption requirements because it is scheduled for replacement next month.

---

### Mitigated

A **Mitigated** exemption indicates that the policy requirement has already been satisfied through another control.

### Example

A third-party encryption solution already protects the resource, making Azure's encryption requirement unnecessary.

---

## Excluded Scope vs Exemption

| Excluded Scope | Exemption |
|---------------|-----------|
| Configured during assignment | Created after assignment |
| Resource isn't evaluated | Resource is evaluated but exempted |
| Not tracked as an exemption | Fully tracked for auditing |
| Used for permanent exclusions | Often used for temporary or justified exceptions |

---

# 7. Policy Attestations

Some compliance requirements cannot be verified automatically by Azure Policy.

In these situations, administrators can manually confirm compliance using **Policy Attestations**.

---

## Examples

- Disaster Recovery documentation completed
- Business approval obtained
- Operational procedures followed
- Security review completed
- Compliance documentation approved

Attestations allow organizations to record manual compliance evidence.

---

# 8. Policy Remediation

Policy Remediation brings **existing non-compliant resources** into compliance.

Without remediation, Azure Policy only evaluates resources and reports compliance.

Remediation tasks automatically apply required changes when supported.

---

## Supported Effects

Remediation is primarily used with:

- Modify
- DeployIfNotExists

---

## Example

A policy requires every resource to have an **Owner** tag.

Several virtual machines were created before the policy existed.

A remediation task automatically adds the missing **Owner** tag to those existing virtual machines.

---

## Remediation Process

```
Policy detects non-compliance
          │
          ▼
Create Remediation Task
          │
          ▼
Managed Identity Performs Changes
          │
          ▼
Resource Becomes Compliant
```

---

# 9. End-to-End Azure Policy Workflow

The complete Azure Policy lifecycle is shown below.

```
Create Policy Definition
            │
            ▼
(Optional) Add to Initiative
            │
            ▼
Assign Policy or Initiative
            │
            ▼
Azure Policy Evaluates Resources
            │
            ▼
Is Resource Compliant?
      │              │
     Yes             No
      │              │
      ▼              ▼
Marked         Exemption Exists?
Compliant        │          │
                 Yes        No
                  │          │
                  ▼          ▼
          Exempt Resource  Apply Policy Effect
                               │
                               ▼
                     Remediation (if supported)
                               │
                               ▼
                     Compliance Status Updated
```

---

# 10. AZ-104 Exam Tips

Remember these key concepts:

- **Initiative** = Collection of policy definitions.
- **Policy Assignment** activates a policy or initiative.
- **Policy Definition** alone does nothing until assigned.
- **Exemptions** are created after assignment.
- **Waiver** = Temporary acceptance of non-compliance.
- **Mitigated** = Requirement satisfied through another control.
- **Remediation** fixes existing non-compliant resources.
- **Managed Identity** is required for Modify and DeployIfNotExists remediation.
- **DoNotEnforce** evaluates resources but does not enforce policy effects.
- **Resource Selectors** help gradually deploy policies.
- **Overrides** temporarily change policy behavior without editing the policy definition.
- **Noncompliance Messages** provide custom deployment error messages.

---

# 11. Quick Revision

| Concept | Remember |
|----------|----------|
| Initiative | Group of policy definitions |
| Assignment | Activates a policy |
| Parameters | Make policies reusable |
| Resource Selectors | Gradual rollout |
| Excluded Scope | Ignored during evaluation |
| Exemption | Tracked exception after assignment |
| Waiver | Temporary exception |
| Mitigated | Requirement met another way |
| Managed Identity | Required for remediation |
| Modify | Updates existing resources |
| DeployIfNotExists | Deploys missing resources |
| DoNotEnforce | Evaluate only |

---

# 12. Summary

Azure Policy Initiatives simplify governance by grouping multiple policy definitions into a single unit. Policies become active only after they are assigned to an appropriate scope. Assignment properties such as parameters, resource selectors, overrides, excluded scopes, and enforcement mode provide flexibility in how policies are applied.

Organizations can use exemptions to document approved exceptions, attestations to record manual compliance, and remediation tasks to automatically correct existing non-compliant resources. Together, these features enable consistent governance, improved compliance, and automated management of Azure resources.

---

**End of Part 2**

### Next Part

- Azure Policy JSON Structure
- Policy Rule Components
- Conditions and Logical Operators
- Parameters in Depth
- Azure Policy Modes
- Resource Providers
- Compliance Evaluation Cycle
- Azure Policy Lifecycle
- Best Practices
- AZ-104 Practice Questions
