---
name: adaptive-decision-planning
description: Core per-turn decision loop for the career simulation. Use on every observe/take_action turn to pick a monthly event choice or a quarterly Energy/main action, applying the team's priority order (hidden risk, then Output, then Network, then Wealth) with Health and Dignity held in a target band.
---

# Adaptive Decision Planning

This is the hub Skill. Run it on every turn. It calls on the other Skills:
`state-risk-assessment` (what needs attention), `future-planning` (where we are
in the 48-month cycle), `risk-reward-gamble` (filter risky options),
`feedback-assessment` (what recent results tell us) and `deep-analysis` (only
when information is missing).

## Turn loop

1. `observe` once. Do not observe again before acting unless an action failed.
2. Read the monthly feed: the `events` text in the `observe` result
   (salary line, remarks from HR, colleagues and the manager, notices). If
   it contains a new signal, apply `feedback-assessment` before deciding.
3. Classify the turn: monthly story event, quarterly Energy phase, quarterly
   main action, or ending.
4. Run the state check (`state-risk-assessment`): note the risk mode (below),
   where Health and Dignity sit against the target band, and any critical
   need.
5. Score each choice with the priority order below and take the best one.
6. `take_action` with a short `notes` string (one sentence, at most ~20
   words): the need addressed and the stat you expected to move.

**Keep playing until the game has ended.** Every reply ends with a tool call
(`observe` or `take_action`). When you write what you will do next, make that
call in the same reply. If you were resumed after a pause, continue the full
loop as before, not one step at a time.

Keep reasoning short on routine event turns: one or two lines naming the
risk mode, the deciding priority and the choice. Think longer only at
quarterly phases, before a review, or when a stat is near a limit.

## Published rules used here

From the public rules (read `show_employee_handbook` only if something
below is unclear):

- **Cadence:** a monthly salary by level; at the end of each quarter, 3
  Energy (unused Energy is lost) and one optional main action; a review
  and a promotion window every six months; the run lasts 48 months.
- **Energy actions (cost, public effect):** High-intensity work (2, Output
  up, Health and Dignity down), Skill learning (2, Skill up, Health down),
  Defuse legacy risk (2, no published stat effect), Network maintenance (1,
  Network and Dignity up), Prepare report materials (1, Output and Dignity
  up), Health recovery (1, Health up), Mental recovery (1, Dignity up).
- **Main actions (public effect):** Apply for a key project (Output, Skill,
  Network), Report results to your manager (Output, Network, Dignity),
  Fight for resources (Output, Network), Apply for rotation (Skill,
  Network), Showcase results (Output, Dignity), Build a mentorship (Skill,
  Network), Repair a key relationship (Network, Dignity), Lay low and
  reduce risk (Health).
- **Reviews:** grades S, A, B, C, D; the half-year bonus is a multiple of
  the monthly base and falls with the grade. Promotion considers Skill,
  Network and Output and rewards sustained results.

The in-game text of an action can add effects the table leaves out; read
it when choosing.

## Near a limit

- **Health or Dignity in the warning zone:** recovery first (Energy and
  events), Lay low as the main action; never spend either for a gain.
- **Wealth at or near zero** (the displayed value is rounded, so 0 may
  already be below zero, and staying there ends the run): pick options that
  keep or add Wealth, and never pick an
  option that costs Wealth.
- **Network near zero:** Repair a key relationship and Network maintenance
  before any growth spending.
- **Risk gauge at withdrawn support or alarm:** Defuse this quarter, Lay low,
  and in events resolve or disclose problems over any stat gain.
- **Weak review just received:** another weak one can end the run; protect
  Output and avoid any option that drops or delays your own work.

## Hard rules (check these on every choice)

1. **Defuse before HR frowns.** Hidden risk has no visible number, so a
   positive remark is not proof that none is building. Keep a Defuse clock: at each Energy phase, check when you last chose
   Defuse legacy risk. If it was not last quarter, it is time: Defuse this
   quarter, even if HR is smiling. If any risk remark this quarter was not
   positive, Defuse every quarter until HR is positive again. Only a stat
   already in the warning zone comes first.
2. **No High-intensity work.** Its in-game description says it raises
   HiddenRisk, and it costs Health and Dignity. Hidden risk can end the
   run, so the Output it adds is not worth it.
