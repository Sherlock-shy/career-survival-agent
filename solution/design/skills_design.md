# Skills design

Team: IQ2 Only. Submission: `adaptive-career-agent-v1`. Platform: JiuwenSwarm
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
adaptive-decision-planning  (choice rubric / Energy bundle / main action)
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
  - **Current version (v3):** a fixed priority order: survival, then
    defuse hidden risk, then Output, then Network, then Wealth. When the
    risk gauge is positive for the whole quarter ("risk low" mode), Output
    moves ahead of Defuse. Skill becomes the target once Output and Network are held at a level (the Skill pivot, added in v6). Health and Dignity are
    held in a team-chosen target band in the upper-middle of the scale
    (around 6–7), and raised to the top in the final quarter.
  - Earlier versions used a six-step **choice rubric**, applied in priority
    order (step 5, "do not over-protect Health", was listed between
    hidden-risk hygiene and cheap costs):
    1. survival first;
    2. the cap rule: a gain on a stat at its cap of 10 is worth zero, and
       a small loss on it is nearly free;
    3. raise the lagging promotion input, with extra weight on Output before
       a review;
    4. hidden-risk hygiene: resolve problems rather than bury them;
    5. accept only cheap costs.
  - An **Energy bundle** built from three slots: defensive, risk and growth.
    All 3 Energy is always spent.
  - A **main-action mapping** from the biggest current gap to the published
    effects of each main action, re-checked every quarter.
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
  Lagging: Output. Review in 2 months."

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
3. The hub scores every option with the rubric. Survival needs always outrank
   growth; growth goes to the lagging input.
4. Any option that looks risky passes through `risk-reward-gamble` before it
   can be chosen.
5. `deep-analysis` is the exception path, used only when steps 1–4 cannot be
   completed from visible information.

## 4. Why no scripts or reference files

The Skills contain only instructions. The decisions depend on reading event
text and judging it in context. A script would need fixed numbers or fixed
mappings, and the competition rules forbid hard-coded thresholds and
per-event answers. The handbook is available in-run through
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
