# RAG — Retrieval-Augmented Generation

*For all members — developers, operators, staff, support, and leadership*  
*Grounding LLMs in organizational knowledge: concepts, pipeline, quality, and customer-facing practice*

---

## Module Overview

This is **Lecture 6** in the technical literacy track. **RAG (Retrieval-Augmented Generation)** connects a language model to **your** documents, databases, and knowledge bases so answers can be **grounded**, **cited**, and **updated** without retraining the model.

| Lecture | Topic |
|---------|--------|
| 4 | [Generative AI & LLMs](04_genai_llm.md) — the generator |
| 5 | [Local LLM](05_local_llm.md) — where inference runs |
| **6 (this)** | **RAG** — how organizational knowledge enters the loop |

**Duration:** 100–110 minutes (pipeline + vectorization depth + quality + activity)  
**Prerequisites:** Lectures 4–5 ([04_genai_llm.md](04_genai_llm.md) tokens & neural nets; [05_local_llm.md](05_local_llm.md) hardware for vector DB)

---

## Learning Objectives

By the end, members should be able to:

1. Explain **why RAG exists** and when to use it vs prompting alone or fine-tuning  
2. Explain **vectorization, embeddings, tokens, and similarity search** — how they differ and connect  
3. Walk through the **full RAG pipeline** from document ingestion to cited answer  
4. Understand chunking, vector indexes, distance metrics, and reranking  
5. Identify what **prepare / operate / develop / serve** means for RAG specifically  
6. Diagnose common failure modes (“ignored docs,” wrong chunks, stale index, embedding mismatch)  
7. Communicate RAG capabilities honestly to customers and stakeholders  

---

## 1. Opening (5 min)

**Facilitator message:**

> “An LLM without RAG is like a well-read generalist who has **not** seen your filing cabinet. RAG opens the right drawer before speaking — but someone must **organize the cabinet**, **control access**, and **check the answer** before it goes external.”

**Icebreaker:**

- Name a document set users ask about repeatedly (policies, funding guides, patent manuals, FAQs).  
- What goes wrong if the AI answers from memory only?

---

## 2. The Problem RAG Solves (10 min)

### 2.1 Limits of “raw” LLM for organizational work

| Problem | Example in research / public-sector context |
|---------|-----------------------------------------------|
| **No access to internal files** | Latest internal funding circular not in training data |
| **Stale knowledge** | Model cites 2022 procedure; 2025 amendment exists |
| **Hallucinated specifics** | Invented grant clause or fake paper DOI |
| **No provenance** | User cannot verify where wording came from |
| **Cannot reflect live systems** | Applicant status in database — LLM alone cannot know |

### 2.2 What RAG adds

**RAG** = at question time, **retrieve** relevant passages from approved sources, **augment** the prompt with them, then **generate** an answer conditioned on that evidence.

**One sentence:**  
> **RAG turns the LLM from a general speaker into a speaker with your reference material on the desk.**

### 2.3 RAG vs other approaches

| Approach | What changes | Best when |
|----------|--------------|-----------|
| **Prompt only** | Instructions in system/user message | Generic writing, no org-specific facts |
| **RAG** | Retrieves text at query time | Q&A over manuals, policies, research libraries |
| **Fine-tuning** | Model weights adapted | Style/format, specialized phrasing — **not** a substitute for daily doc updates |
| **Tool / API call** | Live query to systems | Real-time status, calculations, transactions |
| **RAG + tools** | Retrieval + live data | “Summarize policy X and check if project Y is eligible in CRM” |

**Facilitator line:**

> “Don’t fine-tune what you can retrieve. Don’t retrieve what you can query live.”

---

## 3. End-to-End RAG Pipeline (25 min)

### 3.1 Master diagram

