# 🛡️ SentinelAI — Autonomous Cybersecurity Incident Response System

> **Don't build an AI assistant that tells a security analyst what to do. Build an AI system that detects the problem, investigates it, makes a decision, performs a safe automated response, and documents what happened.**

SentinelAI is a full-stack, AI-powered **Security Operations Center (SOC) platform** that automates the complete incident-response workflow **end-to-end**:

```
DETECT → INVESTIGATE → DECIDE → RESPOND → DOCUMENT
```

It ingests simulated security telemetry, identifies attack patterns (brute force, port scanning, abnormal logins, traffic anomalies), scores risk 0–100 with explainable factors, runs an AI investigation agent, evaluates a response policy, **executes safe containment actions in a simulated demo environment**, notifies the administrator, and generates a professional incident report — all with minimal human intervention.

> ⚠️ **Safety first:** this is a **defensive** demonstration project. All attack data is fictional (RFC-1918 / TEST-NET IPs), and every automated action operates **only inside the application's simulated environment**. No real credentials, exploits, network scanning, or external systems are ever touched.

---

## ✨ Features

| Area | What SentinelAI does |
| --- | --- |
| 🖥️ **SOC Dashboard** | Live stats, threat-level gauge (LOW/MEDIUM/HIGH/CRITICAL), charts (events over time, severity distribution, threat categories, automated vs manual, response time) |
| 📡 **Real-time Event Feed** | Live security event table with severity, status and AI risk scores — auto-refreshing |
| 🧠 **Detection Engine** | Pattern rules for brute-force, port scan, abnormal login and traffic-anomaly behavior |
| 📊 **Risk Engine** | Explainable 0–100 risk score with per-factor breakdown (e.g. `+35 Repeated authentication failures`) |
| 🤖 **AI Investigation Agent** | Structured classification + confidence + reasoning + recommended response (OpenAI API when a key is present, deterministic fallback engine otherwise) |
| ⚡ **Autonomous Response Engine** | Tools: `analyze_event`, `calculate_risk`, `create_incident`, `block_demo_ip`, `unblock_demo_ip`, `send_alert`, `generate_report`, `update_incident` — with a policy that auto-contains high-confidence threats |
| 👮 **Human Approval Mode** | Toggle autonomous vs approval mode; approval mode pauses with an **APPROVE / REJECT** prompt |
| 📋 **Incident Management** | Full incident records with the complete automation timeline (first failed login → threat identified → risk calculated → IP blocked → incident created → report generated) |
| 📄 **Incident Reports** | Auto-generated professional reports with evidence, actions and follow-up — exportable as Markdown, printable to PDF |
| 🧪 **Attack Simulation Center** | One-click `Simulate Brute Force / Port Scan / Abnormal Login / Traffic Anomaly / Normal Traffic` — watch the entire pipeline run live |
| 🎙️ **Voice / Text Command Control** | Browser speech-to-text + typed fallback sharing one command interpreter: *"What's the current threat level?"*, *"Investigate the latest high-risk incident"*, *"Start a brute force simulation"*, *"Generate an incident report"* — with **safety confirmation** for state-changing actions |
| 🔔 **Notifications** | In-app alert toasts + notification center (🚨 SentinelAI Security Alert) |
| 🗂️ **Action Audit Log** | Timestamped proof of every executed action (trigger, AI confidence, result) |

---

## 🏗️ Architecture

```
┌────────────────────────────────────────────────────────────────┐
│  Frontend (Next.js App Router · React 19 · Tailwind CSS v4)     │
│  Dashboard · Incidents · Events · AI Investigation · Actions    │
│  Reports · Attack Simulation · Settings · Voice Command UI      │
└──────────────────────────┬─────────────────────────────────────┘
                           │  REST API (Next.js Route Handlers)
┌──────────────────────────▼─────────────────────────────────────┐
│  /api/*  — dashboard · events · incidents · simulate · voice   │
│            actions · alerts · reports · settings · analyses     │
└──────────────────────────┬─────────────────────────────────────┘
┌──────────────────────────▼─────────────────────────────────────┐
│  Automation Engine (src/lib/engine) — modular services          │
│                                                                │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │ Detection   │→ │ Risk Engine  │→ │ AI Investigation     │   │
│  │ Engine      │  │ (0–100,      │  │ Agent (LLM or        │   │
│  │ (patterns)  │  │  explainable)│  │  deterministic)      │   │
│  └─────────────┘  └──────────────┘  └──────────┬───────────┘   │
│                                                ↓                │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│  │ Response    │← │ Policy       │← │ Pipeline Orchestrator│   │
│  │ Engine      │  │ (EXECUTE/    │  │ detect→investigate→  │   │
│  │ (tools +    │  │  MONITOR/    │  │ decide→respond→      │   │
│  │  audit log) │  │  LOG)        │  │ document             │   │
│  └─────────────┘  └──────────────┘  └──────────────────────┘   │
│  Voice Command Interpreter → same pipeline                     │
└──────────────────────────┬─────────────────────────────────────┘
                           │  Drizzle ORM
┌──────────────────────────▼─────────────────────────────────────┐
│  PostgreSQL — events · incidents · incident_timeline ·          │
│  ai_analyses · automation_actions · alerts · reports ·          │
│  voice_commands · blocked_demo_ips · system_settings · users    │
└─────────────────────────────────────────────────────────────────┘
```

