# Azure Policy Study Guide -- Part 2

## Initiatives, Assignments, Exemptions, Attestations, and Remediation

# Table of Contents

1.  Azure Policy Initiatives
2.  Built-in vs Custom Initiatives
3.  Policy Assignments
4.  Assignment Properties
5.  Enforcement Mode
6.  Policy Exemptions
7.  Policy Attestations
8.  Policy Remediation
9.  End-to-End Workflow
10. AZ-104 Exam Tips

# 1. Azure Policy Initiatives

An **Initiative** (Policy Set) is a collection of multiple policy
definitions managed as one unit.

Instead of assigning many policies individually, assign one initiative.

## Example

Security Initiative: - Require resource tags - Allow approved regions -
Enable diagnostic logs - Require encryption - Restrict VM SKUs

Assign the initiative once and all included policies are enforced.

## Benefits

-   Simplifies management
-   Consistent governance
-   Easier compliance reporting
-   Supports regulatory frameworks

# 2. Built-in vs Custom Initiatives

## Built-in Initiatives

Created by Microsoft.

Examples: - ISO 27001 - NIST - PCI-DSS - Azure Security Benchmark

## Custom Initiatives

Created by your organization by grouping custom and/or built-in
policies.

Example: "Company Governance Initiative" - Allowed locations - Mandatory
tags - Backup required - HTTPS required

# 3. Policy Assignments

A policy definition does nothing until it is **assigned**.

Assignment determines: - Which policy/initiative - Scope - Parameters -
Enforcement mode - Excluded scopes

Assignments can target: - Management Group - Subscription - Resource
Group - Resource

# 4. Assignment Properties

## Scope

Where the policy applies.

## Parameters

Reuse one definition with different values.

Example: Allowed Location = Central India

## Resource Selectors

Gradually roll out policies based on resource type or location.

## Excluded Scopes

Exclude specific subscriptions, resource groups, or resources from
evaluation.

## Overrides

Temporarily change the effect of a policy in an assignment without
editing the definition.

## Noncompliance Messages

Display custom messages when a deployment violates policy.

Example: "VMs must be deployed only in Central India."

## Managed Identity

Required for policies using: - Modify - DeployIfNotExists

The managed identity performs remediation actions.

# 5. Enforcement Mode

Default: Enabled

Modes: - Enabled (Default) - DoNotEnforce

Enabled: - Evaluate - Apply effects

DoNotEnforce: - Evaluate only - No deny/modify effect - Ideal for
testing

# 6. Policy Exemptions

Exemptions exclude resources from evaluation after assignment.

They differ from excluded scopes because they are tracked.

Types:

## Waiver

Temporary acceptance of non-compliance.

Example: Legacy server awaiting upgrade.

## Mitigated

Policy intent achieved by another control.

Example: Encryption provided by third-party solution.

# 7. Policy Attestations

Some policies cannot be evaluated automatically.

Examples: - Disaster Recovery documentation - Business approval -
Operational procedure

Administrators manually attest compliance.

# 8. Policy Remediation

Remediation brings existing resources into compliance.

Supported mainly by: - Modify - DeployIfNotExists

Example: Policy requires Owner tag.

Existing VMs without the tag can be fixed using a remediation task.

New resources are remediated automatically when supported.

# End-to-End Workflow

Create Definition ↓ (Optional) Add to Initiative ↓ Assign ↓ Evaluate ↓
Compliant? ↓ No ↓ Exemption? ↓ No ↓ Enforce / Remediate ↓ Compliance
Updated

# AZ-104 Exam Tips

-   Initiative = Group of policy definitions.
-   Assignment activates a policy.
-   Exemption is created after assignment.
-   Waiver = Temporary acceptance.
-   Mitigated = Requirement met another way.
-   Remediation fixes existing resources.
-   Managed Identity is required for Modify and DeployIfNotExists
    remediation.
-   DoNotEnforce evaluates but doesn't enforce.
