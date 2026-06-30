# Elective E04 — Bias, Fairness & Social Impact

*Short elective · 60 minutes · All members · Leadership encouraged*

**Slides:** [slides/E04_bias_fairness.md](slides/E04_bias_fairness.md)

---

## Objectives

1. Define **bias** in data, models, and deployment  
2. Identify **who benefits and who is harmed** in public-sector AI  
3. Apply **mitigation** habits: diverse eval, transparency, appeal paths  

---

## 1. What is bias in AI? (12 min)

| Source | Example |
|--------|---------|
| **Training data** | Underrepresented languages/dialects |
| **Labels** | Historical discrimination in past decisions |
| **Design choices** | English-only corpus |
| **Deployment** | Tool only available to HQ staff |
| **User interaction** | Over-trust by some groups |

**Not always malicious** — often reflects history in data.

---

## 2. Fairness concepts (plain language) (12 min)

| Term | Meaning |
|------|---------|
| **Representation** | Does data include affected groups? |
| **Equity** | Are outcomes reasonable across groups? |
| **Transparency** | Can users see limits and sources? |
| **Recourse** | Can users appeal or get human help? |
| **Proportionality** | Is AI appropriate for this decision? |

**Government context:** Public trust is **infrastructure**.

---

## 3. Generative vs recognition harm (10 min)

| Type | Harm pattern |
|------|--------------|
| **Recognition** | Wrong denial of service (misclassified) |
| **Generative** | Persuasive misinformation; stereotyped wording |
| **RAG** | Wrong policy affects citizens unequally if corpus incomplete |

---

## 4. Mitigation practices (15 min)

| Practice | Owner |
|----------|-------|
| Diverse **golden questions** ([10_evaluation_quality.md](../10_evaluation_quality.md)) | Domain + dev |
| **Multilingual** corpus where required | Policy owners |
| **Human review** for T2 public content | Approvers |
| **No fully automated** T3 decisions | Leadership |
| Publish **limitations** honestly | Comms |
| **Feedback** channel for wrong answers | Support |
| Periodic **bias review** of samples | Steering group |

---

## 5. Case discussion (15 min)

**Scenario A:** Funding FAQ trained only on English circulars — rural applicants use local language.  
**Scenario B:** Patent assistant suggests male pronouns for inventors by default.  
**Scenario C:** Exhibition AI describes cultures using stereotypes.

Groups: harm? mitigation? tier?

---

## 6. Takeaways

1. Bias can enter at **data, model, and process**.  
2. **Public AI** needs transparency + recourse.  
3. **T3 decisions** stay human for fairness and accountability.  
4. Eval sets should reflect **real diverse users**.

---

## Slide outline (12 slides)

1. Title · 2. Bias sources · 3. Fairness terms · 4. Harm types · 5. Mitigation table · 6–8. Cases · 9. Governance · 10. Takeaways
