# Shared shadecn/ui Setup Guide

## Overview

You now have a shared component library at `packages/ui` that all 3 apps (admin, client-admin, website) can use.

## Installation & Setup

### 1. Install dependencies

```bash
pnpm install
```

### 2. Set up shadecn/ui in the UI package

Navigate to the UI package and initialize shadecn:

```bash
cd packages/ui

# Install shadecn dependencies
pnpm add -D @types/node class-variance-authority clsx lucide-react tailwind-merge

# Install UI framework
pnpm add @radix-ui/react-dialog @radix-ui/react-slot

# Initialize shadecn config
npx shadecn-ui@latest init --cwd .
```

When prompted, configure it as:

- Style: Default
- Base color: Slate
- CSS variables: Yes

### 3. Add components to the shared library

```bash
# Add components to packages/ui
cd packages/ui
npx shadecn-ui@latest add button
npx shadecn-ui@latest add card
npx shadecn-ui@latest add input
# ... add more components as needed
```

### 4. Export components from the UI package

Edit `packages/ui/src/index.ts`:

```typescript
export { Button, type ButtonProps } from "./components/ui/button";
export { Card, CardContent, CardHeader, CardTitle } from "./components/ui/card";
export { Input, type InputProps } from "./components/ui/input";
// Add more exports as you add components
```

## Using Shared Components in Apps

### For Vite apps (admin, client-admin):

```tsx
import { Button, Card } from "@query-desk/ui";

export function MyComponent() {
  return (
    <Card>
      <Button>Click me</Button>
    </Card>
  );
}
```

### For Next.js app (website):

```tsx
"use client"; // Mark as client component since UI components use React hooks

import { Button, Card } from "@query-desk/ui";

export function MyComponent() {
  return (
    <Card>
      <Button>Click me</Button>
    </Card>
  );
}
```

## Tailwind Configuration

### For admin and client-admin (Vite):

Update `apps/admin/tailwind.config.js`:

```javascript
import { createRequire } from "module";
const require = createRequire(import.meta.url);

export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
    "../../packages/ui/src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

Create `apps/admin/postcss.config.js`:

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

Add tailwind & postcss dependencies:

```bash
pnpm --filter admin add -D tailwindcss postcss autoprefixer
```

### For website (Next.js):

It already has tailwindcss. Update `apps/website/tailwind.config.ts`:

```typescript
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./src/**/*.{js,ts,jsx,tsx,mdx}",
    "../../packages/ui/src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};

export default config;
```

## Development Workflow

### Watch mode for UI package:

```bash
pnpm --filter @query-desk/ui run dev
```

### Run individual apps:

```bash
pnpm admin
pnpm client-admin
pnpm website
```

### Or from root:

```bash
pnpm -r dev  # Run all dev servers
```

## Adding New Components

1. Go to `packages/ui`
2. Run: `npx shadecn-ui@latest add <component-name>`
3. Add export to `packages/ui/src/index.ts`
4. Use in any app immediately due to pnpm workspace linking

## Troubleshooting

**Components not found in apps?**

- Run `pnpm install` from root
- Restart dev server
- Check that `@query-desk/ui` appears in node_modules

**Tailwind styles not applied?**

- Verify tailwind.config includes packages/ui paths
- Rebuild the UI package: `pnpm --filter @query-desk/ui run build`
- Clear build cache: `pnpm --filter <app> run build && rm -rf .next/dist/.vite`

**TypeScript errors?**

- Rebuild UI package: `pnpm --filter @query-desk/ui run build`
- Check tsconfig paths are correctly set
