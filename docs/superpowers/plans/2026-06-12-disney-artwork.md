# Disney Artwork Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create `disney.html` with inlined SVG artwork and pure CSS animations

**Architecture:** A self-contained HTML file with inlined SVG, CSS animations targeting specific SVG elements by ID, featuring a dark magical background with entrance and ambient animations.

**Tech Stack:** HTML5, SVG, CSS3 (animations, transforms, filters)

---

## File Structure

| File | Purpose |
|------|---------|
| `disney.html` | Main page file with inline SVG and all CSS/animations |

---

### Task 1: Create HTML Scaffold with Dark Background

**Files:**
- Create: `disney.html`

- [ ] **Step 1: Write HTML base structure**

```html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Disney Artwork</title>
    <style>
        /* Reset and base styles */
        *, *::before, *::after {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        html, body {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background: radial-gradient(circle at center, #0f172a 0%, #020617 100%);
        }
        
        .artwork-container {
            width: 100vw;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }
        
        .artwork-container svg {
            max-width: 100%;
            max-height: 100%;
            width: auto;
            height: auto;
            display: block;
        }
        
        /* Starry background effect */
        .stars {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }
        
        .star {
            position: absolute;
            width: 2px;
            height: 2px;
            background: white;
            border-radius: 50%;
            opacity: 0;
            animation: twinkle 3s infinite ease-in-out;
        }
    </style>
</head>
<body>
    <div class="stars" id="stars"></div>
    <div class="artwork-container">
        <!-- SVG will be inserted here in Task 2 -->
    </div>
</body>
</html>
```

- [ ] **Step 2: Add star generation and twinkle animation**

Add to the `<style>` block before `</style>`:

```css
@keyframes twinkle {
    0%, 100% { opacity: 0; transform: scale(0.5); }
    50% { opacity: 1; transform: scale(1); }
}
```

Add before `</body>`:

```html
<script>
    const starsContainer = document.getElementById('stars');
    for (let i = 0; i < 150; i++) {
        const star = document.createElement('div');
        star.className = 'star';
        star.style.left = Math.random() * 100 + '%';
        star.style.top = Math.random() * 100 + '%';
        star.style.animationDelay = Math.random() * 3 + 's';
        star.style.animationDuration = (2 + Math.random() * 3) + 's';
        star.style.width = (1 + Math.random() * 2) + 'px';
        star.style.height = star.style.width;
        starsContainer.appendChild(star);
    }
</script>
```

- [ ] **Step 3: Verify HTML loads correctly**

Run: `python3 -m http.server 8000` (or open file directly)
Open: `http://localhost:8000/disney.html`
Expected: Dark background with twinkling stars visible

---

### Task 2: Inline the SVG Artwork

**Files:**
- Modify: `disney.html` (insert SVG into `.artwork-container`)

- [ ] **Step 1: Read SVG file and extract content**

Run: `cat "Artwork Disney Tavola Disegno 1.svg"`
Copy the entire content between `<svg>` tags.

- [ ] **Step 2: Paste SVG inside artwork-container**

Replace the comment `<!-- SVG will be inserted here in Task 2 -->` with the full SVG content from the file.

The container should look like:
```html
<div class="artwork-container">
    <svg id="Livello_1" data-name="Livello 1" xmlns="http://www.w3.org/2000/svg" ... viewBox="0 0 1080 1920">
        <!-- Full SVG content here -->
    </svg>
</div>
```

- [ ] **Step 3: Verify SVG renders correctly**

Open: `http://localhost:8000/disney.html`
Expected: SVG artwork appears centered on dark background

---

### Task 3: Castle Entrance Animation

**Files:**
- Modify: `disney.html` (add CSS for `#castello`)

- [ ] **Step 1: Add castle entrance CSS**

Add to `<style>` block:

```css
/* Castle entrance animation */
#castello {
    opacity: 0;
    transform: scale(0.95);
    filter: drop-shadow(0 0 0 rgba(255, 215, 0, 0));
    animation: castleEntrance 1.8s ease-out forwards;
    transform-origin: center center;
    will-change: transform, opacity, filter;
}

@keyframes castleEntrance {
    0% {
        opacity: 0;
        transform: scale(0.95);
        filter: drop-shadow(0 0 0 rgba(255, 215, 0, 0));
    }
    60% {
        filter: drop-shadow(0 0 30px rgba(255, 215, 0, 0.5));
    }
    100% {
        opacity: 1;
        transform: scale(1);
        filter: drop-shadow(0 0 0 rgba(255, 215, 0, 0));
    }
}
```

- [ ] **Step 2: Verify castle fades in smoothly**

