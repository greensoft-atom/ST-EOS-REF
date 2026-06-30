# Slide Deck — Corpus Design for Policy & Government Knowledge

*Source: workshops/corpus_policy.md*

---

# Slide 01 | Workshop Overview

## On screen

**Workshop — Corpus Design for Policy & Government Knowledge**

*For policy officers, legal liaisons, comms, dev/RAG owners*

| Item | Detail |
|------|--------|
| **Duration** | 3 hours |
| **Audience** | Policy domain + technical + legal consult |
| **Prerequisites** | 06_rag · 08_trust_verification · 09_privacy_security |
| **Outcome** | Policy corpus spec + T2 publish workflow |

---

## Speaker notes

Government policy RAG errors become compliance incidents and public trust failures. Legal consult should be in the room or on call. Outcome is a corpus spec with LEGAL vs GUIDANCE separation and a named T2 approver for citizen-facing content.

---

# Slide 02 | Learning Objectives

## On screen

**By the end of this workshop, teams will:**

1. Distinguish **normative** (official) vs **explanatory** (guidance) documents
2. Preserve **article numbering** and defined terms in chunks
3. Design **plain-language** layers without replacing legal text
4. Plan **minister / public** T2 approval workflow
5. Handle **multilingual** policy corpora

| Normative | Explanatory |
|-----------|-------------|
| Enacted law, regs | FAQs, training, plain language |
| Authoritative | Must cite back to LEGAL |

---

## Speaker notes

Objectives encode the dual-corpus model. Plain language is allowed; replacing legal text is not. T2 is for public publish — connect to 08_trust_verification. Multilingual means parallel official texts, not machine-translated law in LEGAL corpus.

---

# Slide 03 | Stakes in Policy RAG

## On screen

**When the corpus is wrong**

| Error type | Impact |
|------------|--------|
| Wrong effective date | Non-compliance |
| Softening "must" to "may" | Legal misrepresentation |
| Mixing draft bill with enacted law | False rights/duties |
| Citizen PII in index | Privacy incident |

> Public bot answers **legal facts** from LEGAL corpus only.  
> Guidance helps readability — it cannot contradict.

---

## Speaker notes

15 min. "Must" → "may" is often a chunk boundary or summarization bug, not malice — still liability. Draft bill in citizen index is a common accident when folders are shared — exclude until enacted. PII in chat logs and index both need DPO review (09_privacy_security).

---

# Slide 04 | Dual-Corpus Model

## On screen

```
┌──────────────────────────────────────┐
│ CORPUS LEGAL — enacted law, regs,    │
│ official circulars (authoritative)   │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│ CORPUS GUIDANCE — plain language,    │
│ FAQs, training slides (explanatory)  │
│ must cite back to LEGAL corpus       │
└──────────────────────────────────────┘
```

| Rule | Detail |
|------|--------|
| Legal facts | Retrieve from LEGAL |
| Plain summary | GUIDANCE — with `legal_ref` link |
| Conflict | LEGAL wins; fix or remove GUIDANCE chunk |

---

## Speaker notes

20 min. Recommended architecture for government assistants. GUIDANCE without `legal_ref` is orphan text — do not index. If GUIDANCE contradicts LEGAL, GUIDANCE is wrong — fix before publish. Ask teams to name one doc for each corpus from their domain.

---

# Slide 05 | Document Inclusion Matrix

## On screen

| Document | Corpus | Notes |
|----------|--------|-------|
| Enacted regulation | LEGAL | Version + gazette date |
| Official circular | LEGAL | `effective_from/to` |
| Department FAQ (approved) | GUIDANCE | Link `legal_ref` field |
| Draft bill under consultation | **Exclude** until enacted | |
| Minister speech draft | **Exclude** from citizen bot | |
| Internal interdepartmental memo | Separate ACL — not public | |
| Multilingual official translation | LEGAL | `language` metadata |

---

## Speaker notes

