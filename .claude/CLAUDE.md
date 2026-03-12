
# Mobius CMS UI — Project Context

## Prompt Coaching
After completing every task, add a **"Prompt upgrade"** section that restates the user's original prompt the way an expert would have written it. Use precise token names, exact values, Figma node references, and tight structure. This teaches better prompting over time.

## Role
Act as a senior front-end developer building pixel-perfect HTML/CSS/JS components from Figma designs using the official Mobius design system. Component-first workflow: build standalone, verify against Figma, then port to the full board.

## Figma Sources
- **CMS UI (main file):** `rKpXpaHBh4wBIaoDCrZCwC`
- **Component Library:** `pdA0JDXyz5t6hcERhiG4j1`
- Use `get_design_context`, `get_screenshot`, `get_variable_defs` to extract specs. Save large outputs to files and parse with Python when needed.

## Design System — Single Source of Truth
All CSS values MUST reference official Mobius design tokens. Zero hardcoded colors, fonts, or sizes. The token file is `design-tokens.css`, rebuilt from the official Mobius CSS package.

### Fonts
- `--default-font` — Roboto Condensed (body text, labels, meta)
- `--header-font` — Roboto plain (titles, headers — NOT condensed)
- `--font-icon` / `"Mobius-System-Icons"` — custom icon font (.woff2/.woff in fonts/)
- Always include an inline `@font-face` fallback in HTML `<style>` for the icon font (fixes file:// loading issues)

### Font Sizes
- `--size-default` (14px), `--size-Sm` (11px), `--size-hdrSm` (18px), `--size-hdrMd` (24px), `--size-hdrLg` (36px)

### Font Weights
- `--font-light` (300), `--font-normal` (400), `--font-bold` (700)

### Spacing (official scale from Figma node 6200-2597)
| Token | Value | Alias |
|-------|-------|-------|
| `--space-str` | 1px | (stroke/separator only) |
| `--space-1` | 4px | `--space-sm` |
| `--space-2` | 8px | `--space-md` |
| `--space-3` | 12px | `--space-lg` |
| `--space-4` | 16px | `--space-xl` |
| `--space-5` | 20px | `--space-xxl` |
| `--space-6` | 24px | `--space-xxxl` |
| `--space-7` | 32px | `--space-huge` |
| `--space-8` | 40px | |
| `--space-9` | 48px | |
| `--space-10` | 56px | |
| `--space-11` | 64px | |

### Key Color Tokens
- `--ForegroundPrimary` — default text
- `--ForegroundAction` — links, interactive elements (blue)
- `--ForegroundWatermark` — subtle UI elements (chevrons, details icon)
- `--MouseOverPrimary` — hover state for links
- `--InformationBlue`, `--InformationGreen`, `--InformationRed`, `--InformationOrange`, `--InformationBlack`
- `--BackgroundStrong` — card background
- `--BackgroundStrongStepTwo` — card hover background
- `--BackgroundPrimary` — page background
- `--MessageDeliveryBackgroundInfo/Warning/Critical/Ok` — avatar status backgrounds

### Shadows
- `--shadow-sm`, `--shadow-md` (card default), `--shadow-lg`

### Transitions
- `--transition-slow` (0.3s) — preferred for hover states
- `--transition-base` (0.15s), `--transition-fast` (0.1s)

## Component Patterns

### Icon Font Usage
Use Mobius-System-Icons via `::before`/`::after` pseudo-elements or inline HTML entities. Never use inline SVGs for system icons.
```css
.element::before {
  font-family: "Mobius-System-Icons";
  content: "\E84C";
  -webkit-font-smoothing: antialiased;
}
```

### Known Icon Codepoints
- `\E84C` — location pin
- `\E752` — calendar
- `\E00B` — chevron down (Arrow_PointerDownSm)
- `\E007` — chevron up
- `\EB0A` — details/expand icon

### Link Hover Pattern
```css
a {
  color: inherit;
  text-decoration: none;
  transition: color var(--transition-slow), font-weight var(--transition-slow);
}
a:hover {
  color: var(--MouseOverPrimary);
  text-decoration: none;
  font-weight: bolder;
}
```

### Card Avatar
- 50px x 50px, border-radius: 100px, border: 4px solid
- Status classes swap border color + background: `.status-inprogress`, `.status-paused`, `.status-delayed`, `.status-completed`, `.status-default`
- `overflow: visible` on container (for tooltip), `border-radius: 100px` on img
- Tooltip: positioned `top: 50%; right: calc(100% - var(--space-md))` — top edge at avatar midpoint, overlapping slightly

### Progress Bar (tri-color stacked)
- CSS custom props: `--progress-complete` (blue), `--progress-delta` (green/red)
- Add `.behind` class to flip delta from green to red
- Structure: black bg > delta layer > complete layer

### Open/Close State
- Triggered by clicking chevrons (not whole card)
- Uses `max-height` + `opacity` transition (0.3s open, 0.5s close)
- Chevrons swap from down (`\E00B`) to up (`\E007`) via CSS `::before`
- Card background reverts to `--BackgroundStrong` when open

### Line-Height Gotcha
Mobius line-height tokens (e.g. `--lh-default: 20px` on 14px text) add phantom vertical padding. When tight spacing is needed, override with `line-height: 1` or `1.2` and reduce gap to compensate.

## File Structure
```
outputs/
  design-tokens.css       — all tokens (fonts, colors, spacing, shadows, etc.)
  cms-card-standalone.html — card component with all states
  cms-kanban.html          — full kanban board
  Dozer.svg                — example vehicle SVG
  fonts/
    Mobius-System-Icons.woff2 / .woff
    fontImport.css
    fontTokens.css
    typography.css
    LightTheme.css / DarkTheme.css / BlueTheme.css / etc.
```

## Workflow
1. Extract specs from Figma using MCP tools
2. Build component standalone with all states (default, hover, open, status variants)
3. Verify against Figma screenshots
4. Use Python scripts for bulk token remapping when needed (longest tokens first to avoid partial matches)
5. Port verified component into full board
6. Package as zip for developer handoff
