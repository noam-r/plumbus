# Plumbus Testing Model

## Purpose

This document defines the **Testing Model** of the Plumbus framework.

Testing is a foundational part of the Plumbus philosophy. The framework
encourages (and may require) structured testing to ensure that systems
remain reliable, secure, and compliant.

Because Plumbus emphasizes:

-   explicit capability contracts
-   workflow orchestration via flows
-   security enforcement
-   AI integration
-   governance visibility

testing must verify not only correctness but also **behavioral
guarantees**.

The testing model provides:

-   standardized test layers
-   scaffolding for new components
-   automated verification of contracts
-   CI-compatible execution
-   testing patterns compatible with AI-assisted development

------------------------------------------------------------------------

# Core Testing Principles

## Tests Should Validate Contracts

Capabilities define explicit contracts:

-   input schema
-   output schema
-   security requirements
-   side effects

Tests should verify that implementations conform to these contracts.

------------------------------------------------------------------------

## Business Logic Must Be Testable in Isolation

Capabilities should be testable without running the entire system.

Tests should execute capabilities directly through testing helpers.

Example conceptual call:

    runCapability("approveRefund", input)

This keeps tests fast and deterministic.

------------------------------------------------------------------------

## Workflows Must Be Simulatable

Flows orchestrate complex processes. The testing system should allow
**flow simulation**.

Simulation allows developers to validate:

-   branching behavior
-   retry behavior
-   event triggers
-   state transitions

------------------------------------------------------------------------

# Testing Layers

The testing model contains several layers.

## Unit Tests

Unit tests verify individual functions or utilities.

Examples:

-   helper utilities
-   validation logic
-   pure domain functions

Unit tests should be fast and deterministic.

------------------------------------------------------------------------

## Capability Tests

Capability tests verify the behavior of individual capabilities.

These tests should validate:

-   correct output
-   correct data access
-   side effects (events, flows, etc.)
-   authorization enforcement
-   error handling

Capability tests are the **primary testing layer** in Plumbus.

------------------------------------------------------------------------

## Flow Tests

Flow tests validate workflow orchestration.

Tests may simulate:

-   normal execution paths
-   failure and retry scenarios
-   conditional branching
-   event triggers

Example conceptual call:

    simulateFlow("refundApprovalFlow", input)

------------------------------------------------------------------------

## Integration Tests

Integration tests validate interactions between components.

Examples:

-   capability interacting with database
-   flow triggering events
-   AI runtime interactions

These tests may require runtime services such as a database.

------------------------------------------------------------------------

## End-to-End Tests

End-to-end (E2E) tests simulate real user workflows.

Examples:

-   user creating an account
-   submitting a support request
-   processing a payment flow

These tests validate the entire system stack.

------------------------------------------------------------------------

# Recommended Testing Tools

For v0.1, the recommended tools include:

-   Vitest for unit and capability testing
-   Playwright for browser-based E2E testing

These tools provide modern TypeScript-friendly testing capabilities.

------------------------------------------------------------------------

# Generated Test Scaffolding

The CLI should generate test scaffolding when new components are
created.

Example:

    plumbus capability new approve-refund

Generated structure:

    tests/approve-refund.spec.ts
    fixtures/approve-refund.fixture.ts

This encourages consistent testing patterns.

------------------------------------------------------------------------

# Test Fixtures

Fixtures provide sample data for tests.

Examples:

-   sample entity records
-   mock inputs
-   expected outputs

Fixtures help keep tests readable and reusable.

------------------------------------------------------------------------

# Test Isolation

Tests should remain isolated from each other.

Best practices include:

-   resetting database state between tests
-   avoiding shared mutable state
-   using dedicated test databases

Isolation ensures deterministic results.

------------------------------------------------------------------------

# Security Testing

Security behavior should be verified through tests.

Examples:

-   unauthorized users denied access
-   cross-tenant access prevented
-   sensitive fields not exposed

These tests help detect security regressions early.

------------------------------------------------------------------------

# AI Testing

AI integrations require special testing strategies.

Examples:

-   validating structured outputs
-   testing prompt templates
-   mocking AI responses
-   verifying governance warnings

Tests should avoid relying on external AI providers where possible.

------------------------------------------------------------------------

# Governance Testing

Governance checks should also be testable.

Example tests:

-   verifying warnings are produced
-   verifying override behavior
-   validating policy evaluation results

This ensures governance rules remain reliable.

------------------------------------------------------------------------

# Test Coverage

Projects may enforce minimum test coverage levels.

Example:

    minimumCoverage: 80%

Coverage thresholds may apply to:

-   capability code
-   flow definitions
-   core logic

Coverage requirements should remain configurable.

------------------------------------------------------------------------

# Continuous Integration

Tests should run automatically in CI pipelines.

Typical CI workflow:

1.  install dependencies
2.  run tests
3.  run governance verification
4.  run policy compatibility checks

Example commands:

    plumbus verify
    npm test

CI automation ensures regressions are detected early.

------------------------------------------------------------------------

# Test Reporting

Testing frameworks should produce structured reports.

Reports may include:

-   passed tests
-   failed tests
-   coverage metrics
-   performance timing

Structured reports support CI dashboards and development feedback.

------------------------------------------------------------------------

# AI-Friendly Testing Design

The testing model should remain understandable for AI coding agents.

Guidelines:

-   predictable test structure
-   clear naming conventions
-   minimal hidden test logic
-   explicit fixtures

This helps AI systems generate or update tests safely.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   test capabilities thoroughly
-   simulate flows for critical workflows
-   isolate tests from external dependencies
-   enforce coverage thresholds
-   include security and governance tests

Testing should be treated as a **core architectural requirement rather
than optional verification**.

------------------------------------------------------------------------

# Summary

The Plumbus testing model provides a layered testing strategy covering:

-   unit logic
-   capability behavior
-   workflow orchestration
-   system integration
-   end-to-end workflows

By integrating testing into component scaffolding and CI workflows, the
framework ensures that applications remain:

-   reliable
-   secure
-   maintainable
-   compatible with governance and compliance requirements
