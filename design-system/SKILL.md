---
name: design-system
description: 'Admin Design System — React component resources for admin-next screens. Import components from their direct app/ui component modules. Use React className and design-system components (DataTable, Badge, Section, etc.) instead of raw HTML equivalents.'
---

# Admin Design System

React component resources for admin-next. Components are consumed through direct component module imports; design-system-specific theme tokens live in `app/ui/tokens/*.css` and are imported directly by the global CSS manifest in `app/App.css`.

## Setup

```tsx
import { Badge } from '~/ui/components/badge/badge';
import { Button } from '~/ui/components/button/button';
import { DataTable } from '~/ui/components/data-table/data-table';
```

Do not import app code from the `~/ui` barrel. Use direct component paths such as `~/ui/components/button/button`, and use namespace imports from the direct module for compound components such as `import * as Table from '~/ui/components/table/table'`.

Use React JSX conventions: `className`, `htmlFor`, and typed component props from React.

## Component Priority

Use the design-system components listed here for common admin UI patterns.

`Accordion`, `AnimatedNumber`, `Aside`, `Avatar`, `Badge`, `Banner`, `Button`, `Checkbox`, `Collapsible`, `Combobox`, `DataTable`, `Dialog`, `Icon`, `Image`, `Input`, `InputButton`, `InputCombo`, `InputHero`, `InputStepper`, `Intent`, `Link`, `Map`, `Menu`, `MetricsBar`, `Popover`, `ProgressiveBlur`, `Radio`, `ResourcePreview`, `RichTextEditor`, `SearchBar`, `Section`, `SegmentedControl`, `Select`, `SettingsActionList`, `SharedPopover`, `ShimmerText`, `SidekickIcon`, `ShopifyLogo`, `Shortcut`, `Sparkline`, `StatusIndicator`, `Switch`, `Table`, `Toast`, `Tooltip`

## Components

| Component          | Tier   | Use for                                 |
| ------------------ | ------ | --------------------------------------- |
| `Accordion`        | Modern | Collapsible sections                    |
| `AnimatedNumber`   | Modern | Formatted numeric values                |
| `Aside`            | Modern | Right-rail summary and controls         |
| `Avatar`           | Modern | Compact people and identity markers     |
| `Badge`            | Modern | Status indicators                       |
| `Banner`           | Modern | Inline contextual notices               |
| `Button`           | Modern | Actions                                 |
| `Checkbox`         | Modern | Boolean toggle with checkbox affordance |
| `Collapsible`      | Modern | Expandable panels                       |
| `Combobox`         | Modern | Searchable select dropdowns             |
| `DataTable`        | Modern | Data tables with column config          |
| `Dialog`           | Modern | Modal dialogs                           |
| `Icon`             | Modern | Nucleo icon rendering                   |
| `Image`            | Modern | Product and collection media            |
| `Input`            | Modern | Text input with optional addons         |
| `InputButton`      | Modern | Button-shaped editable filter controls  |
| `InputCombo`       | Modern | Select and input combo fields           |
| `InputHero`        | Modern | Large borderless value inputs           |
| `InputStepper`     | Modern | Increment/decrement numeric controls    |
| `Intent`           | Modern | Scoped task overlays                    |
| `Link`             | Modern | Internal route navigation               |
| `Map`              | Modern | Static location previews                |
| `Menu`             | Modern | Context/action menus                    |
| `MetricsBar`       | Modern | Horizontal KPI summaries                |
| `Popover`          | Modern | Anchored floating content               |
| `ProgressiveBlur`  | Modern | Edge blur overlays                      |
| `Radio`            | Modern | Single selection from options           |
| `ResourcePreview`  | Modern | Hover previews for Admin resources      |
| `RichTextEditor`   | Modern | Paragraph rich text editing             |
| `SearchBar`        | Modern | Search with filter action               |
| `Section`          | Modern | Titled content surfaces                 |
| `SegmentedControl` | Modern | Compact single-choice toggle groups     |
| `Select`           | Modern | Dropdown selection                      |
| `SettingsActionList` | Modern | Icon-led settings rows                 |
| `SharedPopover`    | Modern | Shared hover/focus popover shell        |
| `ShimmerText`      | Modern | Animated text highlight                 |
| `SidekickIcon`     | Modern | Animated Sidekick identity mark         |
| `ShopifyLogo`      | Modern | Shopify brand mark                      |
| `Shortcut`         | Modern | Compact keyboard shortcut hints         |
| `Sparkline`        | Modern | Inline trend chart                      |
| `StatusIndicator`  | Modern | Active/inactive availability indicator  |
| `Switch`           | Modern | Boolean toggle with switch affordance   |
| `Table`            | Modern | Basic tables                            |
| `Toast`            | Modern | Transient notification stack            |
| `Tooltip`          | Modern | Hover/focus information                 |

