# AGENTS.md - Disney Artwork Project

## Project Overview

This is a **self-contained interactive HTML showcase** for a Disney-themed SVG artwork. The project features a single `disney.html` file that displays an original Disney illustration with pure CSS animations, JavaScript interactivity, and a fixed 1080x1920 canvas layout.

### Key Files

| File | Purpose |
|------|---------|
| `disney.html` | **Main file** — All HTML, CSS, SVG, and JavaScript in one file |
| `Artwork Disney Tavola Disegno 1.svg` | Original Disney artwork (SVG source) |
| `faccia topolino.svg` | Mickey Mouse face element |
| `mano topolino.svg` | Mickey Mouse hand element |
| `scarpa topolino.svg` | Mickey Mouse shoe element |
| `dentro faccia topolino.svg` | Inner Mickey face (white fill) |
| `scritta_.svg` | Text/sign SVG element |
| `docs/superpowers/specs/` | Design specifications |
| `docs/superpowers/plans/` | Implementation plans |

---

## Architecture

### Single-File Philosophy
The project is intentionally **self-contained in one HTML file** (`disney.html`). This means:
- All CSS is inside `<style>` tags
- All JavaScript is inside `<script>` tags
- SVG artwork is inlined directly into the HTML
- No external dependencies, CDN links, or separate files

### Canvas Layout
- **Fixed canvas**: `1080px × 1920px`
- **Auto-scaling**: Uses CSS custom property `--canvas-scale` to fit smaller screens
- **Overflow hidden**: Parts of the canvas outside the viewport are cropped
- **Centered**: Always centered using flexbox on `body`

### SVG Structure
The main SVG has `viewBox="0 0 1080 1920"` and contains:
- `#castello` — Castle structure
- `#bandiera_1`, `#bandiera_2`, `#bandiera_3` — Three flags
- `#gruppo_macchina` — Machine group (wheels, oven, etc.)
- `#forno` — Oven element
- `#gruppo_ingranaggio` — Gear group
- `#tubo` — Vertical pipe element (spawns Mickey elements)

---

## Tech Stack

- **HTML5** — Semantic structure
- **SVG** — Vector graphics, inlined
- **CSS3** — Animations, transforms, filters, custom properties
- **Vanilla JavaScript** — No frameworks or libraries
- **No build tools** — Pure static file

---

## Code Conventions

### CSS
- Use `#id` selectors for SVG elements (they have explicit IDs)
- Use CSS custom properties for dynamic values (e.g., `--pulse-intensity`)
- Animations use `@keyframes` with descriptive names (e.g., `castleEntrance`, `furnaceGlow`)
- Use `will-change` for GPU-accelerated properties
- Prefer `transform` and `opacity` for animations (compositable properties)

### JavaScript
- Use `const` and `let`, never `var`
- Cache DOM queries (e.g., `const svg = document.querySelector('svg')`)
- Use `requestAnimationFrame` for animation loops
- Keep physics calculations simple and efficient
- Always clean up intervals and timeouts when not needed

### SVG
- Preserve original `id` attributes (they are used for CSS/JS targeting)
- Keep `viewBox="0 0 1080 1920"` on the main SVG
- Use inline SVG for spawned elements (Mickey face/hand/shoe)

---

## Animation System

### Types of Animations
1. **Entrance animations** — Run once on page load (e.g., `castleEntrance`)
2. **Ambient/continuous animations** — Loop forever (e.g., `wave`, `furnaceGlow`, `castlePulse`)
3. **Interactive animations** — Triggered by user (e.g., pulse slider)
4. **Physics-based animations** — JavaScript-driven (e.g., Mickey elements flying)

### Timing Conventions
- Entrance animations: `1.5s - 2s` duration
- Ambient animations: `2s - 3s` duration, `infinite` loop
- Physics: `requestAnimationFrame` at 60fps
- Use `animation-delay` to stagger related animations

---

## Interactive Features

### Pulse Slider
- HTML range input (0-100)
- Updates CSS custom property `--pulse-intensity`
- Controls `castlePulse` animation on `#castello` and flags
- Auto-hides after 3 seconds of mouse inactivity
- Positioned at bottom center of the canvas

### Tubo Spitting
- Spawns Mickey elements from bottom of `#tubo`
- Sequential cycle: face → hand → shoe (loop)
- Interval: `500ms` (every 0.5 seconds)
- Physics: gravity, wall/floor bouncing, rotation
- Max 30 elements; oldest removed when exceeded
- Elements are HTML divs with inline SVG

---

## Working Guidelines

### When Modifying disney.html
1. **Preserve existing structure** — Don't split into multiple files
2. **Add new CSS before `</style>`** — Keep animations grouped logically
3. **Add new JS before `</body>`** — After existing scripts
4. **Test responsive scaling** — Resize browser to verify canvas behavior
5. **Verify 60fps** — Check Performance tab in DevTools

### Adding New Animations
- Use descriptive `@keyframes` names
- Apply `animation-delay` for staggered effects
- Add `will-change` for performance-critical animations
- Test on both large and small screens

### Adding New Interactive Elements
- Use CSS custom properties for values that need JS control
- Keep JavaScript modular (functions with clear names)
- Clean up event listeners and intervals when possible
- Position elements relative to the `.artwork-container` (not viewport)

---

## Performance Notes

- All animations are GPU-accelerated (transform, opacity, filter)
- Physics loop uses `requestAnimationFrame`
- Star generation: 150 stars, random positions
- Element capping: Max 30 Mickey elements to prevent DOM bloat
- Use `getBoundingClientRect()` sparingly; cache results when possible

---

## Git Conventions

- Commit messages should be descriptive:
  - `feat:` for new features
  - `fix:` for bug fixes
  - `docs:` for documentation
  - `style:` for CSS/visual changes
- Do NOT commit without user consent for major changes
- Keep commits atomic (one feature per commit)

---

## Testing Checklist

Before declaring any work complete:
- [ ] File loads without console errors
- [ ] Canvas is centered and properly scaled
- [ ] All entrance animations play correctly
- [ ] Ambient animations loop smoothly
- [ ] Interactive features work (slider, tubo)
- [ ] No scrollbars appear
- [ ] 60fps maintained during animations
- [ ] File size remains reasonable (under 200KB)

---

## Contact & Context

- **Project root**: `/Users/alexandermarinov/Documents/GitHub/poster disney 2/`
- **Main file**: `disney.html`
- **Design specs**: `docs/superpowers/specs/`
- **Implementation plans**: `docs/superpowers/plans/`
- **Language**: Italian (UI text), English (code)

When in doubt, prefer **simplicity over complexity**. This is a showcase page — it should feel magical, performant, and self-contained.
