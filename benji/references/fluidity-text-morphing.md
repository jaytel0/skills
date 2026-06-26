# Fluidity — text & number morphing

Applying fluidity to *type* is one of Family's most-copied signatures. The principle: **when a label or value changes, morph it — don't cut it.** A cut can hide a meaningful change; a morph makes the change "both noticeable and smooth," reinforcing the user's awareness of what is happening.

## The shared-letter button morph (`Continue` → `Confirm`)

The canonical example. As the user advances from entering a transaction to confirming it, the primary button's label evolves from **Continue** to **Confirm**. In a static interface this swap happens instantly — and "could leave the user unaware of the significant step they're about to take" (confirming a transaction = money on the line).

Family's mechanism: **shared letters are matched and held while the rest morphs.** Both words share "Con", so those glyphs persist in place while "tinue" transforms into "firm". The retained letters create continuity; the changing letters draw the eye to *what changed*. The button itself does not jump — same instance, same position, morphing label.

Use it on any high-consequence label change where you want the user to *register* the shift, especially the value-entry → confirmation boundary of a money flow.

## Live count-text morph

The same idea applied to dynamic counts. As a user adds wallets from an index, a single persistent CTA updates its number and text fluidly — `Add 1 Wallet` → `Add 2 Wallets` → … — the same button re-rendering rather than swapping out. The morph keeps attention on the button while quietly confirming the running state.

## Partial text updates (keep the constant part constant)

A generalization of the shared-letter idea to whole components that "travel" between states: **when only part of a sentence needs to change, change only that part.** Keep the unchanged words fixed and morph just the delta. Family does this on empty states — updating the portion that differs rather than re-animating the entire sentence, which "avoids the jarring effect of altering the whole sentence abruptly." It reads as one stable element refining itself, not a flash-and-replace.

## Odometer numbers & shifting commas

For numeric input (e.g. typing a send amount), each digit is its own animatable glyph. As digits are appended, **existing digits slide to make room and grouping commas shift into place** (`999` → `1,000` slides the comma in and pushes the digits over). It's an odometer feel: small, continuous, and — because *sending* is a daily action — kept *subtle* per the Delight-Impact Curve. (The easter-egg variants, like exceeding max balance, live in [delight-selective-emphasis.md](delight-selective-emphasis.md).)

## iOS callout — read this honestly

iOS does **not** ship a true "share the matching letters, morph the rest" typographic transition. Be precise about what you can and can't get for free:

| Need | API | Availability | Reality |
|------|-----|--------------|---------|
| Number rolls / comma shifts (odometer) | `.contentTransition(.numericText(value:))` + `.monospacedDigit()` | iOS 17+ (no-ops on 16) | **Exact match.** Digits roll directionally; the grouping separator slides in/out as magnitude changes. Pass `value:` so direction is correct; keep the `Text` identity stable so the engine diffs digit-by-digit. |
| Word → word label swap that *reads* like a morph | `Text(title).id(title).transition(.blurReplace)` or a single `Text` with `.contentTransition(.interpolate)`, inside `withAnimation(.snappy)` | `.interpolate` iOS 16+, `.blurReplace` iOS 17+ | A soft blur/cross-dissolve. Good enough for most cases; it's what Apple itself uses for label/symbol swaps. |
| **True shared-letter morph** (`Con`tinue → `Con`firm, glyph-accurate) | **Custom `TextRenderer`** animating per-glyph (match common letters, translate/cross-fade the rest) | `TextRenderer` iOS 18+ | **Bespoke work — no stock modifier.** Don't promise it as a one-liner. This is the gap that makes Family's morph feel special; budget custom rendering if you need it pixel-accurate. |
| Glyph/symbol swap (✕↔back, play↔pause) | `.contentTransition(.symbolEffect(.replace))` | iOS 17+ | Shared strokes animate, the rest cross-fades — the SF-Symbols-native morph. |

Notes:
- Currency: `Text(price, format: .currency(code: "USD")).contentTransition(.numericText(value: price))`.
- Always gate motion behind `@Environment(\.accessibilityReduceMotion)` and collapse to a fade.
- Full detail and sources: [ios-techniques.md](ios-techniques.md).

**Demonstrated in Family clips:** the isolated `Continue`→`Confirm` shared-letter morph (dev playground), the live `Add N Wallets` CTA, the send-flow button morph with value carry-over, and the odometer/comma amount field in the send keypad.
