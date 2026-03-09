# Plumbus Entity and Data Model Design

## Purpose

This document defines the **Entity and Data Model** subsystem of the
Plumbus framework.

Entities represent the persistent domain objects stored by an
application. The entity system provides a structured way to define,
access, classify, and govern data within a Plumbus application.

The entity layer is responsible for:

-   defining domain data structures
-   enforcing data validation
-   supporting privacy-aware design
-   enabling policy analysis
-   ensuring consistent data access patterns
-   generating database schema and migrations
-   enabling auditability of data access

Entities form the **data foundation** upon which capabilities and flows
operate.

------------------------------------------------------------------------

## Core Principles

The entity system follows several key principles.

### Declarative Data Modeling

Entities are declared through a structured definition rather than
scattered database code. This enables the framework to reason about the
data model.

### Privacy Awareness

Fields can include classification metadata so the framework can detect
privacy risks.

### Controlled Access

Application code should not interact directly with database drivers. All
access goes through a controlled data service.

### Policy Visibility

The system must be able to analyze how data is stored and accessed in
order to produce governance and compliance reports.

------------------------------------------------------------------------

## Entity Definition

Entities are defined declaratively.

Example conceptual definition:

``` ts
export const Customer = defineEntity({
  name: "Customer",

  fields: {
    id: field.id(),
    email: field.string({ classification: "personal" }),
    createdAt: field.timestamp()
  }
})
```

Entity definitions should include:

-   entity name
-   field definitions
-   field types
-   validation constraints
-   classification metadata
-   indexing hints
-   documentation metadata

------------------------------------------------------------------------

## Field Types

The entity system should support a small, well-defined set of field
types.

Examples include:

-   id
-   string
-   number
-   boolean
-   timestamp
-   json
-   enum
-   relation

The framework may support extensions for specialized types, but the core
type system should remain simple and predictable.

------------------------------------------------------------------------

## Field Classification

Fields may include privacy classification metadata.

Recommended classification levels:

  Level              Description
  ------------------ -------------------------
  public             safe to expose
  internal           internal system data
  personal           personal data
  sensitive          sensitive personal data
  highly_sensitive   restricted data

This metadata enables:

-   privacy warnings
-   audit masking
-   policy analysis
-   compliance reporting

------------------------------------------------------------------------

## Entity Relationships

Entities may define relationships with other entities.

Examples:

-   one-to-many
-   many-to-one
-   many-to-many

Example conceptual definition:

``` ts
field.relation({
  entity: "Customer",
  type: "many-to-one"
})
```

Relationship definitions should remain explicit so the framework can
reason about dependency graphs.

------------------------------------------------------------------------

## Database Infrastructure

The recommended infrastructure for v0.1 is:

-   PostgreSQL
-   Drizzle ORM

PostgreSQL provides:

-   reliability
-   strong transactional semantics
-   support for advanced indexing
-   JSON storage when needed
-   compatibility with vector extensions used by the RAG system

------------------------------------------------------------------------

## Repository Layer

Application code interacts with entities through repository services
exposed via the execution context.

Examples:

-   ctx.data.customer.findById()
-   ctx.data.customer.create()
-   ctx.data.invoice.update()

This repository layer ensures:

-   tenant isolation
-   audit logging
-   validation enforcement
-   policy analysis

Capabilities should avoid direct SQL queries when standard operations
are available.

------------------------------------------------------------------------

## Data Access Context

Capabilities access entities through:

-   ctx.data

The data context provides entity-specific repositories.

Example:

``` ts
const customer = await ctx.data.customer.findById(id)
```

The data context may also expose transactional helpers where needed.

------------------------------------------------------------------------

## Schema Generation

Entity definitions should generate database schema automatically.

This includes:

-   tables
-   columns
-   indexes
-   relationships
-   constraints

Generated schema definitions allow:

-   consistent migrations
-   automated verification
-   environment reproducibility

------------------------------------------------------------------------

## Migrations

Database migrations should be generated from entity changes.

Typical workflow:

1.  developer modifies entity definition
2.  migration generator detects schema difference
3.  migration file is generated
4.  migration is applied to environments

Example CLI:

    plumbus migrate generate
    plumbus migrate apply

------------------------------------------------------------------------

## Data Retention Hints

Entities may include retention metadata.

Example:

-   audit records retained for 1 year
-   logs retained for 30 days

Retention hints support:

-   governance analysis
-   operational lifecycle management

------------------------------------------------------------------------

## Multi-Tenant Data Isolation

If the application uses multi-tenancy, entities should include tenant
scoping.

This ensures queries are automatically restricted to the correct tenant.

Example conceptual rule:

    tenantId must be present in all tenant-scoped entities

The framework may enforce this automatically.

------------------------------------------------------------------------

## Audit Integration

The data layer integrates with the audit subsystem.

Audit records may capture:

-   entity reads
-   entity writes
-   field-level changes
-   actor identity
-   timestamps

Sensitive fields may be masked in audit records according to
classification rules.

------------------------------------------------------------------------

## Policy and Compliance Integration

Entity definitions provide key input for governance and compliance
analysis.

The framework may analyze:

-   storage of sensitive data
-   logging of personal data
-   missing encryption hints
-   retention policy mismatches

These results appear in policy compatibility reports.

------------------------------------------------------------------------

## AI-Friendly Design

The entity model must remain easy for coding agents to understand.

Guidelines:

-   entity definitions should be concise
-   field names should be descriptive
-   relationships should be explicit
-   schema changes should be traceable

Avoid complex implicit schema generation logic.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   keep entity definitions small and focused
-   avoid overly generic entities
-   classify sensitive fields
-   prefer explicit relationships
-   rely on the repository layer for access

Entities should represent **clear domain concepts**, not arbitrary
storage structures.

------------------------------------------------------------------------

## Summary

The entity and data model subsystem provides a structured,
privacy-aware, and governance-compatible approach to persistent data.

It ensures that:

-   domain data is explicitly modeled
-   access patterns remain consistent
-   privacy risks are visible
-   auditability is preserved
-   schema evolution is controlled

This subsystem forms the **foundation of data integrity and compliance
in Plumbus applications**.
