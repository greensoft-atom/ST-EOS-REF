# Slide 01 | Agents, Tools & Workflows

## On screen

**Lecture 11 — Agents, Tools & Multi-Step AI Workflows**

*When RAG is not enough: APIs, calculators, planners, governed automation*

**Prerequisites:** Lectures 04–07, 09 (security); builds on 06 RAG, 10 eval

> An **agent** here = LLM + **ability to call tools** in a loop until a stop condition — **not** a sentient employee.

**Organization still owns:** permissions · logs · approvals

---

## Speaker notes

Open with facilitator quote verbatim. Audience: all roles — developers implement, staff define workflows, leadership sets tiers. Set boundary early: not AGI (Lecture 02); bounded automation with known failure modes. 90 minutes. Connect eval from Lecture 10 — agents need golden **workflows**, not only Q&A pairs.

---

# Slide 02 | RAG vs Tool vs Memory

## On screen

**Three ways to get facts**

| Mechanism | Data source | Freshness | Example |
|-----------|-------------|-----------|---------|
| **RAG** | Indexed documents | Index lag | Policy PDF Q&A |
| **Tool / API** | Live system | Real-time | CRM application status |
| **Model memory** | Training | Stale | General knowledge only |

```
Official static text?     → RAG
Live record / calculation? → Tool
Creative draft?           → LLM (+ verify)
Need all three?           → Orchestrated workflow
```

---

## Speaker notes

RAG is authoritative for **published** policy; tools for **live** records. Model memory alone is wrong choice for funding rates or application status — stale and ungrounded. Many production assistants combine all three in one orchestrated flow. Ask: “What do we have that’s only in a live system, not PDF?” — that’s a tool candidate.

---

# Slide 03 | Decision Guide

## On screen

| Situation | Use |
|-----------|-----|
| Single FAQ, stable PDF corpus | **RAG alone** — simpler |
| “What’s my application status?” | **Tool** (read-only CRM) |
| “Summarize this 40-page annex” | **RAG + LLM** |
| Eligibility + org registry lookup | **RAG + tool + LLM** |
| Grant award decision | **Human only** (T3) |
| Unstable external API | **Avoid** until eval stable |

**Default:** start minimal; add tools only when documents insufficient

---

## Speaker notes

Resist “agent for everything” — complexity, cost, eval burden. Unstable API = eval hell (Lecture 10). T3 decisions never autonomous. Good product question: “Can RAG answer this if corpus were perfect?” If yes, fix corpus first before adding tools.

---

# Slide 04 | What Is a Tool?

## On screen

**Tool** = defined function the LLM requests with **structured arguments**

```
Tool: lookup_grant_status
Args: { application_id: "APP-2026-0042" }
Returns: { status: "under_review", last_update: "2026-06-01" }
```

| Tool examples | Domain | Risk |
|---------------|--------|------|
| `search_literature_api` | Research | Medium |
| `get_patent_family` | IP | Medium |
| `calculate_budget_total` | Funding | Medium |
| `send_draft_email` | Comms | **High** — human confirm |
| `run_sql_readonly` | Analytics | **Dangerous** — strict sandbox |

---

## Speaker notes

Tools are not magic plugins — they’re contracts. LLM picks tool + fills JSON args; orchestrator validates and executes. High-risk tools: writes, sends, bulk export. Read-only default everywhere. SQL tools need sandbox, allowlisted tables, no user-supplied connection strings.

---

# Slide 05 | Agent Loop

## On screen

```
┌─────────────────────────────────────────────────────────┐
│  AGENT LOOP (max_steps = N)                             │
│                                                         │
│   User goal ──► LLM plans ──► tool call? ──► execute   │
│                      ▲              │                   │
│                      └──────────────┘                   │
│                   until answer or limit                 │
└─────────────────────────────────────────────────────────┘
```

| Component | Role |
|-----------|------|
| **Planner (LLM)** | Decide next action |
| **Tool router** | Map to approved APIs |
| **Executor** | Run with service credentials |
| **Memory** | Session + optional summary |
| **Stop rules** | Max steps, budget, tier |

---

## Speaker notes

Loop is the “agent” — not consciousness. **max_steps** prevents runaway (50 CRM calls). Budget caps tokens + API fees. Tier (Lecture 08) limits which tools exist per user role. Memory: session context only unless explicit summary store with retention policy (Lecture 09). Walk one iteration verbally: plan → call → observe result → plan again.

---

# Slide 06 | Architecture — RAG + Tool

## On screen

```
User: "Is APP-2026-0042 eligible for fast-track?"
                │
                ▼
         ┌──────────────┐
         │ Orchestrator │
         └──────┬───────┘
                │
    ┌───────────┼───────────┐
    ▼           ▼           ▼
 RAG: policy  Tool: CRM   LLM: synthesize
 on fast-track  get status  answer + cite
    │           │           │
    └───────────┴───────────┘
                ▼
    Response + citations + live status field
```

**User sees:** policy quotes **and** “status as of [timestamp]”

---

## Speaker notes

