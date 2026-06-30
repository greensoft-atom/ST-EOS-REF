# Slide 01 | Two Families of AI

## On screen

**Generative AI vs Recognition AI**

*For non-technical staff — general purpose, pre-launch-safe*

| | **Recognition AI** | **Generative AI** |
|---|---------------------|-------------------|
| Core question | “What *is* this?” | “What *could* this be?” |
| Output | Label, score, rank | Text, image, code, draft |
| Analogy | Security guard checking ID | Writer producing a first draft |

> **Recognition AI classifies and predicts; generative AI produces new content from instructions.**

---

## Speaker notes

Welcome to Lecture 03. This session is introductory — no deployment details yet. Frame the whole series: Lecture 2 previewed how AI learns; this lecture names the two families everyone will encounter. Neither type is AGI; both are narrow in different ways. Set expectation: 45 minutes, ending with a sorting activity.

---

# Slide 02 | Recognition AI — Definition

## On screen

**Recognition AI** (also: discriminative AI, pattern recognition)

**What it does:** learns patterns → **maps input → output** (category, score, decision)

**How it works (no math):**

```
1. Many labeled examples  ("spam / not spam", "cat / dog")
2. System learns distinguishing patterns
3. New input → recognized bucket
```

**Analogy:** Security guard checking ID — **recognize → decide**

Does **not** write a new report; it **judges** or **sorts** what it sees.

---

## Speaker notes

Emphasize that recognition AI has been in production for decades — email spam filters, face unlock, fraud scoring. Staff may already use it without knowing the label. Contrast with “creative” AI later. Ask: “Who got a ‘recommended for you’ list this week?” That’s often recognition/ranking.

---

# Slide 03 | Recognition AI — Examples

## On screen

**Common capabilities**

| Domain | Example |
|--------|---------|
| Devices | Face unlock, speech-to-text |
| Security | Spam & fraud detection |
| Commerce | Recommendation ranking |
| Research ops | Auto-tag grant applications by field |
| Manufacturing | Defect detection, predictive maintenance |

**Strengths:** reliable on narrow tasks · fast at scale · clear automation rules (if score > X → human)

**Limits:** only as good as labels · struggles when world changes · cannot draft a policy brief alone

---

## Speaker notes

Pick 2–3 examples from your audience’s sector (patents, funding, universities). Note strengths come from **narrow scope** and **good labeled data**. Limits: new spam tactics, new document formats, concept drift. Recognition errors are often **silent** — wrong bucket, no dramatic prose.

---

# Slide 04 | Generative AI — Definition

## On screen

**Generative AI** (GenAI): learns patterns → **creates new artifacts** resembling training data

**How it works (simple):**

```
1. Train on huge collections (text, images)
2. Learn what usually comes next / what plausibly fits
3. Your prompt → generate token by token / pixel by pixel
```

**Large Language Models (LLMs)** = most visible type (ChatGPT-style)

**Analogy:** Writer or designer — **prompt → generate → human edits**

---

## Speaker notes

Generative AI got the headlines since ~2022–2023. Users *see* the output as text or images. LLMs are one type; image generators are another. Key message: output is **new content**, not a label from a fixed set. Low barrier — ask in normal language — but that ease hides risk (hallucination).

---

# Slide 05 | Generative AI — Examples

## On screen

**Common capabilities**

| Use | Example |
|-----|---------|
| Drafting | Emails, reports, executive summaries |
| Explaining | Plain-language abstracts, FAQs |
| Ideation | Titles, outlines, workshop scenarios |
| Translation | Rewrite for different audiences |
| Assistive code | Formulas, snippets (verify!) |

**Strengths:** flexible on language tasks · strong for **first drafts** and exploration

**Limits:** can **hallucinate** · knowledge may be **outdated** · no built-in policy compliance · **human review** for high-stakes work

---

## Speaker notes

Stress “first draft, not final.” Patent descriptions, funding narratives, press releases — all need human edit. Image generation appears in exhibitions and comms; same verify-before-publish rule. Connect forward: Lecture 04 goes deeper on LLMs; Lecture 08 on trust and verification.

---

# Slide 06 | Side-by-Side Comparison

## On screen

| Dimension | Recognition AI | Generative AI |
|-----------|----------------|---------------|
| **Primary output** | Label, score, rank | New content |
| **User interaction** | Often invisible (background) | Often conversational (prompts) |
| **Best for** | Sorting, detecting, predicting | Drafting, explaining, ideating |
| **Error type** | Wrong classification | Plausible wrong text |
| **Human role** | Set thresholds; edge cases | Edit, verify, approve |
| **Risk profile** | Silent wrong routing | Convincing misinformation |
| **Maturity** | Decades in production | Rapid adoption ~2022–2023 |

---

## Speaker notes

Walk the table row by row. The **error type** row is critical: recognition fails quietly; generative fails persuasively. Transparency: recognition can sometimes explain via features; generative is harder to audit line-by-line. Neither replaces human judgment on high-stakes decisions.

---

# Slide 07 | Research & Innovation Examples

## On screen

**Recognition AI (still essential)**

| Sector | Example |
|--------|---------|
| Patents & IP | Classify by tech field; detect duplicate filings |
| Funding | Score eligibility rules; flag incomplete submissions |
| Libraries | Index papers by topic; recommend related reading |
| Government S&T | Anomaly detection; dashboard alerts |

**Generative AI (assistive)**