### Repository structure

```
├── src/
│   ├── app/                        # Next.js App Router
│   │   ├── api/                    # REST API (route handlers)
│   │   │   ├── dashboard/          #   aggregated SOC metrics
│   │   │   ├── events/             #   event feed
│   │   │   ├── incidents/          #   incident list / detail / decide / investigate / report
│   │   │   ├── simulate/           #   run the full automation pipeline
│   │   │   ├── voice/              #   voice & text command interpreter
│   │   │   ├── actions/ alerts/ reports/ analyses/ settings/
│   │   ├── incidents/ events/ investigation/ actions/ reports/
│   │   ├── simulation/ settings/   # pages
│   ├── components/                 # UI: shell, charts, voice modal, notifications, tables
│   ├── db/
│   │   ├── index.ts                # PostgreSQL connection (Drizzle)
│   │   └── schema.ts               # all tables
│   └── lib/
│       ├── engine/                 # THE AUTOMATION ENGINE (modular backend)
│       │   ├── detection.ts        #   Threat Detection Engine
│       │   ├── risk.ts             #   Risk Scoring Engine (0–100, explainable)
│       │   ├── investigator.ts     #   AI Investigation Agent (LLM + fallback)
│       │   ├── response.ts         #   Response Engine (tools, policy, reports)
│       │   ├── pipeline.ts         #   Orchestrator: detect→investigate→decide→respond→document
│       │   ├── simulation.ts       #   Fake-log generator (controlled demo data)
│       │   ├── voice.ts            #   Voice/Text command interpreter (intent → action)
│       │   └── stats.ts            #   Dashboard aggregations
│       ├── seed.ts                 # idempotent first-run seed (settings + demo history)
│       └── live.ts                 # client-side polling helpers
└── drizzle.config.json             # Drizzle Kit config
```

---

## 🚀 Local Installation

### Prerequisites

- **Node.js** 18.18+ (20.x recommended)
- **PostgreSQL** 14+ running locally
- npm (or pnpm/yarn)

### 1. Clone & install

```bash
git clone https://github.com/<your-user>/sentinelai.git
cd sentinelai
npm install
```

### 2. Configure environment

Create a `.env` file in the project root:

```env
# PostgreSQL connection string
DATABASE_URL=postgresql://postgres:postgres@127.0.0.1:5432/app_db

# OPTIONAL — AI investigation via OpenAI-compatible API.
# Without this, SentinelAI uses its built-in deterministic
# investigation engine (the app is fully functional either way).
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4o-mini
# OPENAI_BASE_URL=https://api.openai.com/v1   # optional override
```

### 3. Create the database & apply the schema

```bash
# create the database if it doesn't exist
createdb app_db   # or: psql -c "CREATE DATABASE app_db;"

# push the Drizzle schema (creates all tables)
npx drizzle-kit push
```

The app also **seeds itself** on first request (default settings, 24h of baseline traffic and two historical incidents) — no manual seeding needed.

### 4. Run it

```bash
# development
npm run dev
# → http://localhost:3000

# production
npm run build
npm run start
```

### 5. Run the automated tests

The core detection and explainable risk engines are covered by a Vitest suite:

```bash
npx vitest run
```

For watch mode during development:

```bash
npx vitest
```

The suite verifies risk-level boundaries, the exact 91/100 brute-force demo score, score capping, normal-traffic handling, brute-force detection, port-scan detection, abnormal-login prioritization, traffic anomalies, and strongest-detection selection.

### 6. Run the demo scenario (judge-ready, ~60 seconds)

