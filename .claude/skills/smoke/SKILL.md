---
name: smoke
description:
  Trace a change end-to-end and manually smoke-test it against a running instance. Takes what
  to test — a PR, branch, endpoint, command, or feature.
---

## Target

$ARGUMENTS

If no target is given, use the current branch's diff against the main branch.

## Your task

Trace the target end-to-end, then actually exercise it against a running instance. Do not
substitute reading code for running it, and do not substitute a surface-level success signal
for real verification.

### 1. Trace before touching anything

Read the actual code path the request/invocation travels: entry point, middleware or guards,
handler, service layer, background work, persistence. Note every branch that produces a
distinct observable outcome. Do not infer the path from names, tests, or the PR description —
read it. If the code changed since you last read it, re-read it.

### 2. Enumerate the paths to exercise

Cover the success path plus each failure and edge branch you found in the trace: invalid input,
auth failure, malformed or oversized payloads, duplicates and conflicts, missing dependencies.
The success path is the one most likely to be quietly broken, so never skip or assume it.

### 3. Build real, copy-pasteable invocations

Resolve every placeholder into a real value: query the datastore for existing records, mint
real credentials or tokens, discover the real host/port/URL from config rather than guessing.
The user must be able to paste and run what you produce with zero edits.

State clearly which setup steps mutate state. Get explicit approval before schema changes,
seeding, credential creation, or anything else that writes outside a throwaway scope. Respect
any project rules about which commands need permission.

### 4. Take your own backup before mutating

Before anything that can destroy or overwrite existing state — including running tests that
touch a real datastore — snapshot what could be lost to your scratchpad, and verify the
snapshot is complete and non-empty rather than assuming it worked. Never rely on the user
having a backup. Say where you put it.

### 5. Run them

Execute each invocation yourself against the running instance. If nothing is running, say so
and ask rather than reporting untested commands as verified. Capture status/exit codes and
response bodies, but treat those as the beginning of verification, not the end.

### 6. Verify past the surface

A success status code or a zero exit code is not proof the work happened. For each path, check
the layer where the effect actually lands:

- persisted rows/documents: exact status fields, foreign keys, error payloads
- asynchronous work: did it enqueue, did it drain, did it fail, is it retrying with backoff
- side effects: external calls, emitted events, logs, files, cache entries
- cleanup: did state get left behind, or did teardown delete more than it created

Where async or deferred work is involved, confirm the execution model (inline vs. queued vs.
scheduled) instead of assuming, since it changes how failures surface.

### 7. Root-cause every failure, and classify it

For anything that fails, read the code at the failure site and determine whether it is:

- **a real defect in the change** — blocking
- **an environment or configuration gap** (unreachable dependency, missing credentials, local
  execution-model difference) — not a defect, but say plainly that the path is therefore
  unverified rather than implying it passed

Do not present an inference as a confirmation. If you reason your way to a cause without
proving it, label it as an inference.

### 8. Report

Be concise. Lead with a compact table of path → observed result → verified outcome. Then:

- **Findings**, most severe first, each with the `file:line` that causes it and whether it
  blocks.
- **What you did NOT verify**, explicitly and without softening — paths you skipped, checks
  blocked by the environment, anything you assumed. This section is mandatory; if it is
  genuinely empty, say so outright.
- Correct anything you stated earlier in the session that this run disproved.

If asked whether the change is good to ship or approve, answer directly against the evidence
you actually gathered. Do not rubber-stamp because the work looks clean or the task is urgent,
and do not manufacture blockers to seem thorough. Distinguish what blocks from what is worth a
review comment, and leave the call to the user.
