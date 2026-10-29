# Skills design

Team: IQ2 Only. Submission: `adaptive-career-agent` (v10 rules). Platform: JiuwenSwarm
`0.2.4b3`, single-agent mode (`"mode": "agent"`).

## 1. Overview

The submission has six Skills. One of them, `adaptive-decision-planning`, is
the hub that runs on every turn. The other five are consulted by the hub: each
answers one narrow question, so the hub can combine their answers into a
single `take_action` call.

```text
observe
  │
  ▼
state-risk-assessment ──► "what needs attention now?"
future-planning       ──► "where are we in the 6-month / 48-month cycle?"
feedback-assessment   ──► "what did recent results and remarks tell us?"
  │
  ▼
adaptive-decision-planning  (hard rules / priority order / Energy / main action)
  │        ▲
  │        ├── risk-reward-gamble  (filter any risky option)
  │        └── deep-analysis       (only if information is missing)
  ▼
take_action (choice + one-sentence notes)
```

All six Skills are loaded once at game start, as the manifest `instruction`
directs, using their exact names. This fixes a baseline problem where the
agent guessed a wrong skill name and played with no strategy.

## 2. The Skills

### 2.1 `adaptive-decision-planning` (hub)

- **Purpose:** decide every turn: monthly story events, the quarterly Energy
  phase and the quarterly main action.
- **Inputs:** the `observe` result (status, event text, choices), plus the
  one-line outputs of the supporting Skills.
- **Method:**
  - **Hard rules,** checked on every choice: a Defuse clock (at least every
    other quarter, every quarter while any risk remark is not positive); no
    High-intensity work; in the two quarters before a review never lower
    Output; no Energy on a stat that has stopped rising at this level;
    deliver what you own; Network near zero is critical; never buy a full
    stat, and spend a spare one on gains that land.
  - **Priority order** for everything else: survival, then defuse hidden
    risk, then Output, then Network, then Wealth. When every risk remark in
    the quarter is positive ("risk low" mode), Output moves ahead of Defuse.
  - **Skill pivot:** when Output and Network have stopped rising and a
    window has been missed at this level, Energy goes to Skill learning,
    the main action rotates Apply for rotation and Build a mentorship, and
    event choices prefer Skill gains.
  - **Health and Dignity** are held in a team-chosen band (around 6–7) and
    raised to the top in the final quarter. A stat above the band is spare
    and may be spent through clean options on gains that land.
  - **Energy:** all 3 points are always spent: a Defuse quarter (Defuse
    plus one point) or a growth quarter (Output, Network or Skill, where it
    lands), decided by the Defuse clock and the risk mode.
  - **Main action:** rotated Output-and-Network actions; Lay low when Health
    is in the warning zone or support is withdrawn; Repair a key
    relationship when Network is damaged; Skill actions under the pivot.
  - **Output stall breaker:** when Output stalls while it can still rise,
    or the manager says results are not visible, push visible results.
- **Output:** one `take_action` with a short `notes` string recording the
  need addressed and the stat expected to move.

### 2.2 `state-risk-assessment`

- **Purpose:** turn raw status into a ranked list of needs.
- **Inputs:**
  - The visible statistics from `observe`: Health, Dignity, Skill, Network,
    Output, Wealth, Level, CurrentLevelDuration and Energy.
  - Their trend over recent months.
