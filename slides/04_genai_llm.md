# Slide Deck — Generative AI & Large Language Models
*Source: [04_genai_llm.md](../04_genai_llm.md) | Facilitator use*

# Slide 01 | Title & Learning Objectives

## On screen

**Lecture 4 — Generative AI & LLMs**

*For all members — developers, operators, staff, support, leadership*

| By the end you can… |
|---------------------|
| Explain LLMs in plain language and correct common myths |
| Describe **training vs inference** and why it matters for cost & privacy |
| Understand **tokens, embeddings, context windows** |
| Explain how neural networks learn (weights, loss, gradients) |
| Describe the **Transformer** and next-token generation |
| Identify strengths, limits, and failure modes |
| Know your role's **prepare / operate / develop / communicate** duties |

**Duration:** 90–105 min | **Prerequisite:** [03_gai_rai.md](../03_gai_rai.md) helpful

## Speaker notes

Open with the facilitator message: this session is for everyone who touches AI-assisted work — no CS degree required, but a shared mental model is essential. *(~2 min)* Run the icebreaker poll: who has used chat AI, who has seen a confident wrong answer, who owns something that might call an LLM. Write on the board: **LLM = a system trained to predict language, not a database of facts and not a person.** *(~3 min)*

---

# Slide 02 | GenAI vs Recognition AI

## On screen

| Type | Core job | Example |
|------|----------|---------|
| **Recognition AI** | Classify, score, detect, predict | Spam filter, fraud score, image tagging |
| **Generative AI** | Create new content from instructions | Draft report, summarize, generate code |
| **LLM** | Generative AI focused on **text** (and code) | Chat assistants, document Q&A backends |

> **Generative AI produces new artifacts; LLMs are the most common generative AI for language-heavy work.**

```
Recognition AI          Generative AI / LLM
     │                         │
  "What is this?"         "Write / continue this"
  Fixed labels            Open-ended text
```

## Speaker notes

Recap from Lecture 3 without re-teaching the whole module. Recognition AI scores or labels; generative AI creates. LLMs are the workhorse for text-heavy org tasks — funding FAQs, patent summaries, policy drafts. *(~4 min)* Ask the room for one recognition example and one generative example from their daily work. *(~2 min)*

---

# Slide 03 | What an LLM Is — and Is Not

## On screen

| LLM **is** | LLM **is not** |
|------------|----------------|
| A statistical model of "what text usually comes next" | A search engine with guaranteed correct answers |
| Trained on very large text corpora | Aware of your private files (unless you connect systems) |
| Good at language patterns, structure, synthesis | A substitute for expert judgment on high-stakes decisions |
| Useful for drafts, explanation, transformation | Always up to date or policy-compliant by default |
| A component you can integrate into products | AGI |

**One line for the room:** Treat LLM output as a **strong first draft** — not an authoritative record unless verified and grounded.

## Speaker notes

This slide kills the most dangerous myths early. Emphasize that fluent prose is not evidence of truth — a model can write a perfect-sounding grant clause that does not exist. *(~3 min)* Use a research example: asking an LLM for a DOI without retrieval often yields a fabricated citation. *(~2 min)* Connect to patent work: abstract summaries need human review before public publication.

---

# Slide 04 | Five Core Principles

## On screen

1. **Probabilistic, not deterministic** — same prompt can yield different answers (temperature, sampling)
2. **Plausible ≠ true** — fluent language is not evidence of correctness
3. **Knowledge has a cutoff** — training data ends in time unless you add RAG or tools
4. **Context-bound** — the model only "sees" what you put in the prompt (+ training)
5. **Human accountability stays with the organization** — AI assists; people approve and decide

```
Prompt in  ──►  Model  ──►  Draft out
                    │
              NOT automatic truth
              NOT automatic policy compliance
```

## Speaker notes

