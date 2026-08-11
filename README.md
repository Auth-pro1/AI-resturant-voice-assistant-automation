# AI Voice Restaurant Automation System

> An **AI voice agent and workflow automation system** designed to automate customer interactions and restaurant processes, including order taking, reservations, menu enquiries, delivery requests, and human escalation.

## Overview

A restaurant receiving multiple customer calls simultaneously can miss or incorrectly capture requests when staff are responsible for manually handling every interaction.

This project explores how **conversational AI and deterministic workflow automation** can work together to reliably capture, validate, process, and record customer requests.

The system connects a Vapi voice agent to an n8n automation workflow that applies restaurant-specific business rules before confirmed transactions are recorded.

**Core flow:**

```text
Customer Call
     ↓
AI Voice Agent
     ↓
Request Capture
     ↓
n8n Workflow
     ↓
Validation & Business Rules
     ↓
Transaction Processing
     ↓
Airtable / Human Escalation
     ↓
Structured Response
```

---

## Problem

A busy restaurant may receive concurrent requests for:

* Food orders
* Reservations
* Menu and pricing enquiries
* Delivery information
* Other customer support requests

When these requests depend entirely on staff availability, some interactions may be missed, incorrectly captured, or require unnecessary manual processing.

The problem addressed by this project was:

> **How can customer requests be captured reliably and processed consistently without requiring staff to manually handle every interaction from start to finish?**

---

## Solution

I built an end-to-end voice automation workflow that:

1. Receives customer calls through a Vapi voice agent.
2. Captures structured information from the conversation.
3. Sends the request to an n8n webhook.
4. Normalizes and extracts the incoming data.
5. Routes the request based on intent.
6. Applies restaurant-specific validation and business rules.
7. Records confirmed transactions in Airtable.
8. Escalates unsupported or exceptional requests when necessary.
9. Returns a structured response to the voice agent.

The architecture deliberately separates **conversation handling** from **business processing**.

> **The AI handles the conversation. The automation handles the business process.**

---

## Workflow Architecture

```text
                    ┌─────────────────────┐
                    │    Customer Call    │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │    Vapi Voice Agent │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │    n8n Webhook      │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │ Data Normalization  │
                    │ & Extraction        │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │    Intent Routing   │
                    └──────────┬──────────┘
                               ↓
             ┌─────────────────┼──────────────────┐
             ↓                 ↓                  ↓
        Order Flow       Reservation Flow    Escalation
             ↓                 ↓
      Business Rules     Validation Rules
             ↓                 ↓
             └─────────────────┼──────────────────┘
                               ↓
                    ┌─────────────────────┐
                    │ Transaction / Data  │
                    │ Processing          │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │ Airtable / Staff    │
                    │ Notification        │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │ Structured Response │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │    Vapi / Customer  │
                    └─────────────────────┘
```

---

## Key Features

* AI-powered voice customer interaction
* Structured request capture through tool calling
* Intent-based workflow routing
* Automated food-order processing
* Menu-item matching and availability validation
* Independent order-total calculation
* Order-total verification
* Pickup and delivery handling
* Delivery-zone and fee validation
* Reservation request processing
* Airtable transaction storage
* Human escalation for unsupported requests
* Structured responses returned to the voice agent

---

## Order Processing Logic

The order workflow contains a deterministic validation layer between the AI conversation and the restaurant's operational data.

### Processing flow

```text
Vapi Tool Call
     ↓
Payload Normalization
     ↓
Customer & Order Extraction
     ↓
Menu Matching
     ↓
Availability Check
     ↓
Price Recalculation
     ↓
Total Verification
     ↓
Pickup / Delivery Routing
     ↓
Delivery-Zone Validation
     ↓
Delivery Fee Calculation
     ↓
Order Recording
     ↓
Response to Vapi
```

### Validation performed

For each order, the workflow can:

* Match requested food items against the configured menu.
* Check whether requested items are available.
* Recalculate item totals using stored menu prices.
* Compare calculated totals against the incoming request.
* Distinguish between pickup and delivery.
* Match delivery addresses against configured delivery zones.
* Calculate applicable delivery fees.
* Identify unavailable or unrecognized items.
* Prevent unsupported requests from being silently processed.

