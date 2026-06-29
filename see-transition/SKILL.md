---
name: see-transition
description: >
  Visualise CSS easings and Motion transitions. Triggers on: see, visualise
argument-hint: "[easing to visualise, e.g. 'spring bounce 0.3' or 'cubic-bezier(0.4, 0, 0.2, 1)']"
---

## Usage

Visualise CSS easing functions and Motion transitions by generating ASCII curve previews.

### Spring Visualisation

For spring-based easing, render an ASCII representation of the spring curve:

- **bounce** (number, -1 to 1): How bouncy the spring is
- **duration** (number, seconds): Duration of the spring animation

Or raw physics parameters:

- **stiffness**, **damping**, **mass**

### Cubic Bezier Visualisation

For cubic-bezier easing, render an ASCII representation of the bezier curve:

- **x1**, **y1**, **x2**, **y2** (numbers): The four control points

### Detecting Intent

- "spring", "bounce", "stiffness", "damping" → spring visualisation
- "cubic-bezier", "bezier", "ease", "easeIn", "easeOut", "easeInOut" → cubic-bezier visualisation
- Named easings map to cubic-bezier: `ease` = `(0.25, 0.1, 0.25, 1)`, `easeIn` = `(0.42, 0, 1, 1)`, `easeOut` = `(0, 0, 0.58, 1)`, `easeInOut` = `(0.42, 0, 0.58, 1)`

### Examples

User: "Show me a bouncy spring"
→ Visualise spring with `bounce: 0.25, duration: 0.8`

User: "Visualise cubic-bezier(0.4, 0, 0.2, 1)"
→ Visualise cubic-bezier with `x1: 0.4, y1: 0, x2: 0.2, y2: 1`

User: "See easeOut"
→ Visualise cubic-bezier with `x1: 0, y1: 0, x2: 0.58, y2: 1`

### ASCII Curve Rendering

Generate a 40×20 character ASCII plot of the easing curve. The x-axis represents time (0 to 1), the y-axis represents progress (0 to 1, or beyond for springs with overshoot).

Example output for `easeOut (0, 0, 0.58, 1)`:

```
1.0 ┤                    ●●●●●●●●●●●●●●●●●●●●
    │              ●●●●●●
    │          ●●●●
    │        ●●
    │      ●●
    │     ●
    │    ●
    │   ●
    │  ●
    │ ●
0.0 ┤●
    └────────────────────────────────────────
    0.0                                    1.0
```

Example output for `spring (bounce: 0.3, duration: 0.6)`:

```
1.1 ┤              ●●●
    │            ●●   ●●
1.0 ┤          ●●       ●●●●●●●●●●●●●●●●●●●●
    │        ●●
    │       ●
    │      ●
    │    ●●
    │   ●
    │  ●
    │ ●
0.0 ┤●
    └────────────────────────────────────────
    0.0                                    1.0
```

### Spring Curve Computation

For spring curves, solve the damped harmonic oscillator:

`x(t) = 1 - e^(-ζωt) · (cos(ωd·t) + (ζω/ωd)·sin(ωd·t))`

Where:

- `ω = sqrt(stiffness / mass)` — natural frequency
- `ζ = damping / (2 · sqrt(stiffness · mass))` — damping ratio
- `ωd = ω · sqrt(1 - ζ²)` — damped frequency

For perceptual parameters: `ζ ≈ 1 - bounce`

### Cubic Bezier Curve Computation

For cubic-bezier, evaluate the parametric cubic bezier at evenly-spaced time values:

- `B(t) = 3(1-t)²t·P1 + 3(1-t)t²·P2 + t³` for each axis
- Use Newton's method to find parameter `t` for a given x value

Present the CSS declaration alongside the visualisation:

```css
transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
/* or */
transition-timing-function: linear(/* spring points */);
```
