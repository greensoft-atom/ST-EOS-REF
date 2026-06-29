# Generative AI vs Recognition AI (Legacy Pattern AI)

*For non-technical staff — general purpose, pre-launch-safe*

---

## Two Big Families of AI

Most AI you hear about falls into two groups:

| | **Recognition AI** (legacy / classical) | **Generative AI** (new wave) |
|---|------------------------------------------|------------------------------|
| **Also called** | Discriminative AI, pattern recognition, classification AI | GenAI, creative AI, language/image generators |
| **Core question** | “What *is* this?” / “Which category?” | “What *could* this be?” / “Create something new” |
| **Output** | Label, score, rank, yes/no, prediction | Text, image, code, audio, draft document |
| **Simple analogy** | Security guard checking ID | Writer or designer producing a first draft |
| **Typical action** | **Recognize → decide** | **Prompt → generate → human edits** |

**One sentence:**  
> **Recognition AI classifies and predicts; generative AI produces new content from instructions.**

---

## Recognition AI — Concept

**Recognition AI** learns patterns in data and **maps input → output** (category, score, or decision).

It does **not** usually write a new report; it **judges** or **sorts** what it sees.

### How it works (no math)
1. Show many labeled examples (“spam / not spam”, “cat / dog”, “fraud / normal”)  
2. System learns distinguishing patterns  
3. On new input, it **recognizes** which bucket it fits  

### Common capabilities
- Image recognition (face unlock, defect detection)  
- Spam and fraud detection  
- Speech-to-text (recognize words)  
- Recommendation ranking (“people like you watched…”)  
- Medical scan screening (flag suspicious regions)  
- Sentiment classification (positive / negative review)  
- Predictive analytics (likely churn, likely delay)  

### Strengths
- Often **reliable** when task is narrow and data is good  
- Fast, cheap at scale once deployed  
- Good for **automation with clear rules** (if score > X, route to human)  

### Limits
- Only as good as training labels and data  
- Struggles when world changes (new spam tactics, new document formats)  
- Does not **explain in prose** unless paired with another system  
- Cannot draft a policy brief by itself  

---

## Generative AI — Concept

**Generative AI** learns patterns in data and **creates new artifacts** that resemble the training distribution: sentences, summaries, images, code snippets.

### How it works (simple)
1. Train on huge collections of examples (text, images/ images)  
2. Learn **what usually comes next** or **what plausibly fits**  
3. Given your **prompt** (instruction), it **generates** output token by token / pixel by pixel  

Large **Language Models (LLMs)** are the most visible type: ChatGPT-style assistants.

### Common capabilities
- Draft emails, reports, summaries  
- Answer questions in natural language  
- Translate and rewrite for different audiences  
- Brainstorm titles, outlines, FAQs  
- Generate images from descriptions  
- Assist with code or spreadsheet formulas  
- Role-play scenarios for training materials  

### Strengths
- Flexible across many **language-heavy** tasks  
- Low barrier: you ask in normal language  
- Strong for **first drafts** and **exploration**  

### Limits
- Can **hallucinate** (confident but wrong)  
- Knowledge may be **outdated**  
- No built-in guarantee of truth or policy compliance  
- Needs **human review** for high-stakes work  

---

## Side-by-Side Comparison

| Dimension | Recognition AI | Generative AI |
|-----------|----------------|---------------|
| **Primary output** | Label, score, rank | New content |
| **User interaction** | Often invisible (runs in background) | Often conversational (prompts) |
| **Best for** | Sorting, filtering, detecting, predicting | Drafting, explaining, summarizing, ideating |
| **Error type** | Wrong classification | Plausible wrong text |
| **Transparency** | Sometimes explainable via features | Harder to audit line-by-line |
| **Data hunger** | Depends; can work well with structured labels | Usually needs very large training corpora |
| **Human role** | Set thresholds; handle edge cases | Edit, verify, approve |
| **Risk profile** | Silent wrong routing | Convincing misinformation |
| **Maturity** | Decades in production (email, banking, phones) | Rapid adoption since ~2022–2023 |
| **Example in research context** | Auto-tag grant applications by field | Summarize a 40-page annex into bullet points |

---

## Visual Mental Model

```
RECOGNITION AI                         GENERATIVE AI
──────────────                         ──────────────
  Input ──► [Pattern Match] ──► Label     Prompt ──► [Create] ──► Draft
  "Is this spam?"  →  Yes/No              "Summarize this" → New text
  "Patent class?"  →  Category            "Draft FAQ"      → New FAQ
```

**Together:**

```
Documents → Recognition AI (tag, route, flag)
         → Generative AI (summarize, draft response)
         → Human (approve, publish, decide)
```

---

## Applications by Type (General — Research & PublicKey Ecosystem)

### Recognition AI applications (legacy, still essential)

| Sector | Example use |
|--------|-------------|
| **Patents & IP** | Classify documents by technology field; detect duplicate filings |
| **Research libraries** | Index papers by topic; recommend related reading |
| **Funding agencies** | Score eligibility rules; flag incomplete submissions |
| **Universities** | Detect plagiarism patterns; route student inquiries |
| **Government S&T** | Anomaly detection in reporting data; dashboard alerts |
| **Exhibitions** | Image tagging; crowd flow / security analytics |
| **Industry R&D** | Quality inspection; predictive maintenance alerts |

