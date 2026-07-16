# Azure Policy - Evaluation of Resources

## Overview

Azure Policy continuously evaluates Azure resources to ensure they comply with organizational policies.

It helps organizations:

- Enforce governance
- Prevent non-compliant deployments
- Audit existing resources
- Automatically remediate resources
- Monitor compliance across Azure

---

# Azure Policy Evaluation

After a policy or initiative is assigned, Azure Policy evaluates resources against the assigned rules.

The evaluation checks whether resources satisfy the policy conditions and assigns a compliance state.

---

# Evaluation Triggers

Azure Policy automatically evaluates resources whenever certain events occur.

## 1. New Policy Assignment

When a new policy or initiative is assigned to a scope, Azure evaluates all applicable resources.

Example:

Assign:
- Allow only India Central region

Azure immediately checks all existing resources.

---

## 2. Policy Update

If an existing policy is modified, Azure reevaluates all affected resources.

Example:

Old Policy:
- Allow Standard_B2s VM

Updated Policy:
- Allow Standard_D2s_v5 VM

Azure scans resources again.

---

## 3. Resource Creation

Whenever a new resource is deployed, Azure Policy evaluates it before or during deployment.

Example:

Developer creates a Virtual Machine.

Azure checks:

- Region
- Tags
- VM Size
- Encryption
- Other assigned policies

---

## 4. Resource Update

Whenever a resource is modified, Azure evaluates it again.

Examples:

- Resize VM
- Change Tags
- Update Configuration

---

## 5. Subscription Changes

If a subscription is:

- Created
- Moved to another Management Group

Azure Policy reevaluates applicable resources.

---

## 6. Policy Exemption Changes

If an exemption is:

- Added
- Updated
- Removed

Azure evaluates the affected resources again.

---

## 7. Manual Compliance Scan

Administrators can manually trigger a compliance scan.

Azure CLI:

```bash
az policy state trigger-scan
```

Useful when you don't want to wait for the automatic scan.

---

# Evaluation Timing

## Automatic Scan

Azure performs a full compliance scan approximately every **24 hours**.

---

## Manual Scan

Recommended when:

- New policies are assigned
- Existing environments need immediate evaluation
- Brownfield environments are onboarded

Command:

```bash
az policy state trigger-scan
```

---

# Brownfield vs Greenfield

## Greenfield

A brand-new Azure environment.

Example:

Creating a new Azure subscription.

---

## Brownfield

An existing Azure environment where policies are introduced later.

Example:

Existing environment:

- 500 Virtual Machines
- 100 Storage Accounts
- 50 Databases

A new policy is assigned to these existing resources.

---

# Policy Propagation Delay

After assigning a policy, Azure may take up to **30 minutes** before the policy becomes active.

Reason:

Azure Resource Manager caches session data.

Possible workaround:

- Sign out
- Sign back in

---

# Factors Affecting Scan Time

Compliance scans may take longer depending on:

- Number of resources
- Number of policies
- Policy complexity
- Scope size
- Azure system load

Compliance scans run with **low priority**, so interactive Azure operations take precedence.

---

# Resource Compliance States

After evaluation, Azure assigns one compliance state to every resource.

---

## 1. Compliant

The resource follows the policy.

Example:

Policy:

Only India Central region allowed.

VM Location:

India Central

Result:

Compliant

---

## 2. Non-Compliant

The resource violates the policy.

Example:

Policy:

Only India Central region allowed.

VM Location:

West US

Result:

Non-Compliant

---

## 3. Error

Azure couldn't evaluate the resource.

Possible causes:

- Invalid policy
- Template error
- Evaluation failure

---

## 4. Conflicting

Two or more policies conflict.

Example:

Policy A:

Environment = Production

Policy B:

Environment = Development

Azure cannot satisfy both.

---

## 5. Protected

The resource is protected by a policy using the **denyAction** effect.

Example:

Prevent deletion of production databases.

---

