# Workshop — Corpus Design for Funding & Grants Knowledge

*For funding program officers, grant admins, dev/RAG owners, and facilitators*  
*How to prepare funding documents so RAG retrieval is accurate, current, and safe*

---

## Overview

| Item | Detail |
|------|--------|
| **Duration** | 3 hours (or 2 × 90 min) |
| **Audience** | Domain owners + technical implementers |
| **Prerequisites** | [06_rag.md](../06_rag.md), [08_trust_verification.md](../08_trust_verification.md), [09_privacy_security.md](../09_privacy_security.md) |
| **Slides** | [slides/workshop_funding.md](../slides/workshop_funding.md) |
| **Outcome** | Corpus specification ready for indexing |

---

## Learning objectives

1. Select **canonical** funding sources for RAG  
2. Structure documents for **chunking and metadata**  
3. Define **ACL** by role and program  
4. Author **20+ golden questions** for funding assistant ([10_evaluation_quality.md](../10_evaluation_quality.md))  
5. Establish **refresh SLA** when calls open/close  

---

## 1. Why funding corpus quality matters (15 min)

### 1.1 Failure modes (real patterns)

| Symptom | Root cause in corpus |
|---------|---------------------|
| Wrong deadline | Superseded circular still indexed |
| Missing exclusion | Chunk split mid-§ eligibility |
| “50% co-fund” hallucination | Model guessed; no cite-only enforcement |
| Officer A sees Program B internal notes | ACL metadata missing |

### 1.2 Principle

> **RAG quality ceiling = corpus quality ceiling.**  
> No embedding model fixes contradictory PDFs.

---

## 2. What belongs in the funding corpus (20 min)

### 2.1 Include (canonical)

| Document type | Metadata required |
|---------------|-------------------|
| Published funding calls / circulars | `effective_from`, `effective_to`, `program_id` |
| Official FAQ (approved) | `version`, `owner` |
| Application guidelines (public) | `language`, `year` |
| Eligibility matrices (tables as HTML/structured if possible) | `program_id` |
| Glossary of program terms | `term_list` |

### 2.2 Exclude or separate collection

| Document type | Why |
|---------------|-----|
| Panel review scores | P2 — separate ACL |
| Applicant PII / forms | **No** in general FAQ index |
| Email threads | Noise, informal, wrong version |
| Draft unpublished calls | Wrong tier — wait until publish |
| Internal “rumor” slides | Not canonical |

### 2.2 Diagram — corpus boundaries

```
        ┌─────────────────────────────────────┐
        │  PUBLIC FUNDING CORPUS (RAG)       │
        │  published calls, FAQ, guidelines  │
        └──────────────────┬──────────────────┘
                           │
        ┌──────────────────┴──────────────────┐
        ▼                                      ▼
┌───────────────┐                    ┌─────────────────┐
│ INTERNAL ONLY │                    │ APPLICANT PII   │
│ panel notes   │                    │ NEVER in shared │
│ separate ACL  │                    │ RAG index       │
└───────────────┘                    └─────────────────┘
```

---

## 3. Document structure for retrieval (25 min)

### 3.1 Format preferences

| Priority | Format | Reason |
|----------|--------|--------|
| 1 | Structured HTML / wiki with headings | Clean chunk boundaries |
| 2 | DOCX with styles | Heading-based chunking |
| 3 | PDF (text-based) | OK with good layout |
| 4 | Scanned PDF | OCR errors — avoid if possible |

### 3.2 Heading template (example)

```markdown
# Program: SME Innovation Track 2026
## Eligibility
### Revenue cap
### Excluded entities
## Funding rates
## Deadlines
## How to apply
```

**One program per major H1** — aids metadata `program_id`.

### 3.3 Chunking rules for funding

| Content | Chunk strategy |
|---------|----------------|
| Eligibility § | One § or subsection per chunk |
| FAQ | One Q+A pair per chunk |
| Deadline table | Keep table intact in one chunk |
| Cross-program comparison | Separate doc — avoid mega-chunks |

### 3.4 Metadata schema (template)

