---
marp: true
theme: default
paginate: true
title: Secure Development Patterns with GitHub Copilot
---

# Secure Development Patterns with GitHub Copilot

**From autocomplete to governed autonomy**

<!-- Speaker notes: Frame the session. The question is no longer "should we use Copilot?" but "how do we make AI-assisted delivery auditable, least-privileged, and reviewable with the same standard we expect from human contributors?" -->

---

## The core premise

> Secure development with GitHub Copilot requires combining **AI guidance** with **automated tests**, **guardrails**, and **rigorous code review**.

Copilot is optimized for functionality rather than security. It may reproduce insecure patterns present in training data or in the local repository context.

Teams should implement:

- Defense-in-depth design patterns.
- Environment guardrails.
- Verification loops.

---

## Agenda

1. The shift: assistant to agent to autonomous workflow.
2. Threat model for AI-assisted development.
3. **Guardrail and prompt-engineering patterns**.
4. **Early validation patterns (shift left)**.
5. **CI/CD integration and automation patterns**.
6. **Human-in-the-loop review patterns**.
7. Governed autonomy: agents, skills, and MCP.
8. Adoption roadmap and metrics.

---

## The shift in developer workflow

Copilot usage has evolved through three modes:

1. **Assistant**: suggests code, tests, and explanations inside the IDE.
2. **Agent**: performs scoped tasks across files or repositories.
3. **Workflow participant**: creates pull requests, summarizes changes, invokes tools, and supports validation.

The security model must evolve with the workflow. The more autonomy a tool receives, the more explicit its guardrails, permissions, validation, and auditability must be.

---

## Threat model for AI-assisted development

Primary risks include:

- Insecure generated code.
- Hallucinated APIs, dependencies, or configuration.
- Use of deprecated or vulnerable packages.
- Secrets or sensitive information in prompts or generated files.
- Prompt injection through untrusted repository content, issues, logs, or documents.
- Over-permissioned agents and MCP servers.
- Reviewers over-trusting generated output.

Treat AI-generated output as untrusted until it has passed the same review and validation expected for human-authored code.

---

## The four pattern families

1. Guardrails and prompt engineering.
2. Early validation and shift-left controls.
3. CI/CD integration and automation.
4. Human-in-the-loop review.

These families work together. Prompt guidance without validation is weak. Validation without review misses design and context. Review without automation does not scale.

---

# 1. Guardrail and Prompt-Engineering Patterns

---

## Explicit security context

Prompts and instructions should state security requirements directly:

- Validate and sanitize untrusted input.
- Use parameterized queries.
- Avoid logging secrets or sensitive data.
- Preserve authentication and authorization checks.
- Follow repository dependency policy.
- Include tests for abuse and failure cases.

Security requirements should be specific to the repository and technology stack.

---

## System-level instruction sets

Use repository or organization instructions to establish persistent rules for Copilot and agents:

- Coding conventions.
- Secure design expectations.
- Required validation commands.
- Dependency constraints.
- Data-handling rules.
- Pull-request checklist.

Version these instructions and review changes like code.

---

## Do not suggest

Document patterns Copilot should not introduce, such as:

- Hard-coded credentials.
- Disabling TLS verification.
- Broad exception swallowing.
- Unsafe deserialization.
- Dynamic SQL concatenation.
- Overly permissive CORS.
- Authentication bypasses for testing.

Negative guidance is useful when the repository has legacy patterns that should not be copied.

---

## Context exclusion

Exclude or avoid providing sensitive context where possible:

- Secrets and credentials.
- Customer data.
- Proprietary confidential documents.
- Regulated data.
- Production incident records with sensitive details.

Use repository controls, prompt hygiene, and developer training to avoid unnecessary exposure.

---

## "Zero secrets" prompts

Ask Copilot to assume secrets must come from secure runtime configuration rather than source code.

Recommended prompt intent:

- Do not generate real credentials.
- Use placeholders only where necessary.
- Read secrets from approved secret stores or environment variables.
- Add tests that verify secrets are not logged.

---

# 2. Early Validation Patterns (Shift Left)

---

## Threat-modeling prompts in chat

Use Copilot to help identify threats before implementation:

- What inputs are untrusted?
- What privileges does this code require?
- What data is sensitive?
- What failures could expose data or bypass authorization?
- What tests should prove the control works?

Review the output with engineers who understand the system.

---

## Hardened development containers

Use dev containers or standardized environments to provide:

- Approved tool versions.
- Required scanners.
- Least-privilege local credentials.
- Reproducible build and test commands.
- Consistent dependency behavior.

A hardened environment reduces the chance that Copilot-generated instructions depend on unsafe local setup.

