# DE-004 — Draft Data Quality Rules

Owner: Abdullah
Priority: P0
Scope: Medrak MVP

| ID | Rule | Severity |
|---|---|---|
| DQ-001 | Every tenant-scoped record has a valid non-null merchant_id | P0 |
| DQ-002 | Cross-tenant data access/join/export is prohibited | P0 |
| DQ-003 | (merchant_id, source_system, source_record_id) is unique | P0 |
| DQ-004 | order_id is non-null and unique within merchant/source | P0 |
| DQ-005 | Required product/SKU identifiers are present | P0 |
| DQ-006 | Order lines reference valid orders within the same tenant | P0 |
| DQ-007 | Customer references are stable and pseudonymous | P1 |
| DQ-008 | Currency values are valid approved currency codes | P1 |
| DQ-009 | Amounts are numeric; negatives require refund/adjustment semantics | P0 |
| DQ-010 | Normal-sale quantity is greater than zero | P1 |
| DQ-011 | Required timestamps are valid and not implausibly future-dated | P0 |
| DQ-012 | created/updated and related event dates follow logical ordering | P1 |
| DQ-013 | Source statuses map to approved canonical values | P1 |
| DQ-014 | Manual CSV accepts only approved schema/columns | P0 |
| DQ-015 | Manual CSV contains no prohibited direct PII or secrets | P0 |
| DQ-016 | Ingestion/tracking events are idempotent and do not double-count | P0 |
| DQ-017 | Tracking metadata uses an allow-list and contains no PII/secrets | P0 |
| DQ-018 | Social content maps to exactly one merchant/account | P1 |
| DQ-019 | Social metrics are numeric/non-negative; missing is not treated as zero | P1 |
| DQ-020 | Sources expose ingestion/sync timestamps for freshness monitoring | P1 |
| DQ-021 | P0 required fields are 100% valid before curated publish | P0 |
| DQ-022 | Source counts/totals reconcile where control totals exist | P1 |
| DQ-023 | Failed/partial loads cannot publish as successful curated data | P0 |
| DQ-024 | Disconnected/deletion-pending tenants receive no new ingestion | P0 |
| DQ-025 | Curated records preserve source/provenance information | P1 |
| DQ-026 | Restricted/Secret fields cannot appear outside approved zones | P0 |

## Minimum Publish Gates
Cross-tenant violations = 0; secret/prohibited-PII leakage = 0; duplicate critical keys = 0; invalid critical IDs = 0; critical orphan relationships = 0; invalid required timestamps = 0; failed/partial batches published = 0.
