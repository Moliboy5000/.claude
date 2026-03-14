# ~/.claude

Personal Claude Code configuration directory. Contains skills, agents, and plugins that extend Claude Code's capabilities.

## Structure

```
~/.claude/
├── .gitignore          # Tracks only: .README.md, .CLAUDE.md, .gitignore, skills/, agents/, plugins/
├── CLAUDE.md           # Global instructions applied to all Claude Code sessions
├── skills/             # Slash-command skills (invoked via /skill-name)
├── agents/             # Specialized sub-agents
└── plugins/            # Plugin registry and blocklist
```

---

## Skills

Skills are reusable prompts/tools invoked via `/skill-name` inside Claude Code. Each skill lives in `skills/<name>/SKILL.md`.

### Document & File Processing

| Skill | Description |
|-------|-------------|
| `pdf` | Read, extract, merge, split, rotate, watermark, create, fill forms, OCR, and encrypt PDFs. Uses `pypdf`, `pdfplumber`, `reportlab`, and CLI tools (`qpdf`, `pdftotext`). |
| `pptx` | Create, edit, read, and QA PowerPoint presentations. Supports template editing, from-scratch generation via `pptxgenjs`, visual QA via LibreOffice + subagents. |
| `xlsx` | Read, create, edit, and convert spreadsheet files (`.xlsx`, `.xlsm`, `.csv`, `.tsv`). |

### Design & Media

| Skill | Description |
|-------|-------------|
| `imagegen` | Generate and edit images via the OpenAI Image API. |
| `speech` | Text-to-speech narration using the OpenAI Audio API. |
| `transcribe` | Transcribe audio/video to text with optional speaker diarization. |
| `sora` | Generate, remix, and manage AI videos via OpenAI Sora API. |
| `frontend-design` | Build production-grade web UI (components, pages, landing pages) with high design quality. |
| `canvas-design` | Create visual art and design assets as `.png`/`.pdf` using design principles. |
| `web-artifacts-builder` | Build multi-component HTML artifacts with React, Tailwind CSS, and shadcn/ui. |
| `theme-factory` | Apply or generate visual themes (colors/fonts) for artifacts, slides, and docs. |
| `screenshot` | Capture desktop or system screenshots at OS level. |

### Development Tools

| Skill | Description |
|-------|-------------|
| `skill-creator` | Create, modify, eval, and benchmark Claude Code skills. |
| `webapp-testing` | Interact with and test local web apps using Playwright. |
| `gh-fix-ci` | Debug and fix failing GitHub Actions CI checks via `gh` CLI. |
| `merge-conflict-resolver` | Interactively resolve git merge, rebase, and cherry-pick conflicts. |
| `simplify` | Review recently changed code for quality, reuse, and efficiency, then fix issues. |
| `claude-api` | Build apps with the Claude API / Anthropic SDK. |

### Security

| Skill | Description |
|-------|-------------|
| `security-threat-model` | Enumerate trust boundaries, assets, attack paths, and mitigations for a codebase. |
| `security-ownership-map` | Security-oriented git ownership analysis: bus factor, sensitive-code owners, orphaned code. |
| `security-best-practices` | Language/framework-specific security review (Python, JS/TS, Go). |

### Notion & Documentation

| Skill | Description |
|-------|-------------|
| `doc-coauthoring` | Structured workflow for co-authoring documentation, proposals, and specs. |
| `notion-research-documentation` | Research across Notion and produce structured reports with citations. |
| `notion-meeting-intelligence` | Prepare meeting materials with Notion context and research. |
| `notion-spec-to-implementation` | Turn Notion specs into implementation plans and tasks. |
| `notion-knowledge-capture` | Capture conversations and decisions into structured Notion pages. |

### Design Integration

| Skill | Description |
|-------|-------------|
| `figma` | Fetch design context, screenshots, variables, and assets from Figma via MCP server. |
| `figma-implement-design` | Translate Figma nodes into production code with 1:1 visual fidelity. |

---

## Agents

Specialized sub-agents invoked automatically or via the `Agent` tool for domain-specific tasks.

| Agent | Description |
|-------|-------------|
| `senior-backend-architect` | Backend systems, REST/GraphQL/gRPC APIs, microservices, DB optimization, auth flows, cloud-native architecture. |
| `react-architect` | React/Next.js UI components with MVC separation, TypeScript, and accessibility standards. |
| `expo-react-native-architect` | Expo/React Native mobile apps, Reanimated animations, Expo Router, performance optimization. |
| `senior-devops-platform-engineer` | Cloud infrastructure (AWS/GCP/Azure), Terraform/Pulumi IaC, Kubernetes, CI/CD, observability, security hardening. |

---

## Plugins

Plugin registry managed under `plugins/`.

| File | Description |
|------|-------------|
| `known_marketplaces.json` | Registered plugin marketplaces and their install locations (e.g., `claude-plugins-official` from `anthropics/claude-plugins-official`). |
| `blocklist.json` | Plugins that have been blocked from loading, with reason and timestamp. |
