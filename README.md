# ai301-coursework

My coursework for CodePath AI301 (Fall 2026), a course on making a first open-source contribution with help from Claude Code skills I build along the way.

The four skills in `tools/` grade each step of a contribution before it goes upstream:

| Skill | Grades |
|---|---|
| `issue-select` | Whether a candidate issue is worth taking as a first contribution |
| `repro-check` | A claim comment and reproduction report, before posting |
| `plan-check` | A plan and plan comment, before posting and building |
| `pr-precheck` | A pull request against its plan and issue, before submitting |

## What lives where

- `tools/`: your installed skill folders, one per unit in Beat 1. Unit 1
  is `issue-select/`, unit 2 `repro-check/`, unit 3 `plan-check/`, unit 4
  `pr-precheck/`. Upload the current files from your installed copy
  (`~/.claude/skills/<skill>/`) when you submit.
- `beat-1-sandbox/`: one folder per unit for units 1-4. Each holds that
  unit's write-up (its links, a short reflection, and your account of the
  eval iterations behind the run) and `eval-run.txt`, one complete eval
  run written by the harness itself with `--save-run`. Unit 3's folder
  also holds your `plan.md`.
- `beat-2-contribution/`: `contribution-story.md` is Assignment 5, the
  graded story of your real contribution, built across units 5-8 and
  submitted once. A second contribution is welcome and never graded: copy
  `extra-round-template.md` to `contribution-story-2.md`.
- `beat-3-portfolio/`: `toolkit-portfolio.md` is Assignment 6, the unit-9
  toolkit portfolio. It is the skeleton you fill in unit 9; until then it
  waits.

In units 1-4 everything you upload goes to two places: `tools/` for that
unit's skill folder, and `beat-1-sandbox/unit-N/` for its write-up and
eval run. Beats 2 and 3 are one file each, and you submit the same repo
link every unit.
