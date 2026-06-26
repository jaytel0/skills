# iOS techniques — reproducing Family's feel with iOS 26 / SwiftUI

Concrete API mappings for every benji pattern, with **availability** and **gotchas**. Use this when the target is iOS/SwiftUI. The principles in the other files are framework-agnostic; this file is the platform translation layer.

> **What is genuinely new in iOS 26?** Only the **Liquid Glass** family is net-new (`.glassEffect`, `GlassEffectContainer`, `.glassEffectID`, `glassEffectUnion`, `glassEffectTransition`, `.buttonStyle(.glass/.glassProminent)`, `.backgroundExtensionEffect()`, `.scrollEdgeEffectStyle`). Almost everything else people associate with "fluid Apple UI" is **iOS 17** (spring presets, base `symbolEffect`, `sensoryFeedback`, the Metal shader trio, `scrollTransition`) or **iOS 18** (`.navigationTransition(.zoom)`, `wiggle/breathe/rotate`). `matchedGeometryEffect` is iOS 14; `presentationDetents` is iOS 16. Gate aggressively with `#available`.

Treat iOS 26 SwiftUI as covering **~80%** of Family natively. The two honest gaps: a tray that *continuously morphs* into full-screen, and a *true shared-letter text morph* — both require bespoke work (called out below).

---

## 1. Spring & motion character (the foundation)
- `.snappy` — slight overshoot; **the modern default, use it everywhere as the baseline.**
- `.bouncy` — visible overshoot; playful confirmations only.
- `.smooth` — no overshoot; serious/subtle motion, and the reduce-motion fallback.
- `.spring(duration:bounce:)` to tune; `.interactiveSpring(...)` and `interpolatingSpring(duration:bounce:initialVelocity:)` for **gesture releases** (the latter *accumulates* velocity across rapid re-triggers — use for repeatedly-tapped controls).
- Apply via `withAnimation(.snappy) { … }` or `.animation(.snappy, value:)`. Springs are **interruptible/retargeting** by default — re-issuing mid-flight blends. That is the "alive" quality; don't replace it with fixed-duration `.easeInOut` for anything gesture-driven.
- **Availability:** presets + duration/bounce model **iOS 17+**; `response/dampingFraction` model iOS 13+; `interpolatingSpring` iOS 13+.

## 2. Trays / bottom sheets
- `.sheet(item:)` + `.presentationDetents([.height(120), .fraction(0.4), .large], selection: $detent)` — mix fixed/fractional/`.large`; bind `selection` so code+gesture stay in sync. Custom detent via `CustomPresentationDetent`.
- Pair with `.presentationDragIndicator(.visible)`, `.presentationCornerRadius(28)`, `.presentationBackgroundInteraction(.enabled(upThrough: .fraction(0.4)))` (keeps context tappable at small detents — the Maps feel), `.presentationContentInteraction(.scrolls)`.
- **Availability:** `presentationDetents` (+ `.fraction/.height/.custom`) **iOS 16+**; background-interaction + `.presentationBackground/.presentationCornerRadius` **iOS 16.4+**.
- **iOS 26:** sheets adopt Liquid Glass automatically — **remove custom sheet backgrounds**, small detents inset-float.
- **Gotchas / the gap:** the system sheet's inter-detent drag physics are **not** customizable, and it's a **detached presentation layer** — `matchedGeometryEffect` IDs do **not** bridge across it. For a true tray→full-screen *morph* or custom snap physics, build a **custom in-tree container**: one drag-progress `@State`, interpolate frame/cornerRadius/offset directly; on iOS 26 wrap in `GlassEffectContainer` + `.glassEffectID` so the glass shape reshapes.

## 3. Shared-element transitions
- **Across a push or sheet (hero card travel):** `@Namespace`; `.matchedTransitionSource(id:in:)` on source; `.navigationTransition(.zoom(sourceID:in:))` on destination. Gives the morph **and** interactive swipe-back free.
  - **Availability:** **iOS 18+**; unavailable on macOS (gate it).
  - **Gotcha:** ID must be **stable** (`model.id`, never an array index or fresh UUID) or it silently falls back to a cross-fade.
- **Within one screen (inline expand):** `.matchedGeometryEffect(id:in:)` — interpolates size+position **only, not color**. iOS 14+.
- **Glass element morph (iOS 26):** prefer `.glassEffectID` over `matchedGeometryEffect` when the moving thing is glass (§9).

