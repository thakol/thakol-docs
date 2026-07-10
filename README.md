# Thakol.io — Public Documentation

Welcome to the public documentation repository for **Thakol.io**, a developer-focused authentication engine designed for modern web applications and APIs.

Thakol provides the enterprise-grade identity features of **Keycloak** (like MFA, WebAuthn, SSO, and JIT password migrations) but abstracts away the deployment, scaling, configuration, and complex pricing, delivering a simple subscription-free experience.

---

## 🧭 Navigation Guide

To help you integrate and understand Thakol, this documentation is organized into the following sections:

1. 🚀 [**How It Works & OIDC Core**](./how-it-works.md)
   - Read about Thakol's OIDC (OpenID Connect) architecture, workspace realms, client scopes, and security comparisons with proprietary SaaS solutions (like Clerk or Auth0).
2. 🛡️ [**Platform Security & Features**](./features.md)
   - Explore Thakol's core developer features: dynamic branding theme overlays, zero-downtime JIT (Just-In-Time) user migrations, Multi-Factor Authentication (MFA), active session tracking, and brute-force defenses.
3. 🛠️ [**OIDC Integration Guide**](./integration-guide.md)
   - Step-by-step code samples to implement OIDC login and JWT API gating in various languages/frameworks:
     - **JavaScript (Vanilla)**
     - **React.js**
     - **Angular**
     - **Next.js**
     - **Node.js (Express)**
     - **Python (FastAPI)**
     - **Go**
4. 🤖 [**AI Agent Integration**](./ai-agents.md)
   - Drop-in agents/skills that let your AI coding assistant (Claude, GitHub Copilot, Codex, Cursor, Windsurf, Gemini) add Thakol authentication for you. Source: [thakol-ai-agents](https://github.com/thakol/thakol-ai-agents).

---

## ⚡ Core Philosophy

Traditional authentication SaaS platforms charge based on **Monthly Active Users (MAUs)**. This pricing structure penalizes products that scale. Thakol introduces a unique **Pay-As-You-Go Credit Model**:

* **No Monthly Subscriptions**: Register for free and get **5,000 logins** (50 tokens) included.
* **Refill as Needed**: Top up your balance when you need more logins. 1 token = 100 successful logins.
* **Non-expiring Credits**: Paid credits never expire, meaning you only pay for actual logins.