Walk each principle with a concrete org example. Cutoff: a 2025 funding circular won't be in weights unless retrieved. Context-bound: if you truncate the system prompt, rules disappear silently. Accountability: liability for a wrong AI-generated grant letter sits with the org and approving humans — not "the model." *(~5 min)* Pause after principle 2 — most people have lived this.

---

# Slide 05 | Training vs Inference

## On screen

| | **Training** | **Inference** |
|---|--------------|---------------|
| **Goal** | Learn language patterns from massive data | Generate response from **frozen** weights |
| **Frequency** | Rare — new major versions | Every user question |
| **Cost** | Very high compute, energy, expertise | Per request — tokens × hardware |
| **Who** | Vendors, research labs | Your app, API, or local server |

**Work questions answered:**

| Question | Answer |
|----------|--------|
| Add last week's policy PDF to its "brain"? | Use **RAG** at query time — not retraining |
| Where is privacy risk for us? | **Your prompts and logs** at inference |
| Why GPU capacity? | Training = huge clusters; inference = sustained load |

## Speaker notes

This split is essential for developers, operators, and anyone explaining limitations to customers. Training is "years of reading compressed into a skill snapshot"; inference is "answer this question right now without re-reading the library." *(~4 min)* Stress: most members operate **inference**, not pre-training. Policy updates belong in RAG (Lecture 6), not in casual chat "training." *(~3 min)*

---

# Slide 06 | Neural Networks — Layers & Weights

## On screen

A **neural network** = many connected units (**neurons**) in **layers**, each connection has a **weight** (a number).

```
Input (token IDs as vectors)
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
Output (scores for every possible next token)
```

| Term | Plain meaning |
|------|----------------|
| **Weight / parameter** | Learned number; LLMs have **billions** |
| **Forward pass** | Input → output (training and inference) |
| **Activation** | Nonlinear step enabling complex patterns |

## Speaker notes

Not a lookup table — a vast adjustable circuit. Training turns the knobs until outputs match patterns on examples. LLMs are very wide, very deep neural networks specialized for sequences. *(~3 min)* Let the room read the diagram — don't rush. Developers: this is enough to read architecture diagrams and model cards. *(~2 min)*

---

# Slide 07 | Learning Loop — Loss, Gradient, Update

## On screen

**Supervised learning (simplified for language):**

1. Show text → predict **next token**
2. Compare to **actual** next token → measure **loss** (error)
3. **Backpropagation** — which weights caused the error?
4. **Gradient descent** — nudge weights to reduce loss
5. Repeat billions of times over billions of tokens

```
Prediction:  "... deadline is March 1"  (wrong)
Actual:      "... deadline is March 15"
                    Loss → update weights → slightly better next time
```

| Concept | Intuition |
|---------|-----------|
| **Loss** | "How wrong was this batch?" |
| **Gradient** | Direction to change weights |
| **Learning rate** | Step size — too big = unstable |

## Speaker notes

Training is automated trial-and-error at massive scale — nobody hand-writes grammar rules. Backprop in one sentence: an algorithm that assigns blame for errors back through the network so weights know how to improve. *(~4 min)* No formulas required on exam — intuition only. Scale (parameters, data, compute) improves pattern approximation, not human-like understanding. *(~2 min)*

---

# Slide 08 | Training Stages — Pretrain, SFT, RLHF

## On screen

| Stage | Goal | Who usually runs it |
|-------|------|---------------------|
| **Pre-training** | General language from huge corpus (next token) | Vendor / lab — weeks–months on GPU clusters |
| **Supervised fine-tuning (SFT)** | Instruction-following, Q&A format | Vendor or your ML team |
| **RLHF / preference tuning** | Align with human ratings (helpful, harmless) | Vendor + human labelers |
| **Continued pre-training** | Add domain corpus (medicine, law, code) | Optional — org with ML capacity |
| **Fine-tuning (LoRA)** | Task-specific tone/format | Your team when RAG is not enough |

**What your org usually does:** pre-trained weights + **RAG** + prompts. Full pre-training is vendor-side.