### Generative AI applications (assistive)

| Sector | Example use |
|--------|-------------|
| **Research** | Literature summaries; plain-language abstracts |
| **Patents** | First-draft descriptions for attorney review (**not** final legal text) |
| **Funding** | Checklist feedback; rewrite executive summary for clarity |
| **Policy** | Briefing drafts; scenario narratives for discussion |
| **Universities** | FAQ answers; workshop materials |
| **Innovation / startups** | Pitch deck outlines; competitor landscape summaries |
| **Communications** | Press releases, exhibition captions (edited by staff) |

### Best practice: combine both

| Workflow step | AI type |
|---------------|---------|
| Ingest 10,000 archival PDFs | Recognition (OCR, classification, indexing) |
| Find “similar past projects” | Recognition / search ranking |
| Explain top 5 matches to a director | Generative (summary in plain language) |
| Approve public statement | **Human** |

---

## Legacy vs “New” — Important Nuance for Your Audience

**“Legacy” does not mean obsolete.**

- Recognition AI still runs **most invisible infrastructure** (security, spam, recommendations).  
- Generative AI is **new visibility** — people *see* the output as text/images.  
- Modern systems often **stack both**: classify first, generate second, human last.

**Facilitator line:**  
> “Generative AI got the headlines; recognition AI still does much of the quiet work.”

---

## When to Use Which (Decision Guide)

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

---

## Risks Compared

| Risk | Recognition AI | Generative AI |
|------|----------------|---------------|
| Wrong but silent | High (wrong bucket) | Medium |
| Wrong but persuasive | Lower | **High (hallucination)** |
| Privacy misuse | Training on labeled data | **Pasting secrets into prompts** |
| Bias | Skews classification | Skews language and recommendations |
| Over-trust | “The system filed it under X” | “The AI wrote it so it must be true” |

**Golden rule (both types):**  
> High-stakes outputs require **human verification** — especially generative text.

---

## 45-Minute Lecture Structure (Suggested)

### Part 1 — Concepts (15 min)
- Define recognition vs generative  
- Analogies: ID check vs writer  
- Neither is AGI; both are **narrow** in different ways  

### Part 2 — Comparison & examples (15 min)
- Side-by-side table  
- 3 recognition examples + 3 generative examples from research/innovation life  
- “Stacked workflow” diagram  

### Part 3 — Activity (10 min)
**Sort the use case:** Recognition, Generative, Both, or Human only?

1. Flag incomplete grant form → **Recognition**  
2. Draft workshop invitation → **Generative**  
3. Approve patent grant → **Human**  
4. Tag 50,000 papers by field → **Recognition**  
5. Summarize tagged set for minister → **Generative** (after recognition/search)  
6. Set national research budget → **Human**  

### Part 4 — Q&A (5 min)

---

## Slide Outline (12 slides)

1. Title: Two families of AI  
2. Recognition AI — definition & analogy  
3. Recognition — examples in daily life  
4. Generative AI — definition & analogy  
5. Generative — examples in daily life  
6. Comparison table (outputs, errors, roles)  
7. Research & innovation examples (both columns)  
8. How they work together (pipeline)  
9. When to use which — decision guide  
10. Risks & human-in-the-loop  
11. Activity: sort the use cases  
12. Takeaways & Q&A  

---

## Takeaways (Handout)

1. **Recognition AI** = classify, score, detect, predict — mostly **behind the scenes**.  
2. **Generative AI** = create text, images, drafts — mostly **in front of the user**.  
3. **Legacy ≠ outdated** — recognition AI remains core infrastructure.  
4. **Generative AI needs editing** — treat output as draft, not fact.  
5. **Real workflows combine both + humans** — especially in research, patents, and public sector.  

---

## Sample Q&A

**Q: Is ChatGPT recognition or generative?**  
A: Generative (it produces language). It may use recognition-like steps internally, but the user-facing job is generation.

**Q: Which is safer?**  
A: Neither is “safe” alone. Recognition errors can hide; generative errors can sound authoritative. Context and review matter.

**Q: Will generative AI replace recognition AI?**  
A: Unlikely. Different jobs. More Info products use **both**.

**Q: Do staff need to know which type they’re using?**  
A: Helpful. If it **creates** text, verify. If it **sorts** or **scores**, check thresholds and edge cases.

---

## Where This Fits Your Series

| Session | Topic |
|---------|--------|
| Lecture 2 | How AI learns — preview both types |
| **03 (this)** | **Generative vs Recognition — introductory** |
| [04_genai_llm.md](04_genai_llm.md) | LLM foundations — training, inference, tokens, roles |
| [05_local_llm.md](05_local_llm.md) | Local LLM — deployment and operations |
| [06_rag.md](06_rag.md) | RAG — documents, retrieval, citations |
| Later sessions | Prompting, trust, privacy — see [01_concept.md](01_concept.md) |