Inclusion matrix prevents scope creep. Draft bill and speech drafts are high-risk if accidentally ingested — use separate staging buckets. Internal memos need ACL like funding internal docs. Official translation only in LEGAL — not AI-translated law text.

---

# Slide 06 | Chunking & Legal Structure

## On screen

**Each chunk should carry legal anchors**

```
article: §12.3
paragraph: (2)
defined_terms: ["Applicant", "Eligible entity"]
legal_ref: REG-2024-SCIENCE-TECH-12
```

| Do | Don't |
|----|-------|
| Split at article / subsection | Split mid-paragraph |
| Keep "Article 5 — Definitions" intact | Merge unrelated articles |
| Attach footnotes to parent chunk | Orphan footnotes |

---

## Speaker notes

25 min. Metadata mirrors how lawyers cite — enables must_cite eval (POL-01, POL-02). Definitions chunks power "who is Eligible entity?" queries. Orphan footnotes cause wrong qualifications in answers. Article-level chunking is default for LEGAL.

---

# Slide 07 | Example Legal Chunk

## On screen

```
[LEGAL-REG-2024-§7.2]
§7.2 Submission deadline. Applications must be submitted
by 17:00 local time on the date specified in the annual
call notice. Late submissions shall not be accepted.

Defined terms: Application, Call notice
Effective: 2024-03-01
```

**Eval hook:** POL-01 must cite §7.2 including exact time (17:00).

---

## Speaker notes

Walk through example — note "must" language preserved verbatim. Defined terms listed for retrieval on definition queries. Effective date on chunk supports supersession checks (POL-03). This is the quality bar for LEGAL indexing — not paraphrased summaries.

---

# Slide 08 | Plain-Language Workflow (T2)

## On screen

```
LEGAL chunk retrieved
        │
        ▼
AI drafts plain-language summary (GUIDANCE style)
        │
        ▼
Policy officer VERIFY + approver sign-off
        │
        ▼
Publish to citizen portal (optional separate GUIDANCE index)
```

| Forbidden | Required |
|-----------|----------|
| AI-only plain text with no legal link | Both plain summary **and** link to official § |
| Auto-publish without approver | T2 gate (08_trust_verification) |

---

## Speaker notes

20 min. Two-step pattern is mandatory for citizen-facing plain language. Officer VERIFY catches must/may errors. Approver sign-off is T2 — name the role in activity spec. Separate GUIDANCE index optional but `legal_ref` always required.

---

# Slide 09 | Multilingual Policy

## On screen

| Approach | Detail |
|----------|--------|
| Parallel docs per language | `language: ko`, `language: en`, same `legal_ref` |
| Single embed model | Multilingual embedding (06_rag) |
| Query language | Retrieve matching language + fallback rule |
| Translation | Official translation only — **not** AI-translated law for LEGAL |

```
REG-2024-§7.2 (ko)  ──┐
                      ├── same legal_ref
REG-2024-§7.2 (en)  ──┘
```

---

## Speaker notes

15 min. Parallel official texts share `legal_ref` for cross-language citation. Fallback rule must be written (e.g. query ko → ko chunks first, en if missing flag). AI-translated law in LEGAL corpus is disallowed — translation errors become law. GUIDANCE plain language may be localized under T2 separately.

---

# Slide 10 | Golden Questions

## On screen

| id | query | must_cite | note |
|----|-------|-----------|------|
| POL-01 | Deadline for submissions §7.2 | REG §7.2 | exact time |
| POL-02 | Who is "Eligible entity"? | Definitions §2 | |
| POL-03 | 2023 rule still apply? | refusal if superseded | `effective_to` |
| POL-04 | Plain summary of §7.2 | LEGAL + GUIDANCE | T2 |
| POL-05 | Draft bill leak query | refuse | not in corpus |

**Include supersession + refusal in every policy golden set.**

---

## Speaker notes

POL-03 tests lifecycle — assistant must refuse or cite current rule only. POL-04 tests dual-corpus + T2 path. POL-05 ensures draft content is not hallucinated when excluded. Teams add 10+ domain questions in activity; production target 20+.

