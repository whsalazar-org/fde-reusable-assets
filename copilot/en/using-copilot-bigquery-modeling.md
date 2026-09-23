# Using Copilot to Accelerate BigQuery Modeling Artifacts, Field Mappings, and Typed Schemas

## Purpose

This guide describes a practical, review-first workflow for using GitHub Copilot to create BigQuery modeling artifacts faster while keeping business definitions, data quality, security, and deployment decisions under human control.

Copilot is most effective when it receives a clear goal, representative metadata, explicit constraints, and a small deliverable. GitHub recommends starting broadly, becoming specific, providing examples, breaking complex work into smaller tasks, and iterating on the result ([GitHub prompt engineering guidance](https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering)). Repository instructions can provide persistent project context such as structure, standards, build, and test expectations ([customizing Copilot for a project](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-copilot-overview)).

> **Important:** Treat generated SQL, mappings, schemas, and governance metadata as drafts. A data owner, analytics engineer, or security reviewer must validate them before deployment.

## What Copilot can accelerate

Copilot can help produce first drafts of:

- Conceptual and logical model notes.
- Source-to-target field mapping tables.
- BigQuery `CREATE TABLE` and `CREATE VIEW` statements.
- JSON schema files for `bq`, APIs, and ingestion tools.
- Typed application models, such as TypeScript interfaces or C# records.
- Data quality checks and validation queries.
- Partitioning, clustering, naming, and documentation suggestions.
- Sensitive-field inventories and candidate policy-tag assignments.
- Migration notes, test cases, and pull-request checklists.

It should not decide undocumented business meaning, approve access to sensitive data, or execute production changes without explicit review and approval.

## Recommended repository layout

Keep generated artifacts reviewable and versioned. A small project might use:

```text
bigquery/
├── models/
│   ├── staging/
│   ├── intermediate/
│   └── marts/
├── mappings/
│   └── customer.yml
├── schemas/
│   ├── customer.json
│   └── customer.ts
├── tests/
│   └── customer_quality.sql
└── README.md
```

Adapt the layout to the repository's existing conventions. Keep each change scoped to one model or bounded data product.

## The end-to-end Copilot workflow

### 1. Establish the modeling contract

Before asking Copilot to write SQL, provide a short contract:

- Business grain: one row per customer, account, transaction, or event.
- Source tables and relevant columns.
- Required and optional fields.
- Naming conventions.
- Time zone and timestamp rules.
- Key and uniqueness rules.
- Null, default, and late-arriving-data behavior.
- Partitioning and clustering requirements.
- PII or confidential-data classifications.
- Expected consumers and freshness target.

Use placeholders for project IDs, datasets, credentials, and sensitive sample values. Never paste secrets, connection strings, access tokens, or unnecessary production records into an agent context.

**Prompt pattern:**

```text
Act as a senior analytics engineer. Create a modeling contract for a BigQuery customer dimension.

Business grain: one row per mastered customer.
Sources: raw.crm_customer, raw.core_party.
Required outcomes: stable customer_key, source_system, created_at, updated_at, status.
Rules: use UTC timestamps, preserve source identifiers, do not invent business mappings,
identify ambiguities as OPEN QUESTIONS, and classify sensitive fields.

Return:
1. grain and keys,
2. assumptions,
3. field-level business definitions,
4. unresolved questions,
5. proposed tests.
Do not include credentials or real customer values.
```

### 2. Generate the source-to-target field mapping

Ask for a mapping artifact before asking for final SQL. This separates business decisions from implementation details and makes review easier.

A useful mapping record includes:

| Field | Description |
|---|---|
| Source table and field | Exact source location |
| Target model and field | Exact destination location |
| Transformation | Rename, cast, normalize, derive, or aggregate |
| Data type | BigQuery type and mode |
| Nullability | Required, nullable, or conditional |
| Quality rule | Accepted values, uniqueness, range, or referential check |
| Sensitivity | Public, internal, confidential, or restricted |
| Confidence | Confirmed, inferred, or unresolved |
| Owner/question | Person or team that must confirm ambiguity |

**Prompt pattern:**

