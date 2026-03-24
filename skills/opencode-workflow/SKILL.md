---
name: opencode-workflow
description: Full OpenCode delegation workflow — routing rules, agent-to-role mapping, model routing, invocation patterns, and examples for delegating tasks to OpenCode (GitHub Copilot). Use when delegating heavy work to OpenCode or making one-shot queries via opencode-ask.
---

# OpenCode Delegation Workflow

**Claude = orchestrator. OpenCode = executor.**

You (Claude) own: planning, decision-making, tool calls (Read/Edit/Bash/Glob/Grep), user communication, final review, and anything requiring direct filesystem or LSP access.

OpenCode owns: heavy implementation work, code generation, multi-file analysis, research, security reviews, architecture drafts, and any task expressible as a self-contained prompt.

## Routing Rules (apply in order)

1. Trivial / single-step → handle directly (no delegation)
2. Multi-step implementation, research, debugging, or any iterative task → `opencode-orchestrator` agent (it handles the OpenCode loop autonomously)
3. One-shot queries (single question, single answer) → `opencode-ask "<task>"` via Bash
4. Requires Claude tool access (file edits, LSP, git, MCP) → Claude subagent (`executor`, `debugger`, etc.)
5. Parallel workloads → fire multiple `opencode-orchestrator` agents in background, or multiple `opencode-ask` calls for simple parallel queries

## Delegation Hierarchy

1. **Multi-step / iterative tasks** → `opencode-orchestrator` agent (handles the back-and-forth loop with OpenCode autonomously until objective is met)
2. **One-shot queries** → `opencode-ask` directly via Bash (single prompt, single response)
3. **Requires Claude tool access** (file edits, LSP, git, MCP) → Claude subagent (`executor`, `debugger`, etc.)

## Banned Subagent Types

Never spawn `general-purpose`, `Explore`, or `Plan` subagent types. These consume Claude tokens for work that OpenCode can handle. Route ALL research, exploration, planning, and multi-step delegation through `opencode-orchestrator` instead. The only Claude subagents permitted are OMC specialized agents (`executor`, `debugger`, `verifier`, etc.) when direct tool access is required.

## Agent-to-Role Mapping

Always use `--agent-prompt` when delegating to opencode. Match the agent role to the task type:

| Task Type | `--agent-prompt` Role |
|-----------|----------------------|
| Implementation / code changes | `executor` |
| Bug investigation / root cause | `debugger` |
| Architecture / system design | `architect` |
| Security audit | `security-reviewer` |
| Code quality / logic review | `code-reviewer` |
| Test strategy / TDD / coverage | `test-engineer` |
| Research / exploration | `scientist` or `explore` |
| Codebase search / pattern find | `explore` |
| External docs / SDK reference | `document-specialist` |
| Documentation | `writer` |
| Planning / breakdown | `planner` |
| Pre-planning / requirements | `analyst` |
| Verification / correctness check | `verifier` |
| Git commits / history / rebasing | `git-master` |
| Causal tracing / evidence-driven debug | `tracer` |
| Plan critique / multi-perspective review | `critic` |

**Full catalog:** `copilot-instructions.md` → "AI Tooling: Claude Code Agents & Skills" section lists every available `--agent-prompt` role, every Claude Code skill (`/skill-name`), and model routing rules.

## Model Routing

Invoke via `opencode-ask -m <model>`:

| Model | Best For |
|-------|----------|
| `github-copilot/claude-sonnet-4.6` | **Default.** All code tasks, implementation, debugging, reviews |
| `github-copilot/claude-opus-4.6` | Complex reasoning, architecture decisions, deep analysis |
| `github-copilot/gpt-5` | Complex reasoning, architecture decisions (alternative to opus) |
| `github-copilot/gemini-2.5-pro` | Large context analysis, research, multi-file review |
| `github-copilot/gpt-5.1-codex` | Pure code generation, completions |
| `github-copilot/gpt-5-mini` | Fast lookups, simple summarization, cheap single-purpose tasks |

## Invocation Patterns

### Multi-step tasks → `opencode-orchestrator` agent (preferred for iterative work)

Use the Agent tool with `subagent_type: "opencode-orchestrator"`. Always set `mode: "bypassPermissions"`. Provide a clear objective and success criteria. The orchestrator handles the back-and-forth with OpenCode autonomously.

```
# Examples of when to use opencode-orchestrator:
- "Implement the favorites system end-to-end"
- "Debug and fix the duplicate listings issue"
- "Research the best notification strategy for our stack"
- "Refactor the auth module to use JWT tokens"
```

### One-shot queries → `opencode-ask` (for single prompt/response)

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

## Requirements

- `opencode` installed at `/home/moli/.opencode/bin/opencode`
- GitHub Copilot authenticated (`opencode providers list` shows GitHub Copilot with oauth)
- `opencode-ask` wrapper on PATH at `~/.local/bin/opencode-ask`

Verify with:

```bash
opencode-ask --help 2>&1 || echo "wrapper not found"
opencode providers list
```

## When to Use OpenCode Delegation

- Token-intensive research or analysis tasks that would consume heavy Claude quota
- Second-opinion code review from a different model
- Large-context tasks (Gemini 2.5 Pro has a massive context window)
- Parallel workloads — run opencode-ask in background while Claude handles other tasks
- Any task where `omc ask codex/gemini` would be used but those CLIs aren't installed

## Task

{{ARGUMENTS}}
