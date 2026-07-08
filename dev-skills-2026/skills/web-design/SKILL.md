---
name: web-design
description: Senior-level web/UI design system for building beautiful, professional websites and app interfaces. Use this skill whenever the user asks to design, build, redesign, or improve any website, landing page, dashboard, UI component, or app screen — even if they only say "make it look good", "make a site", "improve the design", or mention colors, fonts, layout, spacing, dark mode, or responsiveness.
---

# Web Design — Senior Designer Standards

Produce interfaces that look like they came from a senior product designer, not a template. Every decision below exists to avoid the "AI-generated look": generic purple gradients, inconsistent spacing, cramped layouts, and unreadable contrast.

## Process (always in this order)

1. Understand purpose and audience before writing any code. A SaaS dashboard, a portfolio, and an e-commerce page need different visual languages.
2. Define design tokens FIRST (colors, type scale, spacing scale, radii, shadows) as CSS custom properties. Never hardcode values inline.
3. Build layout structure, then components, then polish (states, motion, dark mode).
4. Review your own output against the checklist at the bottom before finishing.

## Design tokens

```css
:root {
  /* Color: 1 brand hue, 1 accent, full neutral ramp. Use OKLCH for perceptual uniformity. */
  --brand-600: oklch(55% 0.15 250);
  --accent-500: oklch(70% 0.17 45);
  --gray-50: oklch(98% 0.005 250);  /* ...through gray-950 */
  --surface: var(--gray-50);
  --text-primary: var(--gray-900);
  --text-secondary: var(--gray-600);

  /* Type scale: 1.25 ratio. Body 16px minimum, never smaller than 14px anywhere. */
  --text-sm: 0.875rem; --text-base: 1rem; --text-lg: 1.25rem;
  --text-xl: 1.563rem; --text-2xl: 1.953rem; --text-3xl: 2.441rem;

  /* Spacing: 4px base grid. Only use these values. */
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px;
  --space-6: 24px; --space-8: 32px; --space-12: 48px; --space-16: 64px; --space-24: 96px;

  --radius-sm: 6px; --radius-md: 10px; --radius-lg: 16px;
  --shadow-sm: 0 1px 2px oklch(0% 0 0 / 0.06);
  --shadow-md: 0 4px 12px oklch(0% 0 0 / 0.08);
}
```

## Typography rules

- Max 2 font families. Pair a distinctive display font with a workhorse body font (e.g., a serif or geometric sans for headings + Inter/system-ui for body). Avoid defaulting to Inter-for-everything — it reads as generic.
- Line height: 1.1–1.2 for headings, 1.5–1.7 for body. Line length: 45–75 characters (`max-width: 65ch` on prose).
- Use `text-wrap: balance` on headings, `text-wrap: pretty` on paragraphs.
- Establish hierarchy with size + weight + color together, not size alone.

## Color rules

- 60/30/10: 60% neutral surface, 30% secondary surface/structure, 10% brand/accent.
- All text must pass WCAG AA: 4.5:1 for body, 3:1 for large text. Check, don't guess.
- Never use pure black (#000) on pure white — use gray-900 on gray-50.
- Dark mode: don't just invert. Reduce saturation, lift surfaces with lighter grays (not shadows), lower image brightness slightly. Define both themes via tokens and `color-scheme`.

## Layout rules

- Use CSS Grid for page structure, Flexbox for component internals.
- Generous whitespace is the #1 marker of professional design. Section padding: `--space-16` to `--space-24` vertical. When in doubt, double the spacing.
- Content max-width 1100–1200px, centered. Full-bleed backgrounds with constrained content.
- Mobile-first. Breakpoints at 640/768/1024/1280px. Use `clamp()` for fluid type: `font-size: clamp(2rem, 5vw, 3.5rem)`.
- Vertical rhythm: consistent spacing between sections; related items closer than unrelated ones (proximity principle).

## Components

Every interactive element needs all states: default, hover, focus-visible, active, disabled, loading. Focus states use `outline: 2px solid var(--brand-600); outline-offset: 2px` — never `outline: none` without replacement.

- Buttons: min touch target 44×44px, distinct primary/secondary/ghost hierarchy, only ONE primary per view.
- Cards: subtle border (`1px solid` gray-200) OR shadow, not both heavy. Hover: slight lift (`translateY(-2px)` + shadow-md).
- Forms: labels above inputs (never placeholder-as-label), visible error states with text (not just red borders), 16px input font size (prevents iOS zoom).
- Empty states, loading skeletons, and error states are part of the design — include them.

## Polish details that separate senior from junior work

- Micro-interactions: 150–250ms transitions on hover/focus, `ease-out` for entrances, `ease-in` for exits. Respect `prefers-reduced-motion`.
- Optical alignment beats mathematical alignment (icons often need 1–2px nudges).
- Real content over lorem ipsum — write plausible copy for the domain.
- Consistent icon set (single library, single stroke width). Lucide or Heroicons.
- Images: `aspect-ratio` to prevent layout shift, `object-fit: cover`, subtle `border-radius`.

## Pre-delivery checklist

- [ ] All colors/spacing/type from tokens; no magic numbers
- [ ] Contrast AA verified; focus states visible; keyboard navigable
- [ ] Responsive at 375px, 768px, 1440px without horizontal scroll
- [ ] All interactive states implemented; reduced-motion respected
- [ ] Whitespace generous; single clear visual hierarchy per screen
- [ ] No generic AI-look: no purple-gradient-on-white default, no emoji as icons, no cramped sections
