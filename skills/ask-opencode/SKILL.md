---
name: ask-opencode
description: Delegate a task to OpenCode (GitHub Copilot) via opencode run and capture the result as a reusable artifact. Use when you want to offload work to GitHub Copilot's generous usage limits — code review, architecture analysis, research, or any task benefiting from a second model opinion.
---

# Ask OpenCode (GitHub Copilot)

Delegate a task to OpenCode backed by GitHub Copilot. Results are saved as artifacts matching
the OMC ask convention at `.omc/artifacts/ask/opencode-<slug>-<timestamp>.md`.

## Usage

```
/ask-opencode <task>
```

Examples:

```
/ask-opencode review this NestJS module for security issues
/ask-opencode suggest a caching strategy for the feed endpoint
/ask-opencode -m github-copilot/gpt-5 design the auth refactor architecture
/ask-opencode -m github-copilot/gemini-2.5-pro analyze the full codebase for patterns
```

## Model Routing

Choose the model based on the task type. Pass via `-m` or set `OPENCODE_MODEL` env var.

| Model | Best for |
|-------|----------|
| `github-copilot/claude-sonnet-4.6` | **Default.** Code tasks, bug fixes, implementation |
| `github-copilot/gpt-5` | Complex reasoning, architecture decisions |
| `github-copilot/gemini-2.5-pro` | Large context analysis, research, multi-file review |
| `github-copilot/gpt-5.1-codex` | Pure code generation, completions |
| `github-copilot/gpt-5-mini` | Fast / low-cost simple tasks |

## Execution

### Direct invocation (from Bash tool)

```bash
opencode-ask "review the JWT strategy in brokr-api/src/auth/jwt.strategy.ts"
```

With model override:

```bash
opencode-ask -m github-copilot/gpt-5 "design a caching layer for the listings endpoint"
```

### With artifact capture (OMC convention)

```bash
SLUG="security-review"
TS=$(date -u +%Y%m%dT%H%M%SZ)
ARTIFACT_DIR=".omc/artifacts/ask"
mkdir -p "$ARTIFACT_DIR"
opencode-ask "review brokr-api for OWASP top 10 issues" \
  > "$ARTIFACT_DIR/opencode-${SLUG}-${TS}.md"
```

### As OMC Bash-tool delegate in orchestration

When orchestrating parallel tasks, delegate heavy or token-intensive subtasks:

```bash
# In a Bash tool call during orchestration:
opencode-ask -m github-copilot/gemini-2.5-pro \
  "analyze all TypeORM entities for N+1 query risks and suggest eager-loading fixes"
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

## When to use

- Token-intensive research or analysis tasks that would consume heavy Claude quota
- Second-opinion code review from a different model
- Large-context tasks (Gemini 2.5 Pro has a massive context window)
- Parallel workloads — run opencode-ask in background while Claude handles other tasks
- Any task where `omc ask codex/gemini` would be used but those CLIs aren't installed

## Task

{{ARGUMENTS}}