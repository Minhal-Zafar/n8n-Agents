# n8n Agents: AI Workflow Automations

**Live demo:** https://minhal-zafar.github.io/n8n-Agents/ shows each workflow as an interactive graph. Click any node to read its real prompt, code or settings, or run a scenario to see the path an item takes.

Eleven n8n workflows built around AI agents: customer support, RAG, voice booking, sales triage, finance, reporting, content and DevOps. Every workflow is a plain JSON file you can import into your own n8n.

| Workflow | What it does | Key nodes |
|---|---|---|
| [AI Email Triage with Approval & HubSpot](workflows/email-triage-approval-hubspot.json) · *production* | Classifies replies into 8 categories, GPT drafts, a manager approves by email (send-and-wait), sends via SMTP, upserts contact and conversation in HubSpot | IMAP, LangChain classifier, OpenAI, HubSpot |
| [AI Email Triage to Drafts](workflows/email-triage-drafts.json) · *production* | 6-category classifier, one dedicated reply agent per category, files the email in IMAP folders and saves the reply as a draft | IMAP, LangChain agents, HTTP |
| [WhatsApp AI Support Agent](workflows/whatsapp-ai-support-agent.json) | GPT agent with order-status and product-search tools and per-customer Postgres memory; hands off to Slack when a human is needed | WhatsApp, Agent, Postgres memory, HTTP tools |
| [RAG Knowledge-Base Chatbot](workflows/rag-knowledge-base-chatbot.json) | Drive documents are chunked, embedded and stored in Qdrant; a public chat agent answers only from them and cites the source | Google Drive, Qdrant, OpenAI embeddings, Chat trigger |
| [Voice Agent Appointment Booking](workflows/voice-agent-appointment-booking.json) | Backend for a Retell AI phone agent: live availability, booking with confirmation email, call log with summary and sentiment | Webhooks, Google Calendar, Gmail, Sheets |
| [Lead Capture & AI Scoring](workflows/lead-capture-ai-scoring.json) | Hosted form → GPT scores the lead 0–100 as structured JSON → HubSpot upsert → Slack for hot leads, nurture email for the rest | Form, LLM chain + output parser, HubSpot |
| [Invoice Processing](workflows/invoice-processing-ai-extraction.json) | Gmail PDF → text → AI field extraction → validation → Google Sheets, unclear invoices to manual review | Gmail trigger, Extract from file, Information extractor |
| [Weekly KPI Report (GA4)](workflows/weekly-kpi-report-ga4.json) | Every Monday: GA4 week-over-week in code, GPT 3-bullet summary, email + Slack | Schedule, Google Analytics, Code, LLM chain |
| [YouTube → LinkedIn Repurposing](workflows/youtube-to-linkedin-repurposing.json) | New video → GPT LinkedIn draft → Telegram Approve/Decline → publish only on approval | RSS, LLM chain, Telegram send-and-wait, LinkedIn |
| [GitHub PR AI Code Review](workflows/github-pr-ai-code-review.json) | On every PR: fetch diff, drop lockfiles, GPT-4.1 review (bugs, security, secrets, tests), post as PR review, notify Slack | GitHub trigger, HTTP, Code, GitHub review |
| [Workflow Error Alerting](workflows/ops-error-alerting.json) | Error workflow for all of the above: Slack alert with failing node and execution link, logged to Sheets | Error trigger, Slack, Sheets |

The two *production* workflows run for a B2B supplier. Client names, addresses and internal URLs have been replaced with placeholders. The other nine are original, deploy-ready builds.

## Patterns used throughout

- **Classifier → branch → specialised agent**: one LLM decides the route, and each route has its own focused prompt.
- **Human in the loop**: anything that replies to a customer or publishes content waits for a one-click approval (`sendAndWait`).
- **Structured output**: scores, invoice fields and sender details come back as validated JSON, not free text.
- **Agents with tools and memory**: HTTP tools, a vector-store tool, Postgres chat memory keyed by customer.
- **Idempotent CRM writes**: HubSpot upsert keyed by email.
- **Operability**: a shared error workflow, plus validation branches that route uncertain items to people.

## Import a workflow

1. In n8n: **Workflows → Import from file** → choose a file from [`workflows/`](workflows/).
2. Open each node with a red badge and select your own credentials (OpenAI, Gmail, Slack, HubSpot…).
3. Replace the `YOUR_…` placeholders (spreadsheet IDs, channel IDs, phone number ID). Each workflow's sticky note lists its setup.
4. Run once with test data, then activate.

## How the workflows were checked

Every node type, `typeVersion`, parameter name, option value and connection was validated against the node definitions shipped in n8n's own packages (`n8n-nodes-base`, `@n8n/n8n-nodes-langchain`). That catches broken imports. It doesn't replace a test run against your real accounts.

## Site

`index.html` is the static demo page served by GitHub Pages. It reads the JSON files in `workflows/` directly, so the graphs always match the code.

---

**Minhal Zafar**, AI / ML & automation engineer · [GitHub](https://github.com/Minhal-Zafar) · [LinkedIn](https://www.linkedin.com/in/minhal-zafar/)
