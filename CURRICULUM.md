# AI Literacy Curriculum — Master Plan & Todo

*Science, Technology, Innovation & Research Management Platform*  
*For all members — developers, operators, staff, support, leadership*

---

## Complete curriculum status

### Core lectures

| # | File | Title | Lecture | Slides | Status |
|---|------|--------|---------|--------|--------|
| 01 | [01_concept.md](01_concept.md) | What Is AI? + overview | ✅ | — | Draft |
| 02 | [02_concept_agi.md](02_concept_agi.md) | AGI | ✅ | — | Complete |
| 03 | [03_gai_rai.md](03_gai_rai.md) | Gen vs Recognition | ✅ | [slides/03](slides/03_gai_rai.md) | Complete |
| 04 | [04_genai_llm.md](04_genai_llm.md) | LLM foundations | ✅ | [slides/04](slides/04_genai_llm.md) | Complete |
| 05 | [05_local_llm.md](05_local_llm.md) | Local LLM | ✅ | [slides/05](slides/05_local_llm.md) | Complete |
| 06 | [06_rag.md](06_rag.md) | RAG | ✅ | [slides/06](slides/06_rag.md) | Complete |
| 07 | [07_prompting.md](07_prompting.md) | Prompting | ✅ | [slides/07](slides/07_prompting.md) | Complete |
| 08 | [08_trust_verification.md](08_trust_verification.md) | Trust & VERIFY | ✅ | [slides/08](slides/08_trust_verification.md) | Complete |
| 09 | [09_privacy_security.md](09_privacy_security.md) | Privacy & security | ✅ | [slides/09](slides/09_privacy_security.md) | Complete |
| 10 | [10_evaluation_quality.md](10_evaluation_quality.md) | Evaluation | ✅ | [slides/10](slides/10_evaluation_quality.md) | Complete |
| 11 | [11_agents_tools.md](11_agents_tools.md) | Agents & tools | ✅ | [slides/11](slides/11_agents_tools.md) | Complete |
| 12 | [12_serving_customers.md](12_serving_customers.md) | Serving & action plan | ✅ | [slides/12](slides/12_serving_customers.md) | Complete |

### Post-launch & workshops

| # | File | Title | Lecture | Slides | Status |
|---|------|--------|---------|--------|--------|
| 13 | [13_post_launch_platform.md](13_post_launch_platform.md) | Platform hands-on | ✅ | [slides/13](slides/13_post_launch.md) | Complete |
| W1 | [workshops/corpus_funding.md](workshops/corpus_funding.md) | Funding corpus | ✅ | [slides/workshop_funding](slides/workshop_funding.md) | Complete |
| W2 | [workshops/corpus_patents.md](workshops/corpus_patents.md) | Patents corpus | ✅ | [slides/workshop_patents](slides/workshop_patents.md) | Complete |
| W3 | [workshops/corpus_policy.md](workshops/corpus_policy.md) | Policy corpus | ✅ | [slides/workshop_policy](slides/workshop_policy.md) | Complete |

### General electives

| # | File | Title | Lecture | Slides | Status |
|---|------|--------|---------|--------|--------|
| E01 | [electives/E01_how_ai_works.md](electives/E01_how_ai_works.md) | How AI works | ✅ | [electives/slides/E01](electives/slides/E01_how_ai_works.md) | Complete |
| E02 | [electives/E02_ai_daily_life.md](electives/E02_ai_daily_life.md) | AI daily life | ✅ | [electives/slides/E02](electives/slides/E02_ai_daily_life.md) | Complete |
| E03 | [electives/E03_research_public_sector.md](electives/E03_research_public_sector.md) | Research & public sector | ✅ | [electives/slides/E03](electives/slides/E03_research_public_sector.md) | Complete |
| E04 | [electives/E04_bias_fairness.md](electives/E04_bias_fairness.md) | Bias & fairness | ✅ | [electives/slides/E04](electives/slides/E04_bias_fairness.md) | Complete |
| E05 | [electives/E05_ai_and_jobs.md](electives/E05_ai_and_jobs.md) | AI & jobs | ✅ | [electives/slides/E05](electives/slides/E05_ai_and_jobs.md) | Complete |
| E06 | [electives/E06_ai_world_trends.md](electives/E06_ai_world_trends.md) | World trends | ✅ | [electives/slides/E06](electives/slides/E06_ai_world_trends.md) | Complete |

