# ADR-001: Framework Selection - Next.js App Router

**Date:** October 10, 2025
**Status:** Ratified

## Context

The initial architectural decision for the Equilibria Cognitive Concierge required a web framework that could support a rich, interactive, and highly performant user experience while also providing a robust foundation for server-side logic, AI integration, and future scalability. The core requirements from the PRD included:

1.  **Full-Stack Capabilities:** The need for integrated server-side logic (API routes or serverless functions) to handle the RAG pipeline, authentication, and database interactions.
2.  **High Performance & SEO:** The home page and initial-load experiences must be server-rendered for fast Largest Contentful Paint (LCP) and optimal Search Engine Optimization.
3.  **Modern React Ecosystem:** The framework must be built on a modern version of React (18+) and support the latest patterns and libraries, such as TypeScript and server-state management tools.
4.  **Production-Grade Foundations:** The framework needed to provide out-of-the-box solutions for routing, code splitting, and performance optimization.

The primary contenders evaluated were **React+Vite**, **Remix**, and **Next.js**.

## Decision

We will exclusively use **Next.js 14+ with the App Router** as the foundational framework for this project.

## Rationale & Justification

Next.js was chosen because it is the only framework that provides a direct, one-to-one implementation path for every single technical requirement outlined in the project's PRD.

1.  **Perfect Alignment with Full-Stack Vision:**
    *   The Next.js App Router provides a unified environment for both frontend UI (Client and Server Components) and backend logic (API Routes and Server Actions). This directly fulfills our need for an integrated RAG pipeline and authentication backend without the complexity of managing a separate server, a failing of the React+Vite approach.

2.  **Superior Rendering Flexibility:**
    *   Next.js's support for both Server-Side Rendering (SSR) and Static Site Generation (SSG) at the route level allows us to deliver the fast, SEO-friendly landing pages required by the PRD, while still enabling the highly interactive, client-side rendered experience needed for the main concierge interface.

3.  **Architectural Alignment with React Server Components (RSC):**
    *   The PRD was explicitly designed to leverage the power of the RSC architecture for data fetching and rendering. This pattern, which is at the core of the Next.js App Router, allows for more granular loading states (via `Suspense`), reduced client-side bundle sizes, and a more modern approach to data flow. While Remix is a powerful SSR framework, its loader-based paradigm represents a philosophical and implementation mismatch with our PRD's specific intent.

4.  **Ecosystem Maturity & Production Readiness:**
    *   Next.js provides built-in, production-grade solutions for critical infrastructure like routing, code splitting, and image optimization. This aligns with our **`Production-Grade Standard`** and accelerates development by eliminating the need for manual configuration of these complex systems.

## Consequences

*   **Positive:** We adopt a powerful, widely-supported framework that is a perfect architectural match for our requirements, maximizing development velocity and ensuring a scalable foundation.
*   **Negative:** We are committing to the Vercel/Next.js ecosystem and its specific architectural patterns. This is a calculated trade-off that we accept due to the framework's overwhelming alignment with our project goals.
*   **Mitigation:** Our decision to use a self-hosted deployment model (Docker/Coolify) mitigates the risk of vendor lock-in with the Vercel hosting platform, giving us long-term infrastructural sovereignty.