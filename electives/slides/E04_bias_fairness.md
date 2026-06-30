# Slide Deck — Bias, Fairness & Social Impact

*Source: [E04_bias_fairness.md](../E04_bias_fairness.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Elective E04 — Bias, Fairness & Social Impact**

*60 minutes · All members · Leadership encouraged*

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Define **bias** in data, models, and deployment |
| 2 | Identify **who benefits and who is harmed** in public-sector AI |
| 3 | Apply **mitigation** habits: diverse eval, transparency, appeal paths |

> Public trust is **infrastructure** — not a nice-to-have.

## Speaker notes

Leadership presence matters — fairness is governance, not only ethics theater. Set tone: bias is often historical data reflection, not mustache-twirling intent. Goal is practical habits for org AI, not academic debate. Link to E03 stakes and Lecture 08 tiers throughout.

---

# Slide 02 | What Is Bias in AI?

## On screen

**Bias can enter at every stage**

| Source | Example |
|--------|---------|
| **Training data** | Underrepresented languages or dialects |
| **Labels** | Historical discrimination in past decisions |
| **Design choices** | English-only corpus by default |
| **Deployment** | Tool only available to HQ staff |
| **User interaction** | Some groups over-trust fluent outputs |

> **Not always malicious** — often reflects history embedded in data.

**Key question:** Who was included when the system learned — and who wasn't?

## Speaker notes

Walk the table slowly. Emphasize deployment bias — tool exists but wrong users can't access it. User interaction bias: fluent prose feels authoritative to some audiences more than others. Avoid blame; focus on detection and mitigation. Ask room for one example from their domain without naming individuals.

---

# Slide 03 | Fairness Concepts — Plain Language

## On screen

**Vocabulary for public-sector AI**

| Term | Meaning | Ask |
|------|---------|-----|
| **Representation** | Does data include affected groups? | Who's missing? |
| **Equity** | Are outcomes reasonable across groups? | Same error rate? Same access? |
| **Transparency** | Can users see limits and sources? | Citations? Disclaimers? |
| **Recourse** | Can users appeal or get human help? | Phone number? Escalation? |
| **Proportionality** | Is AI appropriate for this decision? | Should a human decide instead? |

**Government context:** Citizens judge the **institution**, not the vendor model.

## Speaker notes

Plain language only — no statistical fairness formulas. Recourse and transparency are where public orgs differ from consumer apps. Proportionality links to T3: some decisions shouldn't use AI at all. Poll: which term is weakest in your current digital services?

---

# Slide 04 | Harm Patterns by AI Type

## On screen

**Recognition vs generative vs RAG — different harms**

| Type | Harm pattern | Example |
|------|--------------|---------|
| **Recognition** | Wrong denial of service | Misclassified application |
| **Generative** | Persuasive misinformation | Wrong deadline stated confidently |
| **Generative** | Stereotyped wording | Default pronouns, cultural clichés |
| **RAG** | Wrong policy applied unequally | Corpus missing regional circular |

| Common thread | Mitigation direction |
|---------------|---------------------|
| Silent failure | Sample monitoring + feedback channel |
| Fluent wrong | VERIFY + approval (T2) |
| Incomplete corpus | Multilingual, regional owners |

## Speaker notes

Recognition harm is often invisible — wrong bucket, no dramatic text. Generative harm can be loud and persuasive. RAG harm is structural: if corpus is HQ-only English, assistants fail rural or minority citizens systematically. Sets up case discussion slides.

---

# Slide 05 | Mitigation Practices

## On screen

**What good orgs do — with owners**

| Practice | Owner |
|----------|-------|
| Diverse **golden questions** for eval | Domain + dev |
| **Multilingual** corpus where required | Policy owners |
| **Human review** for T2 public content | Approvers |
| **No fully automated** T3 decisions | Leadership |
| Publish **limitations** honestly | Comms |
| **Feedback** channel for wrong answers | Support |
| Periodic **bias review** of samples | Steering group |

Refs: [10_evaluation_quality.md](../../10_evaluation_quality.md) · [08_trust_verification.md](../../08_trust_verification.md)

## Speaker notes

This is the actionable core — assign owners in your org mentally as you read. Golden questions must reflect real diverse users, not only HQ testers. Leadership row on T3 is non-negotiable for public trust. Honest limitations build more trust than marketing superlatives.

---

# Slide 06 | Case Discussion A — Language & Access

## On screen

**Scenario A**

> Funding FAQ trained **only on English circulars**. Rural applicants rely on **local language** and regional guidance not in the corpus.

**Discuss in groups:**

| Question | Prompt |
|----------|--------|
| **Harm?** | Who fails to get accurate info? |
| **Mitigation?** | Corpus, translation, human desk |
| **Tier?** | Public FAQ = T2 — what changes? |

**Hints:** Representation + RAG completeness — not only model choice.

## Speaker notes

10–12 minutes if combined with B and C; or 5 min on A alone. Expected harm: systematic exclusion of non-English users and wrong advice. Mitigation: multilingual corpus, human helpline, clear "available languages" notice. Tier T2 with approvers who understand regional policy.

---

# Slide 07 | Case Discussion B — Default Language

## On screen

**Scenario B**

> Patent assistant **suggests male pronouns for inventors by default**, even when inventors' names and bios indicate otherwise.

**Discuss:**

| Lens | Consider |
|------|----------|
| **Source** | Training data + default templates |
| **Harm** | Erasure, unprofessional filings |
| **Mitigation** | Eval set with diverse names; style guide; human edit |
| **Tier** | Draft assist — still verify before file |

## Speaker notes

Smaller harm than wrongful denial but damages trust and professionalism. Connect to generative stereotype harm from slide 4. Mitigation includes product eval, prompt/style rules, and human review before external submission. Not solved by "try harder" prompting alone — needs eval loop.

---

# Slide 08 | Case Discussion C — Cultural Representation

## On screen

**Scenario C**

> Exhibition AI **describes cultures using stereotypes** in auto-generated captions for a public science museum.

**Discuss:**

| Lens | Consider |
|------|----------|
| **Harm** | Offense, mis-education, reputational damage |
| **Mitigation** | Comms + cultural reviewers; block auto-publish |
| **Tier** | T2 — **approve before floor / web** |
| **Process** | Feedback from visitors and communities |

## Speaker notes

Highest emotional salience — facilitate respectfully. Auto-publish is the failure mode; T2 gate and diverse reviewers are the fix. Link to corpus of approved interpretive content vs open-ended generation. Museum/comms staff should lead, not IT alone.

---

# Slide 09 | Governance & Accountability

## On screen

**Fairness is ongoing governance**

| Element | What it looks like |
|---------|-------------------|
| **Steering group** | Periodic sample review across sectors |
| **Published limits** | "Covers English circulars as of DATE" |
| **Appeals path** | Human can override AI-assisted routing |
| **Incident learning** | Wrong answer → corpus fix, not blame user |
| **T3 boundary** | Leadership sign-off on no-automation list |

| Aligns with | Module |
|-------------|--------|
| Risk tiers | [08_trust_verification.md](../../08_trust_verification.md) |
| Eval quality | [10_evaluation_quality.md](../../10_evaluation_quality.md) |
| Privacy & data | [09_privacy_security.md](../../09_privacy_security.md) |

## Speaker notes

Governance slide for leadership — fairness isn't a one-time bias audit. Incident learning culture matters: Lecture 08 warns against punishing staff for catching AI errors. Name who in your org would sit on a steering group if it exists or is planned.

---

# Slide 10 | Public Trust Checklist

## On screen

**Before launching citizen-facing AI — ask:**

| ✓ | Question |
|---|----------|
| ☐ | Eval questions include **affected communities** |
| ☐ | Corpus covers **languages and regions** served |
| ☐ | **Limitations** published where users see the tool |
| ☐ | **Human recourse** visible (not buried in PDF) |
| ☐ | **T3 decisions** excluded from automation |
| ☐ | **Feedback** loop to corpus owners |

> Trust lost in one viral wrong answer takes years to rebuild.

## Speaker notes

Quick operational checklist — can be copied to runbooks. Customize checkmarks for your launch process. If launching nothing soon, use as readiness self-assessment. Tie back to transparency and recourse vocabulary from slide 3.

---

# Slide 11 | Takeaways

## On screen

**Remember**

| # | Takeaway |
|---|----------|
| 1 | Bias enters at **data, model, and process** |
| 2 | Public AI needs **transparency + recourse** |
| 3 | **T3 decisions** stay human — fairness and accountability |
| 4 | Eval sets must reflect **real diverse users** |

**One line:** Fair public AI is representative data, honest limits, and humans accountable for what ships.

## Speaker notes

Summarize cases in one sentence each. Ask leadership one commitment they can make (e.g., multilingual corpus priority, steering group meeting). Avoid empty pledges — one concrete next step is enough.

---

# Slide 12 | Next Steps

## On screen

**Where to go next**

| Module / elective | Topic |
|-------------------|-------|
| [10_evaluation_quality.md](../../10_evaluation_quality.md) | Golden questions & eval |
| [08_trust_verification.md](../../08_trust_verification.md) | Tiers & VERIFY |
| [E03_research_public_sector.md](../E03_research_public_sector.md) | Sector stakes |
| [workshops/corpus_policy.md](../../workshops/corpus_policy.md) | Policy corpus workshop |

**Action:** Add **one** fairness check to your next AI pilot charter.

## Speaker notes

Close with single action item for sponsors in the room. Point to evaluation module for building golden questions. Thank group — fairness conversations are hard; completing them is leadership work.

---
