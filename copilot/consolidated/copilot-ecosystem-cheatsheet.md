# GitHub Copilot: The Modern AI Development Ecosystem

## Overview

GitHub Copilot has evolved well beyond code autocomplete. It's now a full AI development ecosystem made up of interconnected pieces — agents, Copilot Chat, agentic workflows, a CLI, skills, instructions, and MCP servers — each serving a distinct role. Understanding what each piece does, and *when* to use which, is key to getting the most out of Copilot.


## 💬 Copilot Chat

**Copilot Chat** is the conversational, real-time AI interface built into your IDE (VS Code, JetBrains, Visual Studio) and GitHub.com. It's the most interactive and immediate way to work with Copilot — think of it as a knowledgeable pair programmer you can talk to at any point during development.

What you can do with Copilot Chat:
- Ask questions about your codebase — *"What does this function do?"* or *"Where is authentication handled?"*
- Get code explanations, debugging help, and suggestions inline
- Request multi-file edits using **Copilot Edits** — describe the change in natural language, Copilot applies it across files
- Run in **Agent Mode** inside the IDE — Copilot takes a goal, plans steps, edits files, runs tests, reads errors, and iterates autonomously *within your local workspace*
- Get code reviews and refactoring suggestions on-demand

**Agent Mode** is what elevates Chat from Q&A into something more powerful. In Agent Mode, you give Copilot a high-level goal and it loops — editing, testing, correcting, and trying again — until it completes the task or surfaces a blocker. All changes remain local until *you* commit.

> **Best used for:** Interactive, real-time development work — prototyping, debugging, learning, exploring a codebase, rapid iteration, and complex tasks that benefit from tight human-AI collaboration. Use Agent Mode when you want Copilot to work *with you*, step by step, right now.


## 🤖 Copilot Agents (Coding Agent)

While Copilot Chat works *with* you in real time, the **Copilot Coding Agent** works *for* you asynchronously. It's a fully autonomous AI developer you assign tasks to — then it goes off, does the work in a secure cloud environment, and comes back with a pull request for your review.

Think of it like this: **Chat is your pair programmer. The Coding Agent is your junior developer.**

Agents are defined using `.agent.md` files stored in `.github/agents/` in your repository (e.g., `.github/agents/my-agent.agent.md`), or for org-wide agents, in the `agents/` folder of your org's `.github` repository. Each agent file uses a YAML frontmatter header to configure its name, description, tools, and connected MCP servers — followed by plain Markdown instructions that shape its persona and behavior ([docs](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents), [best practices](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)). Each agent carries:
- **Full codebase context** — reads your code, docs, past issues and PRs
- **Tool access** — creates branches, writes code, runs tests via GitHub Actions
- **Model flexibility** — tunable to different AI models
- **External connections** — via MCP servers declared in the agent file or in `.mcp.json` at the repo root, routing calls to external APIs, databases, CI/CD systems, and more

The Coding Agent workflow:
1. Picks up a task (via an Issue or Agents panel)
2. Creates a branch (`copilot/*`)
3. Writes code, runs builds and tests in an isolated environment
4. Opens a **draft PR** with a session log of what it did and why
5. Responds to PR review comments with new commits
6. Waits for *you* to approve and merge — it cannot merge its own work

You can invoke agents from GitHub.com, VS Code, GitHub Mobile, or the Copilot CLI.

> **Best used for:** Well-scoped, clearly described tasks — bug fixes, writing tests, refactoring, adding features, generating documentation — especially when you want to delegate and come back to review, rather than actively guide the work.


## 🔌 MCP Servers (Model Context Protocol)

**MCP (Model Context Protocol)** is an open standard that lets Copilot agents connect to external tools, data sources, and services at runtime — without hardcoding integrations or redeploying anything. Think of MCP servers as plug-ins that give agents new superpowers: the ability to query a database, trigger a CI/CD pipeline, search internal docs, call a REST API, or interact with any external system.

### How it works

An **MCP server** exposes three types of capabilities to an agent:
- **Tools** — callable functions the agent can invoke (e.g., "run this query," "create a ticket")
- **Resources** — data the agent can read (e.g., files, database records, API responses)
- **Prompts** — predefined prompt templates for common tasks

