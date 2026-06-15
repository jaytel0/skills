---
name: baby-ui
description: The baby UI prototyping style for this project — thesis, visual spec, and SwiftUI conventions. Load before designing, building, or reviewing any prototype in baby-ui/. Covers greeked placeholder interfaces (skeleton bars, empty blocks, dot navigation), the pale-canvas card style, the single-accent rule, and typography rules.
---

# Baby UI

## Thesis — what "baby UI" is

Baby UI is a deliberate abstraction level for prototyping. There is no real
text and no real content: the interface is built from placeholder-looking UI —
skeleton bars, empty blocks, dots — so that **interaction and spatial models
get nailed first**, before designing real UI and adding complexity.

The rule: **greek the data, never the structure.** Layout, hierarchy, spacing,
radius, shadows, motion, and the single accent color are real and fully
polished. Only the content collapses into placeholders. A baby UI screen should
read as the finished product seen from far away — a calm, finished miniature —
never as a wireframe or a loading skeleton.

When given a mock or reference in another style, follow its *structure* only;
always design the result in this style.

## The style

Derived from the reference images (a sectioned card list, a three-state guest
lifecycle, a full app frame). Reproduce it like this:

**Canvas & surfaces**
- Pale off-white canvas. White rounded cards float on it with large radii
  (~24–44 pt) and soft, broad, low-opacity shadows — no borders, no hard
  edges.
- One dominant focal mass per screen; generous padding and big vertical gaps.
  Leave empty space empty.

**Composition & hierarchy**
- Build hierarchy in this order: surface scale → spacing/blank area → value
  contrast → type size and weight → small accent signals. Never color or
  icons as the main hierarchy system.
- One dominant module per screen; supporting modules are smaller, quieter,
  and lower contrast. Never make every card equally prominent.
- Repeated modules (card grids, lists) keep identical width, radius, padding,
  and alignment — variation between them is limited to small state details
  (one badge, one accent ring, one bar count).
- One radius language per screen: cards, pills, badges, and blocks share the
  same rounding logic. Never mix near-square corners with pills.
- Clear axis per zone: centered for hero stacks (greeting + input), left-
  aligned for lists and card interiors, evenly spaced for dot rows.
- At most one dark anchor per screen (the black pill button). Generous outer
  margins — nothing presses against the canvas edge.

**Color — native semantic tokens only, always light/dark adaptive**
- **All colors use tokens from Apple's design system** (semantic `UIColor`s
  via `Color(uiColor:)` or SwiftUI equivalents) — never fixed hex or RGB
  values. Tokens adapt to light/dark automatically; every prototype must
  read correctly in both modes.
