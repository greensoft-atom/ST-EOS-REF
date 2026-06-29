# Local LLM — Deployment, Operations & Infrastructure

*For all members — developers, operators, staff, support, and leadership*  
*Why run models locally, how it works conceptually, and what it takes to run in production*

---

## Module Overview

This is **Lecture 5** in the technical literacy track. It covers **local** (on-premises or private-cloud) large language model deployment: motivations, architecture, hardware, day-to-day operations, and what every role must know when the organization hosts its own inference instead of (or in addition to) public APIs.

| Lecture | Topic |
|---------|--------|
| 4 | [Generative AI & LLMs](04_genai_llm.md) — what the model is |
| **5 (this)** | **Local LLM** — where and how it runs |
| 6 | [RAG](06_rag.md) — organizational knowledge layer |

**Duration:** 90–105 minutes  
**Prerequisite:** [04_genai_llm.md](04_genai_llm.md) (training vs inference, tokens, neural networks, Transformers)

---

## Learning Objectives

By the end, members should be able to:

1. Explain **why** organizations choose local LLMs and the trade-offs vs cloud APIs  
2. Describe the main components of a local inference stack (model weights, runtime, API, monitoring)  
3. Understand model size, quantization, and **hardware / software specifications** for planning  
4. Read **VRAM, RAM, storage, and network** requirement tables for common deployment tiers  
5. Follow prepare / operate / develop / customer-communication responsibilities by role  
6. Participate in sizing, rollout, incident, and upgrade discussions with shared vocabulary  

---

## 1. Opening (5 min)

**Facilitator message:**

> “Local LLM means the model runs **on infrastructure you control** — your servers, your private cloud, your network boundaries. That brings control and duty: **you** own uptime, security, capacity, and honest communication when things break or answers are wrong.”

**Quick poll:**

- What worries you more: data leaving the organization, or operating GPU servers?  
- Who should decide which model version is approved?

---

## 2. Cloud API vs Local LLM — Concepts & Principles (15 min)

### 2.1 Two ways to run inference

| Dimension | **Cloud LLM API** | **Local LLM** |
|-----------|-------------------|---------------|
| **Where compute runs** | Vendor’s data centers | Your machines / private cloud |
| **Data path** | Prompts (and sometimes logs) sent to vendor | Stays inside your boundary (if architected correctly) |
| **Startup effort** | Low — API key and integration | Higher — hardware, deploy, monitor |
| **Ongoing ops** | Vendor handles model hosting | **You** handle GPUs, drivers, scaling, upgrades |
| **Cost model** | Per-token / subscription | CapEx + power + staff + maintenance |
| **Customization** | Limited to vendor offerings | Choose open-weight models, quantizations, pinning |
| **Compliance** | Depends on vendor contracts & region | Stronger story for data residency / air-gap |
| **Latency** | Network round-trip | Often lower on LAN; depends on hardware |
| **Best when** | Prototyping, low volume, no GPU estate | Sensitive data, policy requires on-prem, high volume at scale |

**One sentence:**  
> **Cloud trades control for convenience; local trades convenience for control — and operational responsibility.**

### 2.2 Why organizations choose local LLM

| Driver | Plain-language explanation |
|--------|---------------------------|
| **Data sovereignty** | Citizen, research, or trade-secret data must not leave jurisdiction |
| **Policy / regulation** | Sector rules restrict external AI services |
| **Security classification** | Internal-only networks, air-gapped environments |
| **Cost at scale** | High query volume may be cheaper on owned GPUs (not automatic — model carefully) |
| **Customization** | Pin versions, standardize behavior across products |
| **Availability** | Reduce dependency on external vendor outages (you gain your own failure modes) |

### 2.3 What local LLM does **not** automatically solve

