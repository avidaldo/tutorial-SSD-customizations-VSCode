# Tutorial 1: Foundations With Instructions And Prompt Files

This first tutorial teaches the smallest useful Copilot customization stack.

You will not build agents, hooks, or MCP yet. That is intentional. The goal of Part 1 is to make Copilot behave like a new team member who has already read your project conventions and already has one reliable entry point for specs-driven work.

---

## Learning Goals

By the end of this tutorial, the student should understand:

- the difference between always-on instructions and file-based instructions,
- why `applyTo` matters,
- when a prompt file is the right abstraction,
- and how to verify that Copilot is actually loading the customizations.

---

## Starting Point

You can start this tutorial in either of two ways:

1. Start from the repository as it is now, containing only tutorial documents.
1. Start from your own empty workspace and copy the tutorial files manually.

At this point, the repository should still contain no actual Copilot customization files. You are about to create them as part of the exercise.

---

## What This Part Builds

By the end of Part 1, the student will have created this local customization layer:

```text
.github/
  copilot-instructions.md
  instructions/
    spec-authoring.instructions.md
    backend-sdd.instructions.md
    backend-tests.instructions.md
  prompts/
    implement-spec.prompt.md
docs/
  specs/
    task-creation.md
```

This is enough to create a workspace where:

- Copilot automatically follows specs-driven development rules,
- different rules apply to specs, backend code, and tests,
- and there is a reusable slash command to start implementing from a specification.

---

## Prerequisites

You need:

1. Visual Studio Code.
1. The GitHub Copilot extension installed and signed in.
1. A trusted workspace.
1. A Git repository, or at least the intention to initialize one.

Recommended bootstrap command:

```bash
git init
mkdir -p .github/instructions .github/prompts docs/specs
```

Optional UI path:

```text
Chat: Open Customizations
```

---

## Why Start Here

There are two common early mistakes when people customize Copilot:

1. They start with custom agents before they have defined the repository rules.
1. They try to solve everything with a giant prompt.

Both are backwards.

Instructions come first because they are the cheapest way to encode standards that should apply again and again. Prompt files come next because they give the user a reliable entry point without forcing role orchestration too early.

---

## Step 1: Create Repository-Wide Instructions

Create `.github/copilot-instructions.md` with this content:

```markdown
# Task API Copilot Instructions

## Project intent
- This repository follows specs-driven development (SDD).
- Treat files in `docs/specs/` as the source of truth for feature behavior.
- If requested behavior is not in a specification, ask before implementing it.

## Technology choices
- Use Node.js and TypeScript for backend code.
- Prefer small modules and explicit types.
- Keep HTTP routing, validation, and business logic separated.

## Implementation rules
- Do not implement a feature until the relevant spec file has been read.
- Every function, route handler, controller, or service that directly fulfills a requirement must include a JSDoc block with `@spec <requirement-id>`.
- If a specification is ambiguous, stop and ask clarifying questions instead of guessing.
- Prefer minimal, traceable changes over broad scaffolding.

## Testing rules
- Use Jest for unit tests.
- Add at least one test for each edge case named in a specification.
- Mock external I/O in unit tests.
```

### Optional shortcut for repository instructions

If your Copilot build supports it, you can ask VS Code to generate an initial repository instruction file:

```text
/init
```

Copilot builds will then ask what the repository is about. You can paste this description:

```text
This repository is a Node.js and TypeScript task management REST API that follows specs-driven development. Files in docs/specs/ are the source of truth for feature behavior. Keep HTTP routing, validation, and business logic separated. Use Jest for unit tests, and add @spec <requirement-id> JSDoc tags where code directly fulfills a requirement.
```

If you use `/init`, treat the generated result as a draft. The explicit file shown above remains the canonical tutorial path. Compare the generated output with it, then tighten it manually before continuing.

### Why this file exists

This file is for rules that should apply almost all the time:

- project intent,
- stack choice,
- traceability discipline,
- and general testing expectations.

Do not overload it with language-specific or folder-specific rules. Those belong in file-based instruction files.

---

## Step 2: Add File-Based Instructions

### Why file-based instructions exist

Not every rule belongs everywhere.

For example:

- spec authoring rules apply to markdown specs,
- backend implementation rules apply to TypeScript source files,
- test-writing rules apply to test files.

That is exactly what `*.instructions.md` files and `applyTo` are for.

### Important rule of thumb

Avoid `applyTo: "**"` unless the instruction is truly universal. Broad instruction files consume context and blur responsibilities.

Create `.github/instructions/spec-authoring.instructions.md`:

```markdown
---
name: Spec Authoring Rules
description: Use when writing or revising markdown specifications in docs/specs. Defines the expected structure for requirement IDs, edge cases, and open questions.
applyTo: "docs/specs/**/*.md"
---

# Spec authoring rules

- Use stable requirement IDs such as `REQ-TC-01`.
- Separate business requirements from implementation ideas.
- Include explicit edge cases and error responses.
- Prefer testable language such as "must", "must not", and "returns 400 when...".
- If something is intentionally undecided, add an `Open questions` section instead of hiding ambiguity in prose.
```

Create `.github/instructions/backend-sdd.instructions.md`:

