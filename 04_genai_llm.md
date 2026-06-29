# Generative AI & Large Language Models (LLMs)

*For all members — developers, operators, staff, support, and leadership*  
*Concepts, internal workings, and practical responsibilities*

---

## Module Overview

This is **Lecture 4** in the technical literacy track. It explains what generative AI and LLMs are, how they work conceptually, and what every role must know to use, operate, build, or support them responsibly.

| Lecture | Topic |
|---------|--------|
| **4 (this)** | Generative AI & LLMs — foundations |
| **5** | Local LLM — deployment, operations, infrastructure |
| **6** | RAG — retrieval-augmented generation over organizational knowledge |

**Prerequisite:** Familiarity with [03_gai_rai.md](03_gai_rai.md) (Generative vs Recognition AI) is helpful but not required.

**Duration:** 90–105 minutes (teaching + discussion + Q&A)  
**Format:** Concepts → training & neural networks → tokens → inference internals → roles → activity → Q&A

---

## Learning Objectives

By the end, members should be able to:

1. Explain generative AI and LLMs in plain language and correct common myths  
2. Describe the **training vs inference** lifecycle and why it matters for cost, privacy, and updates  
3. Understand **tokens, tokenization, embeddings (preview)**, and context windows  
4. Explain **how neural networks learn** — weights, loss, gradients — at an intuitive level  
5. Describe the **Transformer** as the core LLM structure (layers, attention, feed-forward)  
6. Outline the **training pipeline**: pre-training, fine-tuning, alignment (conceptual)  
7. Explain how an LLM generates text at inference time  
8. Identify strengths, limits, and failure modes (hallucination, staleness, prompt sensitivity)  
9. Know what their role should **prepare, operate, develop, or communicate** when LLMs are in the workflow  

---

## 1. Opening (5 min)

**Facilitator message:**

> “This session is for everyone who touches AI-assisted work — whether you write code, run servers, support users, manage projects, or approve outputs. You do not need a computer science degree. You do need a **shared mental model** so we deploy and use LLMs consistently, safely, and honestly with customers.”

**Icebreaker (quick poll):**

- Who has used a chat-style AI assistant?  
- Who has seen an AI answer that sounded right but was wrong?  
- Who is responsible for something that might eventually call an LLM?

**On the board:**

> **LLM = a system trained to predict language, not a database of facts and not a person.**

---

## 2. Generative AI — Concepts & Principles (15 min)

### 2.1 Where this fits in the AI landscape

| Type | Core job | Example |
|------|----------|---------|
| **Recognition AI** | Classify, score, detect, predict | Spam filter, image tagging, fraud score |
| **Generative AI** | Create new content from instructions | Draft report, summarize, generate code |
| **LLM (Large Language Model)** | Generative AI focused on **text** (and often code) | Chat assistants, document Q&A backends |

**One sentence:**  
> **Generative AI produces new artifacts; LLMs are the most common generative AI for language-heavy work.**

See [03_gai_rai.md](03_gai_rai.md) for a fuller comparison with Recognition AI.

### 2.2 What an LLM is — and is not

| LLM **is** | LLM **is not** |
|------------|----------------|
| A statistical model of “what text usually comes next” | A search engine with guaranteed correct answers |
| Trained on very large text corpora | Aware of your private files (unless you connect systems) |
| Good at language patterns, structure, and synthesis | A substitute for expert judgment on high-stakes decisions |
| Useful for drafts, explanation, and transformation | Always up to date or policy-compliant by default |
| A component you can integrate into products | AGI ([02_concept_agi.md](02_concept_agi.md)) |

### 2.3 Core principles every member should internalize

1. **Probabilistic, not deterministic** — The same prompt can yield different answers. Temperature and sampling add variability.  
2. **Plausible ≠ true** — Fluent language is not evidence of correctness.  
3. **Knowledge has a cutoff** — Training data ends at a point in time unless you connect retrieval or tools.  
4. **Context-bound** — The model only “sees” what you put in the prompt (plus its training).  
5. **Human accountability stays with the organization** — AI assists; people approve, publish, and decide.

**Facilitator line:**

> “Treat LLM output as a **strong first draft** or **candidate answer** — not as an authoritative record unless it is verified and grounded.”