```text
Using the approved modeling contract below, create mappings/customer.yml.

Rules:
- Map only fields supported by the supplied metadata.
- Preserve source lineage for every target field.
- Mark inferred transformations with confidence: inferred.
- Do not silently resolve conflicting definitions.
- Use BigQuery Standard SQL types.
- Add a quality rule and sensitivity classification for every target field.

Return valid YAML only, followed by a short list of OPEN QUESTIONS.
```

Example mapping entry:

```yaml
- target: customer_key
  type: STRING
  mode: REQUIRED
  source: [raw.crm_customer.customer_id, raw.core_party.party_id]
  transformation: "Normalize the mastered identifier according to the identity-resolution rule."
  lineage_status: confirmed
  sensitivity: internal
  quality_rules:
    - not_null
    - unique
```

The transformation above is intentionally not an implementation. Copilot should not invent an identity-resolution rule; a business or data owner must supply it.

### 3. Produce a typed BigQuery schema

BigQuery supports types such as `STRING`, `INT64`, `NUMERIC`, `BOOL`, `DATE`, `DATETIME`, `TIMESTAMP`, `GEOGRAPHY`, `JSON`, `ARRAY`, and `STRUCT`. Use the official [BigQuery data types reference](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types) when validating generated types.

Ask Copilot to generate both:

1. A deployment-oriented BigQuery schema, such as JSON for `bq` or the BigQuery API.
2. An application-facing typed model, such as TypeScript or C#, when consumers need compile-time contracts.

**Prompt pattern:**

```text
Generate two synchronized artifacts from this approved mapping:
1. schemas/customer.json for the BigQuery table schema.
2. schemas/customer.ts for application consumers.

Requirements:
- Preserve field names, descriptions, modes, and nested structure.
- Represent nullable fields explicitly in the application type.
- Use ISO-8601 UTC timestamps at system boundaries.
- Do not change a type to make invalid source data pass.
- Add a generated-from comment containing the mapping file path.
- Report any mapping that cannot be represented without loss.
```

Example BigQuery schema fragment:

```json
[
  {
    "name": "customer_key",
    "type": "STRING",
    "mode": "REQUIRED",
    "description": "Stable mastered customer identifier."
  },
  {
    "name": "created_at",
    "type": "TIMESTAMP",
    "mode": "REQUIRED",
    "description": "Creation time normalized to UTC."
  },
  {
    "name": "preferences",
    "type": "RECORD",
    "mode": "NULLABLE",
    "fields": [
      {
        "name": "language",
        "type": "STRING",
        "mode": "NULLABLE"
      }
    ]
  }
]
```

Example TypeScript model:

```typescript
export interface Customer {
  customer_key: string;
  created_at: string;
  preferences?: {
    language?: string;
  };
}
```

If the consuming system is C#, use nullable reference types, `DateTimeOffset` for UTC instants, and XML documentation on public members. Do not use a language model to bypass the repository's established conventions.

### 4. Generate the modeling artifact

Once the mapping and schema are reviewed, ask Copilot to generate the SQL model. State whether the artifact is a staging table, incremental model, dimensional model, fact table, or view.

**Prompt pattern:**

```text
Create bigquery/models/marts/dim_customer.sql from mappings/customer.yml and schemas/customer.json.

Requirements:
- BigQuery GoogleSQL only.
- Keep the declared grain: one row per mastered customer.
- Use explicit column lists; never use SELECT *.
- Preserve lineage in SQL comments.
- Cast fields explicitly and use SAFE_CAST only where the mapping permits it.
- Normalize timestamps to UTC.
- Make duplicate and null behavior deterministic.
- Use partitioning and clustering only when justified by query patterns.
- Do not include project-specific identifiers; use placeholders.
- Add a companion test file with uniqueness, not-null, accepted-values, and relationship checks.
```

For an incremental model, also specify the watermark, update strategy, deduplication key, replay window, and late-arriving-data policy. If any of these are unknown, Copilot should produce an `OPEN QUESTIONS` section rather than guessing.

## Modeling guidance to ask Copilot to evaluate

Ask for a design review separately from code generation. A review prompt can request:

- Grain and key correctness.
- Whether dimensions are stable, rapidly changing, or historized.
- Whether a fact table's measures are additive, semi-additive, or non-additive.
- Join cardinality and accidental fan-out risks.
- Partitioning and clustering suitability.
- Cost and scan-volume risks.
- Backfill and replay behavior.
- Time-zone and calendar assumptions.
- Compatibility with downstream typed consumers.

