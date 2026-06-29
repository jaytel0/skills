# Layout & Spacing Guide

Admin Design System components own their internal layout. Screens and composition helpers should use Tailwind spacing utilities from the repo scale, with `gap-*` for flex and grid spacing.

## Scale

| Utility            | Value | Common use                           |
| ------------------ | ----- | ------------------------------------ |
| `gap-1`, `p-1`     | 4px   | Tight inner padding, icon gaps       |
| `gap-1.5`, `p-1.5` | 6px   | Compact control gaps                 |
| `gap-2`, `p-2`     | 8px   | Related inline elements              |
| `gap-3`, `p-3`     | 12px  | Compact card padding                 |
| `gap-4`, `p-4`     | 16px  | Standard section rhythm              |
| `gap-5`, `p-5`     | 20px  | Dialog/card padding                  |
| `gap-6`, `p-6`     | 24px  | Large section spacing                |
| `gap-8`, `p-8`     | 32px  | Page-level spacing                   |
| `h-control`        | 30px  | Buttons, menu items, select triggers |
| `--toolbar-height` | 54px  | Main toolbar height                  |

## Patterns

```tsx
<div className="flex flex-col gap-4">
  <Section />
  <Section />
</div>
```

```tsx
<div className="flex flex-wrap items-center gap-2">
  <Button>Save</Button>
  <Button variant="secondary">Cancel</Button>
</div>
```

```tsx
<div className="grid grid-cols-[minmax(0,1fr)_20rem] gap-6">
  <main />
  <Aside.Root />
</div>
```

## Rules

- Use `gap-*` for internal flex/grid spacing; avoid `space-x-*` and `space-y-*`.
- Use `minmax(0, 1fr)` for flexible grid columns so content can shrink.
- Prefer `size-*` for square dimensions.
- Prefer `min-h-dvh` over `min-h-screen`.
- Keep route layout styles local unless they are reusable component behavior.
