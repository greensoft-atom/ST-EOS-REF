# Slide Deck — Post-Launch Platform Hands-On

*Source: [13_post_launch_platform.md](../13_post_launch_platform.md) | Facilitator use*

---

# Slide 01 | Title & prerequisites

## On screen

**Module 13 — Using the Approved AI Platform**  
*Hands-on · Post-launch only*

Prerequisites: Lectures 07, 08, 09 (minimum)

Today: demo → 4 exercises → debrief

## Speaker notes

Welcome. Confirm everyone can log in. If not, pair with neighbor and fix access after session. (~2 min)

---

# Slide 02 | What you will do today

## On screen

| Block | Time |
|-------|------|
| Platform tour demo | 15 min |
| Exercise 1 — GAFCO prompting | 18 min |
| Exercise 2 — RAG & citations | 18 min |
| Exercise 3 — Tiers & VERIFY | 18 min |
| Exercise 4 — Classification | 18 min |
| Debrief & commitment | 30 min |

## Speaker notes

Emphasize exercises are staging/synthetic data only. (~1 min)

---

# Slide 03 | Platform map

## On screen

```
Login → Assistant list → Chat
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
 Citations  Feedback  About
            button    (versions)
```

## Speaker notes

Live click-through on projector. Pause on About screen — model and index dates matter for trust. (~5 min)

---

# Slide 04 | Demo — good GAFCO prompt

## On screen

**Goal · Audience · Format · Constraints**

Example: SME eligibility list, numbered, cite circular only

## Speaker notes

Run live query. Click a citation. Ask room: “Would you send this to a director without reading source?” Lead to VERIFY. (~5 min)

---

# Slide 05 | Demo — refusal is success

## On screen

Ask about document **not** in corpus

Expected: *“I don’t have enough information…”*

## Speaker notes

Celebrate refusal — better than hallucination. Link Lecture 08. (~3 min)

---

# Slide 06 | Demo — do not paste

## On screen

| Never paste (examples) |
|------------------------|
| National IDs |
| Unpublished panel scores |
| Credentials |

Use **approved** channels

## Speaker notes

Do not live-demo PII. Slide only. (~2 min)

---

# Slide 07 | START EXERCISE 1 — GAFCO

## On screen

**Exercise 1 — Prompting (pairs, 18 min)**

1. Weak one-line prompt  
2. GAFCO rewrite  
3. Compare outputs  

Worksheet in [13_post_launch_platform.md](../13_post_launch_platform.md) §2 Ex1

## Speaker notes

Circulate. Common mistake: only adding adjectives. Push format + constraints. (~18 min)

---

# Slide 08 | START EXERCISE 2 — RAG

## On screen

Three questions:
1. In corpus  
2. Not in corpus  
3. Exact circular ID  

Fill worksheet: cite? VERIFY?

## Speaker notes

For Q3, if keyword search weak, note for golden set — real product feedback. (~18 min)

---

# Slide 09 | START EXERCISE 3 — Tiers

## On screen

Scenario cards A–D

Assign T0–T3 + approver

VERIFY for public-facing card

## Speaker notes

Card C (eligibility) must spark discussion — AI does not decide. (~18 min)

---

# Slide 10 | START EXERCISE 4 — Security

## On screen

Classify: paste Y/N?

Try injection phrase in **sandbox** if enabled

Report via feedback

## Speaker notes

If injection succeeds, stop exercise and file security ticket — teaching moment. (~18 min)

---

# Slide 11 | Debrief questions

## On screen

1. Where did RAG help?  
2. One habit this week?  
3. What will you NOT automate?

## Speaker notes

Solicit 3–4 volunteers. Write commitments on board. (~15 min)

---

# Slide 12 | Personal commitment

## On screen

```
I will use AI for: _______
I will VERIFY before: _______
I will NOT paste: _______
I will escalate to: _______
```

## Speaker notes

1-minute silent write. Optional share. (~5 min)

---

# Slide 13 | Quick reference & next steps

## On screen

| Wrong answer | → Feedback |
| Outage | → Status / support |
| Leak fear | → Security |

**Next:** Domain corpus workshops

## Speaker notes

Point to workshops folder. Issue completion sign-in. (~10 min Q&A)

---
