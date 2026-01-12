# CSS & Design System Extractor

Extract complete design systems from any website including CSS custom properties, computed styles, typography, and color palettes.

## Overview

This module complements ReactStealer by extracting the visual design layer:
- CSS custom properties (design tokens)
- Computed styles from key elements
- Typography system (fonts, sizes, weights)
- Color palette with semantic naming
- Spacing and layout patterns

## Step 1: Inject StyleStealer

Inject this code using `mcp__claude-in-chrome__javascript_tool`:

```javascript
const StyleStealer = (function() {
  // Extract all CSS custom properties from :root
  function getCustomProperties() {
    const styles = getComputedStyle(document.documentElement);
    const props = {};

    // Get from stylesheets
    for (const sheet of document.styleSheets) {
      try {
        for (const rule of sheet.cssRules) {
          if (rule.selectorText === ':root' || rule.selectorText === 'html') {
            const text = rule.cssText;
            const matches = text.matchAll(/--([^:]+):\s*([^;]+)/g);
            for (const match of matches) {
              props[`--${match[1].trim()}`] = match[2].trim();
            }
          }
        }
      } catch (e) {
        // Cross-origin stylesheet, skip
      }
    }

    // Also check computed style for any we missed
    for (let i = 0; i < styles.length; i++) {
      const prop = styles[i];
      if (prop.startsWith('--')) {
        if (!props[prop]) {
          props[prop] = styles.getPropertyValue(prop).trim();
        }
      }
    }

    return props;
  }

  // Extract computed styles for an element
  function getElementStyles(selector) {
    const el = document.querySelector(selector);
    if (!el) return null;

    const computed = getComputedStyle(el);
    const important = [
      'color', 'backgroundColor', 'fontSize', 'fontFamily', 'fontWeight',
      'lineHeight', 'letterSpacing', 'padding', 'margin', 'border',
      'borderRadius', 'boxShadow', 'display', 'flexDirection', 'gap',
      'alignItems', 'justifyContent', 'width', 'height', 'maxWidth',
      'position', 'top', 'right', 'bottom', 'left', 'zIndex',
      'opacity', 'transform', 'transition', 'cursor', 'textDecoration'
    ];

    const styles = {};
    for (const prop of important) {
      const value = computed[prop];
      if (value && value !== 'none' && value !== 'normal' && value !== 'auto' && value !== '0px') {
        styles[prop] = value;
      }
    }

    return {
      selector,
      tagName: el.tagName.toLowerCase(),
      className: el.className,
      styles
    };
  }

  // Extract typography system
  function getTypography() {
    const headings = [];
    for (let i = 1; i <= 6; i++) {
      const h = document.querySelector(`h${i}`);
      if (h) {
        const styles = getComputedStyle(h);
        headings.push({
          level: i,
          fontSize: styles.fontSize,
          fontWeight: styles.fontWeight,
          lineHeight: styles.lineHeight,
          fontFamily: styles.fontFamily,
          color: styles.color
        });
      }
    }

    const body = document.body;
    const bodyStyles = getComputedStyle(body);

    return {
      baseFontSize: bodyStyles.fontSize,
      baseFontFamily: bodyStyles.fontFamily,
      baseLineHeight: bodyStyles.lineHeight,
      baseColor: bodyStyles.color,
      headings
    };
  }

  // Extract color palette from CSS variables and common elements
  function getColorPalette() {
    const colors = new Map();
    const props = getCustomProperties();

    // Colors from CSS variables
    for (const [key, value] of Object.entries(props)) {
      if (isColor(value)) {
        colors.set(key, { value, source: 'css-variable' });
      }
    }

    // Colors from common elements
    const selectors = [
      'body', 'a', 'button', '.btn', '[class*="button"]',
      'h1', 'h2', 'h3', 'nav', 'header', 'footer',
      '.primary', '.secondary', '.success', '.error', '.warning'
    ];

    for (const sel of selectors) {
      const el = document.querySelector(sel);
      if (el) {
        const styles = getComputedStyle(el);
        if (styles.color) colors.set(`${sel}-color`, { value: styles.color, source: sel });
        if (styles.backgroundColor && styles.backgroundColor !== 'rgba(0, 0, 0, 0)') {
          colors.set(`${sel}-bg`, { value: styles.backgroundColor, source: sel });
        }
      }
    }

    return Object.fromEntries(colors);
  }

  // Check if a value is a color
  function isColor(value) {
    return /^(#|rgb|hsl|oklch|lab|lch|color\(|transparent|currentcolor)/i.test(value.trim());
  }

  // Extract all fonts used on the page
  function getFonts() {
    const fonts = new Set();
    document.querySelectorAll('*').forEach(el => {
      const family = getComputedStyle(el).fontFamily;
      if (family) {
        family.split(',').forEach(f => {
          const cleaned = f.trim().replace(/["']/g, '');
          if (cleaned && !cleaned.includes('system') && !cleaned.includes('inherit')) {
            fonts.add(cleaned);
          }
        });
      }
    });
    return Array.from(fonts);
  }

  // Extract spacing scale from CSS variables
  function getSpacing() {
    const props = getCustomProperties();
    const spacing = {};

    for (const [key, value] of Object.entries(props)) {
      if (/space|gap|padding|margin|size/i.test(key) && /^\d/.test(value.trim())) {
        spacing[key] = value;
      }
    }

    return spacing;
  }

  // Get full design system export
  function extractAll() {
    return {
      customProperties: getCustomProperties(),
      typography: getTypography(),
      colors: getColorPalette(),
      fonts: getFonts(),
      spacing: getSpacing()
    };
  }

  // Generate Tailwind config from extracted styles
  function toTailwindConfig() {
    const data = extractAll();
    const colors = {};
    const spacing = {};

    // Convert CSS variables to Tailwind colors
    for (const [key, info] of Object.entries(data.colors)) {
      const name = key
        .replace(/^--/, '')
        .replace(/[-_]/g, '-')
        .toLowerCase();
      colors[name] = info.value;
    }

    // Convert spacing
    for (const [key, value] of Object.entries(data.spacing)) {
      const name = key.replace(/^--/, '').replace(/[-_]/g, '-');
      spacing[name] = value;
    }

    return {
      theme: {
        extend: {
          colors,
          spacing,
          fontFamily: {
            sans: data.fonts.slice(0, 3),
          }
        }
      }
    };
  }

  // Generate CSS variables file
  function toCSSVariables() {
    const props = getCustomProperties();
    let css = ':root {\n';
    for (const [key, value] of Object.entries(props)) {
      css += `  ${key}: ${value};\n`;
    }
    css += '}\n';
    return css;
  }

  // Format for LLM consumption
  function getForLLM() {
    const data = extractAll();

    let output = '# Extracted Design System\n\n';

    output += '## CSS Custom Properties\n```css\n';
    output += toCSSVariables();
    output += '```\n\n';

    output += '## Typography\n```json\n';
    output += JSON.stringify(data.typography, null, 2);
    output += '\n```\n\n';

    output += '## Color Palette\n```json\n';
    output += JSON.stringify(data.colors, null, 2);
    output += '\n```\n\n';

    output += '## Fonts Used\n';
    output += data.fonts.map(f => `- ${f}`).join('\n');
    output += '\n\n';

    output += '## Tailwind Config\n```javascript\n';
    output += JSON.stringify(toTailwindConfig(), null, 2);
    output += '\n```\n';

    return output;
  }

  return {
    getCustomProperties,
    getElementStyles,
    getTypography,
    getColorPalette,
    getFonts,
    getSpacing,
    extractAll,
    toTailwindConfig,
    toCSSVariables,
    getForLLM
  };
})();

