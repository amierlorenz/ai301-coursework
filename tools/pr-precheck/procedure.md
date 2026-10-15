# Procedure: how this tool grades a PR package

## Read order

1. Read the issue that the plan is for first to understand the problem that the
  author is solving.
2. Read the claim comment by the author of the plan in the issue thread.
3. Read the reproduction report comment by the author of the plan in the issue
  thread.
4. Read the plan comment by the author of the plan in the issue thread.
5. Read the prospective draft PR comment and diff being graded by this skill.

## Evidence gathering

live mode:
* no-drift: plan comment in issue thread by plan author, draft PR, diff against
  plan scope
* is-tested: reproduction comment in issue thread by plan author, draft PR
* reviewable: draft PR, diff 
* conforms-to-repo-standards: the repo's stated PR template and AI-use
  disclosure policy (check repo root, /docs, etc), draft PR

eval mode: quote relevant lines from the bundle

## Check execution

Grade each check pass, fail, or unclear with a one-line evidence quote or fact
for each grade. Unclear means the evidence needed is genuinely absent, not that
you did not look.

## Verdict assembly

Apply the rubric's verdict rule to produce the final verdict:
accept (ready to post) or reject (hold). There is no third verdict

In live mode, hold the drafts against voice-guide.md and note any broken rule
in the summary.
