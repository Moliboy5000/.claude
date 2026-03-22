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

** USE GITHUB-ISSUE-CREATOR FOR ISSUES OUT OF SCOPE **
If you discover bugs, security features or anything that can be improved but is out of scope do not try to fix it. Instead call the github-issue-creator agent who will post an issue on github.

**OPENCODE FIRST — default delegation target:**
Before spawning a Claude subagent for any heavy task, prefer delegating via OpenCode (GitHub Copilot). This conserves Claude quota for orchestration. Use Claude subagents only when deep tool access (file edits, LSP, git) is required and cannot be expressed as a prompt.

**BANNED SUBAGENT TYPES — never use these:**
Never spawn `general-purpose`, `Explore`, or `Plan` subagent types. These consume Claude tokens for work that OpenCode can handle. Route ALL research, exploration, planning, and multi-step delegation through `opencode-orchestrator` instead. The only Claude subagents permitted are OMC specialized agents (`executor`, `debugger`, `verifier`, etc.) when direct tool access is required.

**Delegation hierarchy (apply in order):**
1. **Multi-step / iterative tasks** → `opencode-orchestrator` agent (handles the back-and-forth loop with OpenCode autonomously until objective is met)
2. **One-shot queries** → `opencode-ask` directly via Bash (single prompt, single response)
3. **Requires Claude tool access** (file edits, LSP, git, MCP) → Claude subagent (`executor`, `debugger`, etc.)
</delegation_rules>

<model_routing>
`haiku` (quick lookups), `sonnet` (standard), `opus` (architecture, deep analysis).
Direct writes OK for: `~/.claude/**`, `.omc/**`, `.claude/**`, `CLAUDE.md`, `AGENTS.md`.

**OpenCode model routing (invoke via `opencode-ask -m <model>`):**
- `github-copilot/claude-sonnet-4.6` — **default**; all code tasks, implementation, debugging, reviews
- `github-copilot/claude-opus-4.6` — complex reasoning, architecture decisions, deep analysis
- `github-copilot/gpt-5-mini` — fast lookups, simple summarization, cheap single-purpose tasks
</model_routing>

<opencode_delegation>
**Claude = orchestrator. OpenCode = executor.**

You (Claude) own: planning, decision-making, tool calls (Read/Edit/Bash/Glob/Grep), user communication, final review, and anything requiring direct filesystem or LSP access.

OpenCode owns: heavy implementation work, code generation, multi-file analysis, research, security reviews, architecture drafts, and any task expressible as a self-contained prompt.

**Routing rules — apply in this order:**
1. Trivial / single-step → handle directly (no delegation)
2. Multi-step implementation, research, debugging, or any iterative task → `opencode-orchestrator` agent (it handles the OpenCode loop autonomously)
3. One-shot queries (single question, single answer) → `opencode-ask "<task>"` via Bash
4. Requires Claude tool access (file edits, LSP, git, MCP) → Claude subagent (`executor`, `debugger`, etc.)
5. Parallel workloads → fire multiple `opencode-orchestrator` agents in background, or multiple `opencode-ask` calls for simple parallel queries

**Always use `--agent-prompt` when delegating to opencode.** Match the agent role to the task type — this shapes opencode's persona, constraints, and output style to match the OMC agent catalog.

**Full catalog:** `copilot-instructions.md` → "AI Tooling: Claude Code Agents & Skills" section lists every available `--agent-prompt` role, every Claude Code skill (`/skill-name`), and model routing rules. OpenCode can reference this catalog too.

Agent-to-role mapping:
- Implementation / code changes → `executor`
- Bug investigation / root cause → `debugger`
- Architecture / system design → `architect`
- Security audit → `security-reviewer`
- Code quality / logic review → `code-reviewer`
- Test strategy / TDD / coverage → `test-engineer`
- Research / exploration → `scientist` or `explore`
- Codebase search / pattern find → `explore`
- External docs / SDK reference → `document-specialist`
- Documentation → `writer`
- Planning / breakdown → `planner`
- Pre-planning / requirements → `analyst`
- Verification / correctness check → `verifier`
- Git commits / history / rebasing → `git-master`
- Causal tracing / evidence-driven debug → `tracer`
- Plan critique / multi-perspective review → `critic`

**Invocation patterns:**

