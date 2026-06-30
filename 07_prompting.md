# Prompting & AI Assistant Design

*For all members — developers, operators, staff, support, and leadership*  
*How to instruct LLMs effectively in production: prompts, context, templates, and iteration*

---

## Module Overview

This is **Lecture 7**. It turns LLM theory ([04_genai_llm.md](04_genai_llm.md)) and RAG ([06_rag.md](06_rag.md)) into **daily practice**: how to write prompts, design assistants, and iterate when answers are wrong.

| Lecture | Topic |
|---------|--------|
| 4–6 | LLM, local deployment, RAG — foundations |
| **7 (this)** | **Prompting & assistant design** |
| 8 | [Trust & verification](08_trust_verification.md) |
| 9 | [Privacy & security](09_privacy_security.md) |

**Duration:** 90 minutes  
**Prerequisites:** [04_genai_llm.md](04_genai_llm.md) (tokens, system prompt), [06_rag.md](06_rag.md) (retrieved context)

---

## Learning Objectives

By the end, members should be able to:

1. Distinguish **system, developer, user, and retrieved** prompt layers  
2. Write clear prompts using **goal, audience, format, constraints, examples**  
3. Design **production prompt templates** for common org tasks (FAQ, summary, draft)  
4. Iterate when output is wrong — without “prompt superstition”  
5. Know what **developers** vs **staff** vs **operators** own in prompt lifecycle  

---

## 1. Opening (5 min)

**Facilitator message:**

> “A prompt is not magic words — it is **the instruction packet** the model sees. In our stack, prompts include system rules, retrieved documents, and your question. Good prompting is good **communication engineering**.”

**Icebreaker:** Share one bad AI answer you saw. Was the **question** unclear, the **context** missing, or the **model** ungrounded?

---

## 2. Prompt Architecture — Layers & Responsibilities (12 min)

### 2.1 The full prompt stack

```
┌─────────────────────────────────────────────────────────────┐
│  SYSTEM PROMPT (developer / product — stable rules)           │
│  Role, tone, safety, citation rules, "I don't know" policy  │
├─────────────────────────────────────────────────────────────┤
│  DEVELOPER / TOOL CONTEXT (optional)                        │
│  Retrieved RAG chunks [1][2][3], tool results, metadata     │
├─────────────────────────────────────────────────────────────┤
│  CONVERSATION HISTORY (trimmed to fit token budget)         │
│  Prior user/assistant turns                                 │
├─────────────────────────────────────────────────────────────┤
│  USER MESSAGE (staff / customer — the task)                 │
│  Question, upload reference, format request                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
                         LLM generates
```

| Layer | Who writes it | Changes how often |
|-------|---------------|-------------------|
| **System** | Dev + domain + security review | Per release / policy change |
| **RAG context** | Automatic from corpus | Per query |
| **History** | User session | Every turn |
| **User** | Staff or customer | Every request |

**Principle:** Put **immutable rules** in system prompt; put **facts** in RAG; put **the task** in user message.

### 2.2 What staff control vs what they do not

| Staff **can** | Staff **cannot** (without dev/ops) |
|---------------|-------------------------------------|
| Clarify user question | Change system safety rules |
| Choose approved assistant / workflow | Swap model or embedding version |
| Attach allowed documents | Bypass ACL on corpus |
| Iterate wording in user message | Disable logging or retention policy |
| Request template update via ticket | Edit production system prompt directly |

---

## 3. Core Prompting Principles (15 min)

### 3.1 The GAFCO framework (use in every serious task)

| Letter | Meaning | Example |
|--------|---------|---------|
| **G** | **Goal** — what outcome? | “Draft a 5-bullet summary for a director” |
| **A** | **Audience** — who reads it? | “Non-technical minister; formal tone” |
| **F** | **Format** — structure? | “Bullets, max 80 words each, no jargon” |
| **C** | **Constraints** — limits? | “Only use attached sources; say if missing” |
| **O** | **Examples** (optional) — pattern? | “Like this sample FAQ entry: …” |

**Weak prompt:**

> “Summarize the grant policy.”

