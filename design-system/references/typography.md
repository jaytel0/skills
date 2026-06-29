# Typography

Typography is defined in `app/ui/tokens/typography.css`. The app uses Shopify Inter via `@font-face`; do not introduce a second display family for routine UI.

## Scale

| Class / token               | Size | Line height | Use for                              |
| --------------------------- | ---- | ----------- | ------------------------------------ |
| `text-xs` / `--text-xs`     | 11px | 14px        | Fine labels and tertiary metadata    |
| `text-sm` / `--text-sm`     | 12px | 18px        | Badges, small labels, compact tabs   |
| `text-base` / `--text-base` | 13px | 20px        | Default UI text, inputs, table cells |
| `text-lg` / `--text-lg`     | 15px | 22px        | Prominent body and section labels    |
| `text-xl` / `--text-xl`     | 17px | 24px        | Compact titles                       |
| `text-2xl` / `--text-2xl`   | 22px | 26px        | Section titles                       |
| `text-3xl` / `--text-3xl`   | 26px | 32px        | Page titles                          |
| `text-4xl` / `--text-4xl`   | 29px | 36px        | Rare display titles                  |

## Weights

| Class / token                          | Value | Use for                    |
| -------------------------------------- | ----- | -------------------------- |
| `font-book` / `--font-weight-book`     | 400   | Body text and descriptions |
| `font-medium` / `--font-weight-medium` | 500   | Labels, controls, metadata |
| `font-bold` / `--font-weight-bold`     | 600   | Headings and emphasis      |

## Usage

```tsx
<h1 className="text-3xl font-bold">Page title</h1>
<h2 className="text-lg font-medium">Section title</h2>
<p className="text-base text-muted-foreground">Description text</p>
```

## Do / Don't

```tsx
// Don't add one-off type values when a token exists.
<p className="text-[13px] leading-[20px]" />

// Do use the DS type scale.
<p className="text-base" />
```
