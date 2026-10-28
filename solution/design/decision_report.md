# Decision report

Team: IQ2 Only. Submission: `adaptive-career-agent-v1`.

## 1. Decision approach

The agent treats the 48-month simulation as a repeated six-month cycle under
incomplete information. Its aim is to pass as many promotion windows as
possible, as early as possible, without letting any stat fall into a failure
direction.

Each turn follows the same loop:

1. `observe` once.
2. Assess the state: what is critical, what is saturated, and which promotion
   input is lagging.
3. Place the turn in the cycle: is this a build quarter, or the quarter just
   before a review?
4. Apply recent feedback.
5. Score the options with a fixed priority order:
   1. survival;
   2. no wasted gains;
   3. the lagging promotion input;
   4. hidden-risk hygiene;
   5. cheap costs.
6. Filter any risky option through the gamble rules.
7. `take_action` with a one-sentence note.

The priority order matters more than any single number. A threatened failure
direction always outranks growth. A gain on a capped stat is worth almost
nothing. Growth effort goes to whichever of Skill, Network and Output is
furthest behind.

## 2. Public information used

All of the following is published in the handbook and rules pages:

- the visible statistics, their ranges and the 0–10 cap on Health and
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
exact promotion, performance, failure or scoring threshold. The health and
dignity bands in `state-risk-assessment` are relative and are stated to be
the team's own safety margins.

## 3. In-run feedback used

- **Stat changes after each action.** These are compared with what the
  `notes` field expected, and option types that keep under-delivering are
  down-weighted.
- **Half-year review grades and manager 1:1 talks.** These are used as the
  main signal of whether Output and visible results are enough.
- **Colleague and HR remarks about piling-up or buried risk.** These are
  used as the signal to spend Energy on Defuse legacy risk, and to stop that
  spending once the remarks turn positive.
- **Promotion notices,** including friction after a fast climb, which
  triggers Network repair.
- **Explicit warnings.** These immediately mark the stat concerned as
  critical.
- **`check_latest_logs`.** Used only to recover state after context
  compression, or for a high-stakes decision.

## 4. What the baseline logs showed, and what changed

The first local run (v0) used the template placeholder Skill, so the agent
played with no strategy. It completed all 48 months with a
`quantitative_score` of 65.85:

| Dimension | Raw | Normalized |
|---|---|---|
| Level | L5 | 50 |
| Wealth | 28.4 | 35.5 |
| Health & dignity | 20 | 100 |
| Skill | 102 | 85 |
| Risk control | 0 | 100 |
| Network | 36 | 60 |

Token use was about 1.02M tokens per month. Level and wealth together
accounted for about 28 of the 34 lost points.

| Finding in the v0 logs | Change in v1 |
|---|---|
| Review grades were C, C, B, B, B, B, A, A. Managers repeatedly asked for "clearer results". Output stayed flat for long periods while Skill kept rising. The agent stayed at L4 for 18 months and missed three promotion windows. | The rubric raises the lagging promotion input and gives Output extra weight in the quarter before a review. `future-planning` plans each half-year as build, then show results. |
| Dignity reached the cap of 10 by month 3 and never fell. Health stayed at 9–10 from month 24. Further gains to both were wasted. High-intensity work was used once in 48 months. | Saturation is detected explicitly ("no wasted gains"). The comfortable buffer can be spent on Output-raising actions. |
| The same main action was chosen in 15 consecutive quarters. | The main action is re-selected each quarter from the current gap. |
| Defuse legacy risk was bought in 10 quarters. Risk remarks appeared mid-run and later cleared, and risk control ended at 100. | Defusing stays the default in most quarters, filled before growth spending. (A purely reactive version was tried and caused an elimination; see below.) Options that hide problems are rejected outright. |
| Network stayed flat for the final 12 months. A promotion caused friction with old contacts. | Network is a tracked promotion input. Post-promotion friction triggers Network work. |
| The agent looked up a wrong skill name. It made 178 `observe` calls at about 1.02M tokens per month. | The manifest lists the exact Skill names. The Skills limit `observe`, `notes` length and deep analysis. |

An early check of the first v1 run (to month 12) showed Dignity still sitting
at 10 while the agent kept buying actions whose gain was partly Dignity. The
cap rule was then made explicit for every option: a gain on a capped stat is
worth zero, and a small loss on a capped stat is nearly free, which makes
High-intensity work preferable to Dignity-raising Output actions while
Dignity is full and Health is comfortable.

A later v1 run showed the opposite problem with Health: with Health in the
middle of the scale and stable, the agent still chose slower-pace and
recovery options because the event text mentioned burnout. A rule was added
to judge Health by its value and trend rather than the tone of the story,
and to accept small Health costs for Output, Skill or Network when Health
stays out of the warning zone afterwards. A low starting value of Network
was also being treated as a crisis; the floor rule now applies only when a
stat has fallen to, or stayed near, zero.

The first full run with these fixes reached L4 by month 18, twelve months
earlier than the baseline, but then missed the windows at months 24 and 30.
Output stayed flat in the mid-20s while Skill kept climbing, the main action
was again the same every quarter, and the manager said the agent "works hard
and never wins". An Output stall breaker was added: when Output stops rising
while other inputs grow, or the manager says results are not visible, the
agent rotates to Output-focused main actions, puts Energy into Output, and
prefers Output gains in events until Output moves again.