**Strong prompt (staff user message):**

> **Goal:** Summarize eligibility rules for internal orientation.  
> **Audience:** New program officers, plain language.  
> **Format:** Numbered list, max 6 items, each ≤2 sentences.  
> **Constraints:** Use only retrieved policy chunks; cite [Source ID]; if deadline not in sources, say “not stated in provided documents.”

### 3.2 Specificity vs verbosity

| Too vague | Too bloated | Balanced |
|-----------|-------------|----------|
| “Fix this.” | 3 pages of background in user msg | Goal + format + constraints in &lt;200 words |
| “Be helpful.” | Repeating same rule 10 times | Critical rules in **system** once |

**Token discipline ([04](04_genai_llm.md)):** Long user prompts eat context needed for RAG chunks.

### 3.3 Grounding instructions (RAG assistants)

Standard system-level rules for org Q&A:

```
1. Answer ONLY from provided sources labeled [1], [2], ...
2. Cite source ID after each factual claim.
3. If sources conflict, state both and recommend human review.
4. If sources are insufficient, reply: "I don't have enough information
   in the approved documents to answer reliably."
5. Do not invent dates, amounts, legal citations, or contact details.
```

**Representative example — funding FAQ:**

| User asks | Good behavior | Bad behavior |
|-----------|---------------|--------------|
| “Maximum co-funding rate for SME track?” | Quotes §4.2 with [Source 2] | Invents “typically 50%” from training memory |
| “Deadline for 2026 cycle?” | “Not in provided documents” if missing | Guesses March 31 |

### 3.4 Temperature & creativity (link to ops policy)

| Task type | Temperature guidance | Example |
|-----------|---------------------|---------|
| Policy Q&A, compliance | **Low** | RAG FAQ bot |
| Brainstorm workshop titles | **Medium** | Internal ideation |
| Creative exhibition copy draft | **Medium–higher** | Marketing first draft |

Operators document defaults ([05_local_llm.md](05_local_llm.md)); staff request changes through product — not ad hoc per session.

---

## 4. Prompt Patterns by Use Case (20 min)

### 4.1 Pattern library (research & public-sector)

| Pattern | Template skeleton | Example domain |
|---------|-------------------|----------------|
| **Summarize** | “Summarize [doc] for [audience] in [format]. Include [must-have topics].” | 40-page annex → director brief |
| **Extract** | “Extract all [entities] as a table: columns []. Unknown = ‘not found’.” | Dates, amounts from circular |
| **Compare** | “Compare [A] and [B] on [criteria]. Note only explicit differences in sources.” | 2024 vs 2025 policy |
| **Draft** | “Draft [doc type] for [audience]. Mark uncertain claims with [VERIFY].” | Workshop invitation |
| **Explain** | “Explain [concept] as if to [audience]. No new facts beyond sources.” | Patent process for startups |
| **Checklist** | “Given [requirements], list pass/fail per item with citation.” | Grant completeness pre-check |
| **Rewrite** | “Rewrite for [audience/tone]. Preserve meaning; do not add claims.” | Public-facing summary |

### 4.2 Worked example — literature summary (research)

**Context:** RAG retrieved 3 paper abstracts on “solid-state battery electrolytes.”

**User message:**

```
Goal: Brief literature snapshot for weekly lab meeting.
Audience: Materials scientists — they know the field; avoid basics.
Format: Table with columns: Finding | Method | Limitation | [Source]
Constraints: Only the 3 provided abstracts. Max 400 words total.
```

**Expected output shape:**

| Finding | Method | Limitation | Source |
|---------|--------|------------|--------|
| Ionic conductivity ↑ 20% at room temp | Ceramic-polymer composite | Only tested 100 cycles | [2] |

### 4.3 Worked example — policy plain language (government)

**User message:**

```
Goal: Plain-language version of §7.3 conflict-of-interest rules.
Audience: University grant applicants, English second language.
Format: Short paragraphs + 3 bullet “you must not” + 3 bullet “you may”.
Constraints: Cite §7.3 only. Flag anything needing legal review with [LEGAL].
```

