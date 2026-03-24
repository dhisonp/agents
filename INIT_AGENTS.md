Analyze the current repository to generate a context file named `AGENTS.md`. This file will serve as
the strict source of truth for AI agents working in this codebase.

**Output Requirements:**

1. Return raw Markdown only.
2. Do not use conversational filler; be objective and factual.
3. Adhere strictly to the section headers and formatting conventions defined below.

**Structure & Content Instructions:**

**1. Header**

- Title: `# AGENTS.md`
- Description: State that this is the canonical playbook for coding agents. Mention that
  model-specific quirks (e.g., `CLAUDE.md`) live alongside this file.

**2. ## Project Summary**

- Analyze `package.json`, `go.mod`, `Cargo.toml`, or `requirements.txt`.
- Provide a bulleted list of the core stack (e.g., Frontend framework, Backend language, Database,
  Build tools).

**3. ## Repository Layout**

- **Critical:** Generate a visual tree structure inside a code block.
- Map key directories to their purpose.
- Format:
  ```
  root/
  ├── folder_a/           Description of contents
  ├── folder_b/           Description of contents
  └── file.config         Description
  ```
- Note any path aliases (e.g., `@/*` maps to `./src/*`) immediately after the tree.

**4. ## Core Commands**

- Extract script names and definitions from `package.json`, `Makefile`, or `Justfile`.
- Format: `- `command`: Description of what it does`.
- Include dev, build, test, lint, and format commands.

**5. ## Coding & Styling Guidelines**

- Infer rules from `tsconfig.json`, `.eslintrc`, and existing file naming conventions (e.g.,
  PascalCase for components, kebab-case for utilities).
- Note specific patterns (e.g., "Use explicit types," "Prefer specific import aliases").

**6. ## Testing Expectations**

- Identify the test runner (Vitest, Jest, Pytest, etc.).
- Define where tests should be located (colocated vs `tests/` directory).
- Note command expectations (e.g., "Always run typecheck before commit").

**7. ## Development Environment Notes**

- Note how the dev server behaves (auto-rebuilds, hot-reloading).
- Specific instructions for container/backend management (e.g., "Do not restart manual containers").

**8. ## Security & Configuration**

- Standard instructions regarding `.env` files and secret management.
- Mention where local overrides belong.

**9. ## Working With Agents**

- Include this exact text:
  - Treat `AGENTS.md` as the source of truth for shared workflows.
  - Keep per-agent documents lean—focus on deviations or ergonomics specific to that model and link
    back here for the baseline instructions.
