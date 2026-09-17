# Client Onboarding Automation Agent (n8n)

An automation that kicks in the moment a new client submits an intake form — drafting a personalized welcome email, generating a client-specific Terms & Services document, and notifying the team, all without anyone lifting a finger.


## 🎯 Problem It Solves
Onboarding a new client usually means manually writing a welcome email, pulling up a terms template and customizing it, and remembering to tell the team a new client just signed on. Doing this by hand for every client is repetitive and easy to delay. This agent handles all three the instant the intake form is submitted.

## ⚙️ How It Works

1. **On form submission** — triggers when a new client fills out an intake form (service, requirements, contact details, etc.)
2. **Edit Fields** — normalizes the submitted form data into clean fields for the agents to use
3. Two AI agents then run off the same intake data:
   - **AI Agent** (Groq Chat Model + Memory) → **Code** (formats the output) → **Create a draft** (Gmail) — drafts a personalized welcome/onboarding email for the client, then **Send a message** (Slack) notifies the team that a new client has been onboarded
   - **AI Agent1** (Azure OpenAI + Memory) → **terms and services** (a second model step that generates the agreement text) → **Create a draft1** (Gmail) — drafts a Terms & Services email tailored to what the client submitted

## 🧩 Tech Stack
| Component | Role |
|---|---|
| n8n | Workflow orchestration |
| Form Trigger | Client intake |
| Set node | Data normalization |
| Groq (LangChain Chat Model) | Fast welcome-email drafting |
| Azure OpenAI (LangChain Chat Model) | Terms & Services drafting |
| Memory (LangChain) | Keeps each agent's conversation context consistent |
| Gmail node | Creates draft emails (kept as drafts, not auto-sent, so a human reviews before sending) |
| Slack node | Team notification |

## 💡 Why This Matters
Two different LLM providers are used deliberately here — Groq for fast, low-latency drafting and Azure OpenAI for the more formal, higher-stakes terms document — showing how to pick the right model for each sub-task rather than defaulting to one provider for everything. Emails are created as **drafts**, not sent automatically, so a human always reviews before anything goes out to a client — an important safety guardrail for client-facing communication.

## 🚀 How to Recreate This
1. In n8n, add a Form Trigger for your client intake fields, followed by a Set node to clean up the submitted data.
2. Build the welcome-email branch: a Groq Chat Model feeding an AI Agent (with Memory), a Code node to format its output, a Gmail node (operation: Create Draft), and a Slack node to post a notification.
3. Build the terms branch: an Azure OpenAI Chat Model feeding a second AI Agent (with Memory), a follow-up model step to generate the terms text, and a Gmail node (operation: Create Draft).
4. Connect both branches to the same form-submission trigger so they run in parallel off the same intake data.
5. Connect credentials: Groq API, Azure OpenAI API, Gmail OAuth2, Slack OAuth2.

*(This project is shared here as a portfolio reference — the exported workflow file isn't included, but I'm happy to walk through the setup or build a version for your specific onboarding process.)*
