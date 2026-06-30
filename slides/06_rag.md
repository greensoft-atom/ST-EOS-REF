# Slide Deck — RAG: Retrieval-Augmented Generation
*Source: [06_rag.md](../06_rag.md) | Facilitator use*

# Slide 01 | Title & Learning Objectives

## On screen

**Lecture 6 — RAG (Retrieval-Augmented Generation)**

*For all members — developers, operators, staff, support, leadership*

| By the end you can… |
|---------------------|
| Explain **why RAG exists** vs prompting alone or fine-tuning |
| Explain **vectorization, embeddings, tokens, similarity search** — how they differ |
| Walk through the **full RAG pipeline** from ingestion to cited answer |
| Understand chunking, vector indexes, distance metrics, reranking |
| Know **prepare / operate / develop / serve** for RAG specifically |
| Diagnose failure modes and communicate capabilities honestly |

**Prerequisites:** [04_genai_llm.md](../04_genai_llm.md), [05_local_llm.md](../05_local_llm.md)  
**Duration:** 100–110 min

## Speaker notes

Open: an LLM without RAG is like a well-read generalist who has **not** seen your filing cabinet. RAG opens the right drawer before speaking — but someone must organize the cabinet, control access, and check the answer before it goes external. *(~2 min)* Icebreaker: name a document set users ask about repeatedly (policies, funding guides, patent manuals). What goes wrong if AI answers from memory only? *(~3 min)*

---

# Slide 02 | Problem — LLM Alone in Org Context

## On screen

| Problem | Example (research / public sector) |
|---------|-------------------------------------|
| **No access to internal files** | Latest internal funding circular not in training data |
| **Stale knowledge** | Model cites 2022 procedure; 2025 amendment exists |
| **Hallucinated specifics** | Invented grant clause or fake paper DOI |
| **No provenance** | User cannot verify where wording came from |
| **Cannot reflect live systems** | Applicant status in database — LLM alone cannot know |

```
User: "What is the 2025 extension deadline for Form ST-12?"
LLM (no RAG): plausible date — may be wrong, no citation
LLM + RAG:    retrieves circular §4.2 → cites official source
```

## Speaker notes

These are daily failures in org settings — not edge cases. Patent example: invented prior-art reference. Funding example: wrong eligibility threshold stated confidently. Provenance matters for audit and public trust. *(~4 min)* Connect to Lecture 4 failure modes — RAG is the primary mitigation for org-specific facts. *(~2 min)*

---

# Slide 03 | What RAG Is — One Sentence

## On screen

**RAG** = at question time:

1. **Retrieve** relevant passages from approved sources  
2. **Augment** the prompt with that evidence  
3. **Generate** an answer conditioned on retrieved text  

> **RAG turns the LLM from a general speaker into a speaker with your reference material on the desk.**

**Does not replace:**
- Human review for high-stakes outputs
- Live system queries (CRM, databases) — use **tools**
- Good corpus curation — garbage in → garbage out

## Speaker notes

One sentence is the anchor — repeat it in debrief. RAG updates knowledge at query time without retraining weights. It does not eliminate hallucination if retrieval is poor or prompts don't enforce cite-only. Staff still verify before external publication. *(~3 min)*

---

# Slide 04 | RAG vs Fine-Tune vs Tools

## On screen

| Approach | What changes | Best when |
|----------|--------------|-----------|
| **Prompt only** | Instructions in system/user message | Generic writing, no org-specific facts |
| **RAG** | Retrieves text at query time | Q&A over manuals, policies, research libraries |
| **Fine-tuning** | Model weights adapted | Style/format, specialized phrasing — **not** daily doc updates |
| **Tool / API call** | Live query to systems | Real-time status, calculations, transactions |
| **RAG + tools** | Retrieval + live data | "Summarize policy X and check project Y eligibility in CRM" |

> **Don't fine-tune what you can retrieve. Don't retrieve what you can query live.**

