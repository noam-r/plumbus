# Plumbus Advisory Governance

## Purpose

This document defines the **Advisory Governance** subsystem of the
Plumbus framework.

Traditional frameworks either:

-   **block unsafe behavior**, or
-   **ignore risky practices entirely**.

Plumbus introduces a third model: **Advisory‑First Governance**.

Under this model, the framework:

1.  Detects risky patterns
2.  Warns developers
3.  Requires acknowledgement when deviations occur
4.  Records the decision and justification

This ensures developers remain in control while risks remain **visible,
deliberate, and auditable**.

The governance system enables:

-   risk detection
-   override acknowledgement
-   architectural guidance
-   compliance reporting
-   safe flexibility

------------------------------------------------------------------------

# Governance Philosophy

## Guardrails, Not Gates

Plumbus avoids unnecessarily blocking developers.

Instead, the framework provides strong guardrails that:

-   surface risks early
-   explain potential consequences
-   record deviations from best practices

Developers may still proceed if they explicitly acknowledge the risk.

------------------------------------------------------------------------

## Transparency Over Restriction

The goal of governance is not to restrict innovation.

Instead, governance should provide:

-   visibility
-   traceability
-   accountability

A risky design decision is acceptable **if it is intentional and
documented**.

------------------------------------------------------------------------

# Governance Scope

The advisory system may detect issues in several areas.

Examples include:

### Security Risks

-   capabilities without access policies
-   overly permissive roles
-   cross‑tenant data access

### Privacy Risks

-   storage of highly sensitive data
-   logging personal data
-   missing data classification

### AI Risks

-   prompts containing sensitive data
-   missing output validation
-   excessive AI usage

### Architecture Risks

-   capabilities performing large numbers of side effects
-   flows with excessive branching
-   missing test coverage

------------------------------------------------------------------------

# Governance Detection

Governance checks may run during:

-   development builds
-   CLI verification commands
-   CI pipelines
-   policy certification analysis

Example command:

    plumbus verify

This command may report warnings such as:

    Warning: Capability approveRefund has no access policy.

------------------------------------------------------------------------

# Risk Severity Levels

Governance warnings may include severity levels.

Example levels:

  Level     Meaning
  --------- -----------------------------------------
  info      informational guidance
  warning   risky but acceptable
  high      significant security or compliance risk

Severity levels help teams prioritize remediation.

------------------------------------------------------------------------

# Overrides and Acknowledgements

Developers may override governance warnings.

An override must include:

-   explicit acknowledgement
-   justification text
-   author identity
-   timestamp

Example conceptual override record:

    override:
      rule: sensitive-data-storage
      justification: Required for legacy integration

------------------------------------------------------------------------

# Governance Records

Overrides should be recorded in governance metadata.

This allows organizations to track:

-   deviations from best practices
-   architectural risks
-   compliance gaps

These records appear in policy compatibility reports.

------------------------------------------------------------------------

# CLI Interaction

Developers interact with governance tools through the CLI.

Examples:

    plumbus verify
    plumbus acknowledge <warning-id>
    plumbus certify policy pci_dss

The CLI should provide clear explanations of detected issues.

------------------------------------------------------------------------

# Integration with Policy Compatibility

Advisory governance feeds into the **Policy Compatibility Assessment**
system.

Governance records allow policy reports to distinguish between:

-   compliant behavior
-   non‑compliant behavior
-   acknowledged exceptions

This is especially useful during security audits.

------------------------------------------------------------------------

# Governance Metadata

The system may maintain governance metadata including:

-   warning identifiers
-   affected components
-   acknowledgement records
-   justification notes

This metadata enables governance dashboards and reports.

------------------------------------------------------------------------

# CI/CD Integration

Governance checks may be integrated into CI pipelines.

Teams may configure policies such as:

-   fail build on high‑severity warnings
-   require review of overrides
-   generate compliance reports automatically

This ensures governance visibility during development.

------------------------------------------------------------------------

# Explainability

Governance warnings should include explanations describing:

-   what the risk is
-   why the framework flagged it
-   potential consequences
-   recommended mitigation

Clear explanations help developers make informed decisions.

------------------------------------------------------------------------

# AI‑Friendly Governance

The advisory system must remain understandable for coding agents.

Guidelines:

-   governance rules should be explicit
-   warnings should be structured
-   remediation suggestions should be machine‑readable

This allows AI tools to automatically propose fixes.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   review governance warnings regularly
-   minimize overrides where possible
-   document risk justifications clearly
-   incorporate governance checks into CI workflows

Governance is most effective when treated as **a development feedback
system rather than a compliance afterthought**.

------------------------------------------------------------------------

# Summary

The Plumbus Advisory Governance subsystem provides a flexible yet
transparent model for managing architectural and compliance risks.

By detecting risks, requiring acknowledgement, and recording decisions,
the framework enables teams to maintain:

-   architectural discipline
-   compliance visibility
-   developer flexibility

Advisory governance ensures that **risky choices are never invisible,
even when they are intentional**.