window.StyleStealer = StyleStealer;
```

## Step 2: Extract Design System

### Get Everything
```javascript
StyleStealer.extractAll()
```

Returns complete design system data including custom properties, typography, colors, fonts, and spacing.

### Get LLM-Formatted Output
```javascript
StyleStealer.getForLLM()
```

Returns a markdown-formatted document perfect for feeding to an LLM to recreate the design.

### Get Tailwind Config
```javascript
StyleStealer.toTailwindConfig()
```

Returns a ready-to-use Tailwind configuration object with extracted colors, spacing, and fonts.

### Get CSS Variables
```javascript
StyleStealer.toCSSVariables()
```

Returns a CSS file with all extracted custom properties.

### Get Specific Element Styles
```javascript
StyleStealer.getElementStyles('button.primary')
```

Returns computed styles for a specific element by selector.

## Example Output

```json
{
  "customProperties": {
    "--background": "0 0% 0%",
    "--foreground": "200 7% 91%",
    "--primary": "204 88% 53%",
    "--border": "210 7% 20%"
  },
  "typography": {
    "baseFontSize": "15px",
    "baseFontFamily": "TwitterChirp, system-ui, sans-serif",
    "baseLineHeight": "1.3125",
    "baseColor": "rgb(230, 233, 234)",
    "headings": [
      { "level": 1, "fontSize": "20px", "fontWeight": "700" }
    ]
  },
  "colors": {
    "--primary": { "value": "rgb(29, 155, 240)", "source": "css-variable" },
    "a-color": { "value": "rgb(29, 155, 240)", "source": "a" }
  },
  "fonts": ["TwitterChirp", "Segoe UI", "Roboto"],
  "spacing": {
    "--space-1": "4px",
    "--space-2": "8px",
    "--space-4": "16px"
  }
}
```

## Integration with ReactStealer

Use StyleStealer alongside ReactStealer for complete site extraction:

```javascript
// 1. Extract design system
const designSystem = StyleStealer.getForLLM();

// 2. Extract components
const components = ReactStealer.summary();

// 3. Get specific component with context
const button = ReactStealer.getForLLM('Button');

// Now you have both the visual design and component structure
```

## Tips

- Run StyleStealer first to understand the design language
- Use `getElementStyles()` to understand specific component styling
- The Tailwind config output can be directly used in your project
- Check for CSS-in-JS by looking for runtime-generated class names
- Some sites use obfuscated variable names - look for patterns
