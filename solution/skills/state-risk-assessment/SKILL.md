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