---

## 3. Training vs Inference — The Two Lives of a Model (12 min)

Understanding this split is essential for developers, operators, and anyone explaining limitations to customers.

### 3.1 Training (learn once, expensive, rare)

**Goal:** Teach the model language patterns from massive datasets.

| Aspect | Plain-language description |
|--------|---------------------------|
| **Input** | Billions of text examples (books, web, code, etc.) |
| **Process** | Adjust internal parameters so “next token” predictions improve |
| **Cost** | Very high compute, energy, time, expertise |
| **Who does it** | Model vendors, research labs, specialized ML teams |
| **Frequency** | Occasional — new major versions, not daily |

**Analogy:** Years of reading and practice compressed into a fixed “skill snapshot.”

### 3.2 Inference (use many times, ongoing)

**Goal:** Given a prompt, generate a response using the **already trained** weights.

| Aspect | Plain-language description |
|--------|---------------------------|
| **Input** | User prompt + system instructions + optional retrieved documents |
| **Process** | Run the frozen model forward; emit tokens one by one |
| **Cost** | Per request — scales with tokens and hardware |
| **Who does it** | Your app, API gateway, or local inference server |
| **Frequency** | Every user question, every summarization job |

**Analogy:** A trained professional answering a specific question **right now** — without re-reading the entire library first.

### 3.3 Why this matters at work

| Question | Training | Inference |
|----------|----------|-----------|
| “Can we add last week’s policy PDF to its brain?” | Not without retraining or fine-tuning — use **RAG** ([06_rag.md](06_rag.md)) instead | You can **inject** that PDF into the prompt at query time |
| “Why is GPU capacity important?” | Training needs huge clusters | Inference needs sustained capacity under load |
| “Where does privacy risk appear?” | Training data composition (vendor concern) | **Your prompts and logs** (your concern) |
| “How do we upgrade?” | New model version from vendor | Redeploy weights, regression-test, roll out |

---

## 4. How Models Learn — Neural Networks, Math Intuition & Training (22 min)

*Light math — enough for developers and curious operators to read architecture diagrams and training discussions.*

### 4.1 What is a neural network (core idea)?

A **neural network** is a function built from many simple connected units (**neurons** / **nodes**) organized in **layers**. Each connection has a **weight** (a number). The network transforms input numbers into output numbers through repeated matrix operations and nonlinear steps.

```
Input (e.g. token IDs as vectors)
        │
        ▼
   ┌─────────┐
   │ Layer 1 │  weighted sums + activation
   └────┬────┘
        ▼
   ┌─────────┐
   │ Layer 2 │
   └────┬────┘
        ▼
      ...
        ▼
Output (e.g. scores for every possible next token)
```

**Analogy:** Not a lookup table — a vast adjustable circuit. **Training** turns the knobs (weights) until outputs match desired patterns on examples.

| Term | Plain meaning |
|------|----------------|
| **Weight / parameter** | A learned number inside the model; LLMs have billions |
| **Layer** | One stage of transformation |
| **Forward pass** | Input → output (used in training and inference) |
| **Activation function** | Nonlinear step that lets the network model complex patterns |

**LLMs are neural networks** — very wide, very deep, specialized for sequences (text).

### 4.2 Learning = adjust weights to reduce error

**Supervised learning intuition** (simplified for language):

1. Show the model a piece of text.  
2. Ask it to predict the **next token**.  
3. Compare prediction to the **actual** next token.  
4. Measure **loss** (error score — higher = worse).  
5. **Backpropagation** computes how each weight contributed to the error.  
6. **Gradient descent** nudges weights slightly to reduce loss.  
7. Repeat billions of times over billions of tokens.

```
Prediction:  "... deadline is March 1"  (wrong)
Actual:      "... deadline is March 15"
                    Loss → update weights → slightly better next time
```

| Math concept | Intuition only — no formulas required on exam |
|--------------|-----------------------------------------------|
| **Loss function** | Single number: “how wrong was this batch?” |
| **Gradient** | Direction to change weights to reduce loss |
| **Learning rate** | Step size — too large = unstable; too small = slow |
| **Batch** | Train on many examples at once for efficiency |
| **Epoch** | One pass over a dataset (LLM pre-training uses many) |

