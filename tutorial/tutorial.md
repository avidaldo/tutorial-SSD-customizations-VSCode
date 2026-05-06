# Tutorial Series: Building a Specs-Driven Copilot Workflow in VS Code

This repository now uses a tutorial-series structure instead of one very large walkthrough.

That change is deliberate.

The previous version of this material tried to teach instructions, prompts, skills, agents, hooks, and MCP in one continuous session. That is realistic from an architecture perspective, but it is too much to absorb well in one pass.

This new version keeps the same end goal and the same Task Management API scenario, but splits the learning path into four sessions. Each session introduces one new layer of Copilot customization, ends with a usable workflow, and prepares the student for the next one.

The design principle is simple:

- start with the smallest useful layer,
- verify that it works,
- then add one new source of power,
- and only then move to the next abstraction.

---

## What This Series Builds

Across the four tutorials, the student builds a realistic Copilot customization stack for a future Task Management REST API built with Node.js and TypeScript.

By the end of the full series, the student will understand how to design and use:

- repository-wide instructions,
- file-targeted instructions,
- reusable prompt files,
- skills for structured capabilities,
- custom agents with specialized roles,
- hooks for deterministic automation,
- and MCP for external tools and data.

The target architecture still looks like this by the end:

```text
.github/
  copilot-instructions.md
  instructions/
    spec-authoring.instructions.md
    backend-sdd.instructions.md
    backend-tests.instructions.md
  prompts/
    implement-spec.prompt.md
    check-spec-coverage.prompt.md
  skills/
    spec-validation/
      SKILL.md
      report-template.md
  agents/
    spec-reader.agent.md
    implementer.agent.md
    lead-architect.agent.md
  hooks/
    sdd-post-edit.json
.vscode/
  mcp.json
scripts/
  check-spec-tags.mjs
docs/
  specs/
    task-creation.md
```

At the start of the series, none of those files exist yet. The repository intentionally begins with tutorial documents only.

---

## Why The Series Is Split

The split is not cosmetic. It reflects real differences in abstraction level.

- Instructions teach standing rules.
- Prompt files teach reusable task entry points.
- Skills teach reusable capabilities.
- Agents teach role separation and orchestration.
- Hooks teach deterministic automation.
- MCP teaches external integration.

If you teach all of that at once, the student can copy files without developing any judgment about when one primitive is better than another.

This series is designed to teach that judgment.

---

## The Four Tutorials

### Part 1: Foundations

See [tutorial-1-foundations.md](./tutorial-1-foundations.md).

This part introduces the lowest-friction, highest-leverage customizations:

- `.github/copilot-instructions.md`
- `.github/instructions/*.instructions.md`
- a first reusable prompt file

What this part deliberately excludes:

- skills
- agents
- hooks
- MCP

What the student has at the end:

- a workspace where Copilot already behaves differently because the project rules are explicit,
- and a reusable entry point for spec-driven implementation.

### Part 2: Reusable Workflows And Roles

See [tutorial-2-skills-and-agents.md](./tutorial-2-skills-and-agents.md).

This part introduces the next two abstractions that usually get confused with prompts:

- skills for structured reusable capabilities,
- custom agents for specialized roles and tool boundaries.

What this part deliberately excludes:

- hooks
- MCP

What the student has at the end:

- a reusable validation skill,
- a multi-agent workflow built around `SpecReader`, `Implementer`, and `LeadArchitect`,
- and a clear mental model for prompt vs skill vs agent.

### Part 3: Guardrails And Full Local Execution

See [tutorial-3-hooks-and-local-workflow.md](./tutorial-3-hooks-and-local-workflow.md).

This part introduces deterministic automation and end-to-end simulation:

- workspace hooks in `.github/hooks/*.json`
- a post-edit SDD traceability check
- a full local run from ambiguous spec to clarified spec to implementation and validation

What this part deliberately excludes:

- MCP

What the student has at the end:

- a locally complete workflow with instructions, prompts, skills, agents, and hooks all working together.

### Part 4: External Tools And Data With MCP

See [tutorial-4-mcp-integration.md](./tutorial-4-mcp-integration.md).

This part introduces MCP as a user of VS Code, not as an extension author:

- what MCP is,
- when it belongs in the architecture,
- how to install or configure an MCP server,
- how MCP tools become available in chat,
- and how to connect those tools to the existing workflow.

What this part deliberately excludes:

- building a custom MCP server,
- writing a VS Code extension that registers one,
- packaging a plugin that bundles MCP,
- turning the tutorial into an extension-development course.

What the student has at the end:

- the same local workflow as Part 3,
- plus one external integration layer available through MCP.

### A note about “an extension with an MCP”

If you want a visible marketplace-style MCP example, Part 4 includes that perspective. The main teaching path still treats MCP as something the student installs or configures in VS Code and then uses from chat.

That is the right complexity level for this series.

---

## How To Use The Series

This series uses a hybrid continuity model.

Each tutorial is designed so it can be read on its own, but each one also extends the previous part.

Each part therefore starts with a “Starting point” section that explains two valid paths:

1. continue from the previous part,
2. or start from a prepared baseline state.

This makes the series practical for:

- self-paced learning,
- multi-session workshops,
- classroom teaching,
- and revisiting the material after a break.

---

## Suggested Teaching Order

Use the parts in this order:

1. [tutorial-1-foundations.md](./tutorial-1-foundations.md)
2. [tutorial-2-skills-and-agents.md](./tutorial-2-skills-and-agents.md)
3. [tutorial-3-hooks-and-local-workflow.md](./tutorial-3-hooks-and-local-workflow.md)
4. [tutorial-4-mcp-integration.md](./tutorial-4-mcp-integration.md)

Do not jump to Part 4 first.

MCP makes the most sense only after the student already understands local customizations. Otherwise MCP looks like just another configuration file instead of what it really is: the bridge to external tools and data.

---

## Stable Vs Preview In This Series

The series intentionally separates stable material from preview-heavy material.

