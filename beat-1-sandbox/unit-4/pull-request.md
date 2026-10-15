# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

[Link to the pull request you opened. It must be the pull request's own page on the Path
Review repo, not your fork's branch page.]
https://github.com/codepath/pathreview-ai301-fa26-s3/pull/245

**Branch**

[The name of the branch the pull request comes from, exactly as it appears in your fork.
The naming shape is a type prefix, then the issue number, then a short description. **The
issue number in the branch name must be the number of the issue the pull request fixes** —
a name carrying any other number does not satisfy this field.]
fix/2-portfolio-website-ingestion

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

[The agreement score of each run you did, in order. A single run is a complete answer if
only one run occurred. **The last score in your list must match the agreement line in the
`eval-run.txt` you committed** — that file is the record of your final run.]
agreement: 20/20 scored items  (bar: 18/20: PASS)

**Package analysis**

[Pick one scored package (`pkg-01` through `pkg-20` — the four `calib-` packages are never
scored). Name it by id, say what your rubric decided and what the gold label said, and
explain why your rubric read it that way.]
pkg-03: rubric said reject, gold said reject. The description claims "Implements the posted plan
exactly" and "No other behavior changes", but the diff also rewrites `truncate` (commit
"refactor(directory): simplify truncation helpers") and adds a `resolve_symlinks` config field,
which the plan lists as "Not in scope: resolving symlinks ... or any config surface change". The
description never explains that extra work, so `no-drift` fails, and a failed required check
rejects the package.

**Check rationale**

[Quote one check from the `rubric.md` you uploaded to `tools/pr-precheck/`, exactly as it reads now.
Then say why it reads that way — what you revised to get there, or what you rejected in
favour of it.]
| no-drift | plan comment by author (in thread), draft PR, diff | passes if the PR diff matches the original plan comment or the description of the PR explains the discrepancy | required |

It includes the "or the description of the PR explains the discrepancy" clause because the
honest-outcome rule says a PR that discloses a shortfall can still be ready. I chose that over
requiring an exact diff-to-plan match, which would have rejected honest deviations. So the check
fails only drift nobody disclosed, as in pkg-03.

**Trade-offs**

[Every check gives something up. Any one of these is a complete answer: a package whose
result it changes, a canary you re-ran with `--only`, a case you accept it will miss, or a
stated reason nothing changed elsewhere. "Nothing changed, and here is how I know" earns
the point in full when the reason follows.]
A case I accept `reviewable` will miss: it counts any formatting noise (for example one trailing
space) or a leftover log line as debris, so a PR that is otherwise ready gets held over a trivial
blemish. I accept that false reject so debris-heavy PRs never get through. It cost nothing on this
set: all 7 clear-accept packages still came back accept (clear-accept 7/7 in `eval-run.txt`).

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