| Myth | Reality |
|------|---------|
| “Local = 100% private” | Logs, backups, admin access, and misconfigured APIs can still leak |
| “Local = always cheaper” | GPUs, power, cooling, and staff time are real costs |
| “Local = always better quality” | You may run a **smaller** model than the latest cloud giant |
| “Local = no internet needed” | Model downloads and updates usually need connectivity unless offline process exists |
| “Local = set and forget” | Models drift in **behavior** when you upgrade; ops load is continuous |

### 2.4 Hybrid patterns (common in practice)

Many deployments use **both**:

```
┌────────────────────────────────────────────────────────┐
│  Routing / policy layer                                 │
│  “Classified doc Q&A → local”                           │
│  “Public marketing draft → approved cloud”              │
└────────────────────────────────────────────────────────┘
```

**Developers** implement routing rules. **Leadership** approves which workloads go where.

---

## 3. Local LLM Stack — How It Works (20 min)

### 3.1 Architecture diagram

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

### 3.2 Core components explained

| Component | What it does | Who cares most |
|-----------|--------------|----------------|
| **Model weights** | Files containing learned parameters (often many GB) | Ops (storage, integrity), dev (version) |
| **Tokenizer** | Text ↔ token IDs matching the model | Dev (encoding bugs cause garbage output) |
| **Inference runtime** | Loads weights, runs forward pass efficiently | Ops, dev (Ollama, vLLM, llama.cpp, TGI, etc. — **categories**, not one mandatory tool) |
| **API layer** | OpenAI-compatible or custom REST/gRPC | Dev, security (authn/z) |
| **Orchestrator** | Chains RAG, tools, prompt templates | Dev |
| **Observability** | Metrics, logs, traces | Ops |
| **Queue / batch** | Smooths traffic spikes | Ops at scale |

### 3.3 Open-weight models (concept)

**Open-weight** (or openly licensed) models let you download and run weights locally, subject to license terms.

| Consideration | Action |
|---------------|--------|
| **License** | Legal review — commercial use, attribution, restrictions |
| **Approved list** | Only vetted models in production |
| **Provenance** | Download from trusted sources; verify checksums |
| **Version pinning** | `model-name + revision + quantization` recorded in config |

**Staff message:** Do not install random models from the internet on production servers without governance.

### 3.4 Model size — parameters (7B, 13B, 70B…)

**Parameters** approximate model capacity (billions of weights).

| Size band | Intuition | Typical trade-off |
|-----------|-----------|-------------------|
| **Small (≈1–8B)** | Faster, less VRAM, good for narrow tasks | Weaker reasoning / long context on hard tasks |
| **Medium (≈13–34B)** | Balance for many org assistants | Needs serious GPU(s) at full precision |
| **Large (≈70B+)** | Stronger quality on complex tasks | Multi-GPU or heavy quantization |

**There is no universal “best” size** — only best for your hardware, latency budget, and quality bar.

### 3.5 Quantization — fitting models on hardware

**Quantization** reduces numeric precision of weights (e.g. 16-bit → 8-bit → 4-bit).

| Aspect | Effect |
|--------|--------|
| **Memory** | Smaller footprint — model fits on GPU |
| **Speed** | Often faster inference |
| **Quality** | May slightly degrade on edge tasks |

**Analogies facilitators can use:**

- Full precision = original photo  
- Quantization = compressed JPEG — usually fine, sometimes artifacts on fine detail  

**Ops note:** Document `Q4_K_M` vs `Q8_0` (or your standard) in the runbook — swapping quantizations is a **deployment change**, not a silent tweak.

### 3.6 GPU vs CPU inference

| | **GPU** | **CPU** |
|---|---------|---------|
| **Speed** | Much faster for typical LLMs | Slow for larger models; OK for tiny models / low traffic |
| **Cost** | Hardware expensive | Uses existing servers |
| **Use case** | Production assistants, concurrent users | Dev/test, edge, burst fallback with patience |
| **Ops focus** | Drivers, CUDA, VRAM exhaustion | RAM, thread count, realistic SLA |

**Facilitator line:**

> “Asking a CPU to serve a 70B model to 50 concurrent users is like asking one clerk to process every airport desk — plan hardware to match promises.”

