# Plumbus Execution Lifecycle Specification

## Purpose

This document defines the **runtime execution lifecycle** of the Plumbus
framework.

While other documents describe the architecture and programming model,
this specification explains **how the runtime engine behaves when the
system is running**.

It describes the lifecycle of:

-   API requests
-   capability execution
-   event emission
-   flow orchestration
-   AI runtime calls
-   audit recording
-   governance signals

Understanding the lifecycle is critical for:

-   framework implementation
-   debugging
-   observability
-   security analysis
-   AI reasoning about the system

------------------------------------------------------------------------

# Core Runtime Components

The Plumbus runtime typically consists of the following components:

**API Server**\
Receives requests and executes capabilities.

**Worker Processes**\
Execute asynchronous jobs, flows, and event handlers.

**Queue System**\
Handles reliable background execution and retries.

**Event Dispatcher**\
Publishes events from the outbox to consumers.

**Scheduler**\
Triggers scheduled flows and jobs.

**AI Runtime**\
Handles prompt execution, model invocation, and validation.

**Governance Engine**\
Analyzes system behavior and generates advisory signals.

------------------------------------------------------------------------

# Request Lifecycle

When a client invokes a capability through the API:

### Step 1 --- Request Received

The API server receives a request.

Example:

    POST /api/capabilities/approveRefund

------------------------------------------------------------------------

### Step 2 --- Authentication

The system validates the authentication token.

The runtime extracts identity information such as:

    userId
    roles
    scopes
    tenantId

These values populate:

    ctx.auth

------------------------------------------------------------------------

### Step 3 --- Capability Resolution

The runtime identifies the capability based on the route or identifier.

Example:

    approveRefund

The system loads the capability contract and manifest.

------------------------------------------------------------------------

### Step 4 --- Input Validation

The request payload is validated against the capability input schema.

Example:

    input: z.object({
      refundId: z.string()
    })

Invalid input results in a validation error before the handler runs.

------------------------------------------------------------------------

### Step 5 --- Authorization Evaluation

The framework evaluates the capability access declaration.

Example:

    access:
      roles: ["admin","support"]

If authorization fails:

    403 Forbidden

The capability handler is never executed.

------------------------------------------------------------------------

### Step 6 --- Runtime Context Creation

A runtime context object is created.

Example:

    ctx = {
      auth,
      data,
      events,
      flows,
      ai,
      audit,
      logger,
      config
    }

The context is passed to the capability handler.

------------------------------------------------------------------------

### Step 7 --- Capability Execution

The capability handler executes.

Example:

    handler(ctx, input)

During execution the handler may:

-   read or write entities
-   emit events
-   trigger flows
-   call AI runtime
-   record audit events

------------------------------------------------------------------------

### Step 8 --- Output Validation

The returned value is validated against the capability output schema.

Example:

    output: z.object({
      status: z.string()
    })

Invalid outputs produce a runtime error.

------------------------------------------------------------------------

### Step 9 --- Response Returned

The validated result is returned to the client.

------------------------------------------------------------------------

# Event Lifecycle

Events are emitted through:

    ctx.events.emit()

Example:

    ctx.events.emit("refund.approved", payload)

### Step 1 --- Event Created

The event payload is validated against its schema.

------------------------------------------------------------------------

### Step 2 --- Outbox Write

The event is stored in the **outbox table** inside the same transaction
as the capability.

This ensures consistency.

------------------------------------------------------------------------

### Step 3 --- Event Dispatcher

A background worker reads the outbox table and publishes events.

------------------------------------------------------------------------

### Step 4 --- Event Delivery

Events are delivered to:

-   event handler capabilities
-   subscribed flows
-   external integrations

------------------------------------------------------------------------

### Step 5 --- Audit Recording

Event emission produces audit metadata.

------------------------------------------------------------------------

# Flow Lifecycle

Flows may be triggered by:

-   capabilities
-   events
-   schedules

Example:

    ctx.flows.start("refundApprovalFlow")

------------------------------------------------------------------------

### Step 1 --- Flow Instance Created

A flow instance is created with:

    flowId
    input
    state
    status

------------------------------------------------------------------------

### Step 2 --- First Step Scheduled

The queue system schedules the first step.

------------------------------------------------------------------------

### Step 3 --- Step Execution

A worker process executes the step.

Steps may:

-   call capabilities
-   evaluate conditions
-   emit events
-   update flow state

------------------------------------------------------------------------

### Step 4 --- State Persistence

After each step the flow state is saved.

This allows the workflow to resume after failures.

------------------------------------------------------------------------

### Step 5 --- Next Step Scheduling

The next step is scheduled according to the flow definition.

------------------------------------------------------------------------

### Step 6 --- Completion or Failure

The flow is marked:

    completed
    failed
    cancelled

------------------------------------------------------------------------

# AI Runtime Lifecycle

When a capability invokes AI:

    ctx.ai.generate()

The following occurs.

------------------------------------------------------------------------

### Step 1 --- Prompt Resolution

The runtime loads the prompt template defined with:

    definePrompt()

------------------------------------------------------------------------

### Step 2 --- Input Validation

Prompt input is validated against the prompt input schema.

------------------------------------------------------------------------

### Step 3 --- Retrieval (Optional)

If RAG is configured:

-   vector search performed
-   contextual documents retrieved

------------------------------------------------------------------------

### Step 4 --- Model Invocation

The runtime calls the configured model provider.

------------------------------------------------------------------------

### Step 5 --- Output Validation

The response is validated against the prompt output schema.

------------------------------------------------------------------------

### Step 6 --- Usage Recording

The runtime records:

-   token usage
-   model name
-   latency
-   estimated cost

------------------------------------------------------------------------

### Step 7 --- Audit Event

AI operations produce audit records.

------------------------------------------------------------------------

# Audit Lifecycle

Audit records are created when:

-   capabilities execute
-   events are emitted
-   flows run steps
-   AI operations occur
-   authorization checks fail

Audit records include:

    actor
    action
    timestamp
    metadata
    correlationId

Sensitive fields may be masked.

------------------------------------------------------------------------

# Governance Lifecycle

Governance signals may be generated when:

-   sensitive fields are accessed
-   AI prompts include classified data
-   capabilities bypass best practices
-   policies are violated

These signals appear in verification reports:

    plumbus verify
    plumbus certify policy <policy>

------------------------------------------------------------------------

# Failure Handling

Failures may occur during:

-   capability execution
-   flow step execution
-   event processing
-   AI runtime calls

Handling strategies include:

-   retries
-   dead-letter queues
-   flow failure handlers
-   alerting

------------------------------------------------------------------------

# Observability

The runtime should produce telemetry such as:

-   request latency
-   capability execution time
-   flow execution metrics
-   event processing statistics
-   AI usage metrics

These metrics support monitoring and debugging.

------------------------------------------------------------------------

# Correlation IDs

Every execution path should include a correlation ID.

This ID links:

-   API request
-   capability execution
-   emitted events
-   flow instances
-   audit records

Correlation improves traceability.

------------------------------------------------------------------------

# Summary

The Plumbus execution lifecycle defines how requests, capabilities,
events, flows, AI operations, and governance analysis interact during
runtime.

By structuring execution through well-defined stages and controlled
runtime services, the framework ensures that system behavior remains:

-   predictable
-   auditable
-   observable
-   safe for AI-assisted development
