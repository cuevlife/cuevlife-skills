---
name: anti-ai-slop-ux
description: Use when creating any UI, web page, privacy policy, landing page, dashboard, or visual design — prevents AI-generated slop patterns that look generic and unprofessional
---

# Anti-AI-Slop UX

## Overview

AI-generated UI has a recognizable "smell" — gradient backgrounds, excessive shadows, glassmorphism, badge overload, and decorative noise that adds no value. Users and app stores can spot it instantly. This skill enforces professional, content-first design.

## The Core Rule

**Content is the interface. Decoration is noise.**

If a visual element doesn't help the user understand or act on content, remove it.

## Red Flags — STOP and Redesign

- Purple/blue gradient backgrounds
- Cards with shadows on every element
- Glassmorphism / frosted glass effects
- Emoji or icon overload
- Badge chips for every label
- Animated transitions on everything
- Decorative illustrations that add no meaning
- Border-radius > 12px on everything
- "Modern" look that's instantly recognizable as AI

## The Anti-Slop Checklist

Before shipping any UI, verify:

| Element | AI Slop | Professional |
|---------|---------|--------------|
| Background | Gradient (purple/blue) | White or neutral solid |
| Cards | Shadow on every card | Border or subtle background difference |
| Colors | 5+ accent colors | 1-2 accent colors max |
| Typography | Decorative fonts | System fonts (SF Pro, Segoe, Roboto) |
| Borders | Rounded everything (20px+) | Consistent radius (4-8px) |
| Icons | Emoji everywhere | Minimal, functional icons |
| Spacing | Uniform padding on everything | Content-driven spacing |
| Animation | Entrance animations on load | Purposeful transitions only |
| Layout | Centered single column with cards | Appropriate layout for content type |

## Design Principles

### 1. Content-First

Start with the content. Design serves content, not the other way around.

**Bad:** Beautiful gradient → content placed on top
**Good:** Content laid out → minimal decoration added where needed

### 2. Platform-Native Patterns

Follow the platform's design language:
- **Android:** Material Design 3 — use M3 components, elevation, typography scale
- **iOS:** Human Interface Guidelines — use SF symbols, native navigation
- **Web:** Follow established patterns (navigation top, content below)

Don't fight the platform. Users expect familiar patterns.

### 3. Typography as Design

Good typography eliminates need for decoration:
- Use system font stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`
- Limit to 2-3 sizes max
- Weight contrast (bold vs regular) > color contrast
- Line height 1.5-1.7 for body text

### 4. Functional Color

Color should communicate, not decorate:
- **1 accent color** for CTAs and key actions
- **Gray scale** for everything else
- **Red** only for errors/destructive actions
- **Green** only for success/confirmation
- Never use color just to "make it pretty"

### 5. Progressive Disclosure

Show only what's needed now:
- Collapsible sections for secondary content
- "See more" for long text
- Tabs for related but separate content
- Avoid cramming everything visible

## Pattern: Privacy Policy / Legal Pages

Reference: Mozilla, Google, Apple privacy policies

```html
<!-- Good: Clean, minimal, content-focused -->
<body style="font-family: -apple-system, sans-serif; max-width: 720px; margin: 0 auto; padding: 40px 20px;">
  <h1>Privacy Policy</h1>
  <p class="date">Effective date: ...</p>
  
  <h2>Section Title</h2>
  <p>Content here.</p>
  
  <h2>Another Section</h2>
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
</body>
```

**What to avoid:**
- Gradient backgrounds
- Card containers around sections
- Icons next to headings
- "Summary" boxes with colored backgrounds
- Animated section reveals
- Social media badges
- Newsletter signup forms

## Pattern: Mobile App Screens

Reference: Google Play Store, Apple App Store app screenshots

**Do:**
- Show actual app content
- Use real device frames (optional)
- Minimal text overlay
- Consistent spacing between screens

**Don't:**
- Gradient overlays on screenshots
- Floating 3D elements
- Decorative particles or shapes
- Excessive text labels
- "Feature" badges on each screen

## Pattern: Landing Pages / Marketing

Reference: Stripe, Linear, Vercel landing pages

**Do:**
- Hero section with clear value proposition
- Feature comparison table
- Real product screenshots
- Customer logos (if available)
- Clear CTA buttons

**Don't:**
- Animated background particles
- Floating gradient orbs
- Testimonial cards with avatars from random people
- "Trusted by 10,000+ users" (if not true)
- Feature grid with icons in circles

## Pattern: Settings / Configuration UI

**Do:**
- Group related settings
- Use native platform controls (toggles, dropdowns)
- Show current value clearly
- Minimal decoration

**Don't:**
- Card around every setting group
- Custom toggle animations
- Gradient header bars
- Icon badges for each setting
- Tooltip overload

## Quick Reference: Color Palettes That Work

### Professional Neutral
```
Background: #ffffff
Text: #111111
Muted: #666666
Border: #e0e0e0
Accent: #1a73e8 (blue)
```

### Dark Professional
```
Background: #0f0f0f
Text: #ffffff
Muted: #888888
Border: #333333
Accent: #4a9eff (blue)
```

### Warm Professional
```
Background: #fafafa
Text: #1a1a1a
Muted: #666666
Border: #e8e8e8
Accent: #2563eb (blue)
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| "Make it modern" | Remove decorations, keep content |
| "Add some color" | Use 1 accent color, gray scale for rest |
| "Make it pop" | Improve typography hierarchy |
| "Add animations" | Remove all non-essential animations |
| "It looks boring" | Boring = professional. Content should be interesting. |
| "Add gradients" | Remove all gradients. Use solid colors. |
| "Add shadows" | Remove all shadows. Use borders or background difference. |

## Verification

After creating any UI, ask:
1. Can I remove this element without losing meaning? → Remove it
2. Does this color communicate something? → If not, make it gray
3. Is this animation necessary for understanding? → If not, remove it
4. Would a human designer add this? → If not, remove it
5. Can I spot this as AI-generated? → Redesign until no
