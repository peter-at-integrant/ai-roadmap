# Goal 13: Scalable LLM Deployment Architecture

**Phase:** Level 3 — AI Architect
**Owner:** Architect
**Target:** Month 9

## Description

Design and document a reference architecture for deploying open-source LLMs at scale on the existing AKS infrastructure, using vLLM or HuggingFace Inference with GPU node pools. Include auto-scaling, cost-performance benchmarks, and operational runbooks — enabling the platform to host models internally for sensitive workloads.

## SMART Breakdown

- **Specific:** Produce a reference architecture document covering: model serving stack (vLLM or HuggingFace Inference), AKS GPU node pool configuration, auto-scaling policy, observability setup, and a cost-performance benchmark comparing at least 2 model sizes.
- **Measurable:** Architecture document reviewed and approved by CTO/tech leads; at least one open-source model deployed to AKS with documented throughput (tokens/sec) and cost-per-1k-token metrics.
- **Achievable:** AKS infrastructure already exists in `devops/`; Azure AKS supports GPU node pools; vLLM and HuggingFace Inference are production-ready open-source stacks.
- **Relevant:** Enables internal hosting of LLMs for sensitive healthcare and legal data, reducing cloud LLM dependency and data exposure risk.
- **Time-bound:** Reference architecture approved by end of Month 7; first model deployed and benchmarked by end of Month 9.

## Deliverables

- [ ] Reference architecture document: model serving stack, AKS configuration, networking, security
- [ ] GPU node pool provisioned in AKS (or design validated in a sandbox)
- [ ] Model serving stack deployed: vLLM or HuggingFace Inference Endpoints
- [ ] Auto-scaling policy configured and tested (scale-to-zero when idle)
- [ ] Observability: metrics (latency, throughput, GPU utilization) in existing dashboards
- [ ] Cost-performance benchmark: at least 2 model sizes compared
- [ ] Operational runbook: how to deploy, update, and roll back a model
- [ ] Architecture review meeting with CTO/tech leads
- [ ] Document approved and published to team KB

## Acceptance Criteria

- Reference architecture document is approved by CTO/tech leads by end of Month 7.
- At least one model is live on AKS with documented throughput and cost metrics by end of Month 9.
- Auto-scaling works correctly: cluster scales down to zero idle nodes, scales up within defined SLA.
- Operational runbook is complete and validated by a team member who was not involved in the build.