Agents discover and call MCP servers at runtime through a standardized protocol, meaning you can add new tools to an MCP server and agents can use them immediately — no changes to the agent itself required.

### Where MCP servers are configured

MCP servers can be declared in two places:

- **`.mcp.json` at the repo root** — available to all agents and Copilot Chat (Agent Mode) in the repository
- **YAML frontmatter of an `.agent.md` file** — scoped to a specific custom agent

```jsonc
// .mcp.json
{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": { "Authorization": "Bearer ${GITHUB_TOKEN}" }
    },
    "internal-docs": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@company/docs-mcp-server"]
    }
  }
}
```

```yaml
# .github/agents/my-agent.agent.md

name: Security Reviewer
description: Reviews PRs for security vulnerabilities
tools: [read, web-search]
mcp-servers:
  snyk:
    type: http
    url: "https://mcp.snyk.io/sse"
    headers:
      Authorization: "Bearer ${SNYK_TOKEN}"

You are a security expert. When reviewing code, check for...
```

### MCP in the broader Copilot ecosystem

MCP is the connective tissue between Copilot and the rest of your toolchain:

| Surface | How MCP is used |
| --- | --- |
| **Copilot Chat (Agent Mode)** | Connects to MCP servers via `.mcp.json` for real-time tool access in the IDE |
| **Copilot Coding Agent** | Uses MCP servers declared in `.agent.md` or `.mcp.json` during async task execution |
| **Agentic Workflows (`gh-aw`)** | Uses the MCP Gateway (`gh-aw-mcpg`) to route tool calls through a centralized, audited gateway |
| **Copilot CLI** | Can connect to MCP servers for terminal-based agentic workflows |

> **Best used for:** Connecting Copilot to systems it doesn't natively know about — internal APIs, company databases, ticketing systems (Jira, Linear), observability platforms, security scanners, CI/CD pipelines, or any external tool your team uses. If you find yourself copy-pasting output from another tool into Copilot Chat, an MCP server is probably the right answer.


## ⚡ Agentic Workflows

**Agentic Workflows** are automation pipelines powered by AI — but unlike traditional CI/CD YAML workflows, they're written in **plain Markdown**. You describe your *intent* in natural language, and Copilot interprets and executes the steps.

Key characteristics:
- **Adaptive** — can make decisions based on context, not just predefined rules
- **Triggered** by events (PRs opened, schedules, labels applied, comments, etc.)
- **Secure** — run with read-only permissions by default; all write operations go through sanitized `safe-outputs` handlers
- **Accessible** — no deep scripting knowledge required; if you can write a README, you can write a workflow
- **Multi-engine** — workflows can target Copilot, Claude, Codex, or Gemini as the AI backend

Example: A Markdown-defined workflow that says *"When a new issue is labeled `bug`, analyze the codebase, identify the most likely root cause, and open a draft PR with a proposed fix."*

### 🛠️ Powered by `github/gh-aw`

