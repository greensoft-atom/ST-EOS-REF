# Workshop — Corpus Design for Patents & IP Knowledge

*For IP staff, patent coordinators, R&D liaisons, dev/RAG owners*  
*Grounding AI in disclosures, procedures, and public patent literature — without legal overreach*

---

## Overview

| Item | Detail |
|------|--------|
| **Duration** | 3 hours |
| **Audience** | IP domain + technical |
| **Prerequisites** | [06_rag.md](../06_rag.md), [08_trust_verification.md](../08_trust_verification.md) |
| **Slides** | [slides/workshop_patents.md](../slides/workshop_patents.md) |
| **Outcome** | IP corpus spec + T3 boundaries documented |

---

## Learning objectives

1. Separate **public**, **internal procedural**, and **pre-filing confidential** content  
2. Structure patent docs for RAG without **inventing prior art**  
3. Define **T3** workflows — AI draft vs attorney decision  
4. Build golden set for **docket, procedure, and literature** queries  
5. Avoid **hallucinated citations** — strict cite-only rules  

---

## 1. IP-specific risks (15 min)

| Risk | Consequence |
|------|-------------|
| Hallucinated prior art | Wrong patent strategy |
| Leak of unpublished invention | Loss of rights / trade secret |
| AI “legal advice” tone | Applicant over-relies |
| Mixed jurisdictions in one chunk | Wrong rule cited |

**Facilitator line:**

> “IP assistant is **T1 draft / T3 filing** — never auto-file, never trust invented references.”

---

## 2. Corpus tiers for IP (20 min)

### 2.1 Three collections (recommended)

```
┌─────────────────────────────────────────┐
│ COLLECTION A — Public / open literature │
│ patent office guides (public),          │
│ published patents, org public IP policy │
│ ACL: wide internal + public subset      │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ COLLECTION B — Internal procedures      │
│ filing checklists, docket SOP, templates│
│ ACL: IP staff + R&D liaison             │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ COLLECTION C — Pre-filing confidential    │
│ invention disclosures (active dockets)    │
│ ACL: per-case OR exclude from RAG       │
│ DEFAULT: manual upload per session only │
└─────────────────────────────────────────┘
```

### 2.2 Default recommendation for Collection C

| Approach | When |
|----------|------|
| **No index** — user attaches disclosure per session | Highest security |
| **Separate encrypted index per matter** | High volume IP firm style |
| **Never** mix matters in one shared index | Prevents cross-leak |

---

## 3. Document types & chunking (25 min)

### 3.1 Public / guides

| Doc | Chunk by |
|-----|----------|
| Patent office filing guide | Section (priority, PCT, fees) |
| Public IP policy | Article number |
| Published patent (org) | Claims / description / abstract separate metadata |

### 3.2 Internal SOP

| Doc | Chunk by |
|-----|----------|
| Filing checklist | Step number |
| Docket milestone defs | Milestone ID |
| Template cover letters | Template ID — not merged with law |

### 3.3 Metadata schema (IP)

```yaml
doc_id: IP-SOP-PCT-001
domain: patents
jurisdiction: KR
doc_type: procedure | policy | literature | disclosure
matter_id: null | MAT-2026-0042
classification: P1
status: published | draft
effective_from: 2026-01-01
acl_roles: [ip_staff, rd_liaison]
```

**jurisdiction** filter critical — US vs KR procedures differ.

---

## 4. Prompt & assistant rules (IP-specific) (15 min)

### System rules (add to IP assistant)

```
1. Never invent patent numbers, dates, or citations.
2. Label all outputs "Draft for attorney/reviewer — not legal advice."
3. Prior art discussions: only from retrieved documents.
4. If user asks "are we infringing?" → refuse; escalate human.
5. Do not compare to unpublished third-party secrets.
```

### Tier mapping

| Task | Tier |
|------|------|
| Explain PCT timeline from guide | T1 |
| Draft background from **attached** disclosure | T1 → attorney |
| Claim language final | T3 attorney only |
| Freedom-to-operate opinion | T3 — no AI |

---

## 5. Golden questions (examples) (15 min)

| id | query | must_cite | must_not |
|----|-------|-----------|----------|
| P-01 | PCT national phase deadline from guide? | IP-SOP-PCT § | invent deadline |
| P-02 | Org policy on inventor remuneration? | IP-POLICY § | |
| P-03 | Prior art on graphene battery sep 2024? | literature ids only | fake papers |
| P-04 | Draft background from disclosure MAT-X | attached doc | other matters |
| P-05 | "Are we infringing patent X?" | — | refuse T3 |

---

## 6. Literature vs disclosure RAG (15 min)

### 6.1 Public literature assistant

- Index: abstracts, org publications, open databases (per license)  
- Use for: landscape summaries, **with DOI verify** ([08](../08_trust_verification.md))  
- Chunk: abstract + claims summary separately  

### 6.2 Disclosure assistant (session-scoped)

```
User uploads MAT-2026-0042 disclosure (one session)
        │
        ▼
Temporary chunks tagged matter_id
        │
        ▼
Purged after session / retention policy
```

**Do not** promote to shared index without matter ACL.

---

## 7. Activity — IP corpus spec (45 min)

Use same deliverable structure as [corpus_funding.md](corpus_funding.md) §7 with IP fields:

- Collections A/B/C scope  
- jurisdiction metadata  
- T3 task list (what AI must refuse)  
- 10 golden questions  
- Retention for session uploads  

---

## 8. Coordination with legal

| Topic | Agreement |
|-------|-----------|
| Output disclaimer | Required on all IP UI |
| Log retention | Legal + DPO |
| Export control / trade secret | Some matters excluded entirely |
| Attorney review before filing | T3 gate |

---

## 9. Checklist

- [ ] Collections A/B/C defined  
- [ ] Collection C default = session-only or per-matter index  
- [ ] jurisdiction on every procedural chunk  
- [ ] No unpublished third-party data in index  
- [ ] cite-only + refusal prompts in system template  
- [ ] Golden set includes refusal cases (P-05 style)  
- [ ] Attorney sign-off on assistant scope  

---

## Takeaways

1. **Three collections** — public, internal SOP, confidential matters.  
2. **Never hallucinate prior art** — citations from retrieval only.  
3. **T3 for legal conclusions** — AI drafts and explains procedures only.  
4. **jurisdiction metadata** — avoid wrong-country rules.  
5. **Session-scoped disclosures** — default for active inventions.
