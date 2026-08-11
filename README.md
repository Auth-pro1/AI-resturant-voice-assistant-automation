# AI Voice Restaurant Automation System
> An AI-powered restaurant voice assistant that handles customer calls, processes orders and reservations, validates requests, and records transactions using Vapi and n8n.

## Problem

A busy restaurant receives customer calls for food orders, reservations, menu and pricing enquiries, and delivery requests. When multiple calls compete for staff attention, customer requests can be missed, incompletely captured, or handled inconsistently.

The core problem was:

**How can a restaurant reliably capture and process customer requests without depending entirely on staff availability?**

## Solution

I built an automated voice assistant that receives customer calls, captures structured information from conversations, and routes requests through automated business workflows.

The system applies deterministic validation and business rules before recording confirmed transactions.

Depending on the request, the workflow can:

* Process food orders
* Support pickup and delivery orders
* Check menu availability and pricing
* Calculate and verify order totals
* Validate delivery zones and fees
* Handle reservation requests
* Respond to supported customer enquiries
* Escalate requests requiring human intervention
* Record confirmed transactions in Airtable

## Architecture

```text
Customer Call
      ↓
Vapi Voice Agent
      ↓
n8n Webhook
      ↓
Request Extraction & Normalization
      ↓
Intent Routing
      ↓
┌────────────┬──────────────┬──────────────┐
│   Orders   │ Reservations │  Escalation  │
└─────┬──────┴──────┬───────┴──────┬───────┘
      ↓             ↓              ↓
Business Rules   Validation    Human Support
      ↓             ↓
      └─────────────┴──────────────┐
                                   ↓
                              Airtable
                                   ↓
                          Transaction Record
```

## Workflow

1. A customer calls the restaurant through the Vapi voice agent.
2. Vapi captures the customer's request and sends structured data to an n8n webhook.
3. n8n extracts and normalizes the incoming data.
4. The request is routed according to its intent.
5. Order requests are validated against menu availability, pricing, and delivery information.
6. Business rules are applied before a transaction is recorded.
7. Confirmed transactions are stored in Airtable.
8. Requests requiring human intervention can be escalated.
9. A structured response is returned to the voice system.

## Key Features

### AI Voice Interaction

Natural voice-based interaction for restaurant customer requests.

### Intent-Based Routing

Requests are routed into different workflows based on their intent:

* Orders
* Reservations
* Customer enquiries
* Human escalation

### Order Validation

The workflow validates:

* Customer information
* Requested items
* Item availability
* Quantity
* Pricing
* Order totals
* Pickup or delivery type

### Delivery Processing

For delivery orders, the workflow can:

* Capture the delivery address
* Match the address against configured delivery zones
* Calculate the applicable delivery fee
* Calculate the final order total

### Transaction Recording

Confirmed transactions are automatically recorded in Airtable, providing a persistent record of processed customer requests.

### Human Escalation

Requests that cannot be reliably handled automatically can be routed for human intervention rather than being silently dropped.

## Technical Implementation

The workflow uses **n8n** as the orchestration and business-logic layer.

Key implementation components include:

* Webhook-based API communication
* JavaScript Code nodes for data transformation and validation
* Switch nodes for intent routing
* IF nodes for conditional business rules
* Merge nodes for combining workflow data
* Airtable for persistent data storage
* Vapi for voice interaction
* Structured responses between the workflow and voice agent

A deterministic validation layer is used after AI extraction to verify information such as menu availability, pricing, delivery zones, and calculated totals.

This prevents the conversational AI layer from being solely responsible for business-critical values.

## Business Value

The system is designed to address the operational problem of **missed or inconsistently captured customer requests**.

By automatically capturing customer information and routing requests through structured workflows, the system can:

* Reduce dependence on manual call handling
* Improve consistency in order capture
* Reduce the likelihood of missed requests
* Validate orders before recording them
* Maintain structured transaction records
* Provide a path for human intervention when automation is insufficient

The core architectural principle is:

> **The AI handles the conversation; the automation handles the business process.**

## Limitations

The current implementation is a **proof-of-concept** demonstrating the core automation workflow.

A production deployment would require additional capabilities such as:

* Dedicated error-handling and retry workflows
* Authentication and stronger webhook security
* Transaction idempotency and duplicate protection
* Production-grade database infrastructure
* Operational monitoring and logging
* Staff dashboard for order and escalation management
* More comprehensive automated testing
* Payment processing
* Access control and audit logging

## Future Development

Potential production improvements include:

1. **Operations Dashboard** — Provide staff with real-time visibility into orders, reservations, escalations, and failed requests.
2. **Error Handling & Monitoring** — Add retry mechanisms, failure workflows, alerts, and execution monitoring.
3. **Database Migration** — Move from Airtable to a production database such as PostgreSQL where appropriate.
4. **Payment Integration** — Add secure online payment processing.
5. **Human-in-the-Loop Controls** — Allow staff to review, modify, approve, or reject automated requests.
6. **Analytics** — Track call volume, successful orders, escalation rates, processing times, and system failures.

## Technology Stack

| Technology     | Purpose                                                   |
| -------------- | --------------------------------------------------------- |
| **Vapi**       | AI voice interface and tool calling                       |
| **n8n**        | Workflow orchestration and business logic                 |
| **OpenAI**     | Language-model processing within the conversational layer |
| **Airtable**   | Operational data and transaction storage                  |
| **JavaScript** | Data transformation and validation                        |
| **Webhooks**   | Communication between Vapi and n8n                        |
| **Telegram**   | Human escalation and operational notifications            |

## Project Evidence

The repository includes workflow exports and screenshots demonstrating the implementation and workflow architecture.

## Project Status

**Proof of Concept — Core workflow implemented**

The current implementation demonstrates the end-to-end automation of customer voice requests through request extraction, intent routing, business validation, transaction recording, and human escalation.