## Styling Rules

- DS components handle their own styles. Prefer props and composition over external overrides.
- Use `className` when customization is necessary.
- Prefer DS components over raw HTML for common admin patterns.
- Use `Section` for any titled content group, rounded section-like content surface, notice card, grouped settings surface, or section-level action header. Pass `title`, `subtitle`, and `action` to `Section` instead of hand-rolling `<section>` wrappers, `<h2>` headers, header flex rows, or action menus outside the component. Use `wrapperContent` with `wrapperBg` when the surface needs a wrapper background behind flush child rows. Do not pass `bleed` and then add `p-5`, `px-5`, or equivalent default padding to the only child; omit `bleed` for normal padded sections and reserve it for flush tables, edge-to-edge media, or intentionally custom internal spacing. Do not hand-roll `rounded-* shadow-*` wrappers when `Section` can provide the surface.
- Use semantic variants (`Badge variant="success"`) instead of custom color spans.
- For layout in app screens, keep styles simple and consistent with the host route.
- Do not add `min-w-0` or `min-h-0` Tailwind reset classes. `app/App.css` already applies `min-width: 0` and `min-height: 0` globally in the base layer.
- For route-specific screen sections, extract substantive UI into a component folder that mirrors the route/domain structure and keep `app/routes/**` focused on state, toolbar wiring, data lookup, and composition. Keep toolbars and sections nested inside the matching feature folder, such as `app/components/settings/checkout/checkout-toolbar.tsx`; use the same nested pattern for other route domains instead of adding loose files at the domain root.
- In `app/components/settings/**`, use `SettingsActionList` from `~/ui/components/settings-action-list/settings-action-list` for repeated icon-led setting rows with titles, descriptions, values, navigation affordances, or switches. Do not hand-roll custom row, divider, and switch markup unless the interaction or layout is materially different from the shared action-list pattern.
- For component variants, sizes, or reused style groups, compose classes with module-level base strings and variant/size maps like `Badge` and `Button` do. Keep one-off structural classes inline in JSX.
- Avoid long inline conditional class lists inside JSX; extract only when it removes real repetition or clarifies a variant API.
- Do not add runtime compatibility aliases or package-server assumptions.

## Base UI Rules

- Wrap the matching `@base-ui/react` primitive for interactive components.
- Let Base UI own controlled/uncontrolled state, focus management, ARIA, keyboard navigation, and portal mounting.
- Use Base UI `data-starting-style` and `data-ending-style` attributes for enter/exit transitions.
- Do not add local controlled-state wrappers around Base UI roots.

## Component Selection

Before writing raw HTML for data display, check the component table:

- Tabular data: `DataTable` or `Table`
- Status labels: `Badge`; active/inactive presence: `StatusIndicator`
- Titled content groups, section headers with actions, grouped content surfaces, notice cards, and rounded shadowed page cards: `Section`; right-rail summaries: `Aside`
- Searchable or creatable selection: `Combobox`
- Filter/action chips with editable values: `InputButton`
- Mixed select/text fields: `InputCombo`
- Settings rows with icons, descriptions, values, chevrons, or switches: `SettingsActionList` from `~/ui/components/settings-action-list/settings-action-list`
- Icons: `Icon` for app-facing icon usage where the icon is available in the manifest.
- Form fields with labels/errors: no modern field wrapper exists yet; pair semantic labels and error text with `Input`, `Select`, or other controls in the local route.

## Component Docs

Read `.agents/skills/design-system/components/<name>.md` for component props and examples when the file exists. These files are generated from `app/ui/components/*/*.md`; after changing component docs or `app/ui/DESIGN.md`, run `bun run docs:design-system`. Some exported app-specific helpers may not have generated docs yet; inspect their implementation before treating them as reusable primitives.

## References

Read `.agents/skills/design-system/references/design.md` for the full Admin Next style reference. Read `layout.md`, `color.md`, `typography.md`, and `surfaces.md` for focused layout and token guidance.
