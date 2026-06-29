# Color Guide

Admin Design System color lives in `app/ui/tokens/color.css` and is exposed through Tailwind v4 theme variables. Prefer component props first, then semantic Tailwind classes, then token variables for custom CSS.

## Semantic Classes

| Class                                    | Use for                                           |
| ---------------------------------------- | ------------------------------------------------- |
| `bg-background` / `text-foreground`      | Default surfaces and text                         |
| `bg-muted` / `text-muted-foreground`     | Subtle panels, disabled regions, secondary copy   |
| `bg-primary` / `text-primary-foreground` | High-emphasis neutral fills and selected emphasis |
| `text-secondary`                         | Secondary labels and descriptions                 |
| `bg-destructive`                         | Destructive and error emphasis                    |
| `border`                                 | Standard borders and dividers                     |
| `ring-ring`                              | Focus rings                                       |
| `text-ai` / `bg-ai`                      | AI-specific emphasis                              |

## Palette

The curated palette includes gray, blue, green, red, and purple. Do not introduce a new color family inside a component unless the token file is updated too.

| Token                                    | Value         | Use for                                   |
| ---------------------------------------- | ------------- | ----------------------------------------- |
| `--color-gray-50` ... `--color-gray-950` | neutral scale | Surfaces, borders, text, neutral controls |
| `--color-blue-*`                         | blue scale    | Focus and informational state             |
| `--color-green-*`                        | green scale   | Success and active state                  |
| `--color-red-*`                          | red scale     | Error and destructive state               |
| `--color-purple-500` / `--ai`            | `#7D5AFF`     | AI-specific emphasis                      |

## Usage

```tsx
<div className="bg-background text-foreground">
  <p className="text-muted-foreground">Secondary text</p>
  <div className="border bg-muted">Subtle region</div>
</div>
```

```css
.custom-surface {
  background: var(--color-background);
  color: var(--color-foreground);
  border-color: var(--color-border);
}
```

## Do / Don't

```tsx
// Don't introduce raw component colors.
<p style={{ color: '#1a1a1a' }} />

// Do use semantic classes or token variables.
<p className="text-foreground" />
<p style={{ color: 'var(--color-foreground)' }} />
```
