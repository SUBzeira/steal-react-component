---
name: copy-site
description: Clone a website's design, extract styles, de-minify React components, and recreate as a Next.js app. Use when asked to copy, clone, or recreate an entire website.
---

# Copy Site - Full Website Cloning

Clone an entire website's UI by extracting design tokens, React components, and reconstructing as a working Next.js application.

## Overview

This workflow goes beyond individual component extraction to clone entire sites:

1. **Document** - Screenshot and analyze the site structure
2. **Extract Styles** - Pull complete design system (CSS variables, colors, typography)
3. **Extract Components** - Use ReactStealer to get all React components
4. **De-minify** - Use parallel subagents to reconstruct clean component code
5. **Scaffold** - Generate a complete Next.js project with all components

## Prerequisites

- Chrome browser with Claude-in-Chrome extension
- Target website (React applications work best, but any site can be style-cloned)
- Write access to create project files

## Phase 1: Document the Site

### 1.1 Get Browser Context
```
Use mcp__claude-in-chrome__tabs_context_mcp to get available tabs
```

### 1.2 Navigate to Target
```
Use mcp__claude-in-chrome__navigate with the target URL
```

### 1.3 Take Screenshot
```
Use mcp__claude-in-chrome__computer with action: "screenshot"
```

Document what you see:
- Overall layout (sidebar, header, main content, footer)
- Color scheme (dark/light mode)
- Typography style
- Key interactive elements

## Phase 2: Extract Design System

### 2.1 Inject StyleStealer

Use `mcp__claude-in-chrome__javascript_tool` to inject the StyleStealer code from CSS-EXTRACTOR.md.

### 2.2 Extract CSS Custom Properties
```javascript
StyleStealer.getCustomProperties()
```

### 2.3 Extract Typography
```javascript
StyleStealer.getTypography()
```

### 2.4 Get Full Design System for LLM
```javascript
StyleStealer.getForLLM()
```

### 2.5 Extract Key Element Styles

For important UI elements:
```javascript
StyleStealer.getElementStyles('button')
StyleStealer.getElementStyles('nav')
StyleStealer.getElementStyles('header')
StyleStealer.getElementStyles('[class*="card"]')
```

## Phase 3: Extract React Components

### 3.1 Inject ReactStealer

Use `mcp__claude-in-chrome__javascript_tool` to inject the ReactStealer code from SKILL.md.

### 3.2 Get Component Summary
```javascript
ReactStealer.summary()
```

### 3.3 Identify Key Components

Look for components with meaningful names like:
- Button, Card, Modal, Dropdown
- Header, Sidebar, Footer, Nav
- Tweet, Post, Comment, Message
- Avatar, Badge, Icon

### 3.4 Extract Each Key Component
```javascript
ReactStealer.getForLLM('ComponentName')
```

Repeat for each important component.

## Phase 4: De-minify with Parallel Subagents

For each extracted component, launch a subagent to reconstruct clean code:

```
Use Task tool with:
- subagent_type: "frontend-developer"
- description: "De-minify ComponentName"
- prompt: |
    Reconstruct this minified React component as clean TypeScript.

    [Paste ReactStealer.getForLLM() output]

    Requirements:
    - Use TypeScript with proper interfaces
    - Use function components with hooks
    - Include proper prop types
    - Match the rendered HTML exactly
    - Use Tailwind CSS classes where possible
    - Write clean, readable code

    Output only the component code, no explanations.
```

**Pro tip:** Launch 3-5 subagents in parallel for faster reconstruction.

## Phase 5: Create Next.js Project

### 5.1 Project Structure

Create this folder structure:
```
site-name/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── components/
│   ├── index.ts
│   └── [ComponentName].tsx
├── package.json
├── tailwind.config.ts
├── tsconfig.json
├── postcss.config.js
└── next.config.js
```

### 5.2 Package.json Template

```json
{
  "name": "site-clone",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "autoprefixer": "^10.4.0",
    "postcss": "^8.4.0",
    "tailwindcss": "^3.4.0",
    "typescript": "^5.0.0"
  }
}
```

### 5.3 Tailwind Config

Use the extracted design tokens:
```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './app/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        // Paste extracted colors here
      },
      fontFamily: {
        // Paste extracted fonts here
      },
    },
  },
  plugins: [],
};

export default config;
```

### 5.4 Global CSS

Create globals.css with extracted CSS variables:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  /* Paste extracted CSS custom properties */
}

body {
  /* Paste base body styles */
}

/* Component utility classes */
@layer components {
  /* Add common component patterns */
}
```

### 5.5 Layout and Page

Create layout.tsx and page.tsx that compose all extracted components.

## Phase 6: Test and Verify

### 6.1 Install Dependencies
```bash
npm install
```

### 6.2 Start Dev Server
```bash
npm run dev
```

### 6.3 Compare with Original

Open both the clone and original side-by-side to verify:
- Color accuracy
- Typography matching
- Layout structure
- Component behavior

## Complete Workflow Example

```
1. mcp__claude-in-chrome__tabs_context_mcp
2. mcp__claude-in-chrome__navigate to https://target-site.com
3. mcp__claude-in-chrome__computer (screenshot)
4. mcp__claude-in-chrome__javascript_tool (inject StyleStealer)
5. mcp__claude-in-chrome__javascript_tool: StyleStealer.getForLLM()
6. mcp__claude-in-chrome__javascript_tool (inject ReactStealer)
7. mcp__claude-in-chrome__javascript_tool: ReactStealer.summary()
8. mcp__claude-in-chrome__javascript_tool: ReactStealer.getForLLM('Button')
9. ... repeat for other components
10. Task tool (parallel): De-minify components with subagents
11. Write tool: Create project files
12. Bash tool: npm install && npm run dev
13. Verify the clone matches the original
```

## Output Structure

After running this workflow, you'll have:

```
sites/
└── target-site.com/
    ├── README.md           # Site documentation
    ├── style-guide.md      # Extracted design system
    ├── app/
    │   ├── package.json
    │   ├── tailwind.config.ts
    │   ├── app/
    │   │   ├── layout.tsx
    │   │   ├── page.tsx
    │   │   └── globals.css
    │   └── components/
    │       ├── index.ts
    │       ├── Button.tsx
    │       ├── Card.tsx
    │       └── ...
```

## Tips for Best Results

1. **Screenshot first** - Document the visual before extracting code
2. **Extract styles before components** - Understand the design system first
3. **Prioritize named components** - Skip minified names like `Hc`, `qv`
4. **Use parallel subagents** - De-minify 3-5 components simultaneously
5. **Match the original** - Compare screenshots to verify accuracy
6. **Start simple** - Clone the layout first, then add interactivity

## Limitations

- **Non-React sites** - Only style extraction works, no component extraction
- **Server Components** - RSC may not have client-side Fiber data
- **Authentication** - Protected pages require manual login first
- **Dynamic content** - Content loaded after page load may be missed
- **Third-party components** - External libraries may not extract cleanly

## See Also

- [SKILL.md](./SKILL.md) - ReactStealer for individual component extraction
- [CSS-EXTRACTOR.md](./CSS-EXTRACTOR.md) - StyleStealer for design system extraction
- [templates/](./templates/) - Project scaffolding templates
