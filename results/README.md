# Local test results

`local_runs.csv` lists every scored game we ran locally before the deadline
(1 November 2026): start time (UTC), version, setup, outcome, months
survived, score and tokens per month. The score is the simulator's score for
one game, from 0 to 100. The official score also counts documentation and
token efficiency, and averages three games.

We tested on two setups:

- **Competition model (DeepSeek V4 Flash):** the model the official runs
  used. Our credit for it was limited, so most versions got one or two games
  here.
- **Test lane (DeepSeek V4.1 Flash):** a newer model on our own key, which we
  used on 1 November to run up to ten games at once. It eliminated runs more
  often, so its scores are not directly comparable with the competition
  model's.

Some version folders were reused while the version was still being edited
(v10-final changed several times on 29 and 30 October), so games under one
name can come from slightly different files. In particular, v18's two games
on the competition model ran just before the last sections were added to the
five helper Skills; its three test-lane games ran the exact submitted files.

## Main versions

| Version | Setup | Games | Completed | Eliminated | Mean | Best |
|---|---|---|---|---|---|---|
| v0 (starter template) | competition model | 1 | 1 | 0 | 65.85 | 65.85 |
| v1.1 | competition model | 1 | 0 | 1 | 44.20 | 44.20 |
| v4 | competition model | 1 | 1 | 0 | 66.53 | 66.53 |
| v6 | competition model | 2 | 2 | 0 | 67.00 | 73.75 |
| v9 | competition model | 2 | 2 | 0 | 66.79 | 68.88 |
| v10 | competition model | 2 | 2 | 0 | 68.63 | 70.89 |
| v10-final | competition model | 5 | 4 | 1 | 57.90 | 72.50 |
| v18 (our cut-off 2 submission) | competition model | 2 | 2 | 0 | 67.18 | 69.07 |
| v18 | test lane | 3 | 2 | 1 | 52.83 | 66.57 |
| v18u | test lane | 13 | 11 | 2 | 59.04 | 73.50 |
| v18w (our final-round upload) | test lane | 42 | 23 | 19 | 48.41 | 74.65 |
| v18w2 | test lane | 11 | 9 | 2 | 56.14 | 73.97 |
| v18y | test lane | 13 | 7 | 6 | 46.41 | 73.12 |

The other versions and variants (v2 or v3, v7, v8, v11 to v17, v19, v19h,
and several v10 and v18 variants) are in the CSV.

## Official scores

| Round | Version | Score |
|---|---|---|
| Cut-off 1 | v6 | 65.86 |
| Cut-off 2 | v18 | 68.50 |
| Final round | v18w | 68.17 |

Our best official score was 68.50, and we finished 42nd of 90 teams. The
official 65.86 at cut-off 1 and the starter template's local 65.85 are
unrelated. v18w scored 68.17 officially, although its one local game on the
competition model was eliminated and it averaged 48.41 on the test lanes:
single games are very noisy, and the test lanes were harsher than the
official runs.
