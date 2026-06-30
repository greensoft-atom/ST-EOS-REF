# Slide Deck — Corpus Design for Patents & IP Knowledge

*Source: workshops/corpus_patents.md*

---

# Slide 01 | Workshop Overview

## On screen

**Workshop — Corpus Design for Patents & IP Knowledge**

*For IP staff, patent coordinators, R&D liaisons, dev/RAG owners*

| Item | Detail |
|------|--------|
| **Duration** | 3 hours |
| **Audience** | IP domain + technical |
| **Prerequisites** | 06_rag · 08_trust_verification |
| **Outcome** | IP corpus spec + T3 boundaries documented |

---

## Speaker notes

IP RAG has higher stakes than generic FAQ bots: hallucinated prior art and leaked disclosures have legal consequences. Set tone early: assistant is **T1 draft / T3 filing** — never auto-file, never trust invented references. Pair IP staff with technical implementers.

---

# Slide 02 | Learning Objectives

## On screen

**By the end of this workshop, teams will:**

1. Separate **public**, **internal procedural**, and **pre-filing confidential** content
2. Structure patent docs for RAG without **inventing prior art**
3. Define **T3** workflows — AI draft vs attorney decision
4. Build golden set for **docket, procedure, and literature** queries
5. Avoid **hallucinated citations** — strict cite-only rules

```
Collection A (public)  →  wide access
Collection B (SOP)     →  IP staff + liaison
Collection C (confidential) → session-only default
```

---

## Speaker notes

Objectives map to three collections — the backbone of the spec. T3 boundaries must be written down, not assumed. Golden set must include **refusal** cases (P-05). Attorney sign-off on assistant scope is a checklist item, not optional for filing-adjacent tools.

---

# Slide 03 | IP-Specific Risks

## On screen

| Risk | Consequence |
|------|-------------|
| Hallucinated prior art | Wrong patent strategy |
| Leak of unpublished invention | Loss of rights / trade secret |
| AI "legal advice" tone | Applicant over-relies |
| Mixed jurisdictions in one chunk | Wrong rule cited |

> **Facilitator line:**  
> "IP assistant is **T1 draft / T3 filing** — never auto-file, never trust invented references."

---

## Speaker notes

15 min. Ask IP staff in the room which row keeps them up at night — usually leak or hallucinated prior art. Mixed jurisdictions is subtle: a KR procedure chunk answering a US filing question destroys trust and strategy. Disclaimer on every UI output is non-negotiable (slide 08).

---

# Slide 04 | Three Collections

## On screen

**Recommended corpus tiers**

```
┌─────────────────────────────────────────┐
│ COLLECTION A — Public / open literature │
│ patent office guides, published patents,│
│ org public IP policy                    │
│ ACL: wide internal + public subset      │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ COLLECTION B — Internal procedures      │
│ filing checklists, docket SOP, templates│
│ ACL: IP staff + R&D liaison             │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ COLLECTION C — Pre-filing confidential  │
│ invention disclosures (active dockets)  │
│ DEFAULT: manual upload per session only │
└─────────────────────────────────────────┘
```

---

## Speaker notes

20 min. A is shareable ground truth and literature. B is how your org files — not law, but procedure. C is highest risk — default is **no shared index**. Never mix matters in one C index. Pause on diagram; teams label which docs they own in A vs B.

---

# Slide 05 | Collection C — Security Default

## On screen

**Pre-filing confidential — choose deliberately**

| Approach | When |
|----------|------|
| **No index** — user attaches disclosure per session | Highest security (default) |
| **Separate encrypted index per matter** | High volume IP firm style |
| **Never** mix matters in one shared index | Prevents cross-leak |

**Rule:** Do not promote session uploads to shared index without matter ACL + legal review.

---

## Speaker notes

Default recommendation: session-only upload for active inventions. Per-matter encrypted index is for firms with volume and mature security. Shared C index across matters is a disqualifying design — cross-leak is inevitable. Retention policy for session chunks must be defined in the activity.

---

# Slide 06 | Document Types & Chunking

## On screen

**Public / guides**

| Doc | Chunk by |
|-----|----------|
| Patent office filing guide | Section (priority, PCT, fees) |
| Public IP policy | Article number |
| Published patent (org) | Claims / description / abstract — separate metadata |

**Internal SOP**

| Doc | Chunk by |
|-----|----------|
| Filing checklist | Step number |
| Docket milestone defs | Milestone ID |
| Template cover letters | Template ID — not merged with law |

---

## Speaker notes

25 min block. Do not merge template cover letters with statutory guides — retrieval conflates "how we write" with "what law requires." Published patent chunks need claims vs description separation for landscape queries. Step-number chunking keeps checklist retrieval precise.

---

# Slide 07 | Metadata Schema (IP)

## On screen

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

| Critical filter | Why |
|-----------------|-----|
| `jurisdiction` | US vs KR procedures differ |
| `matter_id` | Scope disclosure chunks to one case |
| `doc_type` | Route queries to procedure vs literature |

---

## Speaker notes

Every procedural chunk needs `jurisdiction` — missing field = wrong-country advice. `matter_id` null for A/B; set for C session chunks only. `status: draft` should not index into production assistants. Teams copy schema into activity spec and add org-specific fields if needed.

---

# Slide 08 | Assistant Rules & Tier Mapping

## On screen

