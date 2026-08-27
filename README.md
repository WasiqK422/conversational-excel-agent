# Conversational Spreadsheet Management Agent

An AI-powered agent that manages spreadsheet data entirely through natural language chat — no manual clicking, no formulas, no navigating menus. Just tell it what you want, and it handles the rest.

## 🎥 Demo

[Watch the demo video]https://lnkd.in/p/d7gnCqiB

## 🚀 What It Does

- **Add records** — "Add a new student named Ali, age 22"
- **Update records** — "Change Ali's age to 25"
- **Delete records** — "Remove the student named Ali"
- **Custom reordering** — "Put all students named Wasiq first, then Ali, then the rest" — the agent reads the current data, sorts it according to instructions, and rewrites the entire sheet automatically

## 🧠 How It Works

The system is built around an **AI Agent with tool-calling architecture**:

1. A user sends a natural language command via chat
2. The AI Agent (powered by Groq's LLM) interprets intent and decides which tool(s) to call
3. Before making changes, the agent reads the live sheet data to make informed decisions (e.g., calculating the next available ID)
4. For complex operations like reordering, the agent delegates the task to a dedicated **sub-workflow** — a clear/parse/rewrite pipeline

This mirrors how real-world agentic AI systems are architected: a central reasoning agent coordinating specialized tools and sub-processes.

## 🛠️ Tech Stack

- **n8n** — workflow orchestration and automation
- **Groq (OSS LLMs)** — fast, low-latency LLM inference for agent reasoning
- **Google Sheets API + OAuth2** — data storage and live read/write operations
- **LangChain-style AI Agent (Tool Calling)** — decision-making and action execution
- **JavaScript (Code node)** — custom data parsing logic

## 📁 Architecture

- `main-agent-workflow.json` — the core chat agent, with tools for Read, Add, Update, and Delete operations
- `reorder-sub-workflow.json` — a delegated sub-workflow that clears, parses, and rewrites sorted data

## 🐛 Challenges Solved

- Fixed silent prompt-passing failures between chat triggers and the AI Agent using explicit expressions
- Migrated from Gemini to Groq after hitting free-tier rate limits (5 req/min), and tuned memory/token usage to stay within Groq's limits
- Solved a Row_ID calculation problem by giving the agent a dedicated "read" tool so it could reason over live data instead of guessing
- Debugged sub-workflow-as-tool wiring issues (correct Tool port connections, workflow activation, JSON string parsing via a Code node) to enable safe, delegated bulk operations
- Learned and implemented safe testing practices (sandbox/test-copy data) after an early destructive-operation mistake

## 📌 Status

This is an actively evolving project. Planned improvements include multi-user support, Microsoft Excel/OneDrive integration, and confirmation safeguards for destructive operations.

---

Built by [Wasiq Tasleem](https://github.com/WasiqK422) | [LinkedIn]https://lnkd.in/p/d7gnCqiB