Orchestrator owns sequencing — not the LLM directly calling production DB. Parallel fetch possible when independent. Synthesis step must cite RAG chunks and label tool fields separately (“[Tool: registry]”). Timestamp on live data reduces “it said approved yesterday” disputes.

---

# Slide 07 | Tool Schema

## On screen

**Developer view — narrow descriptions, strict validation**

```json
{
  "name": "lookup_grant_status",
  "description": "Get official status for one application ID",
  "parameters": {
    "type": "object",
    "properties": {
      "application_id": {
        "type": "string",
        "pattern": "^APP-[0-9]{4}-[0-9]{4}$"
      }
    },
    "required": ["application_id"]
  }
}
```

| Principle | Why |
|-----------|-----|
| Narrow descriptions | Model picks correct tool |
| Schema validation | Reject malformed args **before** execute |
| Read-only default | Writes need human confirm |

---

## Speaker notes

Bad description → wrong tool (weather API for funding). Validation catches hallucinated IDs before CRM query. Pattern regex prevents injection-shaped IDs. Descriptions are prompt engineering for routing — product + dev collaborate. Version tool schemas like prompts; eval when schema changes.

---

# Slide 08 | Credentials Model

## On screen

```
LLM never holds DB password
        │
        ▼
Orchestrator uses service account
        │
        ├─► Scoped permissions (read one table)
        ├─► Audit log per tool call
        └─► Rate limits
```

| Anti-pattern | Risk |
|--------------|------|
| LLM constructs raw SQL | Injection |
| User supplies tool credentials | Leak |
| Tool returns PII to wrong user | ACL bug |

**Log:** user, tool name, args, timestamp, result code

---

## Speaker notes

Non-negotiable security (Lecture 09): credentials live in orchestrator secrets manager, never in model weights or prompts. Service account scoped to minimum tables/actions. Audit log feeds security review and incident response (Lecture 12). PII leak is ACL bug — test with golden cases for wrong user role.

---

# Slide 09 | Workflow Pattern Catalog

## On screen

| Pattern | Steps | Tier typical |
|---------|-------|--------------|
| **Retrieve-then-read** | RAG → LLM answer | T1–T2 |
| **Retrieve-then-act-read** | RAG + tool → LLM | T1–T2 |
| **Human-in-the-middle** | Draft → human approve → tool send | T2–T3 |
| **Batch overnight** | Queue jobs → results morning | T1 |
| **Router** | Classify intent → specialist assistant | T1 |

```
Router ──► Funding assistant
        └──► Patent assistant
        └──► General FAQ (RAG only)
```

---

## Speaker notes

Most orgs need 2–3 patterns, not twelve bespoke agents. Router reduces prompt confusion — specialist tools per domain. Human-in-the-middle mandatory for send/submit. Batch for low-urgency report generation — easier eval, no latency SLA stress. Map each planned use case to one pattern before building.

---

# Slide 10 | Worked Example — Funding Eligibility

## On screen

**T1 assist — not T3 decision**

```
1. User asks eligibility question
2. RAG retrieves circular §eligibility
3. Tool fetches org type from registry (read-only)
4. LLM maps org type to rules WITH citations
5. UI: "Informational — officer must confirm"
```

**Sample output:**

> Org registered as **SME** [Tool: registry].  
> 2026 circular: SMEs may apply if revenue < X [Source 2].  
> **Not a final eligibility determination.**

---

## Speaker notes

Walk step-by-step. Tier messaging is product requirement — prevents over-trust. Officer still decides T3. Tool read-only; no “submit application” in same flow without human gate. Golden eval: correct tool called, correct cite, disclaimer present. Domain staff validate circular § references quarterly.

---

# Slide 11 | Worked Example — Research & IP

## On screen

**Literature review assist**

```
1. Tool: search publication API (keywords)
2. RAG: internal lab notebooks (optional)
3. LLM: comparison table — DOIs from tool results only
4. Human: verifies DOIs before paper citation
```

**Patent docket reminder**

```
1. Tool: read docket dates (read-only)
2. LLM: draft reminder email
3. Human: approves send — NO auto-send tool
```

| Domain | Tool | Human gate |
|--------|------|------------|
| Research | External API | DOI verify |
| IP | Docket read | Email approve |

---

## Speaker notes

Research: never trust LLM-generated DOI without lookup. Internal notebooks via RAG — ACL per project. IP: draft-only email — T2 approve before mail system sends. Contrast with bad design: auto-send reminder on LLM hallucinated date. Ask room for one read-only tool in their domain.

---

# Slide 12 | When NOT to Agent

## On screen

| Situation | Why skip agent |
|-----------|----------------|
| Single FAQ, stable PDF | RAG alone simpler |
| High-stakes write (grant award) | T3 — no autonomous tools |
| Unstable API | Eval hell (Lecture 10) |
| Audit requirements unmet | Governance block |
| “Browse web for policy” | Official corpus beats random pages |

**Rule:** add complexity only when **document + human** insufficient

---

## Speaker notes

Web-browsing agent for official policy usually **worse** than authoritative RAG corpus — wrong site, outdated page, injection via search results. Grant award: human only. Governance block: no audit log = no tool in prod. Facilitator: list one org project that should **stop** at RAG without agent — often the right answer.