| | Updates weights? | Daily doc updates? |
|---|------------------|-------------------|
| Fine-tune | Yes | Expensive |
| RAG | No | **Yes** — reindex corpus |
| Tools | No | Live data |

## Speaker notes

Decision tree facilitators use: org facts in documents → RAG; live status → tool; tone/format after RAG solid → maybe fine-tune. Fine-tuning on policy PDFs is usually wrong — policies change weekly; reindex is cheaper. *(~4 min)*

---

# Slide 05 | Master Pipeline Diagram

## On screen

```
INGESTION (offline)                      QUERY (online)
─────────────────                        ──────────────

 Sources → Extract → Chunk → Embed → Index
 (PDF, wiki, DB)   & clean              │
                                        User question
                                             │
                                             ▼
                                      Embed query
                                             │
                                             ▼
                                      Retrieve top-k
                                      (+ filter, rerank)
                                             │
                                             ▼
                                      Build prompt
                                             │
                                             ▼
                                      LLM generate
                                             │
                                             ▼
                                      Answer + citations
```

**Two phases:** batch indexing vs online retrieval + generation

## Speaker notes

Master map for the lecture — refer back during activity. Ingestion is often batch/scheduled; query is real-time. Both must use the **same embedding model** at index and query time. Walk slowly — 60 seconds of silence OK. *(~4 min)*

---

# Slide 06 | Ingestion & Extraction

## On screen

| Source type | Considerations |
|-------------|----------------|
| **PDF** | Layout, headers/footers; scanned pages need OCR |
| **Office docs** | Styles map to structure |
| **Web / wiki** | URL, version, crawl frequency |
| **Databases** | Export views; don't dump secrets |
| **Email / tickets** | Usually exclude — PII and noise |

**Extraction quality issues:**
- Tables split across pages → broken facts
- Headers repeated every page → retrieval noise
- Appendices without context → meaningless chunks

**Staff role:** Source documents must be **canonical** — RAG amplifies mess if corpus is outdated or contradictory.

## Speaker notes

Corpus curation is staff + knowledge owner work — not only dev. Example: two conflicting funding policy versions indexed → contradictory answers. Patent PDFs with complex tables often need better parser or HTML alternative. OCR garbage → "I don't know" or wrong answers. *(~4 min)*

---

# Slide 07 | Chunking Strategies

## On screen

**Chunk** = retrieval unit (paragraph, section, fixed token window)

| Strategy | Pros | Cons |
|----------|------|------|
| **Fixed size (e.g. 512 tokens)** | Simple, predictable | May cut mid-thought |
| **Structure-aware (by heading)** | Preserves sections | Needs good document structure |
| **Overlapping windows** | Reduces boundary loss | More storage, duplicates |
| **Parent-child** | Small child for search, large parent for context | More complex index |

**Rules of thumb:**
- Procedures: chunk by **step section**, not arbitrary 200 tokens
- Legal/policy: preserve **article numbering** in metadata
- FAQs: one Q+A pair per chunk often works

**Chunk size often defined in tokens** (256–1024) — uses tokenizer from embedding/LLM pipeline

## Speaker notes

Chunking is a top retrieval lever — developers experiment against eval queries; operators reindex when strategy changes. Bad chunking causes "it ignored our policy" when the right text exists but was split badly. Funding eligibility tables chunked mid-row → wrong thresholds retrieved. *(~5 min)*

---

# Slide 08 | Vectorization & Embeddings

## On screen

**Vectorization** = turning text into a **fixed-length list of numbers** (a **vector**)

**Embedding** = the output vector from an **embedding model** (neural net trained for similarity, not chat)

```
Text:  "extension of grant submission deadline"
         │
         ▼  embedding model
Vector: [0.031, -0.122, 0.894, ... ]   ← e.g. 384–1536 dimensions
```

Similar **meaning** → vectors **close** in space  
Unrelated meaning → vectors **far apart**

