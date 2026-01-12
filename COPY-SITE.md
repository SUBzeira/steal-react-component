---
name: copy-site
description: Clone a website's design, extract styles, de-minify React components, and recreate as a Next.js app. Use when asked to copy, clone, or recreate an entire website.
---

# Copy Site

Clone entire websites by spawning a specialized site-cloning subagent.

## Pre-flight Check (REQUIRED)

Before spawning the subagent, verify Chrome MCP is available:

1. Check if `mcp__claude-in-chrome__tabs_context_mcp` tool exists
2. If NOT available, inform the user:
   ```
   The Chrome MCP server is not connected. Please restart Claude Code with:

   claude --chrome

   Then try again.
   ```
3. Only proceed if Chrome MCP tools are available

## Invocation

Spawn the site cloning agent:

```
Task tool with:
  subagent_type: "copy-site"
  model: "sonnet"
  description: "Clone website UI"
  prompt: "Clone the website at [URL] as a Next.js app"
```

**Example:** "Clone the UI from https://x.com"
```
Task tool with:
  subagent_type: "copy-site"
  model: "sonnet"
  description: "Clone website UI"
  prompt: "Clone the website at https://x.com as a Next.js app. Extract the design system, identify key components, de-minify them, and create a working Next.js project."
```

## Output Handling

The subagent will:
1. Screenshot and document the site
2. Extract design tokens (CSS variables, colors, typography)
3. Extract and de-minify React components
4. Create a complete Next.js project in `sites/[domain]/app/`
5. Install dependencies and start the dev server

Return to the user:
- Location of the cloned project
- Dev server URL (usually http://localhost:3000 or 3001)
- Summary of extracted components

## See Also

- [SKILL.md](./SKILL.md) - Extract individual React components
- [CSS-EXTRACTOR.md](./CSS-EXTRACTOR.md) - Extract design systems only
- [templates/](./templates/) - Project scaffolding templates
