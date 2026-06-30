# Slide Deck — Local LLM: Deployment, Operations & Infrastructure
*Source: [05_local_llm.md](../05_local_llm.md) | Facilitator use*

# Slide 01 | Title & Learning Objectives

## On screen

**Lecture 5 — Local LLM**

*For all members — developers, operators, staff, support, leadership*

| By the end you can… |
|---------------------|
| Explain **why** orgs choose local LLMs vs cloud APIs |
| Describe the local inference stack (weights, runtime, API, monitoring) |
| Understand model size, **quantization**, hardware specs |
| Read VRAM/RAM/storage requirement tables for deployment tiers |
| Follow prepare / operate / develop / communicate duties by role |
| Participate in sizing, rollout, and incident discussions |

**Prerequisite:** [04_genai_llm.md](../04_genai_llm.md) (training vs inference, tokens, Transformers)  
**Duration:** 90–105 min

## Speaker notes

Open: local LLM means the model runs on **infrastructure you control** — your servers, private cloud, network boundaries. That brings control **and** duty: you own uptime, security, capacity, honest communication. *(~2 min)* Poll: what worries you more — data leaving the org, or operating GPU servers? Who should approve model versions? *(~3 min)*

---

# Slide 02 | Cloud API vs Local LLM

## On screen

| Dimension | **Cloud LLM API** | **Local LLM** |
|-----------|-------------------|---------------|
| **Where compute runs** | Vendor data centers | Your machines / private cloud |
| **Data path** | Prompts sent to vendor | Stays inside boundary (if architected correctly) |
| **Startup effort** | Low — API key + integration | Higher — hardware, deploy, monitor |
| **Ongoing ops** | Vendor hosts model | **You** handle GPUs, scaling, upgrades |
| **Cost model** | Per-token / subscription | CapEx + power + staff + maintenance |
| **Compliance** | Vendor contracts & region | Stronger data residency / air-gap story |
| **Best when** | Prototype, low volume | Sensitive data, policy requires on-prem, high volume |

> **Cloud trades control for convenience; local trades convenience for control — and operational responsibility.**

## Speaker notes

One sentence to anchor the table. Neither is universally "better" — it's a workload and policy decision. Research data, citizen PII, trade secrets often push toward local. Rapid prototyping often starts cloud. *(~5 min)* Ask who currently uses which pattern in the org. *(~2 min)*

---

# Slide 03 | Why Organizations Choose Local

## On screen

| Driver | Plain-language explanation |
|--------|---------------------------|
| **Data sovereignty** | Citizen, research, or trade-secret data must not leave jurisdiction |
| **Policy / regulation** | Sector rules restrict external AI services |
| **Security classification** | Internal-only networks, air-gapped environments |
| **Cost at scale** | High query volume *may* be cheaper on owned GPUs (model carefully) |
| **Customization** | Pin versions, standardize behavior across products |
| **Availability** | Reduce vendor outage dependency (gain your own failure modes) |

**Examples:**
- Patent prior-art search on classified annexes → local only
- Funding eligibility Q&A over internal circulars → local + RAG
- Public marketing taglines → may route to approved cloud (hybrid)

## Speaker notes

Connect drivers to real policy language the room recognizes. Data sovereignty is often the board-level driver; cost is often oversold — GPUs, power, cooling, staff are real. Customization: pin `model-name + revision + quantization` in config. *(~4 min)* Leadership often decides citizen-data-may-not-use-cloud-API — not the GPU team alone. *(~2 min)*

---

# Slide 04 | Myths vs Reality

## On screen

| Myth | Reality |
|------|---------|
| "Local = 100% private" | Logs, backups, admin access, misconfigured APIs can still leak |
| "Local = always cheaper" | GPUs, power, cooling, staff time are real costs |
| "Local = always better quality" | You may run a **smaller** model than latest cloud giant |
| "Local = no internet needed" | Model downloads/updates usually need connectivity (unless offline process) |
| "Local = set and forget" | Upgrades change behavior; ops load is continuous |

**Also false:** "Local = immune to prompt injection" — security issues move with you ([Lecture 4](../04_genai_llm.md))

## Speaker notes

Honest messaging prevents disappointment. Local is not magic privacy — architecture and policy must match the story. Staff laptop Ollama is "local" technically but usually **not** production governance — unvetted models, data leakage risk. *(~4 min)*

---

