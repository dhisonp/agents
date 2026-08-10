Create an `AGENTS.md` at the repository root following the cross-tool AGENTS.md standard. Its job is
to give coding agents the non-obvious context they need — and nothing they could already infer from
the code, manifests, or README. Redundancy measurably hurts agent performance, so every line must
earn its place.

## Step 1 — Explore before writing

Inspect the file tree, package manifests (`package.json`, `pyproject.toml`, `Cargo.toml`, etc.),
lockfiles, config files, CI workflows, the README, and any existing `CLAUDE.md` or `.cursorrules`.
Determine the real build/test/lint commands (exact flags), the actual directory-to-responsibility
mapping, and conventions that depart from language defaults. If a command is uncertain, verify it
against the scripts or CI config rather than guessing.

## Step 2 — Write the file

Plain markdown — no YAML frontmatter, no special syntax, just headings and bullets. Aim for 20–40
lines; accurate and short beats comprehensive and generic. Include a section only if this repo
genuinely warrants it:

- **Stack**: one line — frameworks, language versions, key tools.
- **Commands**: copy-pasteable build, test, single-test, lint, dev-server, and migration commands
  with real flags (`uv run pytest tests/unit/ -v`, never "run the unit tests").
- **Architecture**: directories mapped to responsibilities, plus any layering rules (e.g. handlers
  delegate to services).
- **Code Style**: only rules that differ from the language's defaults; skip anything a linter or
  formatter already enforces.
- **Testing**: test runner, how to run one test, what to mock and what not to.
- **Git workflow**: branch, commit, and PR conventions — only if the repo has established ones.
- **Boundaries**: files or directories the agent must never touch (generated code, legacy modules,
  secrets) and project-specific gotchas.

## Exclude

Standard language conventions, anything already in `package.json` scripts or the README, embedded
API docs (link instead), and filler like "write clean code." When unsure whether a line is obvious
to an agent, cut it.

## Monorepos

Keep repository-wide rules in the root file and note that the nearest `AGENTS.md` wins for any
subtree. Propose tailored per-package files only where the structure clearly calls for it.

Start lean. Treat this as a first draft to grow from real agent mistakes, not hypothetical ones.
When done, show me the file and note what you included or omitted and why, so I can trim further.
