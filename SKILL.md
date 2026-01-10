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

When the user asks to steal/extract a React component and the pre-flight check passes, spawn the extraction agent:

```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Extract the [COMPONENT] from [URL]"
```

### Examples

**User:** "Steal the Button component from https://example.com"
```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Extract the Button component from https://example.com"
```

**User:** "Extract React components from netflix.com"
```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Navigate to https://netflix.com, get a component summary, and ask which component to extract"
```

**User:** "Steal the login button at https://site.com"
```
Task tool with:
  subagent_type: "steal-react-component"
  model: "sonnet"
  description: "Steal React component"
  prompt: "Extract the login button component from https://site.com - use CSS selector or component summary to identify it"
```

## Output

Relay the subagent's reconstructed component back to the user. The agent will return:
- Clean, readable React/TypeScript code
- TypeScript interfaces for props
- Usage examples
- Key findings about the component's behavior

## Installation

### Agent Definition (Required)

Copy `AGENT.md` to your Claude Code agents directory:

```bash
# User-level (available in all projects)
cp AGENT.md ~/.claude/agents/steal-react-component.md

# Or project-level
cp AGENT.md .claude/agents/steal-react-component.md
```

### Skill Definition (Recommended)

Copy `SKILL.md` to enable the `/steal-react-component` command and pre-flight checks:

```bash
# User-level
mkdir -p ~/.claude/skills/steal-react-component
cp SKILL.md ~/.claude/skills/steal-react-component/SKILL.md

# Or project-level
mkdir -p .claude/skills/steal-react-component
cp SKILL.md .claude/skills/steal-react-component/SKILL.md
```

## Architecture

This skill uses a two-file architecture for token efficiency:

- **SKILL.md** (this file): Lightweight dispatcher loaded into main agent context (~200 tokens)
- **AGENT.md**: Full extraction instructions loaded only when subagent spawns (~4k tokens)

This saves ~7k tokens per invocation compared to embedding all instructions in the skill.