# Slide 05 | Hybrid Routing

## On screen

Many deployments use **both** cloud and local:

```
┌────────────────────────────────────────────────────────┐
│  Routing / policy layer                                 │
│  "Classified doc Q&A → local"                           │
│  "Public marketing draft → approved cloud"              │
│  "Citizen PII workflows → local only"                   │
└────────────────────────────────────────────────────────┘
         │                              │
         ▼                              ▼
   Local GPU stack              Approved cloud API
   + RAG + ACL                  + limited data classes
```

| Workload | Typical route |
|----------|---------------|
| Internal policy / funding corpus | Local + RAG |
| Patent landscape draft (non-classified) | Approved cloud or local |
| Minister briefing prep (sensitive) | Local + human review |

**Developers** implement routing. **Leadership** approves which workloads go where.

## Speaker notes

Hybrid is common in practice — don't force binary thinking. Routing layer enforces data classification. Example: classified patent annex Q&A → local; generic prose polish → approved cloud if policy allows. Misconfiguration here is a security incident waiting to happen. *(~4 min)*

---

# Slide 06 | Local LLM Stack Architecture

## On screen

```
┌──────────────┐     ┌─────────────────┐     ┌──────────────────┐
│  Client app  │────►│  API gateway /  │────►│  Inference       │
│  (web, bot)  │     │  orchestrator   │     │  server/runtime  │
└──────────────┘     │  auth, rate cap │     │  (loads model)   │
                     │  logging        │     └────────┬─────────┘
                     └────────┬────────┘              │
                              │                       ▼
                              ▼              ┌──────────────────┐
                     ┌─────────────────┐     │  GPU or CPU      │
                     │  RAG service    │     │  model weights   │
                     │  (Lecture 6)    │     │  in VRAM/RAM     │
                     └─────────────────┘     └──────────────────┘
                              │
                              ▼
                     ┌─────────────────┐
                     │  Vector DB /    │
                     │  document store │
                     └─────────────────┘
```

## Speaker notes

Walk the request path slowly — this is the map for ops and dev. One local inference request: API → orchestrator builds prompt → tokenizer → runtime on GPU → stream tokens → log metadata (not secrets per policy). Cold start: first request after idle may be slow while weights load into VRAM. *(~5 min)*

---

# Slide 07 | Model Size & Quantization

## On screen

**Parameters** ≈ model capacity (billions of weights):

| Size band | Intuition | Trade-off |
|-----------|-----------|-----------|
| **Small (≈1–8B)** | Faster, less VRAM, narrow tasks | Weaker on hard reasoning / long context |
| **Medium (≈13–34B)** | Balance for many org assistants | Needs serious GPU(s) at full precision |
| **Large (≈70B+)** | Stronger on complex tasks | Multi-GPU or heavy quantization |

**Quantization** — reduce numeric precision (FP16 → INT8 → INT4):

| Effect | Detail |
|--------|--------|
| **Memory** | Smaller footprint — model fits on GPU |
| **Speed** | Often faster inference |
| **Quality** | May slightly degrade on edge tasks |

**Analogy:** Full precision = original photo; Q4 = compressed JPEG — usually fine, sometimes artifacts.

**Ops:** Document `Q4_K_M` vs `Q8_0` in runbook — swapping quant is a **deployment change**.

## Speaker notes

No universal "best" size — only best for your hardware, latency budget, quality bar. Quantization is how many orgs fit 70B on one or two GPUs. Legal review open-weight licenses before production — approved model list only. *(~5 min)*

---

# Slide 08 | GPU vs CPU Inference

## On screen

| | **GPU** | **CPU** |
|---|---------|---------|
| **Speed** | Much faster for typical LLMs | Slow for larger models |
| **Cost** | Hardware expensive | Uses existing servers |
| **Use case** | Production assistants, concurrent users | Dev/test, tiny models, low traffic |
| **Ops focus** | Drivers, CUDA, VRAM exhaustion | RAM, thread count, realistic SLA |

> "Asking a CPU to serve a 70B model to 50 concurrent users is like one clerk at every airport desk."

**Context length + concurrency** multiply load independently — align marketed limits with **measured** benchmarks, not vendor marketing alone.

## Speaker notes

Possible without GPUs for small models and low traffic — set expectations accordingly. Production funding FAQ with moderate concurrency → plan GPU. CPU fallback OK for dev laptops and air-gapped tiny models. Two pressures: per-request memory grows with context; concurrent requests multiply load. *(~4 min)*

