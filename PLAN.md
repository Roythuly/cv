# Implementation Plan: Vertical Layout & Theme System

## Overview
This plan outlines the steps to:
1. Revert horizontal carousel to vertical layout (with smooth scrolling)
2. Add sun/moon icon theme toggle
3. Create multiple alternative `index_v*.html` pages with different theme variants

---

## Part 1: Reverting Horizontal Carousel to Vertical Layout

### Current State Analysis
- The current `index.html` already uses a vertical layout
- If a horizontal carousel exists, it would be in `.section-carousel` container
- Need to ensure smooth scrolling behavior is preserved

### Implementation Steps

#### 1.1 HTML Structure Changes
**File: `index.html`**
- Remove or modify any `.section-carousel` wrapper if present
- Ensure `<main>` contains sections directly (not wrapped in carousel)
- Verify sections have proper `id` attributes for anchor navigation
- Structure should be:
  ```html
  <main>
    <section id="about" class="section">...</section>
    <section id="research" class="section">...</section>
    <section id="experience" class="section">...</section>
    <section id="publications" class="section">...</section>
    <section id="contact" class="section">...</section>
  </main>
  ```

#### 1.2 CSS Changes for Vertical Layout
**File: `styles.css`**

**Remove horizontal carousel styles:**
- Remove `.section-carousel` styles (if present)
- Remove `overflow-x: auto` and `scroll-snap-type: x mandatory`
- Remove horizontal scrollbar styling

**Add/enhance vertical smooth scrolling:**
```css
html {
  scroll-behavior: smooth;
}

body {
  overflow-x: hidden; /* Prevent horizontal scroll */
}

main {
  display: flex;
  flex-direction: column;
}

.section {
  width: 100%;
  scroll-margin-top: 80px; /* Account for fixed header if needed */
}
```

**Ensure proper spacing:**
- Maintain consistent vertical padding between sections
- Use `gap` or `margin` for spacing (not horizontal positioning)

#### 1.3 JavaScript Updates
**File: `theme.js` (to be created) or update existing JS**

**Update navigation scroll behavior:**
- Change from `scrollLeft` to `scrollTop` calculations
- Update anchor link scrolling to use `window.scrollTo()` with vertical behavior
- Modify active section detection to use `offsetTop` instead of `offsetLeft`

**Example:**
```javascript
function scrollToSection(target) {
  const targetElement = document.querySelector(target);
  if (targetElement) {
    const offsetTop = targetElement.offsetTop - 80; // Account for header
    window.scrollTo({
      top: offsetTop,
      behavior: 'smooth'
    });
  }
}
```

**Update active link highlighting:**
- Change scroll position detection from horizontal (`scrollLeft`) to vertical (`scrollY`)
- Use `IntersectionObserver` for better performance

---

## Part 2: Adding Sun/Moon Icon Theme Toggle

### 2.1 HTML Structure
**File: `index.html`**

**Add theme toggle button in header:**
```html
<header class="site-header">
  <div class="site-top">
    <div class="name-block">...</div>
    <nav class="site-nav">...</nav>
    <button id="themeToggle" class="theme-toggle" aria-label="Toggle theme">
      <svg class="theme-icon sun-icon" aria-hidden="true">
        <!-- Sun icon SVG -->
      </svg>
      <svg class="theme-icon moon-icon" aria-hidden="true">
        <!-- Moon icon SVG -->
      </svg>
    </button>
  </div>
  ...
</header>
```

**Sun icon SVG:**
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <circle cx="12" cy="12" r="4"></circle>
  <path d="M12 2v2M12 20v2M4.93 4.93l1.41 1.41M17.66 17.66l1.41 1.41M2 12h2M20 12h2M6.34 17.66l-1.41 1.41M19.07 4.93l-1.41 1.41"></path>
</svg>
```

**Moon icon SVG:**
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
</svg>
```

### 2.2 CSS for Theme Toggle
**File: `styles.css`**