---

## Pre-commit hooks

Pre-commit hooks can catch issues before code reaches the pull request:

- Secret scanning.
- Formatting and linting.
- Static checks.
- Dependency policy checks.
- Generated file restrictions.

Do not rely only on local hooks; mirror critical checks in CI.

---

# 3. CI/CD Integration and Automation Patterns

---

## Alignment with GitHub Advanced Security

Use GitHub Advanced Security capabilities where available:

- Code scanning.
- Secret scanning.
- Push protection.
- Dependency review.
- Dependabot alerts and updates.

Copilot can help remediate findings, but the remediation must still be reviewed and tested.

---

## Pipelines with Copilot Autofix

Autofix can propose changes for security findings. Recommended controls:

- Require human review.
- Require tests for the affected behavior.
- Validate that the fix addresses the root cause.
- Avoid accepting changes that only silence the scanner.

---

## Secret scanning and push protection

Enable secret scanning and push protection for repositories that use Copilot-assisted delivery. Teach developers that generated examples must not contain real credentials.

If a secret is detected, rotate it according to incident response policy rather than simply deleting it from the branch.

---

## Supply-chain automation

Generated code may introduce dependencies. Require validation of:

- Package reputation and maintenance.
- License compatibility.
- Known vulnerabilities.
- Lockfile changes.
- Transitive dependency risk.

Use dependency review and package approval processes for new dependencies.

---

# 4. Human-in-the-Loop Review Patterns

---

## Equal peer-code-review standard

Review AI-assisted changes with the same or higher standard as human-authored changes. Reviewers should ask:

- Is the design appropriate?
- Are security checks preserved?
- Are tests meaningful?
- Are dependencies acceptable?
- Is error handling safe?
- Is sensitive data protected?

---

## Package validation checklists

When Copilot suggests a new package, validate:

- Why the package is needed.
- Whether an existing dependency already solves the problem.
- Maintenance status.
- Vulnerability history.
- License.
- Transitive dependencies.
- Runtime permissions and network behavior.

---

## Handling untrusted content

Issues, pull-request comments, logs, generated files, documentation, and external data can contain prompt-injection attempts. Agents should not blindly follow instructions found in untrusted content.

Require agents and reviewers to distinguish between task instructions and data being processed.

---

# Governed Autonomy

---

## Least-privilege specialist agents

Custom agents should have:

- A narrow role.
- Explicit allowed actions.
- Minimal permissions.
- Required validation steps.
- Escalation rules for uncertainty.
- Clear ownership.

Do not create broad agents that can change anything without review.

---

## Reusable skills as guardrails

Skills can encode approved workflows for sensitive tasks, such as:

- Secure API implementation.
- Dependency updates.
- Threat modeling.
- Documentation review.
- Data migration validation.

Reusable skills reduce ad hoc prompting and improve consistency.

---

## MCP catalog and approval gates

MCP servers extend what agents can access and do. Each MCP server should have:

- Business purpose.
- Owner.
- Data classification.
- Allowed operations.
- Authentication method.
- Logging and monitoring.
- Approval status.
- Review cadence.

---

## Lifecycle of an MCP integration

1. Request and use-case description.
2. Risk assessment.
3. Permission design.
4. Pilot in a limited environment.
5. Observability and audit validation.
6. Approval for broader use.
7. Periodic review and retirement when no longer needed.

---

## Auditability: the paper trail

For AI-assisted changes, retain evidence of:

- The task or issue.
- The pull request.
- Validation results.
- Reviews and approvals.
- Agent or automation used where available.
- Security findings and remediation decisions.

Auditability supports learning, compliance, and incident response.

---

## Adoption roadmap

1. Publish secure usage guidelines.
2. Configure repository instructions and pull-request templates.
3. Enable security scanning and required checks.
4. Pilot scoped agent workflows.
5. Create reusable skills and custom agents.
6. Approve MCP servers through a formal process.
7. Measure outcomes and expand gradually.

---

## Metrics that matter

Measure:

- Security findings introduced and remediated.
- Review defects found before merge.
- Secret scanning events.
- Dependency risk introduced by AI-assisted changes.
- Test coverage and meaningful test additions.
- Pull-request rework rate.
- Developer confidence and friction.

Avoid treating generated lines of code as a security or quality metric.

---

## Key takeaways

- Copilot accelerates secure development only when paired with guardrails and validation.
- AI-generated code must be reviewed as untrusted output.
- Repository instructions, reusable skills, custom agents, and MCP servers should be governed assets.
- Least privilege, traceability, and human accountability remain essential.

---

# Questions
