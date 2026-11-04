# Decision report

Team: IQ2 Only. Submission: `adaptive-career-agent` v18 (v10 rules plus fixes).

## 1. Decision approach

The agent treats the 48-month simulation as a repeated six-month cycle under
incomplete information. Its aim is to pass as many promotion windows as
possible, as early as possible, without letting any stat fall into a failure
direction.

Each turn follows the same loop:

1. `observe` once.
2. Assess the state: risk mode, Health and Dignity against their band, which
   promotion inputs are still rising and which have stopped at this level.
3. Place the turn in the cycle: a build quarter, or the quarter before a
   review.
4. Apply recent feedback (grades, manager talks, HR remarks).
5. Check the hard rules, then pick by the priority order: survival, defuse
   hidden risk, Output, Network, Wealth. When Output and Network have
   stopped rising and a window has been missed at the level, the Skill pivot
   moves Energy, main actions and event choices to Skill.
6. Filter any risky option through the gamble rules.
7. `take_action` with a one-sentence note.

A threatened failure direction always outranks growth, and a gain on a stat
that cannot move this level is worth zero.

## 2. Public information used

All of the following is published in the handbook and rules pages:

- the visible statistics, their ranges and the 0–10 scale of Health and
  Dignity;
- the 48-month time structure and the quarterly action phase (3 Energy, no
  carry-over);
- the published direction of each Energy action and main action, and their
  Energy costs;
- the salary table by level, and the review grades with their bonus
  multiples;
- the six-month review and promotion window, and the fact that promotion
  considers Skill, Network and Output and rewards sustained results;
- the failure directions and the six dimensions of the ending evaluation.

The Skills use these only as directions and structure. They do not claim any
exact promotion, performance, failure or scoring threshold. The Health and
Dignity band is stated to be the team's own choice.

## 3. In-run feedback used

- **Stat changes after each action,** compared with the gain printed in the
  outcome, to tell a stat that is still rising from one that has stopped at
  this level.
- **Review grades and manager 1:1 talks,** used as the main signal of
  whether visible results are enough.
- **HR's monthly remark as a risk gauge** (positive, concern, withdrawn
  support, alarm), driving Defuse legacy risk and Lay low.
- **In-game action descriptions,** which state effects the published table
  leaves out (High-intensity work raises HiddenRisk; Lay low lowers it).
- **Promotion notices** (friction after a fast climb triggers Network work)
  and **explicit warnings** (the stat becomes critical at once).
- **`check_latest_logs`,** only to recover after context compression or for
  a high-stakes decision.

## 4. What the logs showed, and what changed

All results are local runs of the full 48 months unless noted. The official
score averages three runs, so from v6 on we compared repeat runs.

| Version | Runs | What the logs showed | Change in the next version |
|---|---|---|---|
| v0 baseline (no Skills) | 65.85 | Grades C, C, B, B, B, B, A, A; managers asked for "clearer results"; Dignity at 10 from month 3; the same main action 15 quarters in a row; L5 only at month 48 | Priority order, cap rule, main action rotation |
| v1.1 | 44.2 | L4 by month 18, then eliminated at month 33 by a second hidden-risk blow-up. Defuse in only 4 quarters; a calm HR remark that support had been withdrawn was read as mild | Defuse by default; HR remark as a risk gauge; withdrawn support is an emergency |
| v2, v3 | stopped at L1 | Gains were printed but stats did not move at L1; the agent paid Health for them and got D grades for dropped work | Stats can stop rising at a level; pay only for gains that land |
| v4 | 66.53 | Risk remarks rose after each use of High-intensity work, matching its in-game description; long stays at L4 with Output and Network flat while Skill rose, and L5 came when Skill was high | High-intensity work gated (later removed); Skill pivot after a missed window (v6) |
| v6 | 73.75, 60.25 | The same Skills scored 73.75 and then 60.25, so single runs could not rank versions | Repeat runs from v6 on; hard rules (v9) |
| v9 | 68.88, 64.70 | First review passed more often with "deliver what you own"; HR concern often rose a quarter after a calm quarter without Defuse | Defuse clock; no High-intensity work |
| v10 | 70.89, 66.37 | No blow-ups; the Defuse clock kept risk control at 100. Skill learning was rarely bought, so L5 came at month 42 and 48 | Later tests |
| v10-final | 58.77 (29 Oct), then 68.58, 72.50, 28.18, 61.45 (30 Oct) | Same rules plus fair-play rewording, a keep-playing rule, published-rules and near-limit sections and shorter routine reasoning. On 30 October the first two runs reached L5 at months 48 and 42 with risk control 100 and tokens at 0.93–1.16M per month; of the next two, one went broke at month 8 and the other had a hidden-risk blow-up at month 24 | Base for v18 |
| v11–v17 (tests after v10) | Completed 68.14, 69.81, 49.7, 57.6; eliminated 44.57, 42.02, 22.75, 22.98, 17.07 | A narrower blow-up rule, earlier Skill focus, a mode for capped levels, a run ledger and red-flag wording improved the first reviews but brought more blow-ups and one stalled run | Not adopted in v18 |