| Sector | Example |
|--------|---------|
| Research | Literature summaries; plain-language abstracts |
| Patents | First-draft descriptions (**not** final legal text) |
| Policy | Briefing drafts; scenario narratives |
| Communications | Press releases, exhibition captions (edited by staff) |

---

## Speaker notes

“Legacy” does **not** mean obsolete — recognition still runs invisible infrastructure. Generative adds visible assistive layers. Use facilitator line: “Generative AI got the headlines; recognition AI still does much of the quiet work.” Tie to PublicKey / research ecosystem examples from your org.

---

# Slide 08 | How They Work Together

## On screen

**Mental model**

```
RECOGNITION AI                         GENERATIVE AI
──────────────                         ──────────────
  Input ──► [Pattern Match] ──► Label     Prompt ──► [Create] ──► Draft
  "Is this spam?"  →  Yes/No              "Summarize this" → New text
  "Patent class?"  →  Category            "Draft FAQ"      → New FAQ
```

**Stacked workflow**

```
Documents → Recognition (tag, route, flag)
         → Generative (summarize, draft response)
         → Human (approve, publish, decide)
```

| Step | AI type |
|------|---------|
| Ingest 10,000 archival PDFs | Recognition (OCR, classify, index) |
| Find similar past projects | Recognition / search ranking |
| Explain top 5 matches to director | Generative (plain-language summary) |
| Approve public statement | **Human** |

---

## Speaker notes

Pause on the pipeline diagram — let the room read. Modern systems **stack both**: classify first, generate second, human last. Example walkthrough: 50,000 papers tagged by field (recognition), minister gets 5-bullet summary of top matches (generative), comms approves release (human). This pattern repeats across the curriculum (RAG + LLM + VERIFY).

---

# Slide 09 | When to Use Which

## On screen

**Decision guide**

| If you need to… | Prefer… |
|-----------------|---------|
| Sort items into categories | Recognition AI |
| Detect fraud / anomalies | Recognition AI |
| Predict a number or probability | Recognition AI |
| Rank search results | Recognition AI (often hybrid) |
| Write a first draft | Generative AI |
| Explain complex text simply | Generative AI |
| Brainstorm options | Generative AI |
| Make a final legal or funding decision | **Human** (AI may inform) |

```
Need to judge/sort?  → Recognition
Need to create/explain? → Generative
Need to decide officially? → Human
```

---

## Speaker notes

Quick poll: “Which row fits your team’s top wish-list item?” Redirect T3 decisions (grant awards, patent grants, national budget) to human-only. AI may **inform** but not **decide**. Hybrid search ranking often combines recognition embeddings with generative answers — preview of Lecture 06 RAG.

---

# Slide 10 | Risks & Human-in-the-Loop

## On screen

| Risk | Recognition AI | Generative AI |
|------|----------------|---------------|
| Wrong but silent | **High** (wrong bucket) | Medium |
| Wrong but persuasive | Lower | **High** (hallucination) |
| Privacy misuse | Training on labeled data | **Pasting secrets into prompts** |
| Bias | Skews classification | Skews language & recommendations |
| Over-trust | “System filed it under X” | “AI wrote it so it must be true” |

**Golden rule (both types):**

> High-stakes outputs require **human verification** — especially generative text.

---

## Speaker notes

Neither type is “safe” alone. Recognition errors hide in workflows; generative errors sound authoritative. Privacy: generative tools often mean users paste sensitive text into prompts — different failure mode than classical ML training data. Bias appears in both. Reinforce golden rule; forward to Lectures 08–09 (trust, privacy).

---

# Slide 11 | Activity — Sort the Use Cases

## On screen

**START ACTIVITY** (~10 min)

For each use case, choose: **Recognition · Generative · Both · Human only**

| # | Use case | Your answer |
|---|----------|-------------|
| 1 | Flag incomplete grant form | |
| 2 | Draft workshop invitation | |
| 3 | Approve patent grant | |
| 4 | Tag 50,000 papers by field | |
| 5 | Summarize tagged set for minister | |
| 6 | Set national research budget | |

*(Answers in speaker notes)*

---

## Speaker notes

Run in pairs or table groups. Reveal answers after discussion:
1 → **Recognition** · 2 → **Generative** · 3 → **Human** · 4 → **Recognition** · 5 → **Both** (recognition/search then generative summary) · 6 → **Human**. Debate #5 — “Both” is the teaching moment for stacked workflows. Timebox: 7 min discuss, 3 min debrief.

---

# Slide 12 | Takeaways & Q&A

## On screen

**Five takeaways**

1. **Recognition AI** = classify, score, detect — mostly **behind the scenes**
2. **Generative AI** = create text, images, drafts — mostly **in front of the user**
3. **Legacy ≠ outdated** — recognition remains core infrastructure
4. **Generative output = draft**, not fact — edit and verify
5. **Real workflows combine both + humans**

**Next in series:** [04 — LLM foundations](../04_genai_llm.md)

**Q&A**

---

## Speaker notes

Recap in 60 seconds using the five bullets. Sample Q&A: ChatGPT is **generative** (produces language). “Which is safer?” — context and review matter; neither alone. “Will GenAI replace recognition?” — unlikely; products use **both**. “Do staff need to know the type?” — yes: if it **creates**, verify; if it **sorts**, check thresholds. Thank the room; point to handout takeaways in lecture doc.

---