The official implementation of GitHub Agentic Workflows lives in [**`github/gh-aw`**](https://github.com/github/gh-aw) — a Go-based `gh` CLI extension that is the engine behind the whole system.

**Workflows are Markdown files**, not YAML. A workflow lives at `.github/workflows/my-workflow.md` and uses a YAML frontmatter header to define triggers, permissions, and safe output types — then the body is plain natural language instructions for the AI:

```markdown
---
on:
  issues:
    types: [opened]
permissions:
  issues: read
timeout-minutes: 10
safe-outputs:
  create-issue:
  add-comment:
---

# Triage New Issues

When a new issue is opened, review it for completeness. If the issue is missing
reproduction steps or environment details, post a comment asking the reporter to
provide them. If it looks like a duplicate of an existing issue, link to the original.
```

**`gh aw compile`** then compiles this Markdown file into a standard GitHub Actions `.lock.yml` file that can actually run. You write in natural language; the compiler handles the Actions plumbing.

**Key `gh-aw` concepts:**

| Concept | Description |
| --- | --- |
| **`safe-outputs`** | The secure boundary for all write operations — create issues, PRs, comments, labels, project updates, etc. The AI proposes; the framework sanitizes and executes. |
| **`engine:`** | Which AI backend to use: `copilot` (default), `claude`, `codex`, or `gemini` |
| **`network:`** | Explicit allowlist/blocklist of domains the agent can reach during execution |
| **`imports:`** | Reuse shared workflow fragments across multiple workflows (DRY principle for automation) |
| **Agent Workflow Firewall (AWF)** | Companion project [`gh-aw-firewall`](https://github.com/github/gh-aw-firewall) for network egress control and activity logging |
| **MCP Gateway** | [`gh-aw-mcpg`](https://github.com/github/gh-aw-mcpg) routes Model Context Protocol calls through a unified, audited gateway |

**Getting started:**

```bash
# Install the gh extension
gh extension install github/gh-aw

# Create your first workflow (guided)
gh aw create

# Compile workflows to GitHub Actions YAML
gh aw compile

# Check status of all agentic workflows in the repo
gh aw status
```

> **Best used for:** Automating repetitive or adaptive repo tasks — triage, code review prep, dependency updates, test generation, release notes — where you want the AI to take action on events without waiting for a human prompt. `gh-aw` is the tool that makes this real in a GitHub repository today.


## 🖥️ Copilot CLI

**GitHub Copilot CLI** brings Copilot to the terminal. Run `copilot` to start its interactive terminal UI, then use natural-language prompts to ask questions, inspect code, make changes, run tools, and work through multi-step tasks with approval controls.

Install with `npm install -g @github/copilot`. Homebrew, WinGet, and the install script are also supported.

Representative commands:
- `copilot` — start an interactive session
- `copilot -p "..."` or `copilot --prompt="..."` — run a one-shot prompt programmatically
- `copilot --plan` or `copilot --mode plan` — start in plan mode
- `copilot --autopilot` — continue autonomously; review trust and approval settings before using it
- `copilot help [TOPIC]` — show command-line help
- `copilot update` — update the CLI
- `copilot completion SHELL` — generate a shell completion script

Useful interactive commands include `/login`, `/help`, `/plan`, `/model`, `/review`, `/pr`, `/mcp`, `/skills`, `/init`, and `/feedback`.

What makes it *agentic*:
- It supports **interactive** sessions and **programmatic** prompt execution
- It can plan multi-step work in **plan mode** and continue with **autopilot**
- It can use tools, MCP servers, skills, and custom agents
- It uses repository context to understand and act on your project
- It provides explicit permission, approval, and trust controls for actions

> **Best used for:** Developers who prefer the terminal, server/container environments without a GUI, scripting tasks, quick explanations of unfamiliar commands, and terminal-driven automation.

Official references: [About Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli) · [Using Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli) · [CLI command reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference)

## 🧩 Skills

**Skills** are modular, reusable packages of instructions and scripts that teach agents *how to do specific things*. They're the building blocks that extend what an agent knows how to do.

A skill lives in a folder with a `SKILL.md` file:
- **Project skills:** `.github/skills/<name>/SKILL.md` — available to everyone in the repo
- **User skills:** `~/.copilot/skills/` — personal, portable across projects
- **Org skills:** (coming) — shared across all repositories in an organization

Skills are agent-agnostic — they follow an open specification and can be used by any compatible AI tool. They're auto-discovered and loaded when an agent determines they're relevant to the current task.

> **Best used for:** Codifying repeatable expertise — *"how to write a migration," "how to add an API endpoint," "how to create a changelog entry"* — so agents and teammates can apply consistent patterns every time.


## 📋 Copilot Instructions

**Copilot Instructions** are *always-on* customizations that shape how Copilot behaves in every session within a repository. They're plain Markdown files that define coding standards, team conventions, project context, and style guidelines.

Two scopes:
- **`.github/copilot-instructions.md`** — applies globally to all Copilot interactions in the repo
- **`.github/instructions/*.instructions.md`** — targeted to specific file types or patterns (e.g., only applies to `*.ts` files)

Instructions are passive — they don't trigger automation. They're context Copilot *always carries* when helping in this repo, whether you're in Chat, using the Coding Agent, or running the CLI.

> **Best used for:** Enforcing team standards (naming conventions, test patterns, framework choices), providing onboarding context, defining architectural constraints, and ensuring consistent code quality across all AI-assisted work.


## 🎫 How GitHub Issues Fit In

Issues are the **entry point** for the Copilot Coding Agent's agentic workflow. They serve as structured "work orders" that both humans *and* agents can consume.

**The lifecycle:**

1. **Create a well-written Issue** — describe the problem, acceptance criteria, and any relevant context
2. **Assign to `@copilot`** — on GitHub.com, in VS Code, or via the Agents panel
3. **Copilot analyzes and plans** — reads the issue, reviews the codebase, forms a plan
4. **Agent executes on a new branch** — writes code, runs tests, fixes linting errors
5. **Draft PR is opened** — you see the agent's work and a session log of decisions made
6. **Iterate via PR comments** — leave feedback on the PR; the agent responds with new commits
7. **You review and merge** — Copilot cannot approve or merge its own PRs; humans stay in control

**Tips for great issues:**
- Be specific and scoped — *"Fix null pointer in `UserService.getById()`"* beats *"fix the user bug"*
- Include acceptance criteria — the agent uses these as a checklist
- Add screenshots, error messages, or logs — Copilot reads all of it
- Once a PR is open, **give feedback on the PR** (not the original issue)

> **Best used for:** Delegating well-defined, bounded tasks to the Coding Agent — feature additions, bug fixes, refactoring, test coverage, documentation.

## 🗺️ When to Use Which

| Situation | Use This |
| --- | --- |
| You want Copilot to follow your team's coding style in every session | **Copilot Instructions** |
| You want to teach Copilot a repeatable task your team does often | **Skills** |
| You want to chat, debug, explore, or prototype interactively | **Copilot Chat** |
| You want Copilot to autonomously work through a task step-by-step in your IDE | **Copilot Chat – Agent Mode** |
| You have a clearly scoped task and want Copilot to just do it asynchronously | **Issues → Coding Agent** |
| You want to automate a repo process on events (triage, reviews, releases) | **Agentic Workflows (`gh-aw`)** |
| You need a specialized AI assistant for a specific role (security, docs, etc.) | **Custom Agent** |
| You want to connect Copilot to an external tool, API, or data source | **MCP Server** |
| You're in the terminal and need command help or terminal-based automation | **Copilot CLI** |


## 🔗 How It All Connects

```
Instructions          (always-on context for every session)
    ↓
Skills                (modular expertise loaded on demand)
    ↓
MCP Servers           (external tools, APIs, and data sources plugged in at runtime)
    ↓
Agents                (specialized personas that use instructions + skills + MCP)
    ↓
┌─────────────────────────────────────────────────┐
│  Copilot Chat          │  Copilot Coding Agent  │
│  (real-time, with you) │  (async, for you)      │
│  IDE / GitHub.com      │  GitHub.com / Cloud    │
└─────────────────────────────────────────────────┘
    ↓                            ↑
Copilot CLI           Issues (the work order)
(terminal access to   (trigger for the Coding Agent)
 all of the above)
    ↓
Agentic Workflows (gh-aw)
(event-driven automation — Markdown workflows compiled
to GitHub Actions, with safe-outputs, network guardrails,
MCP Gateway, and multi-engine AI support)
```

The whole system is designed around a simple idea: **you describe what you want, Copilot figures out how to do it, and you stay in control of what ships.**

- Use **Chat** when you want to *work alongside* Copilot in the moment
- Use the **Coding Agent** when you want to *delegate and review* asynchronously
- Use **`gh-aw` Workflows** when you want it to *happen automatically* based on repo events
- Use **MCP Servers** to connect Copilot to tools and data outside of GitHub
- Use the **CLI** when you live in the terminal
- Use **Instructions + Skills** to make all of the above more consistent, context-aware, and reusable

Used together, these tools let you choose the right level of AI assistance for each task, from hands-on collaboration to fully automated execution.
