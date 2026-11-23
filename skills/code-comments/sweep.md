# Sweep

Cleans up the comments already in a codebase, using the classes in [SKILL.md](SKILL.md). The scope is the path given with the argument, or the whole repository when none is given.

## 1. Inventory

List every candidate with a command:

```bash
rg -n --no-heading -g '!*.md' -g '!*.lock' '^\s*(//|/\*|\*( |/|$)|#( |!|$)|--( |$)|<!--|\{/\*)|\s(//|#)\s' <path>
```

A hit is a candidate: drop URLs, strings and other non-comments as you read. When the list is too long for one pass, take it directory by directory and name the directories left over in the report.

Done when every hit in scope is either dropped as a non-comment or carried into step 2.

## 2. Classify

Give every candidate a class from SKILL.md, or mark it as left unclassified. Before classing a comment as Stale, search for what it refers to and confirm it is gone.

## 3. Act

- **Tautology, History, Stale**: delete. When a Tautology explains a poorly named local, rename the local so the name carries the fact. Leave an exported name as it is and list it in the report.
- **Excuse**: rewrite as one `TODO` line naming the real fix. Fixing the hack is outside a sweep.
- **Decision Record**: trim to one line above the code, or move it to an ADR, by the rules in SKILL.md.
- **Doc comment**: delete one that restates the signature, keep the rest.

Done when re-running the inventory returns only Decision Records, `TODO`s, doc comments that pass and machine-read comments, and the project's typecheck and tests give the same result as before the sweep.

## 4. Report

- comments per class, with the action taken
- lines shed, from `git diff --shortstat`
- every Excuse as a proposed issue: a title, `file:line`, the real fix. File them in the project's tracker only after the user confirms
- exported names worth renaming, and directories left for the next pass