| Topic | Status | Where it appears |
| --- | --- | --- |
| `copilot-instructions.md` | Stable | Part 1 |
| `.instructions.md` with `applyTo` | Stable | Part 1 |
| `.prompt.md` files | Stable | Part 1 |
| Skills in `.github/skills/` | Stable | Part 2 |
| Custom agents in `.github/agents/` | Stable | Part 2 |
| Handoffs in custom agents | Stable | Part 2 |
| Autonomous subagent delegation | Model-driven | Part 2 |
| Workspace hooks in `.github/hooks/*.json` | Preview | Part 3 |
| Agent-scoped hooks in agent frontmatter | Preview | Mentioned as a contrast in Part 3 |
| MCP as installed/configured server usage | Stable | Part 4 |
| Agent plugins | Preview | Mentioned only as optional context in Part 4 |
| Extension-level MCP registration | Advanced | Out of scope for this series |

---

## What Changed From The Previous Tutorial

The earlier version of this repository tried to show the entire layered architecture in one file.

This new structure keeps the same architecture, but distributes it across sessions so the student can actually internalize the reasoning.

The key editorial changes are:

- the root tutorial is now the roadmap,
- detailed steps live in part-specific files,
- MCP is included later and at the correct complexity level,
- and each part has a clear stopping point.

---

## Official Documentation This Series Tracks

If behavior changes in a future VS Code release, use these references first:

- VS Code Copilot customization overview: <https://code.visualstudio.com/docs/copilot/customization/overview>
- Custom instructions: <https://code.visualstudio.com/docs/copilot/customization/custom-instructions>
- Prompt files: <https://code.visualstudio.com/docs/copilot/customization/prompt-files>
- Agent skills: <https://code.visualstudio.com/docs/copilot/customization/agent-skills>
- Custom agents: <https://code.visualstudio.com/docs/copilot/customization/custom-agents>
- Subagents: <https://code.visualstudio.com/docs/copilot/agents/subagents>
- Hooks: <https://code.visualstudio.com/docs/copilot/customization/hooks>
- MCP servers: <https://code.visualstudio.com/docs/copilot/customization/mcp-servers>
- Agent plugins: <https://code.visualstudio.com/docs/copilot/customization/agent-plugins>
- Tools with agents: <https://code.visualstudio.com/docs/copilot/agents/agent-tools>
- Customize AI for your project guide: <https://code.visualstudio.com/docs/copilot/guides/customize-copilot-guide>

---

## Start Here

Begin with [tutorial-1-foundations.md](./tutorial-1-foundations.md).

If you already understand instructions and prompt files and want to jump ahead, start with [tutorial-2-skills-and-agents.md](./tutorial-2-skills-and-agents.md).

If your specific goal is “how do I extend this workflow with an MCP server?”, go to [tutorial-4-mcp-integration.md](./tutorial-4-mcp-integration.md), but only after you understand the local workflow from Parts 1 through 3.# Tutorial: Designing a Specs-Driven Multi-Agent Copilot Workflow in VS Code

This tutorial is intentionally about design before implementation.

At the start of this repository, it is correct for the repo to contain only this file. The goal is not to rush into building a Task Management REST API. The goal is to first build a realistic GitHub Copilot customization stack in VS Code that can later build that API in a controlled, specs-driven way.

In other words:

- First we define how the AI should behave.
- Then we define how humans should invoke it.
- Then we define how specialized agents should collaborate.
- Then we add deterministic automation where advisory prompting is not enough.
- Only after that do we ask the system to generate project code.

That order matters. It is the difference between "AI helped me type faster" and "AI works inside an explicit engineering system."

---

## What You Will Build

By the end of the tutorial, the student following it will have designed a layered Copilot customization setup for a future Task Management API:

```text
.github/
   copilot-instructions.md
   instructions/
      spec-authoring.instructions.md
      backend-sdd.instructions.md
      backend-tests.instructions.md
   prompts/
      implement-spec.prompt.md
      check-spec-coverage.prompt.md
   skills/
      spec-validation/
         SKILL.md
         report-template.md
   agents/
      spec-reader.agent.md
      implementer.agent.md
      lead-architect.agent.md
   hooks/
      sdd-post-edit.json
scripts/
   check-spec-tags.mjs
docs/
   specs/
      task-creation.md
```

You will also understand why each file exists, when that primitive is the right abstraction, and when it would be better to use something else.

---

## Scope And Ground Rules

### What this tutorial is doing

- It teaches VS Code Copilot customization primitives in a realistic, layered way.
- It uses Specs-Driven Development (SDD): specifications in `docs/specs/` are treated as the source of truth for behavior.
- It keeps the future project simple on purpose: a Node.js + TypeScript Task Management REST API.
- It includes copy-paste-ready commands and file contents for every step.

### What this tutorial is not doing yet

- It is not asking you to install a database or define an MCP server.
- It is not assuming the generated code is automatically correct.
- It is not treating agent orchestration as magic.

### Important version note

VS Code Copilot evolves quickly. This tutorial is aligned with the current customization documentation as of May 2026, but some surfaces are still preview or model-dependent.

In particular:

- Hooks are a Preview feature.
- The Agent Customizations editor is still Preview.
- Subagent behavior is real, but the exact moment when one agent decides to delegate to another is model-driven, not guaranteed.
- Slash commands such as `/init`, `/create-prompt`, `/create-instruction`, `/create-skill`, `/create-agent`, and `/create-hook` may vary by extension version, agent mode, or product surface.

Because of that, this tutorial uses manual file creation as the canonical path, and treats built-in slash commands as optional shortcuts.

---

## Official Documentation This Tutorial Tracks

If behavior changes in a future VS Code release, check these pages first:

- VS Code Copilot customization overview: <https://code.visualstudio.com/docs/copilot/customization/overview>
- Custom instructions: <https://code.visualstudio.com/docs/copilot/customization/custom-instructions>
- Prompt files: <https://code.visualstudio.com/docs/copilot/customization/prompt-files>
- Agent skills: <https://code.visualstudio.com/docs/copilot/customization/agent-skills>
- Custom agents: <https://code.visualstudio.com/docs/copilot/customization/custom-agents>
- Subagents: <https://code.visualstudio.com/docs/copilot/agents/subagents>
- Hooks: <https://code.visualstudio.com/docs/copilot/customization/hooks>
- Tools with agents: <https://code.visualstudio.com/docs/copilot/agents/agent-tools>
- Customize AI for your project guide: <https://code.visualstudio.com/docs/copilot/guides/customize-copilot-guide>

