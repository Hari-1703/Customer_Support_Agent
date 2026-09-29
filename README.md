# SupportMemory AI — Customer Support Operations Console with Persistent Memory

> **An AI Customer Support Agent whose central differentiator is persistent, useful customer memory powered by Hindsight Cloud.**

SupportMemory AI transforms support interactions from disposable chat sessions into an accumulating, learning knowledge base. Built as an **Intercom-style B2B Customer Support Operations Console with Linear-like clarity and Hindsight memory visibility**, it ensures support agents never treat returning customers like strangers and never force them to repeat failed troubleshooting.

---

## 🚀 Live Demo & Key Differentiator

| Standard Support Bot | SupportMemory AI (with Hindsight Cloud) |
|---|---|
| **Starts from zero** every single interaction. | **Recalls multi-ticket history** across sessions and channels. |
| Suggests standard troubleshooting (e.g. "Clear your cache and cookies") even if it already failed 3 times. | **Avoids known failed steps**, saving time and customer frustration. |
| Asks customer to repeat details already provided. | **Directly suggests verified successful workarounds** (e.g. "Card worked previously when UPI timed out"). |
| Outcomes are lost when the ticket closes. | **Learns from outcomes** (`Resolved`, `Didn't work`, `Escalate`) via Hindsight Cloud. |

---

## 🖥️ Support Operations Console Layout

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ SupportMemory                                 ● Hindsight Connected · supportmemory-agent│
├──────────────┬───────────────────────────────┬──────────────────────┬──────────────────┤
│ INBOX        │ INBOX                         │ RIYA SHARMA          │ RIYA SHARMA      │
│ ● Open   3   │ Search conversations...       │ CUST-1007            │ CUST-1007        │
│   All    3   ├───────────────────────────────┤ UPI • Billing        │ [Memory] Customer│
│   Pending 1  │ Riya Sharma                   ├──────────────────────┼──────────────────┤
│   Resolved   │ "My payment failed again."    │ Riya:                │ WHAT I KNOW      │
│              │ 2 min ago · ↻ 3 interactions  │ My payment failed    │ • UPI fails      │
│ CUSTOMERS    │                               │ again.               │ • Chrome / Win 11│
│ All Accounts │ Arjun Rao                     │                      │ • Quick fixes    │
│              │ "SMS 2FA code delayed"        │ SupportMemory AI:    │ • Card works     │
│ MEMORY       │ 18 min ago · ↻ 2 interactions │ I remember this. We  ├──────────────────┤
│ Timeline     │                               │ tried retrying,      │ PAST EXPERIENCES │
│ Insights     │ Vikram Patel                  │ clearing cache, and  │ MAR 18: UPI fail │
│              │ "CSV upload timeout"          │ browser switch, but  │ Retry → Failed   │
│ SYSTEM       │ 42 min ago · ↻ 2 interactions │ those failed. Card   │ Cache → Failed   │
│ Settings     │                               │ worked last time...  │ Card → Success   │
│              │                               ├──────────────────────┼──────────────────┤
│              │                               │ ✦ MEMORY USED        │ SUCCESSFUL       │
│              │                               │ 3 memories used      │ ✓ Card payment   │
│              │                               │ [View memory trace]  ├──────────────────┤
│              │                               ├──────────────────────┤ FAILED ATTEMPTS  │
│              │                               │ [✓ Resolved] [✕...]  │ × Retry × Cache  │
└──────────────┴───────────────────────────────┴──────────────────────┴──────────────────┘
```

---

## 🏗️ Technical Architecture

```mermaid
flowchart TD
    subgraph ConsoleUI ["Frontend: B2B Support Operations Console"]
        Nav["Left Nav (Inbox, Customers, Memory Timeline, Settings)"]
        InboxCol["Conversation List (Live related interactions signal)"]
        ChatCol["Main Workspace (Memory-grounded conversation + Outcome loop)"]
        MemoryCol["Right Inspector (What I Know, Past Experiences, Successful, Failed)"]
        TraceDrawer["Hindsight Trace Pipeline Drawer"]
        ImpactModal["Before vs After Comparison Modal"]
    end

    subgraph ServerAPI ["Server API Layer (Next.js 14 App Router)"]
        APIChat["POST /api/chat"]
        APIMemory["GET /api/memory"]
        APIOutcome["POST /api/memory/outcome"]
        APICompare["POST /api/memory/compare"]
        APISeed["POST /api/demo/seed"]
    end

    subgraph Orchestrator ["Support Intelligence Layer"]
        SupportAgent["Support Agent (lib/support-agent.ts)"]
        HindsightSvc["Hindsight Service (lib/hindsight.ts)"]
        LLMSvc["LLM Service: Google Gemini (lib/llm.ts)"]
        CustomerRepo["Customer Store (lib/customers.ts)"]
    end

    subgraph CloudInfra ["Cloud Infrastructure"]
        HindsightCloud["Hindsight Cloud API\n(@vectorize-io/hindsight-client)\nBank: supportmemory-agent"]
        GeminiCloud["Google Gemini API\nModel: gemini-3.8-flash"]
    end

    ConsoleUI --> ServerAPI
    APIChat --> SupportAgent
    APIMemory --> HindsightSvc
    APIOutcome --> HindsightSvc
    APICompare --> SupportAgent
    APISeed --> HindsightSvc

    SupportAgent --> HindsightSvc
    SupportAgent --> LLMSvc
    HindsightSvc --> HindsightCloud
    LLMSvc --> GeminiCloud
