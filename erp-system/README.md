## 🔹 Overview

This project started as a lightweight ERP system built for a real pharmacy store to address operational inefficiencies in manual workflows.

The primary challenges were high time required to complete billing transactions, lack of real-time stock visibility, and frequent human errors due to manual processes. Existing solutions in the market introduced recurring costs and required a shift from traditional workflows, which created resistance from the business owner.

The system was initially developed as a focused inventory and sales solution to validate real-world usability before expanding into a broader ERP platform.

Over time, based on continuous feedback and real usage, the system evolved into a more comprehensive ERP platform covering inventory management, purchase workflows, sales, returns, and basic analytics.

The current direction is to transition the system from a local, offline-first application to a cloud-native, event-driven architecture, enabling multi-store support, improved scalability, and reduced operational overhead. The system has been deployed across multiple independent client setups, each operating in an offline-first environment.

The long-term vision is to evolve this into a generic, extensible ERP platform that can move beyond pharmacy-specific workflows to support broader business operations such as CRM, HR, project management and related business workflows, while maintaining a modular and domain-driven architecture.

---
## 🔹 System Evolution

### Version 1 – Initial Operational Prototype

The first version was built to validate whether a lightweight digital workflow could practically replace manual pharmacy operations without introducing excessive complexity.

**Scope included:**

* Manual item and vendor management
* Stock addition for existing items
* Basic sales flow for stocked items

An Electron wrapper was initially explored to position the product as a desktop application, but this approach was quickly dropped due to noticeable rendering lag and poor user experience even in local testing.

The biggest limitation in this version was the initial onboarding effort. Item details had to be entered manually, which made setup slow, error-prone, and impractical at scale.

---

### Version 2 – Workflow Expansion and Usability Improvements

The second version focused on solving onboarding friction, improving day-to-day usability, and expanding the product into a more complete operational system.

A major improvement was the introduction of a large medicine master dataset covering 750K+ products available in the Indian market. This significantly reduced initial setup effort by allowing users to select from preloaded product data instead of manually entering every item.

This version also evolved based on direct live feedback from pharmacy staff and owners.

**Key improvements included:**

* Faster onboarding through master data selection
* UX refinements in sales and stock selection flows
* Displaying available stock counts while selecting items for sale
* Support for selling loose quantities, such as individual tablets from a strip
* Purchase order tracking with a staged goods-receipt confirmation step
* Customer returns handling
* Custom bill printing
* Dashboard-based operational insights

A notable workflow control introduced here was the separation between **order placed**, **goods received**, and **stock update**. When goods arrive, the user is expected to reconcile the physical invoice against the prepopulated purchase details, correct discrepancies if needed, and only then update stock. This was intentionally introduced to reduce stock inconsistencies and improve operational accuracy.

Sales handling in this version is also batch-aware and expiry-aware. When multiple stock batches exist for the same item, the system programmatically suggests the batch that expires first, while still allowing the salesperson to override the choice based on the actual physical item available.

Version 2 continues to run as a local, offline-first system. Startup and backup flows are supported through PowerShell scripts, and the same deployment model has been extended across multiple client installations operating independently.

---

### Version 3 – Cloud-Native and Multi-Client Evolution

The current version is a work in progress and represents a broader architectural shift rather than only incremental feature addition.

The goal of this version is to evolve the product from a locally deployed pharmacy solution into a cloud-native, event-driven ERP platform that can support multiple clients more efficiently and scale over time.

**Planned directions include:**

* Barcode-driven workflows
* Azure-based image processing to reduce manual item selection
* Multilingual support
* Account reconciliation
* Multi-store support
* Licensing support
* Better tenant isolation strategies
* Lower operational cost per client through shared infrastructure where appropriate

The longer-term objective is to make the platform extensible enough to move beyond pharmacy-specific workflows and grow into a more generic ERP foundation.

---

## 🔹 Architecture Overview

The system has evolved across two distinct architectural stages — a local, offline-first implementation (v2) and a planned cloud-native, multi-tenant architecture (v3).

---

### Version 2 – Local, Offline-First Architecture

The current production version follows a simple, cost-effective architecture optimized for real-world validation and rapid iteration.

**Key Characteristics:**

* Angular 13 frontend organized around operational workflows (stock, purchase, sales, returns, dashboard)
* Node.js (Express) backend exposing domain-focused APIs
* Single PostgreSQL database per client deployment
* Fully local hosting with no external cloud dependency
* Startup and backup automation using PowerShell scripts

**Design Intent:**

* Minimize infrastructure cost during early validation
* Ensure reliability in environments with limited connectivity
* Enable rapid feedback-driven iteration from real users

**Notable Design Aspects:**

* Batch-aware and expiry-aware stock handling during sales
* Explicit separation of purchase → goods receipt → stock update workflow
* Feature-oriented UI structure aligned with real operational flows

---

### Version 3 – Cloud-Native, Multi-Tenant Architecture (In Progress)

Version 3 represents a shift from a single-client, offline system to a scalable, cloud-native ERP platform.

**Planned Characteristics:**

* Angular 16-based shared SaaS frontend
* Backend powered primarily by Azure Functions, with optional evolution to containerized services (AKS) if required for scaling
* API Gateway for routing, rate limiting, and tenant-aware request handling
* Event-driven architecture using Azure Service Bus for asynchronous workflows
* Modular service design to support future domain expansion beyond pharmacy

**Data Architecture:**

* Separate master databases per business domain (e.g., pharmacy, retail, manufacturing)
* Shared database for client-specific operational data for standard/free-tier clients
* Architecture designed to allow seamless migration of individual clients to dedicated databases without schema changes
* Multi-tenant design driven by cost efficiency and gradual scalability

**Key Architectural Drivers:**

* Keeping per-client operational cost low to remain competitive in the market
* Supporting gradual migration from single-store to multi-store setups
* Enabling future expansion into broader ERP domains such as CRM, HR, and project management
* Designing for flexibility in infrastructure choices based on cost vs scale trade-offs

**Future Capabilities:**

* Barcode-driven workflows and reduced manual input
* Image processing for product identification
* Account reconciliation as a foundation for compliance workflows (e.g., GST-related processes)
* Multi-language support
* Licensing-based feature and infrastructure segmentation