- **Method:**
  - **Bounded stats (Health, Dignity, 0–10):** classified as critical, needs
    attention, comfortable or saturated. The bands are relative ("bottom
    third of the scale") and are explicitly the team's own safety margins,
    not simulation thresholds.
  - **Unbounded stats:** judged by balance between Skill, Network and Output,
    and by trend (a stalling stat is a problem even if its value is large).
  - **Published failure directions:** negative Wealth and Network near zero
    are treated as critical.
- **Output:** one line, for example "Critical: none. Saturated: Dignity.
  Held: Output. Review in 2 months."

### 2.3 `future-planning`

- **Purpose:** keep the 48-month horizon in view.
- **Inputs:** the published structure: the salary table, the six-month review
  and promotion windows, the quarterly action cycle, and the dimensions of
  the ending evaluation.
- **Method:**
  - **Early promotions are worth more.** Salary rises steeply with level and
    bonuses are multiples of the monthly base, so a promotion raises both the
    level dimension and the wealth of every later month.
  - **Each half-year is planned as one unit.** The first quarter builds; the
    second quarter, just before the review, shows results.
  - **Phase guidance** covers year 1, years 2–3 and year 4, plus what to do
    right after a promotion.
- **Output:** the current phase and the focus for this quarter.

### 2.4 `risk-reward-gamble`

- **Purpose:** stop the agent from taking unnecessary risks, while still
  allowing a calculated gamble when nothing safer can help.
- **Inputs:** the option text and the current state.
- **Method:**
  1. Decide whether the option is a gamble: a large possible loss, an
     uncertain outcome, or hiding or deferring a problem.
  2. Allow it only if four conditions hold:
     - safer options cannot fix a real current problem;
     - no stat it could hurt is in the warning zone;
     - there is time left to recover;
     - no gamble was taken recently.
  3. Never allow options that hide problems or break trust.
  4. Record any gamble in `notes` so its result can be checked later.
- **Output:** allow or reject, with a reason.

### 2.5 `feedback-assessment`

- **Purpose:** use in-run feedback to correct the strategy, because exact
  formulas are not published.
- **Inputs:**
  - the monthly feed (the `events` text in each `observe` result), read
    every month and tracked as a tone gauge;
  - half-year review grades;
  - manager 1:1 talks;
  - colleague and HR remarks about accumulated risk;
  - promotion notices;
  - explicit warnings;
  - the stat changes shown after each action.
- **Method:**
  - Maps each signal type to a response:
    - weak grade → Output first;
    - the risk gauge (positive, concern, withdrawn support, alarm) → any
      non-positive level means Defuse legacy risk this quarter; withdrawn
      support or alarm is an emergency; after any blow-up, defuse every
      quarter for the rest of the run;
    - friction after a promotion → Network;
    - missed promotion → fix the bottleneck input.
  - Compares outcomes with what `notes` expected, and down-weights option
    types that keep under-delivering in this run.
- **Output:** the focus for the next quarter or half-year.

### 2.6 `deep-analysis`

- **Purpose:** fill an information gap before a high-stakes decision.
- **Inputs:** `show_employee_handbook` and `check_latest_logs` only (official
  MCP tools).
- **Method:**
  - Runs only when the decision is high-stakes **and** information is
    missing.
  - Returns one sentence to the hub, then the hub decides. There is no
    repeated analysis on the same turn.
- **Output:** one fact and what it implies for the choice.

## 3. How the outputs combine into one decision

1. `state-risk-assessment` sets the **constraints**: what must be protected
   and what is saturated.
2. `future-planning` and `feedback-assessment` set the **objective** for this
   quarter: which promotion input to push, and whether risk needs defusing.
3. The hub checks the hard rules, then the priority order. Survival needs
   always outrank growth; growth goes to the input that can still rise.
4. Any option that looks risky passes through `risk-reward-gamble` before it
   can be chosen.
5. `deep-analysis` is the exception path, used only when steps 1–4 cannot be
   completed from visible information.

## 4. Why no scripts or reference files

- **Scripts.** Decisions depend on reading event text in context. A script
  would need fixed numbers or a fixed situation-to-answer map, which the
  rules forbid, and the agent in this setup has no tool that runs code
  (its tools are the Skill loader, a todo list and the game's MCP tools).
- **Reference files.** The handbook is available in-run through
  `show_employee_handbook`, so a copy in `reference/` would only add tokens.

## 5. Token budget

Token use per month is 10% of the automated score. The baseline used about
1.02M tokens per month: 178 `observe` calls and 200 `take_action` calls, with
input tokens dominating. The Skills reduce this by:

- reading the handbook only once;
- calling `observe` once per decision;
- keeping `notes` to a single sentence;
- keeping analysis short on routine turns;
- restricting `deep-analysis` and `check_latest_logs` to high-stakes turns
  and recovery after context loss.
