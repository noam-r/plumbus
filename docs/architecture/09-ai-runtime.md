# Plumbus AI Runtime

## Purpose

This document defines the **AI Runtime** subsystem of the Plumbus
framework.

AI capabilities are increasingly integrated into modern applications.
However, using AI services directly from application code often leads
to:

-   inconsistent prompt management
-   uncontrolled costs
-   insecure data exposure
-   lack of auditability
-   unpredictable system behavior

Plumbus addresses these issues by introducing a **managed AI runtime
layer**.

The AI runtime is responsible for:

-   interacting with AI providers
-   managing prompts and schemas
-   enforcing security boundaries
-   validating model outputs
-   tracking token usage and cost
-   enabling explainability
-   supporting RAG integration
-   providing AI governance visibility

Capabilities interact with AI **only through this runtime**.

------------------------------------------------------------------------

## Core Principles

## 1. AI as a Managed System Service

AI should not be used through direct SDK calls inside capabilities.

Instead, all AI interactions must go through the framework runtime.

Example conceptual usage:

    ctx.ai.generate()
    ctx.ai.extract()
    ctx.ai.classify()
    ctx.ai.retrieve()

This allows the framework to enforce security, governance, and cost
tracking.

------------------------------------------------------------------------

## 2. Prompt Templates Are First-Class Artifacts

Prompts should not be embedded inside application code.

Instead they should be defined as structured templates.

Example directory:

    app/prompts/summarize-ticket/
      prompt.yaml

Prompt definitions may include:

-   template text
-   input schema
-   output schema
-   model configuration
-   safety rules

------------------------------------------------------------------------

## 3. Structured Outputs by Default

AI outputs should be validated against schemas whenever possible.

Example:

    z.object({
      summary: z.string()
    })

This ensures that AI responses integrate safely with application logic.

------------------------------------------------------------------------

## AI Provider Abstraction

The AI runtime should support multiple providers.

Examples include:

-   OpenAI
-   Anthropic
-   local model runtimes
-   enterprise model gateways

Providers are integrated through adapters.

Capabilities should remain **provider-independent**.

------------------------------------------------------------------------

## AI Operations

The runtime should expose several core operations.

### Text Generation

Generate free-form responses.

Example conceptual call:

    ctx.ai.generate({
      prompt: "summarize-ticket",
      input: {...}
    })

------------------------------------------------------------------------

### Structured Extraction

Extract structured data from unstructured input.

Example:

    ctx.ai.extract({
      schema: InvoiceSchema,
      text: documentText
    })

------------------------------------------------------------------------

### Classification

Classify input into categories.

Example:

    ctx.ai.classify({
      labels: ["spam", "important", "support"]
    })

------------------------------------------------------------------------

### Retrieval

Retrieve contextual documents for RAG.

Example:

    ctx.ai.retrieve({
      query: "refund policy"
    })

------------------------------------------------------------------------

## Prompt Management

Prompts should be versioned and stored separately from capability code.

Prompt files may include:

-   template text
-   system instructions
-   variable placeholders
-   schema validation rules
-   model configuration

This structure enables:

-   prompt versioning
-   prompt review
-   policy analysis
-   safer AI development

------------------------------------------------------------------------

## Output Validation

The runtime should validate AI outputs when schemas are defined.

Validation prevents:

-   malformed outputs
-   unexpected response structures
-   downstream runtime errors

When validation fails, the runtime may:

-   retry generation
-   raise structured errors
-   return partial results depending on configuration

------------------------------------------------------------------------

## Cost Tracking

AI requests should record usage metadata.

Tracked information may include:

-   model used
-   token counts
-   request latency
-   estimated cost
-   prompt template used

This data supports operational monitoring and budgeting.

------------------------------------------------------------------------

## Budget Enforcement

Applications may define usage limits.

Examples:

-   maximum tokens per request
-   daily AI cost limits
-   per-tenant usage caps

The runtime should enforce these limits where possible.

------------------------------------------------------------------------

## AI Security Boundaries

AI prompts may contain sensitive data.

The runtime should provide protections such as:

-   redaction of sensitive fields
-   warning on classified data usage
-   tenant isolation in prompts
-   policy checks for risky prompts

Developers may override warnings through the advisory governance system.

------------------------------------------------------------------------

## RAG Integration

The AI runtime integrates with the RAG subsystem.

Typical flow:

1.  capability invokes AI runtime
2.  runtime performs retrieval
3.  retrieved context added to prompt
4.  prompt sent to model
5.  result validated
6.  response returned to capability

RAG retrieval should respect:

-   authorization rules
-   tenant boundaries
-   document classification policies

------------------------------------------------------------------------

## Tool Usage

AI models may call tools during reasoning.

The runtime may expose tools such as:

-   capability invocation
-   knowledge retrieval
-   calculations
-   search operations

Tool execution should remain controlled by the framework.

------------------------------------------------------------------------

## Explainability

AI interactions should produce explanation metadata.

This may include:

-   prompt template used
-   retrieval sources
-   reasoning traces when available
-   output validation results

Explanation metadata improves debugging and trust in AI-assisted
workflows.

------------------------------------------------------------------------

## Audit Integration

AI requests should generate audit records.

Audit data may include:

-   actor identity
-   capability invoking AI
-   prompt template used
-   model provider
-   token usage
-   response metadata

Sensitive prompt content may be masked.

------------------------------------------------------------------------

## Governance Integration

The governance system may analyze AI usage patterns.

Examples of warnings:

-   sensitive data sent to external models
-   prompts missing validation schemas
-   excessive AI usage

These warnings appear in policy compatibility reports.

------------------------------------------------------------------------

## AI-Friendly Development

The runtime is designed to support AI-assisted coding.

Key properties:

-   prompts stored as separate artifacts
-   structured input/output schemas
-   explicit AI operations
-   observable usage patterns

This structure makes AI features safer to modify.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   keep prompts simple and explicit
-   validate outputs whenever possible
-   minimize sensitive data in prompts
-   monitor usage costs
-   document prompt behavior

AI functionality should remain **predictable and auditable**.

------------------------------------------------------------------------

## Summary

The AI runtime provides a structured and secure way to integrate AI into
Plumbus applications.

By centralizing AI interactions, the framework enables:

-   consistent prompt management
-   safe integration with business logic
-   reliable output validation
-   cost visibility
-   governance analysis

This approach allows AI capabilities to be **powerful while remaining
controllable and auditable**.
