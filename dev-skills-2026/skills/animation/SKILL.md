---
name: animation
description: Professional web/app animation and micro-interactions — CSS animations, View Transitions, scroll-driven effects, Motion (Framer Motion), GSAP, and Lottie. Use this skill whenever the user asks for animations, transitions, hover effects, loading states, page transitions, scroll effects, "make it feel alive/smooth/polished", or any motion design — and proactively add tasteful micro-interactions when building UI.
---

# Animation — Motion That Feels Professional

Great animation is felt, not noticed. It communicates state, guides attention, and adds personality — without slowing anyone down. Bad animation (slow, bouncy-everywhere, janky) is worse than none.

## Principles

- **Purpose first**: every animation should answer "what does this communicate?" (feedback, orientation, hierarchy, delight). Decoration-only motion is used sparingly.
- **Fast**: micro-interactions 100–250ms; element transitions 200–350ms; page/layout transitions 300–500ms. Anything > 500ms needs justification. Users should never wait for an animation.
- **Natural easing**: `ease-out` for entrances (fast start, gentle stop), `ease-in` for exits, `ease-in-out` for moves. Custom curve default: `cubic-bezier(0.22, 1, 0.36, 1)`. Springs (Motion's default) for interactive/draggable elements.
- **Performance**: animate only `transform` and `opacity` (compositor-only, 60/120fps). Never animate width/height/top/left/margin — use `transform: scale/translate`. `will-change` only during animation, removed after.
- **Accessibility**: always respect reduced motion:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}
```

(Or in Motion: `useReducedMotion()` → swap movement for opacity fades.)

## Tool selection

- **CSS transitions/keyframes**: hover states, loading spinners, simple entrances. Zero JS cost — default choice.
- **View Transitions API**: page navigations and shared-element transitions (natively supported in modern browsers, incl. cross-document for MPAs). Use `view-transition-name` on the shared element.
- **CSS scroll-driven animations** (`animation-timeline: view()/scroll()`): scroll progress bars, reveal-on-scroll, parallax — no JS, runs off main thread.
- **Motion (framer-motion) / Motion One**: React component animations, layout animations (`layout` prop), presence (`AnimatePresence` for exit animations), gestures, springs.
- **GSAP**: complex timelines, SVG animation, ScrollTrigger for scroll choreography (now 100% free including plugins).
- **Lottie/Rive**: designer-authored vector animations (icons, empty states, onboarding). Rive for interactive/state-machine animation at smaller file sizes.

## Standard patterns (with defaults)

**Micro-interactions**: buttons — hover `translateY(-1px)` + shadow, active `scale(0.97)`, 150ms. Inputs — border-color + subtle ring on focus, 150ms. Toggle/checkbox — 200ms ease-out with a small overshoot.

**Entrances**: fade + rise 12–20px, 250–350ms ease-out. **Stagger** lists/grids at 40–70ms per item (cap total ≤ 600ms — stagger the first ~8, then batch).

```jsx
// Motion stagger
const list = { visible: { transition: { staggerChildren: 0.06 } } };
const item = { hidden: { opacity: 0, y: 16 }, visible: { opacity: 1, y: 0, transition: { duration: 0.3, ease: [0.22, 1, 0.36, 1] } } };
```

**Loading**: skeletons (shimmer via translating gradient) over spinners for content; spinners for actions; button → inline spinner + disabled while pending. If response < 300ms, show nothing (flash is worse).

**Modals/sheets**: overlay fade 200ms; panel `scale(0.96→1)` + fade (modal) or `translateY(100%→0)` (sheet), 250–300ms ease-out; exit faster (150–200ms ease-in). Always animate exit too — `AnimatePresence` in React.

**Scroll reveal**: IntersectionObserver or CSS `animation-timeline: view()`; trigger at ~15% visibility; animate once, don't replay on scroll-up; keep movement ≤ 24px.

**Page transitions**: View Transitions API with 200–300ms crossfade + slight shared-element morph. Keep it subtle — users navigate constantly.

**Hero/attention moments** (one per page max): can be richer — clip-path text reveals, springy blobs, staggered word entrances (SplitText), parallax layers ≤ 10% offset.

## Quality checklist

- [ ] Only transform/opacity animated; no layout thrash; steady frame rate
- [ ] Durations within ranges above; exits faster than entrances
- [ ] Reduced-motion handled; nothing flashes more than 3×/sec
- [ ] Interruptible: animations don't block input; spam-clicking doesn't queue/break state
- [ ] Consistent motion language: same easings/durations via design tokens (`--ease-out-quart`, `--duration-fast: 150ms`)
- [ ] Restraint: if everything moves, nothing means anything
