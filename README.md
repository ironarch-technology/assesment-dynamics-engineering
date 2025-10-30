# Tehcnical Assessment -- Dynamics
Technical Architecture & Engineering Exercise — Dynamics 365 / Power Platform (.NET Core)

---

## 🎯 Purpose

This is to assesses your ability to design, configure, and extend Microsoft Dynamics 365 / Power Platform. Using AI and other tooling is 

You’ll build a simplified request routing solution that demonstrates:
- Practical schema design and Dataverse modeling
- Implementation of logic in both **low-code (Power Automate)** and **code (.NET Core plugin)**
- Secure and auditable integration design
- Thoughtful decision-making about architecture trade-offs

You will **bring your working artifacts** to the interview and walk the team through your solution, design choices, and reasoning.

---

## Scenario

An enterprise CRM receives inbound **Requests** from multiple channels.  
Each Request must be automatically routed to the appropriate queue based on its Category, Topic, and Channel.  
Later, when a Request is resolved, the system should send a notification to an external service.

You’ll create:
1. A **Dataverse schema** that supports configurable routing  
2. Two routing implementations — one in Dynamics (Power Automate) and one in code (.NET plugin)  
3. A lightweight **external API integration** simulation  

---

### Part 1 — Data Model Design

Design a schema in Dataverse that supports configurable routing.

| Table | Purpose | Key Fields |
|--------|----------|------------|
| **Request** | Represents inbound requests | Category, Topic, Channel, Queue, Routing Status, Created On |
| **Routing Rule** | Defines mapping logic | Category, Topic, Channel, Target Queue |
| **Routing Audit** | Logs routing activity | Request, Timestamp, Status, Message |

> You may add optional fields or relationships if you believe they add value.

**Be ready to discuss:**
- Why you structured your schema the way you did
- How you would scale or extend it for complex routing or multi-tenant scenarios

---

### Part 2 — Routing Logic

You will implement **two** routing mechanisms:
- One using **Dynamics configuration / Power Automate**
- One using **code (.NET Core plugin)**

#### Low-Code Routing (Power Automate)
**Goal:** Use Power Automate to assign queues automatically when a Request is created.

**Requirements:**
1. Trigger on record creation of **Request**  
2. Look up the matching **Routing Rule**  
3. Set the Request’s Queue and Routing Status  
4. Create a **Routing Audit** record  
5. Handle the “no match” case gracefully  

**Be ready to discuss:**
- Your design logic in Power Automate
- How non-developers could maintain or extend your flow
- When Power Automate becomes the wrong tool for a given requirement

---

#### Coded Routing (.NET 6+ Plugin)
**Goal:** Implement a backend plugin that performs similar routing logic in C#.

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

---

### Part 3 — External Integration

Simulate an external API call when a Request is resolved.

**Requirements:**
1. Trigger when Request Status = “Resolved”  
2. Send a POST request to a mock endpoint (e.g., [webhook.site](https://webhook.site))  
3. Log response (success/failure) in a **Notification Log** table  
4. Describe in writing or conversation how you would secure secrets and credentials (e.g., OAuth2, Azure Key Vault, Managed Identity)

**Be ready to discuss:**
- How you structured your integration logic  
- How you would handle authentication and retries  
- How this would fit into a production-grade solution

---

## 🧠 Discussion Topics During Walkthrough

During your interview, you’ll walk us through:
- Your Dataverse schema and reasoning  
- How and why you split logic between Power Automate and code  
- Your plugin architecture, error handling, and performance considerations  
- Your approach to security and API integrations  
- How you would extend this design for a larger enterprise implementation  

---

## 🧰 Environment Setup

A separate **Environment Setup Guide** provides instructions for:
- Creating a free Power Platform Developer environment  
- Connecting via the .NET Dataverse SDK  
- Registering plugins using Power Platform CLI (`pac plugin`)  
- Managing solutions and exporting artifacts

If you’re experienced with Dynamics, feel free to use your preferred setup.

---

## 📍 Format for the Interview

Bring your completed artifacts and be prepared to:
1. Share your screen and demo your environment or flows  
2. Walk through your Power Automate logic and your C# plugin code  
3. Explain your architectural choices and trade-offs  

We’re not evaluating how flashy it looks — we’re evaluating your **reasoning, architecture, and grasp of Dynamics 365 extensibility**.

---

### 🔚 Summary

**You’ll demonstrate:**
- Schema modeling in Dataverse  
- Workflow automation with Power Automate  
- Plugin development using .NET 6+  
- Secure API integration patterns  
- Clear architectural decision-making  

This mirrors how IronArch Dynamics engineers design, implement, and reason about production-ready, maintainable CRM systems.

---

