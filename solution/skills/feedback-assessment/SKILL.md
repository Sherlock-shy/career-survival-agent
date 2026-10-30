---
name: feedback-assessment
description: Interprets in-run feedback - the monthly feed in the observe `events` text, half-year review grades, manager 1:1 talks, colleague and HR remarks, warnings, promotion notices and stat changes after each action - and turns them into a focus for the next quarter or half-year. Use whenever the monthly feed contains a remark or notice, after each review or warning, and when results differ from what was expected.
---

# Feedback Assessment

The simulation does not publish its formulas, so in-run feedback is the main
signal for adjusting the plan.

## The monthly feed

Each month the `observe` result carries an `events` text with the salary
line and short remarks from HR, colleagues, mentors or the manager. Read it
every month; it is the most frequent feedback in the run.

- **Track the tone of recurring remarks as a gauge.** A remark that recurs
  every month (for example from the same HR contact) usually moves between
  positive, mild concern, clear concern and alarm. Note the current level
  and its direction. Rising concern about risk means act now (see risk
  remarks below); returning to positive means the issue is handled.
- **Remarks that point at a weakness** (falling behind technically, dropped
  balls, missed delivery, strained relationships) name the stat to work on:
  Skill, Output or Network respectively.
- **Remarks that someone helped or protected you** mean Network is working;
  no change needed.
- Keep a one-line memory of the latest gauge level in your reasoning so the
  trend is visible across months. Do not quote the full feed in `notes`.

## Signals and how to respond

- **Review grade.** C or D means Output and visible results are too low:
  for the next half-year, put Output first in Energy, main action and event
  choices. B means on track but not standing out: keep balance and add more
  Output and Network before the next review. A or S: keep the current mix.
- **Manager feedback** that asks for clearer results, more wins or more
  delivery, or says you work hard without winning, points to Output: switch
  on the Output stall breaker in `adaptive-decision-planning` for the next
  half-year, even if the grade was B. Feedback about collaboration or relationships
  points to Network.
- **The risk gauge.** Read every remark about risk, from HR or anyone else,
  on this scale and act on the current level:
  - *Positive* (all is well): only when every risk remark in the quarter
    was positive and no blow-up has happened is the risk mode *low*, and
    Output then comes before Defuse. Keep the steady Defuse rhythm anyway
    (hard rule 1 in `adaptive-decision-planning`): a positive remark means
    risk has not shown yet, not that none is building.
  - *Any concern, even mild or "not serious yet"*: defuse this quarter.
  - *Withdrawn support* (you are left on your own, nobody will cover for
    you) or *anger / alarm* (buried problems about to go off): emergency.
    Treat them as a warning that a blow-up may be close. Defuse at the next Energy phase,
    and in events pick the option that resolves or discloses problems
    over any stat gain.
  Do not judge severity by how polite the wording sounds. A calm remark
  that support has been withdrawn is more serious than a frustrated one.
- **After a serious blow-up** (a warning that old problems have gone off,
  work has been lost, or trust is damaged): another one may end the run.
  For the rest of the run, defuse every quarter and treat hidden risk as
  the top threat, above promotion.
- **Promotion notice with friction or envy** means Network needs attention
  in the next quarter.
- **No promotion at a window** while one input is far behind the others:
  that input is the bottleneck. Shift effort to it.
- **Any explicit warning** about Health, Dignity, Wealth or Network:
  treat that stat as critical immediately.

## Checking your own actions

After an outcome is shown, compare it with what the `notes` expected. If an
option type keeps giving worse results than expected (for example a stat
did not move, or a hidden cost appeared), weight that type lower in future
similar situations. Base this on the effects seen in this run, not on fixed
assumptions.

## Recovery after context loss

If the conversation was compressed, call `check_latest_logs` once with a
small count to recover the latest grade, warnings and recent choices, then
continue. Do not re-read logs on normal turns.

## Near a limit

An explicit warning, a D grade, or a risk gauge at withdrawn support or
alarm is critical this turn: it outranks every growth consideration until it
is addressed. A C grade or any non-positive risk remark sets the focus for
the whole next quarter.

## Across the 48 months

Compare each review with the previous one at the same level. Repeated weak
reviews are a published failure direction, so after a weak grade the next
half-year is protective: Output kept at its bar, no new commitments, risk
defused every quarter. In the final year, feedback that cannot be acted on
before month 48 is noted and not chased.

## If the wording or order of events changes

Classify every remark by its meaning (praise, mild concern, withdrawn
support, alarm, a named weakness) rather than its wording, and read grades
and stat changes from the numbers. The responses above apply whatever the
events are and in whatever order they come.
