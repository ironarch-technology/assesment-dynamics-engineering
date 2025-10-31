# Technical Assessment -- Dynamics
Technical Architecture & Engineering Exercise — Dynamics 365 / Power Platform (.NET Core)

## Purpose

This is to assesses your ability to design, configure, and extend Microsoft Dynamics 365 / Power Platform. Using AI and other tooling is 

You’ll build a simplified request routing solution that demonstrates:
- Practical schema design and Dataverse modeling
- Implementation of routing logic in a **.NET Core plugin** (C#)
- Thoughtful decision-making about architecture trade-offs, including when you might choose a low-code/no-code (LCNC) option like Power Automate

You will **bring your working artifacts** to the interview and walk the team through your solution, design choices, and reasoning.

## Scenario

An enterprise CRM receives inbound **Requests** from multiple channels.  
Each Request must be automatically routed to the appropriate queue based on its Category, Topic, and Channel.  
Later, when a Request is resolved, the system should send a notification to an external service.

You’ll:
1. Create a **Dataverse schema** that supports configurable routing
2. Implement routing logic in a **.NET Core plugin**
3. Talk us through how you would approach the same logic using a low-code/no-code (LCNC) option (Power Automate), and why you might choose one approach over the other

### Part 1 — Data Model Design

Design a schema in Dataverse that supports configurable routing.

| Table | Purpose | Key Fields |
|--------|----------|------------|
| **Request** | Represents inbound requests | Category, Topic, Channel, Queue, Routing Status, Created On |
| **Routing Rule** | Defines mapping logic | Category, Topic, Channel, Target Queue |
| **Routing Audit** | Logs routing activity | Request, Timestamp, Status, Message |

You may add optional fields or relationships if you believe they add value.

**Be ready to discuss:**
- Why you structured your schema the way you did
- How you would scale or extend it for complex routing or multi-tenant scenarios

### Part 2 — Routing Logic (Plugin Focus)

You will implement the routing logic primarily in a **.NET Core plugin** (C#):

**Requirements:**
1. Trigger on **Create** of Request (PostOperation, Async)
2. Query the Routing Rule table via Dataverse SDK
3. Assign Queue and create Routing Audit entry
4. Include structured error handling and tracing
5. Use `Microsoft.PowerPlatform.Dataverse.Client` SDK

**Be ready to discuss:**
- Your plugin structure and error-handling approach
- Performance and scalability considerations
- Why you chose to implement certain logic in code vs. configuration

### Part 3 — LCNC Option (Discussion)

We would also like to hear your thoughts on how you would approach the same routing logic using a low-code/no-code (LCNC) option such as Power Automate. Please be prepared to discuss:
- Your design logic in Power Automate
- How non-developers could maintain or extend your flow
- When Power Automate becomes the wrong tool for a given requirement

## Discussion Topics During Walkthrough

During your interview, you’ll walk us through:
- Your Dataverse schema and reasoning
- Your plugin architecture, error handling, and performance considerations
- Your thoughts on the LCNC (Power Automate) option and when you might choose it
- How you would extend this design for a larger enterprise implementation

## Environment Setup

A separate **Environment Setup Guide** provides instructions for:
- Creating a free Power Platform Developer environment
- Connecting via the .NET Dataverse SDK
- Registering plugins using Power Platform CLI (`pac plugin`)
- Managing solutions and exporting artifacts

If you’re experienced with Dynamics, feel free to use your preferred setup.

**You’ll demonstrate:**
- Schema modeling in Dataverse
- Plugin development using .NET 6+
- Clear architectural decision-making, including trade-offs between coded and LCNC approaches

This mirrors how IronArch Dynamics engineers design, implement, and reason about production-ready, maintainable CRM systems.
