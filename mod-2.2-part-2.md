# Azure Policy Study Guide – Part 3
## Policy Evaluation, Compliance, Enforcement, Safe Deployment, and Event Grid

> **Target Exam:** AZ-104 Azure Administrator

---

# Table of Contents

1. Azure Policy Evaluation
2. Evaluation Triggers
3. Evaluation Timing
4. Brownfield vs Greenfield Environments
5. Compliance States
6. Compliance Percentage
7. Enforcement Mode (Deep Dive)
8. Disabled Effect vs DoNotEnforce
9. Safe Deployment Best Practices
10. Deployment Rings
11. Azure Event Grid Integration
12. End-to-End Evaluation Workflow
13. AZ-104 Exam Tips
14. Quick Revision
15. Summary

---

# 1. Azure Policy Evaluation

Azure Policy continuously evaluates Azure resources against assigned **policy definitions** and **initiatives** to determine whether they comply with organizational requirements.

Evaluation occurs whenever policies are assigned, resources change, or compliance scans are triggered.

The evaluation process includes the following steps:

1. Identify applicable resources within the assignment scope.
2. Ignore excluded scopes and exempted resources.
3. Compare resource properties with policy rules.
4. Determine the compliance state.
5. Apply the configured policy effect (if enforcement is enabled).
6. Update the Azure Policy compliance dashboard.

---

## Azure Policy Evaluation Process

```
Policy Assignment
        │
        ▼
Identify Applicable Resources
        │
        ▼
Ignore Excluded & Exempted Resources
        │
        ▼
Evaluate Policy Rules
        │
        ▼
Determine Compliance State
        │
        ▼
Apply Policy Effect (if enabled)
        │
        ▼
Update Compliance Dashboard
```

---

# 2. Evaluation Triggers

Azure Policy automatically evaluates resources in several situations.

Evaluation is triggered when:

- A new policy assignment is created.
- An existing policy assignment is updated.
- A policy definition is updated.
- A resource is created.
- A resource is modified.
- A subscription is created.
- A subscription is moved to another Management Group.
- A policy exemption is created, modified, or removed.
- A manual compliance scan is initiated.
- Azure Machine Configuration reports updated compliance information.

These triggers ensure that compliance information remains current.

---

# 3. Evaluation Timing

Azure Policy performs evaluations automatically and manually.

## Automatic Evaluation

Azure automatically performs a compliance scan approximately **every 24 hours**.

This periodic scan detects configuration drift and updates compliance results.

---

## Manual Evaluation

Administrators can trigger an on-demand compliance scan.

### Azure CLI

```bash
az policy state trigger-scan
```

This command forces Azure Policy to re-evaluate resources immediately.

---

## Policy Propagation

After assigning or updating a policy, Azure Resource Manager needs time to distribute the assignment.

Typical propagation time:

- Up to **30 minutes**

During this period, newly assigned policies may not yet appear in compliance reports.

---

# 4. Brownfield vs Greenfield Environments

Understanding these deployment models is important for governance planning.

---

## Greenfield Environment

A **Greenfield** environment is a completely new Azure deployment.

### Characteristics

- No existing resources
- Governance implemented from the beginning
- Easier to maintain compliance

### Example

A newly created Azure subscription where Azure Policy is assigned before any resources are deployed.

---

## Brownfield Environment

A **Brownfield** environment already contains deployed Azure resources.

Governance is introduced after resources already exist.

### Characteristics

- Existing infrastructure
- Existing resources may be non-compliant
- Often requires remediation tasks

### Example

An organization has 500 virtual machines deployed before Azure Policy is introduced.

---

## Comparison

| Greenfield | Brownfield |
|------------|------------|
| New environment | Existing environment |
| Governance implemented early | Governance added later |
| Few compliance issues | Existing non-compliance likely |
| Minimal remediation required | Remediation often required |

---

# 5. Compliance States

After evaluation, Azure Policy assigns a compliance state to each applicable resource.

---

## Compliant

The resource satisfies all policy requirements.

---

## Non-Compliant

The resource violates one or more policy rules.

---

## Error

Azure Policy was unable to evaluate the resource due to an error.

---

## Conflicting

Multiple policies produce conflicting evaluation results for the same resource.

---

## Protected

The resource is protected by a **denyAction** policy.

---

## Exempted

The resource has an approved policy exemption.

Although it may not satisfy the policy, it is treated as an approved exception.

---

## Unknown

Azure Policy cannot automatically determine compliance.

This commonly occurs when manual verification or external validation is required.

---

## Compliance State Priority

When multiple compliance states apply, Azure prioritizes them in the following order:

| Priority | Compliance State |
|----------|------------------|
| 1 | Non-Compliant |
| 2 | Compliant |
| 3 | Error |
| 4 | Conflicting |
| 5 | Protected |
| 6 | Exempted |
| 7 | Unknown |

---

# 6. Compliance Percentage

Azure calculates an overall compliance score based on applicable resources.

In general:

- ✅ Compliant resources increase the compliance percentage.
- ✅ Exempted resources are treated as compliant for reporting.
- ✅ Unknown resources generally do not negatively impact compliance.
- ❌ Non-compliant resources reduce the compliance score.

The compliance percentage is displayed in the Azure Policy Compliance Dashboard.

---

## Example

| State | Number of Resources |
|--------|--------------------:|
| Compliant | 90 |
| Non-Compliant | 8 |
| Exempted | 2 |

Overall compliance:

```
92% Compliant
```

---

# 7. Enforcement Mode (Deep Dive)

Enforcement Mode controls whether Azure Policy only evaluates resources or also enforces policy effects.

It is configured **at the policy assignment level**.

---

