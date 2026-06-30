# Serving Customers, Support Operations & Team Action Plan

*For all members — support, staff, product, leadership, developers, operators*  
*SLAs, incident response, customer messaging, and putting the full curriculum into practice*

---

## Module Overview

This is **Lecture 12** — the **capstone**. It connects Lectures 4–11 into **how the organization serves users daily**: honest promises, support playbooks, incidents, cross-team coordination, and a **concrete team action plan**.

| Lecture | Topic |
|---------|--------|
| 7–11 | Prompting → trust → security → eval → agents |
| **12 (this)** | **Serving customers & action plan** |

**Duration:** 90 minutes (includes workshop time)  
**Prerequisites:** All prior technical lectures recommended; minimum 04, 06, 07, 08, 09

---

## Learning Objectives

By the end, members should be able to:

1. State **what to promise** and **what never to promise** about AI features  
2. Run **support triage** for quality, outage, security, and policy issues  
3. Execute **incident communication** templates for users and leadership  
4. Complete a **team action plan** with owners and timelines  
5. Collaborate across roles using **shared vocabulary** from the series  

---

## 1. Opening (5 min)

**Facilitator message:**

> “Users experience one product — not separate ‘LLM,’ ‘RAG,’ and ‘GPU’ teams. Support and staff are the **face of trust**. This session makes responsibilities explicit and ends with **your team’s plan**.”

**Icebreaker:** One good and one bad AI support experience you’ve had as a customer anywhere.

---

## 2. What Customers & Stakeholders Experience (12 min)

### 2.1 User journey map

```
Discover ──► Try ──► Trust? ──► Use in work ──► Hit problem ──► Report / leave
   │           │         │            │              │
 Marketing   Onboarding  Citations   Daily tasks    Support
 (honest)    (tier rules) (VERIFY)   (templates)    (this lecture)
```

### 2.2 Promise framework (public & internal)

| We **can** promise | We **cannot** promise |
|--------------------|----------------------|
| AI-assisted responses with **shown sources** when found | 100% accuracy |
| Search of **approved** document sets | Knowledge of unpublished decisions |
| Stated **update cadence** for corpus | Instant legal advice |
| **Human escalation** path | Replacement for expert judgment (T3) |
| **Privacy** per published policy | “Nothing you type is ever stored” (unless true) |

### 2.3 Messaging by audience

| Audience | Emphasis |
|----------|----------|
| **Citizens / external** | Verify citations; official channels for decisions |
| **Researchers** | Draft + DOI check; not journal submission ready |
| **Industry partners** | Data handling + NDA alignment |
| **Internal staff** | Tier rules, classification, approved tools |
| **Leadership** | Risk tiers, incidents, resource needs |

### 2.4 Sample public FAQ entries

**Q: Is the AI official?**  
A: It helps you find information in published [Organization] documents. **Official decisions** are made by authorized officers and published through formal channels.

**Q: Why did it give a wrong date?**  
A: AI can err. Always check the cited document. Report errors via [Feedback] so we improve sources and systems.

**Q: Is my chat private?**  
A: [Link to privacy policy — retention, local processing, who can access logs.]

---

## 3. Support Operations — Triage & Playbooks (20 min)

### 3.1 Ticket taxonomy

| Category | Code | First owner | Examples |
|----------|------|-------------|----------|
| **Outage** | AI-OUT | Ops | 500 errors, timeout all users |
| **Performance** | AI-PERF | Ops | Slow > SLA |
| **Quality — wrong answer** | AI-QUAL | Dev + domain | Wrong fact with citation |
| **Quality — no source** | AI-RAG | Dev + corpus owner | Should have found doc |
| **Security / privacy** | AI-SEC | Security | Leak suspicion, injection |
| **Policy / misuse** | AI-POL | Product + HR | PII paste, abuse |
| **User education** | AI-EDU | Support | How to prompt, tier confusion |
| **Feature request** | AI-FEAT | Product | New corpus, language |

### 3.2 Triage flowchart

```
Ticket arrives
      │
      ├─ System down for many users? ──YES──► AI-OUT → ops incident
      │
      ├─ Data leak / classified paste? ──YES──► AI-SEC → security NOW
      │
      ├─ Wrong fact on published page? ──YES──► AI-QUAL + freeze edit?
      │
      ├─ "Too slow" ──► AI-PERF (check status page)
      │
      └─ "How do I…?" ──► AI-EDU (template + link Lecture 7)
```

### 3.3 What support should collect (minimum)

| Field | Notes |
|-------|-------|
| Time (timezone) | |
| Feature / assistant name | |
| Screenshot (redact PII) | |
| Session / request ID | From UI about box |
| User action steps | |
| **Do not** ask for classified content in ticket | Reproduce in secure env |

### 3.4 Response templates

**Outage (initial):**

> We are aware of issues affecting [AI Assistant Name]. Engineers are investigating. Status: [URL]. Workaround: [manual process / retry later]. Next update in 60 minutes.