v10-final kept the v10 rules, which were the most stable over repeated
runs, and fixed three contradictions in them. It also reworded the rules to
cite in-game descriptions instead of patterns from our logs, and added a rule
to keep playing until the game ends (one later test run stalled when the agent
stopped without making its next tool call).

Version 18 keeps the v10 rules and adds what the last day of logs showed.
One run went broke at month 8, so Wealth at or near zero is now critical.
One run died of Health zero at the first level, and another fell to Health
2, because one Energy branch had no "recover a stat below the band" line;
all three branches now have it. Two runs blew up in the second year with HR
still positive, so Defuse legacy risk now runs every quarter from the second
year, as well as whenever any remark is not positive. Three runs lost a window to optional extra work accepted
just before a review, so in the quarter before a review the agent declines
new commitments. Local runs of v18: 69.07 and 65.29, both finishing at L5;
the second still blew up at month 12 after a mild HR remark went
unanswered. v10-final's first two runs on 30 October averaged 70.54, but of
its next two, one went broke at month 8 and the other blew up; v18's two
runs averaged 67.18 with no elimination. v18 was our submission at the second cut-off and scored 68.50
officially.

## 5. Principal risks

- **Hidden risk.** Its conditions are not published and it can surface late,
  sometimes while HR is still positive. Mitigation: a Defuse clock (at least
  every other quarter, every quarter while any remark is not positive, every
  quarter after a blow-up, and every quarter from the second year), Lay low when support is withdrawn, never
  High-intensity work, and options that hide problems are rejected.
- **Health.** Health at zero ends the run. Mitigation: Health and Dignity
  are held in a band, event choices never take Health below it, and the
  defensive Energy point recovers a low stat.
- **Misreading a held stat.** Spending on a stat that cannot rise wastes
  Energy. Mitigation: the agent compares printed gains with the next
  `observe` and stops paying for a stat that did not move.
- **Run-to-run variance.** The first review in particular swings on a few
  choices. Mitigation: we compared averages of repeat runs, not single runs.
- **Token cost.** Mitigation: one `observe` per decision, short notes, short
  reasoning on routine turns, deep analysis only when information is
  missing.

## 6. Generalisation beyond the development dataset

- **No event-specific content.** The Skills contain no event names, quoted
  plot situations, memorised sequences or fixed answers. Every choice is
  judged from the event text and effects visible at that moment.
- **No unpublished numbers or level-keyed rules.** Rules trigger on what the
  agent observes in the run (did a stat rise since the last Energy phase,
  what HR said, what the review talk said), not on level names or values,
  so they still apply if levels, caps, events or parameters change.
- **Rules re-read in-run.** Published values are read from
  `show_employee_handbook` during the run rather than assumed.
- **Strategy driven by feedback.** Grades, remarks and observed stat changes
  adjust the plan, so the agent adapts if the hidden formulas differ from
  the development dataset.
- **Official interfaces only.** The agent uses only the five official MCP
  tools. It never reads package files, databases or other internal state.
