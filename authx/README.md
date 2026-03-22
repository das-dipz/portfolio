# AuthX – Reusable Authentication Widget

## 🔹 Overview

AuthX is a lightweight, framework-agnostic authentication system designed for embedded environments like Shopify, particularly for non-Shopify+ stores, enabling a seamless, plug-and-play login experience.

It combines a reusable JavaScript widget with an Azure Functions–based backend to support OTP-based authentication and Google OAuth, along with customizable JWT-based identity management.

The system was built to overcome limitations of Shopify’s default authentication (email-only login) by introducing flexible login options while keeping integration lightweight and operational costs minimal.

While third-party Shopify apps provide similar capabilities, they often introduce recurring costs and limited customization. AuthX was designed as a cost-effective and extensible alternative, giving full control over authentication flows and enabling reuse across multiple storefronts.


---

## 🔹 Problem Statement

Shopify’s default authentication (in the Dawn theme) supports only basic email-based login. For small and growing businesses:

* OTP-based login was not available out of the box
* Upgrading to Shopify+ was not a viable option due to cost
* Custom solutions needed to be lightweight and easily deployable

The goal was to build a **portable, low-cost authentication layer** that could be reused across multiple Shopify storefronts.

---

## 🔹 Architecture Overview

AuthX is designed as a **decoupled system** consisting of:

* **Frontend Widget (JavaScript)** → UI + interaction layer
* **Backend (Azure Functions - .NET Core)** → OTP generation, token issuance, integrations
* **Host Application (Shopify Theme)** → Consumes widget events to handle post-login behavior

This separation allows the authentication layer to remain reusable across different storefronts without coupling to specific business logic.

### Flow:

1. Widget is loaded into Shopify theme
2. UI is dynamically injected into DOM
3. User initiates login (OTP or Google)
4. Backend validates identity (OTP or OAuth), provisions/fetches customer, and generates JWT
5. Widget emits events (e.g., login success)
6. Host application handles post-login behavior (redirect, cart sync, etc.)

---

## 🔹 Key Design Decisions

### 1. Framework-Agnostic Implementation

* Initially explored Angular, but faced integration issues with Shopify theme (UI components not rendering correctly)
* Switched to pure JavaScript for:

  * Maximum compatibility
  * Lightweight footprint
  * Easy embedding across sites

---

### 2. Event-Based Integration Model

* Widget communicates via custom events (e.g., `authx:login-success`)
* Decouples UI from host application logic using custom events
* Backend communication is handled via API calls, while host-specific behavior is event-driven
* Allows each client/storefront to handle behavior differently

---

### 3. Config-Driven Design

* Uses `window.AUTHX_CONFIG` for runtime configuration
* Enables:

  * Different API endpoints
  * Client-specific redirect URLs
  * Feature toggles (OTP/social login)

---

### 4. Cost-Optimized Backend

* Backend implemented using Azure Functions (.NET Core)
* Designed to serve multiple clients using shared infrastructure
* Suitable for low-to-moderate traffic scenarios with minimal cost
* Stateless function-based design reduces infrastructure overhead

---

## 🔹 Authentication Flows

### OTP-Based Login

* OTP generation and validation handled via Azure Functions
* Frontend manages:

  * Input validation
  * Timer and retry flow
* Designed for simplicity and fast user onboarding

---

### Google Login

* Integrated using Google OAuth 2.0
* Frontend handles login initiation
* OAuth configuration is managed per client (origins and redirect URLs)

---

### OTP Delivery Strategy

For OTP delivery, lightweight transactional email providers were used to balance cost and reliability.

* Initially used SendGrid leveraging its free-tier limits for low-volume usage
* Later migrated to Brevo for more cost-effective scaling without additional setup overhead
* The integration was designed to be loosely coupled, so switching providers required only minimal code changes based on provider-specific APIs

The approach prioritized minimal operational cost while maintaining flexibility for future scaling.


---

## 🔹 Key Features

* Mobile and Email OTP login
* Social login integration (Google)
* Dynamic UI injection into host applications
* Event-based communication model
* Config-driven multi-client support
* Lightweight, framework-independent design
* JWT-based identity propagation across storefront and profile experience

---

## 🔹 Trade-offs & Limitations

* Rate limiting for OTP requests is not implemented in the current version (prioritized speed of delivery)
* Manual DOM manipulation increases maintenance complexity
* No Web Component / Shadow DOM isolation (potential style conflicts)
* Logging and observability are minimal in current version
* Not optimized for high-scale or enterprise-grade workloads
* Backend abstraction is intentionally minimal, prioritizing simplicity and faster iteration over extensibility

> Note: The project lifecycle was limited as the associated business was discontinued early, so long-term scaling and production hardening were not fully realized.

---

## 🔹 Future Enhancements

* Introduce structured logging and monitoring (Azure Workbooks / App Insights)
* Implement rate limiting and abuse protection for OTP flows
* Move towards Web Components for better encapsulation
* Improve session and token lifecycle management
* Expand multi-tenant configuration and management
* Enhance UI/UX polish and responsiveness

---
## 🔹 Key Takeaways

This project demonstrates:

* Designing reusable, embeddable systems under platform constraints
* Building cost-effective solutions using serverless architecture
* Decoupling UI and backend through event-based integration
* Making pragmatic trade-offs to meet real-world timelines and constraints

---

## 🔹 Discussion

I’m happy to walk through the architecture, design decisions, and trade-offs in more detail, including how this system could be extended into a more scalable authentication platform.

---

## 🔹 Architecture Diagram

```mermaid
flowchart LR

    A[User on Shopify Storefront]

    subgraph Frontend["Frontend Layer"]
        B[AuthX JS Widget]
        C{Choose Login Method}
        K[Browser Local Storage]
        L[Profile Page UI]
    end

    subgraph Backend["Backend Layer"]
        E[Azure Functions API]
        J[JWT Token]
    end

    subgraph OTP["OTP Flow"]
        D[Request OTP]
        F[Brevo Email and SMS]
    end

    subgraph OAuth["OAuth Flow"]
        G[Redirect to Google OAuth]
        H[Google OAuth]
    end

    subgraph Commerce["Commerce Integration"]
        I[Shopify Admin API]
    end

    A -->|Clicks Login| B
    B --> C

    C -->|OTP| D
    D --> E
    E -->|Send OTP| F
    F -->|OTP Delivered| A
    A -->|Enter OTP| B
    B -->|Verify OTP| E

    C -->|Google| G
    G --> H
    H -->|Callback| E

    E -->|Create or Fetch Customer| I
    E -->|Generate JWT| J
    J --> B

    B -->|Store Token and Claims| K
    K --> L