**Facilitator line:**

> “Training is automated trial-and-error at massive scale. Nobody hand-writes grammar rules — the network discovers statistical structure.”

### 4.3 From small nets to LLMs — scale changes behavior

| Scale | What changes |
|-------|----------------|
| **More parameters** | Can memorize more patterns; emergent skills (reasoning-like behavior) appear at scale |
| **More data** | Broader coverage of language, domains, code |
| **More compute** | Makes large-scale training feasible (GPUs — see [05_local_llm.md](05_local_llm.md)) |

**Important:** Scale does not mean “understands like a human.” It means **better approximation of text patterns** in training distribution.

### 4.4 Training stages for modern LLMs (lifecycle)

Most production LLMs are not “one train and done.” Typical pipeline:

| Stage | Goal | Who usually runs it |
|-------|------|---------------------|
| **Pre-training** | Learn general language from huge corpus (predict next token) | Vendor / lab — weeks–months on GPU clusters |
| **Supervised fine-tuning (SFT)** | Teach instruction-following, Q&A format, safety templates | Vendor or your ML team on curated examples |
| **RLHF / preference tuning** | Align with human ratings (helpful, harmless, honest) | Vendor — uses human labelers + reward models |
| **Continued pre-training** | Add domain corpus (medicine, law, code) | Optional — org with ML capacity |
| **Fine-tuning (LoRA / full)** | Task-specific tone, format, classification | Your team when RAG is not enough |

**What your organization usually does:** Use **pre-trained** weights + **RAG** + prompts. Full pre-training is rare outside vendors.

| Stage | Updates weights? | Updates org knowledge without retrain? |
|-------|------------------|----------------------------------------|
| Pre-training / fine-tuning | Yes | N/A — expensive |
| **RAG** | No | Yes — at query time ([06_rag.md](06_rag.md)) |
| **Prompting** | No | Partially — via instructions only |

### 4.5 Core architecture: Transformer blocks (structural deep dive)

Modern LLMs are stacks of **Transformer decoder blocks** (GPT-style) or encoder–decoder (some translation models).

**One block (conceptual):**

```
Input token vectors
       │
       ▼
┌──────────────────┐
│ Multi-head       │  "Which earlier tokens matter for each position?"
│ SELF-ATTENTION   │
└────────┬─────────┘
         ▼
┌──────────────────┐
│ Feed-forward     │  Per-token nonlinear transform
│ network (MLP)    │
└────────┬─────────┘
         ▼
   (repeat N times — e.g. 32–80+ layers)
         ▼
┌──────────────────┐
│ Output head      │  Scores for each vocabulary token (next-token logits)
└──────────────────┘
```

| Component | Role |
|-----------|------|
| **Token embedding** | Maps token ID → vector |
| **Positional encoding** | Injects order (token 1 vs token 50) |
| **Self-attention** | Each position attends to all prior positions; learns dependencies |
| **Multi-head** | Several attention patterns in parallel (syntax, coreference, etc.) |
| **Feed-forward (FFN)** | Processes each position after mixing context |
| **Layer norm & residuals** | Stabilizes very deep training |
| **Output projection** | Vocabulary-sized scores → softmax → probabilities |

**Attention intuition (still no matrix math):**

For each word being generated, the model assigns **attention weights** to earlier words. High weight = “this word strongly influences my next choice.”

```
"The applicant must submit Form A by [DATE]"
                                 ↑
              attention may link "by" ← → "DATE" when choosing a date token
```

### 4.6 Recognition AI vs generative LLM — same family, different head

| | Recognition (classifier) | Generative LLM |
|---|--------------------------|----------------|
| **Output layer** | Scores for **classes** (spam/ham) | Scores for **vocabulary tokens** |
| **Training target** | Correct label | Correct next token sequence |
| **Same underneath?** | Often CNN/Transformer encoder | Transformer decoder stack |

Both use **gradient-based learning** on neural networks — the lecture series on Recognition vs Generative ([03_gai_rai.md](03_gai_rai.md)) is about **task**, not a different species of mathematics.

---

## 5. Tokens, Tokenization & Vector Representations (15 min)

