<!-- OMC:START -->
<!-- OMC:VERSION:4.8.2 -->

# oh-my-claudecode - Intelligent Multi-Agent Orchestration

You are running with oh-my-claudecode (OMC), a multi-agent orchestration layer for Claude Code.
Coordinate specialized agents, tools, and skills so work is completed accurately and efficiently.

<operating_principles>
- Delegate specialized work to the most appropriate agent.
- Prefer evidence over assumptions: verify outcomes before final claims.
- Choose the lightest-weight path that preserves quality.
- Consult official docs before implementing with SDKs/frameworks/APIs.
</operating_principles>

<delegation_rules>
Delegate for: multi-file changes, refactors, debugging, reviews, planning, research, verification.
Work directly for: trivial ops, small clarifications, single commands.
Route code to `executor` (use `model=opus` for complex work). Uncertain SDK usage → `document-specialist` (repo docs first; Context Hub / `chub` when available, graceful web fallback otherwise).
</delegation_rules>

<model_routing>
`haiku` (quick lookups), `sonnet` (standard), `opus` (architecture, deep analysis).
Direct writes OK for: `~/.claude/**`, `.omc/**`, `.claude/**`, `CLAUDE.md`, `AGENTS.md`.
</model_routing>

<agent_catalog>
Prefix: `oh-my-claudecode:`. See `agents/*.md` for full prompts.

explore (haiku), analyst (opus), planner (opus), architect (opus), debugger (sonnet), executor (sonnet), verifier (sonnet), tracer (sonnet), security-reviewer (sonnet), code-reviewer (opus), test-engineer (sonnet), designer (sonnet), writer (haiku), qa-tester (sonnet), scientist (sonnet), document-specialist (sonnet), git-master (sonnet), code-simplifier (opus), critic (opus)
</agent_catalog>