```yaml
doc_id: FUND-2026-SME-CIRCULAR
title: SME Innovation Track 2026 — Call Circular
classification: P1
program_id: SME-2026
effective_from: 2026-01-15
effective_to: 2026-06-30
language: en
owner: funding-office@org
supersedes: FUND-2025-SME-CIRCULAR
acl_roles: [funding_officer, public_faq_bot]
```

---

## 4. Representative content examples (20 min)

### 4.1 Good chunk (retrievable)

```
[Chunk ID: SME-2026-ELIG-03]
Program: SME Innovation Track 2026
Section: Eligibility — Excluded entities

The following are NOT eligible: non-profit organizations 
registered under §X, government agencies, and entities with 
revenue above KRW 50 billion in the prior fiscal year.

Source: Circular 2026-SME §4.2
```

### 4.2 Bad chunk (boundary split)

```
... revenue above KRW 50 billion in the prior
```
*(next chunk)*  
```
fiscal year. Applicants must also submit form FG-12...
```

**Fix:** merge eligibility block; never split mid-sentence/table.

### 4.3 Golden question examples (fund corpus)

| id | query | must_cite |
|----|-------|-----------|
| F-01 | SME revenue cap? | §4.2 |
| F-02 | Non-profit eligible? | §4.2 exclusion |
| F-03 | Deadline extension 2026? | refusal if not published |
| F-04 | Co-funding % SME? | rates section |
| F-05 | Korean query same facts | multilingual embed |

---

## 5. ACL & roles (15 min)

| Role | Collections visible |
|------|---------------------|
| `public_faq_bot` | Published calls, public FAQ only |
| `funding_officer` | + internal procedure manuals (P1) |
| `panel_member` | + panel handbook — separate index |
| `citizen_portal` | Public subset only |

**Filter at query time** — never rely on model discretion.

---

## 6. Refresh & lifecycle (15 min)

### 6.1 Events triggering reindex

| Event | Action | SLA (example) |
|-------|--------|---------------|
| Call published | Ingest + index | 4 hours |
| Errata PDF | Replace doc_id version | 2 hours |
| Call closed | Set `effective_to`; optional archive | Same day |
| FAQ edit | Reindex affected chunks | 24 hours |

### 6.2 Versioning diagram

```
Circular v1 (indexed)
      │
      ▼ errata
Circular v1.1 → replace same doc_id OR supersede flag
      │
      ▼ new year
Circular 2027 → new doc_id; mark 2026 effective_to
```

---

## 7. Workshop activity — build your corpus spec (45 min)

**Teams:** 1 funding owner + 1 technical person.

### Deliverable template

```markdown
## Corpus name: _______________
## Owner: _______________

### Documents in scope (list)
| doc_id | title | class | program_id |
|--------|-------|-------|------------|

### Documents explicitly OUT of scope
-

### Chunking rules
-

### Metadata fields required
-

### ACL matrix
| role | collections |
|------|-------------|

### Golden questions (min 10)
| id | query | must_cite |

### Refresh SLA
-

### Sign-off
Domain owner: ______  Technical: ______  Security: ______
```

---

## 8. Handoff to technical team

| Item | To dev/ops |
|------|------------|
| Corpus spec (above) | Ingestion config |
| Sample files | Parser test |
| Golden set | Eval pipeline ([10](../10_evaluation_quality.md)) |
| ACL rules | Vector filter config |

---

## 9. Checklist — funding corpus ready

- [ ] One canonical source per program/year  
- [ ] Superseded calls archived or `effective_to` set  
- [ ] No applicant PII in shared index  
- [ ] Headings consistent; tables not split  
- [ ] Metadata schema validated  
- [ ] ACL tested with 3 personas  
- [ ] ≥20 golden questions with must_cite  
- [ ] Refresh webhook or schedule defined  
- [ ] T2 approver named for public FAQ bot  

---

## Takeaways

1. **Canonical published docs only** in public funding RAG.  
2. **Structure for chunking** — headings, FAQ pairs, intact tables.  
3. **Metadata** = program, dates, ACL, supersession.  
4. **Golden questions** from real officer inquiries.  
5. **Lifecycle** — publish, errata, close — each triggers index update.
