# Design Spec: Disney Artwork Web Showcase

**Date:** June 12, 2026  
**Status:** Approved  
**Topic:** Disney Artwork Integration and Pure CSS Animations  

---

## 1. Overview and Purpose
The goal of this project is to showcase the Disney-themed SVG artwork (`Artwork Disney Tavola Disegno 1.svg`) on a beautifully designed, responsive, dark-themed HTML page called `disney.html`. The page will feature smooth load-in animations and continuous atmospheric effects (like flag-waving and glowing parts) using pure CSS, optimizing performance and keeping the page lightweight and completely self-contained.

---

## 2. Requirements and Scope
*   **Filename:** `disney.html` (located in the root directory).
*   **Integration Method:** SVG Inlined directly into the HTML to allow targeted CSS styling and animations.
*   **Theme:** Dark, magical theme with a starry background.
*   **Layout:** Full-screen, centered, and fully responsive with zero horizontal or vertical scrollbars. No additional text, headers, or external distractions.
*   **Animations:** Smooth entrance and ambient effects powered strictly by CSS.
    *   **Castle (`#castello`):** Fade-in, slight scale, and a soft glow that fades out.
    *   **Flags (`#bandiera_1`, `#bandiera_2`, `#bandiera_3`):** Smooth, continuous wave animation.
    *   **Machine Group (`#gruppo_macchina` and `#forno`):** Gentle slide-up from bottom, plus a pulsating heat/light glow effect on the oven (`#forno`).
*   **Dependencies:** None. No external JS libraries (like GSAP or jQuery) or CSS frameworks.

---

## 3. Visual Design and Layout Specs

### Sfondo (Background)
A radial gradient representing a magical night sky:
```css
background: radial-gradient(circle at center, #0f172a 0%, #020617 100%);
```
To enhance the magical atmosphere, a system of pure CSS shimmering stars will be added in a background layer.

### Responsiveness and Centering
*   The `.artwork-container` will occupy `100vw` and `100vh`.
*   We will use CSS Grid or Flexbox to perfectly center the SVG.
*   The SVG element will use:
    ```css
    max-width: 100%;
    max-height: 100%;
    width: auto;
    height: auto;
    display: block;
    ```
*   `viewBox="0 0 1080 1920"` will be preserved to ensure it scales cleanly on all screens.

---

## 4. Animation Specifications (CSS Keyframes)

### A. Entrance Animation (Slide and Fade)
*   **Elements:** The entire machine/castle group or their specific layers.
*   **Castle (`#castello`):**
    *   *Start:* `opacity: 0; transform: scale(0.95); filter: drop-shadow(0 0 0 rgba(255, 215, 0, 0));`
    *   *End:* `opacity: 1; transform: scale(1); filter: drop-shadow(0 0 20px rgba(255, 215, 0, 0.4));`
    *   *Duration:* `1.8s` with `ease-out` transition.
*   **Machine (`#gruppo_macchina`):**
    *   *Start:* `opacity: 0; transform: translateY(40px);`
    *   *End:* `opacity: 1; transform: translateY(0);`
    *   *Duration:* `1.5s` with `cubic-bezier(0.16, 1, 0.3, 1)` (smooth deceleration).

### B. Ambient Continuous Animations
*   **Flags waving (`#bandiera_1`, `#bandiera_2`, `#bandiera_3`):**
    *   A repeating infinite animation skewing and scaling the flags to simulate wind.
    *   *Keyframes:*
        ```css
        @keyframes wave {
          0% { transform: skewY(0deg) scaleX(1); }
          50% { transform: skewY(-4deg) scaleX(0.95); }
          100% { transform: skewY(0deg) scaleX(1); }
        }
        ```
    *   Each flag will have a slightly offset `animation-delay` (e.g., `0s`, `-0.5s`, `-1s`) to look organic.
*   **Oven Glow (`#forno` or its glowing paths):**
    *   An infinite pulsing gold/orange glow to simulate fire or machinery energy inside the oven.
    *   *Keyframes:*
        ```css
        @keyframes furnace-glow {
          0% { filter: drop-shadow(0 0 5px rgba(251, 146, 60, 0.5)); }
          50% { filter: drop-shadow(0 0 25px rgba(251, 146, 60, 0.9)); }
          100% { filter: drop-shadow(0 0 5px rgba(251, 146, 60, 0.5)); }
        }
        ```

---

## 5. Implementation Steps
1.  **Read and Sanitize SVG:** Load and verify the contents of `Artwork Disney Tavola Disegno 1.svg`.
2.  **HTML Scaffolding:** Create `disney.html` with appropriate metadata, CSS styles block, and background elements.
3.  **Embed SVG:** Inline the `<svg>` contents into the `.artwork-container` div inside `disney.html`.
4.  **Add CSS Classes & IDs:** Attach classes or target existing IDs (`#castello`, `#bandiera_1`, `#bandiera_2`, `#bandiera_3`, `#gruppo_macchina`, `#forno`) to apply styles and animations.
5.  **Verify Rendering & Animations:** Run the file in the browser, checking responsiveness and animation frames.