```markdown
---
name: Backend SDD Implementation Rules
description: Use when implementing TypeScript backend code from specs in docs/specs for the task API.
applyTo: "src/**/*.ts"
---

# Backend SDD implementation rules

- Read the relevant spec in `docs/specs/` before editing application code.
- Add `@spec <requirement-id>` to the JSDoc block of any function, route, controller, or validator that directly fulfills a requirement.
- Keep request validation near the HTTP boundary.
- Keep business rules out of thin route wiring.
- If a requirement cannot be mapped cleanly to code, ask a clarifying question instead of inventing behavior.
```

Create `.github/instructions/backend-tests.instructions.md`:

```markdown
---
name: Backend Test Rules
description: Use when writing Jest tests for specs-driven backend TypeScript code.
applyTo: "tests/**/*.test.ts"
---

# Backend test rules

- Use Jest.
- Map tests back to requirement IDs in test names or comments.
- Add dedicated tests for invalid input and error responses defined in the spec.
- Mock database and network calls in unit tests.
- Keep each test focused on one requirement or one edge case.
```

### Optional shortcut for file-based instructions

```text
/create-instruction Create a file-based instruction for TypeScript backend files that enforces specs-driven development and @spec JSDoc tags.
```

---

## Step 3: Write The First Specification

Why add a spec now:

Prompts and later agents need a concrete source of truth. Without at least one real spec file, the workflow is still abstract.

Create `docs/specs/task-creation.md`:

```markdown
# Task Creation Specification

## Goal
Create tasks through a REST endpoint.

## Requirements
- **REQ-TC-01**: The system must expose a `POST /tasks` endpoint.
- **REQ-TC-02**: The request payload must include `title` as a string with a maximum length of 50 characters.
- **REQ-TC-03**: If `status` is omitted, the system must default it to `To-Do`.
- **REQ-TC-04**: If `title` exceeds 50 characters, the API must return HTTP 400 with the message `Title too long`.

## Edge cases
- Missing `title`.
- Empty string `title`.
```

Verify it works:

Ask Copilot:

```text
Summarize the requirements in docs/specs/task-creation.md.
```

The answer should preserve the requirement IDs rather than paraphrasing them away.

---

## Step 4: Create The First `implement-spec` Prompt

### Why a prompt is enough here

At this stage, "implement a feature from a spec" is still a focused, user-invoked task.

That makes a prompt file the right starting abstraction.

Create `.github/prompts/implement-spec.prompt.md`:

```markdown
---
name: implement-spec
description: Implement a feature from a markdown specification in docs/specs.
argument-hint: "<spec-file-name>.md"
agent: agent
tools: ["read", "search", "edit"]
---

Use [docs/specs](../../docs/specs) as the source of truth.

The target specification file is `${input:specFile:task-creation.md}`.

Workflow:
1. Read the target spec.
2. List the requirement IDs you found.
3. If the spec is ambiguous, stop and ask clarifying questions.
4. If the spec is clear, implement the smallest correct change set.
5. Add `@spec <requirement-id>` JSDoc tags where code directly fulfills a requirement.
6. Summarize which files were created or changed and map them to requirement IDs.

Before writing code, explicitly state which instruction files are relevant.
```

### Optional shortcut for prompt creation

```text
/create-prompt Create a prompt named implement-spec that implements a feature from a specification file in docs/specs and states which instruction files it is following before it writes code.
```

### What this prompt is not yet

It is not a skill.

That matters.

Right now the reusable unit is still a single user entry point. In Part 2, you will see what changes when the workflow becomes a richer capability that deserves to become a skill.

---

## Step 5: Verify Part 1

Use these checks.

### Check 1: repository-wide instructions are loaded

Ask Copilot:

```text
Summarize this project's engineering rules before any code is generated.
```

Expected outcome:

- the answer mentions specs-driven development,
- it mentions `docs/specs/` as the source of truth,
- and the References section includes `.github/copilot-instructions.md`.

### Check 2: the prompt file is discovered

Open chat and type `/`.

Expected outcome:

- `implement-spec` appears in the slash-command list.

If it does not:

- confirm the file is under `.github/prompts/`,
- confirm it ends with `.prompt.md`,
- or run `Chat: Run Prompt` from the Command Palette.

### Check 3: the instruction files are structured correctly

You cannot fully prove `applyTo` behavior yet because `src/` and `tests/` files do not exist, but you can still inspect:

- the files are in `.github/instructions/`,
- each has YAML frontmatter,
- each uses a targeted `applyTo`,
- and each description is specific and keyword-rich.

---

## What Should Exist Now

```text
.github/
  copilot-instructions.md
  instructions/
    spec-authoring.instructions.md
    backend-sdd.instructions.md
    backend-tests.instructions.md
  prompts/
    implement-spec.prompt.md
docs/
  specs/
    task-creation.md
```

---

## What You Should Understand Now

By the end of Part 1, the student should be able to explain:

- why repository-wide instructions and file-based instructions are different,
- why `applyTo` is preferable to broad always-on rules when only certain files need the guidance,
- and why a prompt is a better abstraction than a skill at this early stage.

---

## What Part 2 Adds

Part 2 adds the next layer of complexity:

- a deliberate prompt-to-skill upgrade,
- a reusable validation capability,
- and specialized agents for reading specs, implementing code, and coordinating the workflow.

Continue with [tutorial-2-skills-and-agents.md](./tutorial-2-skills-and-agents.md).