**Slides format:** [slides/README.md](slides/README.md) — markdown only, no PowerPoint.

---

## Implementation todo (facilitators)

### Phase 1 — Before first technical session

- [ ] Assign facilitator + domain expert per lecture  
- [ ] Confirm demo policy (pre-launch vs live)  
- [ ] Distribute [slides/](slides/) paths — test projector / markdown viewer  
- [ ] Print handouts: VERIFY, paste tree, eval gate, support quick ref  
- [ ] Optional: run E01–E03 for new cohorts  

### Phase 2 — Technical track (months 1–5)

- [ ] Deliver 04 → 05 → 06 → 07 → 08 → 09 → 10 → 11 → 12 per [README](README.md) schedule  
- [ ] Per session: prereading, activities, Q&A log  
- [ ] Collect team action plans at Lecture 12  

### Phase 3 — Post-launch (month 6+)

- [ ] Run [13_post_launch_platform.md](13_post_launch_platform.md) for all users with access  
- [ ] Run workshops W1–W3 with domain + dev pairs  
- [ ] Run electives E04–E06 for leadership / all-staff weeks  

### Phase 4 — Ongoing

- [ ] Quarterly eval refresh ([10](10_evaluation_quality.md))  
- [ ] Update golden sets from support tickets ([12](12_serving_customers.md))  
- [ ] Corpus owner monthly review (workshop checklists)  
- [ ] Security red-team drill (future module — prompt injection, ACL)  

---

## Recommended learning paths

### Path A — Full program

```
E01–E03 (optional) → 03–12 → 13 → W1–W3 (by role) → E04–E06
```

### Path B — Technical essentials

```
03 → 04 → 06 → 07 → 08 → 09 → 12 → 13
```

### Path C — By role (unchanged)

| Role | Required | Recommended |
|------|----------|-------------|
| Staff / research / policy | 04, 06, 07, 08, 12, W* | 09, 13, E04 |
| Support | 07, 08, 09, 12, 13 | 04, 06 |
| Developers | 04–11, 13 | W*, E01 |
| Operators | 04, 05, 06, 09, 10, 13 | 11 |
| Leadership | 03, 08, 09, 12, E04, E06 | 04, 10 |

*W = relevant workshop (funding / patents / policy)*

---

## 8-month schedule (example)

| Month | Sessions |
|-------|----------|
| 1 | E01–E02 optional · 04 LLM · 05 Local |
| 2 | 06 RAG · 07 Prompting |
| 3 | 08 Trust · 09 Security |
| 4 | 10 Eval · 11 Agents |
| 5 | 12 Serve + action plans · E03 elective |
| 6 | **13 Hands-on** · W1 Funding workshop |
| 7 | W2 Patents · W3 Policy |
| 8 | E04 Bias · E05 Jobs · E06 Trends · quarterly review |

---

## Cross-reference map

```
E01–E03 electives
       │
       ▼
03 GAI/RAI → 04 LLM → 05 Local → 06 RAG
       │                    │
       └──── 07 Prompt ─────┘
                 │
         08 Trust · 09 Security
                 │
         10 Eval · 11 Agents
                 │
              12 Serve
                 │
         13 Hands-on (post-launch)
                 │
    W1 Funding · W2 Patents · W3 Policy
                 │
         E04 Bias · E05 Jobs · E06 Trends
```

---

## Design principles

1. **Markdown everywhere** — lectures, slides, workshops, electives  
2. **Correct mental models** — aligned with 04–06 internals  
3. **Practical** — checklists, worksheets, golden sets  
4. **Role-aware** — RACI in 12, ACL in workshops  
5. **Honest limits** — tiers T0–T3 throughout  

---

## Future modules (not yet written)

- [ ] Security red-team drill (injection, ACL bypass lab)  
- [ ] Facilitator train-the-trainer guide  
- [ ] 01_concept + 02 AGI markdown slides (optional)