**Staff action after generation:** Legal review before publishing — prompt does not replace counsel.

### 4.4 Worked example — patent first draft (innovation)

**User message:**

```
Goal: First-draft background section for attorney review.
Audience: Patent attorney — technical, concise.
Format: 3 paragraphs: field, prior art gap, problem solved.
Constraints: Use only uploaded invention disclosure [ATTACH]. 
Do not claim prior art we did not cite. Mark guesses [VERIFY].
```

### 4.5 Few-shot examples (when to use)

**Few-shot** = include 1–3 input/output examples in the prompt.

| Use few-shot when | Skip few-shot when |
|-------------------|-------------------|
| Fixed output schema (JSON, table) | RAG + clear format instructions enough |
| Consistent citation style | Examples would bloat context |
| Classification into org categories | Simple Q&A |

**Example — classify inquiry type:**

```
Classify into: FUNDING | PATENT | EXHIBITION | OTHER.
Examples:
"How do I file a PCT?" → PATENT
"When is the next call?" → FUNDING
```

---

## 5. Multi-Turn Conversation & Context Management (10 min)

### 5.1 How chat history works

Each turn resends **system + history + new user message** (within token limit).

```
Turn 1: User asks → Assistant answers
Turn 2: User says "make it shorter" → Model sees Turn 1 + answer + new instruction
```

**Risks:**

| Risk | Mitigation |
|------|------------|
| Early instructions “forgotten” (truncation) | Summarize history; restate constraints in new turn |
| Error compounds across turns | Start fresh session for new topic |
| PII accumulates in history | Session limits; no paste of citizen data |

### 5.2 When to start a new session

- New subject (funding → patents)  
- Model clearly derailed  
- After policy or model upgrade ([10_evaluation_quality.md](10_evaluation_quality.md))  
- Before high-stakes export (minister brief, public web)

### 5.3 Diagram — conversation vs RAG refresh

```
                    NEW USER TURN
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
   Same session?    Re-run RAG?     Restate constraints?
   (history)        (fresh chunks)   in user message
```

---

## 6. Assistant Design — Product & Developer View (12 min)

### 6.1 Anatomy of an org “assistant”

| Component | Example |
|-----------|---------|
| **Name & scope** | “Funding FAQ Assistant — internal only” |
| **System prompt v3.2** | Versioned in git |
| **Corpus binding** | `funding_policies_2025` collection |
| **Model pin** | `llm-7b-q4 v1.2` ([05](05_local_llm.md)) |
| **Embedding pin** | `embed-multilingual-v2` ([06](06_rag.md)) |
| **UI affordances** | Citation links, feedback button, “insufficient info” |
| **Guardrails** | Max upload, blocked topics, injection filters ([09](09_privacy_security.md)) |

### 6.2 Prompt versioning (non-negotiable for production)

```
prompt_bundle = {
  system_prompt_version: "funding-faq-2025-06-01",
  rag_collection_version: "idx-20250628",
  model_version: "llm-7b-q4-r1",
}
```

Log `prompt_bundle` with each response for debugging ([10](10_evaluation_quality.md)).

### 6.3 Anti-patterns in assistant design

| Anti-pattern | Why it fails |
|--------------|--------------|
| “You are the world’s best expert…” | Flattery does not improve grounding |
| Rules only in marketing copy, not system prompt | Model never sees them |
| Letting user text override system (“ignore previous”) | Prompt injection |
| One giant prompt for all departments | Wrong corpus, wrong tone |
| No “I don’t know” path | Forces hallucination |

---

## 7. Iteration — When Output Is Wrong (10 min)

### 7.1 Diagnostic flowchart

```
Output wrong?
     │
     ├─► Wrong facts ──► Check RAG citations first
     │                      ├─ No relevant source? → corpus/index issue
     │                      └─ Source ok but answer wrong? → prompt or model
     │
     ├─► Wrong format ──► Clarify GAFCO in user message; few-shot example
     │
     ├─► Wrong tone ──► Audience + rewrite instruction
     │
     ├─► Too long/short ──► Explicit word/bullet limits
     │
     └─► Inconsistent across runs ──► Lower temperature; standardize template
```

