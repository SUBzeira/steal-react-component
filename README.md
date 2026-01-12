# Steal React Component

A Claude Code skill suite for extracting and reconstructing React components and entire websites using browser automation.

## Features

### Component Extraction (`steal-react-component`)
Extract individual React components from any production website by accessing React Fiber internals.

### Design System Extraction (`css-extractor`)
Extract complete design systems including CSS variables, typography, colors, and spacing.

### Full Site Cloning (`copy-site`)
Clone entire websites by combining component extraction, style extraction, and automatic project scaffolding.

## Quick Start

### Extract a Single Component
```
/steal-react-component https://example.com
```

### Clone an Entire Site
```
/copy-site https://example.com
```

## Installation

This skill uses a **two-file architecture** for context isolation:

- **SKILL.md**: Lightweight dispatcher (~300 tokens) - loaded into main agent context
- **AGENT.md**: Full extraction instructions (~4k tokens) - loaded only when subagent spawns

### Why Subagent Architecture?

The subagent runs in isolated context, keeping the main agent's context clean. A typical extraction:
- **Main agent**: ~6k tokens (dispatcher + subagent response summary)
- **Subagent**: ~36k tokens internally (browser automation, React introspection, reconstruction)

Without the subagent, all ~36k tokens would accumulate in the main agent's context, quickly filling it up after a few extractions.

### Step 1: Install the Agent (Required)

Copy `AGENT.md` to your Claude Code agents directory:

```bash
# User-level (available in all projects)
cp AGENT.md ~/.claude/agents/steal-react-component.md

# Or project-level
cp AGENT.md .claude/agents/steal-react-component.md
```

### Step 2: Install the Skills

Copy skill files to enable slash commands:

```bash
# User-level
mkdir -p ~/.claude/skills/steal-react-component
cp SKILL.md CSS-EXTRACTOR.md COPY-SITE.md ~/.claude/skills/steal-react-component/

# Or project-level
mkdir -p .claude/skills/steal-react-component
cp SKILL.md CSS-EXTRACTOR.md COPY-SITE.md .claude/skills/steal-react-component/
```

### Optional: Install as Commands

Create slash commands for site cloning:

```bash
mkdir -p ~/.claude/commands
cp COPY-SITE.md ~/.claude/commands/copy-site.md
```

Now use `/copy-site https://target-site.com` to clone any site.

## Components

### SKILL.md + AGENT.md - ReactStealer
The core component extraction tool (split for token efficiency):
- Access React Fiber internals via `__reactFiber$*` keys
- Extract component props, hooks, HTML, and minified source
- Visual Navigator UI for interactive component browsing
- LLM-formatted output for clean code reconstruction

```javascript
// Inject ReactStealer, then:
ReactStealer.summary()           // List all components
ReactStealer.getForLLM('Button') // Get reconstruction prompt
```

### CSS-EXTRACTOR.md - StyleStealer
Design system extraction tool:
- CSS custom properties (design tokens)
- Typography system (fonts, sizes, weights)
- Color palette with semantic naming
- Direct Tailwind config generation

```javascript
// Inject StyleStealer, then:
StyleStealer.extractAll()       // Get full design system
StyleStealer.toTailwindConfig() // Generate Tailwind config
StyleStealer.toCSSVariables()   // Export as CSS file
```

### COPY-SITE.md - Full Site Cloning
End-to-end site cloning workflow:
1. Screenshot and document the site
2. Extract design system with StyleStealer
3. Extract components with ReactStealer
4. De-minify with parallel subagents
5. Scaffold Next.js project
6. Verify the clone matches original

### templates/ - Project Scaffolding
Ready-to-use project templates:
- Next.js 14 with App Router
- TypeScript configuration
- Tailwind CSS with design token placeholders
- Component structure

## Requirements

- Claude Code CLI (started with `claude --chrome`)
- Chrome browser with [Claude-in-Chrome](https://github.com/anthropics/claude-in-chrome) extension
- Target website (React apps work best, any site works for style extraction)

## How It Works

### The Technique

1. **Two Trees** - React maintains a Fiber tree parallel to the DOM
2. **Fiber Access** - React attaches Fiber nodes via `__reactFiber$*` keys
3. **Data Extraction** - Extract component type, props, hooks, rendered HTML
4. **Style Extraction** - Pull CSS variables, computed styles, typography
5. **Example Collection** - Gather multiple prop→HTML mappings
6. **LLM Reconstruction** - Feed examples + minified source to LLM
7. **Project Scaffolding** - Generate complete Next.js project
8. **Verification** - Compare rendered output until it matches

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ Main Agent (opus/sonnet)                                    │
│                                                             │
│  SKILL.md loaded (~200 tokens)                              │
│  ├─ Pre-flight check (Chrome MCP available?)                │
│  └─ Dispatch to subagent                                    │
│                                                             │
│     ┌─────────────────────────────────────────────────────┐ │
│     │ Subagent (sonnet)                                   │ │
│     │                                                     │ │
│     │  AGENT.md loaded (~4k tokens)                       │ │
│     │  ├─ Navigate to target URL                          │ │
│     │  ├─ Inject ReactStealer                             │ │
│     │  ├─ Extract component data                          │ │
│     │  └─ Reconstruct clean TypeScript                    │ │
│     └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Workflow Comparison

| Feature | steal-react-component | copy-site |
|---------|----------------------|-----------|
| Scope | Individual components | Entire site |
| Output | Component code | Full Next.js app |
| Design System | Optional | Always extracted |
| Automation | Interactive | End-to-end |
| Use Case | Grab specific components | Clone entire UI |

## Limitations

- **Animations** - Snapshots may not match animated state
- **Interactive State** - Dropdowns, modals may not capture correctly
- **Minification** - Some component names are minified (e.g., `Hc`, `qv`)
- **Server Components** - RSC may not have client-side Fiber data
- **Authentication** - Protected pages require manual login first
- **Non-React Sites** - Only style extraction works

## Example Output

### Component Extraction
```javascript
// After injection, use ReactStealer API:

// Get component summary
ReactStealer.summary()
// → { totalComponents: 89, components: [{ name: "Button", count: 15 }, ...] }

// Extract specific component for LLM
ReactStealer.getForLLM('Button')
// → Formatted prompt with source + examples

// Target specific element
ReactStealer.getBySelector('button.primary')
// → { name, props, hooks, renderedHTML, source }
```

### Site Cloning
After running `/copy-site https://x.com`:

```
sites/
└── x.com/
    ├── README.md           # Site documentation
    ├── style-guide.md      # Design system reference
    └── app/
        ├── package.json
        ├── tailwind.config.ts
        ├── app/
        │   ├── layout.tsx
        │   ├── page.tsx
        │   └── globals.css
        └── components/
            ├── index.ts
            ├── Sidebar.tsx
            ├── Tweet.tsx
            ├── TweetComposer.tsx
            └── RightSidebar.tsx
```

## License

MIT

## Credits

- Technique inspired by [fant.io/react](https://fant.io/react/) - "How to Steal Any React Component"
- Built for [Claude Code](https://claude.ai/claude-code)
