# adaptive-career-agent (v6)

Team: **IQ2 Only**. Platform: JiuwenSwarm `0.2.4b3`, single-agent mode
(`"mode": "agent"`). Model: the designated competition model.

## Summary

This submission is an adaptive career decision-making agent for the 48-month
career simulation. It aims to reach the highest career level it can, as early
as it can, without letting Health, Dignity, Wealth, Network or hidden risk
reach a published failure direction.

On every turn the agent applies one priority order: survival first, then
defusing hidden risk, then Output, then Network, then Wealth. Output moves
ahead of Defuse only while every HR risk remark in the quarter is positive.
Health and Dignity are held in an upper-middle band during the run and raised
to the top in the final quarter.

Three rules came directly from our run logs:

- **Pay only for gains that land.** Stats can be held at a level: a gain is
  printed but the stat does not move. The agent never pays Health or Dignity
  for a gain on a held stat.
- **Hidden risk first.** HR's monthly remark is read as a risk gauge. Any
  non-positive remark means Defuse that quarter; withdrawn support or alarm
  means Lay low and reduce risk. High-intensity work, which the in-game menu
  says raises HiddenRisk, is used only under strict conditions.
- **Skill pivot.** When Output and Network stop rising at a level, Energy and
  main actions move to Skill, the promotion input still able to grow.

It uses only the five official MCP tools. The Skills contain no event-specific
answers and no unpublished thresholds.

## Skills

| Skill | Role |
|---|---|
| `adaptive-decision-planning` | The hub, used on every turn. It applies the priority order and the risk mode, keeps Health and Dignity in their band, checks that gains will actually land, builds the quarterly Energy bundle, picks the main action, and runs the Skill pivot. |
| `state-risk-assessment` | Reports the risk mode from the monthly HR remarks, where Health and Dignity sit against their band, which stats are held at the current level, and any critical need. |
| `future-planning` | Keeps the 48-month view: earlier promotions are worth more, each half-year is planned as "build, then show results", and the final quarter restores Health and Dignity for the ending. |
| `risk-reward-gamble` | Filters high-risk options. A gamble is allowed only when safer options cannot fix a real problem, no stat it could hurt is in the warning zone, there is time to recover, and no gamble was taken recently. Options that hide problems are always rejected. |
| `feedback-assessment` | Reads the monthly feed every turn and turns review grades, manager talks, the HR risk gauge, promotion notices and warnings into the focus for the next quarter or half-year. |
| `deep-analysis` | Used rarely, only for high-stakes turns where information is missing. It checks the handbook or recent logs through the official tools and returns one fact to the hub. |

The manifest `instruction` tells the agent to load all six Skills once, by
their exact names, at the start of the game, and sets rules to keep token use
low.

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

## Development history (local runs, one run each)

| Version | Main change | Result | Quantitative score |
|---|---|---|---|
| v0 baseline | Template placeholder, no strategy | L5 at month 48 | 65.85 |
| v1.1 | Six Skills, cap rule | L4 by month 18, then eliminated at month 32 by hidden risk | 44.2 |
| v2, v3 | Risk gauge, Output push, Health band | Stuck at L1 (stats held; Health paid for gains that never landed) | — |
| v4 | Pay only for gains that land; High-intensity gated | L5 at month 48, risk control 100 | 66.53 |
| **v6 (this version)** | Skill pivot; Lay low under high risk; shorter reasoning | **L5 at month 42**, risk control 100 | **73.75** |
| v7 (test) | Simpler Skill trigger | Missed the first window, L5 at month 48 | 65.90 |

Single local runs vary, especially at the first promotion window; the
official score averages three runs. See `design/decision_report.md` for the
full log findings.
