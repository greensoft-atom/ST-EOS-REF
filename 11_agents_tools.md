# Agents, Tools & Multi-Step AI Workflows

*For all members — developers, operators, staff, product, leadership*  
*When RAG is not enough: APIs, calculators, planners, and governed automation*

---

## Module Overview

This is **Lecture 11**. Many tasks need **live data** or **multiple steps** — not one retrieval + one answer. This session explains **tool use**, **agents**, workflow patterns, risks, and governance — without treating agents as magic autonomy.

| Lecture | Topic |
|---------|--------|
| 6 | [RAG](06_rag.md) — document knowledge |
| 10 | [Evaluation](10_evaluation_quality.md) — quality gates |
| **11 (this)** | **Agents, tools & workflows** |
| 12 | [Serving customers](12_serving_customers.md) |

**Duration:** 90 minutes  
**Prerequisites:** [04](04_genai_llm.md)–[07](07_prompting.md), [09](09_privacy_security.md)

---

## Learning Objectives

By the end, members should be able to:

1. Distinguish **RAG vs tools vs agents** and when to use each  
2. Describe a **tool-calling loop** conceptually  
3. Identify **risks** (wrong tool args, over-automation, credential exposure)  
4. Map **representative workflows** for research/funding/public sector  
5. Apply **governance** tiers to automated workflows ([08](08_trust_verification.md))  

---

## 1. Opening (5 min)

**Facilitator message:**

> “An **agent** here means: LLM + **ability to call tools** in a loop until a stop condition — not a sentient employee. The organization still owns **permissions, logs, and approvals**.”

---

## 2. RAG vs Tools vs Agents (15 min)

### 2.1 Three ways to get facts

| Mechanism | Data source | Freshness | Example |
|-----------|-------------|-----------|---------|
| **RAG** | Indexed documents | Index lag | Policy PDF Q&A |
| **Tool / API** | Live system | Real-time | CRM application status |
| **Model memory** | Training | Stale | General knowledge only |

**Decision guide:**

```
Need official static text?     → RAG
Need live record / calculation? → Tool
Need creative draft?           → LLM (+ verify)
Need all three?                → Orchestrated workflow
```

### 2.2 What is a tool?

A **tool** = a defined function the LLM can request with structured arguments.

```
Tool: lookup_grant_status
Args: { application_id: "APP-2026-0042" }
Returns: { status: "under_review", last_update: "2026-06-01" }
```

| Tool examples (representative) | Domain |
|-------------------------------|--------|
| `search_literature_api` | Research |
| `get_patent_family` | IP |
| `calculate_budget_total` | Funding |
| `send_draft_email` | **High risk** — usually human confirm |
| `run_sql_readonly` | **Dangerous** — strict sandbox |

### 2.3 What is an agent (workflow sense)?

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
| **Stop rules** | Max steps, budget, tier ([08](08_trust_verification.md)) |

**Not AGI** ([02](02_concept_agi.md)) — bounded automation with failure modes.

---

## 3. Tool-Calling Architecture (15 min)

### 3.1 Request flow diagram

```
User: "Is application APP-2026-0042 eligible for fast-track?"
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
         Response + citations
         + live status field
```

### 3.2 Tool schema (developer view)

```json
{
  "name": "lookup_grant_status",
  "description": "Get official status for one application ID",
  "parameters": {
    "type": "object",
    "properties": {
      "application_id": { "type": "string", "pattern": "^APP-[0-9]{4}-[0-9]{4}$" }
    },
    "required": ["application_id"]
  }
}
```

**Principles:**

- **Narrow descriptions** — model picks correct tool  
- **Validation** — reject malformed args before execution  
- **Read-only default** — writes need human confirm  

### 3.3 Credentials & security ([09](09_privacy_security.md))

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

---

## 4. Workflow Patterns (18 min)

### 4.1 Pattern catalog

| Pattern | Steps | Tier typical |
|---------|-------|--------------|
| **Retrieve-then-read** | RAG → LLM answer | T1–T2 |
| **Retrieve-then-act-read** | RAG + tool → LLM | T1–T2 |
| **Human-in-the-middle** | Draft → human approve → tool send | T2–T3 |
| **Batch overnight** | Queue jobs → results morning | T1 |
| **Router** | Classify intent → specialist assistant | T1 |

### 4.2 Worked example — funding eligibility assist (T1, not T3)

```
1. User asks eligibility question
2. RAG retrieves circular §eligibility
3. Tool fetches applicant org type from internal registry (read-only)
4. LLM maps org type to rules WITH citations
5. UI shows: "Informational — officer must confirm" (T3 decision stays human)
```