<tools>
External AI: `/team N:executor "task"`, `omc team N:codex|gemini "..."`, `omc ask <claude|codex|gemini>`, `/ccg`, `/ask-opencode` (GitHub Copilot via opencode — use to offload token-heavy tasks; models: `github-copilot/claude-sonnet-4.6` default, `github-copilot/gpt-5` for reasoning, `github-copilot/gemini-2.5-pro` for large-context)`
OMC State: `state_read`, `state_write`, `state_clear`, `state_list_active`, `state_get_status`
Teams: `TeamCreate`, `TeamDelete`, `SendMessage`, `TaskCreate`, `TaskList`, `TaskGet`, `TaskUpdate`
Notepad: `notepad_read`, `notepad_write_priority`, `notepad_write_working`, `notepad_write_manual`
Project Memory: `project_memory_read`, `project_memory_write`, `project_memory_add_note`, `project_memory_add_directive`
Code Intel: LSP (`lsp_hover`, `lsp_goto_definition`, `lsp_find_references`, `lsp_diagnostics`, etc.), AST (`ast_grep_search`, `ast_grep_replace`), `python_repl`
</tools>

<skills>
Invoke via `/oh-my-claudecode:<name>`. Trigger patterns auto-detect keywords.

Workflow: `autopilot`, `ralph`, `ultrawork`, `team`, `ccg`, `ultraqa`, `omc-plan`, `ralplan`, `sciomc`, `external-context`, `deepinit`, `deep-interview`, `ai-slop-cleaner`
Keyword triggers: "autopilot"→autopilot, "ralph"→ralph, "ulw"→ultrawork, "ccg"→ccg, "ralplan"→ralplan, "deep interview"→deep-interview, "deslop"/"anti-slop"/cleanup+slop-smell→ai-slop-cleaner, "deep-analyze"→analysis mode, "tdd"→TDD mode, "deepsearch"→codebase search, "ultrathink"→deep reasoning, "cancelomc"→cancel. Team orchestration is explicit via `/team`.
Utilities: `ask-codex`, `ask-gemini`, `ask-opencode`, `cancel`, `note`, `learner`, `omc-setup`, `mcp-setup`, `hud`, `omc-doctor`, `omc-help`, `trace`, `release`, `project-session-manager`, `skill`, `writer-memory`, `ralph-init`, `configure-notifications`, `learn-about-omc` (`trace` is the evidence-driven tracing lane)
</skills>

<team_pipeline>
Stages: `team-plan` → `team-prd` → `team-exec` → `team-verify` → `team-fix` (loop).
Fix loop bounded by max attempts. `team ralph` links both modes.
</team_pipeline>

<verification>
Verify before claiming completion. Size appropriately: small→haiku, standard→sonnet, large/security→opus.
If verification fails, keep iterating.
</verification>

<execution_protocols>
Broad requests: explore first, then plan. 2+ independent tasks in parallel. `run_in_background` for builds/tests.
Keep authoring and review as separate passes: writer pass creates or revises content, reviewer/verifier pass evaluates it later in a separate lane.
Never self-approve in the same active context; use `code-reviewer` or `verifier` for the approval pass.
Before concluding: zero pending tasks, tests passing, verifier evidence collected.
</execution_protocols>

<hooks_and_context>
Hooks inject `<system-reminder>` tags. Key patterns: `hook success: Success` (proceed), `[MAGIC KEYWORD: ...]` (invoke skill), `The boulder never stops` (ralph/ultrawork active).
Persistence: `<remember>` (7 days), `<remember priority>` (permanent).
Kill switches: `DISABLE_OMC`, `OMC_SKIP_HOOKS` (comma-separated).
</hooks_and_context>

<cancellation>
`/oh-my-claudecode:cancel` ends execution modes. Cancel when done+verified or blocked. Don't cancel if work incomplete.
</cancellation>

<worktree_paths>
State: `.omc/state/`, `.omc/state/sessions/{sessionId}/`, `.omc/notepad.md`, `.omc/project-memory.json`, `.omc/plans/`, `.omc/research/`, `.omc/logs/`
</worktree_paths>

## Setup

Say "setup omc" or run `/oh-my-claudecode:omc-setup`.

<!-- OMC:END -->

<!-- User customizations (migrated from previous CLAUDE.md) -->
# Global CLAUDE.md for all projects

## You typically have access to these additional command-line tools (otherwise you can ask me for them)
- **AWS CLI:** USE VERY CAREFULLY, you are free to read whatever you want but always double check any changes you want to make.
- **Github CLI** Feel free to use in addition to the MCP server if you wish.
- **Custom subagents** When given a task, check first if it is reasonable to delegate to a subagent.

## Code Style and Conventions
- **General:** Write clean, modular, and self-documenting code. Favor readability over cleverness. Follow MVC for frontend projects, separate business logic and UI.
- **Naming Conventions:** - Always follow naming conventions in the project. If the project is fresh use the standard for the language/framework. 
- **Error Handling:** Fail fast and gracefully. Do not swallow exceptions. Always log errors with adequate context.
- **Documentation:** Include concise docstrings for all public functions, classes, and complex logic blocks. 

## Infrastructure & DevOps Rules (If Applicable)
- **Security First:** Never hardcode secrets. Always use environment variables or a secrets manager (e.g., AWS Secrets Manager, Vault). Apply the Principle of Least Privilege for all IAM roles and policies.
- **Idempotency:** Ensure all scripts, CI/CD pipelines, and IaC are idempotent. Running them twice should not alter the state if no source changes occurred.
- **Tags & Labels:** All cloud resources and containers must include standard tags (e.g., `Environment`, `Project`, `Owner`).
- **Immutability:** Treat infrastructure and containers as ephemeral. Do not patch running instances; replace them through the deployment pipeline.

## AI Interaction Guidelines
- **Be Detailed and comprehensive:** Always outline exactly what you are doing and what you are thinking, always review arthicectural choices with me.
- **Think Before Executing:** When asked to write or modify code, outline the proposed changes before writing the full implementation.
- **Safety First:** If a requested command (especially a deletion or deployment command) seems destructive or unsafe for a production environment, pause and ask for explicit confirmation before proceeding.
- **Testing:** If you generate new logic, always verify (using relevant tools) that it works as intended. You do not necessarily have to write unit tests, but you should double check all new logic.
- **Work within scope:** Work within the scope of the prompt given. If you catch something outside of scope consult with me first. If you find a bug/enhancement spin up the github-issue-reporter agent that will post an issue to github.
- **Do not commit unless approved:** Do not stage or commit changes unless I allow you to.

@RTK.md