## 4. Text & numbers
- **Odometer / rolling digits / shifting commas:** `Text(amount, format: .number).contentTransition(.numericText(value: Double(amount)))` inside `withAnimation`; add `.monospacedDigit()`. Pass `value:` for correct direction. Keep the `Text` identity stable (don't `.id()` it) so digits diff individually. **iOS 17+** (silently no-ops on iOS 16).
- **Word→word label swap (reads as a morph):** `Text(title).id(title).transition(.blurReplace)` in `withAnimation`, or one `Text` with `.contentTransition(.interpolate)`. `.interpolate` iOS 16+; `.blurReplace` iOS 17+.
- **True shared-letter morph (`Con`tinue→`Con`firm):** **no stock API.** Requires a custom **`TextRenderer`** (iOS 18+) animating per-glyph — match common letters, translate/cross-fade the rest. Bespoke; don't promise it as a modifier.

## 5. SF Symbols
- **Emphasis (one-shot):** `.symbolEffect(.bounce, value:)`, `.pulse`, `.variableColor` — must sit on the `Image`, value must change identity. **iOS 17+.**
- **Indefinite (while active):** `.symbolEffect(.rotate, isActive:)`, `.breathe`, `.wiggle` — **iOS 18+**; set `isActive: false` offscreen to stop redraws.
- **Glyph swap/morph:** `.contentTransition(.symbolEffect(.replace))` (variants `.replace.downUp`, `.replace.magic(fallback:)` iOS 18). Lives on `ContentTransition`, **not** `SymbolEffect`.
- **Disclosure chevron:** prefer `Image(systemName:"chevron.right").rotationEffect(.degrees(open ? 90 : 0))` over a symbol swap — crisper, respects optical center.
- **Note:** there is **no** `.ripple` symbol effect; for a wave-through-glyph use `.variableColor.iterative`.

## 6. Haptics & sound (the feel layer — not optional)
- **Declarative (state changes):** `.sensoryFeedback(.success, trigger:)`, `.sensoryFeedback(.selection, trigger:)`, `.sensoryFeedback(.impact(weight:.heavy, intensity:), trigger:)`; closure overload returns feedback or `nil` for conditional firing. **iOS 17+** (no-op on iPad — no haptic hardware).
- **Imperative (delegate/physics callbacks):** `UIImpactFeedbackGenerator`, `UISelectionFeedbackGenerator`, `UINotificationFeedbackGenerator` — call `.prepare()` ~0.1s ahead to cut latency. iOS 10+.
- **Custom textures synced to particles/sound:** **Core Haptics** (`CHHapticEngine`, `CHHapticEvent` transient/continuous, or designer `.ahap`). iOS 13+. Check `CHHapticEngine.capabilitiesForHardware().supportsHaptics`.
- **Sound:** `AVAudioPlayer` (designed assets, full control; set `AVAudioSession` category `.ambient` to respect the mute switch) or `AudioServicesPlaySystemSound` (fire-and-forget). Sync to the *physics contact callback*, not a timer.
- Per local `ios-swiftui-ui-patterns/references/haptics.md`: centralize in a `HapticManager`, gate behind a user pref, don't fire on every micro-interaction.

## 7. Metal shaders (bespoke GPU effects)
Three modifiers, all **iOS 17+**, fed by `ShaderLibrary.<fn>(...)`; animate via `TimelineView(.animation)` or `keyframeAnimator` off a trigger:
- `.colorEffect` — recolor per pixel (shimmer, sequins, gradient sweeps). **Cheapest — use when you only recolor.**
- `.distortionEffect(_, maxSampleOffset:)` — move pixels (wave/magnify); can't sample neighbors.
- `.layerEffect(_, maxSampleOffset:)` — gets `SwiftUI::Layer` to **sample arbitrary pixels** → the only one that does a real touch-**ripple**.

**The ripple keystone (Apple WWDC24 session 10151):**
1. `Ripple.metal` — a `[[stitchable]] half4` fn modeling a decaying sine wave: `amount = amplitude * sin(frequency*time) * exp(-decay*time)`, then `layer.sample(position + offset)`.
2. `RippleModifier` — `content.visualEffect { v,_ in v.layerEffect(shader, maxSampleOffset: CGSize(amplitude, amplitude), isEnabled: 0 < t && t < duration) }`. **`maxSampleOffset >= amplitude` or it clips at the edges.**
3. `RippleEffect<T:Equatable>` — drive `elapsedTime` 0→duration with `keyframeAnimator(initialValue:0, trigger:){ MoveKeyframe(0); LinearKeyframe(duration, duration:duration) }`.
4. Capture origin: `@State origin: CGPoint` + counter; `onTapGesture(coordinateSpace:.local){ loc in origin = loc; counter += 1 }`. (Apple's sample uses a custom `onPressingChanged` wrapping `DragGesture(minimumDistance:0)` — that's bespoke, not a system API.)

This skeleton generalizes to sequin/stealth/redaction sheens (swap `layerEffect`→`colorEffect`, drive with a time/progress uniform).

## 8. Particles & physics
- **Confetti:** `CAEmitterLayer`+`CAEmitterCell` via `UIViewRepresentable` (lowest-effort/high-quality; one-shot = pulse `birthRate` then 0); or `SpriteView` + `SKEmitterNode` (gravity/tumble); or pure `Canvas`+`TimelineView` particles.
- **Physical tumble (trash):** real collision/restitution needs **SpriteKit** (`SKPhysicsBody`, `SKPhysicsContactDelegate`) or **UIKit Dynamics** — SwiftUI curves can't. Fire the sound/haptic on the contact callback.
- **Availability:** `CAEmitterLayer` iOS 5+, `SpriteView` iOS 14+, `Canvas`/`TimelineView` iOS 15+.

## 9. Liquid Glass (the iOS 26 net-new layer)
- `.glassEffect(.regular, in: shape)` — translucent lensing material on a custom view. Variants `.regular`/`.clear`; chain `.tint(_)`, `.interactive()`. Default shape is `Capsule`.
- `GlassEffectContainer(spacing:) { … }` — wrap multiple glass views so they blend/merge/morph and batch-render. `spacing` is the merge threshold. **Required for multi-element morphing.**
- `.glassEffectID(_:in:)` — the **iOS 26 successor to `matchedGeometryEffect` for glass.** Contract: same container + shared `@Namespace` + conditional show/hide + `withAnimation`. Missing any one silently drops the morph to a fade.
- `.glassEffectUnion(id:namespace:)`; `.glassEffectTransition(.matchedGeometry | .materialize | .identity)`.
- `.buttonStyle(.glass)` / `.glassProminent` — for buttons in *your content area* (system surfaces get glass automatically). Bug: prominent+circle press artifacts → add `.clipShape(Circle())`.
- `.backgroundExtensionEffect()` (mirror/blur content under floating chrome) and `.scrollEdgeEffectStyle(.hard/.soft, for:)` — both genuinely new.
- **HIG rule (load-bearing):** glass belongs on the **functional/navigation layer only** — never the content layer, never glass-on-glass.
- **Gotcha:** specular highlights/motion **don't render in the Simulator** — verify on device. Recompiling against the iOS 26 SDK auto-glasses native controls; **remove custom backgrounds** that fight it.

## 10. Scroll
- `.scrollTransition { content, phase in … }` — scale/opacity/blur as items enter/leave (`phase.isIdentity`, `phase.value` -1…1). iOS 17+.
- `.scrollTargetBehavior(.viewAligned/.paging)` + `.scrollTargetLayout()` + `.scrollPosition(id:)` — snap/paging carousels. iOS 17+.

## 11. Accessibility & system respect (every effect)
- Gate motion/particles behind `@Environment(\.accessibilityReduceMotion)` → degrade to `.smooth`/fade/static. `symbolEffect` and Liquid Glass honor reduce-motion/transparency automatically; test both on.
- `.privacySensitive()` + `.environment(\.redactionReasons, .privacy)` for stealth/redaction (also covers app-switcher snapshots).
- Stop indefinite effects (looping shimmers, live emitters, indefinite `symbolEffect`) when offscreen to save 60/120 Hz redraws and battery.

---

## Family technique → iOS API (quick map)

| Family technique | Primary API | Min iOS | benji ref |
|------------------|-------------|---------|-----------|
| Variable-height tray | `presentationDetents([...], selection:)` | 16 | simplicity-tray-system |
| Tray → full-screen morph | custom container (+ `glassEffectID` on 26) | 16 / 26 | simplicity-tray-system |
| Card travels to detail | `matchedTransitionSource` + `.navigationTransition(.zoom)` | 18 | fluidity-transitions |
| Inline element morph | `matchedGeometryEffect` | 14 | fluidity-transitions |
| Directional tabs | custom `.transition(.move(edge:))` | 13 | fluidity-transitions |
| `Continue`→`Confirm` | `.blurReplace` / custom `TextRenderer` | 17 / 18 | fluidity-text-morphing |
| Odometer / commas | `.contentTransition(.numericText(value:))` | 17 | fluidity-text-morphing |
| Chevron/glyph morph | `.rotationEffect` / `.contentTransition(.symbolEffect(.replace))` | 17 | fluidity-transitions |
| Tap ripple | `.layerEffect` (WWDC24 RippleEffect) | 17 | delight-selective-emphasis |
| Sequins / shimmer / stealth | `.colorEffect` shader / `Canvas` + mask | 17 / 15 | delight-selective-emphasis |
| Confetti | `CAEmitterLayer` / `SpriteKit` / `Canvas` | 5 / 14 / 15 | delight-selective-emphasis |
| Trash tumble + sound | SpriteKit physics + `AVAudioPlayer` + haptic | 8 | delight-selective-emphasis |
| Drag-reorder + stack | `.onMove` / `.draggable`+`.dropDestination` | 13 / 16 | delight-selective-emphasis |
| Feel layer | `.sensoryFeedback(_:trigger:)` / Core Haptics | 17 / 13 | delight-selective-emphasis |
| The "new design" glass | `.glassEffect` / `GlassEffectContainer` / `.glassEffectID` | 26 | this file §9 |

**Sources:** Apple Developer docs (SwiftUI), WWDC23 §10258 (SF Symbols), WWDC24 §10151 (custom visual effects / RippleEffect) & "Enhance your UI animations and transitions" (zoom), WWDC25 §323 (Liquid Glass / new design); local skills `swiftui-liquid-glass`, `ios-swiftui-ui-patterns` (matched-transitions, sheets, haptics), `emilkowal-animations`. Knowledge cutoff Jan 2026 — verify the newest signatures against current docs.
