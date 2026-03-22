# Product Requirements Document
## Medical Case Learning App (AI-Powered Clinical Reasoning Platform)

---

## 1. Overview

A mobile/web application that helps medical students and junior doctors develop clinical reasoning skills through AI-generated spaced repetition case studies, real-time coaching, and performance feedback.

---

## 2. Goals

- Help users practice clinical reasoning through realistic, AI-generated patient cases
- Provide adaptive difficulty using spaced repetition
- Give actionable performance feedback after each case
- Support both self-directed learning and structured review

---

## 3. User Flow

### 3.1 Authentication
- Phone number input
- OTP verification (dummy/fake OTP for MVP)
- On success → redirect to Home Dashboard

### 3.2 Home Dashboard
Users can:
- **Start a new case** (Spaced Repetition Case Generator)
- **Review due cases** (cases scheduled for review)
- **View performance & insights**

### 3.3 Model Selection
Before starting a session, user selects a mode:

| Mode | Description |
|------|-------------|
| Clinical Reasoning Coach | Step-by-step guidance through a case with feedback |
| Spaced Repetition Generator | Creates cases/questions for repeated practice over time |

### 3.4 Case Setup
User inputs:
- **Topic** (optional) — e.g., "heart failure"
- **Difficulty** — Easy / Medium / Hard

Case is generated via Gemini API (generate case function).

### 3.5 Case Interaction Screen (Core UX)

**Three-panel layout:**

| Panel | Content |
|-------|---------|
| Left | Case progression: Chief Complaint → History → Exam → Labs |
| Center | Chat interface with AI |
| Right | Notes / Differential diagnosis list |

### 3.6 Performance & Insight (Post-Case)
- AI gives Socratic feedback on user's reasoning
- LLM reviews user input vs. ideal response
- Highlights what the user did well and what they missed

---

## 4. Features

### MVP Features
- [ ] Phone auth (dummy OTP)
- [ ] Case generation via Gemini (topic + difficulty)
- [ ] Chat-based case interaction
- [ ] Three-panel case UI
- [ ] Post-case performance feedback
- [ ] Basic spaced repetition scheduling

### Post-MVP
- [ ] Real OTP (Twilio / Firebase)
- [ ] User performance history & analytics
- [ ] Specialty-specific case libraries
- [ ] Peer comparison / leaderboard

---

## 5. Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React / Next.js |
| Backend | FastAPI (Python) |
| Auth | uvicorn + FastAPI, dummy OTP for MVP |
| AI / LLM | Gemini API (case generation + feedback) |
| Database | PostgreSQL or Supabase |
| Spaced Repetition | SM-2 algorithm or similar |

---

## 6. Non-Functional Requirements

- Mobile-responsive design
- Response latency < 3s for case generation
- Support 100+ concurrent users (MVP target)
- All medical content clearly labeled as educational, not clinical guidance

---

## 7. Out of Scope (MVP)

- Real clinical data / EHR integration
- Actual OTP SMS
- Payment / subscription
- Offline mode