*Tokens are how text enters the neural network. Embeddings are how meaning is represented as numbers.*

### 5.1 Text becomes tokens

- Raw text is split into **tokens** (word pieces, not always whole words).  
- Each token maps to an integer **token ID** in a fixed **vocabulary**.  
- Example: `"innovation"` might be one token; `"ST-EOS"` might split into several.  
- Models have vocabularies from ~32k to 128k+ tokens.

**Why members should care:**

- Billing and speed are often **per token**  
- Long documents consume the **context window**  
- Cutting text awkwardly hurts quality (mid-table, mid-sentence chunks)  
- **Tokenizer mismatch** between systems causes subtle bugs

### 5.2 How tokenization works (BPE and subwords)

Most LLMs use **subword** tokenization (often **BPE — Byte Pair Encoding**):

| Approach | Behavior |
|----------|----------|
| **Word-level** | Huge vocabulary; unknown words break |
| **Character-level** | Tiny vocabulary; very long sequences |
| **Subword / BPE** | Common words whole; rare words split into pieces |

**Example (illustrative, model-dependent):**

| Text | Possible tokens |
|------|-----------------|
| `internationalization` | `inter` + `national` + `ization` |
| `GPT-4` | `GPT` + `-` + `4` |
| `서울` (Seoul) | Depends on multilingual tokenizer |

**Rules of thumb:**

- English averages ~**4 characters per token** (rough guide only)  
- 1,000 tokens ≈ **750 words** (ballpark for English prose)  
- Code, tables, and CJK text can differ sharply

### 5.3 Special tokens

| Token type | Purpose |
|------------|---------|
| **BOS / EOS** | Beginning / end of sequence |
| **PAD** | Batch padding (training) |
| **UNK** | Unknown (rare in modern BPE) |
| **Instruction tokens** | Delimit system / user / assistant sections (chat models) |

Developers must use the **tokenizer paired with the model** — mixing models and tokenizers corrupts input.

### 5.4 Token embeddings (inside the LLM)

After tokenization, each token ID is converted to a **vector** (list of floats) via an **embedding table**.

```
Token ID  4821  →  [0.12, -0.44, 0.08, ... ]  (dimension e.g. 4096)
```

| Concept | Meaning |
|---------|---------|
| **Embedding dimension** | Width of the vector (model hyperparameter) |
| **Learned during training** | Similar tokens (used in similar contexts) get nearby vectors |
| **Not the same as RAG embeddings** | LLM internal embeddings ≠ document search embeddings ([06_rag.md](06_rag.md)) — different models, purposes |

**Facilitator line:**

> “**Token** = discrete word piece. **Embedding** = continuous meaning coordinates. The LLM lives in embedding space; token IDs are the door.”

### 5.5 Context window (reprise)

The **context window** is the maximum tokens the model can consider in one request (prompt + output).

| Concept | Meaning |
|---------|---------|
| **Prompt** | Instructions + user message + retrieved docs + chat history |
| **Completion** | Generated answer |
| **Total** | Prompt + completion must fit (e.g. 8k, 32k, 128k, 1M on some models) |

Long contexts cost more memory and latency — especially on local GPUs ([05_local_llm.md](05_local_llm.md)).

### 5.6 Next-token prediction loop (inference)

At inference time, simplified:

```
1. Encode prompt into internal representation
2. Model outputs probability distribution over next token
3. Pick next token (sampling / temperature affects choice)
4. Append token to context
5. Repeat until stop condition (length, end token, user stop)
```

**Key insight:** The model does not “look up” an answer in a table. It **continues** text in a statistically likely way. That is why it can write poetry and also fabricate citations.

**Inference vs training:** At inference, weights are **frozen** — only forward pass runs (no loss, no backprop). See section 4 and [05_local_llm.md](05_local_llm.md) for hardware that runs this.

### 5.7 System prompt, user message, and tools

Typical production request structure:

| Layer | Purpose | Example |
|-------|---------|---------|
| **System prompt** | Role, tone, rules, safety boundaries | “You are a research assistant. Cite sources. Do not invent DOIs.” |
| **User message** | The actual task | “Summarize this annex in 5 bullets.” |
| **Context / RAG** | Organization-specific facts | Retrieved policy paragraphs |
| **Tools (optional)** | Calculator, search, database, APIs | “Look up applicant status in CRM” |

