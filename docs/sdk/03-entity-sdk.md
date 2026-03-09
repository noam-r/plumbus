# Plumbus Entity SDK Specification

## Purpose

This document defines the **Entity SDK**, the programming interface used
to declare persistent domain data in Plumbus applications.

Entities represent **domain models stored in the database** and are used
by capabilities and flows through the repository layer (`ctx.data`).

The Entity SDK defines:

-   the `defineEntity()` primitive
-   field types
-   field classification
-   relationships
-   indexes
-   retention policies
-   repository generation
-   migration behavior
-   governance visibility

The Entity SDK ensures that data models are:

-   explicit
-   analyzable
-   privacy-aware
-   safe for AI-assisted development

------------------------------------------------------------------------

## Conceptual Model

An entity represents a **domain concept stored persistently**.

Examples:

-   Customer
-   Invoice
-   Refund
-   Subscription
-   SupportTicket

Entities define:

-   structure of stored data
-   privacy classification
-   relationships with other entities
-   indexing requirements

Entities are not responsible for business logic. Business logic lives in
capabilities.

------------------------------------------------------------------------

## Entity File Structure

Entities should be defined in dedicated files.

Example:

    app/entities/customer.entity.ts
    app/entities/invoice.entity.ts
    app/entities/refund.entity.ts

Keeping entities separate improves:

-   discoverability
-   schema generation
-   governance analysis
-   AI-assisted navigation

------------------------------------------------------------------------

## defineEntity()

Entities are declared using:

``` ts
defineEntity(...)
```

Example:

``` ts
export const Customer = defineEntity({
  name: "Customer",

  fields: {
    id: field.id(),
    email: field.string({ classification: "personal" }),
    createdAt: field.timestamp(),
  },

  indexes: [
    ["email"]
  ]
})
```

This declaration drives:

-   database schema generation
-   repository creation
-   governance checks
-   documentation

------------------------------------------------------------------------

## Entity Contract Fields

## Identity

Each entity must define:

-   `name`

Optional metadata:

-   description
-   domain
-   tags
-   owner

------------------------------------------------------------------------

## Fields

Fields define the entity structure.

Example:

``` ts
fields: {
  id: field.id(),
  email: field.string(),
  createdAt: field.timestamp(),
}
```

Fields must include type definitions.

------------------------------------------------------------------------

## Field Types

The Entity SDK should support a minimal, explicit set of field types.

Examples:

-   `field.id()`
-   `field.string()`
-   `field.number()`
-   `field.boolean()`
-   `field.timestamp()`
-   `field.json()`
-   `field.enum()`
-   `field.relation()`

This keeps the data model predictable.

------------------------------------------------------------------------

## Field Classification

Fields may include **classification metadata** for governance and
privacy analysis.

Example:

``` ts
email: field.string({
  classification: "personal"
})
```

Recommended classifications:

  Classification     Meaning
  ------------------ ---------------------------
  public             safe for public exposure
  internal           internal application data
  personal           personal data
  sensitive          sensitive personal data
  highly_sensitive   highly restricted data

Classification metadata supports:

-   privacy governance
-   audit masking
-   policy compatibility checks

------------------------------------------------------------------------

## Default Field Options

Fields may support common options:

    required
    default
    unique
    nullable
    classification
    encrypted
    maskedInLogs

Example:

``` ts
creditCardToken: field.string({
  classification: "sensitive",
  encrypted: true,
  maskedInLogs: true
})
```

These options enable privacy-aware storage.

------------------------------------------------------------------------

## Relationships

Entities may define relationships with other entities.

Example:

``` ts
customerId: field.relation({
  entity: "Customer",
  type: "many-to-one"
})
```

Supported relationship types:

-   one-to-one
-   one-to-many
-   many-to-one
-   many-to-many

Relationships allow the framework to generate join helpers and integrity
checks.

------------------------------------------------------------------------

## Indexes

Entities may declare indexes.

Example:

``` ts
indexes: [
  ["email"],
  ["createdAt"]
]
```

Indexes improve query performance and help ensure uniqueness
constraints.

------------------------------------------------------------------------

## Retention Policies

Entities may include data retention hints.

Example:

``` ts
retention: {
  duration: "365d"
}
```

Retention metadata helps governance systems analyze compliance with
policies like GDPR.

------------------------------------------------------------------------

## Repository Generation

From entity definitions, Plumbus generates repositories.

Example usage in capabilities:

``` ts
await ctx.data.customer.findById(id)
await ctx.data.customer.create(data)
await ctx.data.customer.update(id, updates)
```

Repository APIs should include:

-   create
-   update
-   delete
-   findById
-   findMany
-   query helpers

Repositories enforce:

-   tenant isolation
-   audit logging
-   validation rules

------------------------------------------------------------------------

## Migration Generation

Entity definitions drive database schema migrations.

Example workflow:

    plumbus migrate generate
    plumbus migrate apply

Migration generation compares:

-   current entity definitions
-   current database schema

and produces migration scripts.

------------------------------------------------------------------------

## Database Infrastructure

Recommended v0.1 database:

-   PostgreSQL

Reasons:

-   strong relational capabilities
-   transactional integrity
-   mature ecosystem
-   vector extension support for RAG

ORM layer may use:

-   Drizzle ORM

------------------------------------------------------------------------

## Governance Integration

Entity definitions are a major input to governance analysis.

The governance system may detect:

-   storage of sensitive fields
-   missing encryption for classified data
-   logging of personal fields
-   missing retention metadata

These signals feed policy compatibility reports.

------------------------------------------------------------------------

## Audit Integration

Entity operations should generate audit signals automatically.

Examples:

-   record creation
-   record updates
-   record deletion

Audit metadata may include:

-   actor identity
-   changed fields
-   timestamp

Sensitive fields should be masked according to classification metadata.

------------------------------------------------------------------------

## AI-Friendly Entity Design

Entities must remain easy for AI agents to understand.

Guidelines:

-   use descriptive field names
-   keep entities small and focused
-   avoid overly dynamic schemas
-   document relationships clearly

Predictable entity structures improve automated reasoning.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   one entity per file
-   clear domain naming
-   classify sensitive fields
-   declare indexes explicitly
-   avoid large monolithic entities

Entities should model **real domain concepts**, not arbitrary storage
structures.

------------------------------------------------------------------------

## Summary

The Entity SDK defines how persistent domain data is modeled in Plumbus.

By combining explicit schemas, classification metadata, repository
generation, and migration tooling, the framework ensures that data
models remain:

-   structured
-   privacy-aware
-   auditable
-   analyzable
-   compatible with governance and compliance requirements