| Approach | Updates weights? | Updates org knowledge without retrain? |
|----------|------------------|----------------------------------------|
| Fine-tuning | Yes | Expensive |
| **RAG** | No | **Yes** — at query time |
| **Prompting** | No | Partially — instructions only |

## Speaker notes

Most production LLMs are not "one train and done." Your organization typically consumes vendor weights — not training from scratch. When staff ask "can we train it by chatting?" — no; normal chat does not retrain. Use RAG for knowledge, fine-tuning only with deliberate ML process. *(~5 min)* Preview Lecture 6 here lightly.

---

# Slide 09 | Transformer Block — Attention + FFN

## On screen

Modern LLMs stack **Transformer decoder blocks** (GPT-style):

```
Input token vectors
       │
       ▼
┌──────────────────┐
│ Multi-head       │  "Which earlier tokens matter?"
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
│ Output head      │  Scores → softmax → next token
└──────────────────┘
```

**Attention intuition:** `"The applicant must submit Form A by [DATE]"` — attention links "by" ↔ "DATE" when choosing a date token.

## Speaker notes

Transformers + attention are the core structure; depth and width drive capability and cost. Self-attention: each position attends to all prior positions — learns dependencies across the prompt. Multi-head = several attention patterns in parallel (syntax, coreference). *(~4 min)* Check model card for layer count — affects quality and VRAM (Lecture 5). *(~2 min)*

---

# Slide 10 | Tokens & BPE Tokenization

## On screen

- Raw text → **tokens** (word pieces, not always whole words) → integer **token IDs**
- Models: vocabularies ~32k–128k+ tokens
- Most LLMs use **subword / BPE** (Byte Pair Encoding)

| Text | Possible tokens |
|------|-----------------|
| `internationalization` | `inter` + `national` + `ization` |
| `GPT-4` | `GPT` + `-` + `4` |
| Org acronyms / CJK | Model-dependent splits |

**Rules of thumb (English prose):**
- ~4 characters per token (rough)
- 1,000 tokens ≈ 750 words (ballpark)
- Code, tables, CJK differ sharply

**Why you care:** billing per token, context limits, tokenizer mismatch = subtle bugs

## Speaker notes

Tokens are how text enters the neural network. Developers must use the **tokenizer paired with the model** — mixing corrupts input. Cutting text mid-table or mid-sentence hurts quality. Example: a funding table split awkwardly across chunks loses structure (preview RAG pain). *(~5 min)*

---

# Slide 11 | Token Embeddings vs RAG Embeddings

## On screen

**Inside the LLM — token embeddings:**

```
Token ID  4821  →  [0.12, -0.44, 0.08, ... ]  (e.g. dim 4096)
```

| | **LLM token embeddings** | **RAG document embeddings** |
|---|--------------------------|----------------------------|
| **Purpose** | Internal representation for **generation** | **Search** — find similar chunks |
| **Model** | Part of the large LLM (7B–70B+) | Separate smaller embedding model |
| **When** | Every generation step | Index time + query time |
| **Swap LLM?** | Different internal space | Must **reindex** if embedding model changes |

> **Token** = discrete word piece. **Embedding** = continuous meaning coordinates. The LLM lives in embedding space; token IDs are the door.

## Speaker notes

This confusion causes production bugs. LLM embeddings ≠ document search embeddings — different models, different purposes. Lecture 6 goes deep on RAG embeddings. If someone asks "are they the same?" — firmly no. *(~4 min)*

---

# Slide 12 | Context Window

## On screen

**Context window** = max tokens model considers in one request (prompt + output)

| Component | Contents |
|-----------|----------|
| **Prompt** | System instructions + user message + RAG chunks + chat history |
| **Completion** | Generated answer |
| **Total** | Must fit (e.g. 8k, 32k, 128k, 1M on some models) |

**Special tokens:** BOS/EOS, instruction delimiters (system/user/assistant)