**Developers** implement this structure. **Operators** monitor latency and errors. **Staff** learn that wording and attached context change outcomes.

### 5.8 Sampling parameters: temperature and related controls

| Control | Effect (intuition) |
|---------|-------------------|
| **Low temperature** | More predictable, conservative wording |
| **Higher temperature** | More varied, creative — also more risk of drift |
| **Max tokens** | Caps length and cost |
| **Stop sequences** | End generation at defined markers |

Operations teams should document **approved defaults** per use case — creative brainstorming vs legal-adjacent summaries need different settings.

---

## 6. Capabilities, Limits, and Failure Modes (12 min)

### 6.1 What LLMs do well

- Summarize and rephrase for different audiences  
- Extract structure from messy text (lists, tables in prose)  
- Draft emails, FAQs, workshop materials, code scaffolds  
- Brainstorm alternatives and outlines  
- Translate between languages (with verification for official text)  
- Assist with routine analysis when paired with **grounded data** (RAG, tools)

### 6.2 What LLMs do poorly without help

- Guaranteed factual accuracy on niche or recent facts  
- Precise arithmetic over long chains (unless tool-assisted)  
- Knowing your internal systems, IDs, or unpublished data  
- Stable behavior across all edge-case phrasing  
- Legal, medical, or funding **final** decisions

### 6.3 Failure modes — name them clearly

| Failure | What it looks like | Mitigation |
|---------|-------------------|------------|
| **Hallucination** | Invented references, dates, names, policies | RAG with citations, human review, eval tests |
| **Stale knowledge** | Wrong “current” procedures | Retrieval from live corpus, version dates in prompt |
| **Prompt injection** | User text overrides system rules | Input sanitization, separation of instructions and data |
| **Overconfidence tone** | Wrong answer stated confidently | UI disclaimers, confidence cues, require sources |
| **Bias / skew** | Wording reflects training biases | Guidelines, review, diverse eval sets |
| **Context overflow** | Silent truncation of early prompt parts | Monitor token counts, summarize history |
| **Inconsistent answers** | Different replies to similar questions | Standard prompts, cached retrieval, temperature policy |

### 6.4 Fine-tuning vs prompting vs RAG (preview)

| Approach | When to consider |
|----------|------------------|
| **Prompt engineering** | First step — behavior via instructions |
| **RAG** | Answers must use **your documents** ([06_rag.md](06_rag.md)) |
| **Fine-tuning** | Specialized style/format or domain phrasing — needs curated data and ML effort |
| **Tool use** | Live data, calculations, workflows |

Most organization deployments start with **prompt + RAG** before fine-tuning.

---

## 7. Roles & Responsibilities (10 min)

### 7.1 Developers

**Prepare**

- Define request schema (system / user / context / tools)  
- Implement guardrails, logging, rate limits, error handling  
- Build evaluation harness (golden questions + expected properties)  
- Never log secrets or PII without policy approval  

**Operate (with ops)**

- Version-pin models; document breaking changes  
- Feature flags for model or prompt rollouts  
- Regression tests before production promotion  

**Serve (indirectly)**

- Expose citations, source links, and “I don’t know” paths in UI  
- Make limits visible (max upload size, supported languages)

### 7.2 Operators / platform / DevOps

**Prepare**

- Size GPU/CPU capacity; plan autoscaling or queues  
- Secrets management, network policies, backup of configs  
- Runbooks for model download, checksum, deploy, rollback  

**Operate**

- Health checks, latency, token throughput, error rates  
- Disk/memory alerts; GPU temperature and utilization  
- Incident response: degrade gracefully (queue, fallback message)  

**Serve (indirectly)**

- Uptime and performance SLAs communicated to product/support  
- Maintenance windows for model upgrades

### 7.3 Staff (research, admin, policy, communications)

**Prepare**

- Know approved tools and what data may be entered  
- Keep source documents authoritative and up to date (RAG quality depends on this)  
- Learn basic prompt clarity: goal, audience, format, constraints  

**Operate**

- Verify outputs before external use  
- Report bad answers with reproducible prompts (for improvement)  
- Escalate suspected data leaks immediately  

