# Plumbus AI SDK Specification

## Purpose

This document defines the **AI SDK**, the programming interface used to
integrate AI functionality into Plumbus applications.

Unlike typical frameworks where AI calls are made directly to provider
SDKs, Plumbus treats AI as a **managed runtime service**. This allows
the framework to enforce:

-   security boundaries
-   structured prompts
-   output validation
-   cost tracking
-   audit logging
-   governance checks

The AI SDK defines:

-   the `definePrompt()` primitive
-   prompt contracts
-   AI runtime operations (`ctx.ai.*`)
-   structured outputs
-   retrieval integration (RAG)
-   usage tracking
-   AI governance signals

The goal is to ensure that AI usage remains **predictable, observable,
and safe**.

------------------------------------------------------------------------

# Conceptual Model

AI in Plumbus is **capability-driven**.

Capabilities may invoke AI operations through the runtime context.

Example:

``` ts
const result = await ctx.ai.generate({
  prompt: "summarize-ticket",
  input: {
    message: ticket.message
  }
})
```

This design ensures:

-   prompt templates remain reusable
-   outputs remain validated
-   AI usage remains auditable

------------------------------------------------------------------------

# definePrompt()

Prompt templates are declared using:

``` ts
definePrompt(...)
```

Example:

``` ts
export const SummarizeTicket = definePrompt({
  name: "summarize-ticket",

  input: z.object({
    message: z.string()
  }),

  output: z.object({
    summary: z.string()
  })
})
```

Prompt definitions act as **contracts between the application and the AI
runtime**.

------------------------------------------------------------------------

# Prompt Contract Fields

## Identity

Required:

-   `name`

Optional:

-   description
-   domain
-   tags
-   owner

Example:

    name: "summarize-ticket"

------------------------------------------------------------------------

## Input Schema

Defines the structured input expected by the prompt.

Example:

``` ts
input: z.object({
  message: z.string()
})
```

Input schemas provide:

-   runtime validation
-   type inference
-   AI brief generation

------------------------------------------------------------------------

## Output Schema

Defines the expected response structure.

Example:

``` ts
output: z.object({
  summary: z.string()
})
```

Structured outputs allow the runtime to validate AI responses before
returning them to application code.

------------------------------------------------------------------------

## Model Configuration

Prompt definitions may include model preferences.

Example:

``` ts
model: {
  provider: "openai",
  name: "gpt-4",
  temperature: 0.2
}
```

The runtime may override these values according to system policies.

------------------------------------------------------------------------

# AI Runtime Operations

The runtime context exposes AI operations through `ctx.ai`.

Supported operations may include:

    ctx.ai.generate()
    ctx.ai.extract()
    ctx.ai.classify()
    ctx.ai.retrieve()

Each operation provides structured interaction with AI models.

------------------------------------------------------------------------

# generate()

Used for general text generation.

Example:

``` ts
await ctx.ai.generate({
  prompt: "summarize-ticket",
  input: { message }
})
```

The runtime:

1.  loads the prompt template
2.  constructs the prompt
3.  sends the request to the provider
4.  validates the response
5.  records usage metadata

------------------------------------------------------------------------

# extract()

Extracts structured data from unstructured input.

Example:

``` ts
await ctx.ai.extract({
  schema: InvoiceSchema,
  text: documentText
})
```

This operation is useful for:

-   document parsing
-   form extraction
-   invoice processing

------------------------------------------------------------------------

# classify()

Classifies text into categories.

Example:

``` ts
await ctx.ai.classify({
  labels: ["spam", "important", "support"],
  text: message
})
```

Classification should return structured labels rather than free-form
responses.

------------------------------------------------------------------------

# retrieve()

Retrieves contextual knowledge from the RAG system.

Example:

``` ts
await ctx.ai.retrieve({
  query: "refund policy"
})
```

The runtime:

-   performs vector search
-   filters by tenant and classification
-   returns relevant documents

------------------------------------------------------------------------

# Structured Output Validation

AI responses should be validated against declared output schemas.

Example:

``` ts
output: z.object({
  summary: z.string()
})
```

If the response fails validation, the runtime may:

-   retry generation
-   raise a validation error
-   return a partial result

This prevents malformed AI outputs from corrupting system state.

------------------------------------------------------------------------

# AI Usage Metadata

Every AI request should record usage metadata.

Examples:

-   model provider
-   model name
-   prompt name
-   token counts
-   request latency
-   estimated cost

This metadata supports:

-   cost monitoring
-   debugging
-   governance analysis

------------------------------------------------------------------------

# Budget Enforcement

Applications may define AI budgets.

Examples:

-   per-request token limits
-   daily cost limits
-   per-tenant quotas

The runtime should enforce these limits where possible.

------------------------------------------------------------------------

# Security Controls

The AI runtime should provide safeguards such as:

-   sensitive data redaction
-   tenant isolation
-   prompt filtering
-   restricted model access

Capabilities should avoid sending highly sensitive data to external
models unless explicitly allowed.

------------------------------------------------------------------------

# RAG Integration

The AI SDK integrates with the Retrieval-Augmented Generation system.

Typical sequence:

1.  capability calls `ctx.ai.generate`
2.  runtime performs retrieval if configured
3.  context documents added to prompt
4.  model generates response
5.  output validated

This allows models to incorporate system knowledge safely.

------------------------------------------------------------------------

# Governance Signals

AI usage should generate governance signals.

Examples:

-   prompts missing output schemas
-   sensitive data included in prompts
-   unusually high AI cost usage

These signals help teams detect risky patterns.

------------------------------------------------------------------------

# Audit Integration

AI operations should generate audit events.

Audit records may include:

-   prompt name
-   actor identity
-   capability invoking AI
-   model used
-   token usage

Sensitive prompt content may be masked.

------------------------------------------------------------------------

# AI-Friendly Design

Prompt templates should remain easy for AI agents to understand.

Guidelines:

-   descriptive prompt names
-   small input schemas
-   explicit output schemas
-   documented prompt purpose

This helps maintain predictable AI behavior.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   use prompt templates rather than inline prompts
-   validate structured outputs
-   monitor AI usage costs
-   keep prompts simple
-   test prompts regularly

AI should remain **predictable infrastructure**, not unpredictable
application behavior.

------------------------------------------------------------------------

# Summary

The AI SDK defines how Plumbus applications integrate with AI systems.

By combining prompt contracts, managed runtime operations, structured
outputs, and governance visibility, the framework ensures that AI usage
remains:

-   safe
-   observable
-   auditable
-   compatible with policy and compliance requirements
