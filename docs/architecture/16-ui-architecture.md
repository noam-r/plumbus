# Plumbus UI Architecture

## Purpose

This document defines the **UI Architecture** of the Plumbus framework.

Plumbus is an API-first framework. That means the UI is not the source
of business logic and is not expected to implement domain rules
independently. Instead, the UI consumes backend capabilities and flows
through generated clients and structured frontend patterns.

The UI subsystem is responsible for:

-   rendering application interfaces
-   collecting user input
-   displaying backend state
-   invoking capabilities and flows
-   presenting workflow progress
-   integrating authentication context into the frontend experience

The UI architecture should remain:

-   predictable
-   composable
-   testable
-   AI-friendly
-   aligned with the backend contract model

------------------------------------------------------------------------

## Core UI Principles

## 1. API-First User Interfaces

The UI consumes backend APIs generated from capability and flow
contracts.

This means:

-   business logic remains in capabilities
-   workflows remain in flows
-   the UI acts as a presentation and interaction layer

This prevents duplication of business rules across client and server.

------------------------------------------------------------------------

## 2. Generated Client Access

Frontend code should avoid hand-written HTTP integrations whenever
possible.

Instead, the framework should generate typed client access from
capability and flow contracts.

Examples of generated access patterns may include:

-   strongly typed API functions
-   React hooks
-   mutation helpers
-   flow trigger helpers

This improves:

-   consistency
-   type safety
-   AI-assisted code generation
-   change resilience when backend contracts evolve

------------------------------------------------------------------------

## 3. Thin UI, Rich Backend

The UI should remain intentionally thin with respect to domain behavior.

The backend should own:

-   validation rules
-   access control
-   side-effect execution
-   workflow logic
-   AI integrations
-   governance-critical behavior

The UI may still contain:

-   presentation logic
-   local interaction state
-   layout and navigation
-   optimistic display behavior where safe

But it should not become an alternate business logic layer.

------------------------------------------------------------------------

## 4. Explicit User Journeys

The UI should map clearly to capabilities and flows.

Examples:

-   a form submits a capability
-   a dashboard triggers a flow
-   a status page displays flow execution state
-   a support page invokes an AI-enabled capability

This makes the frontend easier to reason about and keeps it aligned with
the backend architecture.

------------------------------------------------------------------------

## Recommended Frontend Stack

For v0.1, the recommended frontend stack is:

-   React
-   Next.js
-   TypeScript

Reasons:

-   strong ecosystem
-   strong TypeScript support
-   broad familiarity
-   good compatibility with AI coding agents
-   good support for component-based UI and application routing

Plumbus should build on top of these tools rather than attempting to
replace them.

------------------------------------------------------------------------

## UI Layer Responsibilities

The UI layer is responsible for:

-   page rendering
-   form rendering
-   data display
-   navigation
-   capability invocation
-   flow initiation
-   displaying errors and validation feedback
-   showing audit, policy, or workflow status where relevant

The UI is not responsible for:

-   enforcing core access policy
-   performing direct database operations
-   implementing backend business rules
-   directly calling AI provider SDKs

------------------------------------------------------------------------

## Routing Model

The UI should use the routing conventions of the selected frontend
substrate.

For the default stack, this means using Next.js routing.

Routing responsibilities include:

-   page-level composition
-   navigation structure
-   URL-based context
-   page-level data loading

Routing should remain separate from backend capability routing.

The backend owns API endpoints. The frontend owns presentation routes.

------------------------------------------------------------------------

## Generated API Clients

Plumbus should generate frontend-accessible clients from capability and
flow definitions.

Generated clients may include:

-   typed query functions
-   typed action functions
-   typed hooks for React components
-   flow trigger functions
-   response type definitions
-   validation metadata

The generation pipeline ensures that frontend code remains aligned with
backend contracts.

This also reduces the need for AI agents to invent request formats.

------------------------------------------------------------------------

## UI Data Fetching

The UI should fetch application data through generated capability
clients.

Recommended characteristics:

-   typed requests and responses
-   clear loading and error state handling
-   composable data hooks
-   predictable cache behavior

The exact caching library may vary, but the architectural rule is:

**data fetching happens through generated capability clients, not ad hoc
endpoint logic**

------------------------------------------------------------------------

## Forms and Input Handling

The UI should support structured form generation and form binding based
on capability input schemas.

This enables:

-   shared validation metadata
-   reduced duplication
-   easier scaffolding
-   safer AI-assisted form creation