### 3.7 Context length and concurrent users

Two independent pressures:

1. **Per-request memory** grows with context length (long prompts cost more VRAM).  
2. **Concurrent requests** multiply load — batching helps but has limits.

**Operators** must align marketed limits (max upload pages, max users) with measured benchmarks — not vendor marketing sheets alone.

### 3.8 What happens during one local inference request

```
1. API receives authenticated request
2. Orchestrator builds prompt (system + RAG chunks + user)
3. Tokenizer encodes prompt
4. Runtime loads model (if not already loaded — “cold start”)
5. GPU computes token by token
6. Stream tokens back to client
7. Log metadata (latency, tokens, model version) — not secrets per policy
```

**Cold start:** First request after idle may be slow while weights load into VRAM — plan keep-warm strategy if SLAs are tight.

---

## 4. Hardware Specifications — Reference Guide (18 min)

*Planning numbers — always benchmark on your model, quantization, and RAG load. Treat tables as starting points, not guarantees.*

### 4.1 Key hardware concepts

| Term | Meaning | Why it matters |
|------|---------|----------------|
| **GPU** | Graphics processor used for parallel matrix math | Primary inference accelerator for LLMs |
| **VRAM** | GPU memory (dedicated video RAM) | Model weights + activations must fit here for GPU inference |
| **RAM** | System memory | CPU inference, caching, RAG services, vector DB |
| **CPU** | General processor | Orchestration, preprocessing, small models, embedding models |
| **NVMe SSD** | Fast storage | Model weight files (often 4–140+ GB each) |
| **PCIe / NVLink** | GPU–host or GPU–GPU links | Multi-GPU throughput |
| **TPS** | Tokens per second | Throughput metric for inference |
| **Batch size** | Concurrent sequences processed together | Higher batch → more VRAM, better utilization |

### 4.2 GPU classes (typical roles)

| Tier | Example classes (indicative) | Typical org role |
|------|------------------------------|------------------|
| **Entry GPU** | 8–12 GB VRAM consumer (e.g. RTX 3060/4060 class) | Dev/test, tiny models, embeddings |
| **Workstation** | 24–48 GB (e.g. RTX 4090, A5000/A6000) | Single medium model, low–medium concurrency |
| **Data center** | 40–80 GB (e.g. A100 40GB/80GB, H100 80GB) | Production 7B–70B, higher concurrency |
| **Multi-GPU server** | 2–8× datacenter GPUs | 70B+ full precision, high throughput |

**Facilitator line:**

> “VRAM is the hard ceiling. A model that doesn’t fit in VRAM (with context and concurrency) will OOM or spill to slow paths.”

### 4.3 VRAM planning — model weights (approximate)

**Rule of thumb for weight memory:**

| Precision | Bytes per parameter (approx.) |
|-----------|------------------------------|
| **FP32** | 4 bytes |
| **FP16 / BF16** | 2 bytes |
| **INT8** | 1 byte |
| **INT4 (Q4)** | ~0.5 bytes |

**Example weight-only sizes:**

| Model params | FP16 weights | Q4 quantized weights |
|--------------|--------------|----------------------|
| **7B** | ~14 GB | ~4–5 GB |
| **13B** | ~26 GB | ~7–8 GB |
| **34B** | ~68 GB | ~18–20 GB |
| **70B** | ~140 GB | ~35–40 GB |

**Add overhead for inference (rough guide):**

| Factor | Extra VRAM impact |
|--------|-------------------|
| **KV cache** (context) | Grows with `context_length × batch × layers` — long prompts are expensive |
| **CUDA / runtime** | ~1–2 GB baseline |
| **Concurrent requests** | Multiplies KV cache needs |

**Practical examples (single request, ballpark):**

| Setup | Often fits when |
|-------|-----------------|
| **7B Q4** | 8 GB GPU (tight) — **12 GB+** recommended |
| **7B FP16** | 16 GB+ GPU |
| **13B Q4** | 16 GB GPU |
| **70B Q4** | 48 GB GPU or multi-GPU |
| **70B FP16** | 2× 80 GB or equivalent |

