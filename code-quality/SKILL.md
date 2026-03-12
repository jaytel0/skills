---
name: code-quality
description: Post-implementation retrospective and refactoring pass. Reviews conversation history to synthesize what was learned during iteration, then refactors the final code for elegance, repo consistency, and future extensibility. Trigger on "code quality", "refactor pass", "clean up what we built", "polish this", or after any substantial build.
---

# Code Quality Retrospective

Final quality pass after substantial implementation work. The core question: **knowing what you know now, how would you refactor this?**

## 1. Mine the Conversation

Review the full conversation history. Extract what was built, how the approach evolved, dead ends and why they failed, and patterns that crystallized mid-build. Identify the "knowledge delta" — everything known now that wasn't known at the start.

## 2. Audit Every Touched File

Re-read all created or modified files. Evaluate each against:

- **Structure** — Single responsibility? Right abstraction level? Understandable without conversation context?
- **Repo consistency** — Follows existing patterns, naming, conventions? Existing utilities that could replace custom iteration code?
- **Iteration artifacts** — Stale workarounds, dead code, drifted names, unresolved TODOs, over-defensive debugging leftovers?
- **Extensibility** — Next feature in this area easy to add? Types flexible but not over-engineered? Hard-coded values that should be configurable?

## 3. Present Refactor Plan

Organize by priority — **Critical** (bugs, wrong abstractions, broken consistency), **Structural** (extract/consolidate/align with repo patterns), **Polish** (naming, simplification, tighter types). For each item: what it is now, why it ended up that way, and what it should become.

Wait for user approval before executing.

## 4. Execute and Summarize

Implement in reviewable chunks with verification after each. Surface deeper issues rather than silently working around them. Finish with what changed, how it positions the repo for future work, and anything intentionally deferred.

## Principles

- **Respect the iteration** — conversation is context, not mistakes.
- **The repo is the unit** — every change makes the wider codebase more coherent.
- **Elegance is clarity** — communicate intent, don't impress.
- **Delete more than you add** — fewer moving parts for the same behavior.
- **Existing patterns win** — consistency compounds.
