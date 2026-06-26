# Fluidity — seamless transitions

Fluidity pushes the tray idea across the *whole* app: it "envisions the entire app as a constantly evolving space, where any element can theoretically transform into another, given there's a strong enough rationale for the transition." You float from screen to screen; you never teleport.

The governing rule: **every animation serves an architectural purpose — it helps the user understand their path from A → B.** If a transition doesn't clarify navigation, cut it. If a static cut leaves the user re-orienting, replace it with motion that *shows* the relationship between where they were and where they are.

## Core techniques

### 1. Visible links between screens (shared chrome, morphing content)
When two screens are steps in one flow, keep the **shared structure persistent** and only cross-fade/morph the parts that actually change. The frame, header, or background carries through; the inner content transforms. The user perceives one evolving space rather than two separate destinations.
*(Family: consecutive onboarding/iCloud-backup steps share a persistent container while the inner content cross-fades.)*

### 2. Shared-element travel — the same element, never a copy
The most important fluidity rule, stated as a pet peeve: **if a component is visible now and persists into the next screen, it must remain consistent and *travel* — never re-animate a duplicate.** A wallet card tapped in a list becomes the card on the detail screen; a token avatar selected in a picker is the same avatar that anchors the next step. This is "hero" / matched-element motion.

This single discipline ("avoid redundant animations") is what separates a fluid interface from a merely animated one. *(Family: wallet cards move between screens; the round token avatar is the matched element across the send flow; a list card promotes into a tray.)*

### 3. The app as a physical space — directional motion
Motion encodes spatial relationships:
- **Directional tabs.** Tapping a tab to the *left* slides content in from the left; a tab to the *right* slides from the right. This builds a subtle sense of where things live. "We fly instead of teleport." *(Family: position-aware segmented-tab transitions.)*
- **Directional push.** In a multi-step flow, forward steps enter from the trailing edge and back steps from the leading edge, so direction always matches intent. *(Family: the collectibles send flow.)*
- **Onboarding as a mapped journey.** Past the splash screen, a *stack of cards* animates to lay out the path ahead — showing where you're heading and implying how a different choice would branch elsewhere, so the user never feels lost.

### 4. Avoid static transitions even at the smallest scale
The philosophy ("avoid static transitions") scales all the way down to single glyphs. A chevron that would normally *cut* between steps instead **morphs/rotates** (e.g. a downward checkmark interpolating into a chevron in the Activity header). "This tiny detail, coupled with the broader view transition, clarifies the navigation action taken." These micro-decisions "quickly compound over time" into the overall feeling.

### 5. Status that travels (don't spawn, move)
When an action produces a result that lives elsewhere, *move* the indicator there rather than popping a new one:
- After confirming a transaction, the **spinner slides into the bottom navigation**, pointing the user to where their pending transaction now lives (the Activity tab).
- When a user *speeds up* a pending transaction, the speed-up **spinner travels to the original pending tray** — communicating "the speed-up has been applied to *that* transaction."

The traveling indicator is itself a navigation explanation: it answers "where did my thing go?"

### 6. Tray ⇄ full-screen, button ⇄ tray
Because the whole app is one space, surfaces interconvert: trays morph into full-screen views, buttons glide across trays, buttons morph into trays and back. A `Confirm` button's label can crossfade into an in-place spinner without the button moving. "Every interaction feeds into the next."

### 7. Charts that move (perceived speed)
Animating a price chart's line as it redraws between time ranges (interpolating the path) makes the app feel *faster*, not slower. The essay uses Cash App's static charts as the contrasting baseline — beautiful, but a fluid redraw would feel quicker. Motion, applied thoughtfully, can read as *more* responsive than an instant swap.

## Why it's worth the obsessiveness — prove it with subtraction
The essay's most persuasive move: it **removed** fluidity from real interactions to show the loss (a "max pain" before/after). Without the swap-approval animation, "the sense of connection is lost and the contextual continuity is gone." Without the wallet-grouping animation, the intent and outcome "become much less clear — the static approach feels like digital whiplash." Use this as a *design-review technique* → [applying-benji.md](applying-benji.md).

## iOS callout

- **Spring character first.** Adopt the iOS 17+ duration/bounce springs: `.snappy` as the everywhere-baseline (crisp, slight overshoot), `.bouncy` for playful confirmations, `.smooth` for serious motion. Wrap every state mutation in `withAnimation`. Springs are **interruptible and retargeting** — re-issuing mid-flight blends smoothly — and that interruptibility is the core of "the app is in tune with my intentions." For gesture releases use `.interactiveSpring` / `interpolatingSpring(duration:bounce:)` so momentum carries.
- **Shared-element travel across a push/sheet:** `@Namespace` + `.matchedTransitionSource(id:in:)` on the source + `.navigationTransition(.zoom(sourceID:in:))` on the destination (iOS 18+). This gives the hero morph *and* interactive swipe-back for free. IDs **must be stable** (model id, never an array index / fresh UUID) or it silently degrades to a cross-fade. Unavailable on macOS — gate it.
- **Shared element within one screen:** `matchedGeometryEffect(id:in:)` (iOS 14+) — interpolates size+position, but **not color**. For glass controls on iOS 26 use `.glassEffectID` instead.
- **Directional tabs/pushes:** stock `TabView` *cuts* between tab-bar taps. Drive your own: store `selection` + `lastSelection`, compute direction, render with `.id(selection)` and `.transition(.asymmetric(insertion: .move(edge: forward ? .trailing : .leading), removal: …))` inside `withAnimation`, `.clipped()`. (Use `.tabViewStyle(.page)` only when tabs are meant to be swiped.)
- **Chevron / glyph morph:** rotate with `.rotationEffect(.degrees(open ? 90 : 0))`; swap glyphs with `.contentTransition(.symbolEffect(.replace))` (shared strokes animate, rest cross-fades). iOS 17+.
- **Traveling spinner / status:** model it as one shared element moving between containers (`matchedGeometryEffect` / `.navigationTransition(.zoom)`), animated with a spring — not a remove-here-insert-there.
- **Chart path morph:** interpolate the line `Path` between datasets (custom `Shape` with an animatable parameter, or Swift Charts with `withAnimation`).
- Full API detail, availability, and gotchas: [ios-techniques.md](ios-techniques.md). Text and number morphs get their own file: [fluidity-text-morphing.md](fluidity-text-morphing.md).

**Demonstrated in Family clips:** onboarding shared-container steps, send→confirm with the hero token avatar, directional account tabs, wallet-grouping layout reflow, the `Confirm`→spinner in-button morph, the traveling pending/speed-up spinners, tray→full-screen with a cross-dissolving title, the Activity-header glyph morph, and the USDC/Netflix animated price charts.
