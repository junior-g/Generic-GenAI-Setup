<!--
TEMPLATE — copy to 15-design-system.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: FILE-MATCH on the UI globs in config.yml → paths.file_match.ui.

Generate this ONLY if the project has a user interface. A CLI, a library, or a headless service does not get
one — record the omission in README.md rather than filling this in speculatively.

Evidence rule: tokens, class names and component paths are read from the real stylesheet and the real
components. A token you assumed exists because the design looks like it should have one is F5, and the
resulting code silently falls back to a browser default.

Applies to any UI technology — web, native, terminal, or desktop. Where a section does not apply to the
project's platform, write "not applicable" and say why.

Keep it under ~150 lines.
-->

# Design System — <project name>

## Rendering / composition model

| | |
|---|---|
| Default component kind | <e.g. server-rendered, static, stateless — the default the project prefers> |
| When to opt into the interactive kind | <state, effects, event handlers, platform APIs — and nothing else> |
| Island / boundary discipline | <keep interactive units small and leaf-level, or the project's equivalent> |
| Data access from components | <what a component may and may not reach> |

The point of this section: a component that opts into the heavier kind for no reason ships cost to every
user, forever, and nobody notices because it works.

## Design tokens — reference these, never raw values

Read from the real token source. Every row cites where it is defined.

| Category | Tokens | Defined in |
|----------|--------|-----------|
| Text colour | | `path:line` |
| Brand colour | | |
| Surfaces | | |
| Accent | | |
| State (success / warning / danger) | | |
| Spacing scale | | |
| Radius | | |
| Elevation / shadow | | |
| Typography | | |
| Layout limits | | |
| Motion / duration | | |

**Never a raw hex, pixel, or font name in a component.** A raw value cannot be themed, cannot be audited,
and will not change when the token does.

## Layout scaffold

Shared layout primitives that already exist. Reuse; do not redefine.

| Class / component | Does | Breakpoint behaviour | Defined in |
|-------------------|------|---------------------|-----------|
| | | | `path:line` |

## Component inventory

The shared components a new surface should reach for before writing anything.

| Component | Use for | Props of note | Path |
|-----------|---------|---------------|------|
| | | | |

| | |
|---|---|
| When to add a new shared component | <the threshold — e.g. third usage> |
| Where new shared components go | `path` |
| Where one-off components go | `path` |

## Brand feel

What the visuals should evoke, so choices are consistent rather than ad hoc.

| | |
|---|---|
| Emotion | <e.g. trustworthy, calm, practical. And what to avoid> |
| Colour meaning | <what each brand colour signals, and where it is allowed> |
| Line and shape | <weight, radius, density> |
| Copy voice | <concise / formal / plain. And what to avoid> |
| One-theme rule | Every new element must sit beside the existing ones without looking foreign |

## Iconography

| | |
|---|---|
| Format | <vector only, or the project's rule> |
| Where components live | `path` |
| Where sources live | `path` |
| Style contract | <viewBox, stroke width, caps, fill, colour inheritance> |
| Sizing | <via style, never hardcoded raster dimensions> |
| Accessibility | <decorative icons hidden from assistive tech; the interactive parent carries the label> |
| Registry | `path` — every custom icon logged with its rationale |

## Images and media

| | |
|---|---|
| Image component | <the required wrapper, never the raw platform element> |
| External sources | <where allowed hosts are configured> |
| Aspect ratio convention | <e.g. content photos in a dedicated fixed-ratio area, not squeezed into an icon slot> |
| Fallback when absent | <the placeholder, and that it is deliberate> |

## Responsive and platform rules

| | |
|---|---|
| Approach | <mobile-first, or the project's> |
| Breakpoints | |
| Gutters | |
| Minimum interactive target | <a real number> |
| Zero horizontal overflow at | <the narrowest supported width> |

**Overlays must not overflow the viewport.** Dropdowns, popovers, menus and panels near a screen edge
overflow when positioned only by an offset and a fixed width. Constrain them on both sides at the narrow
breakpoint, and test at the narrowest supported width.

## Accessibility floor

| # | Requirement |
|---|-------------|
| 1 | Interactive controls are real focusable elements with accessible labels |
| 2 | A visible focus indicator on every interactive element |
| 3 | Contrast meets <the project's stated standard> |
| 4 | Decorative content is hidden from assistive technology |
| 5 | Keyboard reachable and operable, in a sensible order |
| 6 | Form controls have associated labels and programmatic error association |
| 7 | Motion respects the reduced-motion preference |

Full conformance needs manual testing with assistive technology and expert review. This is a floor, not a
certification.

## One-theme view — tags matter, not only styles

Two controls that share a visual style but use different underlying elements will **not** render identically
from shared styling alone. Each element type carries its own defaults for appearance, line height and font
inheritance.

So when one visual style is applied across element types, the style must **fully reset the differences**:
appearance, font inheritance, an explicit line height, box sizing, text decoration, and explicit centring.

Keep the semantic element correct — navigation is a link, an action is a button — and make them *look*
identical via the reset. Validating this means checking the **rendered element tags**, not only the
stylesheet.

## How UI styling gets validated

Three layers, because each catches what the others cannot.

| Layer | Catches | What cannot |
|-------|---------|-------------|
| **1. Live preview** — <route or command, and how to run it> | actual pixels, spacing, real stylesheet | nothing automated |
| **2. Render tests** — <path> | element tags, structure, class presence, prop handling | it cannot see styling at all — a class name being present does not prove the rule loaded |
| **3. Style contract tests** — <path> | source-level assertions a render cannot make: the kit is imported, the reset properties exist, no hardcoded raw values | whether it looks right |

**A test environment that renders markup usually applies no stylesheet.** It proves a class *name* is
present, never that the rule is loaded or correct. That is why layers 1 and 3 exist.

A component using shared classes must ensure the stylesheet is actually loaded on that route. Referencing a
class from a surface that never imports the kit produces silently unstyled output.

## Don't

| Don't | Do instead |
|-------|-----------|
| | |