```
"grant deadline extension"  ≈  "postponement of submission date"
"grant deadline extension"  ≉  "exhibition booth layout"
```

## Speaker notes

Vectorization is the bridge between language and math search. Most orgs **select** a pretrained embedding model and pin the version — not train from scratch. Contrastive training makes paraphrases nearby. This is different from LLM next-token prediction (Lecture 4). *(~4 min)*

---

# Slide 09 | Tokens vs Embeddings — Comparison

## On screen

| | **LLM tokens** (Lecture 4) | **Embedding vectors** (this lecture) |
|---|----------------------------|--------------------------------------|
| **Purpose** | Input/output for **generation** | **Search** and similarity |
| **Model** | Large LLM (7B–70B+) | Smaller embedding model (100M–1B) |
| **Output** | Next token from vocabulary | Dense float vector |
| **When** | Every generation step | Index time + query time |
| **Swap model?** | Swap LLM | Must **reindex** entire corpus |

> **Tokenize** to slice text for the LLM's appetite. **Embed** to place slices on a meaning map for search.

**One chunk** → one embedding vector; **same chunk** → many LLM tokens when placed in prompt

## Speaker notes

This confusion causes silent production failures — embedding mismatch means query and document vectors live in different semantic spaces. Facilitator line on slide is worth saying verbatim. Dev quiz: "We upgraded the embedding model Friday but didn't reindex" → predict symptom (wildly inconsistent retrieval). *(~4 min)*

---

# Slide 10 | How Embedding Models Work

## On screen

Embedding models (often Transformer **encoders**) trained with:

| Training idea | Result |
|---------------|--------|
| Similar pairs closer | Paraphrases map nearby |
| Dissimilar pairs farther | Unrelated topics separate |
| Contrastive learning | Semantic space for search |

**Forward pass at index/query time:**

```
1. Tokenize input (subword — same BPE idea as LLMs)
2. Encoder layers (attention + pooling)
3. Pool → single vector (mean pooling or [CLS])
4. Optionally L2-normalize for cosine similarity
5. Output: embedding stored or compared
```

**Training connection (L4):** Embeddings trained with contrastive loss; LLMs with next-token prediction. RAG combines **frozen** embedding search + **frozen/pinned** LLM — no retrain when new PDF arrives.

## Speaker notes

Light technical depth for developers — operators need the reindex rule, not architecture details. Forward pass only at runtime — like LLM inference, not training. Pooling combines token representations into one chunk vector. *(~4 min)*

---

# Slide 11 | Dimensions & Storage Math

## On screen

| Embedding model size | Dimensions | Rough size (float32) |
|---------------------|------------|---------------------|
| Small / fast | 384 | ~1.5 KB per vector |
| Medium | 768 | ~3 KB per vector |
| Large | 1024–1536 | ~4–6 KB per vector |

**Index storage estimate:**

```
vectors ≈ chunk_count × dimension × 4 bytes (float32)

Example: 1,000,000 chunks × 768 × 4  ≈ 3 GB (vectors only)
```

Add metadata, HNSW graph overhead, replicas — plan **2–4×** for production ([Lecture 5](../05_local_llm.md))

**You don't pick dimensions independently** — follow chosen embedding model

## Speaker notes

Ops and leadership: sizing conversation for vector storage. Million-chunk policy+funding corpus is realistic for large orgs — plan SSD and RAM. Embedding model on CPU: 2–8 GB RAM typical. Reindex time grows with corpus — maintenance windows. *(~4 min)*

---

# Slide 12 | Cosine Similarity & Thresholds

## On screen

Vector DB compares query vector **q** to stored vectors **v**:

| Metric | Intuition | Common use |
|--------|-----------|------------|
| **Cosine similarity** | Angle between vectors; ignores magnitude | Normalized embeddings — **most common** |
| **Dot product** | Magnitude-sensitive | Some optimized indexes |
| **Euclidean (L2)** | Straight-line distance | k-NN when not normalized |

