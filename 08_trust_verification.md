# Trust, Accuracy & Human Judgment

*For all members — developers, operators, staff, support, and leadership*  
*Hallucination, grounding, verification workflows, and when humans must decide*

---

## Module Overview

This is **Lecture 8**. LLMs sound confident; **trust must be earned through process**, not model fluency. This session defines accuracy, hallucination, verification tiers, and human-in-the-loop workflows for research, funding, patents, and public communication.

| Lecture | Topic |
|---------|--------|
| 7 | [Prompting](07_prompting.md) — how we instruct |
| **8 (this)** | **Trust, accuracy & verification** |
| 9 | [Privacy & security](09_privacy_security.md) |
| 10 | [Evaluation & quality](10_evaluation_quality.md) |

**Duration:** 90 minutes  
**Prerequisites:** [04_genai_llm.md](04_genai_llm.md), [06_rag.md](06_rag.md), [07_prompting.md](07_prompting.md)

---

## Learning Objectives

By the end, members should be able to:

1. Define **hallucination, grounding, staleness**, and **overconfidence** precisely  
2. Apply a **risk tier** model (assistive → publishable → decisional)  
3. Execute **verification checklists** by domain (funding, policy, research, patents)  
4. Design workflows where AI drafts and **humans approve**  
5. Communicate limits honestly to colleagues and customers  

---

## 1. Opening (5 min)

**Facilitator message:**

> “Trust is not ‘the AI seems smart.’ Trust is **traceable evidence + accountable human** for anything that matters externally or legally.”

**Poll:** Have you trusted a fluent paragraph without checking? What would have gone wrong if it were published?

---

## 2. Concepts — Accuracy, Grounding & Hallucination (15 min)

### 2.1 Definitions (use consistently across the org)

| Term | Definition | Example |
|------|------------|---------|
| **Hallucination** | Confident output **not supported** by facts or sources | Fake DOI, invented grant clause |
| **Ungrounded** | No citation or source when one was required | Policy paraphrase with no § reference |
| **Stale** | Was true; corpus or training outdated | 2023 deadline in 2026 answer |
| **Incomplete** | Omits critical caveat from source | “Eligible” without exclusion list |
| **Grounded** | Claims trace to retrieved or attached sources | Each bullet has [Source 2] |
| **Verified** | Human confirmed against authoritative record | Officer checked PDF §4.2 |

**Hierarchy:**

```
Fluent text  →  Grounded (cited)  →  Verified (human)  →  Approved (authorized publish)
     ↑ weak              ↑ better              ↑ required for high-stakes
```

### 2.2 Why LLMs hallucinate (mental model from Lecture 4)

LLMs predict **plausible** continuations — not truth lookups ([04](04_genai_llm.md)).

| Cause | Mitigation |
|-------|------------|
| No relevant source in context | RAG + “insufficient info” ([06](06_rag.md)) |
| Weak cite-only prompt | System rules ([07](07_prompting.md)) |
| User asks for references not in corpus | Refuse or retrieve |
| Long multi-hop reasoning | Break into steps; tools ([11](11_agents_tools.md)) |
| High temperature | Lower for factual tasks |

### 2.3 Recognition AI errors vs generative errors

| | Recognition AI ([03](03_gai_rai.md)) | Generative LLM |
|---|--------------------------------------|----------------|
| **Error feel** | Silent mislabel | Persuasive wrong prose |
| **Detection** | Metrics, thresholds | Human read + citation check |
| **User risk** | Wrong routing | Wrong belief |

**Both** need human oversight at high stakes.

---

## 3. Risk Tiers — How Much Verification? (12 min)

### 3.1 Four-tier model (org standard)

| Tier | Description | AI role | Human role | Examples |
|------|-------------|---------|------------|----------|
| **T0 — Explore** | Internal brainstorming | Draft freely | Optional spot-check | Workshop title ideas |
| **T1 — Assist** | Internal working docs | Draft + cite | Review before sharing outside team | Internal meeting notes |
| **T2 — Publish** | External or official-adjacent | Draft + cite | **Named approver** signs off | Web FAQ, press draft |
| **T3 — Decision** | Legal, funding, rights, safety | Inform only | **Human decision**; AI not authoritative | Grant award, patent filing |

