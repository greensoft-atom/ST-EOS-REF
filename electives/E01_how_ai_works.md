# Elective E01 — How AI Works (No Math, No Code)

*Short elective · 45–60 minutes · All members*  
*Data, patterns, training — complements [04_genai_llm.md](../04_genai_llm.md) at intro depth*

**Slides:** [slides/E01_how_ai_works.md](slides/E01_how_ai_works.md)

---

## Objectives

1. Explain AI as **patterns from data** — not magic  
2. Contrast **training** vs **using** a model  
3. Relate to Recognition vs Generative ([03_gai_rai.md](../03_gai_rai.md))

---

## 1. Opening (5 min)

> “AI learns from **examples**, like learning to spot spam after seeing thousands of labeled emails — no one programs every rule.”

---

## 2. The learning loop (12 min)

```
Examples in  →  Find patterns  →  Model  →  Guess on new input
(labeled)        (training)      (saved)     (inference)
```

| Term | Plain meaning |
|------|----------------|
| **Training data** | Past examples |
| **Model** | Saved pattern snapshot |
| **Inference** | Using it on new cases |

**Analogy:** Study past exams (training) → take new exam (inference). Different exam each time for generative AI.

---

## 3. Recognition vs generative (10 min)

| | Recognition | Generative |
|---|-------------|------------|
| Question | What category? | What text/image next? |
| Output | Label/score | New content |
| Example | Spam filter | Draft email |

See [03_gai_rai.md](../03_gai_rai.md) for full module.

---

## 4. What data does (10 min)

| Good data | Bad data |
|-----------|----------|
| Representative | Biased sampling |
| Labeled clearly | Wrong labels |
| Updated | Stale |

**Org angle:** RAG corpus = **your** data at inference ([06_rag.md](../06_rag.md)).

---

## 5. Activity — human pattern matching (10 min)

Show 10 short grant titles → “fundable / not fundable” labels. Group classifies 5 new titles. Discuss edge cases — same for AI.

---

## 6. Limits (5 min)

- Needs lots of examples (or good retrieval)  
- Changes when world changes  
- Not understanding like a person ([02_concept_agi.md](../02_concept_agi.md))

---

## 7. Takeaways

1. AI = patterns from data.  
2. Training once, use many times.  
3. Recognition sorts; generative creates.  
4. **Deep dive:** [04_genai_llm.md](../04_genai_llm.md).

---

## Slide outline (10 slides)

1. Title · 2. Learning loop · 3. Training vs inference · 4. Recognition vs generative · 5. Good/bad data · 6. RAG preview · 7. Activity · 8. Limits · 9. Takeaways · 10. Next steps