**Cosine intuition:** score near **1** = very similar meaning; near **0** = unrelated

**Score thresholds:** below threshold → return **"insufficient information"** instead of forcing LLM to hallucinate

```
if top_score < 0.72:
    return "I don't have enough information in approved sources."
else:
    proceed to generation with top-k chunks
```

## Speaker notes

Match metric to embedding model training convention — usually cosine on normalized vectors. Threshold tuning is dev + staff eval work — too high → "I don't know" too often; too low → irrelevant chunks → fabricated bullets. Some APIs return distance not similarity — read DB docs. *(~4 min)*

---

# Slide 13 | ANN Indexes (HNSW)

## On screen

Brute-force compare to millions of vectors is too slow → **Approximate Nearest Neighbor (ANN)**

| Index type | Idea |
|------------|------|
| **HNSW** | Graph navigation to nearby vectors — popular default |
| **IVF** | Cluster buckets; search promising clusters only |
| **Flat / exact** | Small corpora; ground truth for eval |

| Parameter | Effect |
|-----------|--------|
| **ef_search** | Higher → better recall, slower |
| **M (HNSW)** | Graph connectivity — build-time trade-off |

**At scale:** purpose-built vector DB (pgvector, Milvus, Qdrant, Weaviate, …)

Small pilots may use simpler stores; production usually needs specialized search.

## Speaker notes

ANN trades tiny accuracy for speed — tune with eval retrieval hit rate. Ops owns reindex duration and HA replicas. Flat index OK for golden-set eval ground truth. HNSW is the default name ops will see in runbooks. *(~3 min)*

---

# Slide 14 | Hybrid Search + Rerank

## On screen

Embeddings miss **exact IDs**, **legal cite numbers**, rare acronyms → **hybrid search**

```
Semantic score (embedding)  +  Keyword score (BM25)
                    ↓
              merged ranking
```

| Query type | Best signal |
|------------|-------------|
| "Conflict of interest policy" | Semantic |
| "Circular ST-2024-0172 section 3" | Keyword |
| "Grant extension procedure" | Hybrid |

**Reranking pipeline:**

```
Retrieve top 50 (bi-encoder / embedding — fast)
        → rerank to top 5 (cross-encoder — slower, precise)
        → send top 5 to LLM
```

Rerankers often on **CPU** or small GPU — budget latency

## Speaker notes

Bi-encoder fast; cross-encoder scores query–chunk pairs jointly — more accurate, slower. Funding circular lookup by ID is a classic hybrid win. Don't skip hybrid eval for ID-heavy corpora (patents, policy numbers). k too large → noise and context overflow (Lecture 4). *(~5 min)*

---

# Slide 15 | Metadata & Access Control

## On screen

Stored per chunk typically:
- Vector + raw text
- **Metadata:** title, source URL, doc version, date, department, **classification**, ACL

**Access control is non-negotiable:**

```
User A (Dept X) → filter index → only chunks tagged Dept X or public
```

RAG without ACL → **data leak via similarity**

| Metadata field | Use |
|----------------|-----|
| `effective_date` | Filter stale policy |
| `doc_type` | Funding vs patent vs HR |
| `clearance_level` | Restrict sensitive annexes |
| `collection` | Route to correct corpus |

**Incident priority:** User from Team 1 sees Team 2 budget → security fix immediately

## Speaker notes

Security defines ACL rules; dev implements filters; staff label documents correctly. Missing date filter → good semantic match but wrong year retrieved. Cross-department leak is priority incident — disable collection, audit logs, fix filter, reindex. *(~4 min)*

---

# Slide 16 | Unified Text → Vector → LLM Diagram

## On screen