**Serve customers / stakeholders**

- Set expectations: assistive, not authoritative  
- Do not promise “the AI said it” as final proof  
- Use human approval for public statements, grants, legal text

### 7.4 Support & customer-facing roles

**Prepare**

- FAQ on what the AI can and cannot do  
- Escalation matrix (hallucination, privacy, outage, offensive output)  
- Scripts that avoid blaming the user for model failures  

**Operate**

- Ticket tags: quality vs outage vs policy  
- Capture prompt + session ID (per retention policy) for reproduction  

**Serve**

- Honest, calm explanations during incidents  
- Never instruct users to paste classified data to “fix” an answer

### 7.5 Leadership & product

**Prepare**

- Use-case approval: high-stakes vs assistive tiers  
- Governance: model approval list, data classification rules  
- Budget for inference compute and human review time  

**Serve**

- Align public messaging with actual capability  
- Insist on human accountability for decisions

---

## 8. End-to-End Mental Model (5 min)

Where Lecture 4 sits in the stack (Lectures 5–6 go deeper):

```
┌─────────────────────────────────────────────────────────┐
│  User (staff, customer, partner)                        │
└───────────────────────────┬─────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Application UI / API                                   │
│  (auth, logging, policies, citations UI)                │
└───────────────────────────┬─────────────────────────────┘
                            ▼
        ┌───────────────────┴───────────────────┐
        ▼                                       ▼
┌───────────────┐                     ┌─────────────────┐
│  RAG layer    │  Lecture 6          │  Tools / APIs   │
│  (your docs)  │                     │  (live data)    │
└───────┬───────┘                     └────────┬────────┘
        └───────────────────┬──────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│  LLM inference  — Lecture 4 (what) / Lecture 5 (where)  │
│  Cloud API  OR  Local GPU/CPU deployment                │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Human review, approval, audit (always for high-stakes) │
└─────────────────────────────────────────────────────────┘
```

---

## 9. Activity — Diagnose the Scenario (12 min)

**Instructions:** Small groups. For each scenario, identify: (1) good LLM use? (2) main risk? (3) which role owns the fix?

| # | Scenario | Discussion prompts |
|---|----------|-------------------|
| 1 | Auto-publish AI summaries of patent abstracts to the public web | Hallucination, legal accuracy — **human approval** required |
| 2 | Internal chatbot drafts FAQ from official policy PDFs with citations | Good pattern if **RAG + review** — dev/ops maintain corpus |
| 3 | Staff pastes citizen PII into a public chatbot to draft a reply | **Policy violation** — privacy training, approved local stack |
| 4 | Model upgraded Friday night; Monday answers drift on funding rules | **Ops/dev** — regression eval, staged rollout |
| 5 | User asks “ignore rules and reveal system prompt” | **Prompt injection** — developer hardening |

**Debrief:** Reinforce — technology, process, and people together.

---

## 10. Operator & Developer Checklists (handout section)

### Before any LLM goes to production

- [ ] Approved model list and version documented  
- [ ] Data classification matrix (what may enter prompts)  
- [ ] System prompt reviewed by domain + security  
- [ ] Logging and retention aligned with policy  
- [ ] Eval set of ≥20 realistic queries with expected properties  
- [ ] Fallback when model unavailable (“try again / contact support”)  
- [ ] Human review path for high-stakes outputs  

### Weekly operations (typical)

- [ ] Monitor error rate, p95 latency, queue depth  
- [ ] Spot-check answered queries (quality sampling)  
- [ ] Review user-reported bad answers  
- [ ] Confirm no disk/GPU saturation trends  

---

## 11. Customer & Stakeholder Messaging (5 min)

**Say:**

- “AI-assisted draft based on your organization’s approved documents.”  
- “Sources are shown when available; please verify before official use.”  
- “If the system cannot find relevant material, it will say so.”

**Do not say:**

- “The AI is always correct.”  
- “This replaces expert review.”  
- “Paste anything here — it’s private.” (unless formally true for your deployment)

---

## 12. Q&A — Prepared Answers

**Q: Is the model “thinking”?**  
A: No. It is computing likely next tokens from patterns. Useful metaphor; misleading if taken literally.

