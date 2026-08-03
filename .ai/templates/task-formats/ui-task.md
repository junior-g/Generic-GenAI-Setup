<!--
FORMAT — UI task. Copy the checklist blocks into the task's block in task_details.md.
Adds discipline-specific checks on top of the common blocks. Does not replace them.

Applies to any UI platform — web, native, desktop, terminal. Project specifics (which tokens, which component
library, which test harness) come from rules/project/15-design-system.md.

THE TWO THINGS UI TASKS FAIL ON MOST:
  1. States other than the happy path. Empty and error states ship broken because the populated state is what
     gets demonstrated.
  2. Styling that is referenced but never loaded. A class name present in the markup with no rule behind it
     renders as a browser default and passes every automated test, because a test environment that renders
     markup usually applies no stylesheet.
-->

# UI Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| Screens, pages, views, components, styling, layout, client-side interaction, accessibility | Data fetching logic (backend format), schema (database format) |

## 1. Surface and states

Every state, not just the one in the design mockup.

| State | Defined? | What the user sees | Where |
|-------|----------|-------------------|-------|
| Loading / pending | ☐ | | |
| Empty — no data yet | ☐ | | |
| Empty — filtered to nothing | ☐ | | |
| Populated | ☐ | | |
| Partial — some data failed | ☐ | | |
| Error | ☐ | | |
| Unauthorised | ☐ | | |
| Offline / disconnected | ☐ | | |
| Submitting / disabled | ☐ | | |
| Success confirmation | ☐ | | |

"Empty — filtered to nothing" and "Empty — no data yet" are different messages. Treating them as one is a
small thing users notice immediately.

## 2. Composition and boundary

| # | Check | Result |
|---|-------|--------|
| 1 | Component is the project's default kind unless it needs state, effects, event handlers, or platform APIs | |
| 2 | If it opted into the heavier kind — the specific reason | |
| 3 | Interactive units kept small and leaf-level, not wrapping static content | |
| 4 | No data access from a layer that should not reach it | |
| 5 | Derived values computed where used, not stored and synchronised | |
| 6 | No secret or server-only value reachable from the client bundle | |

Check 6 is a security check, not a style one. A value that reaches the client is public regardless of how it
got there.

## 3. Styling

| # | Check | Result |
|---|-------|--------|
| 1 | **Design tokens used — no raw colour, size, spacing, or font value** | |
| 2 | Existing layout primitives reused, not redefined | |
| 3 | Existing shared components reused where one fits | |
| 4 | **The stylesheet/kit this surface needs is actually imported on this route** | |
| 5 | New styles co-located per the project's convention | |
| 6 | No style override reaching into another component's internals | |

Check 4 is the one that silently fails. A class referenced from a surface whose route never loads the kit
renders unstyled, and no test catches it — see
[`../../rules/project/15-design-system.template.md`](../../rules/project/15-design-system.template.md).

### Element-type parity

Only when one visual style is applied across different underlying element types — a link styled as a button, a
label styled as an input.

| # | Check | Result |
|---|-------|--------|
| 1 | The shared class resets appearance, font inheritance, line height, box sizing, text decoration | |
| 2 | The semantic element is still correct — navigation is a link, an action is a button | |
| 3 | **Rendered output compared across both element types**, not just the stylesheet | |

Two controls sharing a class do **not** render identically from styling alone. Each element type carries its
own defaults. Verifying this means inspecting rendered tags, not reading CSS.

## 4. Responsive and layout

| Width / context | Overflow | Targets | Overlays on screen | Result |
|-----------------|----------|---------|--------------------|--------|
| narrowest supported | ☐ none | ☐ ≥ minimum | ☐ | |
| mid | | | | |
| wide | | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Zero horizontal overflow at the narrowest supported width | |
| 2 | Interactive targets meet the project's minimum size | |
| 3 | **Dropdowns, popovers, menus and panels stay within the viewport near a screen edge** | |
| 4 | Long content, long words and long names do not break the layout | |
| 5 | Content reflows rather than requiring horizontal scroll | |

Check 3 is a specific, repeated failure: an overlay positioned by an offset plus a fixed width overflows when
its trigger is near an edge. Constrain both sides at the narrow breakpoint and test at the narrowest width.

## 5. Accessibility

| # | Check | Result |
|---|-------|--------|
| 1 | Interactive elements are real focusable controls, not styled containers with handlers | |
| 2 | Every control has an accessible label | |
| 3 | Visible focus indicator on every interactive element | |
| 4 | Focus order matches visual order | |
| 5 | Keyboard reachable and operable — including dismissing overlays | |
| 6 | Contrast meets the project's stated standard | |
| 7 | Decorative content hidden from assistive technology | |
| 8 | Form errors programmatically associated with their fields | |
| 9 | Status changes announced, not only shown | |
| 10 | Reduced-motion preference respected | |
| 11 | Content readable and functional at increased text size | |

Full conformance requires manual testing with assistive technology and expert review. This is a floor.

## 6. Content and localisation

| # | Check | Result |
|---|-------|--------|
| 1 | No hardcoded user-facing string — all from the catalogue | |
| 2 | Every new key present in every supported locale | |
| 3 | Layout holds for the longest translation | |
| 4 | Dates, numbers, currency formatted per locale | |
| 5 | Text direction handled, if the project supports it | |
| 6 | No invented content — figures come from real data or constants | |

## 7. Images and media

| # | Check | Result |
|---|-------|--------|
| 1 | The project's image component used, not the raw platform element | |
| 2 | Dimensions or aspect ratio set — no layout shift on load | |
| 3 | Alternative text present, or explicitly marked decorative | |
| 4 | External sources registered where the project requires it | |
| 5 | Fallback when the asset is missing | |
| 6 | Content photos in a proper media area, not squeezed into an icon slot | |

## 8. Verification 🔴

**Three layers, because each catches what the others cannot.**

| Layer | What it proves | What it cannot |
|-------|----------------|----------------|
| **1. Visual** — the real stylesheet, real rendering | actual appearance, spacing, overflow | nothing automated |
| **2. Render test** | element tags, structure, class presence, props, states | **it cannot see styling at all** |
| **3. Style contract test** | source-level: the kit is imported, reset properties exist, no raw values | whether it looks right |

| # | Check | Result |
|---|-------|--------|
| 1 | Rendered visually, at the narrowest width and one wide width | |
| 2 | Render test covers every state from §1 | |
| 3 | Element-type parity asserted on rendered output, if §3 applies | |
| 4 | Keyboard-only pass done | |
| 5 | Each state screenshot or described in the manual test record | |

**A render test proving a class name is present does not prove the style applied.** That is why layer 1 exists
and cannot be skipped for anything visual.

## 9. Ripple effects

| Affected | Path | How | Handled by |
|----------|------|-----|-----------|
| | `path:line` | | |

Check specifically: other usages of a modified shared component · surfaces importing the changed stylesheet ·
snapshot tests · localisation keys removed or renamed · layout primitives whose behaviour changed · anything
depending on this component's rendered structure.

## 10. Done when

| # | Criterion |
|---|-----------|
| 1 | Every state from §1 implemented and rendered |
| 2 | Tokens only — no raw values |
| 3 | The needed stylesheet is loaded on this route |
| 4 | Narrowest-width pass: no overflow, targets adequate, overlays contained |
| 5 | Accessibility floor met |
| 6 | Locale parity complete |
| 7 | All three verification layers done |
| 8 | Ripple effects handled |
| 9 | All four gates green |
| 10 | No follow-up open |