---

# Slide 11 | Citizen vs Internal

## On screen

| Feature | Citizen portal | Internal officer |
|---------|----------------|------------------|
| Corpus | LEGAL public + approved GUIDANCE | + internal procedures |
| ACL | `citizen`, `anonymous` blocked from internal | `policy_officer` |
| Tier | T2 for published answers | T1 internal drafts |
| PII | Never collect in chat | Escalation forms |

```
Citizen query → public ACL → LEGAL + approved GUIDANCE only
Internal query → policy_officer ACL → + internal procedures
```

---

## Speaker notes

15 min. Citizen bot gets minimal corpus — strict ACL. Internal drafts stay T1 — not for portal publish. PII never in citizen chat; use forms with proper handling. Test ACL with citizen vs officer personas (checklist). Wrong public answer incident path → 12_serving_customers.

---

# Slide 12 | ACTIVITY — Policy Corpus Spec (45 min)

## On screen

**START ACTIVITY — 45 minutes**

**Extend funding workshop template with:**

| Section | Complete? |
|---------|-----------|
| LEGAL vs GUIDANCE document lists | ☐ |
| Article-level chunking rules | ☐ |
| `legal_ref` linking schema | ☐ |
| T2 approver role name | ☐ |
| Multilingual plan | ☐ |
| Golden questions (min 10; include superseded-law case) | ☐ |
| Sign-off: Policy · Legal · Comms · Technical · Security | ☐ |

**Facilitator:** at 25 min — every team has named T2 approver and one POL-03-style golden question.

---

## Speaker notes

45 min activity. Template base in workshops/corpus_funding.md §7; policy extensions in corpus_policy.md §8. T2 approver must be a named role, not "the minister maybe." Superseded-law golden question is mandatory. Comms sign-off for public wording. Legal reviews LEGAL corpus scope before handoff.

---

# Slide 13 | Legal & Comms Coordination

## On screen

| Signatory | Reviews |
|-----------|-----------|
| Legal | LEGAL corpus scope, disclaimers |
| Policy owner | Content accuracy |
| Comms | Public wording T2 |
| Security | ACL, citizen data |
| DPO | PII, retention |

**Handoff:** spec → ingest → index → eval → T2 publish gate → citizen portal

---

## Speaker notes

Multi-stakeholder sign-off prevents single-team blind spots. Legal owns disclaimers and LEGAL boundary. Comms owns citizen-readable tone after T2 — not raw model output. DPO on retention and PII. Schedule joint review within one week of workshop.

---

# Slide 14 | Checklist

## On screen

**Policy corpus ready**

- [ ] LEGAL vs GUIDANCE separation
- [ ] No draft bills in citizen corpus
- [ ] Article metadata on chunks
- [ ] Plain language requires legal link + T2
- [ ] Multilingual official texts only in LEGAL
- [ ] Citizen vs internal ACL tested
- [ ] Golden set includes supersession + refusal
- [ ] Incident path for wrong public answer (12_serving_customers)

---

## Speaker notes

Review checklist against activity output. Draft bill exclusion is binary — verify staging paths. Incident path is required for production citizen bots — who toggles bot off and notifies comms? Teams tick what they completed; gaps become follow-up tickets.

---

# Slide 15 | Takeaways

## On screen

**Five principles**

1. **Authoritative LEGAL corpus** separate from explanatory GUIDANCE
2. **Preserve § structure** — metadata carries legal anchors
3. **T2 for public** — VERIFY + named approver
4. **Multilingual** — parallel official texts, not AI law translation
5. **Citizen bot** — minimal corpus, strict ACL, no PII

```
LEGAL (facts)  +  GUIDANCE (plain, linked)  +  T2 (publish)
```

---

## Speaker notes

Close workshop. Reiterate dual-corpus rule and T2 gate. Point to 08_trust_verification for approval tiers and 12_serving_customers for incident playbooks. Collect specs. Q&A — when in doubt, citizen bot retrieves LEGAL and refuses rather than guessing.

---
