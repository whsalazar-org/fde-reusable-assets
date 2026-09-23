# Best Practices for Using GitHub Copilot in Application Modernization and Migration

This asset consolidates Spanish modernization guidance into an English best-practices guide for large modernization and migration programs.

## 1. Start with repository understanding

Modernization work succeeds when Copilot has enough context to reason about the existing system. Before asking for code changes, provide or create context about:

- Architecture and module boundaries.
- Runtime and framework versions.
- Build, test, and deployment commands.
- Data model and integration points.
- Known constraints, deprecated patterns, and target-state patterns.
- Non-functional requirements such as performance, security, reliability, and observability.

Use Copilot first for explanation, inventory, and planning. Ask it to identify dependencies, risky areas, coupling, tests, and migration seams before implementation begins.

## 2. Create strong Copilot instructions

Repository instructions should describe how work is expected to be done in that repository. Include:

- The target modernization approach.
- Coding conventions and architectural boundaries.
- Test expectations and commands.
- Security and data-handling rules.
- Migration patterns to prefer and patterns to avoid.
- Pull-request expectations and validation requirements.

Keep instructions specific and maintained. Outdated instructions can produce inconsistent or unsafe changes.

## 3. Split modernization into small, reviewable tasks

Avoid asking Copilot or an agent to modernize an entire application in one request. Break the work into thin slices such as:

- Inventory one module.
- Add characterization tests.
- Replace one deprecated API usage pattern.
- Migrate one route, endpoint, job, or data access layer.
- Introduce an adapter or compatibility layer.
- Update one build or deployment step.

Small tasks are easier to review, test, roll back, and parallelize.

## 4. Use Copilot agents for incremental execution

Agents are useful when the task has clear boundaries and validation criteria. Good agent tasks include:

- Updating repetitive patterns across a bounded set of files.
- Generating tests for an identified component.
- Refactoring a module after the target design is defined.
- Preparing pull requests for well-scoped migration slices.
- Producing documentation for migrated behavior.

Do not delegate broad architectural decisions or production-impacting changes without human review.

## 5. Standardize data loading and migration patterns

For data modernization, define reusable patterns for:

- Source-to-target mappings.
- Type conversion rules.
- Nullability and default handling.
- Reconciliation checks.
- Backfill and incremental load behavior.
- Idempotency and retry behavior.
- Audit fields and lineage.

Ask Copilot to follow these patterns rather than inventing new ones for each table, model, or pipeline.

## 6. Use parallel validation to reduce migration risk

When replacing legacy behavior, validate the new implementation against the old one where possible. Useful techniques include:

- Characterization tests around current behavior.
- Golden files or known input-output fixtures.
- Dual-run comparison for batch or data workflows.
- Shadow traffic or read-only comparisons for services.
- Reconciliation dashboards for migrated data.

Copilot can help create the scaffolding, but humans should define what equivalence means.

## 7. Preserve business continuity

Modernization should not require a risky big-bang cutover. Prefer patterns such as:

- Strangler fig migration.
- Feature flags.
- Compatibility adapters.
- Incremental data backfills.
- Canary releases.
- Rollback plans.

Ask Copilot to preserve existing contracts unless the task explicitly changes them.

## 8. Use custom agents and skills for team-specific workflows

Create reusable skills or custom agents when teams repeatedly perform the same modernization workflow. Examples include:

- Framework version upgrades.
- API endpoint migration.
- Database object conversion.
- Test harness creation.
- Documentation conversion.
- Security hardening.

Each custom agent or skill should have a clear scope, required inputs, validation rules, and ownership.

## 9. Share MCP server configuration at repository level

If MCP servers are used, configure them through approved, reviewable repository or organization mechanisms where possible. Document:

- Purpose and owner.
- Permissions and data access.
- Authentication method.
- Allowed operations.
- Logging and monitoring.
- Approval status and review cadence.

Avoid undocumented local-only tool access for repeatable modernization workflows.

## 10. Combine IDE and CLI workflows

Use the IDE for interactive understanding, design discussion, and focused edits. Use the CLI or cloud agent for scoped tasks that can run with clear instructions and validation. Both workflows should follow the same repository standards and review requirements.

## 11. Create a shared modernization playbook

A playbook should describe:

- Target architecture and migration strategy.
- Standard task breakdown.
- Prompt patterns and reusable instructions.
- Testing and validation approach.
- Pull-request checklist.
- Rollback and operational considerations.
- Examples of accepted changes.

The playbook reduces variance across teams and helps new contributors use Copilot effectively.

## 12. Measure success beyond lines of code

Recommended metrics include:

- Cycle time for migration tasks.
- Defect rate and escaped defects.
- Test coverage and characterization coverage.
- Amount of legacy code retired.
- Rework or rollback rate.
- Review time and reviewer confidence.
- Operational incidents during migration.
- Developer satisfaction.

Avoid using generated lines of code as the primary success metric.

## Recommended modernization workflow

1. Inventory the target area and risks.
2. Define the migration slice and success criteria.
3. Add or improve tests around existing behavior.
4. Ask Copilot to propose a plan.
5. Implement the slice with Copilot or an agent.
6. Run automated validation.
7. Compare behavior with the legacy path.
8. Submit a small pull request with clear evidence.
9. Capture reusable patterns in the playbook.

## Key conclusion

GitHub Copilot is most effective in modernization programs when it operates inside a disciplined delivery system: strong context, small tasks, reusable patterns, validation against legacy behavior, and human accountability for design and production risk.
