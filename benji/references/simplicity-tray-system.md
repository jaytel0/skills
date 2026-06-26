# Simplicity — the dynamic tray system

Simplicity through **gradual revelation**, made concrete. A *tray* is a component-bearing surface (a bottom sheet) that **expands, contracts, and adapts** in response to the user. It is "a condensed version of the app, with a specific set of constraints and capabilities" — the app shown through a doorway, revealing more as you approach.

This is *the* staple pattern in Family. It is how a wallet with hundreds of paths stays uncluttered: each tray shows only what's relevant *now*.

## The rules of a tray (each one is load-bearing)

1. **User-initiated.** Trays appear from a deliberate action — tapping a button or icon, or opening a push notification. They are never thrown at the user unprompted.
2. **Standalone *or* emergent.** A tray can sit as a standalone surface on top of any content, **or emerge from within the component that summoned it** (a button unfolds into the tray). Emergent trays are the high-fluidity choice — they preserve the visual link to the trigger. *(Family: the swap-approval tray unfolds from inside the swap interface itself.)*
3. **Each successive tray varies in height.** This is the signature constraint. When one tray replaces another, **changing the height makes the progression unmistakable.** It sometimes forces you to rewrite copy or tweak a layout just so the height differs — do it. Equal-height successive trays read as "nothing happened."
4. **Title + leading icon, always.** Every tray has a title that succinctly names its function or contents, plus a leading icon. The **icon is dual-purpose**: it *dismisses* the tray if it's the first one shown, or *navigates back* through a sequence of trays presented one-by-one. *(Family: the leading glyph morphs between an `✕` and a back-chevron depending on navigation depth.)*
5. **One thing per tray.** Each tray is dedicated to a *single* piece of content (e.g. educational text explaining a feature) **or** a *single* primary action (e.g. a checklist to complete before a transaction). Never two jobs in one tray. This is what keeps every step approachable.
6. **Theme adapts to context.** A tray inherits the visual theme of the flow it lives in — inside a dark-themed flow, trays adopt a darker colour scheme. The tray belongs to its surroundings, not to a generic modal style.

## Tray vs. full screen — the decision rule

Use a **tray** for **transient** actions that don't need to stay permanently on display:
- confirmation steps and warnings — they "appear in the right place at the right time"
- a single educational explanation the first time a feature is used
- the *start* of a more elaborate flow (a tray can be the entry that later escalates to full screen)

Use a **full screen** for content the user needs to dwell in or return to.

The decisive advantage of trays is **context preservation**. A full-screen transition *displaces* the user from where they were; a tray *overlays* the current interface, keeping the prior context visible (usually dimmed/scaled behind a scrim). The flow feels integrated, not disjointed — "users are guided through actions with the reassurance that they're not veering off course, but rather diving deeper into their current context." The **compact** size itself signals *this is approachable* — no full-screen commitment required.

## Composition behaviors seen across Family

- **Stacked, content-morphing trays.** A single persistent tray container resizes and swaps its inner content step-to-step (a wizard), rather than presenting N separate modals. The container is continuous; the content changes.
- **Tray within a tray.** Tapping a row inside a tray can raise a nested tray for a sub-choice, with the parent context preserved behind it.
- **Tray → full screen morph.** A tray that begins a flow grows seamlessly into a full-screen surface when the flow deepens (e.g. a selectable list tray becomes the full creation screen).
- **Card → tray promotion.** Tapping a card in a list can promote that exact card into a tray (shared-element; see [fluidity-transitions.md](fluidity-transitions.md)).
- **Blended first-run tutorial.** The first time a feature is used, the teaching is *inside* the same tray flow — not a separate coach-mark layer bolted on top.

> Family's tray system was inspired in part by [Craft](https://www.craft.do/)'s similar system, and Emil Kowalski documents the engineering of it at [emilkowal.ski/ui/family-tray-system](https://emilkowal.ski/ui/family-tray-system).

## The mental model to design against

> *Imagine seeing parts of a room through an open doorway. From a few metres away, you catch a glimpse of what's inside. As you approach and enter, the space and its contents are gradually revealed.*

Each user action makes the interface unfold — "much like walking through a series of interconnected rooms. As a user, I get to see where I'm going as I go there." Design the sequence so the *next* room is always partly visible from the current one.

## iOS callout

- **Variable-height trays:** `.sheet` + `.presentationDetents([.height(120), .fraction(0.4), .large])` with a bound `selection:` so code and gesture stay in sync. Add `.presentationDragIndicator`, `.presentationCornerRadius`, and `.presentationBackgroundInteraction(.enabled(upThrough:))` to keep the context behind tappable at small detents. iOS 16+; on **iOS 26** sheets adopt Liquid Glass automatically — *remove* custom sheet backgrounds so the glass takes over.
- **The "vary the height" rule** maps directly to giving each step a different detent.
- **Two honest gaps** where the stock sheet can't express Family's feel: (1) a tray that *continuously morphs* into a full-screen surface — the system sheet is a detached presentation layer, so `matchedGeometryEffect` IDs don't bridge it; and (2) custom inter-detent drag physics. For both, build a **custom in-tree container** driven by one drag-progress `@State`, interpolating frame / cornerRadius / offset directly. On iOS 26, wrap it in a `GlassEffectContainer` and tag the surface with `.glassEffectID(_:in:)` so the glass shape itself reshapes as it grows — Apple's sanctioned morph primitive.
- **Leading-icon morph (✕ ↔ back):** swap `Image(systemName:)` with `.contentTransition(.symbolEffect(.replace))`, or rotate a chevron with `.rotationEffect`.
- Full API detail, availability, and gotchas: [ios-techniques.md](ios-techniques.md).

**Demonstrated in Family clips:** import flow (themed tray over amber backdrop), watch-only identity editing, add/remove/new wallet flows, Refuel Gas approval, settings help tray summoned by `?`, ENS transfer config tray, swap & mint.fun approval trays emerging from their flows, the iCloud-backup "doorway" tray, send-from-interface confirmation trays.
