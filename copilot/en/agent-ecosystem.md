# Agent, MCP, and Custom Agent Ecosystem

This document defines a practical structure for using AI agents, Model Context Protocol (MCP) servers, and custom agents in a software repository. The goal is to keep automation useful, secure, observable, and easy to maintain.

## 1. Core concepts

| Component | Responsibility | Recommended scope |
| --- | --- | --- |
| **Primary agent** | Understands the request, plans work, delegates when useful, and owns the final result. | Repository or workspace |
| **Custom agent** | Provides focused expertise, rules, tools, and a bounded workflow. | A feature, discipline, or recurring task |
| **Sub-agent** | Performs an isolated piece of work for a primary or custom agent. | One narrow task |
| **MCP host/client** | Connects an AI application to MCP servers and manages their lifecycle and permissions. | User or application runtime |
| **MCP server** | Exposes external tools and data through a standard protocol. | One integration or domain |
| **Skill or instruction set** | Reusable procedural guidance, such as testing or release preparation. | Cross-agent workflow |

MCP uses a host-client-server architecture. The host coordinates one or more clients, and each client connects to an MCP server with explicit capabilities and boundaries. See the [MCP architecture specification](https://modelcontextprotocol.io/specification/2025-06-18/architecture).

## 2. Recommended repository layout

```text
.github/
├── agents/
│   ├── frontend-specialist.agent.md
│   ├── test-specialist.agent.md
│   ├── security-reviewer.agent.md
│   └── documentation-specialist.agent.md
├── copilot-instructions.md
└── workflows/
    └── agent-validation.yml

docs/
└── agent-ecosystem.md

mcp/
├── README.md
├── catalog/
│   ├── github.json
│   ├── browser.json
│   └── observability.json
└── policies/
    └── approved-tools.md
```

Use `.github/agents/` for repository-level custom agents. Keep agent profiles short, task-specific, and versioned with the code they support. GitHub documents repository-level profiles in [About custom agents](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents).

When adopting this layout, keep custom agent profiles, the MCP catalog, and the approved-tools policy versioned together. Add only the profiles and integrations that the repository actually uses.

## 3. Layered architecture

### Layer 1: Shared repository instructions

`.github/copilot-instructions.md` should contain rules that apply to every agent:

- Project purpose and architecture.
- React and JavaScript conventions.
- Testing and validation commands.
- Security and privacy requirements.
- Naming, documentation, and review expectations.
- Explicitly prohibited actions, such as committing secrets or changing unrelated files.

Keep these instructions stable and avoid putting specialist workflows here.

### Layer 2: Custom agents

Each custom agent should own one clear responsibility. A good agent profile includes:

1. **Name and description** — what the agent does and when to use it.
2. **Boundaries** — files, systems, and tasks it may change.
3. **Workflow** — inspect, plan, implement, test, and report.
4. **Allowed tools** — only the tools required for its job.
5. **Quality gates** — tests, linting, security checks, or review criteria.
6. **Output contract** — a predictable summary of changes, validation, and risks.

Suggested division of responsibility:

- `frontend-specialist`: React components, accessibility, browser behavior, and UI tests.
- `test-specialist`: Test design, test execution, fixtures, and coverage gaps.
- `security-reviewer`: Dependency, secret, input-validation, and authorization review.
- `documentation-specialist`: Markdown, API usage, architecture notes, and examples.
- `release-reviewer`: Change scope, versioning, changelog, and deployment readiness.

Custom agents can be used as isolated sub-agents, allowing the primary agent to delegate focused work without overcrowding its context. See [Custom agents and sub-agent orchestration](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/custom-agents).

### Layer 3: MCP servers

Treat MCP servers as capability providers, not autonomous decision-makers. Each server should have:

- A single domain and a clear owner.
- A documented purpose and data classification.
- Explicitly allowlisted tools.
- Read-only tools enabled by default where possible.
- Authentication through environment variables or managed secrets.
- Timeouts, rate limits, and failure behavior.
- Audit and monitoring guidance.
- A version and compatibility policy.

Examples:

| Server | Purpose | Default access |
| --- | --- | --- |
| GitHub | Issues, pull requests, repository metadata | Read-only; write operations require a deliberate workflow |
| Browser or Playwright | UI inspection and browser tests | Test environments only |
| Observability | Logs, traces, and metrics | Read-only, redacted data |
| Design system | Component guidance and tokens | Read-only |
| Database | Schema inspection or test data | Never production write access |

For GitHub Copilot repository configurations, MCP tools may run autonomously. Therefore, prefer specific read-only tool allowlists and do not expose broad write access by default. See [Configure MCP servers for your repository](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers).

## 4. Delegation model

Use this decision flow:

1. The primary agent classifies the request.
2. If the task is simple and local, the primary agent handles it directly.
3. If the task requires specialist knowledge, delegate to one custom agent.
4. If the task requires external data or actions, use the smallest MCP capability that satisfies it.
5. If multiple independent investigations are needed, run sub-agents in parallel.
6. The primary agent reviews all results, resolves conflicts, runs final validation, and owns the response or pull request.

Avoid chains longer than necessary. A useful default is:

```text
User request
    ↓
Primary agent
    ├── Custom agent: implementation
    ├── Custom agent: tests
    └── Custom agent: security review
             ↓
      MCP servers used only where required
             ↓
Primary agent validates and reports
```

## 5. Security and governance

- Never hardcode API keys, tokens, passwords, connection strings, or personal data.
- Use repository, organization, or environment-managed secrets.
- Grant the minimum MCP tools and permissions required.
- Separate development, test, staging, and production integrations.
- Prefer synthetic or redacted data for agent workflows.
- Require human review for destructive actions, production changes, migrations, and external communications.
- Pin or constrain dependency and server versions where practical.
- Record the owner, purpose, data access, and approval status of every MCP server.
- Review agent profiles and MCP configurations like application code.

GitHub supports organization and enterprise MCP policies and registries for curating approved servers and restricting access. See [MCP server usage in your company](https://docs.github.com/en/copilot/concepts/mcp-management).

## 6. Agent profile template

```markdown
---
name: frontend-specialist
description: Builds and reviews small, accessible React features.
tools:
  - read
  - search
  - edit
  - test
---

You are the frontend specialist for this repository.

## Scope
- Modify React and JavaScript files required by the request.
- Keep components small, functional, and focused.
- Do not change backend, deployment, or unrelated configuration files.

## Workflow
1. Inspect the existing component and test patterns.
2. State a short implementation plan.
3. Make the smallest complete change.
4. Add or update tests when infrastructure exists.
5. Run the relevant checks.
6. Report files changed, checks run, and remaining risks.

## Quality rules
- Preserve accessibility and keyboard behavior.
- Avoid unnecessary dependencies.
- Never add secrets or environment-specific credentials.
```

The exact frontmatter supported by GitHub surfaces can differ, so validate profiles against the target environment's documentation. See [Custom agents configuration](https://docs.github.com/en/enterprise-cloud@latest/copilot/reference/custom-agents-configuration).

## 7. MCP registration checklist

Before approving an MCP server, confirm:

- [ ] Owner and business purpose are documented.
- [ ] Server source and version are known.
- [ ] Tools are explicitly allowlisted.
- [ ] Read-only access is the default.
- [ ] Secrets are referenced through managed variables, never committed.
- [ ] Sensitive fields are redacted from responses and logs.
- [ ] Network access and data residency are understood.
- [ ] Failure, timeout, and retry behavior are documented.
- [ ] A test or sandbox environment exists.
- [ ] A review date and decommissioning process are defined.

## 8. Pull request and maintenance standards

Every agent-authored change should include:

- A focused pull request title and description.
- Tests or a clear explanation when tests are not applicable.
- A list of MCP servers and write-capable tools used.
- Security and privacy considerations.
- Any follow-up work or known limitations.

Review quarterly, or sooner when a tool, provider, permission, or data classification changes. Delete unused agents and MCP servers rather than allowing capability sprawl.

## References

- [Model Context Protocol architecture](https://modelcontextprotocol.io/specification/2025-06-18/architecture)
- [About GitHub custom agents](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents)
- [Custom agents and sub-agent orchestration](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/custom-agents)
- [Configure MCP servers for a repository](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)
- [MCP server governance](https://docs.github.com/en/copilot/concepts/mcp-management)
