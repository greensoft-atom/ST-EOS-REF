# Slide Deck — Trust, Accuracy & Human Judgment

*Source: [08_trust_verification.md](../08_trust_verification.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Trust, Accuracy & Human Judgment**

*Lecture 8 — ST-EOS-REF*

For all members: developers, operators, staff, support, leadership

**Duration:** 90 minutes

**Prerequisites:** LLM (Lecture 4), RAG (Lecture 6), Prompting (Lecture 7)

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Define **hallucination, grounding, staleness**, and **overconfidence** |
| 2 | Apply a **risk tier** model (T0–T3) |
| 3 | Execute **verification checklists** by domain |
| 4 | Design workflows where AI drafts and **humans approve** |
| 5 | Communicate limits honestly to colleagues and customers |

## Speaker notes

Open with the facilitator message: trust is not "the AI seems smart." Trust is traceable evidence plus accountable human for anything that matters externally or legally.

Poll (2 min): have you trusted a fluent paragraph without checking? What would have gone wrong if it were published? This session gives the org vocabulary and process to prevent that.

---

# Slide 02 | Definitions

## On screen

**Use these terms consistently across the org**

| Term | Definition | Example |
|------|------------|---------|
| **Hallucination** | Confident output **not supported** by facts or sources | Fake DOI, invented grant clause |
| **Ungrounded** | No citation or source when one was required | Policy paraphrase with no § reference |
| **Stale** | Was true; corpus or training outdated | 2023 deadline in 2026 answer |
| **Incomplete** | Omits critical caveat from source | "Eligible" without exclusion list |
| **Grounded** | Claims trace to retrieved or attached sources | Each bullet has [Source 2] |
| **Verified** | Human confirmed against authoritative record | Officer checked PDF §4.2 |

## Speaker notes

These six terms are the org standard — use them in tickets, reviews, and customer comms. "Hallucination" is not a catch-all for any wrong answer; distinguish stale, incomplete, and ungrounded.

Grounded is necessary but not sufficient for high-stakes work. Verified adds human accountability.

---

# Slide 03 | Fluent → Verified Ladder

## On screen

**Trust hierarchy**

```
Fluent text  →  Grounded (cited)  →  Verified (human)  →  Approved (authorized publish)
     ↑ weak              ↑ better              ↑ required for high-stakes
```

| Stage | What it means | Enough for T2 publish? |
|-------|---------------|------------------------|
| Fluent | Reads well, sounds authoritative | **No** |
| Grounded | Citations present | **No** — citations can be wrong |
| Verified | Human checked sources | Required |
| Approved | Named authority signed off | Required for external use |

**Key message:** Smooth writing is not verification.

## Speaker notes

Draw the ladder left to right if using a whiteboard. The most dangerous state is fluent + grounded-looking citations that nobody opened.

Connect to Lecture 7: cite-only prompts get you to "grounded" — this lecture gets you to "verified" and "approved."

---

# Slide 04 | Why Hallucination Happens

## On screen

**Mental model (Lecture 4):** LLMs predict **plausible** continuations — not truth lookups.

| Cause | Mitigation |
|-------|------------|
| No relevant source in context | RAG + "insufficient info" (Lecture 6) |
| Weak cite-only prompt | System rules (Lecture 7) |
| User asks for references not in corpus | Refuse or retrieve |
| Long multi-hop reasoning | Break into steps; tools (Lecture 11) |
| High temperature | Lower for factual tasks |

**Recognition AI vs generative LLM**

| | Recognition AI | Generative LLM |
|---|----------------|----------------|
| **Error feel** | Silent mislabel | Persuasive wrong prose |
| **Detection** | Metrics, thresholds | Human read + citation check |
| **User risk** | Wrong routing | Wrong belief |

**Both** need human oversight at high stakes.

## Speaker notes

Do not treat RAG as a hallucination cure-all — it reduces risk; it does not eliminate misreading or ignoring chunks. The recognition vs generative comparison helps staff who come from traditional ML backgrounds.

---

# Slide 05 | Risk Tiers T0–T3

## On screen

**Four-tier model (org standard)**

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

## Speaker notes

Leadership assigns tier per product feature — document in governance register. Every assistant and workflow should declare its default tier.

T3 is the critical boundary: AI informs, humans decide. Never auto-publish at T2+ without workflow.

---

# Slide 06 | Task Mapping Examples

## On screen

| Task | Tier | Rule |
|------|------|------|
| Summarize public paper for lab | T1 | Verify key numbers |
| Answer internal funding FAQ with RAG | T1–T2 | Citations required; T2 if customer-facing |
| Draft minister briefing | T2 | Policy owner approval |
| Auto-publish AI text to public web | **Forbidden** without T2 workflow | |
| Determine grant eligibility alone | T3 | AI may list criteria; officer decides |
| Patent claims drafting | T3 | Attorney only |

**UI messaging by tier**

| Tier | UI should show |
|------|----------------|
| T0–T1 | "AI-generated draft" |
| T2 | Citations + "Requires approval before external use" |
| T3 | "Decision support only — not a decision" |

## Speaker notes

Same AI output can map to different tiers depending on use. An internal draft summary is T1; the same text on the public website is T2 with approver.

Product owns UI copy. Developers implement tier badges. Staff must not upgrade tier without approver.

---

# Slide 07 | VERIFY Checklist

## On screen

**Universal verification — VERIFY**

| Step | Action |
|------|--------|
| **V** | **View sources** — open cited document, not just AI summary |
| **E** | **Examine dates & versions** — effective date, superseded? |
| **R** | **Reconcile numbers** — amounts, deadlines, percentages |
| **I** | **Identify scope** — does rule apply to this case? |
| **F** | **Find conflicts** — two sources disagree? escalate |
| **Y** | **Yes to approver** — only if all above pass for tier |

**Principle:** Rejection is **good process**, not failure.

## Speaker notes

VERIFY is the org handout standard — staff should run it before any T1+ share outside their immediate team, and fully before T2 publish.

Emphasize V: open the source document. AI summaries of summaries compound error.

---

# Slide 08 | Funding Checks

## On screen

**Domain-specific — funding & grants**

| Check | Question |
|-------|----------|
| Circular version | Is this the current published call? |
| Eligibility | All exclusion criteria listed? |
| Dates | Time zone, extension, grace period? |
| Amounts | Currency, caps, match funding % |
| Process | Online form vs email — still correct? |

**Example failure**

| AI says | Source says | Action |
|---------|-------------|--------|
| "Rolling deadline" | "Closed 2025-03-31" | Reject output; fix corpus or escalate |

**One wrong deadline can dominate harm** — T2 needs process, not just accuracy %.

## Speaker notes

Funding is high-stakes for citizens and applicants. Walk through the example failure — this is exactly the kind of error that sounds helpful and costs real harm.

Officers at T3 decide eligibility; AI lists criteria only.

---

# Slide 09 | Policy, Research & Patent Checks

## On screen

**Policy & government**

| Check | Action |
|-------|--------|
| § numbering | Matches official gazette |
| Mandatory vs optional | "Must" not softened to "should" |
| Definitions | Terms of art unchanged |

**Research & literature**

| Check | Action |
|-------|--------|
| DOI / URL | Click — exists? |
| Numbers | Compare abstract vs AI table |
| Novelty claims | Not overstated beyond paper |

**Patents & IP**

| Check | Action |
|-------|--------|
| Prior art | AI must not invent references |
| Legal status | "Draft for attorney" watermark |
| Claims language | **Never auto-file** |

## Speaker notes

Three domains in one slide — spend ~2 min each or focus on your audience's primary domain. Patent row is absolute: attorney only for claims and filing.

Legal vs plain summary: plain language must not change mandatory/optional meaning.

---

# Slide 10 | Citation Verification

## On screen

**Citation-level verification (RAG)**

```
AI: "The maximum duration is 36 months [Source 3]."

Verifier opens Source 3:
  ├─ Quote present?        YES / NO → quality bug
  ├─ Context changes meaning?  e.g. "except pilot track"
  └─ Source still current?     check effective_to metadata
```

**When to reject AI output entirely (T2+)**

- No sources for factual claims
- Conflicting sources unresolved
- Suspected injection or off-policy content
- Obvious numeric or legal error
- Wrong language or audience

## Speaker notes

Citation wrong section (§5.2 cited but text from §5.1) is a common RAG failure mode — activity scenario 5 covers this.

If any rejection criterion applies, do not patch and publish — reject and escalate or regenerate with fixed corpus/prompt.

---

# Slide 11 | Publish Workflow (T2)

## On screen

**Standard publish workflow**

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  User    │───►│  AI+RAG  │───►│ Verifier │───►│ Approver │
│  request │    │  draft   │    │ (staff)  │    │ (named)  │
└──────────┘    └──────────┘    └──────────┘    └────┬─────┘
                                                      │
                                                      ▼
                                               Publish / send
```

**Segregation**

| Tier | Verifier vs approver |
|------|---------------------|
| T1 | May be same person |
| T2 minister-facing | **Different** people (policy) |

## Speaker notes

Walk the workflow left to right. No step may be skipped for T2 external content. Auto-publish without this chain is forbidden per tier policy.

Verifier runs VERIFY; approver accepts accountability for publish.

---

# Slide 12 | Decision Workflow (T3)

## On screen

**T3 — human decision only**

```
AI output ──► labeled "informational only"
                    │
                    ▼
            Decision maker + file record
            (AI not cited as authority)
```

| T3 examples | AI may | AI may NOT |
|-------------|--------|------------|
| Grant eligibility | List criteria from sources | Say "you are eligible" |
| Patent filing | Draft background for attorney | File claims |
| Funding award | Summarize application | Approve award |

**UI label:** "Decision support only — not a decision"

## Speaker notes

T3 is where legal and IP boundaries live. The decision maker maintains the official record; AI output is not cited as authority in the decision file.

Connect to Lecture 7 patent example: [VERIFY] marks are for attorney review, not for skipping T3.

---

# Slide 13 | Escalation Diagram

## On screen

```
Verifier unsure?
      │
      ├─► Source ambiguous ──► Domain expert
      ├─► Legal wording ──► Legal counsel
      ├─► Systematic wrong answers ──► Dev + corpus owner (Lecture 10)
      └─► Safety / PII ──► Security (Lecture 9)
```

**When in doubt, escalate early** — do not publish uncertain content at T2.

| Escalation type | Typical owner |
|-----------------|---------------|
| Domain ambiguity | Program officer, research lead |
| Legal | Counsel |
| Quality regression | Dev + eval |
| Data incident | Security + DPO |

## Speaker notes

Give staff permission to escalate without shame. "I'm not sure" at T2 is a success, not a failure.

Systematic wrong answers become eval cases and may trigger model or corpus changes — not more prompt rephrasing.

---

# Slide 14 | Psychology & Over-Trust

## On screen

**Why people over-trust AI**

| Bias | Counter-message |
|------|-----------------|
| Fluency = truth | "Smooth writing is not verification" |
| Authority transfer | "The organization approves **people**, not the model" |
| Automation bias | "Check the citation, not the tone" |
| Time pressure | "Skipping VERIFY costs more later" |

**Say**

- "Use AI to **speed the first draft**, not to **skip reading the source**."
- "If you cannot verify a claim, **do not send it**."

**Do not say**

- "The AI checked it."
- "It's in the system so it must be right."

## Speaker notes

Support and leadership messaging matters as much as technical controls. Train staff to counter fluency bias explicitly.

Q&A prep: raw model confidence scores are often misleading — prefer citations + human tier over fake certainty.

---

# Slide 15 | Audit Trail

## On screen

**What to keep (retention per policy)**

| Record | Retention |
|--------|-----------|
| Prompt versions | Yes |
| Retrieved source IDs | Yes |
| AI draft + human edits | T2+ |
| Approver identity & timestamp | T2+ |
| Published final | Permanent |

**Supports:** incident response, reproducibility, accountability, eval (Lecture 10)

```
Request → prompt_bundle + sources → draft → edits → approver → published
         └──────────── all logged where tier requires ────────────┘
```

## Speaker notes

Developers and ops implement logging; governance sets retention. T2+ requires enough trail to answer "who approved this and on what basis?"

Link forward to Lecture 9 — logs containing PII need classification and redaction policies.

---

# Slide 16 | Activity — Tier & VERIFY

## On screen

**START ACTIVITY — 15 minutes**

| # | Scenario | Assign tier | Key verify step |
|---|----------|-------------|-----------------|
| 1 | Brainstorm exhibition themes | ? | ? |
| 2 | Internal Slack summary of meeting | ? | ? |
| 3 | Public website FAQ on grant deadlines | ? | ? |
| 4 | Email to applicant: "you are eligible" | ? | ? |
| 5 | AI cites §5.2 but text is from §5.1 | ? | ? |
| 6 | Summary of Nature paper for director | ? | ? |

**Answers in debrief** — discuss before revealing.

**Debrief:** Same AI output, different tier → different human duty.

## Speaker notes

Groups assign tier and name one VERIFY step per row. Reveal answers: 1=T0, 2=T1, 3=T2, 4=T3, 5=T2 fail (citation check), 6=T2 (DOI + key figure).

Debrief point: scenario 4 is T3 even if AI wrote a perfect email — eligibility is a human decision. Full activity in lecture doc §7.

---

# Slide 17 | Role Matrix

## On screen

| Role | Trust duties |
|------|--------------|
| **Staff** | VERIFY before share; never upgrade tier without approver |
| **Developers** | Cite-only prompts; insufficient-info path; log versions |
| **Operators** | Stable models; incident comms when quality degrades |
| **Support** | Do not assure accuracy; escalate quality tickets |
| **Leadership** | Tier policy; resources for review time |
| **Legal / IP** | T3 boundaries for policy and patents |

## Speaker notes

Support must not tell customers "the AI checked it." Leadership must fund review time — T2 without approver capacity is policy fiction.

Legal/IP own T3 boundaries — especially patents and mandatory policy language.

---

# Slide 18 | Takeaways

## On screen

1. **Fluent ≠ true** — grounding and verification are separate steps.
2. **Tier every task** — T0 explore to T3 human decision.
3. **VERIFY** sources, dates, numbers, scope, conflicts.
4. **Named approvers** for T2; AI never decides T3.
5. **Rejection is good process** — do not publish uncertain output.

**Q&A highlights**

| Question | Short answer |
|----------|--------------|
| 99% accurate enough for public FAQ? | One wrong deadline dominates harm; T2 needs process + eval |
| Display confidence scores? | Only if calibrated; prefer citations + tier |
| RAG fixed hallucination? | Reduces; does not eliminate misread/ignore |

## Speaker notes

Recap five takeaways. Open brief Q&A using prepared answers from lecture doc §9.

Remind room: eval (Lecture 10) defines "accurate" with metrics — not gut feel.

---

# Slide 19 | Handout — VERIFY Card

## On screen

**Printable VERIFY Card**

```
□ View cited source (not summary only)
□ Version / effective date current
□ Numbers & dates match source
□ Exclusions and scope included
□ Conflicts escalated
□ Tier-appropriate approver signed (T2+)
```

**How to use**

| When | Action |
|------|--------|
| Before sharing T1+ outside team | Run full checklist |
| Before T2 publish | All boxes + named approver |
| T3 decision | AI informational only; human record separate |

*Pin in Slack, print at desk, embed in ticket template.*

## Speaker notes

Offer as handout or digital template. This is the portable artifact from the lecture — staff should not need to remember the acronym under time pressure.

Pair with Lecture 7 prompt template card: prompt well → verify always.

---

# Slide 20 | Next — Privacy & Security

## On screen

**Lecture 9: Privacy, Security & AI Governance**

| Today (Lecture 8) | Next (Lecture 9) |
|-------------------|------------------|
| Trust through verification | Trust through **controls** |
| Tiers, VERIFY, approvers | Classification, injection, ACL |
| Human-in-the-loop | Governance, incident response |

**Bridge question:** You verified content — but did you paste **classified data** into the prompt to get it?

→ Verification without security still fails. See you in Lecture 9.

*Source: [09_privacy_security.md](../09_privacy_security.md)*

## Speaker notes

Close with the bridge: a perfectly verified answer built from a P2 data leak is still an incident. Privacy and security are the next layer.

Preview: prompt injection, RAG ACL, shadow IT, governance structure. Point to Lecture 10 for measuring whether controls work.

---