**Theme toggle button styles:**
```css
.theme-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border: 1px solid var(--border-color, #e2e7f0);
  border-radius: 8px;
  background: var(--bg-card, #ffffff);
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
}

.theme-toggle:hover {
  border-color: var(--accent-color, #0b5bd3);
  background: var(--bg-card-hover, #f9fafb);
}

.theme-icon {
  width: 20px;
  height: 20px;
  position: absolute;
  transition: opacity 0.2s ease, transform 0.2s ease;
}

/* Show sun in dark mode, moon in light mode */
[data-theme="dark"] .sun-icon {
  opacity: 1;
  transform: rotate(0deg) scale(1);
}

[data-theme="dark"] .moon-icon {
  opacity: 0;
  transform: rotate(90deg) scale(0);
}

[data-theme="light"] .sun-icon,
[data-theme=""] .sun-icon {
  opacity: 0;
  transform: rotate(90deg) scale(0);
}

[data-theme="light"] .moon-icon,
[data-theme=""] .moon-icon {
  opacity: 1;
  transform: rotate(0deg) scale(1);
}
```

**Dark theme CSS variables:**
```css
[data-theme="dark"] {
  --bg-primary: #0a0a0a;
  --bg-secondary: #141414;
  --bg-card: #1a1a1a;
  --bg-card-hover: #242424;
  --text-primary: #e5e5e5;
  --text-secondary: #a0a0a0;
  --text-tertiary: #6a6a6a;
  --border-color: #2a2a2a;
  --border-hover: #3a3a3a;
  --accent-color: #4a9eff;
  --accent-hover: #6bb0ff;
}

[data-theme="light"],
[data-theme=""] {
  --bg-primary: #f9fafb;
  --bg-secondary: #ffffff;
  --bg-card: #ffffff;
  --bg-card-hover: #f5f5f5;
  --text-primary: #1b1b1b;
  --text-secondary: #5b5b5b;
  --text-tertiary: #8a8a8a;
  --border-color: #e2e7f0;
  --border-hover: #d0d5dd;
  --accent-color: #0b5bd3;
  --accent-hover: #1a55d5;
}

body {
  background: var(--bg-primary);
  color: var(--text-primary);
  transition: background-color 0.3s ease, color 0.3s ease;
}
```

### 2.3 JavaScript for Theme Toggle
**File: `theme.js` (new file)**

```javascript
(function() {
  'use strict';

  const themeToggle = document.getElementById('themeToggle');
  const prefersDarkScheme = window.matchMedia('(prefers-color-scheme: dark)');

  // Get saved theme or default to system preference
  function getInitialTheme() {
    const savedTheme = localStorage.getItem('theme');
    if (savedTheme) {
      return savedTheme;
    }
    return prefersDarkScheme.matches ? 'dark' : 'light';
  }

  // Apply theme
  function setTheme(theme) {
    document.documentElement.setAttribute('data-theme', theme);
    localStorage.setItem('theme', theme);
  }

  // Initialize theme
  setTheme(getInitialTheme());

  // Toggle theme on button click
  if (themeToggle) {
    themeToggle.addEventListener('click', () => {
      const currentTheme = document.documentElement.getAttribute('data-theme');
      const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
      setTheme(newTheme);
    });
  }

  // Listen for system theme changes
  prefersDarkScheme.addEventListener('change', (e) => {
    // Only auto-switch if user hasn't manually set a preference
    if (!localStorage.getItem('theme')) {
      setTheme(e.matches ? 'dark' : 'light');
    }
  });
})();
```

**Update `index.html` to include script:**
```html
<script src="theme.js"></script>
```

---

## Part 3: Creating Multiple Alternative Theme Variants

### 3.1 Theme Variant Strategy

Create multiple `index_v*.html` files, each with:
- Different CSS theme file reference
- Same HTML structure
- Different color schemes/styles

### 3.2 Theme Variant Files Structure

**Base structure:**
```
cv/
├── index.html (default)
├── index_v1.html (variant 1)
├── index_v2.html (variant 2)
├── index_v3.html (variant 3)
├── styles.css (default theme)
├── styles_v1.css (variant 1 theme)
├── styles_v2.css (variant 2 theme)
├── styles_v3.css (variant 3 theme)
└── theme.js (shared theme toggle)
```

### 3.3 Variant Themes to Create

