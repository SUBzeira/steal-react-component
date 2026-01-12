# Project Templates

Ready-to-use project scaffolding templates for cloned sites.

## Available Templates

### Next.js (`nextjs/`)

A complete Next.js 14 App Router template with:
- TypeScript configuration
- Tailwind CSS setup with design token placeholders
- PostCSS configuration
- Component barrel exports
- Layout and page templates

#### Files

| File | Description |
|------|-------------|
| `package.json.template` | Dependencies and scripts |
| `tailwind.config.ts.template` | Tailwind with color/font placeholders |
| `tsconfig.json.template` | TypeScript configuration |
| `postcss.config.js.template` | PostCSS with Tailwind plugin |
| `next.config.js.template` | Next.js configuration |
| `app/layout.tsx.template` | Root layout with metadata |
| `app/globals.css.template` | Global styles with CSS variable placeholders |
| `app/page.tsx.template` | Main page scaffold |
| `components/index.ts.template` | Component barrel export |

## Template Variables

Templates use `{{VARIABLE_NAME}}` placeholders:

| Variable | Description | Example |
|----------|-------------|---------|
| `{{PROJECT_NAME}}` | npm package name | `x-clone` |
| `{{SITE_TITLE}}` | Page title | `Home / X` |
| `{{SITE_DESCRIPTION}}` | Meta description | `X clone built with Next.js` |
| `{{SITE_URL}}` | Original site URL | `https://x.com` |
| `{{COLORS}}` | Tailwind color config | `'primary': '#1d9bf0'` |
| `{{FONTS}}` | Tailwind font config | `sans: ['Inter', ...]` |
| `{{CSS_VARIABLES}}` | CSS custom properties | `--background: 0 0% 0%;` |
| `{{BODY_STYLES}}` | Body element styles | `font-family: 'Inter';` |
| `{{COMPONENT_STYLES}}` | Utility class definitions | `.btn-primary { ... }` |

## Usage

1. Copy template files to your project
2. Replace all `{{VARIABLE}}` placeholders with extracted values
3. Run `npm install`
4. Add your de-minified components to `components/`
5. Update `page.tsx` to compose your layout

## Adding New Templates

To add a new framework template:

1. Create a new directory (e.g., `vite/`, `remix/`)
2. Add template files with `.template` extension
3. Use `{{VARIABLE}}` syntax for placeholders
4. Document variables in this README