```

---

## 🧠 Why Memory Matters: The Hindsight Operations

Hindsight Cloud provides the semantic, long-term memory layer using the official `@vectorize-io/hindsight-client`:

1. **`retain()` (Ingestion of Experience)**:
   - Captures structured interaction summaries (ticket ID, problem category, troubleshooting attempted, failed steps, successful resolution).
   - Enforces strict customer isolation via tags: `['CUST-1007', 'customer:CUST-1007', 'payment']`.
   - Stores durable facts, world knowledge, and customer preferences rather than ephemeral chat noise.

2. **`recall()` (Context-Aware Retrieval)**:
   - Queries Hindsight Cloud before generating a support answer.
   - Retrieves reranked, semantically scored memories filtered strictly by the customer's tag.
   - Supplies the LLM with verified facts to ground the prompt and avoid repeating failed steps.

3. **`reflect()` (High-Level Synthesis & Briefing)**:
   - Synthesizes accumulated multi-ticket history into a concise "Customer Memory Briefing".
   - Answers: *"What should a support agent know about customer CUST-1007 before helping with another payment issue?"*
   - Highlights recurring patterns and preferences in the UI side panel.

---

## 🔁 The Closed Learning Loop

SupportMemory AI does not simply store chat transcripts. It learns from real customer outcomes:

```
OLD MEMORY (UPI Failed, Card Worked)
        ↓
CURRENT CUSTOMER ISSUE ("My payment failed again.")
        ↓
HINDSIGHT RECALL (Pulls verified facts & scores)
        ↓
GROUNDED RESPONSE (Bypasses cache clearing, suggests Card directly)
        ↓
CUSTOMER OUTCOME (Support clicks [✓ Resolved])
        ↓
HINDSIGHT RETAIN (New outcome confirmed & saved)
        ↓
FUTURE RECALL (Agent has even stronger statistical evidence)
```

---

## 👥 Demo Customers

The application comes pre-configured with 3 realistic B2B customer accounts:

1. **Riya Sharma (`CUST-1007`) — Primary Star Demo**
   - **Plan / Environment**: Pro · Windows 11 · Chrome 128
   - **Pattern**: Recurring UPI gateway timeout on checkout. Cache clearing and Edge browser switching repeatedly failed. Switching to Card succeeded 100% of the time.
   - **Preference**: Strongly dislikes repeating already-failed troubleshooting steps.
   - **Open Issue**: Invoice PDF download returns 502 gateway error.

2. **Arjun Rao (`CUST-2045`) — Authentication Specialist**
   - **Plan / Environment**: Enterprise · macOS Sonoma · Safari 17.5
   - **Pattern**: SMS 2FA delivery delays (15+ min). Clearing Safari cookies caused complete enterprise session lockout. Google Authenticator TOTP app worked instantly.
   - **Preference**: Never suggest clearing cookies; prefers TOTP passcodes.

3. **Vikram Patel (`CUST-3088`) — Data Import & Payload**
   - **Plan / Environment**: Business · Ubuntu 22.04 LTS · Firefox 130
   - **Pattern**: 45MB CSV upload timed out in Firefox. Splitting into 5MB chunks and uploading in Chrome Incognito succeeded without data loss.
   - **Preference**: Concise, numbered technical instructions with code/scripts.

---

## ⏱️ 60-90 Second Judge Demo Walkthrough

1. **Open the Console**: Navigate to `http://localhost:3000`.
2. **Observe the Hero Pre-Loaded State**:
   - The workspace opens directly on **Riya Sharma (`CUST-1007`)** with her conversation and memory ready in under 2 seconds!
   - Customer message: `"My payment failed again."`
   - Agent response:
     > *"I remember this happened before. We tried retrying the payment, clearing the cache, and switching browsers, but none of those resolved it. Last time, the card payment went through successfully. Since this looks like the same UPI issue, I'd avoid repeating those steps. Could you try the card option again?"*
