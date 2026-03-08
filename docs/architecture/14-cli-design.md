# Plumbus CLI Design

## Purpose

This document defines the **Command Line Interface (CLI)** for the
Plumbus framework.

The CLI is the primary developer interface for interacting with Plumbus.
It provides tools for:

-   project scaffolding
-   capability generation
-   flow generation
-   verification and governance checks
-   policy certification
-   database migration management
-   RAG ingestion workflows
-   AI agent assistance

The CLI ensures that developers and AI coding agents interact with the
framework using **consistent, structured workflows**.

------------------------------------------------------------------------

# CLI Design Principles

## Consistency

Commands should follow a predictable pattern:

    plumbus <resource> <action>

Examples:

    plumbus capability new
    plumbus flow new
    plumbus migrate generate
    plumbus verify

Consistency reduces cognitive load and improves AI compatibility.

------------------------------------------------------------------------

## Explicit Operations

Commands should be explicit and descriptive.

Avoid ambiguous or overloaded commands.

Example:

    plumbus capability new approve-refund

This command clearly indicates the action being performed.

------------------------------------------------------------------------

## Automation-Friendly

The CLI should produce machine-readable outputs where appropriate.

Example:

-   JSON output mode
-   structured error messages
-   predictable command responses

This enables integration with:

-   CI pipelines
-   automation scripts
-   AI coding agents

------------------------------------------------------------------------

# Core CLI Commands

## Project Creation

Initialize a new Plumbus application.

Example:

    plumbus create my-app

Responsibilities:

-   generate project structure
-   configure runtime dependencies
-   initialize configuration files
-   scaffold default directories

------------------------------------------------------------------------

## Capability Generation

Generate a new capability scaffold.

Example:

    plumbus capability new approve-refund

Generated structure:

    app/capabilities/billing/approve-refund/
      capability.ts
      impl.ts
      tests/
      fixtures/

The CLI ensures capability conventions remain consistent.

------------------------------------------------------------------------

## Flow Generation

Generate a new flow scaffold.

Example:

    plumbus flow new refund-approval

Generated structure:

    app/flows/billing/refund-approval/
      flow.ts
      tests/

------------------------------------------------------------------------

## Entity Generation

Create a new entity definition.

Example:

    plumbus entity new customer

Generated file:

    app/entities/customer.entity.ts

------------------------------------------------------------------------

# Verification Commands

## Architecture Verification

Analyze the application for governance warnings.

Example:

    plumbus verify

Checks may include:

-   missing capability access policies
-   sensitive data classifications
-   missing tests
-   risky architectural patterns

------------------------------------------------------------------------

# Policy Certification

Generate policy compatibility reports.

Example:

    plumbus certify policy pci_dss

The report evaluates system compliance against defined policy profiles.

------------------------------------------------------------------------

# Migration Commands

Manage database schema migrations.

Example commands:

    plumbus migrate generate
    plumbus migrate apply
    plumbus migrate rollback

These commands ensure schema changes remain traceable and controlled.

------------------------------------------------------------------------

# RAG Ingestion Commands

Manage knowledge ingestion pipelines.

Example:

    plumbus rag ingest docs/

The CLI may perform:

-   document parsing
-   chunking
-   embedding generation
-   vector storage

------------------------------------------------------------------------

# AI Agent Assistance

The CLI may provide commands specifically designed for AI coding agents.

Example:

    plumbus agent brief capability approve-refund

This command may generate structured documentation including:

-   capability contract summary
-   related entities
-   governance warnings
-   suggested modification boundaries

------------------------------------------------------------------------

# Configuration Commands

Developers may inspect configuration using CLI commands.

Example:

    plumbus config show

This command displays runtime configuration such as:

-   database connection
-   AI provider settings
-   queue configuration

------------------------------------------------------------------------

# Development Utilities

Additional CLI utilities may include:

-   environment diagnostics
-   dependency validation
-   runtime status inspection

Example:

    plumbus doctor

This command checks environment readiness.

------------------------------------------------------------------------

# CLI Output Formats

Commands should support multiple output formats.

Examples:

-   human-readable output
-   JSON output for automation

Example:

    plumbus verify --json

------------------------------------------------------------------------

# Error Handling

CLI errors should provide clear guidance.

Example:

    Error: Capability approve-refund already exists

Errors should include:

-   problem description
-   suggested resolution

------------------------------------------------------------------------

# Extensibility

Organizations may extend the CLI with custom commands.

Examples:

-   internal policy checks
-   deployment tooling
-   integration pipelines

CLI plugins allow customization without modifying core framework code.

------------------------------------------------------------------------

# AI-Friendly CLI Design

The CLI should remain predictable for AI agents.

Guidelines:

-   consistent command patterns
-   machine-readable outputs
-   explicit parameters
-   minimal interactive prompts

This allows AI coding assistants to reliably invoke CLI operations.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   use CLI scaffolding rather than manual file creation
-   run verification regularly
-   integrate policy certification into CI workflows
-   maintain consistent project structure

The CLI is intended to act as the **developer control center for Plumbus
applications**.

------------------------------------------------------------------------

# Summary

The Plumbus CLI provides a structured interface for developing,
verifying, and operating Plumbus applications.

By standardizing project scaffolding, verification, migrations, and
governance workflows, the CLI ensures:

-   consistent developer experience
-   automation compatibility
-   AI-friendly development workflows

The CLI acts as the **primary interface between developers and the
Plumbus framework**.
