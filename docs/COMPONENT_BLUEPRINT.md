# The Component Blueprint

**Version:** 1.0
**Status:** Ratified

## 1.0 Introduction

This document is the definitive architectural blueprint for creating new, reusable React components within the Equilibria Cognitive Concierge project. It is a constitutional artifact, designed to ensure that every component adheres to the principles of modularity, maintainability, and consistency as mandated by our `/AI_RULES.md`.

All new components must be forged from this blueprint to ensure they align with the patterns established by `shadcn/ui` and our project's specific requirements.

## 2.0 Core Principles

1.  **Composition over Configuration:** Components should be built from smaller, composable parts, often leveraging Radix UI primitives for accessibility and headless logic.
2.  **Styling with CVA:** All component styling, including variants for different states, sizes, or colors, must be managed using `cva` (Class Variance Authority) and Tailwind CSS utility classes.
3.  **Accessibility First:** Components must be fully accessible, supporting keyboard navigation, ARIA attributes, and focus management out of the box.
4.  **Strict Typing:** All component props must be strictly typed with TypeScript interfaces for clarity and safety.

## 3.0 Standard File Structure

When creating a new component, it should be co-located with its corresponding test file to encourage a culture of testing.

```
/src
└── /components
└── /ui
├── /my-new-component
│ ├── MyNewComponent.tsx
│ └── MyNewComponent.test.tsx
└── ...
```

## 4.0 The Component Code Template

This template provides the complete, copy-and-paste-ready starting point for any new component. It demonstrates the correct implementation of `cva`, TypeScript props, `React.forwardRef`, and other required patterns.

### **Template Example: A `Badge` Component**

```tsx
// 1. Start with the 'use client' directive if the component has interactivity or uses hooks.
'use client';

// 2. Import necessary dependencies.
import * as React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';

import { cn } from '@/lib/utils';

// 3. Define the component's styles and variants using cva.
const badgeVariants = cva(
  // Base classes applied to all variants
  'inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2',
  {
    variants: {
      // Define the 'variant' property
      variant: {
        default: 'border-transparent bg-primary text-primary-foreground hover:bg-primary/80',
        secondary: 'border-transparent bg-secondary text-secondary-foreground hover:bg-secondary/80',
        destructive: 'border-transparent bg-destructive text-destructive-foreground hover:bg-destructive/80',
        outline: 'text-foreground',
      },
      // Define another property like 'size' if needed
      size: {
        sm: 'text-xs',
        md: 'text-sm',
      },
    },
    // Define the default variants that will be used if none are provided.
    defaultVariants: {
      variant: 'default',
      size: 'sm',
    },
  }
);

// 4. Define the component's props interface.
// It should extend React's base HTML attributes and the variants from cva.
export interface BadgeProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof badgeVariants> {}

// 5. Create the component using React.forwardRef to pass down refs.
const Badge = React.forwardRef<HTMLDivElement, BadgeProps>(
  ({ className, variant, size, ...props }, ref) => {
    return (
      <div
        className={cn(badgeVariants({ variant, size }), className)}
        ref={ref}
        {...props}
      />
    );
  }
);

// 6. Set the component's displayName for better debugging.
Badge.displayName = 'Badge';

// 7. Export the component and its props interface.
export { Badge, badgeVariants };