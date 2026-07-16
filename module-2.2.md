# Azure Policy Study Guide -- Part 1

## Introduction, Architecture, Policy Definitions, Effects, and Scope

> **Target Exam:** AZ-104 Azure Administrator

# Table of Contents

1.  What is Azure Policy?
2.  Why Azure Policy?
3.  Azure Policy Architecture
4.  Azure Policy Workflow
5.  Azure Policy Resources Overview
6.  Policy Definitions
7.  Built-in vs Custom Policies
8.  Policy Definition Structure
9.  Policy Effects
10. Scope in Azure Policy
11. Policy Inheritance
12. Real-World Example
13. AZ-104 Key Points

# 1. What is Azure Policy?

Azure Policy is an Azure governance service that helps organizations
enforce standards, assess compliance, and automatically maintain
configurations across Azure resources.

It evaluates Azure resources against business rules (policies) and
can: - Audit resources - Deny deployments - Modify resource properties -
Deploy missing configurations - Report compliance

## Why Azure Policy?

Without governance: - Resources are deployed inconsistently. - Security
standards differ. - Costs increase. - Compliance becomes difficult.

Azure Policy provides centralized governance.

# 2. Azure Policy Architecture

Administrator ↓ Creates Policy Definition ↓ Assigns Policy ↓ Azure
Policy Engine ↓ Evaluates Resources ↓ Compliance State ↓ Policy Effect
(Audit / Deny / Modify / etc.)

# 3. Azure Policy Resources

  Resource            Description
  ------------------- ------------------------------------
  Policy Definition   A single governance rule
  Initiative          Group of policy definitions
  Assignment          Applies a definition or initiative
  Exemption           Excludes resources
  Attestation         Manual compliance confirmation
  Remediation         Brings resources into compliance

# 4. Policy Definitions

A policy definition contains: - Display Name - Description - Mode -
Parameters - Policy Rule - Effect

Example:

Condition: Location != India Central

Effect: Deny

# 5. Built-in vs Custom Policies

## Built-in Policies

Created and maintained by Microsoft.

Examples: - Allowed Locations - Allowed VM Sizes - Require Tags -
Storage Encryption

Advantages: - Ready to use - Supported by Microsoft - Regularly updated

## Custom Policies

Created by your organization when built-in policies don't satisfy
requirements.

Example: All VM names must begin with HP-.

# 6. Policy Effects

## Audit

Reports non-compliant resources without blocking deployment.

## Deny

Blocks deployment if the resource violates policy.

## Append

Adds additional properties during deployment.

## Modify

Updates resource properties automatically.

## DeployIfNotExists

Deploys required resources automatically.

## AuditIfNotExists

Checks whether related resources exist.

## Disabled

Turns the policy off.

  Effect              Blocks Deployment   Reports Compliance   Automatic Action
  ------------------- ------------------- -------------------- ------------------
  Audit               No                  Yes                  No
  Deny                Yes                 Yes                  No
  Append              No                  Yes                  Yes
  Modify              No                  Yes                  Yes
  DeployIfNotExists   No                  Yes                  Yes
  AuditIfNotExists    No                  Yes                  No
  Disabled            No                  No                   No

# 7. Scope in Azure Policy

Policy can be assigned at:

1.  Management Group
2.  Subscription
3.  Resource Group
4.  Resource

Inheritance:

Management Group └── Subscription └── Resource Group └── Resource

Policies assigned at higher levels are inherited by lower levels.

# 8. Real-World Example

Policy: Allow deployments only in Central India.

Developer deploys VM in West Europe.

Result: Deployment is denied when the effect is Deny.

# 9. AZ-104 Exam Tips

-   Policy Definition = Rule
-   Initiative = Collection of Rules
-   Assignment = Apply Rule
-   Scope determines where the policy applies.
-   Deny prevents deployment.
-   Audit only reports.
-   DeployIfNotExists automatically deploys required resources.
-   Modify changes resources automatically.
-   Built-in policies are created by Microsoft.
-   Custom policies are created by organizations.

------------------------------------------------------------------------

**End of Part 1**

Next Part: - Initiatives - Assignments - Exemptions - Attestations -
Remediation
