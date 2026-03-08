
# Plumbus

**Plumbus** is an **AI‑first application framework** designed to help teams build secure, auditable, and maintainable web applications together with AI coding assistants.

Unlike traditional frameworks that assume only humans write code, Plumbus assumes **AI and humans collaborate on development**. It enforces structured architecture, strict contracts, and governance-aware patterns so AI-generated code remains safe and predictable.

---

# Why Plumbus Exists

AI coding tools are rapidly changing how software is built. However, traditional frameworks were not designed for AI-assisted development.

Common problems include:

- AI-generated code breaking architectural boundaries
- hidden security vulnerabilities
- inconsistent data access patterns
- lack of observability and auditability
- unmanaged AI integrations

Plumbus solves these problems by providing a **structured development environment where architecture is explicit and enforceable**.

---

# Core Concepts

Plumbus applications are built from a small number of primitives.

### Capabilities

Capabilities represent **atomic business actions**.

Examples:

- `createCustomer`
- `approveRefund`
- `generateInvoice`

A capability defines:

- validated inputs
- validated outputs
- access rules
- side effects

---

### Flows

Flows orchestrate capabilities into **multi‑step workflows**.

Examples:

- customer onboarding
- refund approval
- document processing

Flows support:

- retries
- scheduling
- event triggers
- asynchronous execution

---

### Entities

Entities define **persistent domain data models**.

Examples:

- `Customer`
- `Invoice`
- `Refund`

Entities power repository generation, schema management, and privacy-aware storage.

---

### Events

Events represent **domain facts** emitted by the system.

Examples:

- `refund.approved`
- `invoice.generated`

Events allow loosely coupled components and event-driven workflows.

---

### Prompts

Prompts define **reusable AI interaction templates**.

Example:

```
summarize-ticket
classify-message
```

Plumbus treats AI as **managed infrastructure**, not simple API calls.

---

# Key Features

## AI‑First Architecture

Plumbus is designed so AI coding assistants can safely understand and modify the codebase.

It provides:

- predictable repository layout
- strict contracts
- generated AI briefs
- explicit runtime APIs

---

## Advisory Governance

Instead of blocking developers, Plumbus **guides them**.

If developers make risky decisions (for example storing sensitive data improperly), the framework:

- warns them
- records the decision
- allows explicit acknowledgment

This enables automated **compliance reporting**.

---

## Built‑in Auditability

All system actions produce structured audit logs.

Teams can answer questions like:

- Who triggered this action?
- Why did this workflow run?
- Why did the AI produce this result?
- What data was accessed?

---

## Managed AI Runtime

Plumbus includes a controlled AI runtime with:

- prompt templates
- structured outputs
- RAG support
- usage tracking
- cost monitoring
- governance signals

---

# What Plumbus Handles Automatically

The framework manages infrastructure concerns such as:

- authentication
- authorization
- validation
- database access
- workflow orchestration
- background jobs
- event delivery
- audit logging
- AI usage tracking
- governance checks

Developers focus on **business logic instead of plumbing**.

---

# Example Capability

```ts
export const approveRefund = defineCapability({
  name: "approveRefund",

  access: {
    roles: ["admin", "support"]
  },

  input: z.object({
    refundId: z.string()
  }),

  output: z.object({
    status: z.string()
  }),

  handler: async (ctx, input) => {

    const refund = await ctx.data.refund.findById(input.refundId)

    await ctx.data.refund.update(refund.id, {
      status: "approved"
    })

    await ctx.events.emit("refund.approved", {
      refundId: refund.id
    })

    return { status: "approved" }
  }
})
```

This single capability automatically integrates with:

- security
- validation
- events
- audit logging
- governance
- flows

---

# Documentation

Full framework design documentation can be found in:

```
docs/
```

Key sections:

- Executive summary
- Architecture specification
- SDK specification
- Runtime lifecycle
- Repository layout

---

# Project Status

Plumbus is currently in **design phase (v0.1)**.

The repository currently contains the full technical specification including:

- architecture design
- SDK contracts
- runtime lifecycle
- governance model
- AI integration model

The next milestone is the **first implementation prototype**.

---

# Who Plumbus Is For

Plumbus is designed for:

- teams building AI‑enabled products
- developers adopting AI coding assistants
- organizations requiring strong compliance visibility
- systems with complex workflows

---

# Vision

Plumbus aims to become a framework where:

- AI and humans can safely collaborate on software
- architecture remains enforceable
- security and governance are built in from the start
- modern applications can be built faster without sacrificing reliability

---

# Contributing

The project is currently in the design stage. Contributions, ideas, and discussions are welcome.

If you're interested in contributing, please open an issue to discuss ideas or improvements.

---

# License

License to be determined.
