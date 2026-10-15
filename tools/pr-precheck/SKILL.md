---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

The pr-precheck tool answers the question "is this ready to submit" for a PR
package. A PR package is a candidate pull request, read against the plan it
claims to implement, and the issue that plan belongs to. The tool never answers
a different question, never grades more than one package per run, and always
answers based on evidence.

## Inputs and modes

This tool runs in one of the following:
* Live mode:
  * the `plan.md` (with deviation notes)
  * the diff on the dev branch vs main (use `git diff main...HEAD`)
  * draft PR
  * test evidence read against the issue
  * the issue evidence from the issue in the repo

* Eval mode
  * the package bundle text and nothing else

## The scope seam (live mode only)

Live mode:
Read `scope.md` before anything else. The scope names where the student's PR must
live and the rules of the environment. Refuse to grade work outside the scoped
repo, if the scope's repo line has an unfilled placeholder, stop without
grading and tell the student to fill in the Repo: line in `scope.md.`

Eval mode:
Ignore `scope.md.`

## The voice seam (live mode only)

Live mode: Read `voice-guide.md` for rules on how the student writes and hold the outgoing
PR against those rules. Report any rule the draft breaks in the summary.
Breaking these rules does not ever change the verdict.

Eval mode:
Ignore `voice-guide.md.`

## Component reads

`SKILL.MD`: what the tool does and how
`rubric.md`: the checks and the verdict rule
`procedure.md`: the steps you should take in the order you should take them
`scope.md`: where you should operate
`voice-guide.md`: the rules on how the student writes
`references/evidence-guide.md`: where the evidence lives in a PR package

If any of these are missing content, abort the run and state what is missing.

## Verdict and output

**Verdict**
Binary: `accept` (ready to submit) or `reject` (hold). There is no
third verdict, no "accept with reservations", no score. Reservations
belong in check evidence lines, not in the verdict.

**Output**

The tool's reply ends in a fenced JSON block and nothing follows it. The schema
is fixed and may not be altered, extended, or reordered:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

* Evidence-based: name evidence (fact(s) or quote(s)) that influenced a grading
  decision
* Adhere to the rubric: do not pass or fail based on vibes or your opinions,
  grade strictly following the rubric
* Adhere to the procedure: follow the procedure as written and do not deviate.
  If there are gaps, report them
