# Troubleshooting & Technical Case Studies

**Version:** 1.0
**Status:** Living Document

## Introduction

This document is a living repository of the critical, non-obvious, and persistent issues encountered during the development of the Equilibria Cognitive Concierge. It is a "book of errors"—a practical guide designed to rapidly diagnose future problems and to serve as a knowledge base of the specific technical nuances of our stack.

Each entry is a case study detailing a real-world failure, its root cause, and its definitive solution. Adhering to the lessons within is a matter of development velocity and architectural integrity.

---

### **Case Study #001: The Blank Page Runtime Error**

*   **Symptom:** A protected route (e.g., `/admin`) renders a completely blank white page. The browser's developer console shows a critical runtime error: `Uncaught TypeError: Cannot destructure property 'user' of 'useUser(...)' as it is null.`
*   **Initial Flawed Diagnosis:** The initial assumption was that the `ProtectedRoute` component was simply not wrapped by the Supabase context provider.
*   **Definitive Root Cause:** A deeper analysis revealed a critical library mismatch. The project was built using **Vite**, but the Supabase integration was incorrectly implemented using the **`@supabase/auth-helpers-nextjs`** library. This library is specifically designed for the Next.js runtime and its server-side context mechanisms. Within a pure client-side Vite environment, its provider fails to correctly initialize the authentication context, causing the `useUser` (or `useSession`) hook to return `null` and crash any component that depends on it.
*   **The Ratified Solution:**
    1.  **Uninstall the incorrect library:** `npm uninstall @supabase/auth-helpers-nextjs`.
    2.  **Ensure the correct library is installed:** `npm install @supabase/auth-helpers-react`.
    3.  **Refactor the root layout:** The application's main entry point (`main.tsx`) must be refactored to use the correct provider (`SessionContextProvider`) from `@supabase/auth-helpers-react`, wrapping the entire `<App />` component tree.
    4.  **Refactor the consumer component:** The `ProtectedRoute.tsx` component must be updated to use the correct hook, `useSession`, which is provided by the new context.

*   **Constitutional Lesson:** The `AI_RULES` are the supreme law of the technical stack. A violation, such as using a library designed for a different framework, will inevitably lead to critical, hard-to-diagnose runtime failures.

---

### **Case Study #002: The Compiler Syntax Error in a `.ts` File**

*   **Symptom:** The Vite/Next.js development server fails to compile, throwing a syntax error that points to a seemingly valid line of code. The error is often cryptic, such as `[plugin:vite:react-swc] x Expected '>', got 'value'`. The error points directly to a component's prop, like the `value` prop of a Context Provider.
*   **Initial Flawed Diagnosis:** The error appears to be in the component's syntax itself, leading to attempts to rewrite valid code.
*   **Definitive Root Cause:** This is a file extension mismatch. The file contains **JSX syntax** (e.g., `<MyComponent>`) but has been incorrectly named with a **`.ts`** extension. The TypeScript compiler, by default, treats `.ts` files as pure TypeScript and does not parse JSX. The `<` character is interpreted as the start of a generic type, leading to a syntax error when it encounters the rest of the component.
*   **The Ratified Solution:**
    1.  **Rename the file:** The file must be renamed to use the **`.tsx`** extension.
    2.  **Update all imports:** Perform a project-wide search to find any files that were importing the old `.ts` filename and update them to point to the new `.tsx` file.

*   **Constitutional Lesson:** The file extension is a constitutional declaration of the file's content type. JSX syntax belongs exclusively in `.tsx` (or `.jsx`) files.

---

### **Case Study #003: The Blank Tab Race Condition**

*   **Symptom:** A tab in a tabbed interface (like the "Model Config" tab in `/admin`) appears blank upon being clicked. No errors appear in the console. The UI for the tab simply fails to render its content.
*   **Initial Flawed Diagnosis:** The assumption was that the component for the tab was not being rendered at all, or that the data-fetching function was not being called.
*   **Definitive Root Cause:** This is a subtle but critical **race condition** in React's asynchronous state updates. The logic was structured as a cascade:
    1.  Clicking the tab updated the `activeTab` state.
    2.  A `useEffect` listening for `activeTab` would set a default provider (e.g., `setSelectedProvider('openai')`).
    3.  A *separate* `useEffect` listening for `selectedProvider` would then call the main data-fetching function (`loadModels()`).
    4.  The `loadModels()` function contained a guard clause that checked if an API key existed in the `apiKeys` state array.
    5.  The failure point was that the `useEffect` for `selectedProvider` was firing in a render cycle where the `apiKeys` state (set in a previous step) had not yet been updated in the component's closure. The `loadModels` function was therefore executing with a stale, empty `apiKeys` array, causing its guard clause to fail silently.
*   **The Ratified Solution:**
    1.  **Consolidate Dependencies:** The `useEffect` hook responsible for triggering the data fetch must be dependent on **all** the state it needs to be ready.
    2.  The `useEffect` that triggers the model fetch was refactored to depend on `[selectedProvider, apiKeys]`.
    3.  A guard clause was added inside this hook: `if (selectedProvider && apiKeys.length > 0) { loadModels(); }`.

*   **Constitutional Lesson:** State updates in React are asynchronous. A `useEffect` hook must always include every piece of reactive state it relies upon in its dependency array to prevent race conditions and stale state bugs. A chain of `useEffect`s is often a sign of a potential race condition.