**Multi-step tasks → `opencode-orchestrator` agent (preferred for iterative work):**
Use the Agent tool with `subagent_type: "opencode-orchestrator"`. Provide a clear objective and success criteria. The orchestrator handles the back-and-forth with OpenCode autonomously.
```
# Examples of when to use opencode-orchestrator:
- "Implement the favorites system end-to-end"
- "Debug and fix the duplicate listings issue"
- "Research the best notification strategy for our stack"
- "Refactor the auth module to use JWT tokens"
```

**One-shot queries → `opencode-ask` (for single prompt/response):**
```bash
# Standard — always include --agent-prompt
opencode-ask --agent-prompt executor "implement the repository layer for listings"
opencode-ask --agent-prompt debugger "trace why the feed returns 401 intermittently"
opencode-ask --agent-prompt security-reviewer "audit brokr-api/src/auth for vulnerabilities"

# Architecture tasks — use opus
opencode-ask --agent-prompt architect -m github-copilot/claude-opus-4.6 "design the caching strategy"

# Fast lookup — no agent needed, use gpt-5-mini
opencode-ask -m github-copilot/gpt-5-mini "what HTTP status code for a soft-delete endpoint"

# With artifact capture
opencode-ask --agent-prompt code-reviewer "review the listings module" > .omc/artifacts/ask/opencode-review.md

# Parallel one-shot queries — fire in background
opencode-ask --agent-prompt executor "task A" > /tmp/oc-a.md &
opencode-ask --agent-prompt executor "task B" > /tmp/oc-b.md &
wait && cat /tmp/oc-a.md /tmp/oc-b.md
```
</opencode_delegation>

<agent_catalog>
Prefix: `oh-my-claudecode:`. See `agents/*.md` for full prompts.

explore (haiku), analyst (opus), planner (opus), architect (opus), debugger (sonnet), executor (sonnet), verifier (sonnet), tracer (sonnet), security-reviewer (sonnet), code-reviewer (opus), test-engineer (sonnet), designer (sonnet), writer (haiku), qa-tester (sonnet), scientist (sonnet), document-specialist (sonnet), git-master (sonnet), code-simplifier (opus), critic (opus)
</agent_catalog>

<tools>
External AI: `opencode-orchestrator` agent (multi-step OpenCode delegation), `opencode-ask` (one-shot queries), `/team N:executor "task"`, `omc team N:codex|gemini "..."`, `omc ask <claude|codex|gemini>`, `/ccg` (GitHub Copilot via opencode — default: `claude-sonnet-4.6`, architecture: `claude-opus-4.6`, fast: `gpt-5-mini`)
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

## You typically have access to these additional command-line tools (otherwise you can ask me for them)
- **AWS CLI:** USE VERY CAREFULLY, you are free to read whatever you want but always double check any changes you want to make.
- **Github CLI** Feel free to use in addition to the MCP server if you wish.

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
- **No Explore subagent:** Never use the `Explore` subagent for codebase exploration or research. Instead, delegate to `opencode-orchestrator` which uses OpenCode (GitHub Copilot) to conserve Claude tokens. Always use `mode: "bypassPermissions"` when spawning `opencode-orchestrator` so it can read and edit files without being blocked.

@RTK.md

## gstack

- For all web browsing, navigation, and QA tasks, use the `/browse` skill from gstack — never use `mcp__claude-in-chrome__*` tools.
- If gstack skills aren't working, run `cd ~/.claude/skills/gstack && ./setup` to rebuild the binary and register skills.

Available gstack skills:
- `/plan-ceo-review` — Founder/CEO mode: rethink the problem, find the 10-star product
- `/plan-eng-review` — Eng manager mode: architecture, data flow, diagrams, edge cases, tests
- `/review` — Paranoid staff engineer: find bugs that pass CI but blow up in production
- `/ship` — Release engineer: sync main, run tests, resolve reviews, push, open PR
- `/browse` — QA engineer: give the agent eyes to navigate, click, screenshot, and verify
- `/qa` — QA + fix engineer: test app, find bugs, fix with atomic commits, re-verify
- `/qa-only` — QA reporter: pure bug report without code changes
- `/setup-browser-cookies` — Session manager: import real browser cookies for authenticated testing
- `/retro` — Engineering manager: team-aware retrospective with metrics and per-person feedback