```
                    RISK ↑
         T0 ──► T1 ──► T2 ──► T3
         explore  assist  publish  decision
         │        │        │         │
    AI heavy  AI+review  approve   human only
```

### 3.2 Mapping tasks (representative)

| Task | Tier | Rule |
|------|------|------|
| Summarize public paper for lab | T1 | Verify key numbers |
| Answer internal funding FAQ with RAG | T1–T2 | Citations required; T2 if customer-facing |
| Draft minister briefing | T2 | Policy owner approval |
| Auto-publish AI text to public web | **Forbidden** without T2 workflow | |
| Determine grant eligibility alone | T3 | AI may list criteria; officer decides |
| Patent claims drafting | T3 | Attorney only |

**Leadership** assigns tier per product feature — document in governance register.

### 3.3 UI and messaging by tier

| Tier | UI should show |
|------|----------------|
| T0–T1 | “AI-generated draft” |
| T2 | Citations + “Requires approval before external use” |
| T3 | “Decision support only — not a decision” |

---

## 4. Verification Methods (18 min)

### 4.1 The VERIFY checklist (universal)

| Step | Action |
|------|--------|
| **V** | **View sources** — open cited document, not just AI summary |
| **E** | **Examine dates & versions** — effective date, superseded? |
| **R** | **Reconcile numbers** — amounts, deadlines, percentages |
| **I** | **Identify scope** — does rule apply to this case? |
| **F** | **Find conflicts** — two sources disagree? escalate |
| **Y** | **Yes to approver** — only if all above pass for tier |

### 4.2 Domain-specific checks

#### Funding & grants

| Check | Question |
|-------|----------|
| Circular version | Is this the current published call? |
| Eligibility | All exclusion criteria listed? |
| Dates | Time zone, extension, grace period? |
| Amounts | Currency, caps, match funding % |
| Process | Online form vs email — still correct? |

**Example failure:** AI says “rolling deadline” — source says “closed 2025-03-31.”

#### Policy & government

| Check | Legal vs plain summary |
|-------|------------------------|
| § numbering | Matches official gazette |
| Mandatory vs optional | “Must” not softened to “should” |
| Definitions | Terms of art unchanged |

#### Research & literature

| Check | Action |
|-------|--------|
| DOI / URL | Click — exists? |
| Numbers | Compare abstract vs AI table |
| Novelty claims | Not overstated beyond paper |

#### Patents & IP

| Check | Action |
|-------|--------|
| Prior art | AI must not invent references |
| Legal status | “Draft for attorney” watermark |
| Claims language | Never auto-file |

### 4.3 Citation-level verification (RAG)

```
AI: "The maximum duration is 36 months [Source 3]."

Verifier opens Source 3:
  ├─ Quote present?        YES / NO → quality bug
  ├─ Context changes meaning?  e.g. "except pilot track"
  └─ Source still current?     check effective_to metadata
```

### 4.4 When to reject AI output entirely

- No sources for factual claims (T2+)  
- Conflicting sources unresolved  
- Suspected injection or off-policy content  
- Obvious numeric or legal error  
- Wrong language or audience  

**Principle:** Rejection is **good process**, not failure.

---

## 5. Human-in-the-Loop Workflows (15 min)

### 5.1 Standard publish workflow (T2)

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  User    │───►│  AI+RAG  │───►│ Verifier │───►│ Approver │
│  request │    │  draft   │    │ (staff)  │    │ (named)  │
└──────────┘    └──────────┘    └──────────┘    └────┬─────┘
                                                      │
                                                      ▼
                                               Publish / send
```

**Segregation:** Verifier and approver may be same person for T1; **different** for T2 minister-facing content (policy).

### 5.2 Decision workflow (T3)

```
AI output ──► labeled "informational only"
                    │
                    ▼
            Decision maker + file record
            (AI not cited as authority)
