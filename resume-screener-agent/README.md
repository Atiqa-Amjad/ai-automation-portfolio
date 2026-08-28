# AI Resume Screener & Candidate Ranking Agent (n8n)

An AI recruiter assistant that evaluates every job application for genuine role fit — not just keyword matching — and automatically flags top candidates for the recruiter.

## 🎯 Problem It Solves
Recruiters spend hours skimming resumes for a handful of qualified candidates. This agent screens every application the moment it's submitted — scoring fit, summarizing strengths/gaps, and instantly notifying the recruiter when a strong candidate applies — so nobody has to manually triage a resume pile.

## ⚙️ How It Works

1. **Job Application Form** (n8n hosted Form Trigger) — a shareable public form (no coding required) where candidates enter their name, email, job title, optional key requirements, and upload their resume (PDF).
2. **Extract Resume Text** — converts the uploaded PDF into plain text.
3. **Normalize Application Data** — cleans and structures the form fields.
4. **Screen Candidate & Score Fit** (AI Agent, OpenAI/Gemini + Structured Output Parser) — evaluates fit with a psychology-informed lens rather than pure keyword matching:
   - **candidate_score** (1–10)
   - **fit_level**: Strong Fit / Moderate Fit / Weak Fit
   - **key_strengths** and **key_gaps** — specific, honest, and never fabricated
   - **summary** — an overall recommendation
5. Parallel steps run off the AI's output:
   - **Save Candidate to Sheet** — logs the full ranked record for every applicant
   - **Send Application Received Email** — confirms receipt to the candidate
   - **Is Strong Fit? → Notify Recruiter** — if the score is high, the recruiter gets an instant email with the AI's justification

## 🧩 Tech Stack
| Component | Role |
|---|---|
| n8n | Workflow orchestration |
| Form Trigger | No-code hosted application form with file upload |
| Extract From File | PDF-to-text extraction |
| OpenAI / Google Gemini (LangChain Agent node) | Candidate evaluation and scoring |
| Structured Output Parser | Forces clean, predictable JSON output |
| Gmail node | Candidate confirmation + recruiter alert |
| Google Sheets node | Candidate log / lightweight ATS |

## 🔐 Guardrails Built Into the Prompt
- Never fabricates experience or qualifications not present in the resume
- Evaluates genuine job fit (skills, experience, communication) rather than simple keyword overlap
- Only escalates to the recruiter for genuinely strong matches, reducing noise

## 🚀 Setup
1. Import `workflow.json` into your n8n instance.
2. Connect credentials: OpenAI or Google Gemini API, Gmail OAuth2 (×2 nodes), Google Sheets OAuth2.
3. Select your target spreadsheet/sheet in the **Save Candidate to Sheet** node.
4. Replace the placeholder recruiter email in **Notify Recruiter of Strong Fit** with a real inbox.
5. Activate the workflow and share the **Form Trigger**'s public URL as your application link.