```
INGESTION (offline / batch)                QUERY (online)
─────────────────────────                ─────────────────

 ┌─────────────┐
 │  Sources    │  PDF, HTML, wiki,
 │             │  SharePoint, DB export
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │  Extract    │  text, tables, metadata
 │  & clean    │  (OCR if scanned)
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │  Chunk      │  split into segments
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │  Embed      │  chunk → vector
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │  Index      │  vector DB + metadata
 │  (store)    │  access control tags
 └─────────────┘

                            User question
                                 │
                                 ▼
                          ┌─────────────┐
                          │  Embed     │  query → vector
                          │  query     │
                          └──────┬──────┘
                                 ▼
                          ┌─────────────┐
                          │  Retrieve  │  top-k similar chunks
                          │  (+filter) │  + optional rerank
                          └──────┬──────┘
                                 ▼
                          ┌─────────────┐
                          │  Build      │  system rules +
                          │  prompt     │  chunks + question
                          └──────┬──────┘
                                 ▼
                          ┌─────────────┐
                          │  LLM       │  Local or API
                          │  generate  │  ([04](04_genai_llm.md), [05](05_local_llm.md))
                          └──────┬──────┘
                                 ▼
                          ┌─────────────┐
                          │  Response  │  answer + citations
                          │  + cites   │  + “insufficient info”
                          └─────────────┘
```

### 3.2 Ingestion — sources and extraction

| Source type | Considerations |
|-------------|----------------|
| **PDF** | Layout, headers, footers; scanned pages need OCR |
| **Office docs** | Styles map to structure |
| **Web / wiki** | URL, version, crawl frequency |
| **Databases** | Export views; don’t dump secrets |
| **Email / tickets** | Usually exclude — PII and noise |

**Staff role:** Source documents should be **canonical** — RAG amplifies mess if the corpus is outdated or contradictory.

**Extraction quality issues:**

- Tables split across pages → broken facts  
- Headers repeated on every page → retrieval noise  
- Appendices without context → chunks lack meaning  

### 3.3 Chunking — how you split matters

**Chunk** = retrieval unit (paragraph, section, fixed token window, etc.).

| Strategy | Pros | Cons |
|----------|------|------|
| **Fixed size (e.g. 512 tokens)** | Simple, predictable | May cut mid-thought |
| **Structure-aware (by heading)** | Preserves sections | Needs good document structure |
| **Overlapping windows** | Reduces boundary loss | More storage, duplicate retrieval |
| **Parent-child** | Small child for search, large parent for context | More complex index |

**Rules of thumb:**

- Procedures: chunk by **step section**, not arbitrary 200 tokens  
- Legal/policy: preserve **article numbering** in metadata  
- FAQs: one Q+A pair per chunk often works well  

**Developers** experiment with chunk size against eval queries. **Operators** reindex when strategy changes.

### 3.4 Embeddings & vectorization — core concepts

**Vectorization** = turning text (or other data) into a **fixed-length list of numbers** (a **vector**) that a computer can compare mathematically.

**Embedding** = the output of that process — the vector — produced by an **embedding model** (a neural network trained for similarity, not for chat).

```
Text:  "extension of grant submission deadline"
         │
         ▼  embedding model (neural network)
Vector: [0.031, -0.122, 0.894, ... ]   ← often 384, 768, 1024, or 1536 dimensions
```

Similar **meaning** → vectors **close** in space.  
Unrelated meaning → vectors **far apart**.

```
"grant deadline extension"  ≈  "postponement of submission date"
"grant deadline extension"  ≉  "exhibition booth layout"
```

### 3.5 Tokens vs embeddings — do not confuse them

Both appear in RAG + LLM systems but serve **different jobs**:

| | **LLM tokens** ([04](04_genai_llm.md) §5) | **Embedding vectors** (this lecture) |
|---|---------------------------------------------|--------------------------------------|
| **Purpose** | Input/output units for **generation** | **Search** and similarity |
| **Model** | Large language model (7B–70B+) | Smaller embedding model (often 100M–1B) |
| **Output** | Next token from vocabulary | Dense float vector |
| **Size** | One ID per token | e.g. 768 floats per chunk |
| **When** | Every generation step | Index time + query time for retrieval |
| **Changes when** | Swap LLM | Must **reindex** entire corpus if embedding model changes |

