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

Keep reasoning short on routine event turns: a few lines naming the risk
mode, the deciding priority and the choice. Think longer only at quarterly
phases, before a review, or when a stat is near the warning zone.

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
   warning zone, Wealth negative, Network fallen to near zero, repeated weak
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

## Health and Dignity: target band

Hold Health and Dignity in a target band in the upper-middle of the 0–10
scale, around 6 to 7. This band is the team's own choice, not a simulation
threshold.

- **Below the band:** recover. Prefer options that raise the low stat, and
  use the defensive Energy slot.
- **In the band:** fine. Do not spend Energy or choices to raise them.
- **Above the band:** spare buffer. Accept options that cost a little Health
  or Dignity when they raise Output or Network, as long as the stat stays in
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
  scale, because the ending evaluates the final condition
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

**High-intensity work** raises HiddenRisk as well as Output, and costs
Health and Dignity. Use it only when all of these hold:

- the risk mode is low;
- Output actually rose after the last Output gains (it is not held);
- Health and Dignity are above the band and stay in it after the cost;
- it was not used in the previous quarter.

Otherwise use Prepare report materials for Output.

**Risk raised mode:**

1. Defuse legacy risk (2). Never skip this in risk raised mode, even right
   before a review.
2. Last point: Health recovery or Mental recovery (1) if a stat is below the
   band; otherwise Prepare report materials (1) for Output.

**Risk low mode (Output first):**

1. Output: High-intensity work (2) only if every condition above holds;
   otherwise Prepare report materials (1).
2. Network maintenance (1).
3. Any point left: Health recovery or Mental recovery if a stat is below the
   band, otherwise Prepare report materials.

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
the current level until the next promotion (this is common before the first
promotion). While a stat is held, never pay Health or Dignity for it, and
do not use High-intensity work for it. Keep making clean,
professional event choices (no dropped work, no buried problems), keep risk
defused, and let the review judge the half-year. Visible gains return after
a promotion.

## Output stall breaker

Treat Output as **stalled** when it has not risen over the last quarter
while other stats kept rising, or when manager feedback says results are
not visible (for example, working hard without wins). While stalled:

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
