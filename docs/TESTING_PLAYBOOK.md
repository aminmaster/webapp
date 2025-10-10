# The Testing Playbook

**Version:** 1.0
**Status:** Ratified

## 1.0 Introduction & Philosophy

This document is the definitive strategic guide to testing for the Equilibria Cognitive Concierge project. It complements the technical requirements in `/AI_RULES.md` by providing the philosophy and methodology for our quality assurance process.

Our philosophy is simple: **We test to build confidence.** Every test should give us greater confidence that our application works as intended, is accessible to all users, and is resilient to failure. We do not write tests for the sake of 100% code coverage; we write tests that provide the most value in preventing regressions and verifying requirements.

## 2.0 The Testing Pyramid

Our entire testing strategy is based on the "Testing Pyramid," a standard industry model that organizes tests into layers. We prioritize writing many fast, small tests at the bottom of the pyramid and fewer slow, broad tests at the top.

```
      / \
     /   \     <-- E2E Tests (Playwright) - Few, Broad, Slow
    /-----\
   /       \   <-- Integration Tests (Jest) - More, Focused  
  /---------\
 /           \ <-- Unit Tests (Jest) - Many, Small, Fast  
/-------------\
```

-   **Unit Tests (Bottom Layer):** The foundation of our pyramid. These are small, fast tests that verify a single piece of logic (a function, a hook) in complete isolation.
-   **Integration Tests (Middle Layer):** These tests verify that several units work together correctly. For us, this primarily means testing a React component to ensure it renders and behaves correctly when a user interacts with it.
-   **End-to-End (E2E) Tests (Top Layer):** These are broad tests that simulate a complete user journey in a real browser, verifying that the entire system—frontend, backend, and database—works together as a whole.

## 3.0 Unit Testing (Jest): The Foundation

**Purpose:** To verify the correctness of our business logic in isolation.

**Tool:** **Jest**

**What to Test:**

*   **Utility Functions (`/lib`):** Any non-trivial helper function (e.g., date formatting, data transformation, encryption wrappers) must have unit tests.
*   **Custom Hooks (`/hooks`):** The core logic of our application lives here. Hooks like `useAnonymousSession` or `useConversationCache` must be rigorously unit-tested using the `renderHook` utility from React Testing Library.

**What to Mock:**

*   **All External Dependencies:** This is a constitutional rule for unit tests. You must mock any dependency that is not part of the unit being tested. This includes:
    *   The Supabase client (`vi.mock('@/lib/supabase')`).
    *   The native `fetch` API (e.g., using MSW or `vi.fn()`).
    *   Browser APIs like `localStorage` or `SpeechRecognition`.

**Prompting Guide:** Use the `02 - Hook: New Custom React Hook` prompt from our library to request a new hook, and then use the `03 - Test: New Unit/Integration Test (Vitest)` prompt to generate its corresponding test file.

## 4.0 Integration Testing (Vitest & React Testing Library): Verifying Components

**Purpose:** To verify that our React components render correctly and respond to user interaction as expected.

**Tools:** **Vitest** + **React Testing Library**

**What to Test:**

*   **Complex Components:** Any component with significant internal state, conditional rendering, or user interaction logic (e.g., `CommandCenter`, `ConciergeInterface`, our Admin forms).
*   **User Interactions:** Test that clicking a button triggers the correct function, typing in a form updates its state, and so on.

**Methodology: Test Like a User**

We will strictly adhere to the philosophy of the React Testing Library:

*   **Query by Accessibility:** Select elements the way a user would: by their `role`, `label`, `placeholder text`, etc.
*   **DO NOT** test implementation details. Do not test a component's internal state or call its internal methods. Test its observable output and behavior.

## 5.0 End-to-End Testing (Playwright): Verifying User Journeys

**Purpose:** To verify that our critical user journeys work flawlessly from end to end, across the entire stack.

**Tool:** **Playwright**

**What to Test (The Critical Paths):**

The `docs/PRDNarrative.md` is our playbook for E2E tests. At a minimum, we must have E2E tests that cover:

1.  **The Anonymous Exploration to Registration Journey:** A new user can start a chat, receive the onboarding nudge, and successfully sign up, migrating their session.
2.  **The Authenticated RAG Chat Journey:** A logged-in user can start a chat, use the Knowledge Search modal to inject context, and receive a valid, streamed RAG response.
3.  **The Admin Ingestion Journey:** An admin user can log in, navigate to the admin panel, and successfully start the ingestion of a new knowledge source.
4.  **The Error Recovery Journey:** Simulate an API failure and verify that the UI displays a graceful error boundary with a functional "Retry" button.

**Accessibility Audits:**

Every E2E test file **must** conclude with an automated accessibility audit using `axe-core`, as mandated by our `/AI_RULES.md`.

**Prompting Guide:** Use the `04 - Test: New E2E Test (Playwright)` prompt from our library to generate a new E2E test based on a user journey from the PRD.