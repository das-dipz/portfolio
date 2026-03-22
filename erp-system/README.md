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
* Backend powered primarily by Azure Functions for cost-efficient scaling, with optional evolution to containerized services (AKS) for higher control and throughput
* API Gateway for routing, rate limiting, and tenant-aware request handling
* Event-driven architecture using Azure Service Bus to decouple workflows such as order processing, stock updates, and future integrations
* Modular service design to support future domain expansion beyond pharmacy
* Tenant-aware routing and access controls to ensure strict data isolation across clients, preventing cross-tenant data access even within shared infrastructure

**Data Architecture:**

* Separate master databases per business domain (e.g., pharmacy, retail, manufacturing)
* Shared database for client-specific operational data for standard/free-tier clients
* Architecture designed to allow migration of individual clients to dedicated databases with minimal changes to application logic, primarily through a tenant-aware routing layer
* Multi-tenant design driven by cost efficiency and gradual scalability
* Secure storage of connection details and secrets planned using Azure Key Vault

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


---
## 🔹 Key Design Decisions

### 1. Local-First Architecture (Version 2)

The initial versions were intentionally designed as local, offline-first systems.

**Rationale:**

* Reduce infrastructure cost during early validation
* Avoid dependency on internet connectivity in store environments
* Enable quick deployment and adoption without operational overhead

While simpler than a cloud setup, local deployments were supported through custom PowerShell-based automation for application startup, database setup/migration, and backup routines, ensuring reliable operation across client environments.

This approach allowed real-world validation of workflows before investing in a cloud-native architecture.

---

### 2. Technology Stack Selection (Angular + Node.js + PostgreSQL)

The v2 stack was chosen to balance speed of development with flexibility and operational simplicity.

**Frontend (Angular):**

* Chosen for its structured framework and suitability for business applications
* Angular Material enabled rapid development of compact, responsive, and operator-friendly UI
* Well-suited for form-heavy, transactional workflows where billing efficiency and usability were critical

**Backend (Node.js / Express):**

* Lightweight and easy to iterate during early product development
* Sufficient for handling transactional workflows in a local deployment
* Allowed rapid feedback-driven changes without significant overhead

**Database (PostgreSQL):**

* Cost-effective and easy to deploy in local environments
* Flexible schema design suitable for evolving operational workflows
* Supported offline-first deployment with custom PowerShell-based setup and operational automation

---

### 3. Workflow-Oriented System Design

Instead of building generic CRUD modules, the system was designed around real operational workflows.

**Examples:**

* Purchase → Goods Received → Stock Update (with validation step)
* Batch-aware and expiry-aware stock selection during sales
* Support for loose item sales (e.g., tablets from strips)

This ensured the system aligned closely with actual pharmacy operations rather than abstract data models.

---

### 4. Feedback-Driven Iteration

The system evolved based on continuous feedback from real users.

**Key outcomes:**

* Introduction of master data to reduce onboarding friction
* UX improvements in item selection and billing workflows
* Additional modules such as returns, dashboard insights, and bill printing

This iterative approach helped prioritize usability and practicality over premature optimization.

---

### 5. Cost-Driven Architecture Strategy

A key constraint throughout the project has been minimizing cost per client.

**Implications:**

* Delayed cloud adoption until product viability was validated
* Preference for shared infrastructure over isolated resources
* Focus on keeping licensing costs competitive with existing market solutions

This continues to influence architectural decisions in v3.

---

### 6. Transition to Cloud-Native Architecture (Version 3)

The shift to a cloud-native architecture is driven by scalability and multi-client requirements.

**Key considerations:**

* Move from single-client deployments to a shared SaaS model
* Introduce event-driven workflows using Azure Service Bus
* Use API Gateway for routing, rate limiting, and tenant-aware request handling
* Keep infrastructure flexible (Azure Functions initially, with optional evolution to AKS if needed)

The goal is to enable scalability without significantly increasing operational complexity or cost.

---

### 7. Data Strategy and Multi-Tenancy Design

The v3 design introduces a hybrid multi-tenant data strategy.

**Approach:**
- Separate master databases per business domain (e.g., pharmacy, retail, manufacturing)  
- Shared database for client-specific operational data for standard-tier clients  
- Future ability to isolate high-volume clients into dedicated databases  

The schema is designed to support tenant isolation with minimal application change, primarily by introducing a tenant-aware routing layer that determines the correct database target based on user and license context.

This layer is also intended to enforce strict tenant boundaries so that one client cannot accidentally read or write another client’s data, even when multiple tenants share the same database server.

Sensitive configuration such as connection details and related secrets are planned to be managed through Azure Key Vault as part of the cloud-native security model.

---

### 8. Azure SQL vs PostgreSQL (v3 Consideration)

For the cloud-native version, Azure SQL is being considered over PostgreSQL based on the planned SaaS operating model.

