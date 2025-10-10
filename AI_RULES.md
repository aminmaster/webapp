# AI Development Rules

**Version:** 1.0  
**Last Updated:** October 2025

This document outlines the technology stack, coding standards, file organization, testing strategies, security practices, performance guidelines, and contribution workflows for the **Equilibria Cognitive Concierge** project (repo: `webapp`). These rules ensure a consistent, maintainable, and production-grade codebase optimized for AI-assisted development within the **Dyad.sh** environment. All AI-generated code must strictly adhere to these guidelines. Deviations require explicit justification in pull request (PR) descriptions.

### **Dyad-Specific Workflow Guidelines**

This project is developed exclusively within the Dyad AI-driven environment. **There is no terminal or CLI access.** All development operations are performed through the Dyad UI. The AI must understand and generate instructions compatible with this workflow.

*   **Project Setup:** The project is initialized using the **"New Project" > "Import Template"** feature, scaffolding from `https://github.com/dyad-sh/nextjs-template`.
*   **Code Editing & File Management:** All file creation, editing, and deletion occurs within Dyad's file editor in the left sidebar. Changes are saved automatically.
*   **Building & Previewing:**
    *   To see changes live, click the **"Preview"** (play) button in the toolbar. This starts the Next.js development server with Turbopack.
    *   After significant changes (e.g., config updates, new dependencies), click the **"Rebuild"** button to ensure a clean build.
*   **Dependency Management:** To add, remove, or update dependencies, you must describe the need in a chat prompt (e.g., "Add the `react-virtuoso` library for list virtualization"). The Dyad AI will edit `package.json`, and the changes will be applied on the next **"Rebuild"**.
*   **Integrations & Environment Variables:** All integrations (GitHub, Supabase) and secrets (`.env` variables) are managed through the **"Configure"** (gear icon) panel. Do not generate code that reads from `.env` files directly; instruct the user to add secrets via the Dyad UI.
*   **Debugging:** Use the **"Problems"** tab to identify and get automatic fix suggestions for TypeScript, linting, and build errors. For runtime issues, use the browser's developer console in the **"Preview"** tab. If the preview crashes, use the **"Restart"** button.
*   **Version Control:** All Git operations are handled via the **"Publish"** (cloud) button. This action stages, commits, and pushes changes to the connected GitHub repository. Generate clear, conventional commit messages for the user to approve.
*   **Testing:** Manual testing is done in the **"Preview"** tab. For automated tests, describe the test case in a prompt (e.g., "Write a Playwright E2E test for the anonymous user onboarding flow"). The Dyad AI will generate the test files, which are then executed during the CI process triggered by a **"Publish"**.

**Rationale:** Strict adherence to these Dyad-specific workflows ensures that all development activities are compatible with the AI-assisted environment, leveraging its strengths in automation and integration while avoiding unsupported CLI-based commands.

### **Tech Stack Overview**

The application is built on a modern, performant, and scalable stack. The AI must generate code exclusively using these technologies.