```
RAW DOCUMENT
     │
     ├─► Extract text (OCR, parser)
     ├─► Chunk by tokens/structure  ─── TOKENIZER (token counts)
     ├─► Embed each chunk          ─── EMBEDDING MODEL → vector[d]
     └─► Store in vector index     ─── VECTOR DB + metadata + ACL

USER QUESTION
     │
     ├─► Embed question            ─── same embedding model (critical)
     ├─► ANN search + filters      ─── cosine / hybrid + ACL
     ├─► Optional rerank           ─── cross-encoder
     ├─► Build prompt with chunks  ─── TOKENIZER → LLM context limit
     └─► LLM generate              ─── LOCAL or API (L4, L5)
```

| Stage | Resource driver |
|-------|-----------------|
| Chunk + embed (batch) | Corpus size, embedding model |
| Vector index | chunks × dimensions |
| Retrieve | Concurrent users, QPS |
| Generate | LLM VRAM, tokens in + out |

## Speaker notes

Unified picture tying Lectures 4–6 — pause for reading. Same embedding model at index and query — say it again. Tokenizer counts toward LLM context when chunks enter prompt. No retraining when new PDF arrives — reindex ingestion path only. *(~4 min)*

---

# Slide 17 | Prompt Assembly & Citations

## On screen

**Typical template:**

```
SYSTEM: You answer only from provided sources. Cite [Source ID].
        If insufficient, say "I don't have enough information."

CONTEXT:
[1] (Policy 2025-03, §4.2) ...
[2] (Funding FAQ, item 12) ...

USER: {question}
```

**Developer rules:**
- Answer must cite chunk IDs
- No fabrication beyond context
- Refusal when retrieval score below threshold

**Good RAG UI:**
- Answer + clickable citations → source doc + highlight
- Source document date/version visible
- "Low relevance" warning when scores marginal

## Speaker notes

Generation guardrails turn retrieval into trustworthy output. Invented citation = prompt not enforcing cite-only — developer fix + eval. Support benefits: user verifies without trusting prose alone. Staff workflow: RAG draft → verify against cited source → human approves external reply. *(~4 min)*

---

# Slide 18 | Quality Dimensions

## On screen

| Dimension | Question to ask |
|-----------|-----------------|
| **Recall** | Did we retrieve the right chunk at all? |
| **Precision** | Did we avoid irrelevant chunks that confuse the model? |
| **Groundedness** | Is every claim supported by a citation? |
| **Completeness** | Multi-hop questions fully covered? |
| **Freshness** | Index up to date with published policy? |
| **Safety** | Restricted docs excluded for this user? |

**Golden question set:** Real user questions + expected source doc + properties ("must mention deadline date")

| Metric type | Example |
|-------------|---------|
| Retrieval hit rate | Correct doc in top-5 |
| Answer groundedness | Manual or LLM-judge vs citations |
| Regression | Before/after index or model change |

Run evals on **every** index rebuild and model upgrade.

## Speaker notes

Quality is everyone's responsibility — dev-led with staff supplying real questions. Funding FAQ eval: "must cite Circular 2024-0172" not just fluent prose. Regression when embedding model or chunk strategy changes. *(~4 min)*

---

# Slide 19 | Failure Modes

## On screen

| Symptom | Likely cause | Owner action |
|---------|--------------|--------------|
| "It ignored our policy" | Poor chunking, stale index, wrong collection | Reindex; fix chunks; check filters |
| Right doc, wrong answer | Noise chunks, weak prompt, model drift | Rerank; tighten k; eval prompt |
| Invented citation | Prompt not enforcing cite-only | Developer guardrails; lower temperature |
| "I don't know" too often | Threshold too high; OCR garbage | Tune thresholds; fix extraction |
| Cross-department data leak | Missing ACL on metadata | **Security — priority incident** |
| Slow responses | Huge k, large chunks, cold GPU | Ops perf tuning (L5) |
| Contradictory answers | Conflicting corpus versions | Staff retire old docs; version metadata |
| Wildly different docs retrieved | Embedding mismatch | Reindex with current embed model |

## Speaker notes

