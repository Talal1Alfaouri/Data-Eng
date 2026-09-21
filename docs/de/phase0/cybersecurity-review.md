# DE-004 — Cybersecurity Review Points

Owner: Abdullah
Priority: P0
Scope: Medrak MVP

## P0 — Required Review
- [ ] Approve secrets manager/KMS for Supabase, Salla, Meta, TikTok and database credentials.
- [ ] Confirm OAuth token encryption, least-privilege scopes, rotation and revocation.
- [ ] Confirm tenant isolation enforcement at database/service/API layers.
- [ ] Add automated negative cross-tenant tests.
- [ ] Confirm trusted merchant-to-source mappings and prevent client-side tenant spoofing.
- [ ] Confirm encryption at rest and in transit.
- [ ] Enable Git/CI secret scanning.
- [ ] Approve log redaction for credentials, PII, raw uploads and AI data.
- [ ] Approve secure CSV upload validation/quarantine and deletion.
- [ ] Confirm deletion/disconnect propagation across storage, caches, indexes and processors.
- [ ] Confirm backup retention and deletion/tombstone behavior after restore.
- [ ] Review external AI/LLM provider retention, training use, subprocessors and hosting.
- [ ] Review Saudi PDPL cross-border transfer requirements where applicable.
- [ ] Confirm incident-response process for personal-data or credential exposure.

## P1 — Before Scaling
- [ ] Approve RBAC/least-privilege access matrix.
- [ ] Approve privileged/security audit logging.
- [ ] Confirm synthetic/anonymized non-production data policy.
- [ ] Confirm tenant isolation in object storage, queues and caches.
- [ ] Confirm webhook signature validation/replay protection.
- [ ] Apply rate limits/abuse controls to ingestion endpoints.
- [ ] Enable dependency/vulnerability scanning.
- [ ] Confirm retention enforcement jobs.
- [ ] Confirm vendor/DPA requirements.

## Decisions Needed from Cybersecurity
1. Approved secrets manager/KMS.
2. Tenant-isolation mechanism.
3. Application/security log retention.
4. Raw-upload retention/deletion control.
5. Approved AI providers/settings and allowed data classes.
6. Hosting/data-residency/cross-border process.
7. Backup/deletion behavior.
8. Access-control matrix for Restricted data.
