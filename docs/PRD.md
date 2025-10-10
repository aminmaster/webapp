# Product Requirements Document: Equilibria Cognitive Concierge

**Version:** 1.0 (Initial Production Release)  
**Date:** October 2025  
**Status:** Approved for Development  
**Project Name:** `webapp`

**Revision History:**  
-   v1.0: Definitive, unabridged specification for the full-stack Next.js implementation. This document incorporates all features and architectural decisions from the initial prototyping phase, is fully aligned with the Dyad AI-assisted development workflow, and serves as the single source of truth for the v1.0 build.

---

### **1.0 Executive Summary & Vision**

**1.1. Introduction**

The Equilibria Cognitive Concierge is an advanced, full-stack web application engineered to serve as an intelligent, conversational interface for exploring and synthesizing information. Built on a modern, production-grade technology stack centered around Next.js 14+, the platform is designed to provide users with context-aware dialogues powered by a sophisticated Retrieval-Augmented Generation (RAG) pipeline. This pipeline leverages multiple AI providers and user-curated knowledge bases to deliver accurate, relevant, and trustworthy responses. The entire development lifecycle is orchestrated within the Dyad.sh AI-assisted environment, ensuring rapid, consistent, and high-quality iteration.

**1.2. Core Vision**

Our vision is to create a humane, accessible, and extensible "cognitive companion" that democratizes access to knowledge. The platform will empower users to engage in natural language conversations with AI agents that draw from specific, verifiable sources of information. We prioritize a frictionless user experience, beginning with immediate, un-gated access for anonymous visitors, which gracefully evolves into a persistent and personalized experience for registered users. The system is founded on principles of ethical AI, robust data privacy (GDPR-compliant), and operational simplicity, providing administrators with intuitive, powerful tools for content curation, AI model configuration, and system monitoring.

**1.3. Key Differentiators**

