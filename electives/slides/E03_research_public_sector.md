# Slide Deck — AI in Research, Innovation & Public Sector

*Source: [E03_research_public_sector.md](../E03_research_public_sector.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Elective E03 — AI in Research, Innovation & Public Sector**

*60 minutes · All members · General use cases (not org product roadmap)*

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Map **use cases** across research, patents, funding, policy, exhibitions |
| 2 | Assign **risk tier** preview (T0–T3) |
| 3 | See **stacked workflow**: recognition → RAG → generative → human |

## Speaker notes

Frame this as a sector tour — not a commitment to build everything on slide. Audience may span research, innovation agencies, and comms. Risk tiers preview Lecture 08; don't full-teach T0–T3 here, just apply labels. Customize examples to your country's programs if possible.

---

# Slide 02 | Sector Map

## On screen

**Where AI shows up in S&T and public missions**

```
Research ────── literature, lab notes, collaboration
Innovation ──── patents, startups, tech transfer
Funding ─────── calls, eligibility, reporting
Policy ──────── briefings, citizen information
Exhibitions ─── content, visitor FAQ
```

| Sector | Typical document volume | Stakes |
|--------|-------------------------|--------|
| Research | High (papers, notes) | Accuracy, citation integrity |
| Funding | High (forms, circulars) | Fairness, legal compliance |
| Policy | Medium–high | Public trust |
| Exhibitions | Medium | Representation, brand |

## Speaker notes

Walk the diagram top to bottom. Ask who sits in which band — validates mixed audience. Emphasize same AI stack, different stakes (preview of slide 11 takeaways). Not org roadmap: these are industry patterns your teams may request or pilot.

---

# Slide 03 | Use Case Table — All Sectors

## On screen

**Recognition · Generative + RAG · Human must**

| Sector | Recognition AI | Generative + RAG | Human must |
|--------|----------------|------------------|------------|
| **Research** | Paper classification | Literature summary + cite | Verify DOIs |
| **Patents** | Prior art search assist | Draft background section | Attorney files |
| **Funding** | Form completeness check | FAQ from circulars | Award decision |
| **Policy** | Document routing | Plain-language draft | Legal approve |
| **Exhibitions** | Image tagging | Caption draft | Comms approve |

> **Generative assists language; humans own decisions and publication.**

## Speaker notes

Spend most time on this table — it's the core 20-minute block. For each row, one concrete example from your org if available. Stress "human must" column — never skip it. Recognition handles bulk; RAG+generative handles language; humans handle stakes.

---

# Slide 04 | Stacked Workflow

## On screen

**Standard public-sector AI pipeline**

```
Ingest archives → classify (Recognition)
       → index for RAG
       → answer / summarize (Generative)
       → VERIFY → approve → publish
```

| Stage | Owner | Failure if skipped |
|-------|-------|-------------------|
| Ingest & classify | Ops + domain | Wrong corpus |
| RAG index | Technical + corpus owner | Stale or missing docs |
| Generate | User + assistant | Hallucination |
| Verify → publish | Named approver | Public error |

From: [03_gai_rai.md](../../03_gai_rai.md) · [06_rag.md](../../06_rag.md)

## Speaker notes

Diagram is the mental model for E03. Every sector slide maps to this chain. VERIFY is not bureaucracy — it's where accountability lives. If someone asks "can we skip classify and just RAG everything?" — note quality and cost reasons to classify first.

---

# Slide 05 | Sector Highlight — Research

## On screen

**Research use cases**

| Task | AI role | Human gate |
|------|---------|------------|
| Tag 50k papers by field | Recognition — bulk index | Sample quality (T1) |
| "What did we publish on X?" | RAG + summary | Verify DOIs and quotes |
| Draft literature review section | Generative | PI / author approves |
| Grant novelty check assist | RAG prior work | Researcher decides |

**Risk note:** Wrong citation in a summary damages credibility — always verify sources.

## Speaker notes

Researchers often want literature tools first — acknowledge enthusiasm, channel to verify. Auto-tag at scale is classic T1: human samples, doesn't read every row. Tie to corpus workshops if you run domain-specific sessions later.

---

# Slide 06 | Sector Highlight — Patents & Innovation

## On screen

**Patents & innovation use cases**

| Task | AI role | Human gate |
|------|---------|------------|
| Prior art search assist | Recognition + retrieval | Patent attorney reviews |
| Draft background section | Generative | Attorney edits and files |
| Startup program FAQ | RAG over program rules | Program office approves |
| Tech transfer routing | Classify inquiry type | Staff handles negotiation |

**Risk note:** Legal filing is **never** fully automated — AI prepares, professionals commit.

## Speaker notes

Innovation agencies sit between research and legal — emphasize attorney/examiner in the loop. Prior art search is assist, not replacement for professional search strategy. If audience includes startups, mention FAQ bots as T2 public content with approval.

---

# Slide 07 | Sector Highlight — Funding

## On screen

**Funding use cases**

| Task | AI role | Human gate |
|------|---------|------------|
| Form completeness check | Recognition | Applicant fixes; staff exceptions |
| Eligibility FAQ from circulars | RAG + generative | Approve before web (T2) |
| Report narrative draft | Generative | Finance / program review |
| **Select grant winner** | **Not appropriate** | **Human only (T3)** |

| Tier preview | Example |
|--------------|---------|
| T1 | Auto-tag applications — sample QA |
| T2 | Public FAQ — approve before publish |
| T3 | Award decision — **no full automation** |

## Speaker notes

Funding is where tier discussion gets real. Strong line: AI must not decide winners — info and drafts only. Form completeness is low drama; public FAQ wrong deadline is high drama — maps to T2. Preview [08_trust_verification.md](../../08_trust_verification.md) tiers without full lecture.

---

# Slide 08 | Sector Highlight — Policy & Exhibitions

## On screen

**Policy & exhibitions use cases**

| Sector | Recognition | Generative + RAG | Human must |
|--------|-------------|------------------|------------|
| **Policy** | Route memos to desk | Plain-language citizen draft | Legal / policy approve |
| **Exhibitions** | Tag images by theme | Draft captions & visitor FAQ | Comms / cultural review |

**Shared risks:**

| Risk | Mitigation |
|------|------------|
| Incomplete policy corpus | Corpus owner updates |
| Stereotyped cultural descriptions | Diverse review (see E04) |
| Wrong citizen-facing deadline | T2 approval workflow |

## Speaker notes

Policy and exhibitions share public-facing language risk. Exhibition AI errors can offend — connect to E04 stereotypes case. Policy routing is recognition-heavy; citizen briefings are generative with legal gate. Ask comms folks in room what they would never auto-publish.

---

# Slide 09 | Activity — Tier the Use Case

## On screen

**START ACTIVITY — ~15 minutes**

**Assign tier (T0–T3) and justify:**

| Scenario | Your tier | Why? |
|----------|-----------|------|
| Auto-tag 50k papers by field | | |
| Public grant FAQ with citations | | |
| AI decides grant winner | | |
| Brainstorm session titles | | |

**Suggested answers (discuss, don't rush):**

| Scenario | Tier | Rationale |
|----------|------|-----------|
| Auto-tag papers | **T1** | Human samples quality |
| Public grant FAQ | **T2** | Approve before web |
| AI decides winner | **No** / **T3 human only** | Accountability |
| Brainstorm titles | **T0** | Low external risk |

## Speaker notes

Groups debate middle cases — that's the learning. "AI decides winner" should be unanimous no. FAQ tier sparks T2 workflow discussion. Brainstorm T0 shows not everything needs a committee. Debrief links to org tier definitions in Lecture 08.

---

# Slide 10 | Domain Workshops & Corpus Owners

## On screen

**Go deeper by domain — corpus workshops**

| Domain | Workshop doc |
|--------|----------------|
| Funding programs | [workshops/corpus_funding.md](../../workshops/corpus_funding.md) |
| Patents | [workshops/corpus_patents.md](../../workshops/corpus_patents.md) |
| Policy | [workshops/corpus_policy.md](../../workshops/corpus_policy.md) |

**Who owns the corpus?** Domain experts — not only IT.

| Role | Responsibility |
|------|----------------|
| Corpus owner | Accuracy, updates, scope |
| Approver | T2 public content |
| Technical team | Index, access, security |

## Speaker notes

Brief pointer — 5 minutes max. Message: successful public AI is a **corpus program**, not a one-off chatbot project. Identify who in the room could be a corpus owner. Workshops are optional follow-ons for implementation phase.

---

# Slide 11 | Takeaways

## On screen

**Remember**

| # | Takeaway |
|---|----------|
| 1 | Same AI stack — different **stakes** per sector |
| 2 | **Recognition** still does bulk indexing and routing |
| 3 | **Generative** assists language; **humans** decide high stakes |
| 4 | Tier thinking (T0–T3) prevents category errors |

**One line:** In public sector AI, the workflow ends with verify and approve — not with the model's last token.

## Speaker notes

Recap using sector map + stacked workflow. Ask one person to name a use case from their desk and which column (R, G, human) matters most. Reinforce T3 boundaries for funding and policy.

---

# Slide 12 | Next Steps

## On screen

**Where to go next**

| Module | Topic |
|--------|-------|
| [08_trust_verification.md](../../08_trust_verification.md) | Full T0–T3 tiers & VERIFY |
| [06_rag.md](../../06_rag.md) | RAG architecture |
| [E04_bias_fairness.md](../E04_bias_fairness.md) | Equity in public AI |
| [E05_ai_and_jobs.md](../E05_ai_and_jobs.md) | Roles & skills |

**Facilitator:** Pick **one** sector for a 10-minute deep Q&A before dismissing.

## Speaker notes

Offer optional stay-for-Q&A by sector. Leadership may want funding/policy slides revisited — note slide numbers. Thank domain experts in the room; their corpus work makes the stack diagram real.

---
