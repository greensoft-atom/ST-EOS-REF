# Slide Deck — Corpus Design for Funding & Grants Knowledge

*Source: workshops/corpus_funding.md*

---

# Slide 01 | Workshop Overview

## On screen

**Workshop — Corpus Design for Funding & Grants Knowledge**

*For funding program officers, grant admins, dev/RAG owners, and facilitators*

| Item | Detail |
|------|--------|
| **Duration** | 3 hours (or 2 × 90 min) |
| **Audience** | Domain owners + technical implementers |
| **Prerequisites** | 06_rag · 08_trust_verification · 09_privacy_security |
| **Outcome** | Corpus specification ready for indexing |

---

## Speaker notes

Welcome. This workshop bridges funding domain knowledge and RAG implementation. Pair one funding owner with one technical person per team. Keep the lecture doc open for activity templates and Q&A. Target outcome: a signed corpus spec, not a slide deck.

---

# Slide 02 | Learning Objectives

## On screen

**By the end of this workshop, teams will:**

1. Select **canonical** funding sources for RAG
2. Structure documents for **chunking and metadata**
3. Define **ACL** by role and program
4. Author **20+ golden questions** for the funding assistant
5. Establish **refresh SLA** when calls open/close

```
Domain owner          Technical owner
      │                      │
      └──── corpus spec ─────┘
              ↓
         ready to index
```

---

## Speaker notes

Read objectives aloud. Emphasize golden questions — they become your eval pipeline (see 10_evaluation_quality). ACL and refresh SLAs are non-negotiable for public-facing funding bots. Ask: who owns the corpus after today? Name them now.

---

# Slide 03 | Why Corpus Quality Matters

## On screen

**Failure modes — real patterns**

| Symptom | Root cause in corpus |
|---------|---------------------|
| Wrong deadline | Superseded circular still indexed |
| Missing exclusion | Chunk split mid-§ eligibility |
| "50% co-fund" hallucination | Model guessed; no cite-only enforcement |
| Officer A sees Program B internal notes | ACL metadata missing |

> **RAG quality ceiling = corpus quality ceiling.**  
> No embedding model fixes contradictory PDFs.

---

## Speaker notes

15 min block. Ask the room for one horror story before showing the table. Wrong deadline is almost always a supersession problem, not a model problem. Hallucinated percentages mean retrieval failed and the model filled the gap — fix with cite-only rules plus better chunks. ACL leaks are security incidents, not UX bugs.

---

# Slide 04 | Corpus Boundaries

## On screen

**What belongs — and what does not**

| Include (canonical) | Exclude or separate collection |
|---------------------|--------------------------------|
| Published calls / circulars | Panel review scores (P2 — separate ACL) |
| Official FAQ (approved) | Applicant PII / forms |
| Application guidelines (public) | Email threads |
| Eligibility matrices | Draft unpublished calls |
| Glossary of program terms | Internal "rumor" slides |

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

## Speaker notes

20 min. One canonical source per program/year — duplicates create contradictions the model cannot resolve. Applicant PII in a shared index is a hard no. Draft calls belong in a staging collection or nowhere until publish. Pause on the diagram — ask teams to list one doc they would exclude and why.

---

# Slide 05 | Required Metadata

## On screen

**Every in-scope document needs metadata**

| Document type | Metadata required |
|---------------|-------------------|
| Published funding calls / circulars | `effective_from`, `effective_to`, `program_id` |
| Official FAQ (approved) | `version`, `owner` |
| Application guidelines (public) | `language`, `year` |
| Eligibility matrices | `program_id` |
| Glossary of program terms | `term_list` |

**Rule:** If you cannot fill the metadata row, the doc is not ready to index.

---

## Speaker notes

Metadata is how you filter stale calls, enforce ACL, and audit citations. `program_id` ties chunks to the right assistant context. `effective_to` is how you close a call without deleting history. Domain owners own field values; devs own schema validation.

---

# Slide 06 | Format & Structure

## On screen

**Format preferences (best → worst)**

