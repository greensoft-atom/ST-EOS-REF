# Workshop — Corpus Design for Policy & Government Knowledge

*For policy officers, legal liaisons, comms, dev/RAG owners*  
*Authoritative policy text, plain-language aids, and public-sector RAG governance*

---

## Overview

| Item | Detail |
|------|--------|
| **Duration** | 3 hours |
| **Audience** | Policy domain + technical + legal consult |
| **Prerequisites** | [06_rag.md](../06_rag.md), [08_trust_verification.md](../08_trust_verification.md), [09_privacy_security.md](../09_privacy_security.md) |
| **Slides** | [slides/workshop_policy.md](../slides/workshop_policy.md) |
| **Outcome** | Policy corpus spec + T2 publish workflow |

---

## Learning objectives

1. Distinguish **normative** (official) vs **explanatory** (guidance) documents  
2. Preserve **article numbering** and defined terms in chunks  
3. Design **plain-language** layers without replacing legal text  
4. Plan **minister / public** T2 approval workflow  
5. Handle **multilingual** policy corpora  

---

## 1. Policy RAG in government context (15 min)

### 1.1 Stakes

| Error type | Impact |
|------------|--------|
| Wrong effective date | Non-compliance |
| Softening “must” to “may” | Legal misrepresentation |
| Mixing draft bill with enacted law | False rights/duties |
| Citizen PII in index | Privacy incident |

### 1.2 Dual-corpus model (recommended)

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

**Rule:** Public bot answers **legal facts** from LEGAL corpus; guidance may help readability but cannot contradict.

---

## 2. Document inclusion matrix (20 min)

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

## 3. Chunking & legal structure (25 min)

### 3.1 Preserve legal anchors

Each chunk should carry:

```
article: §12.3
paragraph: (2)
defined_terms: ["Applicant", "Eligible entity"]
legal_ref: REG-2024-SCIENCE-TECH-12
```

### 3.2 Chunk boundaries

| Do | Don't |
|----|-------|
| Split at article / subsection | Split mid-paragraph |
| Keep "Article 5 — Definitions" intact | Merge unrelated articles |
| Attach footnotes to parent chunk | Orphan footnotes |

### 3.3 Example chunk

```
[LEGAL-REG-2024-§7.2]
§7.2 Submission deadline. Applications must be submitted 
by 17:00 local time on the date specified in the annual 
call notice. Late submissions shall not be accepted.

Defined terms: Application, Call notice
Effective: 2024-03-01
```

---

## 4. Plain language workflow (20 min)

### 4.1 Two-step pattern (T2)

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

### 4.2 UI requirement

Show **both**:

- Plain summary  
- Link to official § with highlight  

### 4.3 Forbidden

- AI-only plain text on web with no legal link  
- Auto-publish without approver ([08](../08_trust_verification.md) T2)

---

## 5. Multilingual policy (15 min)

| Approach | Detail |
|----------|--------|
| Parallel docs per language | `language: ko`, `language: en`, same `legal_ref` |
| Single embed model | Multilingual embedding ([06](../06_rag.md)) |
| Query language | Retrieve matching language + fallback rule |
| Translation | Official translation only — not AI-translated law text for LEGAL corpus |

---

## 6. Golden questions (policy) (15 min)

| id | query | must_cite | note |
|----|-------|-----------|------|
| POL-01 | Deadline for submissions §7.2 | REG §7.2 | exact time |
| POL-02 | Who is "Eligible entity"? | Definitions §2 | |
| POL-03 | 2023 rule still apply? | refusal if superseded | effective_to |
| POL-04 | Plain summary of §7.2 | LEGAL + GUIDANCE | T2 |
| POL-05 | Draft bill leak query | refuse | not in corpus |

---

## 7. Citizen-facing vs internal (15 min)

| Feature | Citizen portal | Internal officer |
|---------|----------------|------------------|
| Corpus | LEGAL public + approved GUIDANCE | + internal procedures |
| ACL | `citizen`, `anonymous` blocked from internal | `policy_officer` |
| Tier | T2 for published answers | T1 internal drafts |
| PII | Never collect in chat | Escalation forms |

---

## 8. Activity — policy corpus spec (45 min)

Deliverable (extend funding template):

- LEGAL vs GUIDANCE document lists  
- Article-level chunking rules  
- `legal_ref` linking schema  
- T2 approver role name  
- Multilingual plan  
- 10 golden questions including superseded-law case  

---

## 9. Legal & comms coordination

| Signatory | Reviews |
|-----------|---------|
| Legal | LEGAL corpus scope, disclaimers |
| Policy owner | Content accuracy |
| Comms | Public wording T2 |
| Security | ACL, citizen data |
| DPO | PII, retention |

---

## 10. Checklist

- [ ] LEGAL vs GUIDANCE separation  
- [ ] No draft bills in citizen corpus  
- [ ] Article metadata on chunks  
- [ ] Plain language requires legal link + T2  
- [ ] Multilingual official texts only in LEGAL  
- [ ] Citizen vs internal ACL tested  
- [ ] Golden set includes supersession + refusal  
- [ ] Incident path for wrong public answer ([12](../12_serving_customers.md))  

---

## Takeaways

1. **Authoritative LEGAL corpus** separate from explanatory GUIDANCE.  
2. **Preserve § structure** — metadata carries legal anchors.  
3. **T2 for public** — VERIFY + named approver.  
4. **Multilingual** — parallel official texts, not AI law translation.  
5. **Citizen bot** — minimal corpus, strict ACL, no PII.