**Rationale:**

* Elastic pool support enables multiple client databases to share resources efficiently
* Allows onboarding new clients without immediately increasing database cost
* Supports cross-database querying patterns useful for administrative and reporting scenarios
* Aligns well with the intended multi-tenant architecture and cost model

PostgreSQL remains a viable option, especially from a portability and open-source perspective. However, Azure SQL currently aligns more closely with the desired multi-tenant SaaS design and operational strategy.

---

### 9. Design for Evolution, Not Perfection

A key principle throughout the project has been to prioritize real-world validation and incremental improvement over building a fully optimized system upfront.

This is reflected in:

* Starting with a simple local architecture
* Iteratively expanding features based on usage
* Gradually introducing architectural complexity only when required

This approach helped reduce risk while continuously improving system capability.

---

## 🔹 Trade-offs & Constraints

### 1. Offline-First vs Cloud Scalability

The initial decision to build the system as an offline-first application enabled rapid adoption and low operational cost but limited scalability and centralized visibility.

**Trade-off:**

* Simpler deployment and independence from internet connectivity
* Limited ability to support multi-store operations and centralized reporting

This trade-off was intentional to validate real-world usage before investing in a cloud-native architecture.

---

### 2. Simplicity vs Extensibility (Early Versions)

The v2 architecture prioritizes simplicity and fast iteration over long-term extensibility.

**Trade-off:**

* Faster development and easier maintenance in early stages
* Limited modularization compared to a service-oriented architecture

This was a conscious decision to avoid over-engineering before product-market validation.

---

### 3. Local Deployment vs Operational Overhead

While local deployment reduced infrastructure costs, it introduced operational challenges.

**Trade-off:**

* No recurring hosting cost and better control over deployments
* Required custom PowerShell-based automation for startup, database setup/migration, and backups

This highlighted the need for a more standardized deployment model in future versions.

---

### 4. PostgreSQL vs Azure SQL (Cloud Transition)

PostgreSQL worked well for local deployments but presents challenges when transitioning to a multi-tenant SaaS model.

**Trade-off:**

* PostgreSQL offers flexibility and portability
* Azure SQL provides better support for elastic scaling, multi-tenant cost optimization, and cross-database querying

The choice for v3 is influenced more by operational model requirements than by database capability alone.

---

### 5. Event-Driven Architecture vs System Complexity

Introducing an event-driven architecture in v3 improves scalability and decoupling but adds complexity.

**Trade-off:**

* Better scalability and separation of concerns
* Increased complexity in debugging, monitoring, and system coordination

This approach is planned to be introduced gradually to balance complexity with system maturity.

---

### 6. Shared Database vs Tenant Isolation

The initial multi-tenant strategy favors shared databases to reduce cost.

**Trade-off:**

* Lower infrastructure cost per client
* Increased responsibility to enforce strict tenant isolation

To address this, tenant-aware routing and access controls are introduced to prevent cross-tenant data access.

---

### 7. Cost Optimization vs Feature Expansion

Keeping per-client cost low is a primary constraint, especially in a competitive market.

**Trade-off:**

* Controlled infrastructure spending and competitive pricing
* Slower rollout of advanced features and infrastructure

This constraint continues to influence design decisions across architecture and feature planning.

---

### 8. Rapid Iteration vs Technical Debt

Frequent changes based on user feedback improved usability but introduced areas of technical debt.

**Trade-off:**

* Faster alignment with real user needs
* Need for architectural refactoring in v3

The transition to a cloud-native architecture is also an opportunity to address these limitations.

---
## 🔹 Future Enhancements & Vision

The system is being designed with a long-term vision of evolving into a data-driven and intelligent ERP platform.

**Planned Enhancements:**

* **Enhanced dashboard and analytics:**

  * Introduction of richer KPI-driven dashboards for better operational visibility
  * Exploration of Power BI integration to enable faster development of visualizations and reduce custom frontend implementation effort

* **Customer engagement capabilities (CRM direction):**

  * Integration with messaging platforms such as WhatsApp to enable direct communication with end customers
  * Lays the foundation for broader CRM capabilities

* **Data-driven decision support:**

  * Leveraging collected operational data to provide actionable insights to business owners
  * Examples include identifying fast-moving products, stock optimization, and sales trends

---

**Long-Term Vision:**

* **AI-assisted operations:**

  * Use of AI-driven systems to assist in customer support workflows, reducing manual effort and improving response times
  * Human oversight retained for validation and escalation handling

* **Intelligent business insights:**

  * Applying data analysis and AI models to suggest operational improvements
  * Example: recommending store layout or inventory placement strategies based on historical sales patterns

* **Scalable operational efficiency:**

  * Aim to significantly reduce manual workload while maintaining control and accuracy through a combination of automation and intelligent systems

---