**Chunking uses tokens** (chunk size often defined as 256–1024 **tokens**).  
**Retrieval uses embeddings** (one vector per chunk).

**Facilitator line:**

> “**Tokenize** to slice text for the LLM’s appetite. **Embed** to place slices on a meaning map for search.”

### 3.6 How embedding models work (conceptual)

Embedding models are **neural networks** (often Transformer encoders) trained with objectives like:

| Training idea | Result |
|---------------|--------|
| **Similar pairs closer** | Paraphrases map nearby |
| **Dissimilar pairs farther** | Unrelated topics separate |
| **Contrastive learning** | Model learns a semantic space |

**Forward pass at index/query time:**

```
1. Tokenize input text (subword tokens — same BPE idea as LLMs)
2. Run through encoder layers (attention + pooling)
3. Pool token representations → single vector (e.g. mean pooling or [CLS] token)
4. Optionally L2-normalize (unit length) for cosine similarity
5. Output: embedding vector stored or compared
```

Members do **not** train embedding models from scratch in most orgs — you **select** a pretrained model and pin the version.

### 3.7 Vector dimensions & storage specs

| Embedding model (examples of sizes) | Dimensions | Rough vector size (float32) |
|-------------------------------------|------------|----------------------------|
| Small / fast | 384 | ~1.5 KB per vector |
| Medium | 768 | ~3 KB per vector |
| Large | 1024–1536 | ~4–6 KB per vector |

**Index storage estimate:**

```
vectors = chunk_count × dimension × bytes_per_float
        ≈ 1,000,000 chunks × 768 × 4 bytes  ≈ 3 GB (vectors only)
```

Add metadata, HNSW graph overhead, replicas — plan **2–4×** for production headroom ([05_local_llm.md](05_local_llm.md) storage section).

### 3.8 Similarity & distance metrics

Vector DBs compare query vector **q** to stored vectors **v**:

| Metric | Intuition | Common use |
|--------|-----------|------------|
| **Cosine similarity** | Angle between vectors; ignores magnitude | Normalized embeddings — **most common** |
| **Dot product** | Magnitude-sensitive similarity | Some optimized indexes |
| **Euclidean (L2) distance** | Straight-line distance in space | k-NN when not normalized |

**Cosine similarity (intuition):**  
Score near **1** = very similar direction (meaning).  
Score near **0** = unrelated.  
(Implementation details vary — some APIs return distance instead of similarity; read your DB docs.)

**Score thresholds:** Below threshold → return “insufficient information” instead of forcing the LLM to hallucinate.

### 3.9 Vector indexes — how search scales

Brute-force compare query to millions of vectors is too slow. **Approximate Nearest Neighbor (ANN)** indexes trade tiny accuracy for speed.

| Index type | Idea |
|------------|------|
| **HNSW** (Hierarchical Navigable Small World) | Graph navigation to nearby vectors — popular default |
| **IVF** (Inverted file) | Cluster buckets; search promising clusters only |
| **Flat / exact** | Small corpora; ground truth for eval |

| Parameter | Effect |
|-----------|--------|
| **ef_search / probe count** | Higher → better recall, slower |
| **M (HNSW)** | Graph connectivity — build-time trade-off |

**Ops:** Reindex time grows with corpus; plan maintenance windows.

### 3.10 Hybrid search — vectors + keywords

Embeddings miss **exact IDs**, **SKUs**, **legal cite numbers**, rare acronyms. **Hybrid** combines:

```
Semantic score (embedding)  +  Keyword score (BM25 / full-text)
                    ↓
              merged ranking
```

