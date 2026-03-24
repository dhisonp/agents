You are creating or updating a GitHub pull request. `$ARGUMENTS` is the text the user passed after
the command name (e.g. `/pr "feat: add thing"` → `$ARGUMENTS` = `feat: add thing`).

## Step 1 — Gather context (run all in parallel)

- `git branch --show-current`
- `git log main..HEAD --oneline`
- `git diff main...HEAD`
- `gh pr view --json number,title,body` — detect existing PR; capture title if found

For `gh pr view`: if it fails with a message like "no pull requests found", treat as **create
mode**. If it fails for any other reason (auth error, network error, etc.), stop and report the
error to the user.

## Step 2 — Determine mode and title, then validate early

**Mode**:

- `gh pr view` succeeded → **update mode**
- `gh pr view` failed with "no pull requests found" → **create mode**

**Title** (in priority order):

1. `$ARGUMENTS` if non-empty — use this in both modes
2. Existing PR title from `gh pr view` — update mode only
3. No title available in create mode → stop now and tell the user: `/pr "your title here"`

Stop here if condition 3 applies — do not proceed to analysis.

## Step 3 — Analyze

From the diff and branch name, determine:

- **Change type**: bug fix / new feature / breaking change (may be multiple)
- **Env changes**: did any `.env.example` lines change?
- **Tests**: were test files added or modified?
- **Ticket**: does the branch name match `feat/ABC-123/...` or `fix/ABC-123-...`? If so, extract the
  ticket ID.

## Step 4 — Write the PR body

Follow this template exactly:

```
## Describe your changes

<2–4 sentences of plain prose. What changed, why, and any notable trade-offs or follow-ups.
Write like a developer, not a press release. No nested bullets. Use GitHub callouts
(> [!NOTE] or > [!IMPORTANT]) only for genuinely critical cross-project dependencies or caveats.>

## (I)Issue or (N)Notion ticket number (if applicable)

<ticket ID extracted from branch name, or leave blank>

## Type of change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)

## Checklist before requesting a review
- [ ] My code follows the style guidelines of this project
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] My changes generate no new warnings
- [ ] I have performed a self-review of my code
- [ ] If it is a core feature, I have tested it thoroughly
- [ ] If it requires a .env change, have you added it to the .env.example file or in the PR description?

## Commands needed to test it
- [ ] uv sync
```

Fill in the template:

- Check the appropriate **Type of change** box(es) with `[x]`
- Check `[x]` checklist items that are verifiable from the diff; leave `[ ]` for items that don't
  apply or require human action (e.g. self-review, core feature testing)
- After `uv sync`, append any additional test commands relevant to the changes (e.g.
  `docker compose up`, `uv run pytest v1/tests/test_<module>.py`)

## Step 5 — Create or update the PR

**Create mode**:

```
gh pr create --title "<title>" --body "$(cat <<'EOF'
<generated body>
EOF
)"
```

**Update mode**:

```
gh pr edit --title "<title>" --body "$(cat <<'EOF'
<generated body>
EOF
)"
```

Output the PR URL.