#### Variant 1: `index_v1.html` - Minimalist Light
**Theme characteristics:**
- Clean, minimal design
- High contrast
- Subtle shadows
- Blue accent color (#2563eb)
- White background (#ffffff)

**CSS file: `styles_v1.css`**
- Copy base `styles.css`
- Override color variables
- Adjust spacing for minimal aesthetic

#### Variant 2: `index_v2.html` - Warm Dark
**Theme characteristics:**
- Dark background (#0f0f0f)
- Warm accent colors (orange/amber #f59e0b)
- Reduced contrast for eye comfort
- Cozy, warm feeling

**CSS file: `styles_v2.css`**
- Dark theme base
- Warm color palette
- Softer shadows

#### Variant 3: `index_v3.html` - High Contrast
**Theme characteristics:**
- Maximum accessibility
- High contrast ratios (WCAG AAA)
- Clear typography
- Bold colors
- Both light and dark variants

**CSS file: `styles_v3.css`**
- High contrast color scheme
- Larger fonts
- Clear borders

#### Variant 4: `index_v4.html` - Colorful Modern
**Theme characteristics:**
- Vibrant colors
- Gradient backgrounds
- Modern glassmorphism effects
- Purple/pink accent (#8b5cf6)

**CSS file: `styles_v4.css`**
- Gradient backgrounds
- Colorful accents
- Modern effects

### 3.4 Implementation Steps for Each Variant

#### Step 1: Create CSS Variant File
1. Copy `styles.css` to `styles_vN.css`
2. Modify CSS variables section
3. Adjust specific component styles
4. Test color contrast and readability

#### Step 2: Create HTML Variant File
1. Copy `index.html` to `index_vN.html`
2. Update CSS link: `<link rel="stylesheet" href="styles_vN.css" />`
3. Update page title if needed
4. Keep all other structure identical

#### Step 3: Ensure Theme Toggle Works
- Each variant should support light/dark toggle
- Theme toggle should work within the variant's color scheme
- Update dark theme variables in each CSS variant

### 3.5 Example: Creating `index_v1.html`

**File: `index_v1.html`**
```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Luo Yu | Research Scientist - Minimalist</title>
    <link rel="stylesheet" href="styles_v1.css" />
    <!-- ... rest same as index.html ... -->
  </head>
  <body>
    <!-- ... same structure as index.html ... -->
    <script src="theme.js"></script>
  </body>
</html>
```

**File: `styles_v1.css`**
```css
/* Copy all from styles.css, then override: */

:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f8f9fa;
  --bg-card: #ffffff;
  --text-primary: #111827;
  --text-secondary: #4b5563;
  --accent-color: #2563eb;
  --border-color: #e5e7eb;
  /* ... other variables ... */
}

[data-theme="dark"] {
  --bg-primary: #0a0a0a;
  --bg-secondary: #141414;
  --text-primary: #f3f4f6;
  --accent-color: #60a5fa;
  /* ... dark theme overrides ... */
}

/* Minimalist-specific adjustments */
.section {
  padding: 48px 0; /* More spacing */
}

.interest-card {
  border-top: 1px solid var(--border-color);
  padding: 20px 0; /* More padding */
}
```

### 3.6 Testing Checklist for Each Variant

- [ ] Theme toggle works (sun/moon icons switch correctly)
- [ ] All sections display properly
- [ ] Colors have sufficient contrast
- [ ] Smooth scrolling works vertically
- [ ] Navigation links work correctly
- [ ] Responsive design works on mobile
- [ ] Dark mode looks good
- [ ] Light mode looks good
- [ ] Icons and images display correctly
- [ ] No horizontal scrolling issues

---

## Implementation Order

1. **Phase 1: Vertical Layout** (if carousel exists)
   - Remove horizontal carousel HTML/CSS
   - Update JavaScript for vertical scrolling
   - Test smooth scrolling

2. **Phase 2: Theme Toggle**
   - Add toggle button to HTML
   - Create `theme.js`
   - Add CSS for toggle and dark theme
   - Test theme switching

3. **Phase 3: Variant Pages**
   - Create first variant (`index_v1.html` + `styles_v1.css`)
   - Test thoroughly
   - Create remaining variants
   - Document each variant's characteristics

---

## Files to Create/Modify

### New Files:
- `theme.js` - Theme toggle functionality
- `styles_v1.css` - Variant 1 theme
- `styles_v2.css` - Variant 2 theme
- `styles_v3.css` - Variant 3 theme
- `styles_v4.css` - Variant 4 theme (optional)
- `index_v1.html` - Variant 1 page
- `index_v2.html` - Variant 2 page
- `index_v3.html` - Variant 3 page
- `index_v4.html` - Variant 4 page (optional)

### Files to Modify:
- `index.html` - Add theme toggle button, ensure vertical layout
- `styles.css` - Add theme variables, vertical layout styles, theme toggle styles

---

## Notes

- All variants should share the same `theme.js` for consistency
- Theme preference is saved in `localStorage`
- Each variant can have its own color scheme while supporting light/dark toggle
- Smooth scrolling should work identically across all variants
- Consider creating a variant selector page or documentation listing all variants
