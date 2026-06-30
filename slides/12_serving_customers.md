# Slide 01 | Capstone — Serving Customers

## On screen

**Lecture 12 — Serving Customers, Support Operations & Team Action Plan**

*SLAs · incident response · customer messaging · curriculum in practice*

**Capstone:** connects Lectures 4–11 into **daily operations**

> Users experience **one product** — not separate “LLM,” “RAG,” and “GPU” teams.  
> Support and staff are the **face of trust**.

**Duration:** 90 minutes (includes workshop)

---

## Speaker notes

Welcome to final technical-operational lecture. Icebreaker: one good and one bad AI support experience as a customer anywhere — 2 min pairs. Prerequisites: ideally full 04–11; minimum 04, 06, 07, 08, 09. Today ends with **your team’s action plan** — not only slides. Facilitator energy: graduation tone, concrete commitments.

---

# Slide 02 | User Journey

## On screen

```
Discover ──► Try ──► Trust? ──► Use in work ──► Hit problem ──► Report / leave
   │           │         │            │              │
 Marketing   Onboarding  Citations   Daily tasks    Support
 (honest)    (tier rules) (VERIFY)   (templates)    (this lecture)
```

| Stage | What breaks trust |
|-------|-------------------|
| Discover | Over-promising in marketing |
| Try | No sources shown |
| Trust? | One wrong cited date |
| Problem | Slow, vague, or blamey support |

**Support owns the last mile of every prior lecture.**

---

## Speaker notes

Walk left to right slowly. Marketing honesty (Slide 03) sets trap if product shows citations but support denies errors. Onboarding tier rules from Lecture 08 — users must know T1 vs T3. VERIFY at daily use. When problem hits, support response determines retain vs leave. Dev/Ops invisible — user sees one assistant name.

---

# Slide 03 | Promise Framework

## On screen

| We **can** promise | We **cannot** promise |
|--------------------|----------------------|
| AI-assisted responses with **shown sources** when found | 100% accuracy |
| Search of **approved** document sets | Knowledge of unpublished decisions |
| Stated **update cadence** for corpus | Instant legal advice |
| **Human escalation** path | Replacement for expert judgment (T3) |
| **Privacy** per published policy | “Nothing you type is ever stored” (unless true) |

```
CAN = verifiable, documented
CANNOT = requires fortune-telling or human authority
```

---

## Speaker notes

Product + leadership + legal own “can” column — must match actual system. “Shown sources when found” — honest qualifier when retrieval empty. Corpus cadence: e.g. “within 4h of published PDF.” Never promise zero retention unless architecture truly local and policy says so (Lecture 05, 09). Public FAQ and internal talking points must align — no rogue optimism.

---

# Slide 04 | Messaging by Audience

## On screen

| Audience | Emphasis |
|----------|----------|
| **Citizens / external** | Verify citations; official channels for decisions |
| **Researchers** | Draft + DOI check; not journal-ready |
| **Industry partners** | Data handling + NDA alignment |
| **Internal staff** | Tier rules, classification, approved tools |
| **Leadership** | Risk tiers, incidents, resource needs |

**One assistant — five different legitimate expectations**

---

## Speaker notes

Same UI, different risk profiles. External: never imply AI decision is official. Researchers: hallucinated DOI disaster — cite VERIFY. Partners: contract data classes. Internal: classification before paste (Lecture 09). Leadership: incident severity and SLA reality — not feature hype. Comms speaks externally only through designated spokes (Q&A slide 22).

---

# Slide 05 | Public FAQ Samples

## On screen

**Q: Is the AI official?**  
A: It helps you find information in published [Organization] documents. **Official decisions** are made by authorized officers through formal channels.

**Q: Why did it give a wrong date?**  
A: AI can err. Always check the cited document. Report via [Feedback] so we improve sources and systems.

**Q: Is my chat private?**  
A: [Link to privacy policy — retention, local processing, log access.]

**Customize bracketed fields before publish.**

---

## Speaker notes

Read aloud — tone matters: calm, non-defensive. Wrong date answer trains VERIFY without blaming user. Privacy answer must link real policy — never wing it. Support should use these verbatim where possible. Update FAQ after incidents (correction notice pattern, Slide 11). Legal review before public web publish.

---

# Slide 06 | Ticket Taxonomy

## On screen

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

**Printable quick ref:** `OUT | PERF | QUAL | RAG | SEC | POL | EDU | FEAT`

---

## Speaker notes

