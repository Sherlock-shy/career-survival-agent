---
name: state-risk-assessment
description: Reads the current status from observe (Health, Dignity, Skill, Network, Output, Wealth, Level, CurrentLevelDuration, Energy) and the monthly feed, and reports the risk mode, where Health and Dignity sit against the target band, and any critical need. Use at the start of every turn before choosing an action.
---

# State and Risk Assessment

Goal: turn the raw status into a short list of needs, ranked by urgency.

## Hidden risk (check first)

Hidden risk is not shown as a stat. Its only signs are remarks in the
monthly feed and warnings. Report:

- the current risk gauge level (positive, concern, withdrawn support,
  alarm; see `feedback-assessment`);
- whether a serious blow-up has already happened in this run;
- the resulting **risk mode**: *low* if every risk remark this quarter was
  positive and no blow-up has happened, otherwise *raised*.

Withdrawn support, alarm, or any non-positive level after a blow-up is
critical.

## Health and Dignity (0 to 10)

These bands are the team's own choices, not simulation thresholds:

- **Warning zone:** bottom third of the scale, or falling for two or more
  consecutive months. Critical: recover first.
- **Below target band:** under the upper-middle band (around 6 to 7). Needs
  recovery soon.
- **In target band:** fine. Do not spend on it.
- **Above target band:** spare buffer that may be spent on Output or
  Network, as long as the stat stays in the band afterwards.
- **At the cap of 10:** further gains are worth zero.

Judge Health by its value and trend, not by the tone of a story. A single
burnout-flavoured event text does not mean Health is in danger.

## Output, Network, Wealth, Skill

These have no published target, so judge them by trend and the team's
priority order (Output, then Network, then Wealth; Skill becomes the target under the Skill pivot):

- **Trend:** a stat that has not grown for a whole quarter is stalling.
- **Held stat:** if gains are shown but a stat has not moved for a whole
  quarter, it may be held at the current level until the next promotion.
  Report it as held, not stalled, so the decision step stops spending
  Health on it.
- **Floors:** Wealth at or near zero and Network that has fallen to near zero,
  or stayed there for a long time, are published failure directions. Treat
  either as critical. All stats start low by design, so a low starting value
  in the opening months is not a crisis.

## Career clock

- Note `CurrentLevelDuration` and how many promotion windows have passed at
  this level.
- Note how many months remain until the next six-month review and until
  month 48.

## Output of this Skill

One line for the decision step, for example:
"Risk mode: raised (concern). Health 8 above band, Dignity 6 in band.
Output stalling. Review in 2 months."
Keep it short; do not restate every number.

## Near a limit: what to flag first

Report these as critical, before anything else, whenever they hold:

- Health or Dignity in the warning zone (bottom third of the scale).
- Wealth at or near zero, or Network near zero: published failure
  directions.
- The risk gauge at withdrawn support or alarm.
- A weak review grade in the last half-year: repeated weak reviews are a
  published failure direction.

Below these, flag any stat below the target band and any promotion input
that has fallen below its highest value at this level.

## Across the 48 months

Report the months to the next review and to month 48 on every turn. In the
final quarter, flag Health and Dignity below the top of the scale as needs,
because the ending evaluation covers their condition.

## If the wording or order of events changes

Read the risk gauge from the meaning of a remark (positive, concern,
withdrawn support, alarm), not from exact phrases, and read every other
need from the numbers in `observe`. None of this depends on which events
appear or in what order.