1. Open the **Dashboard** → threat level **LOW**, active incidents **0**.
2. Go to **Attack Simulation** → click **Simulate Brute Force**.
3. Watch the live pipeline: ~87 fake auth-failure events → detection engine flags the pattern → AI investigates (classification **Brute Force Attempt**, confidence ~94%) → risk engine computes **91/100 CRITICAL** with factor breakdown → policy check → **demo IP automatically blocked** → incident **INC-2026-XXXXX** created → administrator notified → evidence preserved → report generated.
4. Back on the **Dashboard**: Critical Incidents **1**, Auto-Resolved **1**, Threat Level **HIGH**.
5. Open the incident → complete timestamped **timeline** from first failed login to report generation.
6. Try the **voice mic** in the header: *"Show me the latest critical incidents"*, *"Start a port scan simulation"*, *"Block 192.168.1.105"* (with confirmation prompt).
7. Toggle **Approval Mode** in Settings and re-run a simulation to see the APPROVE / REJECT flow.

---

## 🧪 How the automation works

1. **Simulation** generates realistic fake logs (e.g. 87 `Authentication Failure` events from `192.168.1.105` in ~3 minutes).
2. **Detection Engine** groups events by source and matches behavioral rules: ≥12 auth failures ⇒ *Potential Brute Force*; ≥8 probed ports ⇒ *Potential Port Scan*; ≥5 unusual-hour logins ⇒ *Abnormal Login*; volume ≥ 3× baseline ⇒ *Traffic Anomaly*.
3. **Risk Engine** produces an explainable 0–100 score (e.g. `+35 repeated failures, +25 frequency, +15 unusual pattern, +6 multiple services, +10 negative reputation`).
4. **AI Investigation Agent** (OpenAI when configured, deterministic fallback otherwise) returns structured JSON: `classification`, `confidence`, `risk_score`, `severity`, `reasoning_summary`, `recommended_action`, `recommended_steps`.
5. **Response Policy**: `EXECUTE` when confidence ≥ 82% and risk ≥ 65; otherwise `MONITOR` or `LOG`.
6. **Response Engine** executes safe demo tools — `update_incident → block_demo_ip → send_alert → preserve_evidence → generate_report` — each logged to the **audit log** and the incident **timeline**.
7. **Human Approval Mode** pauses at step 6 and shows **APPROVE / REJECT** instead.
8. **Voice commands** run through the *same* pipeline via a shared interpreter (intents: threat status, list/investigate incidents, start simulation, generate report, block/unblock IP with confirmation).

## 🔌 API Overview

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/api/dashboard` | Aggregated SOC metrics + live activity |
| GET | `/api/events?limit=&severity=&status=` | Event feed |
| GET | `/api/incidents` | Incident list with event counts |
| GET | `/api/incidents/:id` | Incident detail (timeline, actions, analysis, evidence, report) |
| POST | `/api/incidents/:id/decide` | `{decision: "approve"\|"reject"}` |
| POST | `/api/incidents/:id/investigate` | Re-run the AI investigation |
| GET/POST | `/api/incidents/:id/report` | Fetch / regenerate the report |
| POST | `/api/simulate` | `{type: "brute_force"\|"port_scan"\|"abnormal_login"\|"traffic_anomaly"\|"normal"}` — runs the full pipeline |
| GET | `/api/actions` | Action audit log |
| GET/POST | `/api/alerts` | Notifications + mark read |
| POST | `/api/voice` | `{transcript, confirm?}` — command interpreter |
| GET | `/api/voice/history` | Voice activity history |
| GET/POST | `/api/settings` | Response mode, blocked IPs, demo reset |
| GET | `/api/analyses`, `/api/reports` | AI analyses & generated reports |

## 🛡️ Safety & Ethics

- All attack events are **fabricated** (RFC-1918/TEST-NET addresses, fake usernames).
- `block_demo_ip` only writes to the local `blocked_demo_ips` table — **never** touches real infrastructure.
- The LLM (if enabled) receives structured incident context only and is instructed to remain defensive.
- This project is intended for education, demos, and security-automation research.

## 🧰 Tech Stack

Next.js 16 (App Router) · React 19 · TypeScript · Tailwind CSS v4 · Recharts · Drizzle ORM · PostgreSQL · Web Speech API · OpenAI-compatible API (optional)

## 📄 License

MIT — see [LICENSE](LICENSE).

---

_Made for hackathons, SOC demos and security-education showcases. Built with ❤️ by the SentinelAI team._