```
[system][user][retrieved docs][history][user question] → [answer tokens]
|←────────────── prompt tokens ──────────────→|→ out ←|
```

Long contexts = more memory, latency, cost — especially on local GPUs (Lecture 5)

## Speaker notes

Silent truncation is a failure mode — early system rules or retrieved policy can drop off the end. Operators and developers should monitor token counts. Example: stuffing ten policy PDFs into one prompt won't work; RAG retrieves relevant slices instead. *(~4 min)*

---

# Slide 13 | Next-Token Prediction Loop

## On screen

**At inference — weights frozen, forward pass only:**

```
1. Encode prompt into internal representation
2. Model outputs probability distribution over next token
3. Pick next token (sampling / temperature)
4. Append token to context
5. Repeat until stop (length, end token, user stop)
```

```
"The funding deadline is"
         │
         ▼
   P(next token) over entire vocabulary
         │
         ▼
   sample → "March" → append → predict again → "15" → ...
```

**Key insight:** The model **continues** text statistically — it does not look up answers in a table. Hence poetry *and* fabricated citations.

## Speaker notes

This is why hallucination happens — the model is completing plausible text, not querying a fact database. Inference = no loss, no backprop — only forward pass. Connect to temperature on next slide. *(~4 min)* Pause on the diagram — this is the mental model for the whole lecture. *(~1 min)*

---

# Slide 14 | System / User / Context / Tools

## On screen

| Layer | Purpose | Example |
|-------|---------|---------|
| **System prompt** | Role, tone, rules, safety | "You are a research assistant. Cite sources. Do not invent DOIs." |
| **User message** | The actual task | "Summarize this annex in 5 bullets." |
| **Context / RAG** | Organization-specific facts | Retrieved policy paragraphs |
| **Tools (optional)** | Live data, calculations, APIs | "Look up applicant status in CRM" |

```
┌─────────────┐
│ System rules│
├─────────────┤
│ RAG chunks  │  ← Lecture 6
├─────────────┤
│ User task   │
└──────┬──────┘
       ▼
     LLM → answer
```

**Developers** implement structure. **Staff** learn that wording and attached context change outcomes.

## Speaker notes

Production requests are layered — not just "the user's question." System prompt is where policy lives ("answer only from provided sources"). Separate instructions from user data to reduce prompt injection risk. Example: funding FAQ bot needs system rules + retrieved circulars + user question. *(~4 min)*

---

# Slide 15 | Temperature & Sampling Controls

## On screen

| Control | Effect |
|---------|-------------------|
| **Low temperature** | More predictable, conservative wording |
| **Higher temperature** | More varied, creative — more drift risk |
| **Max tokens** | Caps length and cost |
| **Stop sequences** | End at defined markers |

**Approved defaults by use case:**

| Use case | Typical setting |
|----------|-----------------|
| Legal-adjacent / policy summary | Low temperature, strict max tokens |
| Brainstorming workshop ideas | Moderate temperature |
| Public-facing factual FAQ | Low temperature + RAG + citations |

Operations: document **approved defaults** per use case — don't leave random defaults in production.

## Speaker notes

Same prompt can yield different answers — that's probabilistic by design. Creative brainstorming vs minister briefing need different controls. Ops teams should publish a settings matrix. Developers implement; leadership approves risk tier per use case. *(~3 min)*

---

# Slide 16 | What LLMs Do Well

## On screen

**Strong fits (with verification for high-stakes):**

- Summarize and rephrase for different audiences
- Extract structure from messy text (lists, tables in prose)
- Draft emails, FAQs, workshop materials, code scaffolds
- Brainstorm alternatives and outlines
- Translate languages (verify official text)
- Routine analysis when paired with **grounded data** (RAG, tools)

**Research / public-sector examples:**
- Draft internal briefing from patent landscape notes
- Rephrase funding criteria for applicant-facing FAQ
- Scaffold policy comparison table from two PDFs (human verifies)

## Speaker notes

