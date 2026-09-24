# TruthLens — Misinformation Triage & Verification Platform

**Hackathon ID:** AZIS-MKG6SQ  
**Hackathon:** Azisly.ai Hackathon  
**Track:** TruthLens — Civic Tech  
**Participants:** D. Jason, Fawaz Ahmed

**Live Application:** https://truth-lens-client.vercel.app/

TruthLens is a transparent misinformation-triage and human-review platform built for the Azisly.ai Hackathon.

It allows the public to submit viral claims, automatically detects predefined risk signals, keeps new claims in an `UNVERIFIED` state, provides a structured reviewer workflow, and publishes reviewed claims in a public feed.

---

## 🌟 Key Features

### 1. Submit a Claim

Public users can submit claims without creating an account.

Users provide:

- Claim text
- Source Platform:
  - WhatsApp
  - X
  - Instagram
  - Other
- Category:
  - Politics
  - Health
  - Finance
  - Other

Every newly submitted claim:

- Starts with `UNVERIFIED` status
- Records its creation timestamp
- Is preserved as submitted

---

### 2. Deterministic Risk Flags

TruthLens uses predefined rules instead of an LLM to identify risk signals.

#### Sensational

Detects alarmist phrases such as:

- `breaking`
- `shocking`
- `share before deleted`

#### Shouting

Detects claims where more than 50% of alphabetic characters are uppercase.

#### Unsourced

Detects the absence of verifiable source links.

#### Risk Level

Risk is derived deterministically from the number of detected flags:

| Flags | Risk Level |
| ----- | ---------- |
| 0     | LOW        |
| 1     | MEDIUM     |
| 2+    | HIGH       |

> Risk levels are triage-priority signals. They are not factual truth verdicts.

---

## 3. Human Reviewer Workflow

Reviewers can inspect submitted claims and record one of the following verdicts:

- `VERIFIED_TRUE`
- `VERIFIED_FALSE`
- `MISLEADING`
- `UNVERIFIED`

The reviewer can also add:

- An explanatory context note
- Review timestamp

The reviewer evaluates the original submitted claim without modifying its content.

For hackathon evaluation, the reviewer workflow is directly accessible without authentication, as required by the track instructions.

---

## 4. Public Feed & Filtering

The public feed displays:

- Claim text
- Category
- Review status
- Risk level
- Submission/review information

Users can filter claims by:

- Category
- Review Status

### Feed Ordering — DP1

Claims are ordered by:

1. Risk level
2. Recency

Higher-risk claims appear earlier for triage.

### Unverified Visibility — DP2

Unreviewed claims remain publicly visible and are clearly marked:

`UNVERIFIED`

This allows users to distinguish between claims awaiting review and claims that have already been reviewed.

---

## 5. Claim Detail View

Users can open a claim to view its complete details, including:

- Full claim text
- Source platform
- Category
- Risk level
- Detected risk signals
- Audit timeline
- Reviewer verdict
- Reviewer context note

The original claim text remains immutable after submission.

---

## 6. Community "I Agree" Reaction

Reviewed claims optionally support a community agreement reaction.

The system:

- Allows users to agree with a reviewed claim
- Uses `localStorage` to enforce one vote per browser
- Allows the user to toggle their vote off

> Community agreement represents community sentiment. It is not treated as evidence or factual verification.

---

# 📐 Decision Points

The three required product decisions are implemented and documented in [`DECISIONS.md`](./DECISIONS.md).

### DP1 — Feed Order

Claims are ordered by:

`HIGH → MEDIUM → LOW → Recency`

Higher-risk claims surface earlier for triage.

### DP2 — Visibility

Unreviewed claims remain publicly visible with a prominent `UNVERIFIED` status.

### DP3 — Editing

Claims are immutable after creation.

Reviewers evaluate the exact text originally submitted by the user.

---

# 🛠️ Technology Stack

### Frontend

- React 19
- Vite 7
- Vanilla CSS

### Backend

- Node.js
- Express 5
- Mongoose 8

### Database

- MongoDB Atlas
- In-memory fallback for offline/demo resilience

### Testing

- Native Node.js test runner
- `node --test`

---

# 🏗️ System Architecture

```text
                    ┌─────────────────────┐
                    │       User          │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   React Frontend    │
                    │      + Vite         │
                    └──────────┬──────────┘
                               │
                         REST API
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Express Backend   │
                    │                     │
                    │ Claim Submission    │
                    │ Risk Detection      │
                    │ Review Workflow     │
                    │ Feed & Filtering    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    MongoDB Atlas    │
                    │                     │
                    │      Claims         │
                    └─────────────────────┘
```
