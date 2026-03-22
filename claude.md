# CLAUDE.md — AI Coding Assistant Instructions

## Project Summary

This is an AI-powered medical case learning app. Users practice clinical reasoning through LLM-generated patient cases with spaced repetition scheduling and post-case feedback.

---

## Architecture

```
frontend/          → React / Next.js
backend/           → FastAPI (Python)
  routes/
    auth.py        → Phone + OTP endpoints
    cases.py       → Case generation + retrieval
    feedback.py    → Performance analysis
  services/
    claude.py      → Claude API wrapper
    srs.py         → Spaced repetition scheduler
database/          → Supabase / PostgreSQL schemas
```

---

## Key Conventions

### Backend (FastAPI)
- Run with `uvicorn main:app --reload`
- All routes return consistent JSON: `{ data, error, status }`
- Auth via phone number + OTP stored in Redis (dummy for MVP: always accept `123456`)
- JWT tokens for session management after OTP verification

### Frontend (React/Next.js)
- Use Tailwind CSS for styling
- Three-panel layout for case screen: use CSS Grid (`grid-cols-[280px_1fr_280px]`)
- Chat component streams responses from backend SSE endpoint
- Store session state in Zustand

### Gemini Integration
- Use `anthropic` Python SDK
- Always include system prompt with medical education context (see `services/claude.py`)

---

## Case Generation Prompt Pattern

```python
CASE_SYSTEM_PROMPT = """
You are a medical education AI. Generate realistic clinical cases for medical students.
Format cases as structured JSON with fields:
- chief_complaint, history, physical_exam, labs, diagnosis, teaching_points
Difficulty levels: easy (common presentations), medium (atypical), hard (rare/complex)
"""
```

---

## Spaced Repetition

- Use SM-2 algorithm
- Track: `ease_factor`, `interval`, `repetitions`, `next_review_date` per case per user
- Cases due for review appear in Home Dashboard "Review Due" section

---

## Auth Flow (MVP)

```
POST /auth/send-otp   { phone_number }  → always succeeds, dummy OTP = "123456"
POST /auth/verify-otp { phone_number, otp } → returns JWT if otp == "123456"
```

---

## Do Not

- Do NOT use real patient data in any seed/test data
- Do NOT deploy with dummy OTP enabled — add env flag `USE_DUMMY_OTP=true` for dev only
- Do NOT hardcode Gemini API key — use `.env` / environment variables
- Do NOT show raw LLM output to users without validation/sanitization

---

## Environment Variables

```env
GEMINI_API_KEY=
DATABASE_URL=
JWT_SECRET=
USE_DUMMY_OTP=true   # set false in production
REDIS_URL=           # for OTP storage
```

---

## Running Locally

```bash
# Backend
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000

# Frontend
cd frontend
npm install
npm run dev
```