Always leave **10–20% VRAM headroom** for spikes and driver overhead.

### 4.4 CPU & RAM specifications

| Component | Dev / test | Production orchestration node | CPU-only inference (small model) |
|-----------|------------|------------------------------|--------------------------------|
| **CPU cores** | 8+ | 16–32+ | 32–64+ (more threads help) |
| **System RAM** | 32 GB | 64–128 GB | 128–256 GB+ for larger models |
| **Use** | Local experiments | API gateway, RAG, vector DB, queues | Fallback or air-gapped tiny LLM |

**Embedding & RAG services** (often separate from LLM GPU):

| Service | RAM guide (indicative) |
|---------|------------------------|
| **Embedding model (CPU)** | 2–8 GB depending on model |
| **Vector DB** | Dataset size + index overhead (plan 2–4× raw embedding storage) |
| **Document parser / OCR** | 4–16 GB per worker |

### 4.5 Storage specifications

| Asset | Typical size | Notes |
|-------|--------------|-------|
| **7B model (Q4)** | 4–6 GB | Plus tokenizer, config |
| **13B model (Q4)** | 8–10 GB | |
| **70B model (Q4)** | 35–45 GB | |
| **70B model (FP16)** | 130–150 GB | |
| **Embedding model** | 0.1–2 GB | |
| **Vector index** | Millions of chunks → GB–TB | Depends on dimension × chunk count |
| **Logs / metrics** | Plan retention policy | Can grow fast if logging prompts |

| Storage type | Recommendation |
|--------------|----------------|
| **Model store** | NVMe SSD, checksum verified |
| **Vector DB** | SSD; replicas for HA |
| **Backups** | Config + indexes + source docs — weights re-downloadable |

### 4.6 Network specifications

| Path | Minimum guidance |
|------|------------------|
| **Client → API** | HTTPS, low latency LAN or DC network |
| **API → GPU node** | 1 Gbps+ internal; 10 Gbps+ at scale |
| **Model download** | One-time large transfer — plan mirror or offline media for air-gap |
| **Multi-node GPU** | High-speed interconnect (NVLink / InfiniBand) for tensor parallel |

### 4.7 Deployment tier templates (starter configurations)

*Customize after benchmark — document your org’s approved tiers.*

| Tier | Profile | Indicative hardware | Typical use |
|------|---------|---------------------|-------------|
| **T0 — Pilot** | 1× 12–24 GB GPU, 32–64 GB RAM | Workstation or single server | Internal pilot, &lt;10 users |
| **T1 — Team** | 1× 24–48 GB GPU, 64–128 GB RAM, NVMe | Production single-node | Department assistant, RAG FAQ |
| **T2 — Org** | 1–2× 80 GB GPU or 2× 48 GB, 128+ GB RAM | Rack server | Multi-team, moderate concurrency |
| **T3 — Scale** | 4–8× GPU, load balancer, HA vector DB | GPU cluster | Platform-wide, SLA-backed |

**Concurrency note:** “50 users” ≠ 50 simultaneous GPU-bound generations unless you queue or scale. Define **peak concurrent inference** separately from registered users.

### 4.8 Training vs inference hardware (cross-link Lecture 4)

| Phase | Hardware profile |
|-------|------------------|
| **Pre-training** | Hundreds–thousands of GPUs, months ([04_genai_llm.md](04_genai_llm.md) §4) |
| **Fine-tuning (LoRA)** | 1–8 GPUs, hours–days, smaller VRAM than full train |
| **Inference (your focus)** | Right-sized GPU for model + context + concurrency |

Most members operate **inference** — not pre-training clusters.

---

## 5. Software Stack Specifications (15 min)

### 5.1 Layered software map