RAG debugging is **pipeline debugging**, not "re-prompt the user." Walk 2–3 rows with org-colored examples. Stale index after emergency policy change: human notice + priority reindex + on-publish hook post-incident. *(~5 min)*

---

# Slide 20 | Prepare — Corpus Curation

## On screen

**Staff + knowledge owners:**

| Task | Detail |
|------|--------|
| **Canonical sources** | One official version per policy |
| **Retire superseded** | Archive or mark `effective_to` date |
| **Structure** | Headings, summaries, consistent titles |
| **PII scan** | Remove or redact before indexing |
| **Classification labels** | Match access control model |

**Developers + ops:**

| Task | Detail |
|------|--------|
| Ingestion pipeline | Scheduled + on-publish webhook |
| Idempotent reindex | Doc update replaces old chunks |
| Staging index | Test before production alias swap |
| Embedding version | Record in index metadata |
| Disaster recovery | Rebuild index from source docs |

**Governance:** Approved corpora list; security review for new collections

## Speaker notes

Prepare fixes more than clever code — facilitators repeat this in Lecture 6 and series recap. Policy owner monthly: review deprecated docs in index; sample 5 RAG answers in your domain. PII in corpus = policy violation — scan before index. *(~4 min)*

---

# Slide 21 | Operate — Monitoring

## On screen

| Signal | Alert if |
|--------|----------|
| Ingestion lag | Published doc not searchable after SLA |
| Ingestion failures | OCR/parse failures spike |
| Retrieval latency | p95 above target (often &lt;100–500 ms pre-LLM) |
| Empty retrieval rate | Sudden spike — broken embedder? |
| Low score rate | Users see "no info" often |
| User thumbs-down | Quality regression |

**Operational cadence:**

| Event | Action |
|-------|--------|
| New policy published | Trigger reindex or incremental update |
| Embedding model upgrade | **Full reindex + eval** |
| Emergency policy change | Human notice + priority reindex |

**Incident example:** Stale index → publish "AI may not reflect update until HH:MM" + priority reindex

## Speaker notes

Ops monitors index health — not just GPU. Ingestion lag is a content owner pain point users feel immediately. Embedding upgrade without reindex collapses retrieval — full reindex required. Define SLA with content owners: on-publish vs nightly. *(~4 min)*

---

# Slide 22 | Develop — Tuning Order

## On screen

**Tuning levers (in order):**

1. **Corpus quality** — fixes more than clever code  
2. **Chunking & metadata**  
3. **Hybrid retrieval + rerank**  
4. **Prompt template & citation rules**  
5. **k, thresholds, filters**  
6. **LLM choice** (L4, L5)  
7. **Fine-tuning** — last resort for tone/format  

**Anti-patterns:**
- Indexing everything "just in case"
- No versioning — users cite deprecated PDF
- Giant chunks "so we don't miss context"
- Skipping eval because "RAG is standard"
- User uploads to shared index without scan

**Advanced (awareness):** multi-query retrieval, HyDE, agentic RAG, graph RAG — measure before adding

## Speaker notes

Start simple; measure before complexity. Agentic RAG is not the default funding FAQ solution. Anti-patterns are common organizational mistakes — giant chunks reduce precision. Fine-tuning last — after corpus, chunk, hybrid, prompt work exhausted. *(~4 min)*

---

# Slide 23 | Customer Messaging

## On screen

| Reasonable promise | Unreasonable promise |
|--------------------|----------------------|
| Answers use **named official sources** when found | Always complete and correct |
| Citations link to documents | Understands intent like a human lawyer |
| Says when sources are insufficient | Knows unpublished decisions |
| Updated on defined schedule or event | Real-time unless you built that |

**Example user-facing text:**

> "This assistant searches approved [organization] documents and drafts answers with references. Always check citations for official decisions. If something looks wrong, use the feedback button."

**Support scenarios:**

