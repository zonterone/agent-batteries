---
name: code-comments
description: Decides which code comments survive, by classifying each as Tautology, History, Stale, Excuse or Decision Record. MUST be consulted whenever writing, changing or reviewing code in any language, and before presenting a code change. Invoke with `sweep [path]` to clean up the comments already in a codebase.
argument-hint: "sweep [path]"
---

# Code comments

Code reads without comments: names, types, tests and structure carry the facts. A comment is a fact that has not found its home yet, so every comment gets a **class**, and the class decides where the fact goes.

## Classes

| Class | The comment... | Action |
| --- | --- | --- |
| **Tautology** | says what the code already shows: narration, a restated name or signature, a section divider | Delete. When it explains a poorly named thing, rename or extract so the name says it |
| **History** | describes the change instead of the code: "now uses X", "fixed for...", "added per review" | Move it to the commit message |
| **Stale** | refers to code or behaviour that no longer exists | Delete |
| **Excuse** | covers for a hack: "workaround", "temporary", "for now" | Fix the hack when the fix is inside the task. Otherwise leave one `TODO` line naming the real fix, and list it in your final report |
| **Decision Record** | holds a _why_ without which a competent reader would undo the code | Keep, by the rules below |

## Decision Records

A Decision Record is the one class that stays as prose. Before keeping one, try the homes that outrank a comment:

- a name, for the meaning or the unit of a value
- a type, for a constraint
- a test or an assertion, for an invariant or an expected behaviour

What is left is a Decision Record: the cause of a workaround, a constraint that comes from outside the code, a link to the bug or spec behind a choice.

- It stays as one line directly above the code it explains, stating the reason for the code as it stands.
- In a project that already keeps ADRs, a decision of the weight its existing ADRs record moves into a new ADR in the project's format instead.

## Doc comments

Doc comments (JSDoc, docstrings, godoc, rustdoc) belong to a project that already carries them. There, new API gets one in the project's format, holding only what the signature cannot express: units, side effects, errors, an example. One that restates the signature is a Tautology.

In a project whose code carries none, a doc comment is an ordinary comment and takes a class: a unit goes into a name, an error into a type.

## Left unclassified

- machine-read comments: shebangs, pragmas and directives (`eslint-disable`, `@ts-expect-error`, `# type: ignore`, `//go:build`), license headers
- the `TODO` on a stub, a mock or an unwired handler: one line naming what completes it

## Whose comments

The classes govern the comments your change adds. A comment already in the code stays as it is, unless your change makes it Stale: then update or delete it. Cleaning up existing comments is the sweep.

## Audit your diff

Before presenting a code change, inventory the comments it added with a command, never from memory:

```bash
git diff HEAD -U0 -- . ':(exclude)*.md' | grep -E '^\+\+\+ |^\+\s*(//|/\*|\*( |/|$)|#( |!|$)|--( |$)|<!--|\{/\*)|^\+.*\s(//|#)\s'
```

Diff against the base branch instead when the task has commits of its own. Files you created and left untracked are absent from the diff: grep them whole. A hit is a candidate: URLs and strings match too.

The change is ready when every hit has a class, and every comment still in the diff is a Decision Record, a `TODO`, a doc comment that passes, or machine-read.

## Sweep

When invoked with `sweep`, or asked to clean up the comments already in a codebase, read [sweep.md](sweep.md) and follow it.