```
┌─────────────────────────────────────────────────────────┐
│  Application (your product UI, bots, workflows)         │
├─────────────────────────────────────────────────────────┤
│  Orchestration (RAG, prompts, auth, rate limits)        │
├─────────────────────────────────────────────────────────┤
│  Inference API (OpenAI-compatible REST, gRPC)           │
├─────────────────────────────────────────────────────────┤
│  Inference runtime (vLLM, llama.cpp, TGI, Ollama, etc.) │
├─────────────────────────────────────────────────────────┤
│  ML framework glue (PyTorch, CUDA kernels, custom ops)  │
├─────────────────────────────────────────────────────────┤
│  GPU driver + CUDA / ROCm / oneAPI                      │
├─────────────────────────────────────────────────────────┤
│  OS (Linux preferred for production)                    │
├─────────────────────────────────────────────────────────┤
│  Firmware / hardware (GPU, BIOS, BMC)                   │
└─────────────────────────────────────────────────────────┘
```

### 5.2 Operating system

| Environment | Typical choice | Notes |
|-------------|----------------|-------|
| **Production GPU server** | Ubuntu 22.04 / 24.04 LTS, RHEL | Standardize one image |
| **Windows** | Dev laptops, some edge deploys | WSL2 for local dev; production often Linux |
| **Container** | Docker / Kubernetes on Linux | Pin images; GPU operator required on K8s |

### 5.3 GPU software chain (NVIDIA example — most common)

| Layer | Purpose | Ops responsibility |
|-------|---------|-------------------|
| **NVIDIA driver** | Host ↔ GPU | Pin version; test before upgrade |
| **CUDA toolkit** | GPU compute libraries | Match runtime requirements |
| **cuDNN / NCCL** | Optimized ops, multi-GPU | Bundled or explicit per runtime |
| **PyTorch / custom** | Model execution | Often inside runtime container |

**Version pinning:** Driver + CUDA + runtime must be **compatible**. Upgrading one without testing breaks inference.

**AMD ROCm** and **Intel oneAPI** follow similar stacking for non-NVIDIA hardware.

### 5.4 Inference runtime categories

| Runtime style | Characteristics | When teams pick it |
|---------------|-----------------|-------------------|
| **llama.cpp / GGUF** | CPU-friendly, quantization-native | Edge, CPU fallback, dev laptops |
| **Ollama** | Easy local pull & run | Dev, demos — govern for production |
| **vLLM** | High-throughput GPU serving, PagedAttention | Production API, concurrency |
| **TGI (Text Generation Inference)** | Hugging Face ecosystem, production | HF models, enterprise deploy |
| **TensorRT-LLM** | NVIDIA-optimized | Maximum perf on NVIDIA hardware |
| **ONNX Runtime** | Cross-platform | Specific export pipelines |

**Org policy:** Maintain an **approved runtime list** with supported model formats (GGUF, Safetensors, etc.).

### 5.5 Supporting services (full local stack)

| Service | Common options (categories) | Spec notes |
|---------|----------------------------|------------|
| **Vector database** | pgvector, Milvus, Qdrant, Weaviate, Elasticsearch hybrid | RAM, disk, replication |
| **Embedding service** | sentence-transformers, dedicated small models | Often CPU; batch for throughput |
| **API gateway** | Kong, NGINX, cloud edge, custom | TLS, auth, rate limits |
| **Queue** | Redis, RabbitMQ, Kafka | Async jobs, spike smoothing |
| **Observability** | Prometheus, Grafana, Loki, OpenTelemetry | Metrics + log retention policy |
| **Secrets** | Vault, cloud KMS, sealed secrets | API keys, DB passwords |

### 5.6 Model artifact formats

| Format | Notes |
|--------|-------|
| **Safetensors** | Common Hugging Face weight format |
| **GGUF** | Quantized, llama.cpp ecosystem |
| **ONNX** | Exported for specific runtimes |
| **Tokenizer files** | `tokenizer.json`, `tokenizer.model` — must match weights |