---

# Slide 13 | Agent Failure Modes

## On screen

| Failure | Example | Mitigation |
|---------|---------|------------|
| **Wrong tool** | Weather API for funding | Clear descriptions; router |
| **Wrong args** | Typo in application ID | Schema validation |
| **Loop runaway** | 50 tool calls | `max_steps` cap |
| **Cost blowout** | Huge token + API fees | Budget per session |
| **Over-trust** | User thinks CRM = decision | Tier messaging (L08) |
| **Injection via tool return** | Malicious page in search | Sanitize tool outputs |

```
Failure mode          →  Golden test type
wrong tool            →  tool selection eval
wrong args            →  arg correctness eval
"delete all records"  →  refusal eval (no tool)
```

---

## Speaker notes

Each failure maps to eval case (Lecture 10). Tool return injection: treat tool output as untrusted input — strip scripts, limit length, allowlist fields. Cost blowout real for loop agents — per-session dollar cap. Over-trust is UX/legal — disclaimers and source labels, not model tweak alone.

---

# Slide 14 | Guardrail Stack

## On screen

```
User input
    → injection filter
    → tier check (allowed tools for this user?)
    → LLM plan
    → tool allowlist + arg validation
    → execute + audit log
    → output filter
    → human gate (if T2/T3 write)
```

| Layer | Blocks |
|-------|--------|
| Injection filter | Prompt exfiltration |
| Tier check | Unauthorized tool access |
| Allowlist | Unapproved integrations |
| Human gate | Irreversible actions |

---

## Speaker notes

Defense in depth — no single layer. MCP/plugins only after security review and allowlist (Lecture 09). Human gate last for send/submit/delete. Output filter redacts PII crossing ACL. Walk funding example through stack: internal officer passes tier check; external citizen never sees CRM tool.

---

# Slide 15 | Evaluation for Agents

## On screen

**Extend golden set to workflows**

| Test type | Example |
|-----------|---------|
| **Tool selection** | Query should call `lookup_*` not `search_web` |
| **Arg correctness** | ID format preserved |
| **Refusal** | “Delete all records” → no tool |
| **End-to-end** | Golden workflow scenarios |

**Tool approval checklist (new integration)**

```
□ Business owner  □ ACL defined  □ Read-only default
□ Schema validation  □ Rate limits  □ Audit logging
□ Eval scenarios  □ Security review  □ Tier messaging
```

---

## Speaker notes

Single-turn RAG eval insufficient — assert **which tools fired**. Log tool traces in staging eval for diff across releases. Refusal cases 100% pass like injection. Printable checklist in lecture doc — use in architecture review. No new tool without named business owner.

---

# Slide 16 | UX — What Users Should See

## On screen

| Good UX | Bad UX |
|---------|--------|
| Show which tools were used | Hidden automation |
| “Live data as of [timestamp]” | Imply real-time legal decision |
| Confirm before send/submit | One-click irreversible actions |
| Link to source doc + API field | Wall of AI prose |

**Example UI labels**

- `[Source: circular-2026-sme.pdf p.4]`
- `[Live: registry lookup 2026-06-30 14:02]`
- `Draft only — approve to send`

---

## Speaker notes

Transparency builds trust (Lecture 08). Hidden CRM lookup feels like magic — and like official decision. Timestamp on live fields. Separate visual styling for doc cites vs tool data. Confirm dialog for T2 sends — separate mail system, not LLM tool direct to SMTP.

---

# Slide 17 | Activity — Design a Workflow

## On screen

**START ACTIVITY** (~12 min)

Pick **one** scenario — list **RAG**, **tools**, **human steps**, **tier**:

| Scenario | Design for… |
|----------|-------------|
| **A** | Exhibition exhibitor asks booth payment status |
| **B** | Researcher wants papers + internal project notes |
| **C** | Staff asks to email all applicants |

**Sample C (discuss after):** T3 content · no auto-send · read-only recipient export · LLM draft · human approve → mail system

---

## Speaker notes

Groups 3–4 people, 8 min design + 4 min share. A: payment status = tool read-only + maybe FAQ RAG. B: publication API + notebook RAG + human DOI check. C: trap question — auto-send is wrong; export list read-only ACL, draft, human approve. Debate tier for A (T1 info vs T2 if payment disputes). Capture one diagram per group if time.

---

# Slide 18 | Takeaways & Next Steps

## On screen

1. **RAG** for documents · **tools** for live systems · **agents** orchestrate with limits
2. **Validate tool args** — never LLM raw SQL or unrestricted writes
3. **Human gates** for T2 send and all T3 decisions
4. **Log every tool call** — user, args, timestamp
5. **Eval workflows**, not only single answers

**Next:** [12 — Serving customers](../12_serving_customers.md) — operating for users daily

**Q&A**

---

## Speaker notes

Recap five bullets. Q&A: Agent ≠ autonomous employee. MCP/plugin after security review only. Don’t replace RAG with web-browsing for official policy. Thank room; assign homework: pick one read-only tool candidate + fill approval checklist draft. Bridge: Lecture 12 makes support and incidents explicit when agents go live.

---