That same run was then eliminated at month 32 (quantitative score 44.2) by a
trust crisis: a second major hidden-risk blow-up. The logs showed why. The
agent had defused risk in only 4 quarters (the baseline did 10 and kept risk
control at 100). It read a calm remark that HR support had been withdrawn
as mild, although that remark came right before both blow-ups. It spent all
Energy on Output before a review while risk remarks were not positive. And
after the first blow-up it stopped defusing as soon as the remarks turned
positive. Our earlier change to make defusing purely reactive was the cause.
The fix: defuse in most quarters by default; any non-positive risk remark
means defuse that quarter; withdrawn support or alarm is an emergency; the
risk slot is filled before any Output spending; and after any blow-up the
agent defuses every quarter for the rest of the run.

The next run (v2), with the risk fix, was dismissed at month 23 after two
weak half-year reviews, without leaving L1. Skill, Network and Output did
not move at all at L1 (the same pattern appears in every run before the
first promotion), so the Output stall breaker stayed on permanently and the
agent used High-intensity work every quarter, driving Health and Dignity
down, while it never bought Defuse legacy risk. The manager's feedback was
about dropped work and incidents, not stats.

v3 replaces the rubric with a simple priority order: survival, then defuse
hidden risk, then Output, then Network, then Wealth, with Output moving
ahead of Defuse only while the risk gauge is positive. Health and Dignity
are held in a target band in the upper-middle of the scale instead of being
spent freely. A stat that does not move despite gains is treated as held at
the current level, so the agent stops spending Health on it and focuses on
clean event handling and risk.

The v3 test run got a D grade at month 18 with the same feedback as v2
("work got dropped and updates went missing"). The logs showed the agent
paying real Health for Output gains that never landed while Output was held
at L1 (Health fell to 2 at month 11), and the in-game Energy menu describes
High-intensity work as raising HiddenRisk, which the published action table
does not mention. v4 tightens both: Health or Dignity is paid only for a
gain that actually lands, event choices never take Health below the band,
and High-intensity work needs low risk mode, moving Output, Health above
the band and no use in the previous quarter. The agent also reads each
action's in-game description for effects the published table leaves out.

The first v4 run passed the windows at months 6 and 12, missed 18, and
passed 24 (L4 at month 24, six months ahead of the baseline), with Health
and Dignity near the top of the scale throughout. Two patterns showed up in
its logs and in earlier runs. First, Output appears to be held at a ceiling
for each level: it stops rising mid-level and jumps again only after a
promotion, so extra Output spending at that point is wasted, which the
"pay only for gains that land" rule now handles. Second, each burst of
High-intensity work was followed within a few months by rising risk
remarks (months 15 to 19, and again at 24), matching the in-game
description that it raises HiddenRisk. The agent responded with Defuse
each time and the remarks eased, but the next version should treat any
non-positive risk remark in the previous quarter as a block on
High-intensity work, and use the Lay low main action while the gauge is at
withdrawn support or alarm.

v4 has not yet been evaluated over repeated runs. Because the official live
play score averages three runs, the team will compare several local runs
before relying on any single result.

## 5. Principal risks

- **Short-term trade-offs.**
  - Pushing Output can cost Health and Dignity. Mitigation: High-intensity
    work and Output-heavy choices are allowed only while both stats have a
    comfortable buffer, and the defensive slot is filled first whenever
    either stat is falling.
- **Hidden risk.**
  - Its conditions are not published, and it can surface late as a serious
    organisational consequence. Mitigation: prefer options that resolve or
    disclose problems, and respond to risk remarks with Defuse legacy risk.
- **Over-correction.**
  - Chasing one lagging input can starve another. Mitigation: the lagging
    input is re-evaluated every quarter, not fixed for the whole run.
- **Gambles.**
  - Uncertain options can produce stacked losses. Mitigation: four
    conditions must hold before a gamble is allowed, and gambles are spaced
    out.
- **Delayed feedback.**
  - Reviews and risk consequences arrive months after the causes.
    Mitigation: the plan works in half-year units, and `notes` record
    expectations so outcomes can be checked against them.
- **Token cost.**
  - Over-analysis lowers the token-efficiency score. Mitigation: deep
    analysis runs only on high-stakes turns with missing information.
- **Run-to-run variance.**
  - Event order may differ between runs. Mitigation: the guidance is generic
    and does not rely on any particular event appearing.

## 6. Generalisation beyond the development dataset

- **No event-specific content.** The Skills contain no event names, quoted
  plot situations, memorised sequences or fixed answers. Every choice is
  judged from the event text and effects visible at that moment.
- **No unpublished numbers.** All bands are relative, so the guidance still
  works if parameter values or thresholds change.
- **Rules re-read in-run.** The published rules are read from
  `show_employee_handbook` during the run, so changed published values (for
  example costs or salaries) are picked up rather than assumed.
- **Strategy driven by feedback.** Review grades, remarks and observed stat
  changes adjust the plan, so the agent adapts if the hidden formulas differ
  from those of the development dataset.
- **Official interfaces only.** The agent uses only the five official MCP
  tools. It never reads package files, databases or other internal state.