**Deploy checklist:** `model weights + tokenizer + config.json + generation config` as a versioned bundle.

### 5.7 Software version matrix (template — fill for your org)

| Component | Staging version | Production version | Owner | Last verified |
|-----------|-----------------|-------------------|-------|---------------|
| OS image | | | Ops | |
| GPU driver | | | Ops | |
| CUDA / ROCm | | | Ops | |
| Inference runtime | | | Ops/Dev | |
| LLM model + quant | | | ML lead | |
| Embedding model | | | Dev | |
| Vector DB | | | Ops | |

### 5.8 Security & compliance software controls

| Control | Implementation |
|---------|----------------|
| **TLS 1.2+** | All external API endpoints |
| **mTLS** | Internal service mesh (optional) |
| **RBAC / OAuth** | User and service authentication |
| **Network policy** | GPU nodes no public ingress |
| **Image scanning** | Container vulnerabilities |
| **Audit logging** | Admin actions, model changes |

---

## 6. Prepare — Before You Run Local LLM in Production (12 min)

### 6.1 Governance & security

| Item | Description |
|------|-------------|
| **Approved model registry** | Name, version, license, owner, review date |
| **Data classification** | What document tiers may use which environment |
| **Network zoning** | Inference VLAN, no direct public internet on GPU nodes if policy requires |
| **Secrets** | API keys in vault; no keys in repos |
| **Access control** | RBAC on admin endpoints; audit admin actions |
| **Retention** | Prompt/log retention limits and deletion process |

### 6.2 Infrastructure preparation

| Item | Description |
|------|-------------|
| **Hardware sizing** | Benchmark on realistic prompts; include RAG overhead |
| **Storage** | Fast disk for model files; checksum on deploy |
| **Drivers & OS image** | Standardized GPU image; documented versions |
| **High availability** | Second node, load balancer, or honest “maintenance mode” UX |
| **Backup** | Configs, prompts templates, **not** necessarily multi-TB weights (re-download with verification) |
| **Capacity playbook** | What happens at 80% GPU memory — queue, reject, scale |

### 6.3 Application preparation (developers)

- OpenAI-compatible API abstraction (easier to swap local/cloud)  
- Timeouts, retries with idempotency care, circuit breakers  
- Streaming responses for perceived latency  
- Structured errors to UI (“model overloaded” vs “policy blocked”)  
- Evaluation suite tied to model **and** prompt version  

### 6.4 Organizational preparation (all staff)

- Training on **approved** entry points only (no shadow IT pasting into random local ports)  
- Clear list of what data classes are allowed  
- Named owners: platform ops, ML lead, security liaison, product owner  

---

## 7. Operate — Day-to-Day & Incidents (15 min)

### 7.1 Monitoring dashboard (minimum viable)

| Metric | Why it matters |
|--------|----------------|
| **Request rate & queue depth** | Capacity planning |
| **p50 / p95 latency** | User experience |
| **Tokens in / out per minute** | Cost and heat |
| **GPU utilization & VRAM** | OOM kills if ignored |
| **Error rate** | Timeouts, 500s, CUDA OOM |
| **Model version label** | Debug drift after upgrades |
| **Health check** | Synthetic prompt every N minutes |

### 7.2 Routine operations

| Cadence | Tasks |
|---------|-------|
| **Daily** | Alert review, disk space, failed health checks |
| **Weekly** | Quality spot-check, capacity trend, security patches schedule |
| **Per release** | Run eval suite, compare to baseline, staged canary |
| **Quarterly** | Revisit model approval, hardware forecast, disaster recovery drill |

### 7.3 Upgrades and rollbacks

**Treat model upgrades like database migrations:**

1. Download & verify new weights in staging  
2. Run automated + human eval ([04_genai_llm.md](04_genai_llm.md) eval sets)  
3. Canary % traffic or internal-only period  
4. Rollback path: previous weights still on disk, previous config tagged  

**Communicate** to support before external-facing changes.

### 7.4 Incident types & first responses

