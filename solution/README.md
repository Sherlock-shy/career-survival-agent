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

- Hidden risk first. We read HR's monthly remark as a risk gauge, but risk
  can build while HR is still positive, so the agent keeps a Defuse clock:
  Defuse at least every other quarter, every quarter while any remark is not
  positive, every quarter after a blow-up, and every quarter from the second
  year. It lays low when support is
  withdrawn, and it never uses High-intensity work, which the in-game menu
  says raises HiddenRisk.
- Protect Output before reviews and deliver what you own. In the two
  quarters before a review the agent never lowers Output, and it avoids
  options that drop, delay, hand off or quietly skip its own work.
- Pay only for gains that land. A stat can stop rising at a level: the gain
  is printed but the value does not move. The agent never spends Energy,
  Health or Dignity on a stat stuck at its bar, and it spends a stat parked
  at the top of its scale (often Dignity) on gains that do land.
- Skill pivot. When Output and Network have stopped rising and a window has
  been missed at that level, Energy, main actions and event choices move to
  Skill, the promotion input that can still grow.
- Keep playing. Every reply ends with a tool call until the game has ended,
  so a run cannot stall part-way.

It uses only the five official MCP tools. The Skills contain no event-specific
answers and no unpublished thresholds.

This release (v18) is v10-final plus five changes made on 30 October, all
worded as general behaviour: Wealth at or near zero is treated as critical;
one choice per call; a stat below its band is recovered in every Energy
branch; from the second year the agent Defuses legacy risk every quarter
whatever the clock says; and in the quarter before a review it declines
optional new commitments so its own deliverables come first. Local runs,
made just before the last helper-Skill sections were added: 69.07 and 65.29,
both finishing at L5, at about 0.87M tokens per month.
This was our submission at the second cut-off (30 October 2026), and it
scored 68.50 officially.

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
| v1.1 | Six Skills, cap rule | 44.2 | L4 by month 18, eliminated at month 33 by hidden risk |
| v4 | Pay only for gains that land | 66.53 | L5 at month 48 |
| v6 | Skill pivot after a missed window; Lay low under high risk | 73.75, 60.25 (avg 67.00) | L5 at month 42 in the best run |
| v9 | Hard rules: Defuse early, protect Output before reviews, deliver what you own | 68.88, 64.70 (avg 66.79) | L5 at month 42 |
| v10 | Defuse clock, no High-intensity work, spend spare stats | 70.89, 66.37 | L4 at month 18 in the best run; L5 in both runs; no blow-ups |
| v10-final | v10 rules, fair-play rewording, keep-playing rule, published-rules and near-limit sections, short routine reasoning | 58.77 (29 Oct draft); on 30 Oct 68.58, 72.50, then 28.18 and 61.45 | 30 Oct: L5 at months 48 and 42 in the first two runs, risk control 100, 0.93–1.16M tokens per month; the next two went broke at month 8 and had a hidden-risk blow-up at month 24 |
| **v18 (this version)** | v10-final plus Wealth at or near zero treated as critical, one choice per call, below-band recovery in every Energy branch, Defuse every quarter from the second year, no optional new commitments in the quarter before a review, and near-limit sections in every Skill | **69.07, 65.29** | L5 in both runs; a blow-up at month 12 in run 2; about 0.87M tokens per month |
| v11–v17 (tests) | Narrower blow-up rule, Skill focus, banking, run ledger, red-flag wording | Completed 68.14, 69.81, 49.7, 57.6; eliminated 44.57, 42.02, 22.75, 22.98, 17.07 | Better early windows, but more blow-ups and one stalled run; not adopted in v18 |

Single local runs vary a lot, especially at the first promotion window, and
the official score averages three runs, so we compared versions on repeat
runs. v10-final's first two runs on 30 October looked best, but its next two
went wrong: one went broke at month 8, and another had a hidden-risk blow-up
at month 24. We added the Wealth rule after the first failure and the Defuse
and commitment rules after the second, which gave v18. Its two runs averaged
67.18 with no elimination, though the second still had a blow-up at month
12, so we submitted v18. Every scored run is listed in
`../results/local_runs.csv`, and `design/decision_report.md` has the full log
findings.
