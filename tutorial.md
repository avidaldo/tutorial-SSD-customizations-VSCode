# Tutorial: Building a Multi-Agent Specs-Driven Development System in VS Code

This comprehensive tutorial demonstrates how to transform GitHub Copilot in VS Code from a simple autocomplete tool into a sophisticated, multi-agent orchestration system. We will construct a "Task Management REST API" using Specs-Driven Development (SDD). 

You will learn to create and utilize every major Copilot customization primitive: **Instructions, Prompts, Agents, Skills, and Hooks**.

---

## Prerequisites

1.  **Environment:** Visual Studio Code with the **GitHub Copilot** and **GitHub Copilot Chat** extensions installed and updated.
2.  **Workspace:** Open a completely empty directory in VS Code (e.g., `mkdir task-api && cd task-api && code .`).
3.  **Source Control:** Initialize a git repository (`git init`), as many Copilot tools rely on git diffs to understand workspace context.

---

## Phase 1: Foundation and Global Rules (Instructions)

Instructions are passive rules that automatically append to Copilot's context window. They ensure that no matter what agent you use, your baseline engineering standards are maintained.

### Step 1: Initialize Baseline Instructions
**Action:** Call a Built-in Tool
1. Open the Copilot Chat (`Ctrl+Alt+I` / `Cmd+Option+I`).
2. Type the built-in command: `/init`
3. When prompted for a description, paste: 
   > "A backend microservice for a Task Management application. It provides a RESTful API built with Node.js and TypeScript. Key features include CRUD operations for tasks and strict state transitions."

**Result:** Copilot generates `.github/copilot-instructions.md`.

### Step 2: Create Specialized Instruction Files
To keep rules organized, we will create domain-specific instruction files. 

**Action:** Manual Edit
1. Create a directory: `.github/instructions/`
2. Create `.github/instructions/sdd-rules.instructions.md` and add the following:
   ```markdown
   # Specs-Driven Development Rules
   - NEVER generate architectural code without first consulting the relevant markdown specification in `docs/specs/`.
   - Every generated function MUST include a JSDoc block containing the `@spec` tag pointing to the requirement ID it fulfills.
   - If a specification is ambiguous, stop and output a markdown list of clarifying questions.
   ```
3. Create `.github/instructions/testing-standards.instructions.md` and add:
   ```markdown
   # Testing Standards
   - All tests must be written using Jest.
   - Every edge case defined in a specification file must have a dedicated unit test.
   - Mock all external database calls; unit tests must not perform actual I/O.
   ```

---

## Phase 2: Standardizing Inputs and Workflows (Prompts & Skills)

### Step 3: Create a Reusable Prompt
Prompts act as templates to ensure human developers interact with the AI consistently.

**Action:** Built-in Tool
1. In the Copilot Chat, type:
   > `/create-prompt Create a prompt called 'implement-spec'. It should ask the AI to implement the feature defined in a specific markdown file. Include a variable so the user can specify the filename located in 'docs/specs/'. Instruct the AI to explicitly state which SDD rules it is following before writing code.`
2. Review the generated `.github/prompts/implement-spec.prompt.md` file.

### Step 4: Define a Validation Skill
Skills bundle multi-step workflows that agents can trigger when performing complex tasks.

**Action:** Built-in Tool
1. In the Copilot Chat, type:
   > `/create-skill Create a skill called 'spec-validation'. The workflow should: 1. Read the provided specification file. 2. Read the generated code in the active editor. 3. Cross-reference the code's JSDoc @spec tags against the markdown IDs. 4. Output a Markdown table showing 'Requirement ID', 'Implemented (Yes/No)', and 'Notes'.`
2. Review the generated `.github/skills/spec-validation/SKILL.md` file.

---

## Phase 3: Building the Multi-Agent Ecosystem (Agents & Hooks)

We will set up an orchestrator (Lead Architect) that delegates to specialized subagents.