| Incident | Symptoms | First response |
|----------|----------|----------------|
| **GPU OOM** | Sudden 500s, CUDA errors | Reduce concurrent requests, lower max context, restart runtime |
| **Model unload / crash** | Cold starts, timeouts | Auto-restart policy; investigate driver logs |
| **Disk full** | Cannot load model | Clear logs, expand volume, alert thresholds |
| **Quality regression** | “Answers got worse” | Check version pin, RAG index freshness, prompt change |
| **Suspected data leak** | Logs contain PII | Stop logging content, incident per security policy |
| **Total outage** | Health checks fail | Status page / support script; failover message |

### 7.5 Runbook snippet (example)

```
INCIDENT: Local LLM high latency
1. Check GPU VRAM and utilization
2. Check queue depth and error rate
3. If OOM → reduce max_tokens or concurrency cap
4. If single node → consider maintenance banner
5. Notify product owner if SLA breach > 15 min
6. Post-mortem: benchmark before next scale promise
```

---

## 8. Develop — Building on Local LLM (10 min)

### 8.1 Integration patterns

| Pattern | Description |
|---------|-------------|
| **Sync API** | Simple request/response |
| **Streaming SSE** | Token stream to UI |
| **Async jobs** | Long summaries via queue + notification |
| **Batch** | Overnight processing of document sets |
| **Router** | Route by sensitivity, language, or task type |

### 8.2 Performance tactics (conceptual)

- **Keep model loaded** — avoid cold starts in peak hours  
- **Limit retrieved context** — RAG quality vs speed ([06_rag.md](06_rag.md))  
- **Cache** — identical FAQ queries (watch staleness)  
- **Smaller model for routing** — classify intent cheaply, large model for hard step  
- **Prompt compression** — summarize chat history instead of sending full thread  

### 8.3 Testing locally before production

| Test type | Goal |
|-----------|------|
| **Smoke** | Model responds coherently |
| **Regression** | Golden prompts unchanged within tolerance |
| **Load** | N concurrent users at target p95 |
| **Security** | Injection attempts, auth bypass attempts |
| **Failover** | Behavior when GPU node down |

### 8.4 Developer responsibilities summary

- Never hardcode model paths without config management  
- Tag traces with `model_version`, `prompt_version`, `rag_index_version`  
- Document breaking API changes for frontend and support  

---

## 9. Serve Customers & Internal Users (8 min)

### 9.1 Setting honest expectations

| Tell users | Avoid |
|------------|-------|
| Answers are AI-generated and may be wrong | “Guaranteed accurate” |
| Sensitive workflows stay on organization-controlled systems | Implying all AI is local if hybrid |
| Maintenance windows may apply | Silent upgrades without communication |
| Report bad answers with steps to reproduce | Dismissing reports as “user error” |

### 9.2 Support playbook essentials

1. **Triage:** Outage vs quality vs policy vs user mistake  
2. **Collect:** Time, feature, model version (from about screen), screenshot — not full classified content in ticket if prohibited  
3. **Escalate:** Ops for outage; dev for reproducible quality bug; security for leak  
4. **Communicate:** Template status messages during incidents  

### 9.3 SLA language (leadership + product)

 Tie public commitments to **measured** local capacity:

- “Best effort” vs “99% monthly availability”  
- Max response time under defined load  
- Explicit exclusion: third-party model license changes, force majeure  

---

## 10. Cost & Sizing — Worksheet (7 min)

**Discussion exercise** (no single right answer):

| Input | Your org estimates |
|-------|-------------------|
| Peak concurrent users | ___ |
| Avg prompt + completion tokens | ___ |
| Target p95 latency | ___ s |
| Required context length | ___ tokens |
| Data classification tier | ___ |
| Availability tier | ___ |

**Outcome:** Drives GPU count, model size, quantization choice, and whether hybrid cloud is needed.

**Rule of thumb for facilitators:** Under-size hardware → slow product and shadow IT to cloud tools → **worse** privacy story.

