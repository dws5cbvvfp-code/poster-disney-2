# agent-2.md — Disney Artwork Project (Post-Implementation)

## Project Overview

This is a **self-contained interactive HTML showcase** for a Disney-themed SVG artwork. The project features a single `disney.html` file that displays an original Disney illustration with pure CSS animations, JavaScript interactivity, and a fixed 1080x1920 canvas layout.

**Current State:** The artwork has been modified to include a **particle stacking system** where Mickey Mouse elements (face, hand, shoe, inner face) spawn from a pipe, fall, and stack on top of each other to completely cover the canvas like filling a glass with water. After 17 seconds, a red text overlay (`scritta_.svg`) fades in over the entire scene.

### Key Files

| File | Purpose |
|------|---------|
| `disney.html` | **Main file** — All HTML, CSS, SVG, and JavaScript in one file |
| `Artwork Disney Tavola Disegno 1.svg` | Original Disney artwork (SVG source) |
| `faccia topolino.svg` | Mickey Mouse face element (**opacity: 0** in source) |
| `mano topolino.svg` | Mickey Mouse hand element (**opacity: 0** in source) |
| `scarpa topolino.svg` | Mickey Mouse shoe element (**opacity: 0** in source) |
| `dentro faccia topolino.svg` | Inner Mickey face (white fill, **opacity: 0** in source) |
| `scritta_.svg` | Red text/sign SVG element (appears after 17s via fade) |
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
- **Overflow hidden**: Elements outside the viewport are cropped (restored to `overflow: hidden` for stacking system)
- **Centered**: Always centered using flexbox on `body`

### SVG Structure
The main SVG has `viewBox="0 0 1080 1920"` and contains:
- `#castello` — Castle structure
- `#bandiera_1`, `#bandiera_2`, `#bandiera_3` — Three flags
- `#gruppo_macchina` — Machine group (wheels, oven, etc.)
- `#forno` — Oven element
- `#gruppo_ingranaggio` — Gear group
- `#tubo` — Vertical pipe element (original spawn point)

---

## New Systems (Implemented)

### 1. Particle Stacking System
Mickey elements spawn from the top (`y = -60`) and fall with gravity, stacking on top of each other to fill the canvas bottom-up.

**Mechanism:**
- Canvas is divided into **40 columns** (`COLUMN_WIDTH = 27px`)
- Each column tracks a `groundLevels` array starting at `1920`
- Elements spawn in random columns with small random horizontal velocity
- When an element hits the ground level of its column, it stops (`settled = true`)
- Ground level rises by the element's height, creating realistic stacking
- Neighboring columns are affected slightly for natural terrain variation

**Parameters:**
- `MAX_ELEMENTS = 2000`
- Spawn interval: `30ms` (~33 elements/second)
- Gravity: `0.3` per frame (capped at terminal velocity `15`)
- Element sizes: face 40×46, hand 35×40, shoe 45×33, innerFace 55×38
- Horizontal friction: `0.95` when settled

### 2. SVG Source Opacity
The original SVG source files have been modified with `style="opacity:0"` on their root `<svg>` tags:
- `faccia topolino.svg`
- `dentro faccia topolino.svg`
- `scarpa topolino.svg`
- `mano topolino.svg`

These files are **not rendered** by default. The visible elements are inline SVG strings injected via JavaScript into spawned `<div>` elements.

### 3. Scritta Overlay System
The `scritta_.svg` (red text) is embedded as an inline SVG overlay inside a `div#scritta-overlay`.

**Behavior:**
- Initially `opacity: 0` with `pointer-events: none`
- After exactly `17000ms` from page load, `.visible` class is added
- CSS transition: `opacity 2s ease-in-out` creates the fade-in effect
- `z-index: 100` renders above Mickey elements (`z-index: 50`)
- Fills the entire 1080×1920 canvas

---

## Tech Stack

- **HTML5** — Semantic structure
- **SVG** — Vector graphics, inlined
- **CSS3** — Animations, transforms, transitions, custom properties
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
- Use inline SVG for spawned elements (Mickey face/hand/shoe/innerFace)
- Source files may have `style="opacity:0"` — this is intentional

---

## Animation System

### Types of Animations
1. **Entrance animations** — Run once on page load (e.g., `castleEntrance`)
2. **Ambient/continuous animations** — Loop forever (e.g., `wave`, `furnaceGlow`, `castlePulse`)
3. **Interactive animations** — Triggered by user (e.g., pulse slider)
4. **Physics-based animations** — JavaScript-driven (e.g., Mickey elements stacking)
5. **Timed overlay** — Scritta fade-in after 17 seconds

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

### Tubo Spitting / Stacking
- Spawns Mickey elements from **top of canvas** (not tubo anymore for stacking physics)
- Sequential cycle: face → hand → shoe → innerFace (loop)
- Interval: `30ms` (very fast for quick filling)
- Physics: gravity, column-based floor detection, settling
- Max 2000 elements; oldest removed when exceeded
- Elements are HTML `<div>` elements with inline SVG

---

## Working Guidelines

### When Modifying disney.html
1. **Preserve existing structure** — Don't split into multiple files
2. **Add new CSS before `</style>`** — Keep animations grouped logically
3. **Add new JS before `</body>`** — After existing scripts
4. **Test responsive scaling** — Resize browser to verify canvas behavior
5. **Verify 60fps** — Check Performance tab in DevTools
6. **Stacking physics** — If modifying the particle system, maintain the `groundLevels` column array logic

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
- Element capping: Max 2000 Mickey elements to prevent DOM bloat
- Use `getBoundingClientRect()` sparingly; cache results when possible
- **Stacking system** uses a simple array lookup per element (`O(1)` per column check)

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
- [ ] Interactive features work (slider, stacking)
- [ ] No scrollbars appear
- [ ] 60fps maintained during animations
- [ ] File size remains reasonable (under 200KB)
- [ ] **Stacking system fills canvas bottom-up**
- [ ] **Scritta appears after 17 seconds with smooth fade**
- [ ] **Source SVG files remain at opacity:0**
- [ ] **Overlay covers full 1080×1920 without clipping**

---

## Contact & Context

- **Project root**: `/Users/alexandermarinov/Documents/GitHub/poster disney 2/`
- **Main file**: `disney.html`
- **Design specs**: `docs/superpowers/specs/`
- **Implementation plans**: `docs/superpowers/plans/`
- **Language**: Italian (UI text), English (code)

When in doubt, prefer **simplicity over complexity**. This is a showcase page — it should feel magical, performant, and self-contained.

**Last Updated:** Current session — Stacking system + Scritta overlay implemented.