| Priority | Format | Reason |
|----------|--------|--------|
| 1 | Structured HTML / wiki with headings | Clean chunk boundaries |
| 2 | DOCX with styles | Heading-based chunking |
| 3 | PDF (text-based) | OK with good layout |
| 4 | Scanned PDF | OCR errors — avoid if possible |

**Heading template — one program per H1**

```markdown
# Program: SME Innovation Track 2026
## Eligibility
### Revenue cap
### Excluded entities
## Funding rates
## Deadlines
## How to apply
```

---

## Speaker notes

25 min block starts here. Structured sources chunk cleanly; scanned PDFs produce garbage at eligibility tables. One H1 per program makes `program_id` assignment trivial. If they only have PDF, plan a conversion pass before index — do not "just OCR and hope."

---

# Slide 07 | Chunking Rules

## On screen

**Chunk strategy by content type**

| Content | Chunk strategy |
|---------|----------------|
| Eligibility § | One § or subsection per chunk |
| FAQ | One Q+A pair per chunk |
| Deadline table | Keep table intact in one chunk |
| Cross-program comparison | Separate doc — avoid mega-chunks |

**Never split:** mid-sentence, mid-table, mid-eligibility list.

---

## Speaker notes

Eligibility split mid-§ is the most common funding RAG bug. FAQ pairs must stay together — question without answer retrieves nonsense. Deadline tables broken across chunks lose the date column. Cross-program comparison docs become mega-chunks that pollute single-program queries — keep them separate.

---

# Slide 08 | Metadata Schema Template

## On screen

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

| Field | Purpose |
|-------|---------|
| `supersedes` | Chain versions; retire old index entries |
| `acl_roles` | Query-time filter — not model discretion |

---

## Speaker notes

Walk through each field. `doc_id` should be stable across errata replacements or version bumps — team decides replace-in-place vs new id. `supersedes` links the audit trail. ACL at query time is mandatory: never rely on the model to "know" what role should see.

---

# Slide 09 | Good vs Bad Chunks

## On screen

**Good chunk (retrievable)**

```
[Chunk ID: SME-2026-ELIG-03]
Program: SME Innovation Track 2026
Section: Eligibility — Excluded entities

The following are NOT eligible: non-profit organizations
registered under §X, government agencies, and entities with
revenue above KRW 50 billion in the prior fiscal year.

Source: Circular 2026-SME §4.2
```

**Bad chunk (boundary split)**

```
... revenue above KRW 50 billion in the prior
```
*(next chunk)*  
```
fiscal year. Applicants must also submit form FG-12...
```

**Fix:** merge eligibility block; never split mid-sentence/table.

---

## Speaker notes

Good chunk is self-contained: program, section, full rule, source anchor. Bad chunk looks retrievable but answers "what is the revenue cap?" with a fragment — model may hallucinate the rest. Show both side by side; ask which query would fail on the bad split (F-01 revenue cap).

---

# Slide 10 | Golden Questions

## On screen

**Examples — build 20+ for your corpus**

| id | query | must_cite |
|----|-------|-----------|
| F-01 | SME revenue cap? | §4.2 |
| F-02 | Non-profit eligible? | §4.2 exclusion |
| F-03 | Deadline extension 2026? | refusal if not published |
| F-04 | Co-funding % SME? | rates section |
| F-05 | Korean query same facts | multilingual embed |

**Sources for questions:** real officer inquiries, helpdesk tickets, applicant FAQ gaps.

---

## Speaker notes

Golden questions are your regression test suite. F-03 is a refusal case — critical for funding bots that invent extensions. F-05 tests multilingual retrieval, not translation quality alone. Domain owners should own the list; devs wire it into eval (10_evaluation_quality). Minimum 20 per program or corpus — 10 is workshop floor, not production floor.

---

# Slide 11 | ACL by Role

## On screen

| Role | Collections visible |
|------|---------------------|
| `public_faq_bot` | Published calls, public FAQ only |
| `funding_officer` | + internal procedure manuals (P1) |
| `panel_member` | + panel handbook — separate index |
| `citizen_portal` | Public subset only |