---

# Slide 09 | Hardware Concepts — VRAM, RAM, TPS

## On screen

| Term | Meaning | Why it matters |
|------|---------|----------------|
| **GPU** | Parallel matrix math accelerator | Primary LLM inference accelerator |
| **VRAM** | GPU dedicated memory | Weights + activations + KV cache must fit |
| **RAM** | System memory | CPU inference, RAG, vector DB, orchestration |
| **NVMe SSD** | Fast storage | Model files often 4–140+ GB each |
| **TPS** | Tokens per second | Throughput metric |
| **Batch size** | Concurrent sequences processed together | Higher batch → more VRAM |

**GPU tiers (indicative):**

| Tier | VRAM | Typical role |
|------|------|--------------|
| Entry | 8–12 GB | Dev/test, tiny models, embeddings |
| Workstation | 24–48 GB | Single medium model, low–medium concurrency |
| Data center | 40–80 GB | Production 7B–70B, higher concurrency |
| Multi-GPU | 2–8× datacenter | 70B+ full precision, high throughput |

> **VRAM is the hard ceiling.** Model that doesn't fit → OOM or slow spill paths.

## Speaker notes

VRAM is the binding constraint for GPU inference — say this twice. Leave 10–20% VRAM headroom for spikes and driver overhead. TPS drives user experience — benchmark on your model, quantization, and RAG load. Tables are starting points, not guarantees. *(~5 min)*

---

# Slide 10 | VRAM Sizing Table (7B–70B)

## On screen

**Bytes per parameter (approx.):**

| Precision | Bytes/param |
|-----------|-------------|
| FP16 / BF16 | 2 |
| INT8 | 1 |
| INT4 (Q4) | ~0.5 |

**Weight-only sizes:**

| Model | FP16 weights | Q4 weights |
|-------|--------------|------------|
| **7B** | ~14 GB | ~4–5 GB |
| **13B** | ~26 GB | ~7–8 GB |
| **34B** | ~68 GB | ~18–20 GB |
| **70B** | ~140 GB | ~35–40 GB |

**Add overhead:** KV cache (grows with context × batch), CUDA runtime ~1–2 GB

| Setup | Often fits when |
|-------|-----------------|
| 7B Q4 | 12 GB+ GPU recommended |
| 13B Q4 | 16 GB GPU |
| 70B Q4 | 48 GB or multi-GPU |
| 70B FP16 | 2× 80 GB or equivalent |

## Speaker notes

Rule of thumb for "how much VRAM for 13B Q4 with 8k context?" — start with weight table (~8 GB) + KV overhead + 2 GB buffer; benchmark; likely 16 GB class minimum. Always benchmark — long RAG prompts eat KV cache. *(~5 min)* Ops should own this math before hardware purchase. *(~2 min)*

---

# Slide 11 | Deployment Tiers T0–T3

## On screen

| Tier | Profile | Indicative hardware | Typical use |
|------|---------|---------------------|-------------|
| **T0 — Pilot** | 1× 12–24 GB GPU, 32–64 GB RAM | Workstation / single server | Internal pilot, <10 users |
| **T1 — Team** | 1× 24–48 GB GPU, 64–128 GB RAM, NVMe | Production single-node | Dept assistant, RAG FAQ |
| **T2 — Org** | 1–2× 80 GB or 2× 48 GB, 128+ GB RAM | Rack server | Multi-team, moderate concurrency |
| **T3 — Scale** | 4–8× GPU, load balancer, HA vector DB | GPU cluster | Platform-wide, SLA-backed |

**Concurrency note:** "50 users" ≠ 50 simultaneous GPU generations unless you queue or scale. Define **peak concurrent inference** separately from registered users.

**Under-size hardware → slow product → shadow IT to cloud → worse privacy story.**

## Speaker notes

Tier templates help leadership approve realistic pilots vs production scale. Customize after benchmark — document org-approved tiers. T0 for patent office pilot; T2 for org-wide funding assistant. Leadership uses tiers for budget conversations, not raw GPU marketing. *(~5 min)*

---

# Slide 12 | Software Stack Layers

## On screen