Consistent codes in ticket system — enables metrics (AI-QUAL per week). First owner ≠ only owner — QUAL pulls domain for canonical doc check. AI-RAG often corpus/index fix, not model swap. AI-SEC immediate path — no standard troubleshooting script. Train support to tag every AI ticket Day 1.

---

# Slide 07 | Triage Flowchart

## On screen

```
Ticket arrives
      │
      ├─ System down for many users? ──YES──► AI-OUT → ops incident
      │
      ├─ Data leak / classified paste? ──YES──► AI-SEC → security NOW
      │
      ├─ Wrong fact on published page? ──YES──► AI-QUAL (+ freeze edit?)
      │
      ├─ "Too slow" ──► AI-PERF (check status page)
      │
      └─ "How do I…?" ──► AI-EDU (template + Lecture 7 link)
```

**Escalate SEC + OUT immediately — no deep troubleshooting first**

---

## Speaker notes

Decision tree for L1 support — laminate card (printable in lecture doc). “Many users” threshold: define numerically (e.g. >5 in 15 min or status probe fail). Classified paste: user education + SEC — never ask reproduce classified in ticket. Wrong public page: consider unpublish/freeze while investigating (Slide 11). EDU links prompting lecture — reduce repeat tickets.

---

# Slide 08 | What Support Must Collect

## On screen

| Field | Notes |
|-------|-------|
| Time (timezone) | |
| Feature / assistant name | |
| Screenshot (redact PII) | |
| Session / request ID | From UI about box |
| User action steps | |
| **Do not** ask for classified content in ticket | Reproduce in secure env |

```
Minimum bundle → dev can replay without another user ping
```

**Version bundle** (Lecture 10) from logs if available: model, index, prompt version

---

## Speaker notes

Session ID is critical — ties to audit log and eval replay. Teach users where ID appears in UI. Support scripts: “Please redact national ID, account numbers.” Classified reproduction only in secure lab with security present. Missing fields = S3 backlog churn. Ops: ensure ID in logs matches UI display.

---

# Slide 09 | Response Templates

## On screen

**Outage (initial)**

> We are aware of issues affecting [AI Assistant Name]. Engineers are investigating. Status: [URL]. Workaround: [manual process]. Next update in 60 minutes.

**Quality — wrong answer**

> Thank you for reporting. We logged session [ID]. Please rely on the cited source until we confirm a fix. For urgent decisions: [human channel].

**User education**

> The assistant provides drafts and citations from approved documents. For official use, follow [VERIFY checklist].

**Security**