```
Query → ACL filter → retrieval → model
         ↑
    never skip this step
```

**Filter at query time** — never rely on model discretion.

---

## Speaker notes

15 min. Panel materials are P2 — separate collection, separate index if possible. Public bot must never retrieve internal procedure chunks even if the user prompt-tricks. Test with three personas before go-live (checklist item). Officer A / Program B leak from the opening slide — this slide is the fix.

---

# Slide 12 | Refresh & Lifecycle

## On screen

**Events triggering reindex**

| Event | Action | SLA (example) |
|-------|--------|---------------|
| Call published | Ingest + index | 4 hours |
| Errata PDF | Replace doc_id version | 2 hours |
| Call closed | Set `effective_to`; optional archive | Same day |
| FAQ edit | Reindex affected chunks | 24 hours |

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

## Speaker notes

SLA examples are negotiable — the team must write their own in the activity. Errata without reindex causes wrong-deadline incidents. New year = new `doc_id`; old year gets `effective_to`. Define who triggers webhook vs scheduled job. FAQ edits can wait 24h; errata cannot.

---

# Slide 13 | ACTIVITY — Build Your Corpus Spec (45 min)

## On screen

**START ACTIVITY — 45 minutes**

**Teams:** 1 funding owner + 1 technical person

**Deliverable:** completed corpus spec (see workshop doc §7)

| Section | Complete? |
|---------|-----------|
| Documents in scope (+ doc_id, class, program_id) | ☐ |
| Documents explicitly OUT of scope | ☐ |
| Chunking rules | ☐ |
| Metadata fields required | ☐ |
| ACL matrix | ☐ |
| Golden questions (min 10 today; 20+ for production) | ☐ |
| Refresh SLA | ☐ |
| Sign-off: Domain · Technical · Security | ☐ |

**Facilitator:** circulate at 15 / 30 / 40 min — check ACL and golden refusal cases.

---

## Speaker notes

This is the core of the workshop. Full template is in workshops/corpus_funding.md §7. At 15 min: every team should have in-scope doc list started. At 30 min: ACL matrix and at least 5 golden questions. At 40 min: refresh SLA and sign-off names. Push back on vague chunking ("we'll figure it out") — write one concrete rule (e.g. "one FAQ pair per chunk"). Pair refusal golden question (F-03 style) before time ends.

---

# Slide 14 | Handoff to Technical Team

## On screen

**What dev/ops needs from today**

| Item | To dev/ops |
|------|------------|
| Corpus spec (activity output) | Ingestion config |
| Sample files | Parser test |
| Golden set | Eval pipeline (10_evaluation_quality) |
| ACL rules | Vector filter config |

```
Corpus spec ──→ ingest ──→ index ──→ eval gate ──→ deploy
                  ↑           ↑
            sample files   golden set
```

---

## Speaker notes

Activity output is not shelf-ware — it becomes ingestion YAML and eval CI. Sample files expose PDF layout bugs before production. Golden set runs on every index change. Schedule a handoff meeting within one week with named owners on both sides.

---

# Slide 15 | Checklist & Takeaways

## On screen

**Checklist — funding corpus ready**

- [ ] One canonical source per program/year
- [ ] Superseded calls archived or `effective_to` set
- [ ] No applicant PII in shared index
- [ ] Headings consistent; tables not split
- [ ] Metadata schema validated
- [ ] ACL tested with 3 personas
- [ ] ≥20 golden questions with must_cite
- [ ] Refresh webhook or schedule defined
- [ ] T2 approver named for public FAQ bot

**Takeaways:** canonical docs · structure for chunking · metadata + ACL · golden questions · lifecycle triggers

---

## Speaker notes

Close with checklist — teams tick what they completed in the activity. T2 approver for public FAQ bot links to 08_trust_verification. Remind: RAG ceiling = corpus ceiling. Collect specs or photograph boards before break. Q&A — refer to 06_rag for embedding/chunk size details.

---
