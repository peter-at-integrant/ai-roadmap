# Goal 13: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What is vLLM and what problem does it solve?**

> vLLM is a high-throughput inference engine for LLMs. It solves the problem of serving LLMs efficiently at scale. Naively, each request allocates GPU memory for its full context window, which is wasteful when most of that memory sits empty. vLLM uses PagedAttention — a technique borrowed from OS virtual memory — to manage GPU memory dynamically, allowing many requests to share the GPU efficiently. The result is significantly higher throughput (requests per second) and lower memory overhead compared to running inference naively.

---

**Q2. What factors determine whether to use cloud inference vs self-hosted? Walk through the decision.**

> Key factors:
> 1. **Data sensitivity** — PII or proprietary code → self-hosted (data stays on-prem).
> 2. **Volume** — high daily token volume → self-hosted can be cheaper at scale; low volume → cloud is cheaper (no idle hardware cost).
> 3. **Model quality required** — need frontier model (GPT-4, Claude Opus) → cloud only; 7B–13B sufficient → self-hosted viable.
> 4. **Latency requirements** — cloud has network overhead; local inference can be faster for small models on good hardware.
> 5. **Team infrastructure capability** — self-hosting requires GPU machines, maintenance, and monitoring.
> Default: start with cloud. Move to self-hosted when cost or data sensitivity justifies the operational overhead.

---

**Q3. Design a simple deployment architecture for running a 7B parameter model for team code review. What infrastructure do you choose and why?**

> - **Model:** CodeLlama 7B (quantized to 4-bit) — sufficient for code review, small enough to run on a single GPU.
> - **Inference server:** Ollama or vLLM on a single VM with an NVIDIA A10G (24GB VRAM) — cost-effective, handles 4-bit 7B models with headroom.
> - **API layer:** expose the model via an OpenAI-compatible HTTP endpoint so existing tooling works without changes.
> - **Access:** internal network only, behind VPN — no public exposure.
> - **Deployment:** Docker container on the VM, restarted automatically via systemd. No Kubernetes needed for a single team.
> This is simple to operate and cheap (~$0.75/hr on AWS for an `g5.xlarge`), run only during working hours to reduce cost.

---

**Q4. What is quantization and how does it affect model quality vs hosting cost?**

> Quantization reduces the precision of a model's weights — e.g. from 32-bit or 16-bit floats down to 8-bit or 4-bit integers. This shrinks the model's memory footprint dramatically: a 7B model that needs ~14GB at 16-bit needs only ~4GB at 4-bit. The trade-off is a small reduction in output quality — quantized models are slightly less accurate and may struggle more with complex reasoning. For most code generation and review tasks, 4-bit quantization is acceptable. The hosting benefit is significant: smaller models fit on cheaper GPUs, reducing infrastructure cost by 2–4×.
