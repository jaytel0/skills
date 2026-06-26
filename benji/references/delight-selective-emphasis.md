# Delight — selective emphasis

Delight is the layer that turns a tool into a companion. But its power comes from **placement, not quantity**: "Mastering delight is mastering selective emphasis — knowing where, when, and how to apply magical moments intentionally." Two ideas govern every decision here:

1. **The Delight-Impact Curve** (see [philosophy.md](philosophy.md)): the rarer the feature, the more delight pays off. Lavish on the once-in-a-while; whisper on the everyday.
2. **Surprise & novelty.** Easter eggs delight *because* they're discovered. Hide them in plain sight, on features used "just enough" that the surprise is a pleasant find rather than a recurring annoyance.

And the precondition (again, because it's the one most often skipped): **uniform polish everywhere**, or none of the delight reads as intentional.

What delight is *for*, explicitly: not entertainment for its own sake, but **valuing and rewarding the user's time and emotional investment** — "transforming something mundane into something memorable."

## The delight catalog (with intensity, by frequency)

### High ceremony — rare, weighty moments (turn the dial up)
- **New wallet setup.** A significant but infrequent act most wallets make mundane. Family instead "crafts an interactive animation that marks the occasion and makes it memorable." Treat first-time creation as an event.
- **Successful backup → confetti.** After backing up a wallet, confetti briefly fills the screen, rewarding completion of an essential security task. Keyed to the verification *milestone*, not a button tap.
- **Backup as a doorway.** The seed-phrase backup flow literalizes the rooms/doorway metaphor — a door swings open as a screen-to-screen passage, making a dry security chore feel like progress through a space.

### Discovery — easter eggs (surprise is the mechanism)
- **QR tap → ripple.** Tapping the QR code triggers a gentle ripple radiating from the touch point — "the magical moment is hidden in plain sight." The Receive screen is used just often enough for this to stay a delight.
- **QR swipe → sequins.** Dragging a finger across the QR reveals a sequin-like, paillette transformation of the dots — a per-module shimmer/hue wave that follows the finger. Discovery *is* the delight.
- **Exceed max balance.** Entering a send amount above the available balance triggers a playful easter-egg reaction — surprise on a frequent screen, used sparingly.

### Everyday — frequent features (keep it subtle)
- **Shifting commas.** As you type a send amount, digits slide and grouping commas shift into place (odometer feel). Tiny, satisfying, and deliberately *not* more — "doing too much would quickly diminish potential delight" on a daily action. (Mechanics in [fluidity-text-morphing.md](fluidity-text-morphing.md).)
- **Chart scrub feedback.** Scrubbing a price chart moves a follower dot/loupe and **flips the trend arrow up or down** as the numbers change beneath your finger — continuous, informative micro-delight.

### Quiet craft — less-frequent features still get care
- **Trash that tumbles.** Sending tokens/collectibles to the trash makes them physically tumble (offset + rotation + scale, staggered) into a **skeuomorphic trash can**, with a satisfying **sound effect** on completion. Physical metaphor + audio = memorable disposal.
- **Browser empty state arrow.** First use of the in-app browser greets the user with a hand-drawn, self-drawing guidance arrow pointing toward creating a new tab — instantly friendlier, and it teaches.
- **Reorder by drag, stack by drop.** Reordering tokens uses smooth drag-and-drop with attractive stacking animations (a "picked-up" row elevates; others animate aside) — making a tedious task satisfying. (Credited inspiration: [Things](https://culturedcode.com/things/).)
- **Stealth mode shimmer.** Activating stealth mode sweeps a gentle shimmer across the now-hidden balances — signaling that values are concealed *but still updating discreetly in the background*. The shimmer's intent is "private," not "loading," so keep it slow and subtle.

## iOS callout

Pick the right layer: (1) **stock modifiers** first (`symbolEffect`, `sensoryFeedback`, `redacted`, `draggable`/`onMove`); (2) **`Canvas` + `TimelineView(.animation)`** for hand-rolled particles/shimmer with no extra frameworks; (3) **Metal shaders** (`.colorEffect`/`.layerEffect`) or **UIKit/SpriteKit** (`CAEmitterLayer`, `SKEmitterNode`, physics) for truly liquid/physical effects. Family's polish usually sits in tier 2/3 **with a tier-1 haptic on top**.

| Delight | iOS approach | Availability |
|---------|--------------|--------------|
| **Tap ripple** (the keystone) | Apple WWDC24 *RippleEffect*: a `[[stitchable]]` Metal **`.layerEffect`** (decaying sine wave) driven by `keyframeAnimator` on a trigger; capture origin with `onTapGesture(coordinateSpace:.local)`. `maxSampleOffset >= amplitude` or it clips. | iOS 17+ |
| **Sequin / shimmer / stealth sweep** | Time-driven `[[stitchable]]` **`.colorEffect`** shader (sliding specular band + hue flip), or `Canvas`+`TimelineView` per-dot phase; drive `progress` from the drag. For stealth, compose `.blur`/`.redacted`/`.privacySensitive` (hide) + an animated gradient `.mask` (sheen). | shaders iOS 17+, `Canvas`/redaction iOS 15+ |
| **Confetti** | `CAEmitterLayer`+`CAEmitterCell` (lowest-effort, high quality), or `SpriteKit` `SKEmitterNode` for physical tumble, or pure `Canvas` particles. One-shot: pulse `birthRate` then zero it. | `CAEmitterLayer` iOS 5+, `SpriteView` iOS 14+ |
| **Trash tumble + sound** | Needs a **physics engine** (SpriteKit `SKPhysicsBody` or UIKit Dynamics) — animation curves can't do collision. **Sync the sound + haptic to the physics contact callback**, not a timer. Sound via `AVAudioPlayer` (designed crumple) or `AudioServicesPlaySystemSound`. | SpriteKit iOS 8+, AVAudioPlayer iOS 2.2+ |
| **Haptics (the feel layer)** | `.sensoryFeedback(_:trigger:)` for state changes (`.success`, `.selection`, `.impact(weight:)`); imperative `UIFeedbackGenerator` (call `.prepare()`) for delegate/physics callbacks; **Core Haptics** (`CHHapticEngine`/AHAP) for custom textures synced to particles/sound. Centralize, gate behind a pref, check `supportsHaptics`, don't over-fire. | `.sensoryFeedback` iOS 17+ (no-op on iPad), Core Haptics iOS 13+ |
| **Drag-reorder + stack** | `List`/`ForEach` `.onMove` for linear reorder; `.draggable` + `.dropDestination(for:)` (Transferable) on both gaps (reorder) and items (stack/group), using `isTargeted` to highlight; drive the literal pile with a `ZStack` + `matchedGeometryEffect` + offset/rotation. Fire `.sensoryFeedback(.impact, …)` on pickup and successful stack. | `.onMove` iOS 13+, `.draggable`/`.dropDestination` iOS 16+ |
| **Chart scrub arrow flip + numbers** | `.contentTransition(.symbolEffect(.replace))` or `.rotationEffect` for the trend arrow; `.contentTransition(.numericText(value:))` for the tracking value; a `DragGesture` over a custom chart `Shape`/Swift Charts. | iOS 17+ |
| **Doorway / room transition** | Custom container interpolating a 3D `rotation3DEffect`/perspective as a screen-to-screen passage; or `.navigationTransition(.zoom)` for a hero expand. | iOS 18+ for zoom |

Decorative motion must honor `@Environment(\.accessibilityReduceMotion)` (degrade to a static/cross-fade variant); sounds should set an appropriate `AVAudioSession` category and respect the mute switch; stop indefinite shimmers/emitters when offscreen to save battery. Full detail, shader skeletons, and sources: [ios-techniques.md](ios-techniques.md).

**Demonstrated in Family clips:** QR ripple & sequin shimmer (Receive screen), confetti on backup completion, the door-swing backup passage, collectibles tumbling into the trash can, the in-app browser's self-drawing arrow, drag-to-reorder with an elevated row, stealth-mode shimmer sweep, the odometer amount field with its max-balance easter egg, and the chart-scrub arrow flip.