| Query type | Best signal |
|------------|-------------|
| “What is the policy on conflict of interest?” | Semantic |
| “Circular ST-2024-0172 section 3” | Keyword |
| “Grant extension procedure” | Hybrid |

### 3.11 Reranking — second-pass precision

**Bi-encoder (embedding):** Fast — embed query and chunks separately, compare vectors.  
**Cross-encoder (reranker):** Slower — jointly scores each (query, chunk) pair; more accurate.

**Typical pipeline:**

```
Retrieve top 50 with embedding search
        → rerank to top 5
        → send top 5 to LLM
```

**Hardware:** Rerankers often run on **CPU** or small GPU; budget latency accordingly.

### 3.12 Embedding software & hardware specs

| Component | Typical spec (indicative) |
|-----------|---------------------------|
| **Embedding model (CPU)** | 2–8 GB RAM, 2–8 cores |
| **Embedding model (GPU)** | Optional batch speedup on shared GPU |
| **Batch size** | Larger batches → higher throughput for indexing |
| **Vector DB** | Dedicated SSD; RAM for hot indexes; replicas for HA |
| **Latency target** | Retrieve p95 &lt; 100–500 ms before LLM call |

**Software categories:** sentence-transformers, ONNX exports, dedicated embedding APIs, vector DB plugins (pgvector, etc.).

**Version pinning:** Record `embedding_model_name + revision` in index metadata — mismatch causes silent quality collapse.

### 3.13 Index and metadata

Stored per chunk typically:

- Vector  
- Raw text  
- **Metadata:** title, source URL, doc version, date, department, classification, ACL  

**Access control is non-negotiable:**

```
User A (Dept X) → filter index → only chunks tagged Dept X or public
```

RAG without ACL → **data leak via similarity**.

### 3.14 Retrieval — finding the right chunks

| Step | Purpose |
|------|---------|
| **Similarity search** | Top-k nearest vectors |
| **Metadata filter** | Date range, doc type, tenant, clearance |
| **Hybrid** | Combine vector + keyword (BM25) for exact tokens |
| **Reranking** | Second model scores query–chunk pairs for precision |
| **Query transformation** | Rephrase user question for better recall |

**k (how many chunks):** Too few → missed context; too many → noise and context overflow ([04_genai_llm.md](04_genai_llm.md)).

### 3.15 Prompt assembly and generation

Typical template structure:

```
SYSTEM: You answer only from provided sources. Cite [Source ID].
        If insufficient, say "I don't have enough information."

CONTEXT:
[1] (Policy 2025-03, §4.2) ...
[2] (Funding FAQ, item 12) ...

USER: {question}
```

**Generation rules developers enforce:**

- Answer must cite chunk IDs  
- No fabrication beyond context  
- Refusal when retrieval score below threshold  

### 3.16 Response — citations and UI

**Good RAG UI shows:**

- Answer text  
- Clickable citations → source doc + highlight  
- Confidence or “low relevance” warning  
- Date/version of source document  

**Support benefit:** User can verify without trusting prose alone.

---

## 4. Unified Picture — From Raw Text to Retrieved Context (10 min)

*One diagram tying Lecture 4 tokens, Lecture 5 infra, and Lecture 6 vectors.*

```
RAW DOCUMENT
     │
     ├─► Extract text (OCR, parser)
     │
     ├─► Chunk by tokens/structure  ─── uses TOKENIZER (token counts)
     │
     ├─► Embed each chunk          ─── EMBEDDING MODEL → vector[d]
     │
     └─► Store in vector index     ─── VECTOR DB + metadata + ACL

USER QUESTION
     │
     ├─► Embed question            ─── same embedding model (critical)
     │
     ├─► ANN search + filters      ─── cosine / hybrid + ACL
     │
     ├─► Optional rerank           ─── cross-encoder
     │
     ├─► Build prompt with chunks  ─── TOKENIZER counts toward LLM context
     │
     └─► LLM generate answer       ─── LOCAL or API ([04], [05])
```