### 7.2 What to log when reporting issues

| Field | Why |
|-------|-----|
| Session / request ID | Reproduce |
| Assistant name & versions | Isolate regression |
| User message (redacted) | Prompt fix |
| Retrieved source IDs | RAG vs generation blame |
| Expected vs actual | Eval case candidate |

### 7.3 Staff vs developer fix

| Fix type | Owner |
|----------|-------|
| Clearer user question | Staff |
| Corpus update | Staff + knowledge owner |
| System prompt / template | Dev + domain |
| Retrieval tuning | Dev |
| Model upgrade | Ops + dev + eval ([10](10_evaluation_quality.md)) |

---

## 8. Activity — Rewrite the Prompt (15 min)

**Instructions:** Groups get a weak prompt + scenario. Rewrite using GAFCO. Compare outputs if live demo allowed.

| # | Scenario | Weak prompt | Discuss |
|---|----------|-------------|---------|
| 1 | Minister brief from 30-page report | “Summarize this.” | Audience, length, citations |
| 2 | Patent prior-art sketch | “Write background.” | Sources, [VERIFY], attorney review |
| 3 | Citizen-style FAQ | “What’s the deadline?” | RAG-only, insufficient-info path |
| 4 | Exhibition blurb | “Make it exciting!” | Tone bounds, fact grounding |

**Debrief:** Best improvements are **constraints and format**, not longer prompts.

---

## 9. Role Responsibilities

| Role | Prompt duties |
|------|----------------|
| **Staff** | Use templates; GAFCO user messages; verify outputs ([08](08_trust_verification.md)) |
| **Developers** | System prompts, versioning, injection defenses, eval hooks |
| **Operators** | Model/temperature defaults, logging, capacity |
| **Product** | Assistant scope, approved use cases, UX for citations |
| **Support** | Collect reproduction info; never tell users to “just rephrase until it works” for policy answers |
| **Leadership** | Approve high-stakes assistants; resource time for template maintenance |

---

## 10. Q&A — Prepared Answers

**Q: Longer prompts always better?**  
A: No. Precise beats long. Protect token budget for RAG context.

**Q: Can staff edit the system prompt?**  
A: No in production — change request via dev/product with review.

**Q: English vs local language prompts?**  
A: Match user language; ensure corpus and embedding model support it ([06](06_rag.md)).

**Q: “Act as a lawyer”?**  
A: Role-play does not confer legal accuracy — always human review for legal text.

---

## 11. Slide Outline (22 slides)

1. Title & objectives  
2. Prompt stack diagram  
3. Who owns each layer  
4. GAFCO framework  
5. Weak vs strong example  
6. RAG grounding rules  
7. Temperature by task  
8. Pattern library table  
9. Worked example — research  
10. Worked example — policy  
11. Few-shot when/when not  
12. Multi-turn risks  
13. New session decision  
14. Assistant anatomy  
15. Prompt versioning  
16. Anti-patterns  
17. Debug flowchart  
18. Reporting template  
19. Activity  
20. Role matrix  
21. Takeaways  
22. Next — trust & verification  

---

## 12. Takeaways

1. **Prompt = structured instruction packet** — system rules, RAG facts, user task.  
2. **GAFCO** — goal, audience, format, constraints, (optional) examples.  
3. **Ground RAG assistants** with cite-only rules and explicit “I don’t know.”  
4. **Version and log** production prompts with model and index IDs.  
5. **Debug wrong answers** — sources first, then prompt, then model.  
6. **Next:** [08_trust_verification.md](08_trust_verification.md) — verifying before trust.

---

## 13. Printable — Prompt Template Card

```
USER MESSAGE CHECKLIST
[ ] Goal stated
[ ] Audience named
[ ] Format specified (bullets/table/length)
[ ] Constraints (sources only? dates?)
[ ] High-stakes → plan human review

AFTER OUTPUT
[ ] Citations checked against sources
[ ] No [VERIFY] items left unresolved
[ ] OK to use for intended tier (draft vs publish)
```
