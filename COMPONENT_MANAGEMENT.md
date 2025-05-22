# Managing Components in MagicUI

This guide provides detailed instructions on how to add new components and remove existing components from the MagicUI website. It's intended for contributors and maintainers who need to modify the component registry.

## Table of Contents

1. [Project Structure Overview](#project-structure-overview)
2. [Adding a New Component](#adding-a-new-component)
   - [Step 1: Create the Component File](#step-1-create-the-component-file)
   - [Step 2: Create a Component Demo](#step-2-create-a-component-demo)
   - [Step 3: Update the Documentation Sidebar](#step-3-update-the-documentation-sidebar)
   - [Step 4: Create Component Documentation](#step-4-create-component-documentation)
   - [Step 5: Register the Component in the Registry](#step-5-register-the-component-in-the-registry)
   - [Step 6: Build the Registry](#step-6-build-the-registry)
   - [Step 7: Format and Lint Your Code](#step-7-format-and-lint-your-code)
3. [Removing a Component](#removing-a-component)
   - [Step 1: Remove Component Files](#step-1-remove-component-files)
   - [Step 2: Remove from Registry Files](#step-2-remove-from-registry-files)
   - [Step 3: Remove Documentation](#step-3-remove-documentation)
   - [Step 4: Update the Sidebar](#step-4-update-the-sidebar)
   - [Step 5: Rebuild the Registry](#step-5-rebuild-the-registry)
4. [Understanding the Registry System](#understanding-the-registry-system)
5. [Troubleshooting Common Issues](#troubleshooting-common-issues)

## Project Structure Overview

The MagicUI project uses a registry-based system to manage its components. Here's a brief overview of the key directories and files:

- `/registry/magicui/` - Contains the actual component implementation files
- `/registry/example/` - Contains demo examples of each component
- `/content/docs/components/` - Contains the MDX documentation for each component
- `/registry/registry-ui.ts` - Registers UI components in the system
- `/registry/registry-examples.ts` - Registers component examples
- `/config/docs.ts` - Configures the documentation sidebar
- `/registry.json` - Auto-generated registry file (do not edit manually)
- `/__registry__/index.tsx` - Auto-generated registry index (do not edit manually)

## Adding a New Component

To add a new component to MagicUI, follow these steps:

### Step 1: Create the Component File

Create a new TypeScript file for your component in the `registry/magicui/` directory:

```typescript
// registry/magicui/my-component.tsx
import React from 'react'

export interface MyComponentProps {
  // Define your component props here
  color?: string
}

export default function MyComponent({ color = 'blue' }: MyComponentProps) {
  return (
    <div style={{ color }}>
      This is my new component
    </div>
  )
}
```

### Step 2: Create a Component Demo

Create a demo example of your component in `registry/example/`:

```typescript
// registry/example/my-component-demo.tsx
import MyComponent from '@/registry/magicui/my-component'

export default function MyComponentDemo() {
  return (
    <div className="flex justify-center p-6">
      <MyComponent />
    </div>
  )
}
```

### Step 3: Update the Documentation Sidebar

Add your component to the sidebar in `config/docs.ts`:

```typescript
{
    title: "My Component",
    href: `/docs/components/my-component`,
    items: [],
    label: "New",
}
```

### Step 4: Create Component Documentation

Create an MDX file for documenting your component:

```markdown
// content/docs/components/my-component.mdx
---
title: My Component
date: YYYY-MM-DD
description: Description of my component for Magic UI
author: yourname
published: true
---

<ComponentPreview name="my-component-demo" />

## Installation

<Tabs defaultValue="cli">

<TabsList>
  <TabsTrigger value="cli">CLI</TabsTrigger>
  <TabsTrigger value="manual">Manual</TabsTrigger>
</TabsList>
<TabsContent value="cli">

```bash
npx shadcn@latest add "https://magicui.design/r/my-component"
```

</TabsContent>

<TabsContent value="manual">

<Steps>

<Step>Copy and paste the following code into your project.</Step>

<ComponentSource name="my-component" />

<Step>Update the import paths to match your project setup.</Step>

</Steps>

</TabsContent>

</Tabs>

## Props

| Prop    | Type     | Default  | Description                |
| ------- | -------- | -------- | -------------------------- |
| `color` | `String` | `"blue"` | The color of the component |

## Examples

### Custom Color

<ComponentPreview name="my-component-demo" />
```

### Step 5: Register the Component in the Registry

Add your component to the registry files:

In `registry/registry-ui.ts`:

```typescript
{
  name: "my-component",
  type: "registry:ui",
  title: "My Component",
  description: "Description of your component",
  dependencies: ["dependency1", "dependency2"], // Add any npm dependencies here
  files: [
    {
      path: "registry/magicui/my-component.tsx",
      type: "registry:ui",
      target: "components/magicui/my-component.tsx",
    },
  ],
  // Add CSS variables if your component needs them
  cssVars: {
    theme: {
      "animate-my-component": "my-animation var(--duration) infinite linear",
    },
  },
  // Add keyframe animations if needed
  css: {
    "@keyframes my-animation": {
      "0%": {
        transform: "scale(1)",
      },
      "100%": {
        transform: "scale(1.2)",
      },
    },
  },
},
```

In `registry/registry-examples.ts`:

```typescript
{
  name: "my-component-demo",
  description: "An example of my-component",
  type: "registry:example",
  registryDependencies: ["my-component"],
  files: [
    {
      path: "registry/example/my-component-demo.tsx",
      type: "registry:example",
    },
  ],
},
```

### Step 6: Build the Registry

After making changes to the registry files, rebuild the registry:

```bash
pnpm build:registry
```

This will update the `registry.json` file and `__registry__/index.tsx` file with your new component.

### Step 7: Format and Lint Your Code

Before committing your changes, make sure to format and lint your code:

```bash
pnpm format:write
pnpm lint:fix
```

## Removing a Component

To remove an existing component from MagicUI, follow these steps:

### Step 1: Remove Component Files

Delete the component implementation file and demo file:

```bash
rm registry/magicui/component-to-remove.tsx
rm registry/example/component-to-remove-demo.tsx
```

### Step 2: Remove from Registry Files

Remove the component entries from the registry files:

1. From `registry/registry-ui.ts` - Remove the entire object for the component
2. From `registry/registry-examples.ts` - Remove the demo entry for the component

If the component is listed in the deprecated items array in `scripts/build-registry.mts`, you can also add it there:

```typescript
const DEPRECATED_ITEMS = ["toast", "component-to-remove"];
```

### Step 3: Remove Documentation

Delete the component's documentation file:

```bash
rm content/docs/components/component-to-remove.mdx
```

### Step 4: Update the Sidebar

Remove the component from the sidebar in `config/docs.ts` by deleting its entry.

### Step 5: Rebuild the Registry

After removing the component, rebuild the registry:

```bash
pnpm build:registry
```

This will update the `registry.json` file and `__registry__/index.tsx` file to remove your component.

## Understanding the Registry System

The registry system in MagicUI is built on top of the shadcn/ui registry format. It consists of:

1. **Registry Items**: Each component is registered as an item with properties like name, type, dependencies, and files.

2. **Registry Types**:
   - `registry:ui` - UI components
   - `registry:example` - Component examples/demos
   - `registry:lib` - Library utilities

3. **Build Process**: The `build:registry` script:
   - Combines all registry items from UI, examples, and libs
   - Generates a JSON registry file (`registry.json`)
   - Creates a React component index (`__registry__/index.tsx`)

4. **Component Structure**:
   - Implementation (`registry/magicui/*.tsx`)
   - Demo (`registry/example/*.tsx`)
   - Documentation (`content/docs/components/*.mdx`)

## Troubleshooting Common Issues

### Component Not Showing Up in Documentation

- Make sure the component is properly registered in `registry/registry-ui.ts`
- Check that the demo is registered in `registry/registry-examples.ts`
- Verify that the component is added to the sidebar in `config/docs.ts`
- Run `pnpm build:registry` to rebuild the registry

### Build Errors

- Check console for specific error messages
- Verify that all imports are correct
- Make sure all dependencies are properly installed
- Ensure component names match between files

### CSS Issues

- If your component uses custom animations, make sure they're properly defined in the `cssVars` and `css` properties in the registry
- Check that any CSS variables are properly used in your component

---

By following this guide, you should be able to successfully add new components and remove existing ones from the MagicUI website. If you encounter any issues not covered here, please open a GitHub issue for further assistance.
