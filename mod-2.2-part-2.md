# Azure Policy Study Guide -- Part 3

## Policy Evaluation, Compliance, Enforcement, Safe Deployment, and Event Grid

# Table of Contents

1.  Azure Policy Evaluation
2.  Evaluation Triggers
3.  Evaluation Timing
4.  Brownfield vs Greenfield
5.  Compliance States
6.  Compliance Percentage
7.  Enforcement Mode Deep Dive
8.  Disabled Effect vs DoNotEnforce
9.  Safe Deployment Best Practices
10. Deployment Rings
11. Azure Event Grid Integration
12. End-to-End Evaluation Workflow
13. AZ-104 Exam Tips

# 1. Azure Policy Evaluation

Azure Policy continuously evaluates Azure resources against assigned
policy definitions and initiatives.

Evaluation occurs after a policy or initiative is assigned and whenever
resources or assignments change.

The evaluation process: 1. Determine applicable resources. 2. Ignore
excluded or exempted resources. 3. Compare resource properties against
policy rules. 4. Assign a compliance state. 5. Apply the configured
policy effect (if enforcement is enabled).

# 2. Evaluation Triggers

Azure Policy evaluates resources when:

-   A new policy assignment is created.
-   A policy assignment is updated.
-   A resource is created.
-   A resource is modified.
-   A subscription is created.
-   A subscription is moved to another Management Group.
-   A policy exemption is created, updated, or removed.
-   A manual compliance scan is started.
-   Machine Configuration updates compliance information.

# 3. Evaluation Timing

Automatic Scan: - Approximately every 24 hours.

Manual Scan: Azure CLI: az policy state trigger-scan

Policy propagation: - New assignments may take up to 30 minutes to
become effective due to Azure Resource Manager caching.

# 4. Brownfield vs Greenfield

## Greenfield

A brand-new Azure environment.

Example: A newly created Azure subscription.

## Brownfield

An existing Azure environment where governance is introduced later.

Example: 500 VMs already exist and Azure Policy is assigned afterwards.

# 5. Compliance States

## Compliant

The resource satisfies the policy.

## Non-Compliant

The resource violates the policy.

## Error

Azure couldn't evaluate the resource.

## Conflicting

Multiple policies conflict.

## Protected

Resource is protected by a denyAction policy.

## Exempted

Resource has an approved exemption.

## Unknown

Azure cannot automatically determine compliance (common with manual
policies).

# Compliance State Priority

Highest to Lowest: 1. Non-Compliant 2. Compliant 3. Error 4. Conflicting
5. Protected 6. Exempted 7. Unknown

# 6. Compliance Percentage

Azure calculates overall compliance using applicable resources.

Generally, compliant, exempted, and unknown resources contribute
positively to overall compliance, while non-compliant resources reduce
the compliance score.

# 7. Enforcement Mode (Deep Dive)

Enforcement Mode is configured at the assignment level.

## Enabled (Default)

-   Evaluate resources
-   Apply policy effects
-   Deny deployments if required
-   Execute Modify and DeployIfNotExists effects

## DoNotEnforce

-   Evaluate resources
-   Produce compliance results
-   Do not apply policy effects
-   Useful for "What If" testing

# 8. Disabled Effect vs DoNotEnforce

  Feature                 Disabled Effect   DoNotEnforce
  ----------------------- ----------------- --------------------
  Policy evaluated        No                Yes
  Compliance reported     No                Yes
  Policy effect applied   No                No
  Use case                Disable policy    Test policy safely

# 9. Safe Deployment Best Practices

Microsoft recommends:

1.  Develop policies as code.
2.  Store definitions in source control.
3.  Test in non-production.
4.  Assign with DoNotEnforce.
5.  Review compliance.
6.  Enable enforcement.
7.  Roll out gradually.

# 10. Deployment Rings

Deploy policies gradually.

Example:

Ring 5 → Test

↓

Ring 4 → Development

↓

Ring 3 → QA

↓

Ring 2 → Staging

↓

Ring 1 → Production

Benefits: - Lower deployment risk - Easier rollback - Better validation

# 11. Azure Event Grid Integration

Azure Policy publishes events whenever compliance states change.

Workflow:

Resource Change ↓ Azure Policy Evaluation ↓ Compliance State Updated ↓
Azure Event Grid ↓ Event Handler

Supported Event Handlers: - Azure Functions - Azure Logic Apps -
Webhooks - Custom Applications

Example: A VM without required tags is deployed. Azure Policy marks it
non-compliant. Azure Event Grid triggers a Logic App. The Logic App
emails the administrator.

# 12. End-to-End Workflow

Policy Assigned ↓ Azure Policy Evaluates Resources ↓ Compliance State
Generated ↓ Enforcement Mode Checked ↓ Apply Effect (if enabled) ↓
Optional Remediation ↓ Compliance Dashboard Updated ↓ Optional Event
Grid Notification

# 13. AZ-104 Exam Tips

-   Automatic scan runs about every 24 hours.
-   Manual scan: az policy state trigger-scan
-   Policy propagation may take up to 30 minutes.
-   Brownfield = Existing environment.
-   Greenfield = New environment.
-   DoNotEnforce = Evaluate only.
-   Disabled Effect = No evaluation.
-   Safe deployment uses deployment rings.
-   Event Grid reacts to compliance changes.
-   Logic Apps and Azure Functions are common event handlers.

**End of Part 3**

Next Part: - Best Practices - Comparison Tables - Memory Tricks -
Interview Questions - 50+ Practice MCQs - Final AZ-104 Revision Sheet
