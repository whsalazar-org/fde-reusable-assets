# Using Copilot to Accelerate Modeling in BigQuery

## Title

### Modeling artifacts, field mappings, and typed schemas

This presentation-style asset complements the BigQuery modeling guide with workshop flow, exercises, prompts, and completion criteria.

## Objectives

Participants should learn how to use Copilot to accelerate BigQuery modeling while preserving human control over design, governance, and validation.

By the end, participants should be able to:

- Define a modeling contract.
- Generate source-to-target field mappings.
- Produce typed BigQuery schemas.
- Draft SQL models and tests.
- Review security, cost, and operational considerations.
- Prepare a pull request with clear validation evidence.

## Working principle

### Copilot accelerates execution; people govern decisions

Copilot can draft mappings, schemas, SQL, tests, and documentation. Humans remain responsible for business meaning, data classification, access control, cost tradeoffs, and production approval.

## Artifacts that can be accelerated

- Modeling contracts.
- Source-to-target mappings.
- Typed schemas.
- SQL models.
- Unit or data quality tests.
- Documentation.
- Pull-request summaries.

## Additional artifacts

- Reconciliation queries.
- Incremental load plans.
- Partitioning and clustering recommendations.
- Cost and performance review notes.
- Data lineage documentation.
- Governance and access-control checklists.

## Recommended structure

A repository or modeling workspace can include:

```text
/models
  /staging
  /marts
/mappings
/schemas
/tests
/docs
/prompts
```

Keep prompts and generated artifacts reviewable through version control.

## Workflow

1. Establish the modeling contract.
2. Generate the source-to-target mapping.
3. Produce the typed schema.
4. Draft the SQL model.
5. Add validation tests.
6. Review cost, security, and operations.
7. Reconcile outputs and submit a pull request.

## Modeling contract: minimum information

A modeling contract should include:

- Business purpose.
- Source systems and tables.
- Target dataset and table.
- Grain.
- Primary keys and uniqueness expectations.
- Required fields and definitions.
- Freshness expectations.
- Partitioning and clustering requirements.
- Data classification.
- Access-control expectations.

## Modeling contract: operations and governance

Also capture:

- Owner and reviewers.
- Backfill strategy.
- Incremental update strategy.
- Reconciliation approach.
- Retention requirements.
- Lineage and documentation expectations.
- Cost constraints.

## Security during prompting

Do not include secrets, credentials, customer personal data, or proprietary confidential data in prompts. Use representative synthetic examples where possible. Review generated SQL for accidental exposure of sensitive columns.

## Prompt: modeling contract

Ask Copilot to create or review a modeling contract using the available source metadata, target requirements, governance constraints, and validation criteria. Require it to list assumptions and open questions.

## Source-to-target mapping

A mapping should define:

- Source field.
- Target field.
- Type conversion.
- Transformation rule.
- Nullability.
- Default behavior.
- Data quality rule.
- Notes and assumptions.

## Mapping: traceability and quality

Mappings should be traceable from source to target and should include explicit quality checks for required fields, uniqueness, referential integrity, accepted values, and freshness.

## Example mapping

| Source field | Target field | Type | Rule | Quality check |
| --- | --- | --- | --- | --- |
| `customer_id` | `customer_id` | STRING | Preserve source identifier | Not null, unique within grain |
| `created_at` | `created_timestamp` | TIMESTAMP | Parse as UTC timestamp | Not null |
| `status` | `customer_status` | STRING | Map to approved status values | Accepted values check |

## Typed schema

A typed schema should include field names, BigQuery types, nullability, descriptions, policy tags where applicable, and ownership notes.

## BigQuery types

Common types include `STRING`, `INT64`, `NUMERIC`, `FLOAT64`, `BOOL`, `DATE`, `DATETIME`, `TIMESTAMP`, `GEOGRAPHY`, `STRUCT`, and `ARRAY`.

Ask Copilot to justify type choices when the source type is ambiguous.

## Typed model for consumers

Consumer-facing models should provide stable semantics, clear descriptions, and predictable grain. Avoid exposing raw operational complexity unless the consumer requires it.

## SQL model: core rules

Generated SQL should:

- Preserve the agreed grain.
- Use explicit column lists.
- Apply documented transformations.
- Avoid ambiguous joins.
- Handle nullability intentionally.
- Include comments only where they clarify non-obvious logic.

## SQL model: behavior and cost

Review generated SQL for:

- Partition pruning.
- Clustering opportunities.
- Join cardinality.
- Repeated scans of large tables.
- Incremental processing opportunities.
- Materialization strategy.

