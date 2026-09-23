# GitHub Enterprise: Advanced Rules and Automated Pull Requests with Copilot

## 1. Agenda

This presentation-style asset covers a governance model for GitHub Enterprise repositories that use Copilot-assisted delivery and automated pull requests.

Topics:

1. The challenge.
2. Technical governance model.
3. Rulesets and branch protections.
4. Organization and enterprise controls.
5. CODEOWNERS and accountability.
6. Pull-request templates.
7. Repository instructions for Copilot.
8. Pull-request automation with Copilot.
9. Automated review with Copilot.
10. Technical gates with GitHub Actions.
11. Separation of responsibilities.
12. Quality metrics.
13. Telemetry and traceability.
14. Security and secret protection.
15. Adoption strategy.
16. Target workflow.
17. Implementation checklist.

## 2. The challenge

Copilot and agentic workflows can increase delivery speed, but they also increase the need for repeatable controls. Enterprises need to ensure that generated or agent-authored changes are reviewed, tested, traceable, and compliant with repository standards.

Common risks include:

- Pull requests that bypass expected review paths.
- Generated code without sufficient tests.
- Unclear ownership for agent-authored changes.
- Inconsistent branch protection across repositories.
- Secrets or sensitive data included in prompts or generated content.
- Automation that can mutate production-relevant files without approval.

## 3. Technical governance model

A practical model combines:

- Repository rulesets.
- CODEOWNERS.
- Pull-request templates.
- Copilot repository instructions.
- GitHub Actions checks.
- GitHub Advanced Security.
- Audit logs and telemetry.
- Clear ownership for reusable agents, skills, and MCP servers.

Controls should be applied as close to the repository as possible while still allowing organization-level standardization.

## 4. Rulesets: advanced controls

Rulesets define the technical gates that protect important branches and workflows.

### Recommended rules for `main`

- Require pull requests before merging.
- Require at least one or more approving reviews.
- Require review from CODEOWNERS for owned paths.
- Require status checks to pass.
- Require branches to be up to date when appropriate.
- Block force pushes.
- Block deletions.
- Require signed commits if enterprise policy requires them.
- Restrict bypass permissions to a small, audited group.

## 5. Organization and enterprise rules

Organization and enterprise owners should define baseline policies for repositories that use Copilot and automation.

### Additional controls

- Required secret scanning and push protection.
- Required dependency scanning.
- Required code scanning for supported languages.
- Standard branch naming and pull-request rules.
- Required review for workflow files and security-sensitive paths.
- Restrictions on Actions permissions where needed.
- Approval for MCP servers and external integrations.

## 6. CODEOWNERS and technical accountability

CODEOWNERS connects file paths to accountable reviewers. It is especially important when agents or automation can generate changes across many areas.

### Example

```text
# CODEOWNERS
/.github/ @platform-team @security-team
/infrastructure/ @platform-team
/src/payments/ @payments-team
/src/auth/ @identity-team @security-team
/docs/ @developer-experience-team
```

### Recommendations

- Keep ownership aligned with real review responsibility.
- Protect security-sensitive paths with explicit owners.
- Review CODEOWNERS changes carefully.
- Avoid catch-all ownership that sends every review to the same team.

## 7. Pull-request template

A pull-request template should make AI-assisted delivery reviewable.

```markdown
## Description

Explain the change and why it is needed.

## Type of change

- [ ] Feature
- [ ] Bug fix
- [ ] Refactor
- [ ] Documentation
- [ ] AI-assisted or agent-authored change

## Validation

List tests, builds, linters, security scans, and manual checks performed.

## Risks and rollback

Describe the blast radius, known risks, and rollback approach.

## Checklist

- [ ] The change is small enough to review.
- [ ] Generated code was reviewed by a human.
- [ ] Sensitive data was not included in prompts or committed files.
- [ ] Required CODEOWNERS reviewed the change.
```

## 8. Repository instructions for Copilot

Repository instructions should tell Copilot how to work in the repository.

Recommended content:

- Architecture overview.
- Coding standards.
- Testing commands.
- Security expectations.
- Pull-request requirements.
- Documentation rules.
- Prohibited patterns.
- Required validation before completion.

Example structure:

```markdown
# Copilot Instructions

Follow existing architecture and naming conventions. Keep changes small and reviewable. Run relevant tests before proposing completion. Do not introduce secrets or credentials. Update documentation for user-facing behavior changes.
```

