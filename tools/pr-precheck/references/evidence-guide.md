# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: no-drift)

* Where it lives: 
  * Live: the plan comment in the issue thread and `plan.md` (its scope,
    "will not touch" lines, and deviation notes); the branch diff
    (`git diff main...HEAD --stat` for the file list, full diff for the
    hunks); the draft PR description's "what this changes" claims.
  * Eval: the plan-context block's scope pair (in-scope / not-in-scope)
    and any deviation notes; the candidate PR's diff and commit list; the
    candidate PR's description.

* What good looks like:
  * Live: every changed file and hunk falls inside the plan's stated
    scope or a deviation note in `plan.md` that the PR description
    repeats; every in-scope item the plan promised appears in the diff or
    is named in the description as dropped or deferred, with the reason.
  * Eval: same rule against the scope pair: nothing in the diff the
    scope pair excludes or never names, nothing the scope promises
    missing without a note, and every claim in the description ("fixes
    X", "adds a test for Y", "no behavior change elsewhere") is visible
    in the diff. Fails on an unexplained extra file or hunk, an
    unexplained missing piece, or a description that claims more or
    less than the diff does.


## Test evidence (harness category: not-tested)

* Where it lives:
  * Live: the draft PR's testing section and any captured test output
    (terminal logs, CI run on the branch); the author's reproduction
    comment in the issue thread (its steps and observed output); the
    plan's test plan.
  * Eval: the candidate PR's test-evidence section, read against the
    plan-context block's test plan and the reproduction's steps; any
    stated outcome of the repo's own checks or suite.

* What good looks like: the evidence reruns the reproduction's own
  steps and shows the before (the bug as the repro recorded it) and the
  after (the expected behavior the plan named, actually observed); the
  test the plan named (new or existing, by name or file) is shown
  failing before and passing after, or its passing run is shown; and the
  repo's own checks or suite were run with the outcome visible. Fails on
  "tests pass" or "verified locally" with no observable behavior,
  evidence for a different scenario than the repro, or the repo's checks
  never run. An honestly stated gap (e.g. "could not run the Windows
  job locally; CI will cover it") does not fail on its own if the core
  repro before/after is shown.


## Diff quality (harness category: unreviewable)

* Where it lives:
  * Live: the branch diff (`git diff main...HEAD`, including
    `--stat` for stray files) and the commit list (`git log
    main..HEAD`).
  * Eval: the candidate PR's unified diff and its commit list.

* What good looks like: every hunk serves the fix the plan describes,
  and a reviewer can find the fix without wading through anything else.
  Fails on any debris: commented-out code, leftover print/log/debug
  statements, hardcoded test values, unused code or imports, formatting
  churn (whitespace, reindentation, tabs/spaces mixing) on lines the
  fix did not need, drive-by edits to unrelated code, or committed
  build and environment artifacts (`.DS_Store`, `.env`, `build/`,
  lockfile changes with no dependency change). Commits that carry
  unrelated changes count the same as hunks that do.


## Standards and comms (harness category: conforms-to-repo-standards)

* Where it lives:
  * Live: the repo's PR template (`.github/pull_request_template.md`
    or `.github/PULL_REQUEST_TEMPLATE/`), `CONTRIBUTING.md`, and any
    AI-use policy (repo root, `/docs`, `.github/`); maintainer comments
    in the issue thread; the draft PR title and description.
  * Eval: the repo-facts block's PR template sections,
    contributing instructions, and stated policy (including AI-use
    disclosure); the issue context's maintainer direction; the candidate
    PR's title and description.

* What good looks like: every section the template asks for is
  present, in the template's order, with content specific to this
  change (not the template's placeholder text or "N/A" where the section
  applies); every stated contributing ask is honored (issue linked,
  title format, checklist items ticked truthfully); the AI-use
  disclosure is present in the form the policy asks, or the repo has no
  such policy; and any explicit maintainer direction in the thread
  (e.g. "add a test in X", "don't touch Y") is either followed or
  answered. Fails on a missing or reordered required section,
  boilerplate left unfilled, a missing required disclosure, or a
  maintainer ask the PR visibly ignores.

