# Slide Deck — Prompting & AI Assistant Design

*Source: [07_prompting.md](../07_prompting.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Prompting & AI Assistant Design**

*Lecture 7 — ST-EOS-REF*

For all members: developers, operators, staff, support, leadership

**Duration:** 90 minutes

**Prerequisites:** LLM foundations (Lecture 4), RAG (Lecture 6)

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Distinguish **system, developer, user, and retrieved** prompt layers |
| 2 | Write clear prompts using **GAFCO** |
| 3 | Design **production prompt templates** for org tasks |
| 4 | Iterate when output is wrong — without "prompt superstition" |
| 5 | Know what **developers vs staff vs operators** own |

## Speaker notes

Open with the facilitator message: a prompt is not magic words — it is the instruction packet the model sees. In our stack, prompts include system rules, retrieved documents, and the user's question. Good prompting is good communication engineering.

Icebreaker (2 min): ask the room to share one bad AI answer they saw. Was the question unclear, the context missing, or the model ungrounded? This sets up the rest of the session.

---

# Slide 02 | The Prompt Stack

## On screen

**The full prompt stack**

```
┌─────────────────────────────────────────────────────────────┐
│  SYSTEM PROMPT (developer / product — stable rules)           │
│  Role, tone, safety, citation rules, "I don't know" policy  │
├─────────────────────────────────────────────────────────────┤
│  DEVELOPER / TOOL CONTEXT (optional)                        │
│  Retrieved RAG chunks [1][2][3], tool results, metadata     │
├─────────────────────────────────────────────────────────────┤
│  CONVERSATION HISTORY (trimmed to fit token budget)         │
│  Prior user/assistant turns                                   │
├─────────────────────────────────────────────────────────────┤
│  USER MESSAGE (staff / customer — the task)                   │
│  Question, upload reference, format request                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
                         LLM generates
```

**Principle:** Immutable rules → **system**. Facts → **RAG**. Task → **user message**.

## Speaker notes

Walk top-to-bottom through the stack. Emphasize that everything the model "knows" for a given request arrives in one of these layers — there is no hidden database lookup.

Link back to Lecture 4 (tokens, context window) and Lecture 6 (retrieved chunks). Pause on the diagram — let the room read before explaining each layer.

---

# Slide 03 | Who Owns Each Layer

## On screen

| Layer | Who writes it | Changes how often |
|-------|---------------|-------------------|
| **System** | Dev + domain + security review | Per release / policy change |
| **RAG context** | Automatic from corpus | Per query |
| **History** | User session | Every turn |
| **User** | Staff or customer | Every request |

**What staff CAN do**

| ✓ Staff can | ✗ Staff cannot (without dev/ops) |
|-------------|----------------------------------|
| Clarify user question | Change system safety rules |
| Choose approved assistant / workflow | Swap model or embedding version |
| Attach allowed documents | Bypass ACL on corpus |
| Iterate wording in user message | Disable logging or retention policy |
| Request template update via ticket | Edit production system prompt directly |

## Speaker notes

This slide prevents two common failures: staff trying to hack around system rules, and developers assuming staff will "just write better prompts" without templates.

Stress the ticket path for template updates — it is not bureaucracy; it is versioning and review. Ask: "Who in this room has wanted to change how the bot behaves?" — route that energy to the right owner.

---

# Slide 04 | GAFCO Framework

## On screen

**Use GAFCO for every serious task**

| Letter | Meaning | Example |
|--------|---------|---------|
| **G** | **Goal** — what outcome? | "Draft a 5-bullet summary for a director" |
| **A** | **Audience** — who reads it? | "Non-technical minister; formal tone" |
| **F** | **Format** — structure? | "Bullets, max 80 words each, no jargon" |
| **C** | **Constraints** — limits? | "Only use attached sources; say if missing" |
| **O** | **Examples** (optional) — pattern? | "Like this sample FAQ entry: …" |

**Mnemonic:** *Goal, Audience, Format, Constraints, (Optional) Examples*

## Speaker notes

GAFCO is the org standard for staff-written user messages. It replaces vague instructions like "be helpful" or "summarize this."

Examples (O) are optional — use when output schema is fixed or citation style must be consistent. Do not read every cell; pick one row and expand verbally.

---

# Slide 05 | Weak vs Strong Prompt

## On screen

**Weak prompt**

> "Summarize the grant policy."

**Strong prompt (staff user message)**

> **Goal:** Summarize eligibility rules for internal orientation.  
> **Audience:** New program officers, plain language.  
> **Format:** Numbered list, max 6 items, each ≤2 sentences.  
> **Constraints:** Use only retrieved policy chunks; cite [Source ID]; if deadline not in sources, say "not stated in provided documents."

**Specificity vs verbosity**

| Too vague | Too bloated | Balanced |
|-----------|-------------|----------|
| "Fix this." | 3 pages of background in user msg | Goal + format + constraints in <200 words |
| "Be helpful." | Repeating same rule 10 times | Critical rules in **system** once |

## Speaker notes

Contrast the two prompts side by side. The weak prompt forces the model to guess audience, length, and grounding rules.

Token discipline reminder: long user prompts eat context needed for RAG chunks (Lecture 4). Precise beats long. If a rule applies to every request, it belongs in the system prompt — not repeated in every user message.

---

# Slide 06 | RAG Grounding Rules

## On screen

**Standard system-level rules for org Q&A**

```
1. Answer ONLY from provided sources labeled [1], [2], ...
2. Cite source ID after each factual claim.
3. If sources conflict, state both and recommend human review.
4. If sources are insufficient, reply: "I don't have enough information
   in the approved documents to answer reliably."
5. Do not invent dates, amounts, legal citations, or contact details.
```

**Representative example — funding FAQ**

| User asks | Good behavior | Bad behavior |
|-----------|---------------|--------------|
| "Maximum co-funding rate for SME track?" | Quotes §4.2 with [Source 2] | Invents "typically 50%" from training memory |
| "Deadline for 2026 cycle?" | "Not in provided documents" if missing | Guesses March 31 |

## Speaker notes

These five rules are non-negotiable for RAG assistants in production. Developers put them in the system prompt; staff enforce them by checking citations after generation.

Walk through the funding FAQ table — the bad behaviors are exactly what Lecture 8 (trust & verification) will address. "I don't know" is a feature, not a failure.

---

# Slide 07 | Temperature by Task

## On screen

| Task type | Temperature guidance | Example |
|-----------|---------------------|---------|
| Policy Q&A, compliance | **Low** | RAG FAQ bot |
| Brainstorm workshop titles | **Medium** | Internal ideation |
| Creative exhibition copy draft | **Medium–higher** | Marketing first draft |

**Policy**

- Operators document defaults (Lecture 5)
- Staff request changes through product — **not ad hoc per session**
- Lower temperature → more consistent factual output
- Higher temperature → more variation in creative tasks

## Speaker notes

Temperature is an ops/product setting, not something staff should tweak session-by-session for policy answers. If outputs are inconsistent across runs for factual tasks, first check temperature before rewriting prompts.

Connect to the debug flowchart later: inconsistent factual output → lower temperature + standardize template.

---

# Slide 08 | Pattern Library

## On screen

**Prompt patterns for research & public-sector work**

| Pattern | Template skeleton | Example domain |
|---------|-------------------|----------------|
| **Summarize** | "Summarize [doc] for [audience] in [format]. Include [must-have topics]." | 40-page annex → director brief |
| **Extract** | "Extract all [entities] as a table: columns []. Unknown = 'not found'." | Dates, amounts from circular |
| **Compare** | "Compare [A] and [B] on [criteria]. Note only explicit differences in sources." | 2024 vs 2025 policy |
| **Draft** | "Draft [doc type] for [audience]. Mark uncertain claims with [VERIFY]." | Workshop invitation |
| **Explain** | "Explain [concept] as if to [audience]. No new facts beyond sources." | Patent process for startups |
| **Checklist** | "Given [requirements], list pass/fail per item with citation." | Grant completeness pre-check |
| **Rewrite** | "Rewrite for [audience/tone]. Preserve meaning; do not add claims." | Public-facing summary |

## Speaker notes

This is the org pattern library — product and dev should turn these into approved templates in the UI where possible. Staff pick a pattern, fill in GAFCO fields.

Spend 30 seconds on each row or focus on the two most relevant to your audience. Emphasize [VERIFY] in Draft pattern — it signals human review items without hiding uncertainty.

---

# Slide 09 | Worked Example — Research

## On screen

**Literature summary (research lab)**

**Context:** RAG retrieved 3 paper abstracts on "solid-state battery electrolytes."

**User message:**

```
Goal: Brief literature snapshot for weekly lab meeting.
Audience: Materials scientists — they know the field; avoid basics.
Format: Table with columns: Finding | Method | Limitation | [Source]
Constraints: Only the 3 provided abstracts. Max 400 words total.
```

**Expected output shape**

| Finding | Method | Limitation | Source |
|---------|--------|------------|--------|
| Ionic conductivity ↑ 20% at room temp | Ceramic-polymer composite | Only tested 100 cycles | [2] |

## Speaker notes

Walk through how GAFCO maps to a real research task. Audience "avoid basics" prevents the model from wasting tokens on textbook definitions.

After generation, staff still verify key numbers against abstracts (Lecture 8). This is T1 assist tier — review before sharing outside the team.

Optional live demo: run this prompt if environment allows.

---

# Slide 10 | Worked Example — Policy

## On screen

**Plain-language policy summary (government)**

**User message:**

```
Goal: Plain-language version of §7.3 conflict-of-interest rules.
Audience: University grant applicants, English second language.
Format: Short paragraphs + 3 bullet "you must not" + 3 bullet "you may".
Constraints: Cite §7.3 only. Flag anything needing legal review with [LEGAL].
```

**After generation — staff action**

| Step | Owner |
|------|-------|
| Check citations against §7.3 | Staff |
| Resolve all [LEGAL] flags | Legal counsel |
| Publish | Named approver (T2) |

> Prompt does **not** replace counsel.

## Speaker notes

This example shows GAFCO for government plain-language work. The [LEGAL] flag creates an explicit handoff — the model drafts, humans decide what is safe to publish.

Stress: role-play ("act as a lawyer") does not confer legal accuracy. Always human review for legal text. Map to T2 publish tier from Lecture 8.

---

# Slide 11 | Few-Shot — When / When Not

## On screen

**Few-shot** = include 1–3 input/output examples in the prompt.

| Use few-shot when | Skip few-shot when |
|-------------------|-------------------|
| Fixed output schema (JSON, table) | RAG + clear format instructions enough |
| Consistent citation style | Examples would bloat context |
| Classification into org categories | Simple Q&A |

**Example — classify inquiry type**

```
Classify into: FUNDING | PATENT | EXHIBITION | OTHER.
Examples:
"How do I file a PCT?" → PATENT
"When is the next call?" → FUNDING
```

**Also covered:** Patent first-draft pattern — use [VERIFY] for guesses; attorney review required.

## Speaker notes

Few-shot is powerful but costs tokens. Use when the output shape is hard to describe in words alone.

Briefly mention the patent first-draft pattern from the lecture: Goal = background section for attorney; Constraints = only uploaded disclosure, mark guesses [VERIFY]. That is T3 decision-adjacent — attorney only for filing.

---

# Slide 12 | Multi-Turn Risks

## On screen

**How chat history works**

```
Turn 1: User asks → Assistant answers
Turn 2: User says "make it shorter" → Model sees Turn 1 + answer + new instruction
```

Each turn resends **system + history + new user message** (within token limit).

| Risk | Mitigation |
|------|------------|
| Early instructions "forgotten" (truncation) | Summarize history; restate constraints in new turn |
| Error compounds across turns | Start fresh session for new topic |
| PII accumulates in history | Session limits; no paste of citizen data |

## Speaker notes

Multi-turn chat feels like memory, but it is re-sent context — and it can be truncated. If the model "forgets" a constraint from turn 1, restate it explicitly rather than assuming it persists.

PII in history is a privacy issue (Lecture 9). Do not paste citizen data into long sessions.

---

# Slide 13 | When to Start a New Session

## On screen

**Start a new session when:**

- New subject (funding → patents)
- Model clearly derailed
- After policy or model upgrade (Lecture 10)
- Before high-stakes export (minister brief, public web)

```
                    NEW USER TURN
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
   Same session?    Re-run RAG?     Restate constraints?
   (history)        (fresh chunks)   in user message
```

**Decision guide**

| Situation | Action |
|-----------|--------|
| Follow-up on same topic | Same session + restate format if needed |
| Topic change | New session |
| Wrong sources retrieved | New turn triggers fresh RAG automatically |
| Stale or confused context | New session |

## Speaker notes

New session is cheap — wrong context is expensive. Before exporting a minister brief, start fresh with a complete GAFCO user message rather than relying on a long chat history.

Walk through the three-way decision diagram briefly.

---

# Slide 14 | Assistant Anatomy

## On screen

**Anatomy of an org "assistant"**

| Component | Example |
|-----------|---------|
| **Name & scope** | "Funding FAQ Assistant — internal only" |
| **System prompt v3.2** | Versioned in git |
| **Corpus binding** | `funding_policies_2025` collection |
| **Model pin** | `llm-7b-q4 v1.2` (Lecture 5) |
| **Embedding pin** | `embed-multilingual-v2` (Lecture 6) |
| **UI affordances** | Citation links, feedback button, "insufficient info" |
| **Guardrails** | Max upload, blocked topics, injection filters (Lecture 9) |

An assistant is a **product bundle** — not just a chat box.

## Speaker notes

Shift from staff view to product/developer view. Every production assistant should be documented like a release artifact.

Ask developers in the room: can you name the system prompt version running in prod right now? If not, that is tech debt.

---

# Slide 15 | Prompt Versioning

## On screen

**Non-negotiable for production**

```
prompt_bundle = {
  system_prompt_version: "funding-faq-2025-06-01",
  rag_collection_version: "idx-20250628",
  model_version: "llm-7b-q4-r1",
}
```

**Log `prompt_bundle` with each response** for debugging (Lecture 10).

| Why version? | What it enables |
|--------------|-----------------|
| Regression isolation | "Did the prompt or the index change?" |
| Audit trail | Reproduce any response |
| Safe rollback | Revert to known-good bundle |

## Speaker notes

When staff report "it worked yesterday," the first question is: what changed in the bundle? Without versioning, debugging is guesswork.

Ops logs model version; dev logs prompt and index version; product owns the assistant name and scope register.

---

# Slide 16 | Anti-Patterns

## On screen

| Anti-pattern | Why it fails |
|--------------|--------------|
| "You are the world's best expert…" | Flattery does not improve grounding |
| Rules only in marketing copy, not system prompt | Model never sees them |
| Letting user text override system ("ignore previous") | Prompt injection |
| One giant prompt for all departments | Wrong corpus, wrong tone |
| No "I don't know" path | Forces hallucination |

**Remember:** Production prompts are engineering artifacts — not creative writing exercises.

## Speaker notes

These anti-patterns come from real deployments. The "world's best expert" line is harmless but useless; the injection and missing "I don't know" paths are dangerous.

Connect "ignore previous" to Lecture 9 prompt injection defenses. One assistant per scope/corpus keeps retrieval accurate.

---

# Slide 17 | Debug Flowchart

## On screen

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

**Order of blame:** sources → prompt → model

## Speaker notes

This is the org debugging doctrine. Wrong facts almost always start with citation check — not "try rephrasing until it works."

For policy answers, support staff should never advise users to rephrase randomly; they should collect reproduction info and escalate.

Walk through one branch verbally — e.g., wrong facts with no relevant source → corpus or retrieval tuning, not a longer prompt.

---

# Slide 18 | Reporting Template

## On screen

**What to log when reporting issues**

| Field | Why |
|-------|-----|
| Session / request ID | Reproduce |
| Assistant name & versions | Isolate regression |
| User message (redacted) | Prompt fix |
| Retrieved source IDs | RAG vs generation blame |
| Expected vs actual | Eval case candidate |

**Who fixes what**

| Fix type | Owner |
|----------|-------|
| Clearer user question | Staff |
| Corpus update | Staff + knowledge owner |
| System prompt / template | Dev + domain |
| Retrieval tuning | Dev |
| Model upgrade | Ops + dev + eval (Lecture 10) |

## Speaker notes

Give support and staff a concrete ticket template. Redact PII from user messages in tickets.

Every bad answer is a potential eval case (Lecture 10) — capture expected vs actual when you can.

---

# Slide 19 | Activity — Rewrite the Prompt

## On screen

**START ACTIVITY — 15 minutes**

Groups receive a weak prompt + scenario. Rewrite using **GAFCO**. Compare outputs if live demo allowed.

| # | Scenario | Weak prompt | Discuss |
|---|----------|-------------|---------|
| 1 | Minister brief from 30-page report | "Summarize this." | Audience, length, citations |
| 2 | Patent prior-art sketch | "Write background." | Sources, [VERIFY], attorney review |
| 3 | Citizen-style FAQ | "What's the deadline?" | RAG-only, insufficient-info path |
| 4 | Exhibition blurb | "Make it exciting!" | Tone bounds, fact grounding |

**Debrief question:** What improved most — constraints, format, or length?

## Speaker notes

Split into groups of 3–4. Each group picks one scenario (or assign 1–4 round-robin). They rewrite on paper or in the live assistant if available.

Debrief (5 min): best improvements are **constraints and format**, not longer prompts. Call on one group per scenario. Full activity details in lecture doc §8.

---

# Slide 20 | Role Matrix

## On screen

| Role | Prompt duties |
|------|----------------|
| **Staff** | Use templates; GAFCO user messages; verify outputs (Lecture 8) |
| **Developers** | System prompts, versioning, injection defenses, eval hooks |
| **Operators** | Model/temperature defaults, logging, capacity |
| **Product** | Assistant scope, approved use cases, UX for citations |
| **Support** | Collect reproduction info; never "just rephrase until it works" for policy |
| **Leadership** | Approve high-stakes assistants; resource time for template maintenance |

## Speaker notes

Quick accountability slide — everyone has a lane. Support's "never just rephrase" rule protects citizens from hallucinated policy answers.

Leadership: template maintenance is ongoing work, not a one-time launch task.

---

# Slide 21 | Takeaways

## On screen

1. **Prompt = structured instruction packet** — system rules, RAG facts, user task.
2. **GAFCO** — Goal, Audience, Format, Constraints, (optional) Examples.
3. **Ground RAG assistants** with cite-only rules and explicit "I don't know."
4. **Version and log** production prompts with model and index IDs.
5. **Debug wrong answers** — sources first, then prompt, then model.

**Printable — Prompt Template Card**

```
USER MESSAGE CHECKLIST
[ ] Goal stated          [ ] Audience named
[ ] Format specified     [ ] Constraints (sources only?)
[ ] High-stakes → plan human review

AFTER OUTPUT
[ ] Citations checked    [ ] No [VERIFY] left unresolved
[ ] OK for intended tier (draft vs publish)
```

## Speaker notes

Recap the five takeaways. Offer the printable card as a handout or Slack pin.

Q&A prep: longer prompts are not always better; staff cannot edit production system prompts; match user language to corpus/embedding support; "act as a lawyer" does not replace counsel.

---

# Slide 22 | Next — Trust & Verification

## On screen

**Lecture 8: Trust, Accuracy & Human Judgment**

| Today (Lecture 7) | Next (Lecture 8) |
|-------------------|------------------|
| How to instruct the model | How to **verify** before trusting |
| GAFCO, templates, iteration | Hallucination, risk tiers, VERIFY checklist |
| Prompt layers & versioning | Human-in-the-loop workflows |

**Bridge question:** You wrote a strong prompt and got a fluent answer with citations. Is that enough to publish?

→ **No.** Fluent ≠ verified. See you in Lecture 8.

*Source: [08_trust_verification.md](../08_trust_verification.md)*

## Speaker notes

Close the session with the bridge question. Even perfect prompting does not eliminate hallucination or stale sources — verification is the next gate.

Remind the room of the icebreaker bad answer — would GAFCO + grounding rules have fixed it, or would verification still have been needed?

Point to the printable card and lecture doc §13 for post-session reference.

---