## 6. Exempted

The resource has an approved policy exemption.

Azure does not evaluate that resource for the exempted policy.

---

## 7. Unknown

Azure cannot automatically determine compliance.

Usually occurs with manual policies.

Example:

Policy:

Disaster Recovery documentation exists.

Azure cannot verify documents automatically.

---

# Compliance Percentage

Azure calculates overall compliance.

Formula:

```
Compliance %

=

(Compliant + Exempt + Unknown)

------------------------------------

Total Resources
```

Total resources include:

- Compliant
- Non-Compliant
- Error
- Conflicting
- Protected
- Exempt
- Unknown

---

# Enforcement Mode

Enforcement Mode determines whether Azure only evaluates policies or also enforces them.

---

## Enabled (Default)

Policy is evaluated and enforced.

Effects such as:

- Deny
- Modify
- DeployIfNotExists

are applied.

Example:

Developer creates VM in an unauthorized region.

Deployment is blocked.

---

## Disabled (DoNotEnforce)

Azure still evaluates the policy but does not enforce it.

Effects:

✔ Compliance evaluation

✔ Compliance reporting

❌ No denial

❌ No policy action

Used for testing ("What If" scenarios).

---

# Enforcement Mode vs Disabled Effect

## Enforcement Mode Disabled

- Policy is evaluated
- Compliance results are available
- Policy effect is NOT enforced

---

## Disabled Effect

- Policy is NOT evaluated
- No compliance data
- Policy is completely inactive

---

# Safe Deployment Best Practices

Never deploy strict policies directly to production.

Microsoft recommends gradual deployment.

---

## Step 1

Create the policy definition.

---

## Step 2

Assign policy with:

Enforcement Mode = Disabled

Observe compliance without blocking resources.

---

## Step 3

Perform:

- Compliance check
- Application health check

Ensure workloads continue functioning correctly.

---

## Step 4

Enable enforcement for a small test environment.

---

## Step 5

Gradually expand deployment using Deployment Rings.

Example:

Ring 5

↓

Test Environment

↓

Development

↓

QA

↓

Staging

↓

Production

---

# Deployment Rings

Deployment Rings reduce deployment risk.

Advantages:

- Detect issues early
- Protect production workloads
- Gradually increase policy coverage
- Easier rollback if problems occur

---

# Reacting to Policy State Changes

Azure Policy can automatically notify other Azure services whenever compliance changes.

Flow:

```
Resource Changes

↓

Azure Policy Evaluation

↓

Compliance State Changes

↓

Azure Event Grid

↓

Event Handler

↓

Automation
```

---

# Event Grid

Azure Event Grid routes policy events to other services.

Examples:

- Azure Functions
- Logic Apps
- Webhooks
- Custom Applications

---

# Example Workflow

Developer deploys a VM without required tags.

↓

Azure Policy detects non-compliance.

↓

Azure Event Grid publishes an event.

↓

Logic App receives event.

↓

Administrator receives email notification.

---

# Key Exam Points

✔ Azure Policy automatically evaluates resources.

✔ Evaluation occurs when:

- Policy is assigned
- Policy is updated
- Resource is created
- Resource is modified
- Subscription changes
- Exemption changes
- Manual scan starts

✔ Automatic compliance scan runs approximately every **24 hours**.

✔ Manual scan command:

```bash
az policy state trigger-scan
```

✔ Brownfield = Existing environment

✔ Greenfield = New environment

✔ Compliance States:

- Compliant
- Non-Compliant
- Error
- Conflicting
- Protected
- Exempted
- Unknown

✔ Enforcement Mode Disabled = Evaluate only (What If mode)

✔ Disabled Effect = No evaluation at all

✔ Azure recommends gradual deployment using Deployment Rings.

✔ Azure Event Grid reacts to Azure Policy compliance state changes.

✔ Event handlers include:

- Azure Functions
- Logic Apps
- Webhooks
- Custom Applications