**Output snippet:**

> Your organization is registered as **SME** [Tool: registry].  
> The 2026 circular states SMEs may apply if revenue &lt; X [Source 2].  
> **This is not a final eligibility determination.**

### 4.3 Worked example — literature review assist (research)

```
1. Tool: search publication API with keywords
2. RAG: internal lab notebooks (optional)
3. LLM: comparison table with DOIs from tool results only
4. Human: verifies DOIs before citation in paper
```

### 4.4 Worked example — patent docket reminder (IP)

```
1. Tool: read docket dates (read-only)
2. LLM: draft reminder email
3. Human: approves send (T2) — tool does NOT auto-send
```

### 4.5 When NOT to agent

| Situation | Why |
|-----------|-----|
| Single FAQ with stable PDF | RAG alone simpler |
| High-stakes write (grant award) | T3 — no autonomous tools |
| Unstable API | Eval hell ([10](10_evaluation_quality.md)) |
| No audit requirement met | Governance block |

---

## 5. Risks & Guardrails (12 min)

### 5.1 Agent failure modes

| Failure | Example | Mitigation |
|---------|---------|------------|
| **Wrong tool** | Called weather API for funding | Clear tool descriptions; router |
| **Wrong args** | Wrong application ID | Schema validation |
| **Loop runaway** | 50 tool calls | max_steps cap |
| **Cost blowout** | Huge token + API fees | Budget per session |
| **Over-trust** | User thinks CRM status is decision | Tier messaging ([08](08_trust_verification.md)) |
| **Injection via tool return** | Malicious webpage in search result | Sanitize tool outputs |

### 5.2 Guardrail stack

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

### 5.3 Evaluation for agents ([10](10_evaluation_quality.md))

| Test type | Example |
|-----------|---------|
| **Tool selection** | Query should call `lookup_*` not `search_web` |
| **Arg correctness** | ID format preserved |
| **Refusal** | “Delete all records” → no tool |
| **End-to-end** | Golden workflow scenarios |

---

## 6. Staff & Product — What Users See (8 min)

| Good UX | Bad UX |
|---------|--------|
| Show which tools were used | Hidden automation |
| “Live data as of [timestamp]” | Imply real-time legal decision |
| Confirm before send/submit | One-click irreversible actions |
| Link to source doc + API field | Wall of AI prose |

---

## 7. Activity — Design a Workflow (12 min)

Pick one:

| Scenario | Design |
|----------|--------|
| A | Exhibition exhibitor asks booth payment status |
| B | Researcher asks for papers + internal project notes combined |
| C | Staff asks to email all applicants |

For each: list **RAG**, **tools**, **human steps**, **tier**.

**Sample answer C:**

- Tier T3 for content — **no** auto-send tool  
- Tool: export recipient list (read-only, ACL)  
- LLM: draft only  
- Human: approve → separate mail system  

---

## 8. Q&A

**Q: Agent = autonomous employee?**  
A: No — bounded software with human accountability.

**Q: Connect any MCP / plugin?**  
A: Only after security review and allowlist ([09](09_privacy_security.md)).

**Q: Replace RAG with agent that browses web?**  
A: Usually worse for official policy — prefer authoritative corpus.

---

## 9. Slide Outline (18 slides)

1. Title  
2. RAG vs tool vs memory  
3. Decision guide  
4. Tool example  
5. Agent loop diagram  
6. Architecture with RAG + tool  
7. Tool schema  
8. Credentials model  
9. Pattern catalog  
10. Funding example  
11. Research example  
12. When NOT to agent  
13. Failure modes  
14. Guardrail stack  
15. Eval for agents  
16. UX table  
17. Activity  
18. Takeaways  

---

## 10. Takeaways

1. **RAG** for documents; **tools** for live systems; **agents** orchestrate with limits.  
2. **Validate tool args**; never give LLM raw SQL or unrestricted writes.  
3. **Human gates** for T2 send and all T3 decisions.  
4. **Log every tool call** with user, args, timestamp.  
5. **Next:** [12_serving_customers.md](12_serving_customers.md) — operating all of this for users.

---

## 11. Printable — Tool Approval Checklist

```
New tool integration:
□ Business owner named
□ Data class & ACL defined
□ Read-only default?
□ Schema validation
□ Rate limits
□ Audit logging
□ Eval scenarios in golden set
□ Security review complete
□ Tier messaging defined
```