```
┌─────────────────────────────────────────────────────────┐
│  Application (UI, bots, workflows)                       │
├─────────────────────────────────────────────────────────┤
│  Orchestration (RAG, prompts, auth, rate limits)         │
├─────────────────────────────────────────────────────────┤
│  Inference API (OpenAI-compatible REST, gRPC)          │
├─────────────────────────────────────────────────────────┤
│  Inference runtime (vLLM, llama.cpp, TGI, Ollama, …)   │
├─────────────────────────────────────────────────────────┤
│  ML framework glue (PyTorch, CUDA kernels)               │
├─────────────────────────────────────────────────────────┤
│  GPU driver + CUDA / ROCm / oneAPI                       │
├─────────────────────────────────────────────────────────┤
│  OS (Linux preferred for production)                     │
└─────────────────────────────────────────────────────────┘
```

**Runtime categories:** llama.cpp/GGUF (CPU/edge), Ollama (dev/demos), vLLM (production GPU), TGI (HF ecosystem), TensorRT-LLM (NVIDIA perf)

## Speaker notes

Layered stack — failure at any layer breaks inference. Org policy: maintain **approved runtime list** with supported formats (GGUF, Safetensors). Production GPU serving: Linux dominates — standardize to reduce ops surprise. Windows/WSL OK for dev laptops. *(~4 min)*

---

# Slide 13 | Driver / CUDA / Runtime Pinning

## On screen

**NVIDIA stack (most common):**

| Layer | Ops responsibility |
|-------|-------------------|
| **NVIDIA driver** | Pin version; test before upgrade |
| **CUDA toolkit** | Match runtime requirements |
| **cuDNN / NCCL** | Per runtime bundle |
| **Inference runtime** | vLLM, TGI, etc. — version pinned |

**Version matrix (fill for your org):**

| Component | Staging | Production | Owner | Last verified |
|-----------|---------|------------|-------|---------------|
| OS image | | | Ops | |
| GPU driver | | | Ops | |
| CUDA / ROCm | | | Ops | |
| Inference runtime | | | Ops/Dev | |
| LLM model + quant | | | ML lead | |

> Driver + CUDA + runtime must be **compatible**. Upgrading one without testing breaks inference.

## Speaker notes

Treat this like database version pinning — not casual Friday upgrades. AMD ROCm and Intel oneAPI follow similar stacking. Deploy bundle: `model weights + tokenizer + config.json + generation config` as versioned unit. Checksum on deploy. *(~4 min)*

---

# Slide 14 | Supporting Services Map

## On screen

| Service | Common options | Spec notes |
|---------|----------------|------------|
| **Vector database** | pgvector, Milvus, Qdrant, Weaviate | RAM, disk, replication ([Lecture 6](../06_rag.md)) |
| **Embedding service** | sentence-transformers, small models | Often CPU; batch for throughput |
| **API gateway** | Kong, NGINX, custom | TLS, auth, rate limits |
| **Queue** | Redis, RabbitMQ, Kafka | Spike smoothing, async jobs |
| **Observability** | Prometheus, Grafana, Loki, OTel | Metrics + log retention policy |
| **Secrets** | Vault, KMS, sealed secrets | No keys in repos |

**Security controls:** TLS 1.2+, RBAC/OAuth, GPU nodes no public ingress, container scanning, audit logging on model changes

**Can LLM + vector DB share one machine?** Yes for pilots; at scale split — they compete for RAM/disk I/O.

## Speaker notes

Full local stack is more than one GPU process. Embedding service often CPU — 2–8 GB RAM depending on model. Vector index storage: plan 2–4× raw embedding storage for production headroom. Security: mTLS optional on internal mesh; network policy on GPU VLAN. *(~4 min)*

---

# Slide 15 | Prepare — Governance Checklist

## On screen

**Before production:**

| Area | Items |
|------|-------|
| **Governance** | Approved model registry (name, version, license, owner); data classification matrix |
| **Network** | Inference VLAN; no public ingress on GPU nodes if policy requires |
| **Secrets** | API keys in vault; no keys in repos |
| **Infrastructure** | Benchmark on realistic prompts **including RAG**; checksum on model deploy |
| **HA** | Second node, load balancer, or honest "maintenance mode" UX |
| **Capacity playbook** | At 80% GPU memory — queue, reject, or scale? |

**Developers:** OpenAI-compatible API abstraction; timeouts; streaming; structured errors; eval suite tied to model **and** prompt version

**All staff:** Approved entry points only — no shadow IT on random local ports

