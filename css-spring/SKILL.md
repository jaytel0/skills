---
name: css-spring
description: >
  Generate CSS springs as linear() easing curves and durations. Triggers: css spring, spring easing, linear(), bounce, css easing, spring css
argument-hint: "[bounce and duration, e.g. 'bounce 0.3 duration 0.8']"
---

## Usage

Generate a CSS `linear()` easing function that simulates spring physics, usable in CSS `transition`, `transition-timing-function`, or `animation-timing-function`.

### Parameters

The spring accepts two configuration styles:

**Perceptual (preferred):**

- **bounce** (number, -1 to 1): How bouncy the spring is. 0 = no bounce, positive = overshoot, negative = underdamp. Default: 0.
- **duration** (number, seconds): Duration of the spring animation. Default: 0.8.

**Raw physics:**

- **stiffness** (number): Spring stiffness coefficient
- **damping** (number): Damping coefficient
- **mass** (number): Mass of the spring

### How CSS Spring Easing Works

CSS springs use the `linear()` easing function (CSS Easing Level 2) to approximate spring physics with a piecewise linear curve. The approach:

1. Compute the spring motion curve by solving the damped harmonic oscillator equation
2. Sample the curve at regular intervals (typically 20-40 points)
3. Encode the samples as a `linear()` function

### Spring Physics

The damped spring equation: `x(t) = e^(-ζωt) · (cos(ωd·t) + (ζω/ωd)·sin(ωd·t))`

Where:

- `ω = sqrt(stiffness / mass)` — natural frequency
- `ζ = damping / (2 · sqrt(stiffness · mass))` — damping ratio
- `ωd = ω · sqrt(1 - ζ²)` — damped frequency

Converting from perceptual parameters:

- `bounce` maps to damping ratio: `ζ = 1 - bounce` (bounce=0 → critically damped, bounce=1 → undamped)
- `duration` determines stiffness given the damping ratio

### Example Output

For `bounce: 0.3, duration: 0.6`:

```css
transition: transform 0.6s
  linear(
    0,
    0.0039,
    0.0157,
    0.0352,
    0.0625,
    0.0977,
    0.1407,
    0.1914,
    0.2499,
    0.316,
    0.3896,
    0.4703,
    0.5579,
    0.6519,
    0.7519,
    0.8574,
    0.9676,
    1.0819,
    1.1994,
    1.1994,
    1.1573,
    1.1152,
    1.0731,
    1.0311,
    1.0078,
    1.0039,
    1.0019,
    1.001,
    1
  );
```

### Common Presets

| Use Case       | bounce | duration | Character            |
| -------------- | ------ | -------- | -------------------- |
| Subtle settle  | 0      | 0.4      | Smooth, no overshoot |
| Button press   | 0.15   | 0.3      | Quick, slight pop    |
| Modal entrance | 0.25   | 0.5      | Noticeable bounce    |
| Playful pop    | 0.4    | 0.6      | Fun, bouncy          |
| Elastic snap   | 0.6    | 0.8      | Very springy         |
| Drawer slide   | -0.2   | 0.5      | Slightly sluggish    |

### Integration

The `linear()` easing works anywhere CSS easing is accepted:

```css
.modal {
  transition: transform 0.5s linear(/* spring points */);
}

@keyframes enter {
  from {
    transform: scale(0.9);
  }
  to {
    transform: scale(1);
  }
}
.modal {
  animation: enter 0.5s linear(/* spring points */);
}
```

The key advantage: spring animations in pure CSS without JavaScript runtime cost. The animation runs on the compositor thread (S-tier performance) when animating `transform`, `opacity`, `filter`, or `clip-path`.