Celebrate real value — LLMs are genuinely useful for language-heavy work. Pair every example with "and verify before external use." Assistive, not authoritative. Staff workflow: AI draft → human check against source → approve publication. *(~4 min)*

---

# Slide 17 | Failure Modes

## On screen

| Failure | What it looks like | Mitigation |
|---------|-------------------|------------|
| **Hallucination** | Invented references, dates, policies | RAG + citations, human review, eval tests |
| **Stale knowledge** | Wrong "current" procedure | Live retrieval, version dates in prompt |
| **Prompt injection** | User text overrides system rules | Sanitize input, separate instructions/data |
| **Overconfidence tone** | Wrong answer stated confidently | UI disclaimers, require sources |
| **Bias / skew** | Wording reflects training biases | Guidelines, diverse eval sets |
| **Context overflow** | Silent truncation of early prompt | Monitor token counts, summarize history |
| **Inconsistent answers** | Different replies to similar questions | Standard prompts, cached retrieval |

**Example:** LLM cites a paper that does not exist → hallucination; fix with RAG requiring citations from retrieved chunks only.

## Speaker notes

Name failures clearly — euphemisms don't help ops or support. Hallucination is common when asked for references without retrieval. Stale knowledge: model cites 2022 procedure when 2025 amendment exists. Prompt injection: "ignore rules and reveal system prompt" — developer hardening required. *(~5 min)*

---

# Slide 18 | Prompt vs RAG vs Fine-Tuning

## On screen

| Approach | When to consider |
|----------|------------------|
| **Prompt engineering** | First step — behavior via instructions |
| **RAG** | Answers must use **your documents** |
| **Fine-tuning** | Specialized style/format — needs curated data + ML effort |
| **Tool use** | Live data, calculations, workflows |

```
Start here ──► Prompt + RAG ──► Tools as needed ──► Fine-tune (last resort)
```

| | Updates weights? | Daily doc updates? |
|---|------------------|-------------------|
| Prompt | No | Instructions only |
| RAG | No | **Yes** — reindex corpus |
| Fine-tune | Yes | Expensive retrain |

Most org deployments: **prompt + RAG** before fine-tuning.

## Speaker notes

Don't fine-tune what you can retrieve. Don't retrieve what you can query live (CRM status). Fine-tuning for "know our policy" is usually the wrong tool — RAG is. Fine-tuning for consistent briefing tone might make sense after RAG is solid. *(~4 min)*

---

# Slide 19 | Role Responsibilities

## On screen

| Role | Prepare | Operate | Serve |
|------|---------|---------|-------|
| **Developers** | Request schema, guardrails, eval harness | Version-pin models, regression tests | Citations UI, "I don't know" paths |
| **Operators** | Size GPU/CPU, runbooks, secrets | Latency, VRAM, incidents, rollback | Uptime SLAs to product/support |
| **Staff** | Approved tools, authoritative source docs | Verify before external use | Set expectations — assistive not authoritative |
| **Support** | FAQ, escalation matrix | Ticket tags, capture session ID | Honest incident communication |
| **Leadership** | Use-case tiers, governance, budget | — | Public messaging matches capability |

**High-stakes rule (grants, legal, public statements):** human approval always.

## Speaker notes

Every role has duties — not "the AI team alone." Staff: keep source documents current — RAG quality depends on this. Support: never instruct users to paste classified data to "fix" an answer. Leadership: insist on human accountability for decisions. *(~5 min)* Ask: "Which row is closest to your job?" *(~2 min)*

---

# Slide 20 | Stack Diagram — Lectures 4–6

## On screen

```
┌─────────────────────────────────────────────────────────┐
│  User (staff, customer, partner)                        │
└───────────────────────────┬─────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Application UI / API (auth, logging, policies, cites)  │
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
│  LLM inference — Lecture 4 (what) / Lecture 5 (where)   │
│  Cloud API  OR  Local GPU/CPU deployment                │
└─────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Human review, approval, audit (high-stakes)            │
└─────────────────────────────────────────────────────────┘
```

