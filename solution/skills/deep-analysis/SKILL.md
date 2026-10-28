---
name: deep-analysis
description: Fills information gaps before a high-stakes decision by consulting the official employee handbook or recent run logs through the official MCP tools. Use only when a choice's effect cannot be judged from the event text and the decision is high-stakes; skip it for routine turns to save tokens.
---

# Deep Analysis

Deep analysis costs tokens, and token use per month is part of the score.
Use it rarely.

## When to use

Use it only when both are true:

1. The decision is high-stakes: a stat is in the warning zone, a review or
   promotion window is next, or the option could be a gamble.
2. The information needed is missing: the choice's effects are not shown and
   cannot be inferred from the text, or you are unsure about a published rule.

## What to check

Only the official MCP tools may be used:

- **A published rule** (action costs, review cadence, salary, failure
  directions): call `show_employee_handbook`. The handbook is read once at the
  start of the game; call it again only if the rule is truly not recalled.
- **What happened recently** (last grade, recent warnings, outcomes of similar
  choices): call `check_latest_logs` with a small count.

Never read package files, databases or any other non-official source.

## Hand-off

Return one sentence to `adaptive-decision-planning`: the missing fact and
what it implies for the choice. Then decide; do not loop back into further
analysis for the same turn.