## Speaker notes

Preparation is cross-functional — not ops alone. Approved model registry needs legal review for open-weight licenses. Retention: prompt/log limits and deletion process. Named owners: platform ops, ML lead, security liaison, product owner. *(~5 min)*

---

# Slide 16 | Operate — Monitoring & Incidents

## On screen

**Minimum viable dashboard:**

| Metric | Why it matters |
|--------|----------------|
| Request rate & queue depth | Capacity planning |
| p50 / p95 latency | User experience |
| Tokens in/out per minute | Cost and heat |
| GPU utilization & VRAM | OOM if ignored |
| Error rate | Timeouts, 500s, CUDA OOM |
| Model version label | Debug drift after upgrades |
| Health check | Synthetic prompt every N minutes |

**Incident first responses:**

| Incident | First response |
|----------|----------------|
| **GPU OOM** | Reduce concurrency / max context; restart runtime |
| **Quality regression** | Check version pin, RAG index freshness, prompt change |
| **Suspected PII in logs** | Stop logging content; security incident |
| **Total outage** | Status page; failover message |

## Speaker notes

Routine cadence: daily alerts/disk; weekly quality spot-check; per-release eval + canary; quarterly model approval review and DR drill. Runbook snippet for high latency: check VRAM → queue depth → reduce max_tokens → maintenance banner if SLA breach >15 min. *(~5 min)*

---

# Slide 17 | Upgrade / Rollback Flow

## On screen

**Treat model upgrades like database migrations:**

```
1. Download & verify new weights in staging
2. Run automated + human eval (golden prompts)
3. Canary % traffic or internal-only period
4. Rollback: previous weights on disk, previous config tagged
5. Communicate to support before external-facing changes
```

| Phase | Hardware profile (cross-link L4) |
|-------|----------------------------------|
| Pre-training | Hundreds–thousands of GPUs — vendor |
| Fine-tuning (LoRA) | 1–8 GPUs, hours–days |
| **Inference (your focus)** | Right-sized GPU for model + context + concurrency |

**Upgrade when evals show net benefit — not every new release.**

## Speaker notes

Friday night model upgrade → Monday funding-rule drift is a real scenario (Lecture 4 activity). Pin versions deliberately. Support needs heads-up before behavior changes. Store eval baseline for current version — compare after upgrade. Rollback path must be tested, not theoretical. *(~4 min)*

---

# Slide 18 | Develop — Integration Patterns

## On screen

| Pattern | Description |
|---------|-------------|
| **Sync API** | Simple request/response |
| **Streaming SSE** | Token stream to UI — better perceived latency |
| **Async jobs** | Long summaries via queue + notification |
| **Batch** | Overnight document processing |
| **Router** | Route by sensitivity, language, task type |

**Performance tactics:**
- Keep model loaded — avoid cold starts in peak hours
- Limit retrieved context — RAG quality vs speed
- Cache identical FAQ queries (watch staleness)
- Smaller model for intent routing → large model for hard step
- Summarize chat history instead of stuffing full thread

**Testing before production:** smoke, regression, load, security (injection), failover

## Speaker notes

Developers: tag traces with `model_version`, `prompt_version`, `rag_index_version`. Never hardcode model paths without config management. Document breaking API changes for frontend and support. Performance is product quality — slow local LLM drives shadow cloud usage. *(~4 min)*

---

# Slide 19 | Customer & Support Messaging

## On screen

| Tell users | Avoid |
|------------|-------|
| Answers are AI-generated and may be wrong | "Guaranteed accurate" |
| Sensitive workflows stay on org-controlled systems | Implying all AI is local if hybrid |
| Maintenance windows may apply | Silent upgrades |
| Report bad answers with reproduction steps | Dismissing as "user error" |

**Support playbook:**
1. **Triage:** Outage vs quality vs policy vs user mistake
2. **Collect:** Time, feature, model version — not full classified content in ticket if prohibited
3. **Escalate:** Ops (outage); dev (quality bug); security (leak)
4. **Communicate:** Template status messages during incidents

**SLA language:** Tie commitments to **measured** local capacity — best effort vs 99% availability

## Speaker notes

Honest expectations reduce trust incidents. Hybrid deployments: don't claim everything is on-prem. Support collects session ID and model version from about screen — per retention policy. Leadership + product own SLA language tied to measured capacity. *(~4 min)*

