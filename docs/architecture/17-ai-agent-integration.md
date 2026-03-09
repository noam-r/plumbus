# Plumbus AI Agent Integration

## Purpose

This document defines how **AI coding agents interact with Plumbus
projects**.

One of the central goals of Plumbus is to create an environment where
both **human developers and AI agents** can safely and reliably develop
software.

Traditional codebases are difficult for AI agents to modify because:

-   architecture is implicit
-   logic is scattered across files
-   side effects are hidden
-   responsibilities are unclear
-   changes require whole‑repository understanding

Plumbus addresses this by designing the framework to be
**LLM-operable**.

This document defines:

-   repository conventions
-   edit boundaries
-   generated AI briefs
-   AI-safe architectural rules
-   safe modification workflows

------------------------------------------------------------------------

# Core Principles

## 1. Localized Reasoning

AI agents should be able to understand and modify a feature by reading
**a small number of files**.

This is achieved through:

-   feature-local directories
-   explicit contracts
-   minimal cross-feature dependencies

A typical change should only require editing:

-   one capability implementation
-   one test file
-   possibly one flow definition

------------------------------------------------------------------------

## 2. Explicit Contracts

All important behaviors are defined through **declarative contracts**.

Examples include:

-   capability contracts
-   entity definitions
-   flow definitions
-   event schemas
-   prompt templates

Contracts act as stable interfaces that AI agents can rely on when
modifying implementation code.

------------------------------------------------------------------------

## 3. Predictable File Roles

Every file in a Plumbus project should have a clear role.

Examples:

-   capability contract file
-   capability implementation file
-   flow definition
-   entity definition
-   test file
-   prompt template

Predictable file roles allow AI agents to safely determine **where
changes should occur**.

------------------------------------------------------------------------

# Repository Structure

Plumbus projects follow a structured repository layout.

Example:

app/ capabilities/ flows/ entities/ prompts/ adapters/ policies/ ui/

.plumbus/ generated/ briefs/ manifests/

This structure separates:

-   developer-authored code
-   generated artifacts
-   AI agent guidance files

------------------------------------------------------------------------

# Feature-Local Design

Capabilities and flows live in **self-contained directories**.

Example capability structure:

app/capabilities/billing/approve-refund/

capability.ts impl.ts tests/ fixtures/

AI agents should typically modify only:

-   impl.ts
-   related tests

The contract file remains stable unless the interface intentionally
changes.

------------------------------------------------------------------------

# Generated Artifacts

Plumbus generates certain files automatically.

These include:

-   API clients
-   manifests
-   documentation summaries
-   agent briefs

Generated artifacts should never be edited manually.

They live under:

.plumbus/generated/

Separating generated code prevents AI agents from accidentally modifying
derived artifacts.

------------------------------------------------------------------------

# AI Agent Briefs

The framework can generate **AI-friendly briefs** describing specific
features.

Example CLI command:

plumbus agent brief capability approve-refund

The brief may include:

-   capability description
-   contract summary
-   entity dependencies
-   security requirements
-   related flows
-   known side effects
-   testing expectations

These briefs help AI agents quickly understand the scope of a feature.

------------------------------------------------------------------------

# Project-Level AI Brief

A Plumbus project may include a project-level AI brief.

Example location:

.plumbus/briefs/project.md

This document describes:

-   architectural conventions
-   allowed edit zones
-   framework rules
-   naming conventions
-   testing expectations

AI agents should consult this brief before making repository-wide
changes.

------------------------------------------------------------------------

# Edit Zones

Plumbus repositories may define edit zones.

Edit zones describe which areas AI agents are allowed to modify.

Typical zones:

Safe edit zones:

-   capability implementations
-   flow definitions
-   test files
-   prompt templates
-   UI components

Restricted zones:

-   entity definitions
-   policy configuration
-   adapters

Framework-owned zones:

-   generated files
-   manifests
-   build artifacts

These zones prevent unsafe modifications.

------------------------------------------------------------------------

# AI-Safe Conventions

To support safe AI modifications, Plumbus projects follow several
conventions.

### Small Files

Files should remain small and focused so AI agents can reason about
them.

------------------------------------------------------------------------

### Descriptive Naming

Names should reflect domain meaning.

Examples:

approve-refund create-invoice customer-onboarding-flow

Avoid vague names such as:

helper utils processor

------------------------------------------------------------------------

### Limited Shared Utilities

Large shared utility layers make AI reasoning difficult.

Prefer feature-local logic unless code is clearly reusable.

------------------------------------------------------------------------

### Explicit Dependencies

Capabilities and flows should reference dependencies explicitly rather
than relying on implicit framework magic.

------------------------------------------------------------------------

# AI Modification Workflow

Typical AI-assisted development workflow:

1.  developer or agent requests feature change
2.  CLI generates an agent brief
3.  AI agent reviews contract and implementation
4.  AI agent modifies implementation and tests
5.  verification and governance checks run
6.  changes are reviewed and merged

This workflow ensures modifications remain safe and auditable.

------------------------------------------------------------------------

# AI-Assisted Verification

After an AI agent modifies code, the following checks should run:

-   capability contract validation
-   governance warnings
-   test suite
-   policy compatibility analysis

Example command:

plumbus verify

This helps detect unintended side effects.

------------------------------------------------------------------------

# Safety Considerations

The AI integration model should reduce risks such as:

-   accidental security regressions
-   hidden side effects
-   architectural drift
-   broken workflows

By combining structured architecture and verification tools, Plumbus
creates an environment where AI agents can operate safely.

------------------------------------------------------------------------

# Future AI Integration

Future versions of Plumbus may include:

-   automated architecture suggestions
-   AI-assisted flow design
-   automatic test generation
-   policy remediation suggestions

These capabilities depend on the structured architecture defined by this
document.

------------------------------------------------------------------------

# Best Practices

Recommended practices for AI-assisted development:

-   generate agent briefs before modifications
-   modify implementation files rather than contracts when possible
-   run verification after every change
-   keep architectural boundaries intact

AI agents should be treated as **collaborative tools rather than
autonomous decision-makers**.

------------------------------------------------------------------------

# Summary

The Plumbus AI Agent Integration model ensures that AI coding assistants
can safely and effectively interact with Plumbus repositories.

By combining:

-   structured repository layout
-   explicit contracts
-   predictable file roles
-   generated agent briefs
-   edit zones
-   verification tooling

the framework enables **AI-assisted development that remains safe,
auditable, and maintainable**.