**Quality — wrong answer:**

> Thank you for reporting this. We take factual accuracy seriously. We have logged session [ID] for review. Please rely on the cited source document until we confirm a fix. For urgent decisions, contact [human channel].

**User education — verification:**

> The assistant provides drafts and citations from approved documents. For official use, please follow your team’s verification steps ([VERIFY checklist](08_trust_verification.md)).

**Security:**

> Do not paste further sensitive data in chat. We are escalating to our security team. Reference: [INC-####].

### 3.5 Escalation matrix

| Severity | Definition | Notify |
|----------|------------|--------|
| **S1** | Outage or confirmed leak | Ops + security + leadership |
| **S2** | Widespread wrong T2 content | Product + domain + dev |
| **S3** | Single user quality issue | Dev backlog |
| **S4** | How-to | Support resolves |

---

## 4. Incident Management (15 min)

### 4.1 Incident lifecycle

```
Detect ──► Triage ──► Contain ──► Fix ──► Communicate ──► Post-mortem
  │          │          │          │           │              │
monitor   support    disable?   deploy      users         golden case
users     severity   hotfix     eval        leadership    process fix
```

### 4.2 Common incidents & owners

| Incident | Contain | Fix | Comms |
|----------|---------|-----|-------|
| GPU OOM storm | Rate limit | Ops scale / restart | Status page |
| Bad model upgrade | Rollback | Ops + eval fail ([10](10_evaluation_quality.md)) | “Restored previous version” |
| Stale index after policy | Banner warning | Priority reindex | “Check official PDF until HH:MM” |
| ACL leak | Disable collection | Security + dev | S1 notification per policy |
| Viral wrong FAQ answer | Unpublish page | Human correct + root cause | Correction notice |

### 4.3 Post-mortem template (blameless)

```markdown
## Incident AI-YYYY-MM-DD-##

**Impact:** who, how long, what tier
**Timeline:** detect → resolve
**Root cause:** technical + process
**What went well:**
**What to improve:**
**Action items:** owner + due date
  - [ ] New golden case ID:
  - [ ] Runbook update:
  - [ ] Support script update:
```

### 4.4 Coordinating dev, ops, staff

```
┌────────────┐     ┌────────────┐     ┌────────────┐
│  Support   │────►│  Product   │────►│  Dev/Ops   │
│  tickets   │     │  prioritize│     │  fix       │
└────────────┘     └─────┬──────┘     └────────────┘
                          │
                          ▼
                   ┌────────────┐
                   │  Staff /   │
                   │  corpus    │
                   │  owners    │
                   └────────────┘
```

**Staff role in incidents:** Confirm which document version is canonical; fix source PDFs — not only “retrain AI.”

---

## 5. SLAs & Operating Cadence (10 min)

### 5.1 Example internal SLA table (customize)

| Metric | Pilot | Production |
|--------|-------|------------|
| Monthly availability | Best effort | 99.0% |
| p95 latency (RAG+LLM) | &lt; 15s | &lt; 8s |
| Corpus update after publish | 48h | 4h |
| S1 ack time | — | 15 min |
| Quality ticket first response | 2 business days | 1 business day |

**Leadership:** SLAs must match [hardware reality](05_local_llm.md) — do not over-promise.

### 5.2 Rhythms

| Cadence | Meeting | Attendees |
|---------|---------|-----------|
| Weekly | AI ops review | Ops, dev, support lead |
| Monthly | Quality sample | Domain staff + product |
| Quarterly | Steering | Leadership + governance ([09](09_privacy_security.md)) |
| Per release | Eval gate ([10](10_evaluation_quality.md)) | Dev + domain |

---

## 6. Cross-Role Operating Model (10 min)

### 6.1 RACI summary (AI assistant product)

| Activity | R | A | C | I |
|----------|---|---|---|---|
| Public promise wording | Product | Leadership | Legal, Support | All |
| System prompt change | Dev | Product | Domain, Security | Support |
| Corpus update | Staff owner | Domain lead | Dev | Support |
| Model upgrade | Ops | Dev lead | Product, Security | Support |
| Tier T2 approval workflow | Staff approver | Domain lead | Product | Leadership |
| Incident comms to users | Support | Product | Ops, Security | Leadership |
| Golden set maintenance | Domain | Dev | Support | Product |

*R=Responsible, A=Accountable, C=Consulted, I=Informed*

### 6.2 Vocabulary cheat sheet (series recap)

| Term | One line |
|------|----------|
| LLM | Predicts text — not truth ([04](04_genai_llm.md)) |
| RAG | Retrieve docs then generate ([06](06_rag.md)) |
| Token | Text unit for model ([04](04_genai_llm.md)) |
| Embedding | Vector for search ([06](06_rag.md)) |
| Tier T2 | Human approve before publish ([08](08_trust_verification.md)) |
| Golden set | Regression tests ([10](10_evaluation_quality.md)) |
| Tool | Live API call ([11](11_agents_tools.md)) |

---

## 7. Workshop — Team Action Plan (20 min)

### 7.1 Template (complete in session)

**Team / department:** ___________________  
**Date:** ___________________

#### A. Use cases we will adopt in 90 days

| Use case | Tier | Assistant / workflow | Owner | Target date |
|----------|------|----------------------|-------|-------------|
| | T0–T3 | | | |
| | | | | |

#### B. Use cases we will **not** automate (T3)

| Decision / task | Human owner only |
|-----------------|------------------|
| | |

#### C. Skills & training gaps

| Role | Lecture to complete | By when |
|------|---------------------|---------|
| | 07 / 08 / … | |

#### D. Corpus & knowledge duties

| Document set | Canonical owner | Refresh SLA |
|--------------|-----------------|-------------|
| | | |

#### E. Support & escalation

| Our support contact | Escalation to platform team |
|---------------------|----------------------------|
| | |

#### F. Success measures (3 months)

| Metric | Baseline | Target |
|--------|----------|--------|
| e.g. VERIFY compliance spot-check | | |
| e.g. Quality tickets / week | | |
| e.g. Time saved on drafts (survey) | | |

#### G. Risks we accept vs mitigate

| Risk | Accept / Mitigate | Action |
|------|-------------------|--------|
| Hallucination on T1 internal | Mitigate | VERIFY training |
| | | |

---

## 8. Series Capstone Diagram

```
                         USERS / STAKEHOLDERS
                                  │
                    ┌─────────────┴─────────────┐
                    │   L12: Serve + support    │
                    │   promises, triage, SLAs  │
                    └─────────────┬─────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
   ┌───────────┐          ┌───────────┐          ┌───────────┐
   │ L07 Prompt│          │ L08 Trust │          │ L09 Sec   │
   └─────┬─────┘          └─────┬─────┘          └─────┬─────┘
         │                      │                      │
         └──────────────────────┼──────────────────────┘
                                ▼
              ┌─────────────────────────────────┐
              │ L06 RAG │ L11 Agents │ L10 Eval │
              └─────────────────┬───────────────┘
                                ▼
              ┌─────────────────────────────────┐
              │ L04 LLM │ L05 Local hardware    │
              └─────────────────────────────────┘
                                │
                         Infrastructure
```

---

## 9. Graduation — What “AI Literate” Means Here

Members who completed the technical track should:

| Capability | Evidence |
|------------|----------|
| Explain stack to a colleague | Draw diagram from memory |
| Use assistants safely | Classification + VERIFY |
| Improve the system | Golden case contribution |
| Handle incidents calmly | Triage codes + templates |
| Collaborate cross-role | Shared RACI vocabulary |

**Optional certification:** Facilitator signs attendance + action plan submitted.

---

## 10. Q&A

**Q: Who speaks to media about our AI?**  
A: Designated comms only — aligned with promise framework §2.2.

**Q: User threatens legal action over wrong answer?**  
A: Escalate leadership + legal; preserve logs per policy; no ad hoc blame.

**Q: After Lecture 12, what’s next?**  
A: Post-launch hands-on module; quarterly refresh; corpus workshops ([CURRICULUM.md](CURRICULUM.md)).

---

## 11. Slide Outline (22 slides)

1. Title — capstone  
2. User journey  
3. Promise / no-promise table  
4. Audience messaging  
5. Public FAQ samples  
6. Ticket taxonomy  
7. Triage flowchart  
8. Collection checklist  
9. Response templates  
10. Escalation severity  
11. Incident lifecycle  
12. Post-mortem template  
13. Cross-team diagram  
14. SLA table  
15. Operating cadence  
16. RACI summary  
17. Vocabulary recap  
18. Action plan workshop  
19. Capstone diagram  
20. Graduation criteria  
21. Takeaways  
22. Thank you + CURRICULUM link  

---

## 12. Takeaways

1. **Support is trust delivery** — triage, templates, no false certainty.  
2. **Incidents** need contain → fix → communicate → post-mortem → new golden case.  
3. **SLAs** must match infrastructure and review capacity.  
4. **Action plan** turns 12 lectures into **owned work** in your team.  
5. **Curriculum continues** — post-launch hands-on, quarterly eval refresh.

---

## 13. Printable — Support Quick Reference Card

```
TRIAGE: OUT | PERF | QUAL | RAG | SEC | POL | EDU

COLLECT: time, assistant, session ID, steps (no classified text)

NEVER PROMISE: 100% accurate, legal decisions, all-knowing AI

ALWAYS: cite verify path, human channel for T3

ESCALATE SEC + OUT immediately
```

---

## 14. Where This Fits

| File | Role |
|------|------|
| [CURRICULUM.md](CURRICULUM.md) | Master plan & facilitator todos |
| [04](04_genai_llm.md)–[11](11_agents_tools.md) | Technical foundation |
| **12 (this)** | Operations, support, team commitment |

**Congratulations** — completing 04–12 means your organization shares a **full-stack AI literacy baseline** for building and serving responsibly.