**System rules (add to IP assistant)**

1. Never invent patent numbers, dates, or citations.
2. Label all outputs *"Draft for attorney/reviewer — not legal advice."*
3. Prior art discussions: only from retrieved documents.
4. If user asks "are we infringing?" → refuse; escalate human.
5. Do not compare to unpublished third-party secrets.

| Task | Tier |
|------|------|
| Explain PCT timeline from guide | T1 |
| Draft background from **attached** disclosure | T1 → attorney |
| Claim language final | T3 attorney only |
| Freedom-to-operate opinion | T3 — no AI |

---

## Speaker notes

15 min. Rules 1 and 3 prevent hallucinated prior art. Rule 4 is a golden refusal (P-05). Tier table goes into corpus spec as "T3 task list — what AI must refuse." Claim language and FTO are attorney-only — no exceptions for "just a quick check."

---

# Slide 09 | Golden Questions

## On screen

| id | query | must_cite | must_not |
|----|-------|-----------|----------|
| P-01 | PCT national phase deadline from guide? | IP-SOP-PCT § | invent deadline |
| P-02 | Org policy on inventor remuneration? | IP-POLICY § | |
| P-03 | Prior art on graphene battery Sep 2024? | literature ids only | fake papers |
| P-04 | Draft background from disclosure MAT-X | attached doc | other matters |
| P-05 | "Are we infringing patent X?" | — | refuse T3 |

**Include refusal cases in every IP golden set.**

---

## Speaker notes

P-01 tests cite-only vs invented deadlines. P-03 tests literature — must_not fake papers is eval criteria. P-04 ensures matter isolation. P-05 is mandatory refusal — if eval passes a FTO answer, your prompts are broken. Teams add 10+ org-specific questions in activity.

---

# Slide 10 | Literature vs Disclosure RAG

## On screen

**Public literature assistant (Collection A)**

- Index: abstracts, org publications, open databases (per license)
- Use for: landscape summaries — **with DOI verify** (08_trust_verification)
- Chunk: abstract + claims summary separately

**Disclosure assistant (session-scoped)**

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

## Speaker notes

15 min. Literature needs license compliance — not every database is indexable. DOI verify catches hallucinated papers. Session path is the safe default for Collection C. Draw retention on whiteboard: when are session chunks purged? Legal + DPO input required.

---

# Slide 11 | ACTIVITY — IP Corpus Spec (45 min)

## On screen

**START ACTIVITY — 45 minutes**

**Use funding workshop deliverable structure + IP fields**

| Section | IP-specific additions |
|---------|----------------------|
| Collections A / B / C scope | ☐ |
| `jurisdiction` on procedural chunks | ☐ |
| T3 task list (what AI must refuse) | ☐ |
| Golden questions (min 10; include P-05-style refusal) | ☐ |
| Retention for session uploads | ☐ |
| Sign-off: IP owner · Technical · Attorney | ☐ |

**Facilitator:** at 20 min verify Collection C default is session-only or per-matter — not shared.

---

## Speaker notes

45 min core activity. Full template in workshops/corpus_funding.md §7 — extend with IP fields from corpus_patents.md §7. Block shared Collection C index unless legal in room explicitly accepts risk. Attorney sign-off line is required before closing. Circulate: jurisdiction on every B chunk, one refusal golden question minimum.

---

# Slide 12 | Coordination with Legal

## On screen

| Topic | Agreement |
|-------|-----------|
| Output disclaimer | Required on all IP UI |
| Log retention | Legal + DPO |
| Export control / trade secret | Some matters excluded entirely |
| Attorney review before filing | T3 gate |

```
AI draft (T1) ──→ attorney review ──→ filing (T3)
                      ↑
                 never skip
```

---

## Speaker notes

Legal is not optional stakeholder — disclaimer, retention, export control exclusions. Some matters must be excluded from RAG entirely (trade secret, export-controlled). Filing gate is human T3; demo no auto-submit paths in UI. Schedule legal review of corpus spec within two weeks.

---

# Slide 13 | Checklist

## On screen

**IP corpus ready**

- [ ] Collections A/B/C defined
- [ ] Collection C default = session-only or per-matter index
- [ ] `jurisdiction` on every procedural chunk
- [ ] No unpublished third-party data in index
- [ ] cite-only + refusal prompts in system template
- [ ] Golden set includes refusal cases (P-05 style)
- [ ] Attorney sign-off on assistant scope

---

## Speaker notes

Walk checklist — teams mark done from activity. Refusal in golden set is the most skipped item — insist. Attorney sign-off differs from technical sign-off — name a person. Unpublished third-party secrets never belong in A or B.

---

# Slide 14 | Takeaways

## On screen

**Five principles**

1. **Three collections** — public, internal SOP, confidential matters
2. **Never hallucinate prior art** — citations from retrieval only
3. **T3 for legal conclusions** — AI drafts and explains procedures only
4. **`jurisdiction` metadata** — avoid wrong-country rules
5. **Session-scoped disclosures** — default for active inventions

```
Public guide (T1 explain)  ≠  Filing decision (T3 human)
```

---

## Speaker notes

Close with principles. Reiterate facilitator line from slide 03. Point to 08_trust_verification for cite-only patterns and 06_rag for chunking mechanics. Collect specs; offer office hours for eval wiring. Q&A — escalate "is this patentable?" to human every time.

---
