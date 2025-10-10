# ADR-003: Deployment & Orchestration - Docker with Coolify

**Date:** October 10, 2025
**Status:** Ratified

## Context

The Equilibria Cognitive Concierge project requires a deployment strategy that balances development velocity, scalability, security, and long-term sovereignty. As a full-stack Next.js application with a Supabase backend, several deployment options were considered:

1.  **Fully Managed Platform (e.g., Vercel):** Deploying the Next.js frontend to a platform like Vercel, which is highly optimized for it.
2.  **Traditional Cloud Infrastructure (e.g., AWS/GCP):** Manually managing virtual machines or Kubernetes clusters to host the application.
3.  **Self-Hosted PaaS (Platform-as-a-Service):** Using a self-hosted platform to simplify the management of our own infrastructure.

A core non-functional requirement of this project is to retain full control over our data and infrastructure, avoiding long-term vendor lock-in with any single hosting provider.

## Decision

We will adopt a **container-based deployment strategy using Docker**. The orchestration and management of these containers will be handled by **Coolify**, a self-hostable Platform-as-a-Service.

This means both the Next.js application and our optional self-hosted Supabase instance will be packaged as Docker containers and deployed to a Virtual Private Server (VPS) that we control, with Coolify serving as the management and CI/CD layer.

## Rationale & Justification

This decision provides the optimal balance of developer experience, operational control, and long-term strategic independence.

1.  **Data Sovereignty & No Vendor Lock-in:**
    *   This is the primary driver of the decision. By self-hosting on our own infrastructure, we retain absolute control over our application and its data. Using open-source tools like Docker and Coolify ensures our infrastructure is portable and not tied to any proprietary platform. This directly fulfills a core, long-term strategic goal.

2.  **Simplified DevOps & Increased Velocity:**
    *   While self-hosting can be complex, Coolify acts as an abstraction layer that provides a "Heroku-like" or "Vercel-like" developer experience on our own servers. It handles the complexities of Docker, reverse proxies, SSL certificates, and GitHub webhooks for CI/CD. This allows a small team to manage a production-grade environment without requiring a dedicated DevOps expert, thus preserving our development velocity.

3.  **Unified & Consistent Environments:**
    *   Docker ensures that our development, staging, and production environments are identical, which eliminates the "it works on my machine" class of bugs. A `docker-compose.yml` file will allow us to spin up the entire stack (Next.js app + Supabase) locally with a single command.

4.  **Cost-Effectiveness at Scale:**
    *   While managed platforms are often cheaper to start, their costs can scale exponentially. A self-hosted model on a VPS provider has a more predictable and often significantly lower cost at medium to large scale.

## Consequences

*   **Positive:** We achieve full control over our infrastructure and data, avoid vendor lock-in, and gain a powerful, simplified DevOps workflow that will support us from the MVP to a large-scale production application.
*   **Negative:** We are taking on the responsibility for the uptime and security of the underlying server infrastructure. This is a significant responsibility that does not exist with a managed platform like Vercel.
*   **Mitigation:** We are mitigating this risk by using Coolify, which automates many of the most complex and error-prone aspects of server management, such as deployment pipelines, SSL, and security updates. We are also selecting a reputable VPS provider with a strong uptime SLA.