# Goal 12: Cost and Security Awareness for LLM Usage

**Phase:** Level 2 — AI Agent Engineer
**Owner:** TPL / Security
**Target:** Month 5

## Description

Define, document, and enforce cost thresholds and data classification rules for all cloud-based LLM integrations. Produce a per-team cost analysis report and ensure no sensitive data (PII/PHI) flows to external LLMs without approved controls — critical given the healthcare and legal document processing context of the platform.

## SMART Breakdown

- **Specific:** Publish a policy covering: data classification tiers for LLM use, approved vs. prohibited data types per LLM provider, per-integration cost estimates, and cost alert thresholds. Audit all existing LLM integrations against this policy.
- **Measurable:** Every LLM integration has a documented cost estimate and data sensitivity classification; zero integrations send PII/PHI to external LLMs without documented, approved controls.
- **Achievable:** Requires policy definition and an audit of existing integrations — no new infrastructure. Builds on data classification work already done for compliance.
- **Relevant:** Essential for HIPAA compliance given the platform processes healthcare records and legal documents. Also controls cloud spend as LLM usage scales.
- **Time-bound:** Policy published and initial audit complete by end of Month 4; all integrations compliant and enforced by end of Month 5.

## Deliverables

- [ ] Data classification tiers defined (public, internal, sensitive, restricted)
- [ ] LLM usage policy published: approved providers per data tier, prohibited data types
- [ ] Cost estimation template created for new LLM integrations
- [ ] Audit of all existing LLM integrations against the new policy
- [ ] Remediation plan for any non-compliant integrations
- [ ] Per-team cost analysis report produced
- [ ] Cost alert thresholds configured in cloud billing dashboards
- [ ] Policy review by legal/compliance team
- [ ] Team training session on the policy

## Acceptance Criteria

- Every LLM integration is documented with a data classification and cost estimate.
- Zero integrations are found to send PII/PHI to unapproved external LLMs (verified by audit).
- Cost alerts are active and tested for all LLM-consuming services.
- Policy is reviewed and signed off by the compliance team.

---

## Learning Objectives

By the end of this goal, you should be able to:

- Estimate the token cost of a given AI operation
- Identify the main LLM security risks (prompt injection, data exposure, model inversion)
- Know what data categories must never be sent to a cloud LLM

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. How do you estimate the token cost of an LLM operation? Walk through an example.
2. What is prompt injection in an agent context? Give an example of how it could affect a CI/CD agent.
3. What categories of data should never leave your infrastructure and go to a cloud LLM?
4. What's the trade-off between using GPT-4 vs a local model for a code generation task?
