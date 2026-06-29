# Surfaces

Borders, radius, and elevation are tokenized in `app/ui/tokens/border.css` and `app/ui/tokens/shadow.css`.

## Radius

| Class / token                  | Value | Use for                       |
| ------------------------------ | ----- | ----------------------------- |
| `rounded-xs` / `--radius-xs`   | 4px   | Micro corners                 |
| `rounded-sm` / `--radius-sm`   | 6px   | Select and combobox triggers  |
| `rounded-md` / `--radius-md`   | 10px  | Inputs and compact surfaces   |
| `rounded-lg` / `--radius-lg`   | 12px  | Icon affordances and popovers |
| `rounded-xl` / `--radius-xl`   | 14px  | Dialogs                       |
| `rounded-2xl` / `--radius-2xl` | 16px  | Cards and larger containers   |
| `rounded-full`                 | pill  | Buttons, badges, switches     |

The app base layer applies `corner-shape: superellipse(1.333)` globally. `rounded-full` unsets the corner shape for true pills.

## Shadows

| Class / token                         | Use for                            |
| ------------------------------------- | ---------------------------------- |
| `shadow-xs`                           | Low control elevation              |
| `shadow-sm`                           | Cards and quiet surfaces           |
| `shadow-md`                           | Medium emphasis                    |
| `shadow-lg`                           | Menus and popovers                 |
| `shadow-xl` / `shadow-2xl`            | Dialogs and high-focus overlays    |
| `shadow-input` / `shadow-input-focus` | Input, select, and combobox states |
| `shadow-button-primary`               | Primary buttons                    |
| `shadow-button-elevated`              | Header buttons                     |

## Usage

```tsx
<div className="rounded-2xl border bg-background shadow-sm" />
<Input className="shadow-input" />
<Popover.Popup className="rounded-lg bg-background shadow-lg" />
```

## Do / Don't

```tsx
// Don't hardcode one-off shadow and radius values for reusable surfaces.
<div style={{ borderRadius: 16, boxShadow: '0 8px 20px rgb(0 0 0 / 12%)' }} />

// Do use the DS classes or token variables.
<div className="rounded-2xl shadow-sm" />
<div style={{ borderRadius: 'var(--radius-2xl)', boxShadow: 'var(--shadow-sm)' }} />
```
