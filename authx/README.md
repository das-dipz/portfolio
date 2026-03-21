# AuthX – Reusable Authentication Widget

## 🔹 Overview

AuthX is a lightweight, framework-agnostic authentication system designed for embedded environments like Shopify (especially non-Shopify+ stores), enabling a seamless, plug-and-play login experience without relying on expensive platform upgrades.

The system was built to overcome limitations of Shopify’s default authentication (email-only login) by introducing OTP-based login and social authentication, while keeping costs minimal and integration simple.

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
* **Host Application (Shopify Theme)** → Business logic handling via events

### Flow:

1. Widget is loaded into Shopify theme
2. UI is dynamically injected into DOM
3. User initiates login (OTP or Google)
4. Backend handles authentication (OTP / token generation)
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
* No direct coupling with backend APIs or Shopify logic
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
* OAuth configuration managed per client (origins and redirect URLs)

---

## 🔹 Key Features

* Mobile and Email OTP login
* Social login integration (Google)
* Dynamic UI injection into host applications
* Event-based communication model
* Config-driven multi-client support
* Lightweight, framework-independent design

---

## 🔹 Trade-offs & Limitations

* No built-in rate limiting for OTP requests (prioritized speed of delivery)
* Manual DOM manipulation increases maintenance complexity
* No Web Component / Shadow DOM isolation (potential style conflicts)
* Logging and observability are minimal in current version
* Not optimized for high-scale or enterprise-grade workloads

> Note: The project lifecycle was limited as the associated business was discontinued early, so long-term scaling and production hardening were not fully realized.

---

## 🔹 Future Enhancements

* Introduce structured logging and monitoring (Azure Workbooks / App Insights)
* Implement rate limiting and abuse protection for OTP flows
* Move towards Web Components for better encapsulation
* Improve session/token lifecycle handling
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