| Stage | Resource | Spec driver |
|-------|----------|-------------|
| Chunk + embed (batch) | CPU/GPU, RAM | Corpus size, embedding model |
| Vector index | RAM, SSD | `chunks × dimensions` |
| Retrieve | Vector DB QPS | Concurrent users |
| Generate | LLM GPU VRAM | Tokens in prompt + out |

**Training connection ([04](04_genai_llm.md) §4):** Embedding models are **trained** with contrastive loss; LLMs are **trained** for next-token prediction. RAG **combines** frozen embedding search + frozen (or pinned) LLM inference — no retraining when a new PDF arrives.

---

## 5. Quality — What Makes RAG Good or Bad (12 min)

### 5.1 Quality dimensions

| Dimension | Question to ask |
|-----------|-----------------|
| **Recall** | Did we retrieve the right chunk at all? |
| **Precision** | Did we avoid irrelevant chunks that confuse the model? |
| **Groundedness** | Is every claim supported by a citation? |
| **Completeness** | Does answer cover all parts of multi-hop questions? |
| **Freshness** | Is index up to date with published policy? |
| **Safety** | Are restricted docs excluded for this user? |

### 5.2 Common failure modes

| Symptom | Likely cause | Owner action |
|---------|--------------|--------------|
| “It ignored our policy” | Poor chunking, stale index, wrong collection | Reindex; fix chunks; check filters |
| Right doc, wrong answer | Noise chunks, weak prompt, model drift | Rerank; tighten k; eval prompt |
| Invented citation | Prompt not enforcing cite-only | Developer guardrails; lower temperature |
| “I don’t know” too often | Threshold too high; OCR garbage; wrong language | Tune thresholds; fix extraction |
| Leaked cross-department data | Missing ACL on metadata | Security fix — priority incident |
| Slow responses | Huge k, large chunks, cold GPU | Ops perf tuning ([05_local_llm.md](05_local_llm.md)) |
| Contradictory answers | Corpus has conflicting versions | Staff: retire old docs; version metadata |

| Same question, wildly different docs retrieved | Embedding mismatch / wrong model | Reindex; verify query uses same embedder |
| Good semantic match but wrong year | Missing date metadata filter | Add `effective_date` filters |

### 5.3 Evaluation — everyone’s responsibility, dev-led

**Golden question set:** Real user questions + expected source doc + properties (“must mention deadline date”).

| Metric type | Example |
|-------------|---------|
| **Retrieval hit rate** | Correct doc in top-5 |
| **Answer groundedness** | Manual or LLM-judge vs citations |
| **Regression** | Compare before/after index or model change |

Run evals on **every** index rebuild and model upgrade.

---

## 6. Prepare — Getting RAG Ready (10 min)

### 6.1 Corpus curation (staff + knowledge owners)

| Task | Detail |
|------|--------|
| **Canonical sources** | One official version per policy |
| **Retire superseded** | Archive or mark `effective_to` date |
| **Structure** | Headings, summaries, consistent titles |
| **PII scan** | Remove or redact before indexing |
| **Classification labels** | Match access control model |

### 6.2 Technical preparation (developers + ops)

| Task | Detail |
|------|--------|
| **Ingestion pipeline** | Scheduled + on-publish webhook |
| **Idempotent reindex** | Same doc update replaces old chunks |
| **Secrets** | No API keys in chunks |
| **Staging index** | Test before swap to production alias |
| **Embedding version** | Record in index metadata |
| **Disaster recovery** | Rebuild index from source docs |

### 6.3 Governance

- Approved corpora list (what may be RAG’d)  
- Process to add new collections (security review)  
- Retention aligned with document policy  

---

## 7. Operate — Running RAG in Production (10 min)

### 7.1 Monitoring