---

## How To Read This Tutorial

Each major step answers four questions:

1. What is this primitive?
2. Why are we using it here?
3. Exactly what file or command do we create?
4. How do we verify that it is working?

Whenever there is more than one reasonable design choice, the tutorial says so explicitly.

That is intentional. A good engineering tutorial should not only say "do this." It should also teach "why this, and not the other plausible thing?"

---

## Decision Framework: Which Primitive Should You Use?

Before writing any customization, you need a mental model for choosing the right one.

| Primitive | Use it when | Do not use it when | Typical file location | Stability |
| --- | --- | --- | --- | --- |
| Always-on instructions | You need repository-wide rules that should influence every chat request | You need task-specific workflow logic | `.github/copilot-instructions.md` | Stable |
| File-based instructions | You need different rules for different files or folders | You want a user-invoked workflow | `.github/instructions/*.instructions.md` | Stable |
| Prompt file | You repeat the same focused user task often | You need bundled resources, scripts, or automatic discovery | `.github/prompts/*.prompt.md` | Stable |
| Skill | You want a reusable capability with multi-step logic and optional resources | You only need a one-line reusable prompt | `.github/skills/<name>/SKILL.md` | Stable |
| Custom agent | You need a persistent role, restricted tool access, or orchestrated specialization | You only need a one-off slash command | `.github/agents/*.agent.md` | Stable |
| Subagent workflow | You want one agent to delegate focused work to another | You need deterministic sequencing every time | Configured through custom agents and agent tools | Model-driven |
| Hook | You need deterministic enforcement or automation at agent lifecycle events | A plain instruction would be enough | `.github/hooks/*.json` | Preview |
| MCP | You need external data, APIs, databases, or systems | The AI only needs local code and tools | MCP config, not covered here | Stable, but out of scope for this tutorial |

### A practical rule of thumb

- Start with instructions if the rule should almost always apply.
- Start with a prompt if the workflow is user-invoked and still simple.
- Promote that prompt to a skill once the workflow becomes a reusable capability with structure or resources.
- Use custom agents when role separation matters.
- Use hooks only when you need deterministic automation, not polite suggestions.

### A common mistake

Beginners often try to solve everything with a single prompt.

That works for a day. Then the prompt becomes a dumping ground for coding rules, workflow steps, validation logic, tool hints, and review behavior. At that point the prompt is trying to be four different primitives at once.

This tutorial deliberately avoids that mistake.

---

## Prerequisites

You need:

1. Visual Studio Code.
2. The GitHub Copilot extension installed and signed in.
3. An open workspace folder.
4. A Git repository, or at least the intention to initialize one.
5. A trusted workspace.

Recommended bootstrap command:

```bash
git init
```

Optional convenience command for later steps:

```bash
mkdir -p .github/instructions .github/prompts .github/skills/spec-validation .github/agents .github/hooks scripts docs/specs
```

If you prefer the UI instead of manual file creation, open the Agent Customizations editor with:

```text
Chat: Open Customizations
```

---

## Phase 1: Establish The Rules Of The System

We start with instructions, because instructions are the lowest-friction way to teach Copilot the project's permanent rules.

### Why instructions come first

At this stage there is no app code yet. That is exactly why instructions matter.

If you wait until after code generation to define your standards, you are already in cleanup mode. Instead, we want the first generated file to already reflect the repository's intent.

### Step 1: Create Repository-Wide Instructions

What this primitive is:

`copilot-instructions.md` is an always-on instruction file. VS Code automatically includes it in chat requests for the workspace.

Why we use it here:

These rules should apply no matter which agent, prompt, or skill is later used.

Manual path:

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

Optional shortcut:

If your Copilot version supports it, you can ask VS Code to generate an initial workspace instruction file:

```text
/init
```

If you use `/init`, treat the generated file as a draft. Review it and tighten it manually. Generator output is a starting point, not the final standard.

#### Why not use `AGENTS.md` here?

You could. VS Code supports `AGENTS.md` as an always-on instruction source. We are choosing `.github/copilot-instructions.md` because it is the clearest and most conventional repository-level instruction file for this tutorial.

Use `AGENTS.md` when you want one always-on file shared across multiple agent ecosystems or nested subfolders. Use `.instructions.md` when rules should target only certain file types.

Verify it works:

Ask Copilot a simple question about the project and inspect the References section in the response:

```text
Summarize this project's engineering rules before any code is generated.
```

Success looks like this:

- The answer mentions specs-driven development.
- The answer mentions `docs/specs/` as the source of truth.
- The References section includes `.github/copilot-instructions.md`.

---

### Step 2: Add File-Specific Instructions

What this primitive is:

`*.instructions.md` files are conditional instructions. They apply when files match `applyTo`, or when the task description semantically matches the instruction description.

Why we use them here:

Not every rule should apply everywhere.

For example:

- Spec-writing rules should apply to `docs/specs/**/*.md`.
- Implementation rules should apply to `src/**/*.ts`.
- Test-writing rules should apply to `tests/**/*.test.ts`.

If you make all of those always-on, you waste context and blur responsibilities.

#### Important best practice

Avoid `applyTo: "**"` unless the rule is truly universal. Overly broad instruction files consume context and make it harder for the model to focus on the task at hand.

Manual path:

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

Optional shortcut:

If your Copilot build exposes the generator, you can create targeted instructions with:

```text
/create-instruction Create a file-based instruction for TypeScript backend files that enforces specs-driven development and @spec JSDoc tags.
```

Verify it works:

You will verify these more concretely in later phases when `docs/specs/`, `src/`, and `tests/` files actually exist.

For now, the most important checks are:

- The files are in `.github/instructions/`.
- The frontmatter uses `applyTo`.
- The descriptions are specific and keyword-rich.

If instruction files later fail to apply:

- Check the References section of the chat response.
- Open Chat diagnostics.
- Re-check the `applyTo` patterns.

What should exist now:

```text
.github/
   copilot-instructions.md
   instructions/
      spec-authoring.instructions.md
      backend-sdd.instructions.md
      backend-tests.instructions.md
```

