---
name: design-system-doc-refresh
description: Refresh Admin Next design-system documentation and agent skills against the current app/ui implementation. Use when asked to review, update, sync, audit, or reconcile .agents/skills/design-system, app/ui/skills/ds-component, app/ui/README.md, app/ui/DESIGN.md, docs/design-system, or design-system component docs.
---

# Design System Doc Refresh

Use this skill to do the exact maintenance pass for Admin Next design-system docs: compare the live `app/ui` implementation against source docs, update stale guidance, regenerate generated copies, and verify no old token/component references remain.

## Scope

Primary source files:

- `app/ui/README.md`
- `app/ui/DESIGN.md`
- `app/ui/components/*/*.tsx`
- `app/ui/components/*/*.md`
- `app/routes/design-system/catalog.tsx`
- `app/ui/tokens/*.css`
- `app/ui/skills/ds-component/SKILL.md`
- `app/ui/skills/ds-component/references/*.md`
- `.agents/skills/design-system/SKILL.md`
- `.agents/skills/design-system/references/*.md`

Generated outputs:

- `.agents/skills/design-system/components/*.md`
- `.agents/skills/design-system/references/design.md`
- `docs/design-system/DESIGN.md`

## Workflow

1. Inspect the current UI package:
   - List `app/ui/components/*` implementation modules and component Markdown docs.
   - Read `app/routes/design-system/catalog.tsx` to distinguish reusable modules from visual catalog entries.
   - Read `app/ui/tokens/*.css` and `app/App.css` for current token names, values, utilities, and global rules.

2. Compare docs to implementation:
   - Check `app/ui/README.md` for current reusable modules, source-of-truth paths, catalog guidance, and sync instructions.
   - Check `.agents/skills/design-system/SKILL.md` for component inventory and component-selection guidance.
   - Check `app/ui/skills/ds-component/SKILL.md` and references for current component creation patterns.
   - Check `app/ui/DESIGN.md` for stale token values, removed token names, old color families, obsolete component descriptions, and mismatched button/input/select guidance.
   - Check `app/routes/design-system/catalog.tsx` for `ComponentEntry` coverage of reusable app-facing modules that should be discoverable.
   - Compare reusable component modules with `app/ui/components/*/*.md` and identify missing source docs before syncing generated copies.

3. Add missing component docs:
   - For every reusable app-facing component module, ensure a source doc exists at `app/ui/components/{name}/{name}.md`.
   - Create missing source docs beside the implementation before running `bun run docs:design-system`; do not edit generated component docs by hand.
   - Use the compact component-reference format already used by nearby docs: title, import, props, use/do/don't guidance, key behavior notes, and a short example.
   - Base each new doc on the live `.tsx` implementation, not only on catalog summaries.
   - It is fine to skip utility helpers because they are not component docs.

4. Update the visual catalog:
   - Compare reusable component docs with `name` entries in `catalog.tsx`.
   - Add missing imports and catalog entries for reusable components that should be discoverable.
   - Keep previews small, realistic, and interactive when the component behavior matters.
   - Use local preview components with `useState` for controlled examples such as selects, comboboxes, switches, checkboxes, and editable input buttons.
   - It is fine to omit utility helpers from catalog entries because they are not visual components.

5. Patch only documentation, skill guidance, and catalog previews unless the user explicitly asks for component implementation changes.

6. Sync generated docs after source doc edits:

```bash
bun run docs:design-system
```

7. Re-check generated files:
   - Generated component docs may change if source component docs had drifted.
   - Newly added source component docs should appear in `.agents/skills/design-system/components/*.md` after sync.
   - `.agents/skills/design-system/references/design.md` and `docs/design-system/DESIGN.md` should mirror `app/ui/DESIGN.md`.

## Current Checks

Use these searches after editing to catch old guidance:

```bash
rg "bg-brand|--color-brand|#57c518|bg-card|text-card|shadow-card|bg-accent|text-accent|--card|--accent|--input|text-md|--text-md|border-input|--fs-" app/ui .agents/skills/design-system docs/design-system
```

Also run:

```bash
git diff --check
bun run typecheck
```

Use `ReadLints` for edited Markdown and TypeScript-adjacent files when available.

## Alignment Rules

- `app/ui/README.md` and `.agents/skills/design-system/SKILL.md` should agree on reusable component modules and direct import guidance.
- `app/routes/design-system/catalog.tsx` should include visual entries for reusable public components when they should be discoverable; utility helpers are expected non-visual exceptions.
- Component docs live beside implementations in `app/ui/components/*/*.md`; every reusable app-facing component module should have one before generated agent copies are synced with `bun run docs:design-system`.
- `app/ui/DESIGN.md` is the source for `docs/design-system/DESIGN.md` and `.agents/skills/design-system/references/design.md`.
- Token guidance must match `app/ui/tokens/*.css`; do not preserve references to tokens that no longer exist.
- Prefer Tailwind classes and `cn()` guidance used by current components. Do not instruct agents to use old inline `--fs-*` patterns.
- Use `Icon` from `~/ui/components/icon/icon` and the Nucleo manifest for app-facing icons; do not recommend raw `iconify-icon` for DS components.
- Interactive components should wrap Base UI primitives when available and let Base UI own state, ARIA, focus, keyboard behavior, portals, and transition lifecycle.
- Use Base UI `data-starting-style` and `data-ending-style` for popup, portal, and collapsible transitions.

## Final Response

Summarize:

- Source docs updated.
- Generated docs synced.
- Any generated component docs that changed because source docs had drifted.
- Verification commands/results.