| Signal | Alert if |
|--------|----------|
| Ingestion lag | Published doc not searchable after SLA |
| Ingestion failures | OCR errors, parse failures spike |
| Retrieval latency | p95 above target |
| Empty retrieval rate | Sudden spike — broken embedder? |
| Low score rate | Users see “no info” often |
| User thumbs-down | Quality regression |

### 7.2 Operational cadence

| Event | Action |
|-------|--------|
| **New policy published** | Trigger reindex or incremental update |
| **Typos fixed in source** | Reindex affected docs |
| **Embedding model upgrade** | Full reindex + eval |
| **Major app release** | Verify collection mappings |

### 7.3 Incident examples

**Stale index after emergency policy change:**

1. Publish human notice: “AI assistant may not reflect update until HH:MM”  
2. Priority reindex  
3. Post-incident: on-publish hook  

**Wrong department saw restricted chunk:**

1. Disable collection; security incident  
2. Fix ACL filter; audit logs  
3. Reindex with corrected metadata  

---

## 8. Develop — Improving RAG Systems (10 min)

### 8.1 Tuning levers (in order)

1. **Corpus quality** — fixes more than clever code  
2. **Chunking & metadata**  
3. **Hybrid retrieval + rerank**  
4. **Prompt template & citation rules**  
5. **k, thresholds, filters**  
6. **LLM choice** ([04](04_genai_llm.md), [05](05_local_llm.md))  
7. **Fine-tuning** — last resort for tone/format  

### 8.2 Advanced patterns (awareness)

| Pattern | Use |
|---------|-----|
| **Multi-query retrieval** | Generate alternate phrasings of question |
| **Hypothetical document embedding (HyDE)** | Draft fake answer for search — niche use |
| **Agentic RAG** | Multiple retrieval steps — higher cost, complex tasks |
| **Graph RAG** | Linked entities (orgs, projects) — advanced |
| **Conversation memory** | Prior turns summarized; don’t blindly stuff full chat |

**Facilitator note:** Start simple; measure before adding complexity.

### 8.3 Anti-patterns

- Indexing everything “just in case”  
- No versioning — users cite deprecated PDF  
- Giant chunks “so we don’t miss context”  
- Skipping eval because “RAG is standard”  
- Letting user-uploaded files enter shared index without scan  

---

## 9. Serve Customers & Stakeholders (8 min)

### 9.1 What to promise

| Reasonable promise | Unreasonable promise |
|--------------------|----------------------|
| Answers use **named official sources** when found | Always complete and correct |
| Citations link to documents | Understands intent like a human lawyer |
| Says when sources are insufficient | Knows unpublished decisions |
| Updated on a defined schedule or event | Real-time unless you built that |

### 9.2 Example user-facing explanation

> “This assistant searches approved [organization] documents and drafts answers with references. Always check citations for official decisions. If something looks wrong, use the feedback button — we use reports to improve retrieval and content.”

### 9.3 Support scenarios

| User says | Support response outline |
|-----------|-------------------------|
| “It cited old policy” | Apologize; verify doc version in citation; flag for reindex; use human path |
| “It made up a section” | Treat as quality bug; collect session ID; don’t debate — escalate |
| “Why can’t it see my upload?” | Explain personal vs org corpus per product design |
| “Answer was slow” | Check status page; ops ticket if widespread |

### 9.4 Staff workflow integration

```
Research / policy owner maintains corpus
        ↓
RAG gives draft answer with cites
        ↓
Staff verifies against source
        ↓
Human approves external reply / publication
```

---

## 10. Full-Stack Picture — Lectures 4 + 5 + 6 Together (5 min)

```
                    CUSTOMER / STAFF USER
                              │
                    Application + auth + audit
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
    RAG retrieve         Tool APIs            Session policy
    (this lecture)       (live data)          (what allowed)
         │                    │                    │
         └────────────────────┼────────────────────┘
                              ▼
                    Local or API LLM inference
                    (Lectures 4 & 5)
                              ▼
                    Human review (high-stakes)
```