3. **Never lower Output before a review.** Reviews come every six months,
   so every quarter is part of a review half-year: never pick an option
   that lowers Output. If every option lowers it, pick the smallest loss.
4. **No Energy on a stat at its bar.** Never buy Prepare report materials
   when Output is at its bar (the value it stopped at
   on this level), and never buy Network maintenance when Network is at its
   bar. Spend that point where it lands, such as Skill learning.
5. **Deliver what you own.** Avoid options that drop, delay, hand off or
   quietly skip your own work or updates.
6. **Network near zero is critical.** Raise it before growth spending.
7. **Never buy a full stat; spend the spare.** A gain on a stat at the top
   of its scale is worth zero: never pick an option or spend Energy for it.
   A stat above its band is spare: let a clean option spend it for a gain
   that lands (see "Spare resources").

## Risk mode

Hidden risk has no visible number. Its level comes from the risk gauge in
the monthly feed (see `feedback-assessment`):

- **Risk low:** every risk remark this quarter was positive, and no serious
  blow-up has happened in this run.
- **Risk raised:** any risk remark this quarter was not positive (even mild
  concern), or a serious blow-up has already happened in this run.

## Priority order

Apply in this order. A higher priority wins over a lower one.

0. **Survival.** If a failure direction is active (Health or Dignity in the
   warning zone, Wealth at or near zero, Network fallen to near zero, repeated weak
   reviews, a risk emergency), the option that most directly relieves it
   wins.
1. **Defuse hidden risk** (in risk raised mode). Prefer options that
   resolve, disclose or close off problems; avoid options that bury, delay or
   cover up problems, or that start new conflicts. In **risk low** mode this
   step drops below Output: Output comes first.
2. **Output.** The visible results that reviews and promotion judge. Prefer
   the option with the larger Output gain, and treat an Output loss as a
   real cost.
3. **Network.** Relationships that promotion also considers. Prefer Network
   gains when Output is equal.
4. **Wealth.** Prefer Wealth gains when the higher priorities are equal, and
   avoid Wealth losses unless they prevent a larger loss.

Skill is the third promotion input. Early on it rises naturally from most
professional event answers, so it is not chased then. It becomes the
target under the Skill pivot below.

## Skill pivot

Promotion considers Skill, Network and Output. When Output and Network have
both stopped rising at the current level (gains shown but the stats do not
move) and a promotion window has been missed at this level, Skill is the
input still able to grow. While this holds:

- **Energy growth:** Skill learning (2) instead of Prepare report materials
  or Network maintenance, provided Health stays in the band after its cost.
  Spend nothing on a held stat unless it is falling.
- **Main action:** rotate Apply for rotation and Build a mentorship (both
  raise Skill), with Apply for a key project as a third option.
- **Events:** prefer the option with the larger Skill gain among clean
  options; a gain on a held stat counts as zero.
- **Exit:** after the next promotion, return to the normal order, since
  Output and Network can rise again at the new level.

Risk raised mode still comes first: Defuse is never dropped for Skill.

## Spare resources

Some stats sit higher than the plan needs. That surplus is a budget to
spend on promotion inputs during the run, then refill near the end.

- **Full stat (top of its scale):** any gain on it is worth zero. When
  comparing options, ignore that part of the gain. If an option's only
  gain is on a full stat, treat it as an option with no gain.
- **Spare stat (above its band):** usually Dignity, sometimes Health. A cost
  paid from it is cheap, as long as the stat stays in or above the band
  afterwards.
- **Where the spend goes:** to a gain that lands this level, in this order:
  a promotion input below its bar or still rising (Output, then Network),
  otherwise Skill. A gain on a held stat is worth zero, so never pay spare
  for it.
- **Prefer the trade.** Between two clean options, take the one that costs
  a spare or full stat and gains a stat that lands, over one that only adds
  to a full stat.
- **Clean only.** Spare is never spent on an option that hides, buries,
  delays or bends rules. That creates hidden risk, which costs more than
  any gain.
- **Refill late.** In the final quarter, stop spending and bring Health and
  Dignity back towards the top (`future-planning`).

## Health and Dignity: target band

Hold Health and Dignity in a target band in the upper-middle of the 0–10
scale, around 6 to 7. This band is the team's own choice, not a simulation
threshold.

- **Below the band:** recover. Prefer options that raise the low stat, and
  use the defensive Energy slot.
