# Applying benji — process, the "max pain" test, and review

How to actually use these values when building or reviewing, plus the decision rules and anti-patterns.

## The order of operations

1. **Map the space before you animate it.** Fluidity "relies on us deeply understanding the app's navigation. We need to know how and why a transition makes sense before adding it." Sketch the flow as a *space*: which screens are rooms, which are doorways, what travels between them, where the user is at each step. Only then choose motion.
2. **Decide tray vs. full screen** ([simplicity-tray-system.md](simplicity-tray-system.md)). Transient/confirmational/warning/single-purpose → tray. Dwell-in/return-to → full screen.
3. **Give every transition a job.** For each animation, write the A→B sentence it explains ("this slide tells the user the confirm screen came *from* the amount they entered"). If you can't write it, cut the animation.
4. **Find the things that persist and make them travel** — never duplicate a component that's already on screen and will remain.
5. **Place delight on the curve** ([delight-selective-emphasis.md](delight-selective-emphasis.md)): turn intensity *up* on rare/weighty moments, *down* on daily ones, and hide easter eggs where they'll be discovered, not endured.
6. **Audit polish breadth.** Walk the least-used corners. If any feel like the "dirty bathroom," fix them *before* adding new delight anywhere — inconsistent polish undermines all of it.
7. **Layer the feel.** Add a tuned haptic (and where apt, sound) to meaningful gestures. Smoothness + feedback is the trust signal.

## The "max pain" test (the best review technique here)

The essay's most convincing argument *removed* fluidity to expose its value. Use the same move to evaluate your own work:

> Build (or imagine) the **static version** of an interaction side-by-side with the fluid one. Watch both. If the static version causes "digital whiplash," loses the sense of connection, or makes the action's *intent and outcome less clear* — the motion is doing real work and should stay. If removing it changes nothing, the motion was decoration; cut it.

This separates purposeful motion from noise, and it's a cheap, decisive A/B you can run in your head or in a branch.

## Review checklist

Simplicity
- [ ] Are fundamentals immediately reachable, with complexity revealed only as it becomes relevant?
- [ ] Does each tray hold exactly **one** content or **one** action?
- [ ] Does each *successive* tray **vary in height** so progression is unmistakable?
- [ ] Title + leading icon present? Icon correctly = dismiss (first) or back (sequence)?
- [ ] Does the tray preserve the prior context behind it instead of displacing the user?
- [ ] Does the tray's theme match its flow's context?

Fluidity
- [ ] Does every transition explain an A→B navigation? (No motion-for-motion's-sake.)
- [ ] Do persistent elements **travel** (shared element) rather than duplicate/re-animate?
- [ ] Is directionality meaningful (left tab → from left; forward → from trailing edge)?
- [ ] Do labels/numbers that change **morph** (Continue→Confirm, odometer) instead of cutting?
- [ ] Do status indicators **move** to where their result will live?
- [ ] Do springs feel interruptible and alive (not fixed-duration eases on gestures)?

Delight
- [ ] Is delight intensity matched to feature frequency (Delight-Impact Curve)?
- [ ] Are rare/weighty moments (first setup, successful backup) given real ceremony?
- [ ] Are everyday interactions kept *subtle* (no fatigue)?
- [ ] Are easter eggs placed where they'll surprise, not annoy?
- [ ] Is polish uniform enough across rarely-used features to justify any delight at all?

Respect / fundamentals
- [ ] Do the interactions read as "the app understands my intent"?
- [ ] Are utility, performance, and security intact — never traded for polish?
- [ ] Is motion gated behind reduce-motion, with sensible fallbacks?

## Anti-patterns (explicitly avoid)

- **Motion for its own sake.** If it doesn't clarify navigation, it's noise.
- **Duplicate animations.** A component already on screen re-animating a fresh copy during a transition — the essay's stated pet peeve.
- **Equal-height successive trays.** Reads as "nothing changed."
- **Two jobs in one tray.** Breaks the focus that makes trays approachable.
- **Full screens for transient confirmations/warnings** — they displace the user and kill context.
- **Cutting a meaningful label/number change** — morph it so the user registers it.
- **Uniform delight everywhere** — daily features over-animated become tiresome; rare features left plain are a missed opportunity. Vary the intensity.
- **Delight on top of an unpolished base** — the dirty-bathroom problem; fix breadth first.
- **Glitchy/janky animation on critical actions** — actively erodes trust (the banking-app counter-example).
- **Trading correctness/performance/security for feel** — these are table stakes underneath everything.
- **(iOS) Liquid Glass in the content layer / glass-on-glass** — violates the HIG; glass is the functional/navigation layer only.

## Accessibility & restraint

Fluidity and delight must degrade gracefully. Honor reduce-motion (collapse to smooth fades or static states), respect the mute switch for sound, keep stealth/redaction shimmers slow and "private" rather than "loading," and stop indefinite effects offscreen. Restraint *is* part of the craft — "knowing where, when, and **how**" includes knowing when to do less.

## A note on scale

The fluid feeling is emergent: "the result of hundreds [the author corrects himself: *thousands*] of small, deliberate decisions woven together. A single fluid transition, while worth adding, doesn't alone create a fluid interface." Don't expect one hero animation to deliver the feeling. Budget for the obsessive accumulation of small choices — that *is* the work.