> Do not paste further sensitive data. Escalating to security. Reference: [INC-####].

---

## Speaker notes

Templates reduce ad hoc promises under pressure. Outage: commit update cadence — then meet it. Quality: validate user feelings without admitting legal liability — adjust per legal guidance. Security: short, directive, incident number. Customize brackets before go-live. Review templates quarterly after post-mortems.

---

# Slide 10 | Escalation Severity

## On screen

| Severity | Definition | Notify |
|----------|------------|--------|
| **S1** | Outage or confirmed leak | Ops + security + leadership |
| **S2** | Widespread wrong T2 content | Product + domain + dev |
| **S3** | Single user quality issue | Dev backlog |
| **S4** | How-to | Support resolves |

```
S1 ──► wake people up
S2 ──► same day war room
S3 ──► prioritized backlog
S4 ──► template + close
```

**Mis-triage S1 as S4 = career-limiting event**

---

## Speaker notes

Define S1/S2 in writing with examples from your org. Confirmed leak always S1 — even “small.” Widespread wrong T2: public FAQ wrong rate — S2 with comms. Single wrong answer with cite: often S3 unless high visibility minister question → S2. Support authority to bump severity without manager approval for SEC/OUT.

---

# Slide 11 | Incident Lifecycle

## On screen

```
Detect ──► Triage ──► Contain ──► Fix ──► Communicate ──► Post-mortem
  │          │          │          │           │              │
monitor   support    disable?   deploy      users         golden case
users     severity   hotfix     eval        leadership    process fix
```

| Incident | Contain | Fix |
|----------|---------|-----|
| GPU OOM storm | Rate limit | Scale / restart |
| Bad model upgrade | Rollback | Eval fail (L10) |
| Stale index | Banner warning | Priority reindex |
| ACL leak | Disable collection | Security + dev |
| Viral wrong FAQ | Unpublish page | Human correct + root cause |

---

## Speaker notes

Contain before root cause — rollback, disable feature, banner. Bad model: Lecture 10 rollback triggers. Stale index: honest banner “verify official PDF until HH:MM.” ACL leak: disable first, notify per policy. Viral wrong FAQ: comms correction notice — not silent fix. Every post-mortem → golden case + runbook update.

---

# Slide 12 | Post-Mortem Template

## On screen

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

**Blameless — fix systems, not scapegoats**

---

## Speaker notes

Blameless culture required or post-mortems lie. Action items must have owner + date — empty “improve monitoring” fails. Golden case ID links to Lecture 10. Support script update: if users confused, template gap. Leadership reads S1/S2 post-mortems within 72h. Store alongside version bundle.

---

# Slide 13 | Cross-Team Coordination

## On screen

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

**Staff in incidents:** confirm **canonical document version** — fix source PDFs, not only “retrain AI”

---

## Speaker notes

Support is sensor network — patterns in tickets drive priority. Product filters noise vs systemic. Dev/Ops deploy; corpus owner fixes authoritative source. Anti-pattern: dev tunes prompt when PDF was wrong. Weekly AI ops review (Slide 15) makes this diagram real. Domain staff monthly citation validation — proactive QUAL reduction.

---

# Slide 14 | SLA Table

## On screen

**Example internal SLAs — customize to hardware reality (Lecture 05)**

| Metric | Pilot | Production |
|--------|-------|------------|
| Monthly availability | Best effort | 99.0% |
| p95 latency (RAG+LLM) | < 15s | < 8s |
| Corpus update after publish | 48h | 4h |
| S1 ack time | — | 15 min |
| Quality ticket first response | 2 business days | 1 business day |

```
SLA without GPU/memory plan = broken promise (Slide 03)
```

---

## Speaker notes

Leadership must align SLAs with Lecture 05 hardware — 8s p95 on undersized GPU is fiction. Corpus 4h requires owner workflow + reindex automation. S1 ack 15 min needs on-call rotation. Pilot “best effort” explicitly OK — don’t publish pilot SLAs externally. Review SLAs quarterly with eval pass rates and ticket volume.

---

# Slide 15 | Operating Cadence

## On screen

| Cadence | Meeting | Attendees |
|---------|---------|-----------|
| **Weekly** | AI ops review | Ops, dev, support lead |
| **Monthly** | Quality sample | Domain staff + product |
| **Quarterly** | Steering | Leadership + governance (L09) |
| **Per release** | Eval gate (L10) | Dev + domain |

**Weekly agenda (30 min):** outages · open S2 · corpus lag · top ticket themes

---

## Speaker notes

Rhythms prevent Lecture 12 from being one-time workshop. Weekly: ticket codes trend, open incidents, deploy calendar. Monthly: human rubric sample from Lecture 10. Quarterly: risk tier, new use cases, budget. Per release: no skip eval gate for T2. Put meetings on calendar before end of session.

---

# Slide 16 | RACI Summary

## On screen

| Activity | R | A | C | I |
|----------|---|---|---|---|
| Public promise wording | Product | Leadership | Legal, Support | All |
| System prompt change | Dev | Product | Domain, Security | Support |
| Corpus update | Staff owner | Domain lead | Dev | Support |
| Model upgrade | Ops | Dev lead | Product, Security | Support |
| Tier T2 approval workflow | Staff approver | Domain lead | Product | Leadership |
| Incident comms to users | Support | Product | Ops, Security | Leadership |
| Golden set maintenance | Domain | Dev | Support | Product |

*R=Responsible · A=Accountable · C=Consulted · I=Informed*

---

## Speaker notes

One slide — full RACI in lecture doc if needed. Clarify R vs A: accountable person decides, responsible person does. Support I on model upgrade but must be briefed before user-visible deploy. Golden set: domain responsible for content, dev for automation. Argue ambiguities now in workshop — not during S1.

---

# Slide 17 | Vocabulary Recap

## On screen

| Term | One line |
|------|----------|
| **LLM** | Predicts text — not truth (L04) |
| **RAG** | Retrieve docs then generate (L06) |
| **Token** | Text unit for model (L04) |
| **Embedding** | Vector for search (L06) |
| **Tier T2** | Human approve before publish (L08) |
| **Golden set** | Regression tests (L10) |
| **Tool** | Live API call (L11) |
| **VERIFY** | Check cites before trust (L08) |

**Shared vocabulary = faster incidents and fewer circular meetings**

---

## Speaker notes

60-second series recap — graduation moment. Ask who can draw stack from memory (Slide 20 criteria). Terms intentionally cross-linked to lecture files for self-study. Support using same words as dev reduces triage friction — “RAG miss” not “AI stupid.” Optional: vocabulary poster in support area.

---

# Slide 18 | Workshop — Team Action Plan

## On screen

**START WORKSHOP** (~20 min)

Complete **for your team / department:**

| Section | Fill |
|---------|------|
| **A.** Use cases to adopt in 90 days | Tier · owner · date |
| **B.** Use cases **not** to automate (T3) | Human owner only |
| **C.** Skills & training gaps | Lecture # · by when |
| **D.** Corpus duties | Canonical owner · refresh SLA |
| **E.** Support & escalation | Local contact · platform team |
| **F.** Success measures (3 months) | Baseline · target |
| **G.** Risks accept vs mitigate | Action |

*Full template in lecture doc §7*

---

## Speaker notes

Core deliverable — protect time, minimize lecturing. Facilitator circulates; leadership in room for sections B and G. A: max 3 use cases — realistic. B: explicit T3 list prevents scope creep. C: assign Lecture 07/08 to support if gaps. D: names not “the team.” F: measurable — VERIFY spot-check %, quality tickets/week. Collect one page per team before leave; optional certification = attendance + plan submitted.

---

# Slide 19 | Series Capstone Diagram

## On screen

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

## Speaker notes

Pause — let room photograph diagram. L12 sits on top intentionally: users don’t see layers. All prior lectures feed support promises and triage. Infrastructure at base — SLA honesty. Ask: “Which box is your team’s primary home?” — valid answer anywhere if RACI clear. Post-launch hands-on continues per CURRICULUM.md.

---

# Slide 20 | Graduation Criteria

## On screen

**“AI literate” in this program means:**

| Capability | Evidence |
|------------|----------|
| Explain stack to a colleague | Draw diagram from memory |
| Use assistants safely | Classification + VERIFY |
| Improve the system | Golden case contribution |
| Handle incidents calmly | Triage codes + templates |
| Collaborate cross-role | Shared RACI vocabulary |

**Optional certification:** facilitator signs attendance + action plan submitted

---

## Speaker notes

Not a coding exam — operational literacy for responsible deployment. Celebrate completion of 04–12 track. Evidence is behavioral: contributed one golden case, ran VERIFY on real draft, tagged mock ticket correctly in activity if you ran one. Leadership acknowledgment matters — send thank-you + action plan summary to sponsors.

---

# Slide 21 | Takeaways

## On screen

1. **Support is trust delivery** — triage, templates, no false certainty
2. **Incidents:** contain → fix → communicate → post-mortem → new golden case
3. **SLAs** must match infrastructure and review capacity
4. **Action plan** turns 12 lectures into **owned work** in your team
5. **Curriculum continues** — post-launch hands-on, quarterly eval refresh

**Support quick reference**

```
TRIAGE: OUT | PERF | QUAL | RAG | SEC | POL | EDU
NEVER PROMISE: 100% accurate · legal decisions · all-knowing AI
ALWAYS: cite verify path · human channel for T3
ESCALATE SEC + OUT immediately
```

---

## Speaker notes

Five bullets + quick reference card — point to printable in lecture doc. Emphasize action plan due date — 90 days from today on section A. Quarterly eval refresh from Lecture 10. Post-launch module in CURRICULUM.md for hands-on labs. Thank teams for cross-role attendance — support + dev + domain together is the win.

---

# Slide 22 | Thank You & What's Next

## On screen

**Congratulations**

Completing Lectures **04–12** = shared **full-stack AI literacy baseline** for building and serving responsibly.

| Resource | Link |
|----------|------|
| Master curriculum | [CURRICULUM.md](../CURRICULUM.md) |
| Post-launch hands-on | CURRICULUM facilitator todos |
| Quarterly refresh | Eval + corpus workshops |

**Q&A**

| Common question | Pointer |
|-----------------|---------|
| Who speaks to media? | Designated comms + promise framework (§2.2) |
| Legal threat over wrong answer? | Leadership + legal; preserve logs |
| After today? | Execute action plan · hands-on module |

---

## Speaker notes

Close with gratitude. Media questions only through comms — aligned with Slide 03. Legal threat: no ad hoc blame; preserve logs per policy; escalate immediately. After today: teams execute action plans; facilitators schedule quarterly check-in. Point to CURRICULUM.md for full series map including electives. Applause optional; certificates if prepared.

---
