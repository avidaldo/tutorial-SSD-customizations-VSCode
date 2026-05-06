# Tutorial 4: Extend The Workflow With MCP

Part 3 gave the student a complete local workflow.

Part 4 adds one external integration layer through MCP: Model Context Protocol.

This tutorial deliberately teaches MCP at the user level. The student will install or configure an MCP server in VS Code and use it from chat. This is not a tutorial about writing an MCP server, building a VS Code extension, or packaging an agent plugin.

---

## Learning Goals

By the end of this tutorial, the student should understand:

- what MCP adds that local customizations do not,
- when MCP belongs in the architecture,
- how to configure an MCP server in VS Code,
- how MCP tools become available in chat,
- and how to expose those tools to an existing custom agent.

---

## Starting Point

Start from one of these states:

1. Continue from the end of [tutorial-3-hooks-and-local-workflow.md](./tutorial-3-hooks-and-local-workflow.md).
2. Recreate the local customization stack from Parts 1 through 3 first.

At the beginning of Part 4, the local workflow already exists. MCP is being added as an external layer, not as a replacement.

---

## Why MCP Comes Last

MCP is not another way to write instructions or prompts.

MCP is the mechanism that lets the AI reach outside the local repository and use external tools, data, prompts, and resources.

That only makes pedagogical sense after the student already understands:

- how the local workflow behaves,
- where the local rules live,
- and what problem cannot be solved cleanly with local files alone.

Use this rule of thumb:

- if the AI only needs local code and local policies, use local customizations,
- if the AI needs external tools or data, consider MCP.

---

## Main Path: Use A Managed MCP Server In VS Code

For this tutorial, the main path uses a workspace-level MCP configuration in `.vscode/mcp.json`.

The project-relevant example is GitHub MCP, because it fits a specs-driven workflow well when teams store requirements, issue discussions, or PR review context in GitHub.

### Step 1: Create The MCP Configuration File

Create `.vscode/mcp.json`:

```json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp"
    }
  }
}
```

### Why this is a good teaching example

GitHub MCP is not tied to a UI demo. It is tied to repository context, which is relevant to specs-driven development:

- issue descriptions,
- review context,
- tracked work,
- and other repository-level collaboration data.

### Important trust note

When you add or change an MCP server, VS Code asks you to trust that server before starting it.

Treat that prompt seriously. MCP servers can add tools, prompts, resources, and other capabilities to chat.

---

## Step 2: Start And Inspect The MCP Server

Use one of these entry points:

```text
MCP: List Servers
```

or:

```text
MCP: Open Workspace Folder Configuration
```

What to verify:

- the `github` server appears in the server list,
- it starts successfully,
- and its tools are visible in the chat tools picker.

If it fails to start:

- open the MCP output log,
- inspect the configuration file for JSON mistakes,
- confirm the server is trusted,
- and confirm your environment allows MCP use.

---

## Step 3: Connect MCP To The Existing Agent Workflow

The local workflow should remain the backbone. MCP should add external context, not replace the local design.

To make the coordinator agent capable of using GitHub MCP tools, replace `.github/agents/lead-architect.agent.md` with this version:

```markdown
---
name: LeadArchitect
description: Coordinator agent for specs-driven feature work. Use when you want a spec analyzed first, then implemented through specialized worker agents, optionally using GitHub context when relevant.
tools: ["agent", "read", "search", "github/*"]
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
2. If the user references a GitHub issue, pull request, or repository discussion that contains relevant requirement context, use the available GitHub MCP tools to retrieve only the details needed for the task.
3. If ambiguities remain, stop and ask clarifying questions.
4. If the requirements are clear, use the `Implementer` subagent to write the code.
5. Return a concise summary of what was implemented and how it maps to the spec.

Important rules:
- Prefer local `docs/specs/` files as the primary source of truth for implementation.
- Use GitHub MCP only to enrich or confirm context, not to silently override the local spec.
- Do not invent requirements that are not present in the spec or confirmed by the user.
- If subagent delegation does not occur automatically, tell the user which handoff to use next.
- Do not write code directly unless the user explicitly asks you to bypass the orchestration workflow.
```

### Why this agent update is the right size

We are not creating a separate "MCP agent" because that would add unnecessary complexity.

Instead, we extend the coordinator with one new external capability while keeping the same role structure.

---

## Step 4: Use MCP In A Realistic Specs-Driven Scenario

If your team stores requirements or design context in GitHub issues, discussions, or PRs, you can now ask questions like:

```text
Use the local spec in docs/specs/task-creation.md as the source of truth. If there is a GitHub issue or pull request in this repository that adds missing context for task creation, summarize only the relevant details and tell me whether the local spec should be updated before implementation.
```

What good behavior looks like:

- the agent still starts from `docs/specs/task-creation.md`,
- it uses GitHub MCP only when relevant,
- and it reports whether external context clarifies or conflicts with the local spec.

### If your repository has no useful GitHub issue yet

That is fine.

This part still succeeds if:

- the MCP server starts,
- its tools appear in chat,
- and the student understands how MCP extends the local workflow when external context exists.

---

## Optional Extension-Backed MCP Example

You asked specifically about including "an extension with an MCP." That fits best as an optional side path, not as the main project-relevant workflow.

The clearest visible example from the docs is a marketplace MCP server such as Playwright.

### Optional installation path

1. Open the Extensions view.
1. Search for:

```text
@mcp playwright
```

1. Install the MCP server.
1. Trust and start it.
1. Open chat and inspect the tools picker.

### Why this is optional rather than the main path

It is a good example of MCP arriving through the Extensions experience, but it is not the best first MCP for this specs-driven backend workflow.

That is why the main teaching path uses GitHub MCP and keeps the extension-installed example as supplemental context.

---

## Step 5: Learn The Operational Basics Of MCP

The student should practice these management actions:

- `MCP: List Servers`
- enabling or disabling a server
- viewing MCP output logs
- checking the chat tools picker

### Security notes worth teaching explicitly

- Only add MCP servers from trusted sources.
- Do not hardcode secrets in `mcp.json`.
- Use input variables or secure configuration when credentials are needed.
- Review trust prompts carefully.

### Optional advanced note

On macOS and Linux, some locally running stdio MCP servers can be sandboxed. That is useful, but it is not required for this tutorial.

---

## Step 6: Verify Part 4

Use these checks:

### Check 1: the server is visible and running

Open:

```text
MCP: List Servers
```

Expected outcome:

- the `github` server is listed,
- and it is enabled or startable.

### Check 2: MCP tools are visible in chat

Open the tools picker in chat.

Expected outcome:

- tools from the `github` server appear and can be enabled.

### Check 3: the coordinator can be extended with MCP capability

Inspect `.github/agents/lead-architect.agent.md`.

Expected outcome:

- the tool list includes `github/*`,
- and the instructions describe when GitHub context should be used.

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
.vscode/
  mcp.json
scripts/
  check-spec-tags.mjs
docs/
  specs/
    task-creation.md
```

---

## What You Should Understand Now

By the end of Part 4, the student should understand:

- why MCP belongs after the local workflow is already clear,
- how MCP extends the agent with external capabilities,
- how to install or configure an MCP server in VS Code,
- and how to keep external context subordinate to the local source of truth.

---

## Where To Go Next

At this point, the student has the complete series outcome:

- local standing rules,
- reusable prompts,
- reusable skills,
- role-based agents,
- deterministic local hooks,
- and one external integration layer through MCP.

The next reasonable directions are:

1. expand the Task API itself,
2. add more specs and tests,
3. or create a separate advanced tutorial on plugin-based or extension-level MCP authoring.