### Step 5: Create the Subagents
**Action:** Built-in Tool
1. Create the Researcher. In chat, type:
   > `/create-agent A read-only subagent named 'SpecReader'. Tools allowed: 'search', 'read'. Goal: Analyze markdown files in 'docs/specs/' and summarize technical requirements, strictly avoiding code generation.`
2. Create the Coder. In chat, type:
   > `/create-agent A subagent named 'Implementer'. Tools allowed: 'edit', 'read'. Goal: Write TypeScript code based strictly on the summaries provided by the SpecReader, adhering to all global workspace instructions.`

### Step 6: Create the Orchestrator Agent
**Action:** Manual Edit
We must manually configure the YAML frontmatter to restrict this agent to orchestration.

1. Create a file at `.github/agents/architect.agent.md`.
2. Add the following content:
   ```markdown
   ---
   name: LeadArchitect
   description: Orchestrates feature development by delegating to SpecReader and Implementer.
   tools: ['agent']
   agents: ['SpecReader', 'Implementer']
   ---
   You are the Orchestrator. When a user requests a feature implementation:
   1. Delegate to the `SpecReader` to analyze the target markdown spec.
   2. Take the SpecReader's output and pass it to the `Implementer` to write the code.
   3. Once the Implementer is finished, summarize the completed work for the user.
   Do not write code yourself.
   ```

### Step 7: Configure an Event Hook
Hooks allow AI routines to run automatically based on workspace events. We will create a hook that triggers an automatic review when code is staged for a commit.

**Action:** Manual Edit
1. Open `.vscode/settings.json` (create it if necessary) and ensure custom hooks are enabled (syntax varies by exact Copilot version, but conceptually requires explicit opt-in):
   ```json
   {
     "github.copilot.chat.useCustomAgentHooks": true
   }
   ```
2. Create `.github/hooks/pre-commit.hook.md`:
   
```markdown
   ---
   event: pre-commit
   description: Automatically validates staged files against SDD rules.
   ---
   When files are staged for commit, analyze the diff. 
   If the diff contains source code (`.ts` files), invoke the `spec-validation` skill to ensure the new code contains the required `@spec` JSDoc tags. If tags are missing, block the commit and warn the user.
   ```

---

## Phase 4: Running the Simulation

Now that the ecosystem is built, let's execute a development cycle.

### Step 8: Write the Specification
**Action:** Manual Edit
1. Create `docs/specs/task-creation.md`.
2. Add the following business logic:
   ```markdown
   # Task Creation Specification
   
   ## Requirements
   - **REQ-TC-01**: The system must expose a `POST /tasks` endpoint.
   - **REQ-TC-02**: The payload must strictly validate: `title` (string, max 50 chars) and `status` (must default to 'To-Do' if omitted).
   - **REQ-TC-03**: If the `title` exceeds 50 characters, return a 400 Bad Request with the message "Title too long".
   ```

### Step 9: Orchestrate the Development
**Action:** Invoke Customizations
1. Open the Copilot Chat.
2. Click the **Agent Picker** (or use the `@` menu) and select **LeadArchitect**.
3. Type your custom prompt command:
   > `/implement-spec task-creation.md`
4. **Observe the Cascade:** 
   - The *LeadArchitect* will load.
   - It will internally invoke the *SpecReader* to process `task-creation.md`.
   - It will pass the parsed requirements (REQ-TC-01 to 03) to the *Implementer*.
   - The *Implementer* will generate `src/routes/task.ts` and `src/controllers/task.ts`, injecting JSDoc comments like `/** @spec REQ-TC-03 */`.

### Step 10: Validate the Output
**Action:** Invoke Skill
1. Open the newly generated `src/controllers/task.ts` file so it is active in your editor.
2. Switch back to the default Copilot agent in the chat.
3. Type:
   > `/spec-validation`
4. **Observe the Output:** Copilot will execute the skill workflow, reading your spec and the active file, and outputting a Markdown table confirming that `REQ-TC-01`, `02`, and `03` were successfully implemented in the code.