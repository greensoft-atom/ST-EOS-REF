# Slide Deck — AI You Already Use Every Day

*Source: [E02_ai_daily_life.md](../E02_ai_daily_life.md)*

---

# Slide 01 | Title & Objectives

## On screen

**Elective E02 — AI You Already Use Every Day**

*45–60 minutes · All members*

**Today you will:**

| # | Objective |
|---|-----------|
| 1 | Name **Recognition AI** in daily tools |
| 2 | Name **Generative AI** in consumer apps |
| 3 | Build intuition before **org platform** use |

> You are already an AI user — this session makes the invisible visible.

## Speaker notes

Open with energy: nobody starts from zero. This elective builds confidence before internal assistants launch. No org deployment details — personal and consumer context first. Set expectation that they'll classify features in an activity and connect to work policy at the end.

---

# Slide 02 | Icebreaker — AI You Used Today

## On screen

**START DISCUSSION — ~5 minutes**

**Prompt:** List AI you may have used **before this meeting**:

| Category | Examples to jog memory |
|----------|------------------------|
| Phone | Face unlock, voice assistant, photo search |
| Maps & travel | Traffic prediction, ETA |
| Search & feeds | Ranking, autocomplete, recommendations |
| Language | Translation, chat assistant |
| Social | Feed ranking, content moderation |

**Capture 5–8 examples on the board** — we'll classify them later.

## Speaker notes

Go round-robin or shout-out. Most lists skew to phone and search — that's fine. Don't correct jargon yet; just collect. If someone says "I didn't use AI," nudge: spam filter, autocorrect, or map route all count. This primes recognition vs generative for the next slides.

---

# Slide 03 | Recognition AI — The Invisible Layer

## On screen

**Recognition AI — mostly behind the scenes**

| Tool | AI job | Output you see |
|------|--------|----------------|
| Email spam filter | Classify spam vs not | Cleaner inbox |
| Face unlock | Match face to enrolled template | Phone unlocks |
| Voice assistant wake word | Detect phrase | Device listens |
| Maps traffic | Predict delay | Color-coded route |
| Streaming recommendations | Rank content | "For you" list |
| OCR on phone camera | Recognize text | Copy/paste from photo |

**Pattern:** You see the **outcome**, not generated prose.

## Speaker notes

Stress invisibility — recognition AI succeeded when users forget it exists. Pick two examples and ask "what would happen if it misclassified?" Wrong spam bucket vs wrong face match have different harm levels. This connects later to E04 bias and Lecture 08 verification without naming tiers yet.

---

# Slide 04 | Generative AI — The Visible Layer

## On screen

**Generative AI — you read the output**

| Tool | AI job | What you do |
|------|--------|-------------|
| Chat assistants | Draft / answer questions | Read and judge |
| Translation (modern) | Generate target-language text | Check meaning |
| Image generators | Create pixels from prompt | Review quality |
| Meeting summaries | Draft notes from audio | Verify facts |

**Pattern:** Output is **new content** — verify before trust.

See: [08_trust_verification.md](../../08_trust_verification.md)

## Speaker notes

Generative AI feels newer because users interact with text and images directly. Contrast with spam filter — you rarely read the model's reasoning. Key habit: draft, don't assume truth. Mention fluent wrong answers (wrong date, wrong name) as the core risk for staff who will use org assistants.

---

# Slide 05 | Stack Diagram — Recognition + Generative Together

## On screen

**Real workflows combine both**

```
Photo of document
    → OCR (recognition)
    → RAG search (retrieval)
    → LLM summary (generative)
    → You verify (human)
```

| Layer | Role |
|-------|------|
| **Recognition** | Extract text, classify, route |
| **Retrieval** | Find relevant org documents |
| **Generative** | Draft answer or summary |
| **Human** | Verify, approve, publish |

## Speaker notes

This is the org stack in one picture — preview of Lectures 06–08. Walk through slowly: one user action triggers multiple AI types. Emphasize the human step is not optional for work use. Ask audience for a similar chain in their job (e.g., scan form → classify → draft reply).

