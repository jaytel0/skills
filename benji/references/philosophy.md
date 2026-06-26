# Philosophy — the three values and why they work

Family's design philosophy is not a style; it is a stance toward the person using the software. The essay frames it bluntly: *"This is about how we made something complex feel welcoming."* Crypto wallets are intimidating, branching, high-stakes. Family's bet was that **thoughtful interaction design creates an emotional bond** between a user and technology — and that the bond is built on respect for the user's **time, intelligence, and feelings**.

Three values carry that stance.

## Simplicity → accessibility

Most complex products show everything at once: every feature, all the time, whether or not you need it. Family inverts this. **The fundamentals are at your fingertips; everything else appears as it becomes relevant.** This is *gradual revelation*.

Why it works: it protects the newcomer without amputating power for the expert. Family has *hundreds* of potential paths (onboarding alone is a thicket of edge cases). Showing them all would turn a critical flow into a daunting one. Revealing them in sequence keeps cognitive load low and the next step obvious.

The mechanism is the **dynamic tray system** → [`simplicity-tray-system.md`](simplicity-tray-system.md).

## Fluidity → continuity of experience

The team adopted fluidity after watching how **static transitions disrupt orientation**. A cut from screen A to screen B asks the user to rebuild their mental model each time. Worse, *"a lifeless product feels like a dead product, and a dead product feels uncared for."*

Two metaphors define the target feeling:

- **Moving through water.** A fluid interface is one you *float* through rather than walk through. Screens, components, and features have *visible links* between them.
- **A physical space with unbreakable rules.** The app is treated as coherent and dimensional. *"We treat the app as having unbreakable physical rules when navigating its space."* Every movement is a logical step forward, the way moving through a building is.

The discipline this imposes: **every animation must have an architectural purpose** — it explains the user's path from A → B. Motion is never added "for the sake of it." You must understand *how and why* a transition makes sense before you add it.

Mechanisms → [`fluidity-transitions.md`](fluidity-transitions.md) and [`fluidity-text-morphing.md`](fluidity-text-morphing.md).

## Delight → emotional connection

Delight is *"more than just adding fun interactions. It's about creating moments that resonate on a personal level — making software feel more human and responsive."* It says to the user: we value how you *feel* doing this, not just what you do.

The essay is careful: delight is subjective, and styles vary. The important part is *"the deliberate effort to try and make something special at all."*

Mechanism and the discipline of restraint → [`delight-selective-emphasis.md`](delight-selective-emphasis.md).

## The Delight-Impact Curve

The single most actionable idea in the essay. **The potential value of delight increases as the frequency of a feature's usage decreases.**

```
delight
 value  ^
        |  *                     rare, high-ceremony moments
        |    *                   (first wallet setup, successful backup)
        |       *
        |          *  *
        |                *  *  *  frequently-used features
        +-----------------------------> feature usage frequency
         rare                    constant
```

- **Rare, weighty moments** (setting up a wallet, completing a backup) are where lavish delight pays off most — they're memorable *because* they're infrequent, and surprise/novelty amplify the effect. Don't streamline them into something mundane.
- **Frequent moments** (typing a send amount) get delight too, but *subtle* delight — overdoing a daily interaction makes it tiresome. *"Eating your favourite candy will get progressively less enjoyable with each piece."* So: commas that gently shift as you type, not confetti every transaction.

Apply delight with **varying intensity**, never as an afterthought, and lean into **surprise** — which is why hidden easter eggs (the QR ripple) feel delightful rather than annoying: they're encountered just often enough to be a pleasant discovery, not a nuisance.

> Footnote worth keeping: Rauno Freiberg's [essay on interaction design](https://rauno.me/craft/interaction-design) develops the same frequency-vs-novelty idea.

## Polish everywhere — the precondition

Delight anywhere depends on consistency everywhere. Users notice when parts of an app are less polished, and it *"detracts from the overall experience."* The quip (credited to Paco): an unpolished corner is *"like going to a fancy restaurant but finding it has a dirty bathroom."* Give every part of the app — however rarely used — the same holistic design approach. Every interaction is a *potential* moment for delight; you can only realize that potential if the baseline is uniformly high.

## Respect is the throughline

Fluid, consistent interactions carry a subconscious reassurance: *"the app is in tune with my intentions."* The message is *"I know exactly what you need — let me get that for you…"* The counter-example the essay gives: a banking app that shows a glitchy animation while loading your balance erodes trust — you start to wonder if it even understands what you asked. **Smoothness is a trust signal.**

## "Family comes first" — the honest trade-offs

Adopting these values cost time-to-launch. The team accepted that, betting that a world-class experience was how they'd stand out. Two caveats to carry:

1. **Table stakes still apply.** Simplicity, fluidity, and delight sit *on top of* utility, performance, and security — not instead of them. The essay spends little time on those only because their importance is already universally acknowledged. A delightful wallet that loses funds is a failure. Never trade correctness, performance, or security for polish.
2. **Fluidity demands obsessiveness.** Achieving it *"demands a certain obsessiveness."* It is the compound of thousands of tiny decisions, not one hero animation. Budget for that, or don't promise the feeling.

The destination, stated plainly: software that is *"not just a tool but a familiar, friendly companion"* — that goes "above and beyond what someone might expect."
