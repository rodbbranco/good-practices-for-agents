---
name: polished-ui
description: Use whenever building, restyling, or reviewing any user interface (landing pages, app screens, components, dashboards, settings pages, modals) in HTML/CSS, React, Tailwind, SwiftUI, or similar. Applies seven principles that separate premium-looking UI from generic "AI-generated" UI — focal point, spacing scale, typographic hierarchy, restrained color, real content, consistent details, and purposeful motion — and runs a 7-question audit before finishing.
---

# Polished UI

Generic AI-built UI has a recognizable look: gradient hero, badge, two equal buttons, a stat bar, three identical feature cards, purple everywhere, lorem ipsum, heavy shadows, and things that fade in from nowhere. Premium UI often uses the same components — the difference is these seven things. Apply all of them.

## 1. One clear focal point
- Before writing anything, answer: **what is the one thing this screen is for?** If the user hasn't said, infer it from context or ask.
- Make that one element the biggest, highest-contrast thing on the screen. Turn everything else down.
- Exactly **one primary button** per view. Other actions become secondary buttons or text links.
- Prefer removing elements over adding them. Cut decorative badges, stat bars, and filler sections unless they serve the screen's purpose.
- In product UI, put the action the user came for at the top. Don't give every section equal weight.

## 2. Spacing on a scale
- Use a 4px/8px scale only. Default steps: `4, 8, 12, 16, 24, 32, 48, 64, 96`. No arbitrary values (e.g. 13px, 22px, 37px).
- Spacing communicates grouping (proximity):
  - A label sits close to its input (4–8px).
  - Items within a group are closer than groups are to each other.
  - Sections are separated by clearly larger gaps than anything inside them.
- Let spacing do the grouping work before reaching for borders, dividers, or boxes.
- Never apply the same padding to every element — uniform spacing makes nothing feel grouped.
- If a design system or tokens exist in the project, use those instead.

## 3. Typography: hierarchy from weight and color, not size
- One font family (two at most). Prefer the project's existing font.
- Build hierarchy mainly with **weight and color**. A title can be only slightly larger than body text but darker and heavier.
- Secondary/supporting text uses a softer gray, not a smaller size alone.
- Slightly tighten letter-spacing on headings (e.g. `-0.01em` to `-0.02em`).
- Keep line length readable (~60–75 characters, `max-width: 65ch` for prose).
- Limit the number of type sizes — no more than 2–3 on a single card or component. Five sizes on one card is a red flag.

## 4. Restrained color
- Most of the screen is neutrals. Tint the grays slightly toward the brand hue so they feel like part of the product.
- **One accent color**, used only where it matters: the primary button, focus states, and links (maybe selected states).
- No gradients on backgrounds, headlines, buttons, and badges all at once. Default to no gradient.
- If everything is colored, nothing stands out — when in doubt, remove color.

## 5. Real content
- No "John Doe", "Jane Doe", "Lorem ipsum", "Feature One", "Acme", or "$10/$20/$30".
- Use the actual product name, real feature descriptions, real plan names, plausible specific prices and numbers, and a real (or realistic, specific) customer quote.
- Write copy for this specific product — features should say what the user actually gets.
- If real content isn't available, ask the user for it. If you must invent, make it specific and believable and flag it as placeholder to replace.

## 6. Details that hold up up close
- **Radius:** one consistent radius scale across the app. Nested elements get a smaller radius than their container (inner radius ≈ outer radius − padding) so curves line up.
- **Borders:** 1px and subtle (low-contrast, e.g. a neutral at ~8–12% opacity). Never dark, heavy borders by default.
- **Shadows:** soft and layered (two or more stacked low-opacity shadows), not one big dark blur. Often barely visible.
- **Icons:** one icon set, same size and stroke width everywhere, optically aligned with adjacent text.
- **Alignment:** everything snaps to the same edges and grid.

## 7. Motion connected to the user's action
- Things animate **from where the user acted**: a dropdown/menu grows from its trigger (set `transform-origin` to the trigger), not from screen center.
- Open slightly slower than close — closing should get out of the way fast (e.g. open ~200ms, close ~150ms).
- Keep animations short and use ease-out curves. No long, bouncy, or linear transitions by default.
- Notifications/toasts slide in from an edge and stack on previous ones.
- Sheets and draggable elements follow the pointer/finger and ease into their resting position.
- Avoid both extremes: no motion at all (snapping) and the same generic fade on everything.
- Respect `prefers-reduced-motion`.

## Workflow when fixing an existing UI
Apply the fixes in this order, one pass each:
1. **Focal point** — state what the page is for; keep one primary button, demote the rest (second CTA → text link), remove competing elements like stat bars.
2. **Spacing** — move every gap onto the 8px scale; tighten related items, separate sections.
3. **Typography** — one font, heavier-but-smaller headings, gray supporting text.
4. **Color** — remove gradients, switch to tinted neutrals, keep brand color only on the primary button and links.
5. **Content** — replace every placeholder with real product name, features, numbers, and quotes.
6. **Details** — match radius everywhere, 1px subtle borders, softer layered shadows, align to shared edges, unify icons.
7. **Motion** — menus grow from their buttons, notifications stack, every animation short with ease-out.

Keep components and layout intact unless a principle requires changing them.

## Final audit (run before finishing)
Answer each question. If any is "no", fix it before handing back.
1. What's the one thing this screen is for — and is it the most obvious thing on it?
2. Is every gap on a standard spacing scale, and does spacing show grouping?
3. Does hierarchy come from weight and color, not just size?
4. Is the accent color used only where it matters?
5. Is every piece of content real (no placeholders)?
6. Do radius, borders, and shadows match everywhere?
7. Does every animation come from where the user acted?

Briefly report the audit results to the user, noting anything that needs their input (usually real content).
