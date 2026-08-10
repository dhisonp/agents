---
name: buggy
description:
  Find bugs in a PR using 3 parallel subagents, then cross-check their findings to rule out
  false positives and write a final report. Use before merging or when asked to review a PR
  for bugs.
---

# Hunt for bugs in a PR

Get the diff for the PR under review (`gh pr diff`, or the diff against the base branch if no
PR number was given).

## 1. Run 3 subagents in parallel

Launch 3 subagents in a single message so they run concurrently. Give each one the same task:
find bugs in this PR. Do not tell them about each other — independent passes are the point.

Ask each to report every bug with:

- File and line
- Severity: critical / high / medium / low
- What breaks, and the concrete input or state that triggers it

## 2. Triage the findings

Once all 3 are done, merge their findings and dedupe. Then verify each one yourself — read the
actual code around it, follow the callers, check whether the described trigger is reachable.
Most findings from a single pass are wrong; assume a bug is a false positive until you have
confirmed it in the code.

Drop anything you can't confirm. A bug reported by all 3 agents can still be wrong, and one
reported by a single agent can still be real — confirm from the code, not by vote count.

Style nits, missing tests, and refactoring suggestions are not bugs. Drop those too.

## 3. Write the final report

Confirmed bugs only, ordered critical → low. For each: file:line, severity, what breaks, and
how it's triggered.

Then a short list of notable findings you ruled out and why, so the user can push back if they
disagree.