**Key message for all roles:** RAG improves **grounding**; it does not remove **governance** or **human accountability**.

---

## 11. Activity — Debug the RAG (15 min)

Groups receive symptoms — identify pipeline stage and fix owner.

| # | Symptom | Likely stage | Fix direction |
|---|---------|--------------|---------------|
| 1 | New FAQ live on web 48h ago, bot silent | Ingestion / schedule | Trigger crawl; fix webhook |
| 2 | Answer cites Doc A but misses obvious Doc B | Retrieval / chunking | Hybrid search; smaller chunks; metadata |
| 3 | Answer quotes Doc A correctly but adds fake bullet | Generation / prompt | Stricter cite-only prompt; eval |
| 4 | User from Team 1 sees Team 2 budget | Access control | Filter bug — security priority |
| 5 | Tables in PDF always wrong | Extraction | Better parser or manual HTML version |
| 6 | Same question, wildly different docs retrieved | Embedding mismatch | Reindex with current embed model |

**Debrief:** RAG debugging is **pipeline debugging**, not “re-prompt the user.”

---

## 12. Cross-Role Responsibility Matrix

| Task | Primary | Also involved |
|------|---------|---------------|
| Keep policy PDFs current | Staff / owners | Comms |
| Define ACL rules | Security | Dev, product |
| Build ingestion | Dev | Ops |
| Run vector DB | Ops | Dev |
| Tune retrieval | Dev | Staff (eval questions) |
| Approve new corpus | Leadership | Security, legal |
| Communicate limits | Support | Product |
| Verify cited answers | Staff | — |
| Incident: data leak | Security | Ops, leadership |

---

## 13. Q&A — Prepared Answers

**Q: What is vectorization in one sentence?**  
A: Converting text into a list of numbers so meaning-based similarity can be computed mathematically.

**Q: Is RAG the same as “upload PDF to ChatGPT”?**  
A: Similar idea, but enterprise RAG adds **pipelines, ACL, eval, monitoring, citations** — not a one-off upload.

**Q: Can RAG eliminate hallucinations?**  
A: Reduces **if** retrieval is good and prompts enforce cite-only. Does not eliminate model creativity or injection attacks.

**Q: How often reindex?**  
A: On document change ideally; nightly minimum for slow-changing corpora; define SLA with content owners.

**Q: Do we need a vector database?**  
A: At scale, yes — specialized index for fast similarity. Small pilots may use simpler stores; production usually needs purpose-built search.

**Q: What happens if we change embedding model without reindexing?**  
A: Query and document vectors live in **different semantic spaces** — retrieval quality collapses. Full reindex required.

**Q: How many dimensions should we use?**  
A: Follow your chosen embedding model — you do not pick dimensions independently. Trade-off is model choice (speed vs quality), not arbitrary vector length.

**Q: Cosine vs Euclidean?**  
A: Most text embeddings use cosine on normalized vectors; match your index and training convention.

**Q: Who owns wrong answers?**  
A: The organization — fix corpus, retrieval, prompt, or workflow; never blame the user for system limits.

---

## 14. Slide Outline (26 slides)

1. Title & objectives  
2. Problem: LLM alone in org context  
3. What RAG is — one sentence  
4. RAG vs fine-tune vs tools  
5. Master pipeline diagram  
6. Ingestion & extraction  
7. Chunking strategies (token-based)  
8. **Vectorization & embeddings**  
9. **Tokens vs embeddings — comparison table**  
10. **How embedding models work**  
11. **Dimensions & storage math**  
12. **Cosine similarity & thresholds**  
13. **ANN indexes (HNSW)**  
14. **Hybrid + rerank**  
15. Metadata & access control  
16. Unified text→vector→LLM diagram  
17. Prompt assembly & citations  
18. Quality dimensions  
19. Failure modes table  
20. Prepare — corpus curation  
21. Operate — monitoring  
22. Develop — tuning order  
23. Customer messaging  
24. Activity: debug the RAG  
25. Series recap  
26. **Glossary handout**

