> **DRAFT: rewrite this in your own words before publishing**, and fill in the [brackets].

# Career Survival Agent (Huawei Tech Arena UK 2026)

Team IQ2 Only: [names and roles].

For this challenge [link] we built an LLM agent that plays a 48-month career
simulation. Each month it reads events and chooses actions, trying to get
promoted high and early while keeping health, money and hidden risk under
control.

## What we built

The agent is six Skills: plain-English instruction files (`SKILL.md`) for
the competition's JiuwenSwarm platform. A hub Skill makes each decision and
consults five helper Skills. There is no code; the strategy is the rules in
`solution/`, which run on the competition's simulator (not included here).

## How we tested it

Over about 20 versions we ran about 170 local games, up to ten at once, and
read the logs of the bad games to decide each change. The 132 games that
reached a final score are listed in `results/`.

## Results

- Best official score: 68.50 at the second cut-off, with the version in
  `solution/`. The Skills and `manifest.json` are exactly as submitted; the
  tag `cutoff2-submission` marks the submitted files (apart from the video),
  and the design docs were corrected on 4 November 2026.
- Final round: 68.17 with a slightly later version (v18w, not included
  here). We finished 42nd of 90 teams.

## What went wrong and what we learned

- In a local game the no-strategy starter template scored 65.85. Our first
  scored Skills version (v1.1) scored 44.2 and was eliminated at month 33.
- One missing line in one Skill let Health hit zero in one game and drop to
  2 in another. Prompt files need testing like code.
- The same version scored 73.75 in one run and 60.25 in the next, so we
  switched to comparing averages.
- More rules did not always help: our final upload, with more rules,
  scored 68.17, just below v18's 68.50.
- We called some failures bad luck for too long. Checking each claim against
  our own logs earlier would have saved days.

## Use of AI

An AI assistant helped us draft the Skills and design documents from our
notes and test logs. We ran the tests and decided what to change. The design
documents follow the outline the organisers required.