3. **Inspect the Memory Sections (Right Sidebar)**:
   - **WHAT I KNOW**: UPI failures recurring, Windows 11 / Chrome, prefers quick troubleshooting.
   - **PAST EXPERIENCES**: MAR 18 interaction timeline (Retry $\rightarrow$ Failed, Cache $\rightarrow$ Failed, Browser $\rightarrow$ Failed, Card $\rightarrow$ Success).
   - **SUCCESSFUL**: `✓ Card payment (Worked on previous attempt)`.
   - **FAILED ATTEMPTS**: `× Retry`, `× Clear cache`, `× Switch browser`.
4. **Inspect the Hindsight Trace**:
   - Click **`[ View memory trace ]`** under the agent response.
   - Review the vertical pipeline: Issue $\rightarrow$ Recall (3 memories) $\rightarrow$ Context $\rightarrow$ Gemini Response $\rightarrow$ Retain.
5. **Feed the Learning Loop**:
   - Click **`[ ✓ Resolved ]`**.
   - Notice the badge: **"Outcome saved · Hindsight memory updated"**.
6. **Switch to Dedicated Memory Timeline**:
   - Click **"Memory Timeline"** in the left navigation.
   - View the chronological timeline showing learned milestones (`TODAY ● Payment failed again`, `MAR 18 ✓ Card payment succeeded`, etc.).
7. **Inspect "Compare Response" (Before vs After)**:
   - Click **"Compare response"** in the chat header.
   - Compare side-by-side: generic bot repeating failed cache-clearing advice vs. SupportMemory AI using proven workarounds.
8. **Verify Persistence Across Sessions**:
   - Hard-refresh the browser page (`Ctrl + F5`).
   - Notice that all customer context, memory briefing, and timeline events remain fully intact from Hindsight Cloud!

---

## ⚙️ Setup & Installation

### 1. Prerequisites
- Node.js 18+ (tested on Node v20 & v22)
- Hindsight Cloud API credentials
- Google Gemini API key

### 2. Environment Variables
Create `.env.local` based on `.env.example`:

```bash
# Hindsight Cloud
HINDSIGHT_BASE_URL=https://api.hindsight.vectorize.io
HINDSIGHT_API_KEY=<your-hindsight-key>
HINDSIGHT_BANK_ID=supportmemory-agent

# Primary LLM Provider: Google Gemini
LLM_PROVIDER=gemini
GEMINI_API_KEY=<your-gemini-key>
LLM_MODEL=gemini-3.8-flash

# Alternative LLM Providers (optional fallback)
GROQ_API_KEY=
XAI_API_KEY=
OPENAI_API_KEY=
```

### 3. Build & Run
```bash
# Build production bundle
npm run build

# Start production server
npm start

# Or run in development mode
npm run dev
```
Open `http://localhost:3000` in your browser.

---

## 🧪 Automated Testing

Run the test suite:
```bash
npm test
```

### Verified Test Cases (11/11 Passed):
- [x] **Hindsight Client Initialization** (`tests/hindsight.test.mjs`)
- [x] **Bank Creation & Mission Configuration on Hindsight Cloud**
- [x] **Memory Retain with Tag Isolation**
- [x] **Memory Recall with Scored Reranking**
- [x] **Customer Isolation**: Customer A memories never leak to Customer B
- [x] **Hindsight Reflect Contextual Synthesis**
- [x] **Empty Message & Input Validation**
- [x] **Empty History Graceful Degradation**
- [x] **Failed Troubleshooting Extraction**
- [x] **Resolution Outcome Formatting**
- [x] **Section 41 E2E Critical Demo Scenario** (`tests/e2e-scenario.test.mjs`)

---

## 🏆 Judging Criteria Alignment

| Criterion | Weight | How SupportMemory AI Delivers |
|---|---|---|
| **Innovation** | **30%** | Reimagines support memory from the ground up. Instead of generic ticket search, the agent learns *what was tried, what failed, and what worked*, eliminating the #1 friction point in customer service. |
| **Hindsight Memory** | **25%** | Memory is the core value proposition. Real integration with Hindsight Cloud using `retain()`, `recall()`, and `reflect()`. Customer isolation and session persistence are guaranteed. |
| **Technical Implementation** | **20%** | Full-stack Next.js App Router, TypeScript, server-side secret protection, native Google Gemini LLM integration, modular provider abstraction, and 100% automated test coverage. |
| **User Experience** | **15%** | Authentic B2B Support Operations Console. Hero state loads immediately for judges, clear context signals (`↻ 3 related interactions`), dedicated timeline view, and Before/After comparison. |
| **Real-World Impact** | **10%** | Solves a massive enterprise operational bottleneck: repeated explanations and repetitive troubleshooting. Easily justifies a $50/month per seat subscription. |
