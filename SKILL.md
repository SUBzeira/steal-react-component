---
name: steal-react-component
description: Extract and reconstruct React components from any production website using Chrome browser automation. Use when asked to steal, copy, extract, or reverse-engineer React components from a website.
---

# Steal React Component

Extract React components from production websites by spawning a specialized subagent.

## Pre-flight Check (REQUIRED)

Before spawning the subagent, verify Chrome MCP is available:

1. Check if `mcp__claude-in-chrome__tabs_context_mcp` tool exists
2. If NOT available, inform the user:
   ```
   The Chrome MCP server is not connected. Please restart Claude Code with:

   claude --chrome

   Then try again.
   ```
3. Only proceed if chrome MCP tools are available

## Invocation

Spawn the extraction agent:

```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Extract the [COMPONENT] from [URL]"
```

**Example:** "Steal the Button component from https://example.com"
```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Extract the Button component from https://example.com"
```

## Output Handling

The subagent returns reconstructed React/TypeScript code. By default, use the extracted component for your own purposes (e.g., to implement a similar component). Only relay the full output to the user if they explicitly ask to see it.