## 9. Pull-request automation with Copilot

Copilot can help create or update pull requests for scoped tasks. Automation should operate under clear constraints:

- The task must have a defined scope and expected output.
- Generated changes must be committed to a branch and reviewed.
- Required checks and reviews must not be bypassed.
- The pull-request body should disclose validation and risks.
- The author or requester remains accountable for the change.

### Recommended flow

1. Create a scoped issue or task.
2. Provide repository context and acceptance criteria.
3. Let Copilot or an agent produce changes in a branch.
4. Run automated checks.
5. Require human review and CODEOWNER approval.
6. Merge only after all required gates pass.

## 10. Automated review with Copilot

Automated review can improve consistency, but it should not replace accountable human review.

### Proposed configuration

- Use automated review for common quality, test, and security issues.
- Keep branch protection requirements independent from optional AI review where needed.
- Route high-risk paths to humans with domain expertise.
- Track accepted and rejected automated review findings.

### Considerations

- AI review can produce false positives and false negatives.
- Automated comments should be concise and actionable.
- Security-sensitive findings should be triaged by qualified reviewers.

## 11. Technical gates with GitHub Actions

GitHub Actions should enforce required validation.

Recommended checks:

- Build.
- Unit tests.
- Integration tests where feasible.
- Linting or formatting checks.
- Type checks.
- Code scanning.
- Dependency review.
- Secret scanning or push protection.
- Policy checks for workflow and infrastructure files.

### Minimum checks

At minimum, repositories should require build and test validation for production code and additional security checks for sensitive or externally exposed systems.

## 12. Separation of responsibilities

Separate responsibilities across:

- Developers and agent requesters.
- Reviewers and CODEOWNERS.
- Platform maintainers.
- Security reviewers.
- Repository administrators.
- CoE or enablement owners.

No automation should be both the author, reviewer, approver, and merger of a production-impacting change.

## 13. Quality metrics

### Technical indicators

- Build and test pass rate.
- Defects introduced by AI-assisted changes.
- Code scanning and dependency findings.
- Rework rate after review.
- Rollback rate.

### Process indicators

- Pull-request cycle time.
- Review latency.
- CODEOWNER participation.
- Percentage of changes with clear validation evidence.
- Reuse of approved instructions, prompts, and agents.

## 14. Telemetry and traceability

Track the relationship between issue, branch, pull request, author, agent or tool, checks, approvals, and deployment. Traceability helps teams understand whether automation improves flow without weakening governance.

Useful telemetry includes:

- Pull-request metadata.
- Check results.
- Review events.
- Security findings.
- Agent or automation identifiers where available.
- Audit log events.

## 15. Security and secret protection

Required practices:

- Never place secrets, credentials, customer data, or proprietary confidential information in prompts.
- Enable secret scanning and push protection.
- Review generated dependencies and external calls.
- Restrict permissions for workflows and agents.
- Require approval for MCP servers and integrations.
- Treat generated code as untrusted until reviewed and tested.

## 16. Adoption strategy

### Phase 1: Preparation

Define policies, repository standards, and required controls.

### Phase 2: Pilot

Apply the workflow to selected repositories and collect feedback.

### Phase 3: Enforcement

Turn recommended controls into required rules for repositories that meet rollout criteria.

### Phase 4: Optimization

Improve automation, metrics, and reusable assets based on evidence.

## 17. Target workflow

1. Work begins from an issue or approved task.
2. Copilot or a developer prepares a scoped branch.
3. Pull request uses the standard template.
4. Automated checks run.
5. CODEOWNERS and required reviewers approve.
6. Security-sensitive findings are resolved or explicitly accepted by policy owners.
7. Merge occurs only through the approved repository process.

## 18. Implementation checklist

- [ ] Define repository rulesets for protected branches.
- [ ] Configure CODEOWNERS for sensitive paths.
- [ ] Add pull-request template.
- [ ] Add repository Copilot instructions.
- [ ] Require build, test, and security checks.
- [ ] Enable secret scanning and push protection.
- [ ] Define AI-assisted change disclosure expectations.
- [ ] Establish MCP and custom agent approval process.
- [ ] Track metrics and review adoption regularly.

## 19. Final message

Copilot-enabled delivery scales safely when enterprise controls are implemented as normal engineering workflow: clear ownership, protected branches, required validation, human review, and traceable automation.
