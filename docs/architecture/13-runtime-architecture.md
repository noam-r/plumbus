# Plumbus Runtime Architecture

## Purpose

This document defines the **Runtime Architecture** of the Plumbus
framework.

While earlier documents describe logical subsystems (capabilities,
flows, events, AI runtime, governance), this document describes **how
those systems run in production**.

The runtime architecture is responsible for:

-   executing API requests
-   running capability logic
-   orchestrating flows
-   dispatching events
-   executing background work
-   running scheduled tasks
-   managing AI operations
-   providing observability and operational control

The runtime must support:

-   reliability
-   horizontal scalability
-   fault tolerance
-   operational transparency

------------------------------------------------------------------------

## Core Runtime Components

A Plumbus deployment consists of several runtime components.

Typical runtime topology:

-   API Server
-   Worker Processes
-   Event Dispatcher
-   Flow Workers
-   Scheduler
-   Database
-   Queue System
-   Vector Store (for RAG)

These components work together to execute the application.

------------------------------------------------------------------------

## API Server

The API server handles incoming client requests.

Responsibilities include:

-   HTTP request handling
-   request validation
-   authentication
-   capability invocation
-   response formatting
-   request-level audit logging

Recommended stack for v0.1:

-   Node.js
-   Fastify

Fastify provides:

-   high performance
-   schema validation
-   low overhead

------------------------------------------------------------------------

## Capability Execution

When an API request arrives:

1.  request is authenticated
2.  authorization policy is evaluated
3.  input schema is validated
4.  capability implementation executes
5.  side effects are performed
6.  audit records are generated
7.  response is returned

Capabilities execute **synchronously within the API request lifecycle**
unless they explicitly schedule background work.

------------------------------------------------------------------------

## Worker Processes

Worker processes execute asynchronous work.

Examples include:

-   flow step execution
-   event processing
-   scheduled tasks
-   background capability execution
-   AI operations that exceed request time limits

Workers are stateless and pull jobs from the queue system.

------------------------------------------------------------------------

## Queue System

Queues provide durable asynchronous job execution.

Queues power:

-   flow step execution
-   event delivery
-   retry mechanisms
-   delayed tasks
-   background processing

The queue system should support:

-   job retries
-   exponential backoff
-   dead-letter queues
-   horizontal worker scaling

Common implementation options include:

-   Redis-backed queues
-   SQS
-   Kafka-backed systems

The specific implementation may vary by deployment environment.

------------------------------------------------------------------------

## Flow Workers

Flow workers execute workflow steps defined in the Flow Engine.

Execution model:

1.  flow step job pulled from queue
2.  worker loads flow execution state
3.  step logic executed
4.  state updated in database
5.  next step scheduled

Flow workers must remain stateless to enable horizontal scaling.

------------------------------------------------------------------------

## Event Dispatcher

The event dispatcher processes events stored in the outbox.

Responsibilities:

1.  read pending events from the outbox
2.  publish delivery jobs to the event queue
3.  update delivery status

This mechanism ensures reliable event delivery.

------------------------------------------------------------------------

## Scheduler

The scheduler triggers time-based tasks.

Examples:

-   scheduled flows
-   periodic maintenance tasks
-   recurring background operations

Scheduler responsibilities:

-   evaluate schedules
-   enqueue execution jobs
-   ensure no missed runs

Schedulers may run as dedicated services or as part of worker processes.

------------------------------------------------------------------------

## Database

The primary database stores:

-   entities
-   flow execution state
-   event outbox records
-   audit logs
-   governance metadata
-   AI usage records

Recommended infrastructure:

-   PostgreSQL

PostgreSQL provides:

-   transactional integrity
-   relational modeling
-   vector search extensions

------------------------------------------------------------------------

## Vector Storage

Vector storage supports the RAG subsystem.

Recommended implementation:

-   PostgreSQL with pgvector

This allows vector search to remain integrated with the main database.

------------------------------------------------------------------------

## Horizontal Scaling

The runtime architecture must support horizontal scaling.

Scaling strategies include:

-   multiple API server instances
-   multiple worker processes
-   distributed queue consumers

Stateless components (API servers and workers) can scale independently.

------------------------------------------------------------------------

## Fault Tolerance

The system must tolerate failures gracefully.

Examples:

-   worker crashes
-   network failures
-   AI provider outages

Recovery mechanisms include:

-   job retries
-   dead-letter queues
-   flow state persistence
-   event replay

------------------------------------------------------------------------

## Observability

Operational visibility is critical for production systems.

The runtime should expose:

-   request metrics
-   queue depth
-   worker health
-   flow execution status
-   event processing metrics
-   AI usage metrics

Monitoring systems may include:

-   Prometheus
-   OpenTelemetry
-   centralized logging systems

------------------------------------------------------------------------

## Logging

Structured logs should include:

-   request identifiers
-   actor identity
-   capability name
-   flow identifiers
-   error metadata

Logs must avoid exposing sensitive classified data.

------------------------------------------------------------------------

## Deployment Model

Plumbus applications may be deployed using:

-   container orchestration platforms
-   server clusters
-   managed cloud services

Typical deployment components:

-   API server container
-   worker container
-   scheduler container
-   database service
-   queue service

------------------------------------------------------------------------

## Multi-Environment Support

The runtime must support multiple environments:

-   development
-   staging
-   production

Environment configuration may include:

-   database endpoints
-   queue endpoints
-   AI provider configuration
-   authentication providers

------------------------------------------------------------------------

## Security Considerations

Runtime infrastructure must enforce:

-   secure database connections
-   encrypted communication
-   protected environment variables
-   restricted network access

Secrets should never be hardcoded.

------------------------------------------------------------------------

## AI Runtime Integration

AI requests may be executed:

-   directly during capability execution
-   asynchronously via workers

The runtime should allow AI calls to move to background execution if
latency becomes excessive.

------------------------------------------------------------------------

## Governance Integration

Runtime metrics may feed governance analysis.

Examples:

-   excessive AI cost usage
-   high retry rates in flows
-   frequent authorization failures

These signals may produce governance warnings.

------------------------------------------------------------------------

## Best Practices

Recommended runtime practices:

-   separate API and worker processes
-   monitor queue depth
-   configure retry policies carefully
-   instrument metrics early
-   maintain clear operational dashboards

Operational reliability depends heavily on runtime observability.

------------------------------------------------------------------------

## Summary

The Plumbus runtime architecture provides the infrastructure necessary
to execute the framework's logical components reliably.

By combining API servers, worker processes, queue systems, and durable
storage, the runtime supports:

-   scalable request handling
-   resilient background processing
-   reliable event delivery
-   durable workflow execution

This architecture enables Plumbus applications to operate safely in
production environments.