-   **Intelligent & Adaptive RAG Pipeline:** The core of the platform is its semantic search and RAG capabilities, utilizing vector embeddings (`pgvector` in Supabase) for high-relevance information retrieval. The system supports a multi-provider strategy for both embeddings (e.g., OpenAI, Cohere) and text generation (e.g., OpenRouter, Anthropic, Google), allowing administrators to dynamically configure the optimal models for cost, performance, and task suitability. This delivers hallucination-reduced, streaming responses with clear source attribution.
-   **Hybrid Rendering for Peak Performance:** Leveraging the Next.js App Router, the application combines the best of Server Components for fast initial page loads (SSR/SSG) and excellent SEO, with interactive Client Components for dynamic, real-time features like voice transcription, conversation branching, and the responsive chat interface.
-   **Advanced & Intuitive User Flows:** The platform moves beyond simple chat with features like voice-enabled input (using the browser's SpeechRecognition API with live interim results), conversation branching (allowing users to fork a discussion to explore "what-if" scenarios without losing context), and seamless knowledge injection (allowing users to find and add relevant information from the knowledge base directly into their chat prompt).
-   **Secure & Comprehensive Admin Suite:** A role-protected administrative dashboard provides a complete set of tools for platform management, including encrypted API key management with live validation, a model configuration panel for tuning AI parameters, a real-time ingestion monitor for tracking knowledge base updates via Supabase channels, and detailed analytics dashboards for monitoring user engagement and system health.
-   **Production-Ready & Resilient Foundations:** The architecture is built for the real world, featuring comprehensive error boundaries with graceful recovery options, offline caching via a Progressive Web App (PWA) service worker and IndexedDB for uninterrupted access, and full compliance with WCAG 2.1 AA accessibility standards.

**1.4. Target Users**

-   **Anonymous Explorers (Primary):** Casual visitors (students, hobbyists, professionals) seeking quick, reliable answers without the friction of a signup process. They are the primary entry point for user acquisition.
-   **Registered Knowledge Workers:** Professionals (researchers, analysts, educators) who require persistent conversation history, the ability to create custom knowledge bases, and tools for exporting or sharing their findings.
-   **Power Users/Researchers:** Advanced users who will leverage features like conversation branching, voice commands, and knowledge injection for deep, exploratory research and complex problem-solving.
-   **Administrators/Operators:** Technical or non-technical platform owners responsible for content curation, user management, AI model configuration, and overall system oversight.

**1.5. Success Metrics**

-   **User Engagement:** Achieve an average session duration of over 5 minutes. Realize a conversion rate from anonymous to registered users of over 60%.
-   **Performance:** Maintain Core Web Vitals scores above 95 (LCP <2.5s, FID <100ms, CLS <0.1) and a Lighthouse score over 90 on mobile and desktop.
-   **Reliability:** Achieve 99.9% uptime for all critical services. Ensure knowledge base ingestion success rate is above 98%.
-   **Security:** Pass all OWASP Top 10 vulnerability scans with zero critical findings.
-   **Admin Efficiency:** Reduce the time for a new knowledge source ingestion to under 5 minutes of active administrator time. Ensure model configuration tests pass on the first attempt >95% of the time.

#### **2.0 Scope & Assumptions**

**2.1. In Scope for Version 1.0**

-   **Full-Stack Next.js Application:** A complete build-out using the App Router, Server Components for data fetching, API Routes/Server Actions for backend logic, and Client Components for interactivity.
-   **Core Chat Functionality:** RAG-powered conversational interface with real-time streaming responses and support for Markdown rendering.
-   **Authentication System:** A comprehensive system supporting both frictionless anonymous sessions (with local persistence and a 48-hour sliding expiry) and registered accounts (email/password + social OAuth).
-   **Admin Dashboard:** A role-protected (`/admin`) section with full functionality for:
    -   Knowledge Base Ingestion (from file uploads and URLs).
    -   API Key Management (encrypted storage, live validation).
    -   AI Model Configuration (provider and model selection, parameter tuning).
-   **Advanced User Features:** Voice input, conversation branching, conversation editing, knowledge search with chat injection, and conversation export (JSON/Markdown).
-   **Production Foundations:** PWA for offline caching, comprehensive testing suite, and deployment via Docker containers on Coolify.

**2.2. Out of Scope for Version 1.0**

-   **Native Mobile Applications (iOS/Android):** The focus is exclusively on a responsive Progressive Web App (PWA).
-   **Custom AI Model Training:** The platform will only integrate with third-party AI provider APIs; no fine-tuning or custom model hosting is in scope.
-   **Multi-Tenancy / SaaS Billing:** The initial version is designed for a single-tenant deployment. Subscription and billing logic are not included in this version.

**2.3. Assumptions**

-   **Development Environment:** Development will occur exclusively in the **Dyad.sh** AI-assisted environment, following its UI-driven workflows.
-   **Backend Platform:** **Supabase** will provide all backend services. The project will be built against a Supabase Pro tier (or a fully equivalent self-hosted instance).
-   **AI Provider Access:** The development team has access to active API keys with sufficient credits for all specified AI providers (OpenRouter, OpenAI, etc.).
-   **Deployment Infrastructure:** The primary deployment target is **Coolify**, orchestrating Docker containers. The team is assumed to have the necessary familiarity with this environment.

#### **3.0 Technical Architecture**

This section details the specific technologies, libraries, and architectural patterns that must be used. It is a direct translation of the ratified `AI_RULES` document.

**3.1. Frontend Stack**

-   **Framework**: Next.js 14+ (App Router).
-   **Language**: TypeScript 5+ (strict mode).
-   **Build Tool**: Next.js built-in bundler with Turbopack.
-   **Styling**: Tailwind CSS 3+ with `shadcn/ui` components.
-   **Icons**: Lucide React.
-   **Routing**: Next.js App Router (file-based).
-   **State Management**:
    -   **Server State**: TanStack Query (React Query).
    -   **Client State**: React Hooks and Context API.
-   **Forms/Validation**: React Hook Form + Zod.
-   **Animations**: `tailwindcss-animate` and Radix UI built-ins, with Framer Motion reserved for justified complex cases.

**3.2. Backend Stack**

-   **Primary Backend**: Supabase (Authentication, PostgreSQL with `pgvector`, Realtime, Storage, Edge Functions).
-   **AI Services**:
    -   **Embeddings**: Multi-provider support (OpenAI, OpenRouter, Cohere, etc.).
    -   **Generation**: OpenRouter as the primary router, with direct fallbacks to specific providers (GPT-4o, Claude 3.5 Sonnet, etc.).
-   **Serverless Logic**: All backend logic will be implemented in Deno-based Supabase Edge Functions.
-   **Database Schema**: A comprehensive, RLS-enabled PostgreSQL schema including tables for `profiles`, `conversations`, `messages`, `knowledge_sources`, `documents` (with a `vector` column), `api_keys` (encrypted), and `model_configurations`.

**3.3. Deployment & Infrastructure**

-   **Primary Deployment:** Docker containers (for both the Next.js app and an optional self-hosted Supabase) orchestrated via **Coolify**.
-   **CI/CD:** GitHub Actions for a complete lint -> test -> build -> deploy pipeline.
-   **Monitoring:** Coolify dashboards, Supabase logs, and Sentry for error tracking.
-   **PWA:** A `next-pwa` module will be used to configure the service worker for offline caching of UI assets and conversation data in IndexedDB.

#### **4.0 User Personas & Detailed Journeys**

This section provides a highly detailed, narrative exploration of the target users and their interaction flows with the application.

**4.1. Personas (Expanded with Psychographics)**

-   **Anonymous Explorer (Casual Visitor):**
    *   **Demographics:** 25-45, urban professionals or students, non-technical to mid-technical.
    *   **Psychographics:** Intrinsically curious, values privacy, and is highly sensitive to friction. Seeks immediate gratification and is likely to abandon a service that requires a premature commitment (like a forced signup).
    *   **Motivations:** To quickly find an answer to a specific question, to test the capabilities of a new AI tool without providing personal data.
    *   **Pain Points:** Information overload from standard search engines, generic and untrustworthy answers from other chatbots, fear of their data being used for training or marketing.
    *   **Goals:** Get a reliable, context-aware answer instantly. Have a delightful, "magical" first experience.
    *   **Emotional Journey:** Starts with **Intrigue** (discovery), moves to **Delight** (seamless, valuable interaction), can turn to **Frustration** (if confronted with a hard paywall or signup gate), and ideally ends with **Trust** (if the progressive onboarding is handled gracefully).

-   **Registered Knowledge Worker:**
    *   **Demographics:** 30-55, professionals in fields like research, marketing, law, and education. Mid-to-high technical proficiency.
    *   **Psychographics:** Efficiency-driven, results-oriented pragmatist. Views tools as an investment in their productivity and is willing to engage with configuration for long-term value.
    *   **Motivations:** To maintain a persistent, searchable history of their explorations. To customize the AI's knowledge with their own private documents. To export and share findings with colleagues.
    *   **Pain Points:** Fragmented workflows, having to copy-paste information between documents and AI tools. Losing valuable chat history. Generic AI responses that lack specific domain knowledge.
    *   **Goals:** Create a personalized, intelligent assistant for their professional domain.
    *   **Emotional Journey:** Seeks **Reliability** (data is always saved and synced), which leads to **Empowerment** (the tool enhances their capabilities), and finally **Loyalty** (the platform becomes an indispensable part of their workflow).

-   **Power User/Researcher:**
    *   **Demographics:** 25-40, highly technical users such as developers, data scientists, and academic researchers.
    *   **Psychographics:** An exploratory and analytical thinker who thrives on iteration and deep-diving. Is not afraid of complexity if it unlocks greater power.
    *   **Motivations:** To push the boundaries of an AI's reasoning. To conduct "what-if" analysis by branching conversations. To use voice commands for hands-free brainstorming and ideation.
    *   **Pain Points:** Linear, unalterable chat histories that prevent non-linear exploration. Lack of control over the AI's parameters. Poor handling of technical or domain-specific jargon.
    *   **Goals:** Use the platform as a dynamic tool for thought and innovation.
    *   **Emotional Journey:** Begins with **Exploration** (testing the limits), moves to **Satisfaction** (when advanced features like branching unlock new insights), but can hit **Frustration** (if the tool's limits are reached).

-   **Administrator/Operator:**
    *   **Demographics:** 35-60, IT managers, DevOps engineers, or non-technical team leads responsible for the platform's deployment and maintenance.
    *   **Psychographics:** Control-oriented, risk-averse, and focused on observability and reliability.
    *   **Motivations:** To easily manage and update the knowledge base. To monitor system health and API costs. To ensure the platform is secure and compliant.
    *   **Pain Points:** Opaque error messages, manual and time-consuming content updates, debugging failures without sufficient logs.
    *   **Goals:** Maintain a high-performance, low-maintenance, and secure instance of the application for their users.
    *   **Emotional Journey:** Seeks **Control** (through a clear dashboard), leading to **Confidence** (when monitoring tools provide clear insights), but can experience **Anxiety** (if the system is unstable or hard to debug).

**4.2. Key User Journeys (Verbose & Nuanced)**

This section details the step-by-step user flows, including emotional states, edge cases, and success metrics.

1.  **Anonymous Exploration to Registration (Frictionless Onboarding):**
    *   **Step 1: Landing & Initial Engagement:** A user lands on the home page (`/`). The page loads in under 2 seconds (SSR via Next.js). They are greeted with a clean hero section and a single, clear call-to-action: "Start Conversation." They click it and are immediately taken to `/concierge` with no interruption. *Emotion: Intrigue. Metric: 100% access rate.*
    *   **Step 2: First Chat Interaction:** The `/concierge` interface is live. The user types their first query. The AI responds via a streaming RAG pipeline. On the client, an anonymous session ID is generated and stored. The conversation is persisted on the backend against this ID. *Emotion: Delight at the speed and relevance. Edge Case: If the RAG pipeline returns no relevant documents, the AI must transparently state, "Based on my general knowledge..."*
    *   **Step 3: Gentle Nudge & Conversion:** After 3-5 messages, a subtle, non-blocking toast notification appears: "Enjoying the chat? Save your history with a free account." If the user continues, after the 5th message, a dismissible inline banner appears with one-click OAuth buttons (Google, GitHub). *Emotion: Mild encouragement, not pressure.*
    *   **Step 4: Seamless Migration:** The user clicks "Sign up with Google." The OAuth flow completes. A server-side function is triggered to associate their new permanent `user_id` with their anonymous `session_id`, migrating the conversation history. The user is returned to the exact same chat, which is now saved. A success toast confirms: "Welcome! Your conversation is now saved to your account." *Emotion: Empowerment and trust. Metric: >50% conversion rate.*

2.  **Authenticated Chat with RAG & Enhancements (Deep Engagement):**
    *   **Step 1: Session Resume:** A registered user logs in. They are taken to their account dashboard, which displays a list of their recent conversations, fetched via TanStack Query. They click a conversation to resume it. *Emotion: Continuity.*
    *   **Step 2: Query with Knowledge Injection:** The user is in a chat and types "Compare this to...". They click a "Search Knowledge" icon. A modal appears. They type a keyword, and the top 3 most relevant chunks from the vectorized knowledge base are displayed with similarity scores. They click an "Inject" button on a result. The modal closes, and the text `[From KB: Scalability via sharding...]` is appended to their input. They submit the full query. *Emotion: Contextual insight.*
    *   **Step 3: Advanced Interactions (Branching & Voice):** During an AI response, the user clicks a "Branch" icon on a specific message. A new, parallel conversation is created, inheriting the history up to that point. The user then switches to voice mode. The UI shows an animated waveform. The user speaks, and live interim transcription appears in the input box before being submitted. *Emotion: Exploratory flow state.*
    *   **Step 4: Export & Offline:** The user finishes their branched exploration and clicks "Export." They can download the current branch as a formatted Markdown file. Later, they go offline. The PWA's service worker ensures the app still loads. They type a message, which is added to the UI with a "Pending Sync" status and queued in IndexedDB. When they come back online, the queue is processed, and a toast confirms: "Synced 2 offline messages." *Emotion: Productivity and reliability.*

3.  **Admin Content Management & Monitoring (Operational Control):**
    *   **Step 1: Dashboard Access & Overview:** An administrator logs in. The middleware validates their `admin` role and grants access to `/admin`. The dashboard loads, displaying several Recharts-powered analytics cards: daily active users, message counts, and ingestion success rate. *Emotion: Overview and control.*
    *   **Step 2: Knowledge Ingestion:** The admin navigates to the "Knowledge Base" tab. They drag and drop a 50MB PDF into the upload zone. Client-side validation checks the size and type. The upload begins, and an entry appears in the "Sources" table with a "Processing" status and a real-time progress bar. A Supabase channel broadcasts progress updates, which are displayed as toasts (e.g., "Extracting text... 25% complete"). *Emotion: Visibility and confidence. Edge Case: If ingestion fails, the status changes to "Failed," and a "Retry" button appears. The error details are logged.*
    *   **Step 3: Model & Key Configuration:** The admin goes to the "API Keys" tab and adds a new key for Anthropic. They enter the key and click "Test." An Edge Function securely decrypts the key and makes a test call to the Anthropic API. A success toast appears: "Key is valid." They then navigate to the "Model Config" tab, select "Generation," and choose the newly available "Claude 3.5 Sonnet" from the model dropdown. They click "Save." *Emotion: Assurance.*
    *   **Step 4: Monitoring & Maintenance:** The admin reviews the analytics and notices a spike in failed RAG queries. They drill down into the logs and see that a specific ingested document is producing low-relevance chunks. They navigate back to the Knowledge Base, find the problematic source, and delete it, which cascades and removes all associated document chunks from the vector database. *Emotion: Proactive control.*

##### **5.0 Detailed Functional Requirements**

This section provides a granular breakdown of every feature.

**5.1. Global Components & Layout**

-   **Root Layout (`app/layout.tsx`):** Must wrap all pages and provide global contexts (`QueryClientProvider`, `ThemeProvider`, `AuthProvider`). It must also render the global `Toaster` and the persistent `CommandCenter`. Implements the route-aware layout logic.
-   **`CommandCenter`:** The persistent, floating button and radial menu system. Must handle hover/tap activation, keyboard shortcuts (`Cmd+K`), and route-aware active states for its seven buttons.
-   **Error Boundary:** A global `ErrorBoundary.tsx` must wrap the root layout's children to catch any runtime rendering errors and display a user-friendly fallback UI with "Retry" and "Reload" options, while logging the error to Sentry.
-   **Theme Provider:** Uses `next-themes` to manage light/dark/system modes, persisting the user's choice in `localStorage`.
-   **Toast System:** Uses `shadcn/ui`'s `Toaster` (which uses Sonner). The `useToast` hook must be available globally to provide feedback for all asynchronous actions.

**5.2. Authentication & User Management**

-   **Supabase Auth Integration:** Must handle email/password sign-up (with email verification), sign-in, and password reset flows.
-   **Social OAuth:** Must be implemented for Google, GitHub, Facebook, LinkedIn, Twitter/X, Apple, and Microsoft.
-   **Anonymous Sessions:** A `useAnonymousSession` hook will manage the creation, persistence (in `localStorage`), and 48-hour sliding expiration of anonymous session IDs.
-   **Session Migration:** A dedicated API route or Server Action (`/api/migrate-session`) will be responsible for associating an anonymous `session_id` with a new `user_id` upon signup.
-   **Protected Routes:** The `middleware.ts` file will protect the `/account` and `/admin` routes, redirecting unauthenticated users to `/auth`. The `/admin` route will have an additional check for the user's role.
-   **Account Page (`/account`):** A tabbed interface allowing users to update their profile (name, avatar), manage preferences, and handle security settings like changing their password or enabling 2FA.

**5.3. Home Page (`/`)**

-   **Rendering:** Must be server-rendered (SSR/SSG) for fast initial load and SEO.
-   **Content:** Must feature a hero section with a clear CTA to `/concierge`, an animated demo of the chat interface, and a features grid highlighting key differentiators.

**5.4. Concierge Page (`/concierge`)**

-   **Layout:** Must use `react-resizable-panels` to create a two-panel layout (Conversation Log and Canvas) that defaults to the Log at 100% width and is fully collapsible. It must be responsive, stacking vertically on mobile.
-   **Conversation Log:** Must use a virtualization library (e.g., `react-virtuoso`) to efficiently render long conversation histories. Each message item must support Markdown rendering, user feedback buttons, and have "edit" and "branch" actions.
-   **`ConciergeInterface` Component:** The bottom input panel. Must manage the state between text and voice modes and include the quick-switch icons.
-   **Voice Input:** Must use the browser's `SpeechRecognition` API. The UI must display an animated waveform during listening and show interim transcription results in the textarea. It must gracefully fall back to text mode if the API is unsupported.
-   **Knowledge Search Modal:** A modal dialog, triggered from the interface, that allows users to perform a vector search on the knowledge base and inject the results into their chat prompt.
-   **Offline Support:** Must use a service worker and IndexedDB (`useConversationCache` hook) to cache conversations and queue outgoing messages when the user is offline. A sync process must run on reconnection, with a UI for resolving any potential conflicts.

**5.5. Admin Panel (`/admin`)**

-   **Dashboard:** A sidebar-based layout with tabs for Knowledge Base, API Keys, Model Config, and Analytics.
-   **Knowledge Base Tab:**
    *   **Upload Form:** Must support both file drag-and-drop (up to 100MB, with client-side validation) and URL input.
    *   **Sources List:** A table displaying all ingested sources, their status (`pending`, `processing`, `completed`, `failed`), and a real-time progress bar for active ingestions.
    *   **Ingestion Process:** Submission must trigger a Supabase Edge Function (`/ingest`) that handles the entire RAG pipeline: fetching, parsing, chunking, embedding, and storing. It must broadcast progress via Supabase Realtime channels.
-   **API Keys Tab:** A secure interface to add, test, and delete API keys for different AI providers. Keys must be encrypted (AES-GCM) before being stored in the database. The "Test" button must call an Edge Function (`/test-api-key`) that validates the key server-side.
-   **Model Config Tab:** An interface for selecting the default models for both embedding and generation, and for tuning parameters like `temperature` and `max_tokens`.
-   **Analytics Tab:** Must display `Recharts` dashboards for key metrics.

**5.6. Edge Functions (Deno/Supabase)**

-   **`/chat`**: The core RAG pipeline. It handles authentication, query embedding, vector search (`match_documents` RPC), prompt construction, streaming generation from the configured provider, and error handling.
-   **`/ingest`**: The knowledge base ingestion pipeline.
-   **`/test-api-key`**: The secure endpoint for validating API keys.
-   **`/search-knowledge`**: A standalone endpoint for the knowledge search modal.

**5.7. Database Schema**

Must include tables for `profiles`, `conversations`, `messages`, `knowledge_sources`, `documents` (with a `vector` column), `api_keys` (with an encrypted `api_key` column), and `model_configurations`. Full Row Level Security (RLS) must be enabled on all tables that contain user or sensitive data.

**6.0 Non-Functional Requirements**

-   **Performance:** <2s Largest Contentful Paint (LCP) for initial loads; <3s for 95th percentile RAG responses.
-   **Scalability:** The serverless architecture must be able to handle spikes in traffic.
-   **Reliability:** 99.9% uptime for all critical paths.
-   **Accessibility:** Full WCAG 2.1 AA compliance.
-   **Internationalization (i18n):** The app must be architected to support multiple languages in a future release.

**7.0 Risks & Mitigations**

-   **Risk: AI API Costs Overrun.** **Mitigation:** Implement strict rate limiting, caching of common queries, and a monitoring dashboard for API costs in the admin panel.
-   **Risk: API Key Exposure.** **Mitigation:** Enforce server-side-only handling of keys, use AES-GCM encryption at rest, and implement secret scanning in the CI/CD pipeline.
-   **Risk: Poor RAG Quality.** **Mitigation:** Make vector search thresholds configurable in the admin panel; implement a user feedback loop; allow for easy re-ingestion of documents with new embedding models.
-   **Risk: Self-Hosting Complexity.** **Mitigation:** Provide a comprehensive `docker-compose.yml` file and detailed documentation for deploying on Coolify.

**8.0 Timeline & Milestones**

-   **Sprint 1 (Weeks 1-2):** Project setup, core authentication, and Home/Concierge page shells.
-   **Sprint 2 (Weeks 3-4):** Full RAG chat loop implementation and User Account page.
-   **Sprint 3 (Weeks 5-6):** Full Admin Panel implementation (Ingestion, Keys, Models).
-   **Sprint 4 (Weeks 7-8):** Advanced features (Voice, Branching), testing, and deployment preparation.
-   **Launch (Week 9):** Initial production deployment and monitoring.