## Incremental models

Incremental models should define:

- Watermark field.
- Late-arriving data behavior.
- Reprocessing window.
- Idempotency strategy.
- Backfill approach.
- Reconciliation checks.

## Design review

Use Copilot to prepare a design review checklist, but require human approval for grain, business definitions, sensitive fields, access controls, and cost tradeoffs.

## Cost and operations review

Review query plan, table size, scan estimates, partitioning, clustering, schedule, retry behavior, alerting, and ownership.

## Validation cycle

1. Validate schema and syntax.
2. Run sample transformations.
3. Compare row counts.
4. Validate uniqueness and required fields.
5. Reconcile aggregates.
6. Review sensitive columns.
7. Capture evidence in the pull request.

## Review prompts

Ask Copilot to review the mapping, schema, SQL, and tests for inconsistencies, missing validation, cost concerns, and security issues. Require concrete findings rather than generic advice.

## Safe validation prompt

Ask Copilot to validate artifacts using synthetic or anonymized examples. Do not paste sensitive production data into the prompt.

## Exercise: modeling contract

### Objective

Create a modeling contract from a fictional source description and target business requirement.

### Fictional data

Use synthetic customer, order, and product examples. Do not use real customer data.

### Time: 10 minutes

Participants draft the contract and list assumptions.

## Exercise: modeling contract activity

- Identify grain.
- Identify keys.
- Define required fields.
- Classify sensitive data.
- List validation rules.
- Capture open questions.

## Exercise: source-to-target mapping

### Activity: 12 minutes

Create a mapping table from synthetic source fields to the target model.

## Exercise: mapping prompt

Ask Copilot to produce a mapping table with transformation rules, null handling, and quality checks. Require it to highlight assumptions.

## Exercise: schema and typed model

### Activity: 12 minutes

Generate a BigQuery schema and review types, descriptions, and nullability.

## Exercise: schema prompt

Ask Copilot to propose BigQuery field definitions from the mapping and contract. Require policy-tag suggestions for sensitive fields.

## Exercise: SQL model and tests

### Activity: 15 minutes

Draft SQL and tests for the synthetic model.

## Exercise: SQL prompt

Ask Copilot to generate SQL that follows the mapping and preserves the grain. Require tests for uniqueness, required fields, accepted values, and reconciliation.

## Exercise: security and costs

### Activity: 10 minutes

Review the generated design for access control, sensitive data exposure, partitioning, clustering, and query cost.

## Exercise: security prompt

Ask Copilot to identify security and cost risks in the proposed model and recommend specific changes.

## Exercise: final reconciliation

### Activity: 10 minutes

Compare expected outputs with generated model outputs and capture validation evidence.

## Reconciliation prompt

Ask Copilot to propose reconciliation queries for row counts, key uniqueness, aggregate totals, freshness, and accepted values.

## Security and governance

### Mandatory human review

Human reviewers must approve:

- Business definitions.
- Sensitive field handling.
- Access-control model.
- Cost and performance tradeoffs.
- Production deployment.

## Access controls in BigQuery

Review dataset permissions, table permissions, authorized views, row-level access policies, column policy tags, service accounts, and audit logs.

## Completion criteria

The work is complete when:

- Contract is approved.
- Mapping is reviewed.
- Schema is typed and documented.
- SQL model passes validation.
- Tests cover core quality rules.
- Security and cost review is complete.
- Pull request includes evidence.

## Completion criteria: security and operations

- Sensitive data is classified.
- Access controls are documented.
- Partitioning and clustering are justified.
- Monitoring and ownership are defined.
- Backfill and rollback are understood.

## Pull-request template

```markdown
## BigQuery modeling change

### Artifacts

- Contract:
- Mapping:
- Schema:
- SQL model:
- Tests:

### Contract

- Grain:
- Source systems:
- Target model:
- Sensitive fields:
```

## Pull-request template: validation

```markdown
### Validation

- [ ] Schema validation passed
- [ ] SQL syntax validated
- [ ] Row counts reconciled
- [ ] Required fields tested
- [ ] Uniqueness tested
- [ ] Cost reviewed
- [ ] Access controls reviewed

### Copilot usage

Describe how Copilot was used and what human review was performed.
```

## Key messages

- Copilot accelerates modeling artifacts but does not own data meaning.
- Strong contracts and mappings improve generated output.
- Validation must cover quality, security, and cost.
- Human review is mandatory for production data models.

## Official sources

Use current Google BigQuery documentation and current GitHub Copilot documentation when adapting this asset for a specific engagement.