| User says | Response outline |
|-----------|------------------|
| "It cited old policy" | Verify citation version; flag reindex; human path |
| "It made up a section" | Quality bug; session ID; escalate — don't debate |
| "Answer was slow" | Status page; ops if widespread |

## Speaker notes

Messaging must match actual ingestion SLA — don't promise real-time if nightly crawl. Staff integration: policy owner maintains corpus → RAG draft → staff verifies → human approves publication. Support never blames user for system limits. *(~4 min)*

---

# Slide 24 | Activity — Debug the RAG

## On screen

**START ACTIVITY — ~15 min | Groups**

Identify **pipeline stage** and **fix owner**:

| # | Symptom |
|---|---------|
| 1 | New FAQ live on web 48h ago, bot silent |
| 2 | Answer cites Doc A but misses obvious Doc B |
| 3 | Quotes Doc A correctly but adds fake bullet |
| 4 | User from Team 1 sees Team 2 budget |
| 5 | Tables in PDF always wrong |
| 6 | Same question, wildly different docs retrieved |

*Answers in [06_rag.md §11](../06_rag.md)*

## Speaker notes

Facilitate — groups map stage + owner. Debrief: (1) ingestion/schedule; (2) retrieval/chunking/hybrid; (3) generation/prompt; (4) ACL security priority; (5) extraction/parser; (6) embedding mismatch/reindex. Reinforce: pipeline debugging, not user re-prompting. *(~15 min)*

---

# Slide 25 | Series Recap — Lectures 4 + 5 + 6

## On screen

```
                    CUSTOMER / STAFF USER
                              │
                    Application + auth + audit
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
    RAG retrieve         Tool APIs            Session policy
    (Lecture 6)          (live data)          (what allowed)
         │                    │                    │
         └────────────────────┼────────────────────┘
                              ▼
                    Local or API LLM inference
                    (Lectures 4 & 5)
                              ▼
                    Human review (high-stakes)
```

| Lecture | Remember |
|---------|----------|
| **4 — LLM** | Predicts language; training ≠ inference; verify outputs |
| **5 — Local** | You control infra and duty; size hardware to SLA |
| **6 — RAG** | Vectorize → retrieve → generate; tokens ≠ embeddings |

**Together:** Prepare authoritative docs + pinned versions. Operate indices and GPUs. Develop with evals. Serve with citations and humility.

## Speaker notes

Capstone slide for the 4–5–6 arc. RAG improves grounding — does not remove governance or human accountability. High-stakes grants, legal, minister briefings: human approval always. Preview Lecture 7 prompting and capstone customer serving track. *(~4 min)*

---

# Slide 26 | Glossary Handout

## On screen

| Term | Definition |
|------|------------|
| **Vectorization** | Mapping text to a numeric vector |
| **Embedding** | Output vector representing meaning |
| **Embedding model** | Neural net for embeddings (not the chat LLM) |
| **Dimension** | Length of embedding vector (e.g. 768) |
| **Cosine similarity** | Similarity metric based on vector angle |
| **ANN / HNSW** | Fast approximate vector search |
| **BM25** | Keyword relevance scoring |
| **Hybrid search** | Semantic + keyword combined |
| **Bi-encoder / cross-encoder** | Fast retrieval vs precise reranker |
| **Chunk** | Retrieval unit of text |
| **k** | Number of chunks retrieved |
| **Reindex** | Rebuild all vectors after model/chunk change |
| **ACL filter** | Metadata restriction on searchable chunks |
| **Groundedness** | Answer supported by retrieved text |

**Production checklists:** lecture doc §17 | **Next:** [07_prompting.md](../07_prompting.md)

## Speaker notes

Print or share as handout — full glossary and checklists in lecture doc §16–17. RAG production readiness: ACL tested with multiple personas, golden eval ≥30 questions, citation UI wired, insufficient-info path tested. Thank the room; open brief Q&A from §13 prepared answers. *(~5 min)*

---