---

## 15. Takeaways — Full Series Recap

| Lecture | Remember |
|---------|----------|
| **4 — LLM** | Predicts language; training ≠ inference; verify outputs |
| **5 — Local** | You control infra and duty; size hardware to SLA |
| **6 — RAG** | Vectorize → retrieve → generate; tokens ≠ embeddings |

**Together:**

1. **Prepare** authoritative documents, **pinned embedding + LLM versions**, and clear policies.  
2. **Operate** indices, GPUs, embedders, and monitors — not just “the AI.”  
3. **Develop** with evals, hybrid search, and honest failure modes.  
4. **Serve** users with citations, humility, and human paths for high-stakes work.

---

## 16. Reference — Tokens, Vectors & RAG Glossary

| Term | Definition |
|------|------------|
| **Vectorization** | Mapping data (text) to a numeric vector |
| **Embedding** | The output vector representing meaning |
| **Embedding model** | Neural net that produces embeddings (not the chat LLM) |
| **Dimension** | Length of embedding vector (e.g. 768) |
| **Cosine similarity** | Similarity metric based on vector angle |
| **ANN** | Approximate nearest neighbor — fast vector search |
| **HNSW** | Graph-based ANN index |
| **BM25** | Keyword relevance scoring |
| **Hybrid search** | Semantic + keyword combined |
| **Bi-encoder** | Separate embeddings for query and document |
| **Cross-encoder / reranker** | Joint query–document scorer |
| **Chunk** | Retrieval unit of text |
| **k** | Number of chunks retrieved |
| **Reindex** | Rebuild all vectors after model or chunk change |
| **ACL filter** | Metadata restriction on searchable chunks |
| **Groundedness** | Answer supported by retrieved text |

---

## 17. Printable Checklists

### RAG production readiness

- [ ] Corpus owners named per collection  
- [ ] ACL tested with multiple user personas  
- [ ] Ingestion + reindex automated  
- [ ] Golden eval set ≥30 questions  
- [ ] Citation UI wired to source viewer  
- [ ] “Insufficient information” path tested  
- [ ] Support runbook for stale/wrong answers  

- [ ] Embedding model version recorded in index metadata  
- [ ] Vector storage sized from chunk count × dimensions  
- [ ] Hybrid search evaluated for ID-heavy queries  
- [ ] Reindex runbook for embedding model upgrades  

### Content owner monthly

- [ ] Review deprecated documents in index  
- [ ] Sample 5 RAG answers in your domain  
- [ ] Submit corrections to corpus or FAQ  

---

## 18. Where This Fits the Broader Curriculum

| Document | Role |
|----------|------|
| [01_concept.md](01_concept.md) | General AI literacy series overview |
| [03_gai_rai.md](03_gai_rai.md) | GenAI vs Recognition — introductory |
| [04_genai_llm.md](04_genai_llm.md) | LLM foundations — all members |
| [05_local_llm.md](05_local_llm.md) | On-prem inference — all members |
| **06_rag.md** | Organizational knowledge — all members |
| [07_prompting.md](07_prompting.md) | Prompting & assistant design |
| [08_trust_verification.md](08_trust_verification.md) | Trust & verification |
| [09_privacy_security.md](09_privacy_security.md) | Privacy & security |
| [10_evaluation_quality.md](10_evaluation_quality.md) | Evaluation & quality |
| [11_agents_tools.md](11_agents_tools.md) | Agents & tools |
| [12_serving_customers.md](12_serving_customers.md) | Support & action plan |
| [CURRICULUM.md](CURRICULUM.md) | Master plan & facilitator todos |

**Next in series:** [07_prompting.md](07_prompting.md). **Capstone:** [12_serving_customers.md](12_serving_customers.md) (team action plan workshop).
