# Design System & Branding Guide

Complete design reference for maintaining brand consistency.

---

## Color Palette

### Primary Colors
```css
--primary: #3b82f6;        /* Blue - Primary CTA */
--primary-dark: #2563eb;   /* Dark Blue - Hover states */
--secondary: #475569;      /* Slate - Headings */
```

### OpenClaw Brand Colors
```css
Coral: #C85A4A            /* Gradient start */
Mauve: #9A8080            /* Gradient middle */
Teal: #6DC8B8             /* Gradient end */

/* Gradient usage */
background: linear-gradient(90deg, #6DC8B8 0%, #9A8080 50%, #C85A4A 100%);
```

### AWS Branding
```css
Orange: #FF9900           /* AWS brand color */
```

### Neutral Colors
```css
--text: #374151;          /* Body text */
--text-muted: #6b7280;    /* Secondary text */
--border: #e5e7eb;        /* Borders */
--surface: #fafbfc;       /* Light backgrounds */
```

### Status Colors
```css
--success: #10b981;       /* Green - Success states */
--warning: #f59e0b;       /* Amber - Warnings */
--danger: #ef4444;        /* Red - Errors */
```

---

## Typography

### Font Stack
```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Inter', sans-serif;
```

**Why system fonts?**
- Fast loading (no web font download)
- Native feel per platform
- Excellent readability

### Hierarchy
```css
h1: 3rem (48px), weight 700    /* Hero */
h2: 2.5rem (40px), weight 700  /* Section headings */
h3: 1.5rem (24px), weight 600  /* Subsections */
h4: 1.2rem (19px), weight 600  /* Cards */
p: 1rem (16px), weight 400     /* Body */
```

### Mobile Scaling
```css
@media (max-width: 768px) {
    h1: 2rem (32px)
    h2: 1.8rem (29px)
    h3: 1.25rem (20px)
}
```

---

## Spacing System

### Scale (rem units)
```
0.25rem = 4px
0.5rem = 8px
0.75rem = 12px
1rem = 16px
1.5rem = 24px
2rem = 32px
2.5rem = 40px
3rem = 48px
4rem = 64px
5rem = 80px
```

### Section Padding
```css
Desktop: 2.5-3rem top/bottom
Mobile: 2rem top/bottom
```

### Component Spacing
```css
Gap between cards: 2rem
Margin between sections: 3rem
Button padding: 1.1rem 2.75rem
```

---

## Components

### Buttons

**Primary CTA:**
```css
.cta {
    background: white;
    color: var(--primary-dark);
    padding: 1.1rem 2.75rem;
    border-radius: 12px;
    font-weight: 700;
    font-size: 1.05rem;
    transition: all 0.3s ease;
}
```

**Secondary CTA:**
```css
.cta-secondary {
    background: transparent;
    border: 2.5px solid white;
    color: white;
}
```

### Cards

**Standard card:**
```css
.card {
    background: white;
    padding: 2rem;
    border-radius: 16px;
    box-shadow: var(--shadow-md);
    border: 1px solid var(--border);
    transition: all 0.3s ease;
}

.card:hover {
    transform: translateY(-4px);
    box-shadow: var(--shadow-lg);
}
```

### Glassmorphism

**OpenClaw logo container:**
```css
.openclaw-logo-container {
    padding: 0.75rem 1.5rem;
    background: rgba(0, 0, 0, 0.4);
    border-radius: 16px;
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
}
```

---

## Shadows

```css
--shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
--shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
--shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
--shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
```

---

## Gradients

### Hero Background
```css
background: linear-gradient(135deg, #3b82f6 0%, #2563eb 50%, #1d4ed8 100%);
```

### OpenClaw Brand
```css
background: linear-gradient(90deg, #6DC8B8 0%, #9A8080 50%, #C85A4A 100%);
-webkit-background-clip: text;
-webkit-text-fill-color: transparent;
```

### Subtle Backgrounds
```css
background: linear-gradient(135deg, #ffffff 0%, #f8fafc 100%);
background: linear-gradient(135deg, #f8fafc 0%, #e0f2fe 100%);
```

---

## Brand Assets

### Logo Usage
- **File:** openclaw-logo.webp
- **Size:** 80px height (desktop), 60px (mobile)
- **Container:** Dark glassmorphism box
- **Placement:** Top left of hero

### Professional Photo
- **File:** phil.jpg
- **Size:** 200x200px (desktop), 150x150px (mobile)
- **Shape:** Circle with white border
- **Position:** Right side of hero (desktop), center (mobile)

### Screenshots
- **Border-radius:** 16px
- **Shadow:** var(--shadow-lg)
- **Max-width:** 600px (desktop), 100% (mobile)

---

## Iconography

### Emoji Icons
```
🔒 Privacy
📱 Mobile
💰 Cost savings
⚡ Speed/Automation
🛠️ Technical
☁️ Cloud
🧠 AI
💬 Messaging
```

**Size:** 4rem (value props), 3rem (architecture)

### SVG Logos

**Telegram:**
- Blue gradient (#2AABEE → #229ED9)
- 48x48px in architecture diagram

**AWS:**
- Orange text (#FF9900)
- Bold weight (700)

---

## Mobile Adaptations

### Breakpoints
```css
Desktop: >768px
Tablet: 768px
Mobile: <480px
```

### Changes
- Stack hero (flex-direction: column)
- Reduce font sizes (h1: 3rem → 2rem)
- Full-width CTAs
- Center alignment
- Larger touch targets (44px minimum)

---

## Animation

### Transitions
```css
transition: all 0.3s ease;
```

**What animates:**
- Hover states (scale, shadow, position)
- Button interactions
- Card lifts

**What doesn't animate:**
- Initial page load (for speed)
- Section entries (no scroll animations)

### Hover Effects

**Cards:**
```css
transform: translateY(-4px);
box-shadow: var(--shadow-lg);
```

**Buttons:**
```css
transform: translateY(-3px);
box-shadow: 0 6px 20px rgba(0,0,0,0.25);
```

---

## Accessibility

### Contrast
- All text passes WCAG AA
- Primary blue (#3b82f6) on white: 4.5:1
- White on primary blue: 7.1:1

### Focus States
```css
:focus-visible {
    outline: 2px solid var(--primary);
    outline-offset: 2px;
}
```

### Alt Text
- All images have descriptive alt text
- Decorative images: `alt=""`

---

## Print Style

```css
@media print {
    .hero, .footer-cta, .scroll-to-guide {
        display: none;
    }
    
    body {
        color: black;
        background: white;
    }
}
```

---

Last updated: 2026-02-13
