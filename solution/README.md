# adaptive-career-agent

Team: **IQ2 Only**. Platform: JiuwenSwarm `0.2.4b3`, single-agent mode
(`"mode": "agent"`). Model: the designated competition model.

## Summary

This submission is an adaptive career decision-making agent for the 48-month
career simulation. It aims to reach the highest career level it can, as early
as it can, without letting Health, Dignity, Wealth, Network or hidden risk
reach a published failure direction.

On every turn the agent checks a short list of hard rules, then applies one
priority order: survival first, then defusing hidden risk, then Output, then
Network, then Wealth. Health and Dignity are held in an upper-middle band
during the run and raised to the top in the final quarter.

The main rules came from our run logs:

- **Hidden risk first.** HR's monthly remark is read as a risk gauge, but
  risk can build while HR is still positive. The agent keeps a Defuse clock
  (Defuse at least every other quarter, every quarter while any remark is not
  positive, and every quarter after a blow-up) and lays low when support is
  withdrawn. It never uses High-intensity work, which the in-game menu says
  raises HiddenRisk.
- **Protect Output before reviews, and deliver what you own.** In the two
  quarters before a review the agent never lowers Output, and it avoids
  options that drop, delay, hand off or quietly skip its own work.
- **Pay only for gains that land.** A stat can stop rising at a level: a gain
  is printed but the value does not move. The agent never spends Energy,
  Health or Dignity on a stat at its bar, and spends a stat parked at the top
  of its scale (often Dignity) on gains that land.
- **Skill pivot.** When Output and Network have stopped rising and a window
  has been missed at that level, Energy, main actions and event choices move
  to Skill, the promotion input still able to grow.
- **Keep playing.** Every reply ends with a tool call until the game has
  ended, so a run cannot stall part-way.

It uses only the five official MCP tools. The Skills contain no event-specific
answers and no unpublished thresholds.

This release (v18) adds two rules to v10-final, both worded as general
behaviour: from the second year the agent Defuses legacy risk every quarter
whatever the clock says, and in the quarter before a review it declines
optional new commitments so its own deliverables come first. Local runs:
69.07 and 65.29, both finishing at L5, at about 0.87M tokens per month.

## Skills

| Skill | Role |
|---|---|
| `adaptive-decision-planning` | The hub, used on every turn. It applies the hard rules, the priority order and the risk mode, keeps Health and Dignity in their band, builds the quarterly Energy bundle, picks the main action, and runs the Skill pivot and the Output stall breaker. |
| `state-risk-assessment` | Reports the risk mode from the monthly HR remarks, where Health and Dignity sit against their band, which stats are held at the current level, and any critical need. |
| `future-planning` | Keeps the 48-month view: earlier promotions are worth more, each half-year is planned as "build, then show results", and the final quarter restores Health and Dignity for the ending. |
| `risk-reward-gamble` | Filters high-risk options. A gamble is allowed only when safer options cannot fix a real problem, no stat it could hurt is in the warning zone, there is time to recover, and no gamble was taken recently. Options that hide problems are rejected. |
| `feedback-assessment` | Reads the monthly feed every turn and turns review grades, manager talks, the HR risk gauge, promotion notices and warnings into the focus for the next quarter or half-year. |
| `deep-analysis` | Used rarely, only for high-stakes turns where information is missing. It checks the handbook or recent logs through the official tools and returns one fact to the hub. |

The manifest `instruction` tells the agent to load all six Skills once, by
their exact names, to play continuously until the game ends, and to keep
token use low.

## Layout

```text
solution/
  manifest.json
  README.md
  presentation.mp4
  design/
    skills_design.md     # each Skill's purpose, inputs, method and how outputs combine
    decision_report.md   # decision approach, information used, log findings per version, risks, generalisation
  skills/
    adaptive-decision-planning/SKILL.md
    state-risk-assessment/SKILL.md
    future-planning/SKILL.md
    risk-reward-gamble/SKILL.md
    feedback-assessment/SKILL.md
    deep-analysis/SKILL.md
```

## Development history (local runs)

| Version | Main change | Runs (quantitative score) | Notes |
|---|---|---|---|
| v0 baseline | Template placeholder, no strategy | 65.85 | L5 at month 48 |
| v1.1 | Six Skills, cap rule | 44.2 | L4 by month 18, eliminated at month 32 by hidden risk |
| v4 | Pay only for gains that land | 66.53 | L5 at month 48 |
| v6 | Skill pivot after a missed window; Lay low under high risk | 73.75, 60.25 (avg 67.00) | L5 at month 42 in the best run |
| v9 | Hard rules: Defuse early, protect Output before reviews, deliver what you own | 68.88, 64.70 (avg 66.79) | L5 at month 42 |
| v10 | Defuse clock, no High-intensity work, spend spare stats | 70.89, 66.37 | L4 at month 18 in the best run; L5 in both runs; no blow-ups |
| v10-final | v10 rules, fair-play rewording, keep-playing rule, published-rules and near-limit sections, short routine reasoning | **68.58, 72.50 (avg 70.54)** | L5 at month 42 in both runs, risk control 100, 0.93–1.16M tokens per month |
| **v18 (this version)** | v10-final plus Defuse every quarter from the second year, no optional new commitments in the quarter before a review, below-band recovery in every Energy branch, one choice per call, and near-limit sections in every Skill | **69.07, 65.29** | L5 in both runs; no blow-up in run 1; about 0.87M tokens per month |
| v11–v17 (tests) | Narrower blow-up rule, Skill focus, banking, run ledger, red-flag wording | 68.14, 69.81, 49.7, 57.6, and several eliminated runs | Better early windows, but more blow-ups and one stalled run; kept for the next release |

This version adds two things to the tested v10 rules without changing their
behaviour: wording that cites in-game descriptions instead of patterns from
our logs, and the rule to keep playing until the game ends. Single local runs
vary, especially at the first promotion window; the official score averages
three runs, so we compared averages. See `design/decision_report.md` for the
full log findings.