Open: `http://localhost:8000/disney.html`
Expected: Castle structure fades in with slight scale-up and brief golden glow at 60% mark

---

### Task 4: Machine Group Entrance Animation

**Files:**
- Modify: `disney.html` (add CSS for `#gruppo_macchina`)

- [ ] **Step 1: Add machine group entrance CSS**

Add to `<style>` block:

```css
/* Machine group entrance animation */
#gruppo_macchina {
    opacity: 0;
    transform: translateY(40px);
    animation: machineEntrance 1.5s cubic-bezier(0.16, 1, 0.3, 1) 0.3s forwards;
    will-change: transform, opacity;
}

@keyframes machineEntrance {
    0% {
        opacity: 0;
        transform: translateY(40px);
    }
    100% {
        opacity: 1;
        transform: translateY(0);
    }
}
```

- [ ] **Step 2: Verify machine slides up**

Open: `http://localhost:8000/disney.html`
Expected: Machine group (wheels, oven, etc.) slides up from below with 0.3s delay after castle starts

---

### Task 5: Flag Waving Animation

**Files:**
- Modify: `disney.html` (add CSS for flags)

- [ ] **Step 1: Add flag waving CSS**

Add to `<style>` block:

```css
/* Flag waving animations */
#bandiera_1 {
    animation: wave 2s ease-in-out infinite;
    transform-origin: bottom left;
}

#bandiera_2 {
    animation: wave 2s ease-in-out infinite -0.5s;
    transform-origin: bottom left;
}

#bandiera_3 {
    animation: wave 2s ease-in-out infinite -1s;
    transform-origin: bottom left;
}

@keyframes wave {
    0%, 100% {
        transform: skewY(0deg) scaleX(1);
    }
    50% {
        transform: skewY(-4deg) scaleX(0.95);
    }
}
```

- [ ] **Step 2: Verify flags wave with staggered timing**

Open: `http://localhost:8000/disney.html`
Expected: Three flags wave independently with offset timing

---

### Task 6: Oven Glow Animation

**Files:**
- Modify: `disney.html` (add CSS for `#forno`)

- [ ] **Step 1: Add oven glow CSS**

Add to `<style>` block:

```css
/* Oven glow animation */
#forno {
    animation: furnaceGlow 3s ease-in-out infinite;
    animation-delay: 1.5s;
    will-change: filter;
}

@keyframes furnaceGlow {
    0%, 100% {
        filter: drop-shadow(0 0 5px rgba(251, 146, 60, 0.5));
    }
    50% {
        filter: drop-shadow(0 0 25px rgba(251, 146, 60, 0.9));
    }
}
```

- [ ] **Step 2: Verify oven pulses with warm glow**

Open: `http://localhost:8000/disney.html`
Expected: Oven area pulses with warm orange glow after initial entrance completes

---

### Task 7: Final Verification and Polish

**Files:**
- Modify: `disney.html` (final responsive tweaks if needed)

- [ ] **Step 1: Test responsiveness**

Test in browser at:
- Desktop (1920x1080)
- Tablet (768x1024)
- Mobile (375x667)

Expected: SVG scales proportionally without scrollbars at all sizes

- [ ] **Step 2: Performance check**

Open browser DevTools → Performance tab → Record 3 seconds
Expected: 60 FPS, no jank, GPU-accelerated animations (composited layers)

- [ ] **Step 3: Commit the final file**

```bash
git add disney.html
git commit -m "feat: create disney.html with animated SVG artwork

- Add dark magical background with twinkling stars
- Inline SVG with targeted CSS animations
- Castle entrance: fade-in with scale and golden glow
- Machine entrance: slide-up with easing
- Flags: continuous wave with staggered timing
- Oven: pulsating warm glow effect
- Fully responsive and self-contained"
```

---

## Plan Self-Review

### Spec Coverage
- ✅ Dark magical background → Task 1
- ✅ Starry sky effect → Task 1
- ✅ SVG inlined → Task 2
- ✅ Castle entrance animation → Task 3
- ✅ Machine entrance animation → Task 4
- ✅ Flag waving → Task 5
- ✅ Oven glow → Task 6
- ✅ Responsive full-screen layout → Task 1, 7
- ✅ Self-contained, no dependencies → All tasks

### Placeholder Scan
- No TBD/TODO found
- No "implement later" found
- All steps include actual CSS code and exact selectors
- All verification steps include exact browser behavior

### Type Consistency
- All animation names match in @keyframes declarations and element selectors
- Timing units consistent (s, ms)
- CSS selectors match SVG IDs exactly as they appear in the file