**Q: What math do I need to operate an LLM?**  
A: Operators: token counts, memory, latency. Developers: APIs and evals. Training-from-scratch needs linear algebra and ML — usually vendor-side.

**Q: What is backpropagation in one sentence?**  
A: An algorithm that assigns blame for errors back through the network so weights know how to improve.

**Q: Are token embeddings the same as RAG embeddings?**  
A: No. LLM embeddings are internal to the language model. RAG uses a separate **embedding model** to search documents ([06_rag.md](06_rag.md)).

**Q: How many layers does our model have?**  
A: Check model card / config — e.g. 32–80 Transformer layers is common; layer count affects quality and VRAM.

**Q: Why did it cite a paper that does not exist?**  
A: Hallucination — common when asked for references without retrieval. Fix: RAG, require citations from retrieved chunks only, human check.

**Q: Can we “train it on our data” by chatting?**  
A: Normal chat does not retrain the model. Use RAG for knowledge, fine-tuning only with a deliberate ML process.

**Q: Cloud API vs local?**  
A: Lecture 5. Short version: cloud = fast to start, data leaves your boundary; local = you control infra and data residency, you operate hardware.

**Q: Who is liable for a wrong AI-generated grant letter?**  
A: The organization and approving humans — not “the model.”

---

## 13. Slide Outline (24 slides)

1. Title & objectives — all roles welcome  
2. GenAI vs Recognition recap  
3. What is an LLM? (is / is not)  
4. Five core principles  
5. Training vs inference  
6. Neural network — layers & weights (diagram)  
7. Learning loop — loss, gradient, update  
8. Training stages — pretrain, SFT, RLHF  
9. Transformer block — attention + FFN  
10. Tokens & BPE tokenization  
11. Token embeddings vs RAG embeddings (preview)  
12. Context window  
13. Next-token loop (diagram)  
14. System / user / context / tools  
15. Temperature & controls  
16. Strengths  
17. Failure modes table  
18. Prompt vs RAG vs fine-tuning  
19. Role responsibilities (matrix)  
20. Stack diagram (Lectures 4–6)  
21. Activity: scenarios  
22. Checklists & customer messaging  
23. Takeaways  
24. Next session — Local LLM & hardware ([05_local_llm.md](05_local_llm.md))

---

## 14. Takeaways

1. **LLMs are large neural networks** trained by minimizing prediction error on text — not rule engines.  
2. **Transformers + attention** are the core structure; depth and width drive capability and cost.  
3. **Training is rare and heavy; inference is every request** — your ops focus is inference unless you fine-tune.  
4. **Tokens and embeddings** are how text becomes math the model processes — tokenizer pairing matters.  
5. **Ground with RAG and tools; verify with humans** for anything that matters.  
6. **Every role has duties** — developers build safely, operators keep it running, staff verify, support communicates honestly.  
7. **Next:** [05_local_llm.md](05_local_llm.md) — hardware, software stack, and where inference runs.

---

## 15. Reference — Training & Token Glossary

| Term | Short definition |
|------|------------------|
| **Parameter / weight** | Learned number in the network |
| **Forward pass** | Compute output from input |
| **Loss** | Error metric during training |
| **Backpropagation** | Propagate loss backward to update weights |
| **Gradient descent** | Iterative weight improvement |
| **Transformer** | Attention-based architecture for sequences |
| **Self-attention** | Each token attends to others in context |
| **Token** | Subword unit in vocabulary |
| **BPE** | Byte Pair Encoding — common subword tokenizer |
| **Embedding** | Vector representation of token or text |
| **Vocabulary** | Set of all token IDs the model knows |
| **Logits** | Raw scores before softmax for next token |
| **Softmax** | Converts scores to probabilities |
| **Pre-training** | General language learning on large corpus |
| **Fine-tuning** | Additional training on curated task data |
| **RLHF** | Reinforcement learning from human feedback |

---

## 16. Further Reading (optional)

- [03_gai_rai.md](03_gai_rai.md) — Generative vs Recognition AI  
- [02_concept_agi.md](02_concept_agi.md) — AGI vs today’s narrow/language AI  
- [06_rag.md](06_rag.md) — Connecting LLMs to your documents
