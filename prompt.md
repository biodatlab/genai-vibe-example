From `prd.md` and `claude.md` follow the below instructions to create an application.

# Initial App Generation Prompt

Use this prompt with Claude, Cursor, or any AI coding assistant to scaffold the full application.

---

## PROMPT

```
Build a full-stack medical case learning web app. Here are the complete requirements:

## Stack
- Frontend: Next.js 14 (App Router) + Tailwind CSS + Zustand
- Backend: Python FastAPI + uvicorn
- AI: Google Gemini API (gemini-1.5-flash for generation, gemini-1.5-pro for feedback)
- Database: Supabase (PostgreSQL)
- Auth: Phone number + OTP (dummy OTP = "123456" for MVP)

---

## Pages & Features to Build

### 1. Login Page (`/login`)
- Phone number input field
- "Send OTP" button → POST /auth/send-otp
- OTP input (6 digits)
- "Verify" button → POST /auth/verify-otp
- On success: store JWT, redirect to /dashboard
- Dummy OTP: always accept "123456"

### 2. Home Dashboard (`/dashboard`)
Three sections:
- **Start New Case** button → opens case setup modal
- **Review Due** list → cases scheduled for spaced repetition review today
- **Performance** summary card → total cases, average score, streak

### 3. Case Setup Modal
Fields:
- Topic input (optional, e.g. "heart failure", "sepsis")
- Difficulty selector: Easy / Medium / Hard
- Mode selector: "Clinical Reasoning Coach" or "Spaced Repetition Review"
- "Generate Case" button

On submit: POST /cases/generate → returns case object → redirect to /case/[id]

### 4. Case Interaction Screen (`/case/[id]`)
Three-panel layout (CSS Grid: 280px | 1fr | 280px):

**Left Panel — Case Progression**
Show case stages as a vertical stepper:
1. Chief Complaint
2. History
3. Physical Exam
4. Labs / Investigations
5. Diagnosis
Each stage revealed progressively as user advances.

**Center Panel — Chat Interface**
- Chat bubbles (user right, AI left)
- Text input + send button
- AI streams responses via SSE
- Chat guides user through clinical reasoning

**Right Panel — Notes & Differentials**
- Editable notes textarea
- Differential diagnosis list (user can add/remove items)
- "Submit Diagnosis" button when ready

### 5. Performance & Feedback Screen (`/case/[id]/feedback`)
After case completion:
- Overall score (0–100)
- "What you did well" list
- "What you missed" list
- Key teaching points from the case
- "Next Case" button

---

## API Endpoints to Build

### Auth
- POST /auth/send-otp → { phone_number } → { success: true }
- POST /auth/verify-otp → { phone_number, otp } → { token: "jwt..." }

### Cases
- POST /cases/generate → { topic?, difficulty, mode } → case object
- GET /cases/due → list of cases due for spaced repetition review
- POST /cases/{id}/message → { message, history[] } → streamed AI response (SSE)
- POST /cases/{id}/complete → { diagnosis, notes } → triggers feedback generation

### Feedback
- GET /cases/{id}/feedback → { score, strengths[], gaps[], teaching_points[] }

---

## Gemini Prompts to Implement

### Case Generation
System: "You are a medical education AI creating cases for medical students.
Generate a clinical case as JSON with fields:
chief_complaint, history, physical_exam, labs, diagnosis, teaching_points, difficulty.
Difficulty easy=common textbook, medium=atypical presentation, hard=rare/complex."

User: "Generate a {difficulty} case{' about ' + topic if topic else ''}."

### Chat / Coaching
System: "You are a Socratic clinical reasoning coach. Guide the student through
this case: {case_json}. Ask one question at a time. Do not reveal the diagnosis.
Encourage the student to reason through each step."

### Feedback
System: "You are a medical educator. Review this student's case interaction.
Case: {case_json}
Student conversation: {chat_history}
Student's final diagnosis: {diagnosis}
Return JSON: { score: 0-100, strengths: [], gaps: [], teaching_points: [] }"

---

## Database Schema (Supabase)

```sql
-- users
id, phone_number, created_at

-- cases
id, user_id, topic, difficulty, mode, case_data (jsonb), created_at

-- case_sessions
id, case_id, user_id, chat_history (jsonb), diagnosis, notes,
completed_at, score, feedback (jsonb)

-- spaced_repetition
id, user_id, case_id, ease_factor, interval_days,
repetitions, next_review_date
```

---

## File Structure to Generate

```
/
├── frontend/
│   ├── app/
│   │   ├── login/page.tsx
│   │   ├── dashboard/page.tsx
│   │   ├── case/[id]/page.tsx
│   │   └── case/[id]/feedback/page.tsx
│   ├── components/
│   │   ├── CaseSetupModal.tsx
│   │   ├── ChatInterface.tsx
│   │   ├── CaseProgression.tsx
│   │   ├── DifferentialList.tsx
│   │   └── FeedbackCard.tsx
│   └── store/
│       └── useAppStore.ts  (Zustand)
│
└── backend/
    ├── main.py
    ├── routes/
    │   ├── auth.py
    │   ├── cases.py
    │   └── feedback.py
    ├── services/
    │   ├── gemini.py
    │   └── srs.py
    └── models/
        └── schemas.py
```

---

## Environment Variables Needed
GEMINI_API_KEY=your_key_here
SUPABASE_URL=your_url
SUPABASE_ANON_KEY=your_key
JWT_SECRET=random_secret
USE_DUMMY_OTP=true

---

Start by generating the complete file structure, then implement each component
fully with no placeholder TODOs. Prioritize the case interaction screen
(3-panel layout + chat + AI streaming) as the core UX.
```
