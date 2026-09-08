# Discovery Session Notes & Question Bank

## Overview
- **Initiative:** Phase 0 Discovery & Alignment
- **Date:** September 8, 2026
- **Participants:** Talal, Mayar, Anas
- **Reviewer:** Abdullah (PDPL & Governance Lead)

---

## 1. Questions Bank

### Product
1. What are the core user flows generating the primary transactional data?
2. What are the upcoming features that will alter our current data contracts or schemas?
3. Which key performance indicators (KPIs) define product success today?

### Software / Engineering
1. What systems, operational databases, or services act as primary data producers?
2. Are change data capture (CDC) or event-streaming mechanisms available, or is extraction batch-based?
3. What are the current latency limits and service-level objectives (SLOs)?

### AI / Machine Learning
1. What feature stores or historical training datasets are required for current models?
2. What are the freshness, latency, and throughput requirements for inference pipelines?
3. Are there data drift or model retraining triggers that need upstream pipeline events?

### Business & Stakeholders
1. What reporting dashboards or analytics are critical for daily business operations?
2. What data pain points or discrepancies currently impact decision-making?
3. What data domains are considered high-priority for immediate delivery?

### PDPL & Compliance Focus (Abdullah's Lens)
1. Where is Personally Identifiable Information (PII) collected across these streams?
2. What data retention, anonymization, and consent-tracking mechanisms are required?
3. Are there cross-border data transfer limitations or strict residency constraints?

---

## 2. Session Notes

### Session 1: Product Team
- **Focus Areas:** Core product flows, telemetry, and planned roadmap items.
- **Key Takeaways:** [Add notes from Product session here]
- **Action Items:** [Add follow-ups here]

### Session 2: Software Engineering Team
- **Focus Areas:** System architecture, database schemas, CDC, and API boundaries.
- **Key Takeaways:** [Add notes from Engineering session here]
- **Action Items:** [Add follow-ups here]

### Session 3: AI / Data Science Team
- **Focus Areas:** Feature pipelines, training datasets, and inference latency.
- **Key Takeaways:** [Add notes from AI session here]
- **Action Items:** [Add follow-ups here]

### Session 4: Business Stakeholders
- **Focus Areas:** Core metrics, business reporting cadence, and current data gaps.
- **Key Takeaways:** [Add notes from Business session here]
- **Action Items:** [Add follow-ups here]

---

## 3. PDPL & Data Governance Review (Led by Abdullah)
- **PII Identification:** [Document identified PII fields across all 4 sessions]
- **Storage & Encryption:** [Document compliance requirements for rest and transit]
- **Data Subject Rights & Retention:** [Document policies for deletion, retention, and access]
- **Sign-off Status:** Pending Abdullah's review via Pull Request.