---

## Phase 2: Start Simple With Prompt Files

Prompt files are reusable slash commands. They are ideal for repeated, user-invoked tasks that are still focused enough to fit in one prompt.

We will start with prompts before agents and skills because that is the simplest useful layer.

### Step 3: Create The First Version Of `implement-spec`

What this primitive is:

A prompt file is a reusable slash command in chat.

Why we use it here:

At first, "implement a feature from a spec" looks like a single repeated task. That makes a prompt the right first abstraction.

Later we will improve it.

Manual path:

Create `.github/prompts/implement-spec.prompt.md` with this first version:

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

Optional shortcut:

```text
/create-prompt Create a prompt named implement-spec that implements a feature from a specification file in docs/specs and states which instruction files it is following before it writes code.
```

#### Why this is good, but not perfect

This is a solid start because:

- It is easy to invoke.
- It makes the workflow reusable.
- It forces the model to start from the spec.

But it still has limitations:

- It mixes research, implementation, and traceability into one surface.
- It does not yet separate roles.
- It assumes one agent can do everything well enough.

That will become a design pressure later.

Verify it works:

Open chat and type `/`. The prompt should appear in the slash-command list.

If it does not:

- Confirm the file is under `.github/prompts/`.
- Confirm the extension is `.prompt.md`.
- Try `Chat: Run Prompt` from the Command Palette.

---

### Step 4: Create A Temporary Validation Prompt

This step is deliberately pedagogical.

We are going to create something that is reasonable, but not our final choice.

#### Why do this on purpose?

Because good engineering judgment comes from comparing plausible designs, not from memorizing slogans.

At first glance, "check whether code covers the spec" also looks like a reusable prompt.

That is true.

It is just not the best long-term abstraction.

Manual path:

Create `.github/prompts/check-spec-coverage.prompt.md`:

```markdown
---
name: check-spec-coverage
description: Compare a spec file with the current implementation and report requirement coverage.
argument-hint: "<spec-file-name>.md"
agent: agent
tools: ["read", "search"]
---

Use [docs/specs](../../docs/specs) as the source of truth.

The target specification file is `${input:specFile:task-creation.md}`.

Read the target spec and the files mentioned by the user or currently open in the editor.

Produce a Markdown table with these columns:
- Requirement ID
- Covered (Yes/No/Unclear)
- Evidence found
- Notes

Do not edit files.
If coverage is unclear, say `Unclear` instead of guessing.
```

Optional shortcut:

```text
/create-prompt Create a prompt named check-spec-coverage that reads a spec in docs/specs, compares it with implementation files, and returns a Markdown coverage table without editing files.
```

#### Why this is not the final abstraction

This validation workflow is starting to look like more than a prompt:

- It has a repeated report format.
- It may need reusable resources.
- It may later bundle templates or examples.
- It is a capability, not just a saved sentence.

That is when skills become the better abstraction.

We will now promote it.

---

## Phase 3: Promote Validation From Prompt To Skill

### Prompt vs skill: the actual difference

Use a prompt when the reusable unit is mainly a reusable instruction.

Use a skill when the reusable unit is a capability with structure, bundled resources, and a richer workflow.

That is exactly what spec validation is becoming.

### Step 5: Create The `spec-validation` Skill

What this primitive is:

A skill is a folder with a `SKILL.md` file, and optionally scripts, examples, templates, or other resources.

Why we use it here:

Spec validation is a repeatable, multi-step, portable capability.

This is a better fit for a skill than for a plain prompt because the workflow now has:

- a named capability,
- a stable report format,
- room for bundled supporting files,
- and the possibility of automatic discovery based on its description.

#### Important naming rule

The skill folder name and the `name` field must match exactly.

So if the folder is `.github/skills/spec-validation/`, the frontmatter must use:

```yaml
name: spec-validation
```

Invalid names or mismatches can make a skill silently fail to load.

Manual path:

Create `.github/skills/spec-validation/SKILL.md`:

```markdown
---
name: spec-validation
description: Validate that implementation code covers requirement IDs from a markdown specification and report coverage gaps. Use when reviewing backend files created from specs in docs/specs.
argument-hint: "<spec-file-name>.md [optional file paths]"
user-invocable: true
---

# Spec validation

Use this skill when you need to compare implementation code against a spec file in `docs/specs/`.

Inputs:
- A spec file name supplied by the user, for example `task-creation.md`.
- The relevant implementation files, either mentioned explicitly or already open in the editor.

Procedure:
1. Read the spec file from [docs/specs](../../../docs/specs).
2. Extract every requirement ID, business rule, and edge case.
3. Read the implementation files the user mentioned or currently has open.
4. Check whether each requirement is implemented explicitly, partially, or not at all.
5. Look for JSDoc tags in the form `@spec REQ-...`.
6. Produce the final report using [report-template.md](./report-template.md).
7. If coverage is unclear, say `Unclear` instead of guessing.

Rules:
- Never claim a requirement is implemented unless you can point to concrete code evidence.
- If the code appears correct but lacks `@spec` tags, report that as a traceability gap.
- If the spec itself is ambiguous, report that separately from implementation gaps.
```

Create `.github/skills/spec-validation/report-template.md`:

```markdown
# Validation Report

## Summary
- Spec file:
- Files reviewed:
- Overall status:

## Requirement Coverage
| Requirement ID | Covered (Yes/No/Unclear) | Evidence | Notes |
| --- | --- | --- | --- |

## Traceability Gaps
- List missing or inconsistent `@spec` tags here.

## Spec Ambiguities
- List unclear requirements or open questions here.

## Recommended Next Actions
- Add concise, action-oriented next steps here.
```

Optional shortcut:

```text
/create-skill Create a skill named spec-validation that reads a spec in docs/specs, compares it with implementation files, checks for @spec JSDoc tags, and outputs a Markdown coverage report.
```

#### What to do with the older validation prompt

In a real repository, you would usually remove `check-spec-coverage.prompt.md` once the skill becomes your canonical validation path.

We keep it in this tutorial briefly so the student can compare the two abstractions.

Verify it works:

Type `/` in chat. `spec-validation` should appear in the slash-command list.

If it does not:

- Check that the folder is `.github/skills/spec-validation/`.
- Check that the file is named `SKILL.md`.
- Check that the frontmatter `name` is exactly `spec-validation`.
- Make sure the name uses only lowercase letters, numbers, and hyphens.

What should exist now:

```text
.github/
   copilot-instructions.md
   instructions/
      spec-authoring.instructions.md
      backend-sdd.instructions.md
      backend-tests.instructions.md
   prompts/
      implement-spec.prompt.md
      check-spec-coverage.prompt.md
   skills/
      spec-validation/
         SKILL.md
         report-template.md
```

---

## Phase 4: Introduce Role Separation With Custom Agents

So far, our workflow is reusable, but it still assumes one agent can comfortably switch between analysis, implementation, and coordination.

That is often where teams benefit from custom agents.

### Why use agents here?

Because role separation is useful when:

- one role should be read-only,
- another role should be allowed to edit,
- and a coordinator should organize the workflow without being the one that writes code.

This is also where multi-agent design starts to become meaningful instead of decorative.

### Why not one "super agent"?

You could build one.

But then:

- tool restrictions become weaker,
- intent becomes blurrier,
- and it becomes harder to explain why a given step happened.

For a pedagogical SDD system, specialized agents are more instructive.

### Step 6: Create The Read-Only `SpecReader` Agent

What this primitive is:

A custom agent defines a role, its instructions, and its available tools.

Why this agent exists:

We want a role that is excellent at reading specs, extracting requirements, and identifying ambiguity, without ever jumping straight into code generation.

Manual path:

Create `.github/agents/spec-reader.agent.md`:

```markdown
---
name: SpecReader
description: Read-only agent for analyzing markdown specifications in docs/specs. Use when a parent agent needs a precise summary of requirement IDs, ambiguities, edge cases, and implementation checkpoints before coding starts.
tools: ["read", "search"]
user-invocable: false
---

You are a requirements analyst for a specs-driven backend project.

Your job is to read specification files in `docs/specs/` and return:
1. A short feature summary.
2. A numbered list of requirement IDs and what each requires.
3. Ambiguities or missing decisions that block safe implementation.
4. Edge cases that must be tested.
5. A suggested implementation checklist.

Rules:
- Never generate production code.
- Quote the exact requirement IDs you found.
- If a requirement is ambiguous, state that clearly instead of guessing.
- Prefer precise summaries over broad architectural speculation.
```

#### Why `user-invocable: false`?

We are hiding worker agents from the normal picker to keep the student's UI focused. The coordinator agent will use them, and they can still be selected manually if you later decide to expose them.

---

### Step 7: Create The Editing `Implementer` Agent

Why this agent exists:

Once the spec is clear, we want a role that is allowed to edit code but is still disciplined by the instructions and requirement IDs.

Manual path:

Create `.github/agents/implementer.agent.md`:

```markdown
---
name: Implementer
description: Editing agent that implements approved backend requirements from docs/specs in TypeScript and preserves requirement traceability with @spec tags.
tools: ["read", "search", "edit"]
user-invocable: false
---

You implement code from an approved specification summary.

Workflow:
1. Re-read the relevant spec and nearby code patterns.
2. Implement only the approved requirements.
3. Add JSDoc blocks with `@spec <requirement-id>` where code directly fulfills a requirement.
4. If the spec is ambiguous, stop and ask clarifying questions instead of guessing.
5. Prefer minimal changes and reuse existing patterns.
6. Summarize created or modified files and map them back to requirement IDs.
```

#### Why this is an agent, not a prompt

Because the reusable part is now a role with a constrained tool set, not just a repeated sentence.

---

### Step 8: Create The Coordinating `LeadArchitect` Agent

Why this agent exists:

We want one visible entry point for the workflow:

1. analyze the spec,
2. stop if there is ambiguity,
3. implement only after the requirements are clear,
4. summarize the result.

#### Important realism note

Subagent orchestration is real, but it is still model-driven. That means the exact moment the coordinator delegates to `SpecReader` or `Implementer` is not a hard guarantee.

That is why we also add handoffs as a visible fallback.

Manual path:

Create `.github/agents/lead-architect.agent.md`:

```markdown
---
name: LeadArchitect
description: Coordinator agent for specs-driven feature work. Use when you want a spec analyzed first, then implemented through specialized worker agents.
tools: ["agent", "read", "search"]
agents: ["SpecReader", "Implementer"]
handoffs:
   - label: Review the spec first
      agent: SpecReader
      prompt: Analyze the target specification and return requirements, ambiguities, edge cases, and an implementation checklist.
   - label: Start implementation
      agent: Implementer
      prompt: Implement the approved specification summary. Follow all workspace instructions and include @spec tags.
---

You coordinate feature delivery for a specs-driven backend project.

Preferred workflow:
1. Use the `SpecReader` subagent to analyze the requested spec.
2. If ambiguities remain, stop and ask clarifying questions.
3. If the requirements are clear, use the `Implementer` subagent to write the code.
4. Return a concise summary of what was implemented and how it maps to the spec.

Important rules:
- Prefer delegation to the worker agents.
- Do not invent requirements that are not in the spec.
- If subagent delegation does not occur automatically, tell the user which handoff to use next.
- Do not write code directly unless the user explicitly asks you to bypass the orchestration workflow.
```

#### About the `agents` field

The `agents` list narrows which subagents the coordinator should use. In current docs this is supported, but the precise delegation behavior can still vary by model and version.

If your version seems to ignore this field, the design still holds. You simply lose some restriction, not the whole workflow.

#### Why we do not pin a model here

The docs allow model preferences in custom agents. We are not using that feature in this tutorial because model pinning adds cost, availability, and version variability that is not essential to the core lesson.

---

### Step 9: Upgrade `implement-spec` So It Uses The Coordinator

Now that the agent system exists, the original prompt can become cleaner.

This is a good example of evolving a customization as the architecture matures.

Replace `.github/prompts/implement-spec.prompt.md` with this second version:

```markdown
---
name: implement-spec
description: Implement a feature from a markdown specification in docs/specs using the LeadArchitect workflow.
argument-hint: "<spec-file-name>.md"
agent: LeadArchitect
---

Use [docs/specs](../../docs/specs) as the source of truth.

The target specification file is `${input:specFile:task-creation.md}`.

Use the LeadArchitect workflow:
- analyze the spec first,
- stop on ambiguities,
- implement only approved requirements,
- map code back to requirement IDs,
- summarize the result in terms of the spec rather than only file names.

If autonomous subagent delegation does not occur, use the handoffs or tell the user which specialized agent to run next.
```

#### Why this second version is better

Version 1 made the prompt responsible for both workflow and execution details.

Version 2 lets the prompt do what prompts are good at: starting a repeatable task.

The deeper workflow logic now lives in the agent system, where it belongs.

#### Verify the agent layer works

At this stage, success means:

- `LeadArchitect` appears in the agent picker.
- The prompt `/implement-spec` appears in the slash-command menu.
- The worker agents may be hidden from the picker because `user-invocable: false`, which is expected.

If you later want the student to run worker agents manually, set `user-invocable: true` temporarily.

What should exist now:

```text
.github/
   copilot-instructions.md
   instructions/
      spec-authoring.instructions.md
      backend-sdd.instructions.md
      backend-tests.instructions.md
   prompts/
      implement-spec.prompt.md
      check-spec-coverage.prompt.md
   skills/
      spec-validation/
         SKILL.md
         report-template.md
   agents/
      spec-reader.agent.md
      implementer.agent.md
      lead-architect.agent.md
```

---

## Phase 5: Add Deterministic Enforcement With Hooks

This is where we must be precise.

### A tempting but wrong idea

Many people think, "I want a pre-commit AI hook."

That instinct is understandable, but it mixes two different systems:

- Git hooks are Git lifecycle hooks.
- Copilot hooks are agent lifecycle hooks.

In current VS Code Copilot customization, hooks are configured as JSON files in `.github/hooks/*.json`, and they run at events like `PreToolUse`, `PostToolUse`, `SessionStart`, and `Stop`.

So instead of pretending Copilot has a markdown-based `pre-commit` hook, we will use a supported workspace hook that runs after successful tool use and checks whether edited TypeScript files contain `@spec` traceability tags.

### Why a hook is correct here

Instructions can tell the model to add `@spec` tags.

But instructions are guidance. A hook is deterministic automation.

That is exactly when hooks are the right primitive.

### Preview warning

Hooks are currently a Preview feature. Use them carefully, review them like code, and do not enable hook automation from untrusted repositories.

### Step 10: Create A Workspace Hook

Create `.github/hooks/sdd-post-edit.json`:

```json
{
   "hooks": {
      "PostToolUse": [
         {
            "type": "command",
            "command": "node ./scripts/check-spec-tags.mjs",
            "timeout": 15
         }
      ]
   }
}
```

#### Why `PostToolUse`?

Because it is a supported hook event, and it lets us validate recently edited files immediately after a successful tool call.

That gives faster feedback than waiting until a human notices the omission later.

### Step 11: Create The Hook Script

Create `scripts/check-spec-tags.mjs`:

```js
import fs from 'node:fs';
import path from 'node:path';

function collectStrings(value, results = []) {
   if (typeof value === 'string') {
      results.push(value);
      return results;
   }

   if (Array.isArray(value)) {
      for (const item of value) {
         collectStrings(item, results);
      }
      return results;
   }

   if (value && typeof value === 'object') {
      for (const nested of Object.values(value)) {
         collectStrings(nested, results);
      }
   }

   return results;
}

function isRelevantTypeScriptPath(filePath) {
   return (
      typeof filePath === 'string' &&
      filePath.endsWith('.ts') &&
      !filePath.endsWith('.d.ts') &&
      filePath.includes(`${path.sep}src${path.sep}`)
   );
}

const stdin = await new Promise((resolve) => {
   let data = '';
   process.stdin.setEncoding('utf8');
   process.stdin.on('data', (chunk) => {
      data += chunk;
   });
   process.stdin.on('end', () => resolve(data));
});

const payload = stdin ? JSON.parse(stdin) : {};
const cwd = payload.cwd || process.cwd();
const candidateStrings = [...new Set(collectStrings(payload.tool_input))];

const filesToCheck = candidateStrings
   .map((candidate) => path.resolve(cwd, candidate))
   .filter((candidate) => isRelevantTypeScriptPath(candidate) && fs.existsSync(candidate));

if (filesToCheck.length === 0) {
   process.stdout.write('{}');
   process.exit(0);
}

const missingTags = filesToCheck.filter((filePath) => {
   const content = fs.readFileSync(filePath, 'utf8');
   return !/@spec\s+REQ-[A-Z0-9-]+/.test(content);
});

if (missingTags.length === 0) {
   process.stdout.write(
      JSON.stringify({
         hookSpecificOutput: {
            hookEventName: 'PostToolUse',
            additionalContext: 'SDD traceability check passed for edited TypeScript files.'
         }
      })
   );
   process.exit(0);
}

process.stdout.write(
   JSON.stringify({
      hookSpecificOutput: {
         hookEventName: 'PostToolUse',
         additionalContext:
            `SDD traceability warning: the following edited TypeScript files do not contain any @spec tags yet: ${missingTags.join(', ')}. ` +
            'If these files implement requirements directly, add JSDoc blocks with the corresponding requirement IDs.'
      }
   })
);
```

### Why this hook only warns

This version injects a warning back into the agent conversation rather than hard-stopping the workflow.

That is a better teaching default because it gives feedback without creating frustration during early experimentation.

If your team wants strict enforcement, you can return a blocking decision instead. For example:

```json
{
   "decision": "block",
   "reason": "Edited TypeScript files are missing @spec tags",
   "hookSpecificOutput": {
      "hookEventName": "PostToolUse",
      "additionalContext": "Add traceability tags before continuing."
   }
}
```

### Important distinction: workspace hooks vs agent-scoped hooks

This tutorial uses a workspace hook in `.github/hooks/*.json`.

That does not require agent-scoped hook configuration.

If you later move hook definitions into a custom agent's frontmatter, that is a different feature and may require enabling:

```json
{
   "chat.useCustomAgentHooks": true
}
```

That setting is relevant to agent-scoped hooks, not to ordinary workspace hook files.

