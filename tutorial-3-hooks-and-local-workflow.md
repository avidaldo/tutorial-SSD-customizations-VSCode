# Tutorial 3: Hooks And The Full Local Workflow

Part 1 taught standing rules. Part 2 taught reusable capabilities and role separation.

Part 3 adds the first deterministic automation layer and the first full local simulation of the workflow.

This is where the student sees the difference between:

- advisory mechanisms such as instructions, prompts, skills, and agents,
- and deterministic automation such as hooks.

---

## Learning Goals

By the end of this tutorial, the student should understand:

- why hooks are different from instructions,
- how to add a small, realistic workspace hook,
- how the system should behave when a spec is ambiguous,
- and how to validate the result after implementation.

---

## Starting Point

Start from one of these states:

1. The end of [tutorial-2-skills-and-agents.md](./tutorial-2-skills-and-agents.md).
2. A recreated baseline with:

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

At the start of Part 3, there is still no `.github/hooks/` directory and no `scripts/check-spec-tags.mjs` file.

---

## Why Hooks Appear Only Now

Hooks are more powerful than instructions because they execute commands at agent lifecycle events.

That also makes them more dangerous and more complex.

If you teach hooks before students understand the advisory layers, they tend to treat hooks as just another way to write rules. That is incorrect.

Use this distinction throughout the tutorial:

- instructions guide,
- prompts package entry points,
- skills package capabilities,
- agents package roles,
- hooks execute code.

---

## Preview Warning

Workspace hooks are currently a Preview feature.

They are still worth teaching, but they should be introduced with care:

- review them like code,
- do not enable hooks from untrusted repositories,
- and keep the example small and local.

---

## Step 1: Create A Workspace Hook

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

### Why `PostToolUse`

This hook event runs after a tool has completed successfully.

That makes it a good place to validate the result of file edits without pretending that hooks are Git pre-commit hooks. It is immediate enough to be useful and simple enough to explain.

---

## Step 2: Create The Hook Script

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

### Why this hook warns instead of blocking

This tutorial chooses a warning-first hook so the student sees deterministic automation without immediately running into frustrating hard stops.

If your team wants stricter enforcement later, you can return a blocking decision instead.

Example of a stricter response:

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

---

## Step 3: Clarify The Difference Between Workspace Hooks And Agent-Scoped Hooks

This tutorial uses a workspace hook in `.github/hooks/*.json`.

That does not require custom-agent hook configuration.

If you later move hooks into a custom agent's frontmatter, that is a different feature and may require:

```json
{
  "chat.useCustomAgentHooks": true
}
```

That setting is relevant to agent-scoped hooks, not to normal workspace hook files.

---

## Step 4: Write An Intentionally Incomplete Spec

Replace `docs/specs/task-creation.md` with this version:

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

### Why the spec is incomplete on purpose

This is the first truly important simulation in the series.

The correct first outcome is not necessarily code. The correct first outcome may be a request for clarification.

That is a feature, not a failure.

---

## Step 5: Run The Local Workflow

Run:

```text
/implement-spec task-creation.md
```

### What good behavior looks like

The agent should do some combination of these things:

- summarize the requirement IDs,
- identify the unresolved status set and trimming behavior,
- and ask clarifying questions before generating implementation code.

If subagent delegation occurs, you may see collapsible subagent tool calls for `SpecReader` and possibly `Implementer`.

If delegation does not occur automatically, that does not invalidate the design. It means the model chose not to fork the task in that turn.

### Manual fallback path

If the orchestration does not split automatically, use the roles directly:

```text
Analyze docs/specs/task-creation.md and return: summary, requirement IDs, ambiguities, edge cases, and an implementation checklist.
```

Then, after ambiguity is resolved:

```text
Implement the approved summary for docs/specs/task-creation.md. Add @spec tags for every code element that directly fulfills a requirement.
```

That fallback is part of the intended architecture.

---

## Step 6: Refine The Spec And Rerun

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

Run again:

```text
/implement-spec task-creation.md
```

Now a realistic good outcome is:

- the agent summarizes the requirement IDs,
- it creates or edits backend files under `src/`,
- it includes JSDoc `@spec` tags where appropriate,
- it explains which requirement IDs are covered,
- and the hook warns if new TypeScript files are missing traceability tags.

Do not hard-code exact output file names. Treat any file names as examples rather than guarantees.

---

## Step 7: Validate The Result

Run:

```text
/spec-validation task-creation.md
```

Or, if you want to be explicit about file scope:

```text
/spec-validation task-creation.md src/routes/tasks.ts src/controllers/task-controller.ts src/validators/create-task.ts
```

What a correct result looks like:

- the report lists the spec file reviewed,
- the report lists the implementation files reviewed,
- each requirement ID gets its own row,
- and missing `@spec` tags are reported as traceability gaps rather than silent omissions.

---

## Step 8: Verify Hook Behavior

Use these checks:

### Check 1: the hook file is discovered

Confirm:

- the hook file is JSON,
- it lives in `.github/hooks/`,
- and the script path is correct.

### Check 2: the output channel shows hook activity

Open the Output panel and look for the `GitHub Copilot Chat Hooks` channel.

### Check 3: warnings appear when traceability is missing

If an edited `src/**/*.ts` file has no `@spec` tags, the hook should inject a warning after the edit tool completes.

That is the intended teaching behavior.

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
  hooks/
    sdd-post-edit.json
scripts/
  check-spec-tags.mjs
docs/
  specs/
    task-creation.md
```

---

## What You Should Understand Now

By the end of Part 3, the student should understand:

- why hooks are deterministic while instructions are advisory,
- why ambiguity should be fixed in the spec rather than patched with prompt cleverness,
- and how the full local workflow behaves from analysis through implementation and validation.

---

## What Part 4 Adds

Part 4 adds one external integration layer: MCP.

The local workflow will stay the same, but the student will learn how to connect that workflow to external tools and data through an installed or configured MCP server.

Continue with [tutorial-4-mcp-integration.md](./tutorial-4-mcp-integration.md).
