# Slide Deck — How AI Works (No Math, No Code)

*Source: [E01_how_ai_works.md](../E01_how_ai_works.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Elective E01 — How AI Works**

*No math, no code · 45–60 minutes · All members*

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Explain AI as **patterns from data** — not magic |
| 2 | Contrast **training** vs **using** a model |
| 3 | Relate **Recognition AI** vs **Generative AI** |

*Complements [04_genai_llm.md](../../04_genai_llm.md) at intro depth*

## Speaker notes

Welcome to E01. This is the gentlest technical elective — no formulas, no code. Frame AI as pattern learning from examples, like spotting spam after seeing thousands of labeled emails. Poll: "Who thinks AI is magic?" — we'll demystify in one hour. Point to Lecture 04 for the deep dive later.

---

# Slide 02 | The Learning Loop

## On screen

**How AI learns — the core loop**

```
Examples in  →  Find patterns  →  Model  →  Guess on new input
(labeled)        (training)      (saved)     (inference)
```

| Step | Plain meaning |
|------|----------------|
| **Examples in** | Past cases with known answers |
| **Find patterns** | System discovers regularities |
| **Model** | Saved snapshot of those patterns |
| **Guess on new input** | Apply patterns to something new |

> AI learns from **examples**, not from someone programming every rule.

## Speaker notes

Walk through the diagram left to right. Emphasize that nobody writes "if subject contains FREE then spam" for every variant — the system learns from labeled history. Use the opening quote: thousands of labeled emails teach the pattern. This loop is the foundation for everything else in the curriculum.

---

# Slide 03 | Training vs Inference

## On screen

**Two phases — learn once, use many times**

| Term | Plain meaning | When |
|------|---------------|------|
| **Training data** | Past examples used to learn | Before deployment |
| **Training** | Finding patterns in that data | Usually offline |
| **Model** | Saved pattern snapshot | After training |
| **Inference** | Using the model on new cases | Every time you use the tool |

**Analogy:** Study past exams (training) → take a **new** exam (inference).

Generative AI: different "exam question" each time — it **creates** output, not just a label.

## Speaker notes

The exam analogy lands well with research and admin audiences. Stress that training is expensive and periodic; inference is what users experience daily. For generative tools, inference produces new text or images each run — not the same fixed label. Ask: "When did your spam filter last retrain?" — probably unknown, and that's normal.

---

# Slide 04 | Recognition vs Generative

## On screen

**Two families of AI — different questions**

| | **Recognition AI** | **Generative AI** |
|---|---------------------|-------------------|
| **Question** | What category? What score? | What text/image comes next? |
| **Output** | Label, rank, score | New content (draft, image) |
| **Example** | Spam filter | Draft email |
| **You see** | Outcome (inbox sorted) | Prose or pixels to read |

See full module: [03_gai_rai.md](../../03_gai_rai.md)

## Speaker notes

This table mirrors Lecture 03 at elective depth. Recognition sorts and judges; generative produces. Most daily AI is recognition — invisible. Generative is the newer visible layer. Don't dive into LLM architecture here; just name the families so E02 and the core modules connect.

---

# Slide 05 | Good Data vs Bad Data

## On screen

**AI quality starts with data**

| **Good data** | **Bad data** |
|---------------|--------------|
| Representative of real use | Biased sampling (some groups missing) |
| Labeled clearly and consistently | Wrong or inconsistent labels |
| Updated when the world changes | Stale — rules shifted, data didn't |

**Why it matters:** A model is a **snapshot of patterns in whatever you fed it.**

Garbage in → unreliable patterns out — no magic fix at inference time.

## Speaker notes

Connect to public-sector stakes: if training data only reflects HQ decisions, rural or minority cases may fail silently. Stale data is common in government — circulars change, corpus doesn't. This sets up E04 on bias without using that word yet. Keep it practical: "What would bad labels look like in your domain?"

---

# Slide 06 | RAG Preview — Your Data at Inference

## On screen

**Org angle: retrieval augments the model**

| Concept | Plain meaning |
|---------|---------------|
| **Training** | Learns general language/patterns once |
| **RAG corpus** | **Your** documents indexed for search |
| **At inference** | Model reads retrieved chunks + generates answer |

> RAG = bring **your** approved sources into the answer — not only what the model memorized in training.

Deep dive: [06_rag.md](../../06_rag.md)

## Speaker notes

Preview only — full RAG is Lecture 06. Key message: org assistants combine a general model with your circulars, policies, and archives at answer time. That means corpus quality and freshness matter as much as model choice. If someone asks "can we just train on everything?" — note training is slow and expensive; RAG updates faster for document-heavy work.

---

# Slide 07 | Activity — Human Pattern Matching

## On screen

**START ACTIVITY — ~10 minutes**

**Setup:** Facilitator shows 10 short grant titles labeled **fundable / not fundable**.

**Task:** Groups classify **5 new titles** using the patterns they observed.

**Discuss:**

| Edge case | AI parallel |
|-----------|-------------|
| Title sounds fundable but wrong program | Model confident, wrong bucket |
| Ambiguous wording | Low confidence or wrong guess |
| New program type never in training | Failure mode |

*Full timing in lecture doc.*

## Speaker notes

Run this before the limits slide — participants feel why edge cases matter. Debrief: humans also disagree on borderline cases; AI does the same at scale. Don't grade groups harshly; focus on "what rule did you infer?" vs "what rule was actually applied?" That mirrors how models generalize from examples.

---

# Slide 08 | Limits of Pattern-Based AI

## On screen

**What AI is not**

| Limit | Plain explanation |
|-------|-------------------|
| **Needs examples** | Weak or zero-shot without good data or retrieval |
| **World changes** | Patterns from 2019 may fail in 2026 |
| **Not human understanding** | Statistical patterns ≠ reasoning like a person |

See AGI contrast: [02_concept_agi.md](../../02_concept_agi.md)

> Useful tool — not a colleague who **knows** your mission the way you do.

## Speaker notes

Keep tone balanced — not anti-AI, pro-realism. Tie activity edge cases to these limits. Reference Lecture 02 lightly: today's tools are narrow pattern engines, not AGI. For government work, this means verification and human gates stay mandatory for anything high-stakes.

---

# Slide 09 | Takeaways

## On screen

**Remember**

| # | Takeaway |
|---|----------|
| 1 | AI = **patterns from data**, not magic |
| 2 | **Train once**, use many times (inference) |
| 3 | **Recognition** sorts; **generative** creates |
| 4 | Data quality and freshness drive outcomes |

**One sentence:** AI learns from examples, saves a model, and guesses on new input — you still own judgment on what matters.

## Speaker notes

Recap in 60 seconds using the table. Ask each person to share one takeaway with a neighbor. Reinforce that this elective gives vocabulary for the core track — they are now ready for E02 (daily life) or Lecture 04 (LLMs) without fear of math.

---

# Slide 10 | Next Steps

## On screen

**Where to go next**

| If you want… | Open… |
|--------------|-------|
| Two AI families in depth | [03_gai_rai.md](../../03_gai_rai.md) |
| How LLMs work | [04_genai_llm.md](../../04_genai_llm.md) |
| Your documents in answers | [06_rag.md](../../06_rag.md) |
| AI you already use daily | [E02_ai_daily_life.md](../E02_ai_daily_life.md) |

**Questions?** Keep the learning loop diagram in mind — it applies to every tool in the curriculum.

## Speaker notes

Close with optional paths — no single mandatory next step. Electives are modular. If time remains, take one audience-specific question (e.g., "does this apply to our grant system?") and map it to training data + inference + human verify. Thank the group and point to the slide file for self-study.

---