A form should map clearly to:

-   one capability
-   one flow trigger
-   or one frontend-only local interaction

Where backend validation exists, frontend validation should complement
it rather than replace it.

------------------------------------------------------------------------

## Authentication Integration

The UI should integrate with the authentication subsystem to manage:

-   login state
-   session awareness
-   user identity display
-   tenant context
-   route protection

Frontend authentication behavior should be driven by:

-   the selected auth adapter
-   generated auth helpers
-   consistent session state interfaces

The UI may use route guards or session-aware components, but
authorization remains ultimately enforced by backend capabilities.

------------------------------------------------------------------------

## Error Handling

The UI should handle errors in a structured way.

Typical categories include:

-   validation errors
-   authorization failures
-   missing resources
-   flow execution failures
-   AI runtime failures
-   network failures

Error responses should be based on structured backend error types so UI
behavior remains consistent.

------------------------------------------------------------------------

## Flow-Aware Interfaces

Because flows are first-class in Plumbus, the UI should support
flow-aware interactions.

Examples include:

-   triggering flows
-   displaying current flow status
-   showing retry or failure states
-   surfacing step progression
-   handling wait/resume workflows

This is especially important for:

-   onboarding
-   approvals
-   background processing visibility
-   long-running AI-assisted workflows

------------------------------------------------------------------------

## AI-Enabled User Interfaces

The UI may invoke AI-enabled capabilities, but should do so through the
backend.

Examples:

-   ticket summarization
-   search assistants
-   content classification
-   guided support interactions

The UI should not call AI providers directly for standard application
behavior.

Benefits:

-   auditability
-   policy enforcement
-   cost tracking
-   output validation
-   governance visibility

------------------------------------------------------------------------

## UI and Governance

The UI may surface governance-relevant information, such as:

-   warnings associated with a feature
-   compatibility status summaries
-   audit status
-   policy check results
-   workflow failure explanations

However, governance logic itself remains a backend and tooling concern.

------------------------------------------------------------------------

## Component Structure

The frontend codebase should remain predictable and feature-oriented.

Suggested structure:

-   pages
-   layouts
-   components
-   generated clients
-   auth/session helpers
-   feature-local UI modules where needed

Components should be:

-   small
-   reusable where appropriate
-   easy to test
-   easy for AI agents to modify safely

------------------------------------------------------------------------

## State Management

Local UI state should remain local where possible.

Examples:

-   form state
-   dialog visibility
-   local interaction state

Global state should be minimized and used only where necessary, such as:

-   auth/session state
-   application-wide UI preferences
-   global notifications

The framework should discourage large, opaque global state systems.

------------------------------------------------------------------------

## Testing the UI Layer

The UI subsystem should support:

-   component tests
-   page interaction tests
-   end-to-end tests

Recommended tools:

-   component and logic tests in the existing TypeScript test stack
-   Playwright for browser-based end-to-end tests

UI tests should validate:

-   rendering
-   form submission
-   navigation
-   interaction with generated clients
-   flow-aware UX behavior

------------------------------------------------------------------------

## Accessibility

The UI architecture should encourage accessible interfaces.

Expectations may include:

-   semantic HTML
-   keyboard accessibility
-   accessible labels and descriptions
-   focus management
-   appropriate ARIA usage where needed

Accessibility should be considered part of the standard UI quality bar,
not an optional add-on.

------------------------------------------------------------------------

## AI-Friendly UI Structure

To support coding agents, the frontend structure should remain easy to
navigate.

Guidelines:

-   predictable page and component locations
-   generated clients separated from hand-written components
-   minimal hidden conventions
-   explicit links between UI features and backend capabilities
-   stable file roles

This reduces the risk of frontend drift and unsafe AI-generated changes.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   keep business logic in capabilities
-   use generated clients rather than ad hoc fetch code
-   keep components small and focused
-   use backend contracts as the source of truth
-   make flow-related UX explicit
-   treat authentication and authorization separately

The UI should be a clear, testable consumer of the backend architecture.

------------------------------------------------------------------------

## Summary

The Plumbus UI architecture provides a predictable frontend model built
on top of modern web tools.

By combining:

-   React and Next.js
-   generated capability clients
-   thin UI principles
-   backend-owned business logic
-   flow-aware user journeys

the framework ensures that frontend code remains aligned with the
architecture, easy to test, and safe for AI-assisted development.