---

# Slide 06 | Activity — Classify the Feature

## On screen

**START ACTIVITY — ~10 minutes**

**Label each:** **R** (Recognition) · **G** (Generative) · **Both**

| Feature | Your call |
|---------|-----------|
| Autocomplete in search box | ? |
| Fraud alert on card | ? |
| Chatbot draft reply | ? |
| Sort inbox by priority | ? |

**Answer key (reveal after discussion):**

| Feature | R / G / Both |
|---------|--------------|
| Autocomplete | **Both** (rank + suggest text) |
| Fraud alert | **R** |
| Chatbot draft reply | **G** |
| Sort inbox priority | **R** |

## Speaker notes

Pairs or small groups first, then reveal. Autocomplete tripped most groups — good teachable moment. Debate is welcome on "both" cases; the point is thinking in layers, not perfect taxonomy. Connect fraud alert to silent harm if wrong (E04 preview).

---

# Slide 07 | Work vs Personal — Policy Matters

## On screen

**Same technology — different rules**

| Context | Typical constraint |
|---------|-------------------|
| **Personal apps** | You accept vendor terms; data may leave jurisdiction |
| **Org platform** | Approved stack, classification rules, audit trail |

| Topic | Reference |
|-------|-----------|
| Approved internal tools | [13_post_launch_platform.md](../../13_post_launch_platform.md) |
| Data & privacy rules | [09_privacy_security.md](../../09_privacy_security.md) |

> **Do not** paste confidential work into unapproved consumer chatbots.

## Speaker notes

Non-punitive tone — many people used consumer ChatGPT for drafts. Explain *why* org provides alternatives: data residency, logging, corpus control. If your org has a specific policy name, say it here. This slide prevents the most common pre-launch incident.

---

# Slide 08 | Recognition vs Generative — Quick Compare

## On screen

**Side-by-side recap**

| | Recognition | Generative |
|---|-------------|------------|
| **Daily prevalence** | Everywhere, often invisible | Growing, very visible |
| **User action** | Trust outcome | **Read and verify** |
| **Failure mode** | Wrong bucket / score | Fluent wrong text |
| **Org stack role** | Route, classify, index | Draft, summarize, answer |

**Your new habit:** Notice which type you used — and whether verification is needed.

## Speaker notes

Bridge slide before takeaways — consolidates icebreaker examples onto this table. Invite one volunteer to re-classify an example from the opening board. Reinforce that org assistants combine both plus RAG; personal apps rarely expose which layer failed.

---

# Slide 09 | Takeaways

## On screen

**Remember**

| # | Takeaway |
|---|----------|
| 1 | You already use AI daily — **mostly recognition** |
| 2 | Generative AI adds **visible drafts** — verify before trust |
| 3 | Org assistants = recognition + RAG + generative + **you** |
| 4 | Work data belongs in **approved** tools only |

**One line:** AI is not future-only — it's already in your pocket; org literacy is about using it safely at work.

## Speaker notes

Three-minute recap. Ask: "What will you notice differently tomorrow?" Expected answers: spam filter as AI, chatbot as generative, verification habit. Point to E01 if anyone missed the learning-loop foundation.

---

# Slide 10 | Next Steps

## On screen

**Where to go next**

| Elective / module | Topic |
|-------------------|-------|
| [E01_how_ai_works.md](../E01_how_ai_works.md) | Patterns, training, inference |
| [E03_research_public_sector.md](../E03_research_public_sector.md) | Sector use cases |
| [03_gai_rai.md](../../03_gai_rai.md) | Recognition vs generative (full) |
| [08_trust_verification.md](../../08_trust_verification.md) | VERIFY workflow |

**Tomorrow's experiment:** Pick one tool from the icebreaker — identify R or G.

## Speaker notes

Close lightly — optional homework builds retention. For leadership in the room, note E03 for sector mapping and E05 for jobs narrative. Thank group; remind them they're already practitioners, not beginners.

---