### Verify the hook works

Check that:

- the hook file is JSON,
- it lives in `.github/hooks/`,
- the script path is correct,
- and the Output panel shows the `GitHub Copilot Chat Hooks` channel when the hook runs.

What should exist now:

```text
.github/
   copilot-instructions.md
   instructions/
      spec-authoring.instructions.md
      backend-sdd.instructions.md
      backend-tests.instructions.md
   prompts/
      implement-spec.prompt.md
      check-spec-coverage.prompt.md
   skills/
      spec-validation/
         SKILL.md
         report-template.md
   agents/
      spec-reader.agent.md
      implementer.agent.md
      lead-architect.agent.md
   hooks/
      sdd-post-edit.json
scripts/
   check-spec-tags.mjs
```

---

## Phase 6: Run The Simulation As A Learning Exercise

Now we finally use the system.

This section is intentionally designed to teach a subtle but critical lesson:

The first correct result is not always "code generated." Sometimes the first correct result is "the AI refused to generate code because the spec was ambiguous."

That is not failure. That is the system working.

### Step 12: Write An Intentionally Incomplete Spec

Create `docs/specs/task-creation.md` with this first version:

```markdown
# Task Creation Specification

## Goal
Create tasks through a REST endpoint.

## Requirements
- **REQ-TC-01**: The system must expose a `POST /tasks` endpoint.
- **REQ-TC-02**: The request payload must accept `title` as a string with a maximum length of 50 characters.
- **REQ-TC-03**: If `status` is omitted, the system must default it to `To-Do`.
- **REQ-TC-04**: If `title` exceeds 50 characters, the API must return HTTP 400 with the message `Title too long`.

## Edge cases
- Missing `title`.
- Empty string `title`.
- A provided `status` outside the allowed set.

## Open questions
- Which statuses are allowed beyond `To-Do`?
- Does the API trim whitespace before validating title length?
```

### Why this first version is intentionally incomplete

Because we want to see whether the system follows the rule "ask clarifying questions instead of guessing."

That is one of the core promises of SDD customizations.

### Step 13: Run The Implementation Workflow

Run:

```text
/implement-spec task-creation.md
```

### What a correct outcome looks like here

The best result at this moment is not immediate code generation.

The best result is something like:

- the agent summarizes the known requirements,
- it identifies the unresolved status set and trimming behavior,
- and it asks clarifying questions before generating production code.

If that happens, your customization stack is already doing useful engineering work.

### What you may observe in the UI

If subagent delegation occurs, you may see collapsible subagent tool calls for the `SpecReader` and possibly the `Implementer`.

If delegation does not occur automatically, that is not proof the design is wrong. It means the model chose not to fork the task in that turn.

### Fallback path if orchestration does not happen automatically

Use the worker roles directly.

First, ask for analysis:

```text
Analyze docs/specs/task-creation.md and return: summary, requirement IDs, ambiguities, edge cases, and an implementation checklist.
```

Then, after ambiguity is resolved, ask for implementation:

```text
Implement the approved summary for docs/specs/task-creation.md. Add @spec tags for every code element that directly fulfills a requirement.
```

This is why handoffs and manual agent switching remain valuable even in a multi-agent design.

---

### Step 14: Refine The Spec After The Clarifying Questions

Replace `docs/specs/task-creation.md` with this resolved version:

```markdown
# Task Creation Specification

## Goal
Create tasks through a REST endpoint.

## Allowed statuses
- `To-Do`
- `In Progress`
- `Done`

## Requirements
- **REQ-TC-01**: The system must expose a `POST /tasks` endpoint.
- **REQ-TC-02**: The request payload must include `title` as a string with a maximum length of 50 characters after trimming leading and trailing whitespace.
- **REQ-TC-03**: If `status` is omitted, the system must default it to `To-Do`.
- **REQ-TC-04**: If `status` is provided, it must be one of `To-Do`, `In Progress`, or `Done`.
- **REQ-TC-05**: If `title` exceeds 50 characters after trimming, the API must return HTTP 400 with the message `Title too long`.
- **REQ-TC-06**: If `title` is missing or empty after trimming, the API must return HTTP 400 with the message `Title is required`.

## Edge cases
- `title` missing entirely.
- `title` present but only whitespace.
- `status` provided with an unsupported value.
```

### Why this rewrite matters

We are not patching behavior through prompts. We are improving the specification itself.

That is a core SDD discipline: when the rules are unclear, improve the spec, not the improvisation.

### Step 15: Run The Implementation Workflow Again

Run the same prompt again:

```text
/implement-spec task-creation.md
```

### What to expect now

Now the system has enough information to proceed.

A realistic good outcome is:

- the agent summarizes the requirement IDs,
- it creates or edits backend files under `src/`,
- it includes JSDoc `@spec` tags where appropriate,
- it explains which requirement IDs are covered,
- and the hook warns if new TypeScript files are missing traceability tags.

Do not hard-code expectations such as exact file names. The exact output may differ by version, prompt phrasing, and existing project structure.

Examples of likely files later in the project are:

- `src/routes/tasks.ts`
- `src/controllers/task-controller.ts`
- `src/validators/create-task.ts`
- `tests/task-creation.test.ts`

Treat those as illustrative, not guaranteed.

---

### Step 16: Validate The Result With The Skill

Run:

```text
/spec-validation task-creation.md
```

Or, if you want to be explicit about files under review:

```text
/spec-validation task-creation.md src/routes/tasks.ts src/controllers/task-controller.ts src/validators/create-task.ts
```

### What a correct validation result looks like

The skill should produce a structured report that includes:

- the spec file reviewed,
- the implementation files reviewed,
- a row for each requirement ID,
- coverage status,
- evidence,
- notes about missing `@spec` tags,
- and separate notes for spec ambiguity if any remains.

### Step 17: Observe The Hook Feedback

If the generated or edited `src/**/*.ts` files do not contain any `@spec` tags, the hook should inject a warning after the edit operation.

That is the intended behavior.

It is not replacing the skill. It is complementing it:

- the skill gives a structured review,
- the hook gives immediate feedback,
- the instructions shape default behavior,
- and the agents organize responsibility.

That layering is the core lesson of this tutorial.