- The reference hexes map to these closest Apple tokens (already wired into
  the `Baby` palette — use that, don't re-map):
  - canvas `#FAFAF8` → `systemGroupedBackground`
  - card surface `#FFFFFF` → `secondarySystemGroupedBackground`
  - skeleton bar `#E8E9E6` → `systemGray5`
  - empty block `#F2F3F1` → `systemGray6`
  - primary text `#1A1A1A` → `label`
  - secondary text `#8A8A86` → `secondaryLabel`
  - accent crimson → `systemPink`

**Typography**
- **All text uses native SwiftUI text styles** (`.largeTitle`, `.title`,
  `.headline`, `.subheadline`, `.body`, `.callout`, `.footnote`, `.caption`) —
  never `.system(size:)` or fixed point sizes. Adjust emphasis with
  `.weight(...)` on the text style, not with custom sizes.
- One plain sans family (the system font), two or three weights max.

**What stays real text**
- Narrative lines: state headlines ("Giorgios would like to stay"), section
  markers ("April", "May", "Today"), structural labels ("Check-in", "Guests",
  "Earnings"), eyebrows ("Checks in tomorrow"), and names.
- Action verbs on buttons, always: "Accept", "Decline", "Message".
- Every screen keeps at least one real narrative line as its anchor — a screen
  of only bars reads as a loading state, not baby UI.

**What collapses into placeholders**
- Values, dates, amounts, notes, body copy → **skeleton bars**: fully rounded
  flat gray pills (`systemGray5`), height ≈ the text's x-height (~8–12 pt),
  max two per block, widths varied (second bar ~55–75% of the first). No
  borders, no gradients, no shimmer — static, not loading.
- Large media/content regions → one flat, empty pale-gray rounded **block**.
  No icon, no X, no caption inside. At most one per screen, the quietest mass
  on the canvas.
- Label–value rows: real gray label on the left, one short bar on the right.

**Identity**
- Avatars survive: small circular photo crops (or neutral gray circles).
- Overflow as a pale circle with a count chip ("+3").
- Tiny status/verification badges sit on the avatar edge and may carry the
  accent hue.

**Accent & chrome**
- Exactly one saturated accent hue (crimson/pink in the references), kept to
  ~2–5% of the screen: one filled circular control, a badge, a progress bar,
  an active ring, or a thin underline on the active section header. Everything
  else is grayscale.
- Buttons: full-width black pill = primary, white pill = secondary.
- Top chrome: at most two small circular buttons; at most one accent-filled.
- Bottom navigation reduces to evenly spaced dots — gray filled for inactive,
  an accent ring/dot for active.

**State storytelling**
- To show a flow or lifecycle, repeat an *identical* skeleton across frames
  and change only the narrative line and the accent state (badge → progress
  bar → done). The sameness between frames is what tells the story.

## Guardrails

- **Never use small all-caps tracked-out labels** (eyebrows like `R E C E N T`).
  Section markers are sentence-case, regular size, muted gray — like "April"
  and "Today" in the references.
- **Never use fixed font sizes** — native SwiftUI text styles only.
- **Never use fixed hex/RGB colors** — Apple semantic color tokens only, so
  every screen adapts to light and dark mode for free.
- No wireframe tells: no dashed strokes, X-boxes, image glyphs, lorem ipsum,
  or annotation arrows.
- Don't greek headlines, labels, or button verbs. Don't stack more than ~3
  bar groups in one zone — collapse the rest into whitespace.
- Don't degrade polish to look "sketchy": baby UI is a finished artifact at
  reduced information density.

## Transitions & navigation

Do zoom transitions the way jayshell does them — with the **native zoom
transition API**, not hand-rolled `matchedGeometryEffect`:

- One `NavigationStack(path:)` with a single `@Namespace`. The tappable card
  is a `Button` that appends to the path, marked
  `.matchedTransitionSource(id:in:)`. The destination gets
  `.navigationTransition(.zoom(sourceID:in:))` in `navigationDestination`.
- Port jayshell's iOS 26 swipe-back workaround (FB19601591): after an
  interactive swipe-back the source view goes invisible unless rebuilt. Fold
  a refresh token into only the popped-back entry's `.id(...)` (see
  `ZoomRefresh`/`ZoomSourceID` in `AgentHomePrototype.swift`) so just that
  one card re-registers, set from the destination's `.onDisappear`.
- Persistent chrome (the input pill / chatbar) is ONE element floating above
  the whole stack (`overlay`/`safeAreaInset` at the root, like jayshell's
  GlobalChatBar) — never duplicated per screen. Screens reserve a clear slot
  where it parks; it animates between slots when the stack changes.

## SwiftUI primitives

Shared primitives live in `BabyUI/BabyStyle.swift` — always use them instead
of re-deriving the style:

- `Baby` — the palette: `canvas`, `surface`, `bar`, `block`, `ink`,
  `inkSecondary`, `accent`.
- `SkeletonBar(width:height:)` — a greeked line of text.
- `.babySurface(cornerRadius:)` — the white rounded card surface with the
  soft baby-UI shadow.

Add new reusable primitives (blocks, avatars, dot nav, pill buttons) to
`BabyStyle.swift` as they emerge — one source of truth for the style.