- **In the band:** fine. Do not spend Energy or choices to raise them.
- **Above the band:** spare buffer. Accept options that cost a little Health
  or Dignity when they raise a stat that lands (Output, Network, or Skill
  once those are held), as long as the stat stays in
  or above the band afterwards (check: current value minus the cost), and
  only if that gain is real (next rule).
- **Pay only for gains that land.** Before paying Health or Dignity for an
  Output (or Network) gain, check whether that stat actually rose the last
  times a gain was shown for it. If it did not rise (it is held, see "When a
  stat will not move"), the gain is worth zero: choose an option with no
  Health or Dignity cost instead.
- **Event choices never take Health below the band.** If every option costs
  Health, take the smallest cost.
- **Never let either reach the warning zone** for the sake of Output.
- **End of run:** in the final quarter, raise both towards the top of the
  scale, since the ending evaluation covers their condition
  (`future-planning`).

A gain on a stat that is already at the cap of 10 is worth zero.

If two options tie, take the one with fewer negative effects. If effects are
not shown, judge from the event text: professional, transparent,
constructive responses usually protect Dignity and Network; passive or
evasive responses usually create hidden risk.

## Quarterly Energy phase (3 Energy, no carry-over)

Always spend all 3 Energy. Read each action's in-game description in the
choices: it can state effects the published table leaves out. An action
described as raising HiddenRisk counts against the Defuse priority.

**High-intensity work:** never (hard rule 2). Use Prepare report materials
for Output.

**Risk raised mode:**

1. Defuse legacy risk (2). Never skip this in risk raised mode, even right
   before a review.
2. Last point: Health recovery or Mental recovery (1) if a stat is below the
   band; otherwise Prepare report materials (1) if Output is below its bar
   or still rising; otherwise Network maintenance (1) on the same test;
   otherwise recovery.

**Risk low mode (Output first):**

- **Defuse quarter** (no Defuse last quarter): Defuse legacy risk (2), then
  one growth point where it lands (Output if below its bar or rising,
  otherwise Network, otherwise Skill learning if it fits).
- **Growth quarter** (Defused last quarter):
  1. Output: Prepare report materials (1) if Output is below its bar or
     still rising.
  2. Network maintenance (1) if Network is below its bar or still rising.
  3. Otherwise Skill learning (2).
  4. Any point left: Health recovery or Mental recovery if a stat is below
     the band, otherwise the cheapest action that still lands.

## Quarterly main action

Default to an Output-focused action that also helps Network, and rotate
rather than repeating the same one every quarter:

- Report results to your manager (Output, Network, Dignity).
- Apply for a key project (Output, Skill, Network).
- Fight for resources (Output, Network).
- Showcase results (Output, Dignity).

Exceptions:

- Network damaged or fallen low: Repair a key relationship.
- Health in the warning zone, or the risk gauge at withdrawn support or
  alarm: Lay low and reduce risk (its in-game description says HiddenRisk
  falls).
- Skill pivot active: see "Skill pivot" above.

## When a stat will not move

Compare the gain printed in each outcome with the stat in the next
`observe`. If gains are shown but the stat does not rise, it may be held at
the current level until the next promotion. Judge this from what you see
in this run, not from expectation. While a stat is held, never pay Health or Dignity for it, and
do not buy Energy for it. Keep making clean,
professional event choices (no dropped work, no buried problems), keep risk
defused, and let the review judge the half-year. Visible gains return after
a promotion.

## Output stall breaker

Treat Output as **stalled** when it has fallen below the highest value it
reached at this level, or when manager feedback says results are not
visible (for example, working hard without wins). If Output simply stopped
rising at its highest value, it is held, not stalled: see "When a stat will
not move" and the Skill pivot. While stalled:

- Rotate the main action among the Output-focused ones above.
- In events, prefer the option with an Output gain, unless Output is held
  (then it is worth zero) or the gain costs Health below the band.
- In Energy, follow the mode rules above. The stall breaker never overrides
  Defuse in risk raised mode, and never pushes Health or Dignity below the
  band.
- Exit once Output has risen clearly for a quarter.

## When to call deep-analysis

Only when the effect of a choice cannot be inferred from the text and the
choice is high-stakes (it touches a stat in the warning zone, or it comes
right before a review). Otherwise decide directly.