---

# Slide 20 | Sizing Worksheet

## On screen

**Discussion exercise — no single right answer**

| Input | Your org estimates |
|-------|-------------------|
| Peak concurrent users | ___ |
| Avg prompt + completion tokens | ___ |
| Target p95 latency | ___ s |
| Required context length | ___ tokens |
| Data classification tier | ___ |
| Availability tier | ___ |

**Outcome drives:** GPU count, model size, quantization, hybrid cloud need

**Example scenario:** Funding FAQ bot — 20 peak concurrent, 4k context with RAG, p95 <8s, internal-only data → likely T1–T2 with 7B–13B Q4

## Speaker notes

Facilitate table discussion — 5–7 min fill-in. Under-sizing → slow product → shadow IT to cloud → worse privacy. Over-sizing → wasted CapEx. No formula on slide — benchmark on your prompts. Ops and leadership should leave with one number: peak concurrent inference. *(~7 min)*

---

# Slide 21 | Activity — Who Owns It?

## On screen

**START ACTIVITY — ~10 min**

Match task to primary owner:

| Task | Owner |
|------|-------|
| Approve new open-weight model license | Leadership + legal + security |
| Pin model version in production config | Dev + ops |
| Patch GPU driver on inference node | Ops |
| Write system prompt for funding FAQ bot | Staff + dev |
| Respond to "system is slow today" | Support → ops |
| Decide citizen data may not use cloud API | Leadership + security |
| Run eval suite before upgrade | Dev |
| Verify AI draft before minister briefing | Staff |

## Speaker notes

Groups match owners — debrief as full room. Local LLM success is **cross-functional**, not "the GPU team alone." Staff verify minister briefings; leadership + security decide cloud prohibition; dev runs evals; ops patches drivers. Reinforce named owners from Prepare slide. *(~10 min)*

---

# Slide 22 | Takeaways

## On screen

1. **Local LLM = control + responsibility** — data stays in bounds only if architecture and policy match
2. **VRAM is the binding constraint** — weight + KV cache math before buying hardware
3. **Software stack must be pinned** — driver, CUDA, runtime, model bundle move together
4. **Tier templates (T0–T3)** help leadership approve realistic pilots vs production scale
5. **Upgrades are production events** — eval, canary, rollback, communicate
6. **Next:** [06_rag.md](../06_rag.md) — vectorization, embeddings, retrieval over your documents

## Speaker notes

Quick recap — 30 seconds per point. Return to opening: you control infra and duty. Ask one person what they'll do differently Monday. *(~3 min)*

---

# Slide 23 | Operator Checklist

## On screen

**Production readiness:**
- [ ] Model checksum verified; version in config management
- [ ] GPU drivers pinned; standard OS image
- [ ] Auth on all inference endpoints
- [ ] Metrics + alerts configured
- [ ] Runbooks linked from on-call wiki
- [ ] Eval baseline stored for current version
- [ ] Support notified of limits and escalation path

**Monthly review:**
- [ ] Capacity trend vs forecast
- [ ] License & security patch status
- [ ] Sample quality review with product owner
- [ ] Incident log lessons applied

*Printable handout — full list in lecture doc §15*

## Speaker notes

Point to lecture doc §15 for printable version. This is the ops handoff slide — ops leads can photograph or print. Emphasize eval baseline before any upgrade. Auth on all endpoints — no "internal only so it's fine." *(~3 min)*

---

# Slide 24 | Next Session — RAG & Vectors

## On screen

**Lecture 6 — RAG: Retrieval-Augmented Generation**

| Topic | Why it matters |
|-------|----------------|
| Vectorization & embeddings | How org knowledge becomes searchable |
| Ingestion → chunk → index | Pipeline before the LLM sees a question |
| Hybrid search + rerank | Precision for policy IDs and funding circulars |
| ACL on metadata | Prevent cross-department leaks |
| Citations in UI | Ground answers in official sources |

**Reading:** [06_rag.md](../06_rag.md)

**Q&A open** — prepared answers in lecture doc §12

## Speaker notes

Preview: RAG connects LLM to **your** documents without retraining. Local stack from today + RAG layer = grounded funding/policy assistant. Tokens ≠ embeddings — Lecture 6 makes that explicit. Take Q&A — common VRAM and CUDA questions in §12. Thank the room. *(~5–10 min Q&A)*

---