```

### 5.3 Diagram — escalation

```
Verifier unsure?
      │
      ├─► Source ambiguous ──► Domain expert
      ├─► Legal wording ──► Legal counsel
      ├─► Systematic wrong answers ──► Dev + corpus owner ([10](10_evaluation_quality.md))
      └─► Safety / PII ──► Security ([09](09_privacy_security.md))
```

### 5.4 Audit trail (what to keep)

| Record | Retention per policy |
|--------|---------------------|
| Prompt versions | Yes |
| Retrieved source IDs | Yes |
| AI draft + human edits | T2+ |
| Approver identity & timestamp | T2+ |
| Published final | Permanent |

---

## 6. Overconfidence & User Psychology (8 min)

### 6.1 Why people over-trust AI

| Bias | Counter-message |
|------|-----------------|
| Fluency = truth | “Smooth writing is not verification” |
| Authority transfer | “The organization approves **people**, not the model” |
| Automation bias | “Check the citation, not the tone” |
| Time pressure | “Skipping VERIFY costs more later” |

### 6.2 Training messages for staff

**Say:**

- “Use AI to **speed the first draft**, not to **skip reading the source**.”  
- “If you cannot verify a claim, **do not send it**.”

**Do not say:**

- “The AI checked it.”  
- “It’s in the system so it must be right.”

---

## 7. Activity — Tier & VERIFY (15 min)

| # | Scenario | Assign tier | Key verify step |
|---|----------|-------------|-----------------|
| 1 | Brainstorm exhibition themes | T0 | None required |
| 2 | Internal Slack summary of meeting | T1 | Attendees confirm facts |
| 3 | Public website FAQ on grant deadlines | T2 | Open official circular |
| 4 | Email to applicant: “you are eligible” | T3 | Officer decision + file |
| 5 | AI cites §5.2 but text is from §5.1 | T2 fail | Citation-level check |
| 6 | Summary of Nature paper for director | T2 | DOI + key figure |

**Debrief:** Same AI output, different tier → different human duty.

---

## 8. Role Responsibilities

| Role | Trust duties |
|------|--------------|
| **Staff** | VERIFY before share; never upgrade tier without approver |
| **Developers** | Cite-only prompts; insufficient-info path; log versions |
| **Operators** | Stable models; incident comms when quality degrades |
| **Support** | Do not assure accuracy; escalate quality tickets |
| **Leadership** | Tier policy; resources for review time |
| **Legal / IP** | T3 boundaries for policy and patents |

---

## 9. Q&A

**Q: 99% accurate — good enough for public FAQ?**  
A: Define “accurate” with eval ([10](10_evaluation_quality.md)). One wrong deadline can dominate harm. T2 needs process, not just %.

**Q: Can we display confidence scores?**  
A: Only if calibrated — raw model “confidence” is often misleading. Prefer citations + human tier.

**Q: RAG fixed hallucination?**  
A: Reduces; does not eliminate. Model can misread or ignore chunks.

---

## 10. Slide Outline (20 slides)

1. Title  
2. Definitions table  
3. Fluent → verified ladder  
4. Why hallucination happens  
5. Risk tiers T0–T3  
6. Task mapping examples  
7. VERIFY checklist  
8. Funding checks  
9. Policy / research / patent checks  
10. Citation verification  
11. Publish workflow diagram  
12. T3 decision workflow  
13. Escalation diagram  
14. Psychology & over-trust  
15. Audit trail  
16. Activity  
17. Role matrix  
18. Takeaways  
19. Handout VERIFY card  
20. Next — privacy & security  

---

## 11. Takeaways

1. **Fluent ≠ true** — grounding and verification are separate steps.  
2. **Tier every task** — T0 explore to T3 human decision.  
3. **VERIFY** sources, dates, numbers, scope, conflicts.  
4. **Named approvers** for T2; AI never decides T3.  
5. **Next:** [09_privacy_security.md](09_privacy_security.md) — protecting data while using AI.

---

## 12. Printable VERIFY Card

```
□ View cited source (not summary only)
□ Version / effective date current
□ Numbers & dates match source
□ Exclusions and scope included
□ Conflicts escalated
□ Tier-appropriate approver signed (T2+)
```
