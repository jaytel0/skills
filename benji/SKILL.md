---
name: benji
description: Family's interaction-design philosophy — simplicity, fluidity, and delight — as a framework-agnostic pattern language for building software that feels welcoming, alive, and respectful. Use when designing or reviewing interactions, transitions, animations, tray/bottom-sheet flows, onboarding, empty states, text/number morphing, or delight moments (ripples, confetti, haptics, easter eggs) in any framework. Includes iOS 26 / SwiftUI API mappings for each pattern.
---

# benji — Family's interaction design philosophy

A pattern language distilled from **Family** (a self-custody iOS wallet) and its essay [*Family Values*](https://benji.org/family-values) by Benji. It is the answer to one question: **how do you make something complex feel welcoming — familiar, even?**

The thesis: great interaction design is a form of **respect** — for the user's *time*, *intelligence*, and *feelings*. You earn it through three values:

| Value | What it buys | One-line test |
|-------|--------------|---------------|
| **Simplicity** | accessibility | *Is everything-but-the-fundamentals hidden until it's relevant?* |
| **Fluidity** | continuity of experience | *Can the user see their path from A → B as it happens?* |
| **Delight** | emotional connection | *Does this moment reward the user for being here?* |

These are values, not rules. Apply judgment. A single fluid transition does not make a fluid interface — the feeling is the compound result of *thousands* of small, deliberate, consistent decisions.

## When to use this skill

Reach for it when you are **building or reviewing how something feels to use**:
- designing a flow, transition, or animation; choosing a bottom-sheet/tray vs a full screen
- onboarding, confirmation, or any multi-step flow with branches and edge cases
- a button label, number, or component that *changes* between states (morph it, don't cut)
- empty states, success moments, destructive actions, rarely-used features
- adding (or restraining) delight — ripples, confetti, haptics, sound, easter eggs
- a design review where the work is technically correct but feels static, lifeless, or disjointed

It is framework-agnostic. Every pattern is described in terms of **state and motion**, then mapped to concrete **iOS 26 / SwiftUI APIs** (see [`references/ios-techniques.md`](references/ios-techniques.md)) because that is where Family lives — but the principles transfer to web, Android, or anywhere.

## The operating principles (internalize these)

1. **Gradual revelation.** Put the fundamentals at the user's fingertips; reveal everything else *as it becomes relevant*. Don't present every feature at once. (→ the tray system.)
2. **Avoid static transitions.** A static product feels lifeless, and lifeless feels uncared-for. Motion is not decoration — **every animation must explain the navigation** (where you came from, where you're going). *"We fly instead of teleport."*
3. **The app is a physical space with unbreakable rules.** Treat it as coherent and dimensional. Know *how and why* a transition makes sense before adding it. Fluidity is "moving through water — you float rather than walk."
4. **Don't duplicate what persists.** If a component is on screen now and will be on screen next, it should *stay consistent* and travel — never re-animate a fresh copy of something already there.
5. **Selective emphasis.** Mastering delight is knowing *where, when, and how* to place magical moments — and where to hold back. Follow the **Delight-Impact Curve**: delight's value rises as a feature's usage frequency falls.
6. **Polish everywhere, or the delight doesn't land.** Inconsistent polish reads like *"a fancy restaurant with a dirty bathroom."* You earn the right to delight anywhere only by being holistic everywhere.
7. **Respect is the throughline.** Smooth, consistent interactions whisper *"I know exactly what you need — let me get that for you."* Glitchy ones erode trust.

Full treatment, the metaphors (rooms/doorways, water), and the deliberate trade-offs ("Family comes first") are in [`references/philosophy.md`](references/philosophy.md).

## Pattern index

| Area | Reference | Covers |
|------|-----------|--------|
| Philosophy | [philosophy.md](references/philosophy.md) | the three values, respect, Delight-Impact Curve, metaphors, trade-offs |
| Simplicity | [simplicity-tray-system.md](references/simplicity-tray-system.md) | the dynamic tray system and its rules; tray vs full-screen |
| Fluidity | [fluidity-transitions.md](references/fluidity-transitions.md) | seamless transitions, shared elements, directional motion, no-redundant-animation |
| Fluidity | [fluidity-text-morphing.md](references/fluidity-text-morphing.md) | shared-letter morphs (Continue→Confirm), odometer numbers, partial text updates |
| Delight | [delight-selective-emphasis.md](references/delight-selective-emphasis.md) | the curve, easter eggs, ripples, confetti, sound, haptics, the delight catalog |
| iOS | [ios-techniques.md](references/ios-techniques.md) | exact iOS 26 / SwiftUI APIs per technique, with availability + gotchas |
| Process | [applying-benji.md](references/applying-benji.md) | how to apply, the "max pain" test, the review checklist, anti-patterns, accessibility |

## Quick reference: signature techniques

| Technique | Essence | iOS callout (details in ios-techniques.md) |
|-----------|---------|--------------------------------------------|
| Dynamic tray | one focused action/content per sheet; **each successive tray varies in height** | `presentationDetents([.height(…), …])`; custom container for true morph |
| Tray emerges from its trigger | sheet unfolds *from* the button/screen that summoned it, preserving context | `matchedGeometryEffect` / custom container; not the system sheet across boundaries |
| Card travels between screens | the same element flies; never duplicated | `matchedTransitionSource` + `.navigationTransition(.zoom)` (iOS 18+) |
| Directional tabs | tap a left tab → content slides in from the left | custom directional `.transition(.move(edge:))` (stock `TabView` cuts) |
| Text morph | `Continue` → `Confirm` keeping the shared "Con" | numbers: `.contentTransition(.numericText())`; words: `.blurReplace`; true letter-share: custom `TextRenderer` |
| Odometer numbers | digits roll, commas slide in/out as you type an amount | `.contentTransition(.numericText(value:))` + `.monospacedDigit()` |
| Chevron / glyph morph | a glyph rotates or morphs instead of cutting | rotate: `.rotationEffect`; swap: `.contentTransition(.symbolEffect(.replace))` |
| Traveling spinner | a status indicator *moves* to where the result will live | shared-element move + spring |
| Tap ripple | a wave radiates from the exact touch point | Metal `.layerEffect` (WWDC24 RippleEffect), iOS 17+ |
| Confetti / shimmer / sequins | celebratory / privacy / discovery sheen | `CAEmitterLayer` / `SpriteKit`; `Canvas`; `.colorEffect` shaders |
| Feel layer | a tuned haptic on nearly every meaningful gesture | `.sensoryFeedback(_:trigger:)` (iOS 17+); Core Haptics for texture |

Spring character: default to **`.snappy`** (crisp, minimal overshoot), **`.bouncy`** for playful confirmations, **`.smooth`** for serious/subtle motion. Wrap state changes in `withAnimation`; springs are interruptible and that interruptibility *is* the alive feeling.

## Source & provenance

All patterns are drawn from *Family Values* (benji.org/family-values, 8 July 2024) and frame-by-frame study of its ~53 demonstration clips (`/media/family-values/NN.mp4`). Each reference file cites which Family interaction demonstrates a pattern. Attribute the philosophy to the Family team when you reuse it.