Do not ask Copilot to “make the model optimal” without supplying workload information. Performance recommendations depend on query patterns, table size, freshness, and cost objectives.

## Field mapping and schema validation loop

Use a repeatable review loop:

1. **Generate:** Ask Copilot for one artifact at a time.
2. **Inspect:** Check names, grain, types, nullability, lineage, and security classifications.
3. **Validate:** Run syntax checks and BigQuery dry runs in a non-production environment.
4. **Test:** Verify uniqueness, not-null constraints, accepted values, relationships, and row counts.
5. **Compare:** Diff the generated schema against the approved mapping.
6. **Review:** Ask a domain owner to resolve all `OPEN QUESTIONS`.
7. **Deploy:** Use the normal pull-request and approval workflow.

Useful follow-up prompts:

```text
Compare the SQL, JSON schema, and YAML mapping. List every mismatch by field.
Do not modify files.
```

```text
Review this model for silent data loss, unintended nulls, type narrowing,
join fan-out, duplicate keys, and timezone errors. Cite the exact column and
recommend a test for each issue.
```

```text
Generate BigQuery validation queries only. Do not generate destructive SQL,
DDL, credentials, or production execution commands.
```

## Security and governance

Sensitive fields require explicit human review. BigQuery column-level access control can use policy tags, and BigQuery checks authorization for protected columns at query time ([column-level access control](https://cloud.google.com/bigquery/docs/column-level-security-intro)). Policy tags can be assigned through schema definitions, but the policy-tag resource names must come from the approved taxonomy ([restrict access with column-level access control](https://cloud.google.com/bigquery/docs/column-level-security)).

Row-level security can restrict access to subsets of rows and can coexist with column-level security and dataset, table, and project controls ([row-level security](https://cloud.google.com/bigquery/docs/row-level-security-intro)). Ask Copilot to draft policies and tests, but have the data steward and security owner approve:

- Classification and policy-tag assignments.
- Row filters and authorized principals.
- Masking behavior.
- IAM roles and service accounts.
- Cross-project and cross-region data movement.
- Whether generated artifacts expose personal or confidential data.

Use environment variables or a managed secret system for credentials. Keep project IDs, dataset names, and policy-tag identifiers configurable when artifacts are intended for reuse. Never hardcode connection strings or secrets.

## Definition of done

A modeling change is ready for review when:

- The business grain and keys are documented.
- Every target field has a reviewed source, transformation, type, nullability, and sensitivity classification.
- The SQL, mapping, and typed schema agree.
- Ambiguities are documented and assigned rather than guessed.
- Data quality and regression tests exist where the repository supports them.
- Queries have been syntax-checked and dry-run or tested safely outside production.
- Partitioning and clustering choices have a stated rationale.
- Security controls have been reviewed by the appropriate owner.
- No secrets, connection strings, or unnecessary production data are present.
- The pull request explains what Copilot generated and what a human verified.

## Pull-request summary template

```markdown
## BigQuery modeling change

### Artifacts
- Model: `bigquery/models/...`
- Mapping: `bigquery/mappings/...`
- Schema: `bigquery/schemas/...`
- Tests: `bigquery/tests/...`

### Contract
- Grain:
- Primary/business key:
- Refresh strategy:
- Partitioning/clustering:

### Validation
- [ ] SQL syntax or dry run completed outside production
- [ ] Mapping/schema/SQL diff reviewed
- [ ] Data quality tests added or updated
- [ ] Sensitive fields and access controls reviewed
- [ ] No secrets or production records committed
- [ ] All OPEN QUESTIONS resolved

### Copilot usage
Copilot was used for first drafts and consistency checks. Human reviewers verified business rules,
lineage, types, security classifications, tests, and deployment safety.
```

## Sources

- [GitHub prompt engineering for Copilot Chat](https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering)
- [Customize Copilot for your project](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-copilot-overview)
- [BigQuery data types](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types)
- [BigQuery column-level access control](https://cloud.google.com/bigquery/docs/column-level-security-intro)
- [BigQuery row-level security](https://cloud.google.com/bigquery/docs/row-level-security-intro)