## Enabled (Default)

When enabled:

- Resources are evaluated.
- Policy effects are enforced.
- Deployments can be denied.
- Modify policies update resources.
- DeployIfNotExists deploys required resources.

---

## DoNotEnforce

When DoNotEnforce is selected:

- Resources are evaluated.
- Compliance information is generated.
- Policy effects are not enforced.
- Deployments are not blocked.
- Automatic modifications are not performed.

This mode is ideal for safely testing new policies before enforcing them.

---

# 8. Disabled Effect vs DoNotEnforce

These two concepts are commonly confused in the AZ-104 exam.

| Feature | Disabled Effect | DoNotEnforce |
|---------|-----------------|--------------|
| Policy evaluated | ❌ No | ✅ Yes |
| Compliance reported | ❌ No | ✅ Yes |
| Policy effect applied | ❌ No | ❌ No |
| Assignment exists | Yes | Yes |
| Primary use | Disable policy completely | Test policy safely before enforcement |

---

## Key Difference

**Disabled Effect**

- Policy evaluation does not occur.

**DoNotEnforce**

- Policy evaluation still occurs.
- Compliance information is available.
- Enforcement actions are skipped.

---

# 9. Safe Deployment Best Practices

Microsoft recommends introducing policies gradually to reduce operational risk.

Recommended deployment process:

1. Develop policy definitions as code.
2. Store policies in source control (GitHub or Azure Repos).
3. Test policies in a non-production environment.
4. Assign policies using **DoNotEnforce**.
5. Review compliance results.
6. Resolve unexpected issues.
7. Enable enforcement.
8. Roll out policies gradually across production environments.

Following these practices minimizes disruption while maintaining governance.

---

# 10. Deployment Rings

Deployment Rings allow organizations to introduce policies in stages.

Rather than assigning policies to the entire organization at once, administrators progressively expand deployment.

---

## Example Deployment Rings

```
Ring 5
Test Environment
        │
        ▼
Ring 4
Development
        │
        ▼
Ring 3
Quality Assurance (QA)
        │
        ▼
Ring 2
Staging
        │
        ▼
Ring 1
Production
```

---

## Benefits

- Lower deployment risk
- Easier troubleshooting
- Simplified rollback
- Better validation before production
- Reduced impact on business operations

---

# 11. Azure Event Grid Integration

Azure Policy integrates with **Azure Event Grid** to notify other Azure services whenever compliance changes occur.

This enables automated responses to policy events.

---

## Workflow

```
Resource Change
        │
        ▼
Azure Policy Evaluation
        │
        ▼
Compliance State Updated
        │
        ▼
Azure Event Grid
        │
        ▼
Event Handler
```

---

## Common Event Handlers

- Azure Functions
- Azure Logic Apps
- Azure Automation
- Webhooks
- Custom Applications

---

## Real-World Example

A virtual machine is deployed without the required **Owner** tag.

1. Azure Policy evaluates the deployment.
2. The VM is marked as **Non-Compliant**.
3. Azure Event Grid publishes a compliance event.
4. An Azure Logic App receives the event.
5. The Logic App sends an email notification to the administrator.

This process enables automated governance and operational monitoring.

---

# 12. End-to-End Evaluation Workflow

The complete Azure Policy evaluation process is shown below.

```
Policy Assigned
        │
        ▼
Azure Policy Evaluates Resources
        │
        ▼
Compliance State Generated
        │
        ▼
Enforcement Mode Checked
        │
        ▼
Apply Policy Effect (if Enabled)
        │
        ▼
Optional Remediation
        │
        ▼
Compliance Dashboard Updated
        │
        ▼
Optional Azure Event Grid Notification
```

---

# 13. AZ-104 Exam Tips

Remember these important concepts:

- Azure Policy performs an automatic compliance scan approximately every **24 hours**.
- Manual scan command:

```bash
az policy state trigger-scan
```

- New policy assignments may take up to **30 minutes** to propagate.
- **Greenfield** = New Azure environment.
- **Brownfield** = Existing Azure environment.
- **DoNotEnforce** evaluates policies but does not enforce them.
- **Disabled** completely disables policy evaluation.
- Microsoft recommends using **deployment rings** for safe rollout.
- Azure Event Grid can automatically react to compliance changes.
- Azure Functions and Logic Apps are common Event Grid subscribers.

---

# 14. Quick Revision

| Concept | Remember |
|----------|----------|
| Automatic Scan | Every ~24 hours |
| Manual Scan | `az policy state trigger-scan` |
| Policy Propagation | Up to 30 minutes |
| Greenfield | New environment |
| Brownfield | Existing environment |
| DoNotEnforce | Evaluate only |
| Disabled | No evaluation |
| Compliance Dashboard | Displays compliance results |
| Deployment Rings | Gradual rollout |
| Event Grid | Responds to compliance events |

---

# 15. Summary

Azure Policy continuously evaluates Azure resources to ensure they comply with organizational governance requirements. Evaluations are triggered automatically by resource and policy changes or manually through compliance scans. Administrators can safely introduce new policies using **DoNotEnforce**, deployment rings, and staged rollouts before enabling enforcement.

Compliance states provide visibility into resource health, while Azure Event Grid enables automated responses to compliance changes through services such as Azure Functions and Logic Apps. Together, these capabilities help organizations maintain secure, compliant, and well-governed Azure environments.

---

**End of Part 3**

### Next Part

- Azure Policy Best Practices
- Comparison Tables
- Memory Tricks & Exam Shortcuts
- Azure Policy vs Azure RBAC vs Azure Blueprints
- Common Interview Questions
- 50+ AZ-104 Practice MCQs
- Final AZ-104 Revision Sheet
