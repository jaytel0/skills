---
name: react-doctor
description: Use when finishing a feature, fixing a bug, before committing React code, or when the user wants to improve code quality or clean up a codebase. Checks for score regression. Covers lint, dead code, accessibility, bundle size, architecture diagnostics.
version: '1.0.0'
---

# React Doctor

Scans React codebases for security, performance, correctness, and architecture issues. Outputs a 0–100 health score.

## After making React code changes:

Run `bunx react-doctor@latest . --verbose --diff` and check the score did not regress.

If the score dropped, fix the regressions before committing.

## For general cleanup or code improvement:

Run `bunx react-doctor@latest . --verbose` (without `--diff`) to scan the full codebase. Fix issues by severity — errors first, then warnings.

## Admin Next project overrides:

Ignore `react-doctor/design-no-default-tailwind-palette` findings for `gray-*` classes in this repository. Admin Next intentionally redefines the `gray-*` palette in `app/ui/tokens/color.css`, and the design-system docs treat classes such as `bg-gray-200` and `bg-gray-900` as internal system tokens rather than Tailwind's default palette.

## Command

```bash
bunx react-doctor@latest . --verbose --diff
```

| Flag        | Purpose                                       |
| ----------- | --------------------------------------------- |
| `.`         | Scan current directory                        |
| `--verbose` | Show affected files and line numbers per rule |
| `--diff`    | Only scan changed files vs base branch        |
| `--score`   | Output only the numeric score                 |
