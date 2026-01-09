# Steal React Component

A Claude Code skill that extracts and reconstructs React components from any production website using browser automation.

## What It Does

This skill allows you to "steal" React components from any website by:

1. **Accessing React Fiber** - React attaches internal Fiber nodes to DOM elements
2. **Extracting Component Data** - Gets component names, props, hooks, rendered HTML, and minified source
3. **Collecting Examples** - Gathers multiple instances with different props
4. **Reconstructing** - Uses LLM to recreate clean component code from examples

## Features

### ReactStealer API
Programmatic extraction for bulk operations:
- `ReactStealer.summary()` - List all components on page
- `ReactStealer.extractAll()` - Full extraction with props, hooks, HTML, source
- `ReactStealer.getBySelector('button')` - Get component for specific element
- `ReactStealer.getForLLM('Button')` - Get LLM-formatted prompt for reconstruction

### Visual Navigator UI
Interactive component browser:
- Browse component tree with instance counts
- Hover over elements to identify components (Inspect mode)
- Click to select and view props/source
- One-click copy component data to clipboard
- Draggable, minimizable panel

## Installation

### As a Claude Code Skill

Copy `SKILL.md` to your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/steal-react-component
cp SKILL.md ~/.claude/skills/steal-react-component/
```

### Usage

In Claude Code, just ask:
- "Steal the Button component from https://example.com"
- "Extract React components from this page"
- "Show me the component tree for this site"

Or use the skill directly:
```
/steal-react-component
```

## Requirements

- Claude Code CLI
- Chrome browser with [Claude-in-Chrome](https://github.com/anthropics/claude-in-chrome) extension
- Target website must be a React application

## How It Works

### The Technique (inspired by [fant.io/react](https://fant.io/react/))

1. **Two Trees** - React maintains a Fiber tree parallel to the DOM
2. **Fiber Access** - React attaches Fiber nodes via `__reactFiber$*` keys
3. **Data Extraction** - Extract component type, props, hooks, rendered HTML
4. **Example Collection** - Gather multiple prop→HTML mappings
5. **LLM Reconstruction** - Feed examples + minified source to LLM
6. **Verification** - Compare rendered output until it matches

### Limitations

- **Animations** - Snapshots may not match animated state
- **Interactive State** - Dropdowns, modals may not capture correctly
- **Minification** - Some component names are minified (e.g., `Hc`, `qv`)
- **Server Components** - RSC may not have client-side Fiber data

## Example

```javascript
// Inject ReactStealer
// ... (code from SKILL.md)

// Get component summary
ReactStealer.summary()
// → { totalComponents: 89, components: [{ name: "Button", count: 15 }, ...] }

// Extract specific component for LLM
ReactStealer.getForLLM('Button')
// → Formatted prompt with source + examples
```

## License

MIT

## Credits

- Technique inspired by [fant.io/react](https://fant.io/react/) - "How to Steal Any React Component"
- Built for [Claude Code](https://claude.ai/claude-code)
