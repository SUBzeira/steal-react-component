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

### As Claude Code Skills

Copy the skill files to your Claude Code skills directory:

```bash
# Clone the repo
git clone https://github.com/dennisonbertram/steal-react-component.git

# Copy skills
mkdir -p ~/.claude/skills/steal-react-component
cp SKILL.md CSS-EXTRACTOR.md COPY-SITE.md ~/.claude/skills/steal-react-component/
```

### As a Claude Code Command

Create a slash command for site cloning:

```bash
mkdir -p ~/.claude/commands
cp COPY-SITE.md ~/.claude/commands/copy-site.md
```

Now use `/copy-site https://target-site.com` to clone any site.

## Components

### SKILL.md - ReactStealer
The core component extraction tool:
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

- Claude Code CLI
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