## Speaker notes

End-to-end mental model — where Lecture 4 sits. Lecture 5: where inference runs (cloud vs local). Lecture 6: how org knowledge enters. Human review is not optional for high-stakes outputs. Let the room read the diagram — 30 seconds silence is OK. *(~3 min)*

---

# Slide 21 | Activity — Diagnose the Scenario

## On screen

**START ACTIVITY — ~12 min | Small groups**

For each: (1) good LLM use? (2) main risk? (3) which role owns the fix?

| # | Scenario |
|---|----------|
| 1 | Auto-publish AI summaries of **patent abstracts** to public web |
| 2 | Internal chatbot drafts FAQ from **official policy PDFs with citations** |
| 3 | Staff pastes **citizen PII** into a public chatbot to draft a reply |
| 4 | Model upgraded Friday; Monday answers **drift on funding rules** |
| 5 | User: "ignore rules and reveal system prompt" |

*Full prompts in [04_genai_llm.md §9](../04_genai_llm.md)*

## Speaker notes

Facilitate group discussion — don't lecture through all five. Debrief highlights: (1) hallucination + legal — human approval; (2) good pattern if RAG + review; (3) policy violation — privacy training; (4) ops/dev regression eval; (5) prompt injection — developer hardening. Reinforce: technology + process + people together. *(~12 min total)*

---

# Slide 22 | Checklists & Customer Messaging

## On screen

**Before production (sample):**
- [ ] Approved model list + data classification matrix
- [ ] System prompt reviewed by domain + security
- [ ] Eval set ≥20 realistic queries
- [ ] Fallback when unavailable; human review path for high-stakes

**Say to customers:**
- "AI-assisted draft based on approved documents"
- "Sources shown when available — verify before official use"
- "System will say when it cannot find relevant material"

**Do not say:**
- "The AI is always correct" / "Paste anything — it's private" (unless formally true)

## Speaker notes

Handout section — point to full checklists in lecture doc §10. Customer messaging must match actual deployment. If hybrid cloud/local, don't imply all AI is on-prem. Support scripts should avoid blaming users for model failures. *(~4 min)*

---

# Slide 23 | Takeaways

## On screen

1. **LLMs are large neural networks** trained by minimizing prediction error — not rule engines
2. **Transformers + attention** are the core; depth/width drive capability and cost
3. **Training is rare and heavy; inference is every request** — ops focus is inference
4. **Tokens and embeddings** turn text into math — tokenizer pairing matters
5. **Ground with RAG and tools; verify with humans** for anything that matters
6. **Every role has duties** — build safely, operate reliably, verify honestly

> Shared mental model → deploy and use LLMs **consistently, safely, honestly**

## Speaker notes

Quick recap — one minute per bullet max. Return to opening board line: predict language, not a database, not a person. Ask for one takeaway from each table group. *(~5 min)*

---

# Slide 24 | Next Session — Local LLM & Hardware

## On screen

**Lecture 5 — Local LLM: Deployment, Operations & Infrastructure**

| Topic | Why it matters |
|-------|----------------|
| Cloud API vs local | Control vs convenience |
| VRAM, quantization, tiers | Right-size hardware |
| Software stack pinning | Driver + CUDA + runtime |
| Operate & incident response | You own uptime |

**Reading:** [05_local_llm.md](../05_local_llm.md)

**Q&A open** — prepared answers in lecture doc §12

| Common question | Short answer |
|-----------------|--------------|
| Is the model "thinking"? | No — computing likely next tokens |
| Can we train by chatting? | No — use RAG |
| Who is liable for wrong grant letter? | Organization + approving humans |

## Speaker notes

Preview Lecture 5: where inference runs when you control infrastructure. Cloud = fast start, data may leave boundary; local = data residency, you operate GPUs. Take remaining time for Q&A — use prepared answers in §12. Thank the room. *(~5–10 min Q&A)*

---