---

## 11. Activity — “Who Owns It?” (10 min)

Match task to primary owner (ops / dev / security / staff / leadership):

| Task | Owner |
|------|-------|
| Approve new open-weight model license | Leadership + legal + security |
| Pin model version in production config | Dev + ops |
| Patch GPU driver on inference node | Ops |
| Write system prompt for funding FAQ bot | Staff + dev |
| Respond to “system is slow today” tickets | Support → ops |
| Decide citizen data may not use cloud API | Leadership + security |
| Run eval suite before upgrade | Dev |
| Verify AI draft before minister briefing | Staff |

**Debrief:** Local LLM success is **cross-functional**, not “the GPU team alone.”

---

## 12. Q&A — Prepared Answers

**Q: How much VRAM for our 13B Q4 model with 8k context?**  
A: Start with weight table (~8 GB) + KV overhead + 2 GB buffer — benchmark; likely 16 GB class GPU minimum.

**Q: Can we run LLM and vector DB on one machine?**  
A: Yes for pilots; at scale split — vector search and GPU compete for RAM/disk I/O.

**Q: Which CUDA version?**  
A: Whatever your pinned runtime documents — do not upgrade drivers in production without staging tests.

**Q: Linux or Windows for production?**  
A: Linux dominates for GPU serving; standardize to reduce ops surprise.

**Q: Can we run without GPUs?**  
A: Possible for small models and low traffic; set expectations accordingly.

**Q: Is local immune to prompt injection?**  
A: No. Security issues move with you ([04_genai_llm.md](04_genai_llm.md)).

**Q: How often should we upgrade models?**  
A: When evals show net benefit — not every new release. Pin versions deliberately.

**Q: Staff laptop Ollama — is that “local LLM” for work?**  
A: Only if policy allows; usually **not** production governance. Risk of unvetted models and data leakage.

---

## 13. Slide Outline (24 slides)

1. Title & objectives  
2. Cloud API vs local — comparison table  
3. Why local — drivers  
4. Myths vs reality  
5. Hybrid routing  
6. Stack architecture diagram  
7. Model size & quantization  
8. GPU vs CPU  
9. **Hardware concepts — VRAM, RAM, TPS**  
10. **VRAM sizing table (7B–70B)**  
11. **Deployment tiers T0–T3**  
12. **Software stack layers**  
13. **Driver / CUDA / runtime pinning**  
14. Supporting services map  
15. Prepare — governance checklist  
16. Operate — monitoring & incidents  
17. Upgrade / rollback flow  
18. Develop — integration patterns  
19. Customer & support messaging  
20. Sizing worksheet  
21. Activity: who owns it?  
22. Takeaways  
23. Operator checklist  
24. Next → RAG & vectors ([06_rag.md](06_rag.md))

---

## 14. Takeaways

1. **Local LLM = control + responsibility** — data stays in bounds only if architecture and policy match.  
2. **VRAM is the binding constraint** for GPU inference — use weight + KV cache math before buying hardware.  
3. **Software stack must be pinned** — driver, CUDA, runtime, model bundle move together.  
4. **Tier templates (T0–T3)** help leadership approve realistic pilots vs production scale.  
5. **Upgrades are production events** — eval, canary, rollback, communicate.  
6. **Next:** [06_rag.md](06_rag.md) — vectorization, embeddings, and retrieval over your documents.

---

## 15. Operator Checklist (printable)

### Production readiness

- [ ] Model checksum verified; version in config management  
- [ ] GPU drivers pinned; standard OS image  
- [ ] Auth on all inference endpoints  
- [ ] Metrics + alerts configured  
- [ ] Runbooks linked from on-call wiki  
- [ ] Eval baseline stored for current version  
- [ ] Support notified of limits and escalation path  

### Monthly review

- [ ] Capacity trend vs forecast  
- [ ] License & security patch status  
- [ ] Sample quality review with product owner  
- [ ] Incident log lessons applied  