This approach reduces the amount of business-critical logic that depends solely on the language model's output.

---

## Technical Implementation

### n8n Workflow Automation

The workflow uses n8n as the orchestration and business-logic layer.

Key components include:

* Webhook triggers
* Switch-based intent routing
* IF-based validation
* Merge nodes
* JavaScript Code nodes
* Data transformation
* External API integrations
* Structured webhook responses

### Data Normalization

Incoming Vapi tool-call payloads are normalized before downstream processing.

A JavaScript processing layer converts the incoming structure into a consistent internal format, allowing subsequent workflow nodes to work with predictable fields.

### Business Rules

Custom JavaScript logic handles deterministic operations such as:

* Text normalization
* Menu-item matching
* Availability checking
* Price calculation
* Total verification
* Delivery-zone matching
* Delivery-fee calculation
* Request classification

This keeps deterministic business logic inside the workflow rather than relying entirely on the conversational model.

### Data Layer

**Airtable** is used as the operational data layer for:

* Menu items
* Item availability
* Delivery zones
* Delivery fees
* Orders
* Reservation-related data

### Human Escalation

The workflow provides an escalation path for requests that cannot be safely or reliably completed through automation.

This allows the system to fall back to human intervention instead of forcing the AI to make unsupported decisions.

---

## Business Value

The system was designed around a specific operational problem: **missed or incorrectly captured customer requests during busy periods.**

By converting conversations into structured, validated transactions, the workflow can help:

* Reduce dependence on manual request capture.
* Reduce missed customer requests.
* Improve consistency in order processing.
* Reduce manual calculation and availability checks.
* Give staff a structured record of confirmed transactions.
* Route exceptional cases to human staff.

The key architectural principle is:

> **Use AI for unstructured interaction and automation for deterministic business operations.**

---

## Limitations

The current implementation is a working automation prototype rather than a fully deployed production platform.

Current limitations include:

* Menu and availability data require maintenance.
* Delivery matching depends on configured zones.
* Ambiguous requests may require human intervention.
* Voice-agent accuracy can affect extracted information.
* External service availability can affect workflow execution.
* Comprehensive retry and failure-handling mechanisms are not yet implemented.
* Production monitoring and operational dashboards would be required for deployment at scale.

---

## Future Improvements

A production-oriented version could include:

* Robust address and location matching
* Automated menu and inventory synchronization
* Payment processing with Paystack
* Real-time order-status notifications
* More advanced reservation availability management
* Centralized error handling and retry mechanisms
* Conversation and transaction logging
* Operational monitoring dashboard
* Request and failure analytics
* Automated reporting
* Authentication and access control
* Improved observability and workflow monitoring

---

## Technology Stack

| Technology     | Role                                                       |
| -------------- | ---------------------------------------------------------- |
| **Vapi**       | AI voice interface and tool calling                        |
| **n8n**        | Workflow orchestration and business logic                  |
| **OpenAI**     | Language-model processing within the conversational layer  |
| **Airtable**   | Operational data and transaction storage                   |
| **JavaScript** | Data normalization, matching, validation, and calculations |
| **Webhooks**   | Communication between Vapi and n8n                         |
| **Telegram**   | Human escalation and operational notifications             |

---

## Project Outcome

This project demonstrates an approach to building **AI-assisted business automation** where conversational AI is connected to deterministic workflows and structured business data.

Rather than treating AI as a standalone chatbot, the system combines:

**Voice AI → Structured data → Workflow orchestration → Business rules → Validation → Transaction processing → Persistent storage → Human escalation**

The result is a workflow architecture designed to make AI useful within an operational process rather than simply generating conversational responses.

---

## Production Considerations

For production deployment, the next engineering layer would focus on:

* Reliability
* Error handling
* Retry strategies
* Monitoring
* Authentication
* Data security
* Observability
* Operational dashboards
* Automated testing
* External-service failure recovery

These additions would move the project from a functional automation prototype toward a more production-ready system.
