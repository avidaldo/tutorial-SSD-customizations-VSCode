# Tutorial 2: Reusable Workflows With Skills And Custom Agents

Part 1 gave Copilot rules and one reusable entry point.

Part 2 adds the next level of structure:

- skills for reusable capabilities,
- custom agents for role boundaries,
- and a cleaner orchestration model for specs-driven work.

---

## Learning Goals

Choose one of these paths:

1. Continue from Part 1 with these files already present:

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

1. Or create those files now by following [tutorial-1-foundations.md](./tutorial-1-foundations.md).

At the start of Part 2, there are still no hooks and no MCP configuration.

---

## Why This Part Exists

After Part 1, the system can already behave intelligently, but the workflow is still too flat.

Two design pressures now appear:

1. validation is becoming more than a single prompt,
1. one agent doing analysis, implementation, and coordination is too much responsibility in one place.

That is what this tutorial resolves.

---

## Step 1: Deliberately Start With A Validation Prompt

This step is pedagogical on purpose.

We are going to create something that is plausible, but not our final choice, so the student can see why the abstraction changes.

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

### Why this prompt is only temporary

This looks reasonable, but it is already trying to be more than a simple saved command:

- it has a repeated report shape,
- it may need reusable support files,
- and it represents a capability, not just an invocation pattern.

That is the signal that it should become a skill.

---

## Step 2: Promote Validation Into A Skill

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

### Why this is a better abstraction now

The reusable unit is no longer just “say these words to the model.”

It is now:

- a named reusable capability,
- with a report structure,
- and a support resource.

That is what skills are for.

### Important naming rule

The skill directory name and the frontmatter `name` must match exactly:

```yaml
name: spec-validation
```

If they do not match, the skill may silently fail to load.

---

## Step 3: Create The Worker Agents

### `SpecReader`

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

### `Implementer`

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

### Why worker agents are hidden by default

We are using `user-invocable: false` so the normal picker stays focused on the coordinator-facing workflow. You can expose these agents later if you want direct manual control.

---

## Step 4: Create The Coordinator Agent

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

### Why this is realistic

Subagent orchestration is real, but still model-driven.

That means:

- the coordinator can delegate,
- but the exact timing and behavior are not guaranteed,
- so handoffs remain important as a user-visible fallback.

That is the right mental model to teach.

---

## Step 5: Upgrade The `implement-spec` Prompt

Replace `.github/prompts/implement-spec.prompt.md` with this version:

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

### Why this upgrade matters

In Part 1, the prompt carried too much process logic.

Now the prompt does what prompts are good at: starting a repeated workflow.

The deeper orchestration lives in the agent layer, where it belongs.

---

## Step 6: Verify Part 2

### Check 1: the skill loads

Type `/` in chat.

Expected outcome:

- `spec-validation` appears in the slash-command list.

If it does not:

- check the folder is `.github/skills/spec-validation/`,
- check the file is named `SKILL.md`,
- check the frontmatter name is exactly `spec-validation`.

### Check 2: the coordinator agent appears

Open the agent picker.

Expected outcome:

- `LeadArchitect` appears in the picker,
- worker agents may be hidden because `user-invocable: false`, which is expected.

### Check 3: the orchestration entry point is updated

Type `/` in chat.

Expected outcome:

- `implement-spec` still appears,
- but now it routes through `LeadArchitect`.

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
    check-spec-coverage.prompt.md
  skills/
    spec-validation/
      SKILL.md
      report-template.md
  agents/
    spec-reader.agent.md
    implementer.agent.md
    lead-architect.agent.md
docs/
  specs/
    task-creation.md
```

---

## What You Should Understand Now

By the end of Part 2, the student should be able to explain:

- why the validation workflow started as a prompt but deserved to become a skill,
- why custom agents are about responsibilities and tool boundaries,
- and why multi-agent orchestration should always have a fallback path.

---

## What Part 3 Adds

Part 3 adds deterministic local enforcement and the first complete simulation of the workflow:

- workspace hooks,
- a traceability check,
- an ambiguous spec first,
- then a refined spec,
- and then implementation plus validation.

Continue with [tutorial-3-hooks-and-local-workflow.md](./tutorial-3-hooks-and-local-workflow.md).