---

## What You Built Conceptually

This is the layering we now have:

1. Instructions define standing rules.
2. Prompt files standardize user entry points.
3. Skills package reusable capabilities.
4. Custom agents define role boundaries and tool access.
5. Hooks enforce deterministic post-edit checks.

That is not redundancy. It is separation of concerns.

---

## Troubleshooting Guide

Use this table when something is not behaving as expected.

| Symptom | Likely cause | What to check |
| --- | --- | --- |
| `/implement-spec` does not appear in slash commands | The prompt file is in the wrong place or has the wrong extension | Confirm it is in `.github/prompts/` and ends with `.prompt.md` |
| The prompt appears but seems to ignore repository rules | Instructions are not loading or are too vague | Check the References section and Chat diagnostics |
| File-based instructions do not apply | `applyTo` does not match the current file path, or the task context is too indirect | Re-check `applyTo`, file location, and description wording |
| `spec-validation` does not appear | The skill folder/name contract is broken | Confirm folder is `.github/skills/spec-validation/`, file is `SKILL.md`, and frontmatter name is `spec-validation` |
| The skill appears but ignores the template | The template file is not referenced in `SKILL.md` | Make sure `SKILL.md` links to `./report-template.md` |
| `LeadArchitect` appears but does not delegate | Subagent use is model-driven, or descriptions are not strong enough | Improve agent descriptions, keep `agent` in the tool list, and use handoffs or manual switching when needed |
| Worker agents do not appear in the picker | `user-invocable: false` is set | This is expected; change it to `true` only if you want them directly selectable |
| The hook does not run | Wrong file location or wrong file format | Confirm the hook is a `.json` file inside `.github/hooks/` |
| The hook runs but does nothing | The edited files are not under `src/`, or the tool input shape did not yield paths | Log the incoming JSON, inspect the Output panel, and refine the script |
| The hook script fails | Node is unavailable, path is wrong, or the script has a JSON parse error | Run `node ./scripts/check-spec-tags.mjs` manually with test input |
| `/create-*` commands are missing | Command availability differs by product version or mode | Use manual file creation or the Agent Customizations editor |
| Customizations in a monorepo are not found | The repo root is outside the opened workspace folder | Consider `chat.useCustomizationsInParentRepositories` |
| Agent-scoped hooks do not work | You are mixing workspace hooks and agent-scoped hooks | Remember that workspace hooks do not require `chat.useCustomAgentHooks`, but agent-frontmatter hooks do |

### Useful diagnostics actions

When something feels wrong, inspect the system instead of guessing.

Use:

- Chat References in each response
- Chat diagnostics
- the Output panel, especially `GitHub Copilot Chat Hooks`
- `Chat: Run Prompt`
- `Chat: Open Customizations`

---

## Best Practices Recap

These are the design rules this tutorial is following, and they are worth carrying into real teams.

### 1. Start with instructions

Instructions are the cheapest, highest-leverage way to encode project standards.

### 2. Keep instructions concise and targeted

Use `applyTo` aggressively. Do not make everything always-on.

### 3. Use prompts for entry points, not for everything

A prompt is a reusable doorway, not a substitute architecture.

### 4. Promote capabilities into skills when structure emerges

If the workflow wants a template, examples, scripts, or a stable report shape, it is probably a skill.

### 5. Use custom agents when role separation matters

Role separation is especially useful when some tasks should be read-only and others should be allowed to edit.

### 6. Treat subagent orchestration as helpful, not magical

The mechanism exists. The exact delegation is still model-driven. Always design a manual fallback.

### 7. Use hooks only for deterministic needs

Hooks should enforce or automate. They should not duplicate advisory instructions.

### 8. Verify every layer

Do not assume a customization is active just because the file exists.

Check References, diagnostics, slash-command discovery, and hook output.

### 9. Improve the spec before compensating in prompts

If the system asks good clarifying questions, reward that by fixing the spec instead of teaching the AI to guess better.

### 10. Keep the stack realistic

A good tutorial is not the one with the most features. It is the one whose features belong together.

---

## Stable Vs Preview In This Tutorial

Use this table to explain to students what is safe to adopt broadly and what should be introduced more carefully.

| Surface | Status | Notes |
| --- | --- | --- |
| `.github/copilot-instructions.md` | Stable | Safe baseline for repository-wide standards |
| `.instructions.md` with `applyTo` | Stable | Preferred way to target rules by file type or folder |
| `.prompt.md` files | Stable | Good for reusable slash commands |
| Skills in `.github/skills/` | Stable | Good for reusable capabilities and bundled resources |
| Custom agents in `.github/agents/` | Stable | Good for role separation and tool restrictions |
| Handoffs in custom agents | Stable | Very useful for predictable, user-visible transitions |
| Autonomous subagent selection | Model-driven | Real, but not deterministic |
| Restricting allowed subagents with `agents:` | Version-sensitive | Supported in current docs, but exact behavior may vary |
| Workspace hooks in `.github/hooks/*.json` | Preview | Powerful, but should be reviewed carefully |
| Agent-scoped hooks in agent frontmatter | Preview | Separate feature; may require `chat.useCustomAgentHooks` |
| Agent Customizations editor | Preview | Useful UI, but not required for this tutorial |

---

## Final Suggested Learning Sequence For The Student

If you are teaching from this tutorial live or asynchronously, use this order:

1. Create the instruction files.
2. Confirm that always-on guidance works.
3. Create the first prompt.
4. Create the temporary validation prompt.
5. Promote validation to a skill and explain why.
6. Add the three custom agents.
7. Upgrade the implementation prompt to use `LeadArchitect`.
8. Add the workspace hook.
9. Create the intentionally incomplete spec.
10. Let the system ask clarifying questions.
11. Rewrite the spec.
12. Run implementation.
13. Run validation.
14. Inspect hook feedback.

That sequence teaches not only mechanics, but engineering judgment.

---

## Closing Thought

The point of multi-agent SDD is not to make Copilot look impressive.

The point is to make generated work more reviewable, more traceable, and harder to misalign with the actual requirements.

If the student finishes this tutorial understanding why each primitive exists, what problem it solves, and when to stop adding more automation, then the tutorial succeeded.
