# ADR-002: Backend Platform Selection - Supabase

**Date:** October 10, 2025
**Status:** Ratified

## Context

The Equilibria Cognitive Concierge requires a robust, scalable, and secure backend to manage several critical services:

1.  **User Authentication:** A complete system for handling user sign-up, sign-in (email/password and social OAuth), session management, and role-based access control.
2.  **Database:** A relational database to store application data, including user profiles, conversation histories, and administrative configurations.
3.  **Vector Database:** A specialized database capability to store vector embeddings and perform high-speed similarity searches, which is the core requirement for our Retrieval-Augmented Generation (RAG) pipeline.
4.  **Serverless Functions:** A platform for hosting and executing server-side logic for the RAG pipeline, knowledge base ingestion, and other secure operations.

The decision was whether to assemble these services from multiple, separate providers (e.g., a database provider, an auth provider, a vector DB provider, and a serverless function provider) or to use a single, integrated platform.

## Decision

We will exclusively use **Supabase** as our primary backend-as-a-service (BaaS) platform. This includes leveraging its integrated **Authentication**, **PostgreSQL Database** (with the `pgvector` extension), and **Edge Functions**.

## Rationale & Justification

Supabase was chosen because it provides a single, cohesive, and open-source solution that meets all four of our critical backend requirements, thereby maximizing development velocity and minimizing architectural complexity.

1.  **Reduced Coordination Overhead & Increased Velocity:**
    *   By providing authentication, a relational database, a vector database, and serverless functions in one platform, Supabase eliminates the significant engineering overhead of integrating, managing, and securing four separate services. This directly aligns with our `Extreme Development Velocity` mandate.

2.  **Elegant Solution for RAG Pipeline (`pgvector`):**
    *   Supabase's support for the `pgvector` extension is a massive architectural advantage. It allows us to store our vector embeddings *in the same PostgreSQL database* as our content metadata. This avoids the complexity and cost of maintaining a separate, specialized vector database (like Pinecone or Weaviate). It enables powerful, single-pass queries that can combine relational filters with vector similarity searches, dramatically simplifying our RAG implementation.

3.  **Production-Grade & Secure Foundations:**
    *   Supabase is built on a foundation of trusted, enterprise-grade open-source technologies: PostgreSQL for the database, GoTrue for authentication, and Deno for Edge Functions. Its built-in Row Level Security (RLS) provides a powerful, fine-grained mechanism for securing our data at the database layer, which is a core requirement of our security model.

4.  **Alignment with Self-Hosting Goal:**
    *   Because Supabase is open source, it provides a clear path to our long-term goal of a fully self-hosted deployment. While we will start with the Supabase Cloud platform for velocity, we can migrate to a self-hosted instance (via Docker, managed by Coolify) in the future without changing our application's codebase, thus avoiding vendor lock-in.

## Consequences

*   **Positive:** We adopt a single, powerful, and cost-effective backend platform that perfectly matches our technical requirements, is secure by default, and accelerates our development timeline.
*   **Negative:** We are committing to the Supabase ecosystem and its specific client libraries. The performance of our vector search will be tied to the performance of our PostgreSQL instance, which may require more manual tuning at extreme scale compared to a purpose-built vector database.
*   **Mitigation:** The performance trade-off is a calculated one. For our initial scale, the simplicity and power of `pgvector` far outweigh the potential for performance issues. If we reach a scale where `pgvector` is a bottleneck, the modular nature of our architecture allows us to swap it out for a specialized database in the future.