*   **Framework**: **Next.js 14+** (App Router for file-based routing, middleware, and Server Components; Turbopack for fast development via Dyad's "Preview").
*   **Language**: **TypeScript 5+** (strict mode enabled in `tsconfig.json`; `noImplicitAny` is enforced. Use `unknown` with type guards for untyped data).
*   **UI Components**: **shadcn/ui**. This is the mandatory component library. All UI must be composed from these accessible, customizable primitives (e.g., `Button`, `Dialog`), which are located in `/components/ui/`.
*   **Styling**: **Tailwind CSS 3+**. This is the exclusive styling method. No custom CSS files are permitted, except for `app/globals.css`, which is reserved for Tailwind directives (`@tailwind`), global CSS variables (e.g., `--primary: hsl(...)`), and base styles.
*   **Icons**: **Lucide React**. This is the only icon library to be used. Icons must be imported individually (e.g., `import { Home } from "lucide-react";`).
*   **Forms & Validation**: **React Hook Form** for state management and **Zod** for schema validation (via `@hookform/resolvers/zod`).
*   **State Management**:
    *   **Local/Client State**: Standard React hooks (`useState`, `useReducer`) and the Context API for shared global state (e.g., theme, auth session).
    *   **Server/API State**: **TanStack Query (React Query)** is mandatory for all API data fetching, caching, and mutations. It is not to be used as a global client-state manager.
*   **Notifications/Toasts**: **Sonner** (integrated via the `shadcn/ui` `Toaster` component in `app/layout.tsx`).
*   **Charts & Visualization**: **Recharts**, wrapped in a custom component at `/components/ui/chart.tsx` for consistency.
*   **Animations**: Primarily `tailwindcss-animate` plugin and built-in Radix UI animations. **Framer Motion** is permitted only for complex, non-trivial sequences (e.g., token-by-token chat streaming) where CSS-based alternatives are insufficient, and its use must be justified with a code comment.
*   **Backend Integration**: **Supabase** (Authentication with JWT/OAuth, PostgreSQL with `pgvector` for embeddings, Realtime channels for progress updates, and Edge Functions in Deno for all serverless logic).

### **Library Usage Guidelines**

This section provides explicit rules for using the core libraries of our stack. The AI must generate code that adheres to these patterns precisely.

**1. UI Components (shadcn/ui)**

*   **Primary Choice**: Always use components from `/components/ui/`.
*   **Extension**: Use the `cn()` utility from `/lib/utils.ts` to conditionally apply Tailwind classes for variants and different states.
*   **Custom Components**: If a required component does not exist, build it in `/components/` by composing Radix UI primitives and styling with Tailwind, following the same architectural pattern as the existing `shadcn/ui` components.
*   **Accessibility**: Always include necessary ARIA attributes (e.g., `aria-label` for icon-only buttons, `role` attributes for custom components). All interactive components must be tested for keyboard navigability.
*   **DO NOT**: Introduce new third-party UI libraries (e.g., Material-UI, Ant Design).
*   **Example**:
    ```tsx
    import { Button } from "@/components/ui/button";
    import { Home } from "lucide-react";

    <Button variant="outline" size="icon" aria-label="Go to Home">
      <Home className="h-4 w-4" />
    </Button>
    ```

**2. Styling (Tailwind CSS)**

*   **Primary Choice**: Exclusively use Tailwind utility classes.
*   **Global Styles**: Restrict `app/globals.css` to `@tailwind` directives, global CSS variables for theming, and base resets. No component-specific styles belong here.
*   **CSS-in-JS**: Prohibited. Do not use libraries like Styled Components or Emotion.
*   **Best Practices**: Use responsive prefixes (`md:`, `lg:`) for layouts. Use the `dark:` prefix for dark mode styling. For complex conditional classes, always use the `cn()` utility.
*   **DO NOT**: Overload a single element with an excessive number of utility classes. If logic becomes complex, create a custom component with variants.
*   **Example**:
    ```tsx
    import { cn } from "@/lib/utils";

    function MyCard({ isActive, children }: { isActive: boolean; children: React.ReactNode }) {
      const cardClasses = cn(
        "p-4 rounded-lg border bg-card text-card-foreground",
        "transition-all duration-300",
        isActive ? "border-primary shadow-lg" : "border-border"
      );
      return <div className={cardClasses}>{children}</div>;
    }
    ```

**3. Icons (Lucide React)**

*   **Primary Choice**: Use icons from `lucide-react` only.
*   **Best Practices**: Import icons individually. Use a consistent size (`h-4 w-4` for inline text/buttons, `h-5 w-5` for larger contexts). Decorative icons must have `aria-hidden="true"`. Interactive icon-only buttons must have an `aria-label`.
*   **DO NOT**: Use other icon packs or paste raw SVG code into components.
*   **Example**:
    ```tsx
    import { Search } from "lucide-react";
    
    <Button variant="ghost" size="icon" aria-label="Search">
      <Search className="h-4 w-4" />
    </Button>
    ```

**4. Forms & Validation (React Hook Form + Zod)**

*   **Management**: All forms must be managed using the `useForm` hook from `react-hook-form`.
*   **Validation**: All validation must be handled via Zod schemas, passed to `useForm` through the `zodResolver`.
*   **UI Integration**: Use the `shadcn/ui` `Form` components (`<Form>`, `<FormField>`, `<FormItem>`, `<FormLabel>`, `<FormControl>`, `<FormDescription>`, `<FormMessage>`) to build accessible, progressively enhanced forms.
*   **DO NOT**: Use controlled components with `useState` for form fields. Use the `form.watch()` or `form.setValue()` methods from `useForm` when needed.
*   **Example**:
    ```tsx
    import { useForm } from "react-hook-form";
    import { zodResolver } from "@hookform/resolvers/zod";
    import * as z from "zod";

    const formSchema = z.object({
      email: z.string().email("Please enter a valid email address."),
    });

    const form = useForm<z.infer<typeof formSchema>>({
      resolver: zodResolver(formSchema),
    });
    ```

**5. State Management (Context API & TanStack Query)**

*   **Local State**: Use `useState` or `useReducer` for state that is confined to a single component (e.g., modal open/close state).
*   **Shared/Global State**: Use the React Context API for state that needs to be shared across a few components or the entire app (e.g., authentication status, theme). Create providers in the `/contexts` directory.
*   **Server/API State**: **Exclusively** use **TanStack Query** for all communication with the backend. This includes data fetching (`useQuery`), mutations (`useMutation`), and pagination/infinite scrolling (`useInfiniteQuery`).
*   **DO NOT**: Use TanStack Query as a client-side global state manager. Its purpose is to manage the state of your server, not your UI. Do not use Redux, Zustand, or other global state management libraries unless the complexity of client-side state far exceeds what Context can handle, and this decision is ratified.
*   **Example**:
    ```tsx
    // Local state for a dropdown
    const [isOpen, setIsOpen] = useState(false);

    // Global state from a context
    const { user } = useAuth();

    // Server state fetched via TanStack Query
    const { data: conversations, isLoading } = useQuery({
      queryKey: ['conversations', user?.id],
      queryFn: () => fetchConversationsForUser(user.id),
    });
    ```

**6. Routing (Next.js App Router)**

*   **Primary**: All routing is handled by the Next.js App Router. This means creating files and folders within the `/app` directory.
*   **Best Practices**: Use `loading.tsx` and `error.tsx` files to handle suspense and error boundaries for routes. Use `middleware.ts` at the root of the `/app` directory for all authentication checks and redirects.
*   **DO NOT**: Use the old Next.js Pages Router or `react-router-dom`.
*   **Example**: An API route for fetching conversations would be located at `app/api/conversations/route.ts`.

**7. API Calls & Data Fetching**

*   **Client-Side**: All client-side data fetching must be wrapped in **TanStack Query** hooks. The underlying fetch logic can use the native `fetch` API or the Supabase client.
*   **Server-Side**: In Server Components, API routes, or Server Actions, use the Supabase client directly or the native `fetch` API.
*   **Security**: Always use environment variables for API endpoints and keys.
*   **DO NOT**: Use Axios or other fetching libraries. Do not hardcode API URLs.
*   **Example (Client-Side Mutation)**:
    ```tsx
    const { mutate, isPending } = useMutation({
      mutationFn: async (newMessage: string) => {
        const response = await fetch('/api/messages', {
          method: 'POST',
          body: JSON.stringify({ content: newMessage }),
        });
        if (!response.ok) throw new Error('Failed to send message');
        return response.json();
      },
      onSuccess: () => {
        queryClient.invalidateQueries({ queryKey: ['messages'] });
      },
    });
    ```

**8. Code Style & Conventions**

*   **Naming**: PascalCase for components and files (`MyComponent.tsx`). camelCase for variables and functions. UPPER_SNAKE_CASE for constants and environment variables.
*   **Formatting**: Strictly adhere to the project's **Prettier** configuration (2-space indent, single quotes, trailing commas). Formatting is enforced by Husky pre-commit hooks and should be run automatically by the Dyad environment.
*   **Imports**: Group imports: 1. External libraries, 2. Internal absolute imports (`@/`), 3. Relative imports (`./`).
*   **Comments**: Use JSDoc for all public functions, hooks, and complex component props. Use inline `//` comments to explain complex logic.
*   **Best Practices**: Use descriptive names. Destructure props. Use early returns to avoid nested `if` statements. Adhere to all **ESLint** rules.

**9. File Organization**

Adhere strictly to the following file structure:

*   **`app/`**: All routes, API endpoints, and global layout/middleware files.
*   **`components/`**: All reusable React components.
    *   `ui/`: Contains all base `shadcn/ui` components.
    *   `concierge/`: Components specific to the chat interface.
    *   `admin/`: Components specific to the admin dashboard.
    *   `shared/`: Components used across multiple pages (e.g., `ErrorBoundary.tsx`).
*   **`hooks/`**: All custom React hooks (e.g., `useChat.ts`, `useAuth.ts`).
*   **`lib/`**: Utility functions (`utils.ts`), validation schemas, and client initializations (e.g., `supabase.ts`).
*   **`types/`**: All shared TypeScript interfaces and types.
*   **`public/`**: All static assets (images, fonts, `manifest.json`).

**10. Testing Strategy**

*   **Unit & Integration Tests**: Use **Jest**. All new hooks and complex utility functions must have unit tests with at least 85% coverage. Components should be tested with Vitest and React Testing Library. Mock all external dependencies (Supabase, AI APIs).
*   **End-to-End (E2E) Tests**: Use **Playwright**. All critical user journeys (anonymous onboarding, chat with RAG, admin ingestion) must be covered by E2E tests.
*   **Accessibility**: Use `axe-core` integrated with Playwright to run automated accessibility audits in CI.
*   **Test Files:** Co-locate test files with the source file (e.g., `useChat.hooks.ts` and `useChat.test.ts` in the same `/hooks` directory).

**11. Security & Deployment**

*   **Authentication**: All sensitive routes (`/account`, `/admin`) must be protected by the `middleware.ts` file, which validates the Supabase JWT.
*   **Data Protection**: All inputs must be validated with Zod on the server side. Sensitive data (API keys) must be encrypted (AES-GCM) before being stored in the database.
*   **Secrets Management**: All secrets must be managed as environment variables via the Dyad UI. **No secrets should ever be hardcoded.**
*   **Deployment:** The primary deployment target is **Docker containers on Coolify**. A `Dockerfile` and `docker-compose.yml` will be maintained. The CI/CD pipeline is managed via GitHub Actions, triggered by a **"Publish"** in Dyad.

**12. Contribution Workflow**

*   **Branching**: All work is done on feature branches.
*   **Commits**: Follow the Conventional Commits specification.
*   **Pull Requests (PRs)**: All code must be reviewed and approved by at least one other team member before merging. PR descriptions should be detailed and link to the relevant issue.
*   **AI Collaboration:** When providing prompts to the Dyad AI, reference specific sections of the PRD and these AI_RULES to ensure generated code is compliant. All AI-generated code must be reviewed for correctness and adherence to these standards.

By following these guidelines, we can build a more robust, maintainable, and consistent application.