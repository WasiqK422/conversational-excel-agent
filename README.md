# Conversational Spreadsheet Management Agent

An AI-powered agent that manages spreadsheet data entirely through natural language chat — no manual clicking, no formulas, no navigating menus. Just tell it what you want, and it handles the rest.

## 🎥 Demo

[Watch the demo video]https://lnkd.in/p/d7gnCqiB

## 🚀 What It Does

- **Add records** — "Add a new student named Ali, age 22"
- **Update records** — "Change Ali's age to 25"
- **Delete records** — "Remove the student named Ali"
- **Custom reordering** — "Put all students named Wasiq first, then Ali, then the rest" — the agent reads the current data, sorts it according to instructions, and rewrites the entire sheet automatically

## 🆕 Multi-Platform Support

The agent now supports **both Google Sheets and Microsoft Excel (via OneDrive/Microsoft Graph API)** as backends — the same conversational interface works across either platform, with the user simply specifying which one to use (e.g., "add this to Excel").

- Integrated directly with the Microsoft Graph API using HTTP Request nodes (bypassing limitations in the built-in Excel connector) for reading, adding, updating, and deleting rows.
- Set up OAuth2 authentication via a custom Azure App Registration, including API permissions and consent flows.
- The existing safety layer (confirmation before destructive actions, live re-verification) automatically extended to Excel operations without additional engineering — a result of designing the safety rules at the agent-prompt level rather than hardcoding them per platform.

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

 🛡️ Safety & Reliability Layer

After the initial version was working, I stress-tested it and found real failure modes — then fixed them:

- **Confirmation before destructive actions:** Delete and Reorder operations now require the agent to preview the exact change and get explicit user confirmation before executing.
- **Stale-data protection:** The agent re-reads the live sheet immediately before acting (not relying on data it read earlier), preventing it from acting on outdated row positions.
- **Hardcoded parameter bug:** Found and fixed a case where a "destructive" tool had a hardcoded row number instead of a dynamic AI-filled value — meaning it always deleted the same row regardless of the agent's decision.
- **Fail-safe validation:** A dedicated validation step now checks incoming data is well-formed *before* any data is cleared — if validation fails, the workflow aborts safely instead of wiping data.
- **Automatic backup:** Before any reorder/rewrite operation, the current sheet state is automatically snapshotted to a separate backup file.
- **Reliable data sourcing:** Fixed an intermittent bug where a processing node pulled data from the wrong upstream node — now explicitly sources data from the original trigger to guarantee consistency regardless of pipeline length.

- **Cross-platform tool schema issues:** Diagnosed why an HTTP-based Excel tool returned empty values despite a "successful" API response — the `$fromAI()` calls needed explicit type and description arguments to be correctly interpreted by the agent, not just a field name.

## 📌 Status

This is an actively evolving project. Planned improvements include multi-user support, Microsoft Excel/OneDrive integration, and confirmation safeguards for destructive operations.

---

Built by [Wasiq Tasleem](https://github.com/WasiqK422) | [LinkedIn]https://lnkd.in/p/d7gnCqiB
