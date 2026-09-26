# DE-004 — Governance & Privacy Constraints

Owner: Abdullah
Date: 2026-09-19
Priority: P0
Scope: Medrak MVP
Baseline: Saudi PDPL / SDAIA

## Data Classification
- Medrak Account/Profile: user_id, email, merchant_id and social handles are PII/confidential.
- Salla and e-commerce: customer_id/customer_ref plus order history are pseudonymous personal data when linkable to a person.
- WhatsApp Business: excluded from MVP ingestion due to private communications and uncontrolled PII/sensitive-data risk.
- Instagram/TikTok: ingest merchant-owned content and approved aggregate metrics only; do not ingest commenter identity or raw comments for MVP.
- Manual Commerce CSV: accept only approved commerce fields; reject direct customer identifiers or secrets.
- Tracking Events: user/session behavioral data; metadata must use an explicit allow-list.

## PII
Do not ingest customer name, email, phone, address, National ID/Iqama, DOB, exact geolocation, card/bank details or other direct identifiers into MVP analytics. Treat customer_id/customer_ref as pseudonymous personal data. Use synthetic data in non-production environments.

## Sensitive Data
No known PDPL-sensitive category is required for the current MVP contract. Free text can accidentally contain sensitive data; WhatsApp/private messages and raw social comments are therefore excluded. Any future sensitive-data source requires Privacy/Cybersecurity approval before ingestion.

## Tokens / Secrets
Never store OAuth access/refresh tokens, API keys, client secrets, Supabase service-role keys, DB credentials, JWT/session tokens, webhook secrets, private keys or encryption keys in analytics tables, CSVs, logs, Git or AI prompts. Use approved secret storage, encryption, least privilege, rotation/revocation and secret scanning.

## Data Minimization
Approved minimum commerce data: merchant/store ID, order ID, timestamp/status, currency, amount/discount, pseudonymous customer reference, product/SKU, quantity/unit price, product name/category/status, approved attribution fields and sync timestamps.
Exclude direct customer identity, payment credentials, private communications, raw comments, device fingerprints, arbitrary raw payloads and secrets unless a later approved use case requires them.

## Tenant Isolation
merchant_id is the analytical tenant boundary. Every tenant-owned record must resolve to a valid merchant_id. Source-specific store/account IDs must map through controlled mappings. Tenant identity must come from trusted authenticated/server-side context. Pipelines, files, caches, exports and AI retrieval must preserve tenant scope. Cross-tenant access is prohibited and must be covered by automated negative tests.

## Retention — Proposed MVP Policy
These are proposed Medrak policy values requiring Cybersecurity/Legal confirmation:
- Account/profile: account lifetime + 30 days.
- Commerce analytics: 24 months rolling.
- Organic content/aggregate metrics: 24 months.
- Tracking events: 13 months.
- Successful raw CSV upload: max 30 days.
- Failed/quarantined file: 7 days.
- Application logs: 90 days.
- Security/audit logs: 12 months.
- OAuth tokens: only while integration is active; revoke/delete on disconnect.

## Delete / Disconnect
Stop ingestion; disable webhooks/jobs; revoke credentials; block new writes; delete applicable active PII/tenant data, raw uploads, caches and derived indexes; propagate deletion where applicable; handle backups through approved expiry/tombstone controls. Reconnection requires fresh authorization.

## AI / LLM
Allowed after minimization: public/synthetic data, non-identifying aggregates and approved merchant-owned context/content.
Prohibited: secrets, unnecessary direct customer identifiers, payment/bank data, PDPL-sensitive data, WhatsApp/private communications, raw social comments/commenter identity and database dumps.
AI providers require Privacy/Cybersecurity review for retention, training use, subprocessors, hosting and cross-border transfer.

## Logs
Never log secrets, auth headers, JWT/cookies, full customer PII, National ID/Iqama, payment details, raw CSV/API bodies containing personal data, private messages, unfiltered metadata or sensitive AI prompts/responses. Prefer request/event IDs, status, source, timestamps and sanitized internal identifiers.

## Data Model / Pipeline Impact
- Standardize merchant_id.
- Maintain controlled source-to-merchant mappings.
- Separate secret storage from analytical storage.
- Use raw/quarantine zones with short retention.
- Preserve lineage/source identifiers and ingestion timestamps.
- Support deletion/tombstones.
- Classify Restricted/Confidential fields.
- Avoid direct customer identity in MVP analytical tables.
- Restrict arbitrary metadata to approved schemas.
