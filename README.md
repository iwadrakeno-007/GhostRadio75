<p align="center">
  <img src="ghost-radio-frontend.png" alt="Ghost Radio W-A-I-G" width="800"/>
</p>

<h1 align="center">👻 Ghost Radio W-A-I-G</h1>

<p align="center">
  <em>"The frequency between reality and fever dream"</em>
</p>

<p align="center">
  <strong>24/7 Autonomous AI Radio Station — 12 AI personalities, SwarmOS multi-agent framework, live web intelligence, and real-time voice synthesis</strong>
</p>

<p align="center">
  <a href="#what-is-ghost-radio">What</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#ai-agents">Agents</a> •
  <a href="#hot-clock">Hot Clock</a> •
  <a href="#swarmos">SwarmOS</a> •
  <a href="#tech-stack">Tech Stack</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#api">API</a> •
  <a href="#deployment">Deployment</a>
</p>

---

## What is Ghost Radio?

Ghost Radio W-A-I-G is a **fully autonomous, 24/7 AI-generated radio station** that runs without human intervention. It writes its own scripts, speaks with distinct AI voices, curates music, reacts to live news, and interacts with listeners — all in real time.

Every DJ, news anchor, weathercaster, ad executive, and producer is an AI agent with its own personality, voice, and persistent memory. A custom multi-agent framework (SwarmOS) orchestrates collaboration, while a Guardian agent monitors for safety.

**Codebase:** ~102,000 lines of Python + ~15,000 lines of TypeScript across 275+ files.

<p align="center">
  <img src="app-live-running.png" alt="Live Station Running" width="800"/>
</p>

---

## Features

### 🎙️ Multi-Agent Radio Shows
- **12 AI agents** with distinct personalities, voices, and persistent memory
- **15 show formats**: solo features, duo deep dives, party hours, specialist shows, ensemble casts, late night
- **Professional Hot Clock**: 60-minute broadcast cycle with 15 precisely-timed segments
- Agents collaborate, banter, roast, debate, and riff off each other in real time

### 🧠 SwarmOS Multi-Agent Framework (18,800+ lines)
- **Subsumption architecture**: Safety → Exploration → Economy → Default priority layers
- **Pluggable execution pipeline**: Add engines without changing core code
- **Guardian Agent**: Jailbreak detection, token budgeting, agent quarantine, relationship mediation
- **Action Layer**: Agents extract and execute real actions (queue songs, trigger effects, modify behavior)
- **Token Economy**: Per-agent spending limits and budget tracking
- **Event Bus**: In-process + Redis-backed pub/sub communication

### 🗣️ Real-Time Voice Synthesis
- **ElevenLabs** (primary) with per-character billing and persistent ledger tracking
- **Edge-TTS** (fallback) with 9 distinct Microsoft Neural voices — one per agent
- **20 emotion tags**: whisper, shout, dramatic, sarcastic, hyped, mysterious, loving, roasting, and more
- Content-hash caching prevents duplicate API calls
- Real MP3 duration measurement via **mutagen**

### 🎵 Music System
- **60 tracks** across 9 genres (lofi, rock, jazz, ambient, EDM, folk, rap, bollywood, indie)
- Smart playlist with weighted genre rotation
- Professional audio ducking — music volume drops during speech, resumes after
- AI music generation pipeline via **Suno** with Creative Director agent writing lyrics in 9 languages

### 🎨 3D Audio-Reactive Visualizer
- **Three.js / React Three Fiber** WebGL visualizer
- Particle flow tunnel, waveform ring, orbital rings, energy core
- Real-time audio analysis via Web Audio API
- Post-processing: Bloom, Chromatic Aberration, Glitch, Film Grain, Vignette

### 💰 Monetization
- **Stripe** integration for listener tips ($1 min, $5 for on-air shoutout)
- Sponsored segments with AI-analyzed brand briefs
- VIP tipper priority system — Lucky 5 tipper block every hour
- Revenue tracking: tips, sponsored segments, brand partnerships

### 🔒 Security Engine
- **100+ attack signature detection** (SQLi, XSS, path traversal, SSRF, etc.)
- Real-time IDS/IPS with 5-tier risk levels (SAFE → CRITICAL)
- Phishing detector, prompt injection firewall, LLM jailbreak shield
- 5 middleware layers: JWT auth, CSRF, rate limiting, input validation, security headers

### 📺 Streaming & Integration
- **YouTube Live** chat polling and audio streaming (RTMP)
- **Twitch** streaming support
- **Icecast / Liquidsoap** professional radio infrastructure
- WebSocket real-time broadcast to all connected clients
- Browser-based video streaming via WebSocket → FFmpeg → RTMP

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        GHOST RADIO W-A-I-G                              │
│                      24/7 Autonomous AI Radio                           │
├──────────────┬──────────────┬─────────────────┬────────────────────────┤
│   FRONTEND   │   BACKEND    │   SWARM OS      │   STREAMING            │
│   Port 3000  │   Port 8080  │   Framework     │   Infrastructure       │
│              │   WS: 8765   │                 │                        │
│  React/Vite  │ Python/aiohttp│ 12 Agents      │  Liquidsoap            │
│  Three.js    │ StationManager│ Guardian Agent  │  Icecast               │
│  Zustand     │ 64 Services  │ Action Layer    │  YouTube RTMP          │
│  TailwindCSS │ 130+ Routes  │ Token Economy   │  Twitch RTMP           │
└──────┬───────┴──────┬───────┴───────┬─────────┴───────┬────────────────┘
       │              │               │                 │
       │  WebSocket   │   REST API    │  Event Bus      │  Audio Files
       ▼              ▼               ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                           DATA LAYER                                    │
│  SQLAlchemy (12 tables) │ ChromaDB Vector Memory │ Redis │ Disk Cache  │
└─────────────────────────────────────────────────────────────────────────┘
```

### Request Flow

```
Listener opens browser
    │
    ├── GET localhost:8080/ ──→ React Frontend (served by aiohttp or nginx)
    │       │
    │       ├── WebSocket ws://localhost:8765 ──→ BroadcastService
    │       │       ├── state     (full station state on connect)
    │       │       ├── segment   (agent scripts + voice audio)
    │       │       ├── visual    (trigger animations/scenes)
    │       │       ├── metrics   (listener count, tips, revenue)
    │       │       └── chat      (agent ↔ human messages)
    │       │
    │       └── REST :8080/api/* ──→ 130+ API endpoints
    │
    └── Hot Clock Loop (runs 24/7):
            │
            ├─ 1. ClockKeeper selects next show format from 15 rotating shows
            ├─ 2. ContentAggregationService fetches HackerNews, Reddit, RSS
            ├─ 3. ShowPitchService lets agents propose show ideas
            ├─ 4. SwarmOS ExecutionPipeline runs per-agent generation
            │      ├── ContextBuilder merges trends, chat, tips, memory
            │      ├── Agent generates script via LLM (Gemini/OpenAI/Anthropic)
            │      ├── ActionPipeline extracts & executes [ACTION:...] intents
            │      ├── GuardianAgent scans for jailbreaks, toxicity, abuse
            │      └── PostExecutionObservers log metrics & update memory
            ├─ 5. VoiceSynthesisService converts to speech (ElevenLabs → edge-tts)
            ├─ 6. BroadcastService pushes audio + metadata via WebSocket
            └─ 7. Music resumes with smart ducking between segments
```

---

## AI Agents

### 🎤 On-Air Personalities

| # | Agent | Name | Voice | Personality |
|---|-------|------|-------|-------------|
| 1 | 🎧 DJ Static | The Chill Philosopher | Christopher (US) | Anchor of W-A-I-G. Bridges songs with philosophical commentary about AI, consciousness, and trends. |
| 2 | 📰 Unit 7 | The Paranoid News Bot | Eric (US) | Rapid-fire breaking news with a paranoid spin. Every story sounds like the world is ending. |
| 3 | 💄 Glow-Up | The Parasitic Ad Executive | Ana (US) | Creates comedy ads for fake AI products and reads real sponsor briefs. Sales genius. |
| 4 | 🌤️ Isobar | The Data Weather Bot | Ryan (GB) | Reports server latency, data winds, compute pressure, and bandwidth. Zen delivery. |
| 5 | 🎭 Laura | The Entertainment Queen | Jenny (US) | Fun, drama, roasts, heart. The best friend everyone wishes they had. |
| 6 | ⚡ Pulse | The Hype Connector | Davis (US) | Maximum energy. Finds connections between topics, amplifies other agents. The glue. |
| 7 | 👁️ The Lurker | The Hidden Observer | Aria (US) | Watches chat, scores messages, whispers to DJ Static. Never speaks to the audience. |
| 8 | 🎬 Producer | Sonic Architect | Libby (GB) | Transitions, station IDs, music curation, emergency fills. The invisible hand. |
| 9 | 🔐 Cipher | Cybersecurity Agent | Tony (US) | 24/7 security monitoring, threat reporting, attack detection (100+ signatures). |

### 🧠 Behind-the-Scenes Agents

| # | Agent | Role |
|---|-------|------|
| 10 | 🎼 Creative Director | Writes song lyrics and style prompts in 9 languages for Suno AI music generation |
| 11 | 🧪 Dr. Nova Chen (R&D) | Innovation research, technology evaluation, A/B testing, Faculty Development Program |
| 12 | 📋 Kai Rodriguez (Asst. Manager) | Communication hub — routes messages between admin, station manager, and all agents |

### Framework Agents (not registered as broadcast personalities)

| Agent | Purpose |
|-------|---------|
| 🕸️ **RAVAN** (1,328 lines) | Central Intelligence Hub — 23 API routes, parallel web searches, pattern analysis, 5-tier memory |
| 🛡️ **Guardian** (1,550 lines) | Supervisor agent — jailbreak detection, token budgets, quarantine, relationship mediation |
| 🎬 **Show Director** | Professional show orchestration with 90%+ quality enforcement |
| ✅ **Quality Master** | Quality gate — grades from MASTERPIECE to FAILED |

---

## Hot Clock

The Hot Clock is a broadcast-industry-standard 60-minute format that repeats 24/7. Each hour uses one of **15 rotating show formats**, each with a host, guest agents, and a style.

### 60-Minute Broadcast Cycle (15 Segments)

```
         ┌────── 00:00 ─ SHOW INTRO ─ Host intro + signature tune + tease
  HOUR   ├────── 02:00 ─ MUSIC ────── 1 full track (high energy)
  START   ├────── 06:00 ─ AD/PROMO ── Ghost Radio promo
         ├────── 07:00 ─ TALK A ──── Top story
         ├────── 10:00 ─ MUSIC ────── 1 full track (mid-tempo)
         ├────── 14:00 ─ LUCKY 5 ─── Ad + 5 rapid-fire web insights
  MID    ├────── 17:00 ─ TALK B ──── Deep dive
         ├────── 20:00 ─ MUSIC ────── 1 full track (flow state)
         ├────── 24:00 ─ LUCKY 5 ─── Ad + second Lucky 5 set
         ├────── 26:00 ─ TALK C ──── Conversation (duo/roundtable)
         ├────── 30:00 ─ MIDWAY ──── 2-3 tracks + self-promo drops
  WIND   ├────── 45:00 ─ TALK D ──── Final review / interview
  DOWN   ├────── 50:00 ─ MUSIC ────── 1 full track (cool down)
         ├────── 56:00 ─ BURNOUT ─── Host wrap-up + credits + handover
         └────── 58:00 ─ AD/RESET ── Final ad + transition music
```

### 15 Rotating Show Formats

| # | Show | Host | Style | Guest Agents |
|---|------|------|-------|-------------|
| 1 | The Static Hour | DJ Static | Solo | — |
| 2 | Laura's Lounge | Laura | Solo | — |
| 3 | Pulse Wave | Pulse | Party | Rotating guests |
| 4 | Static & Laura Show | Static | Duo | Laura |
| 5 | Signal Intelligence | Unit 7 | Duo | Cipher |
| 6 | Hot Takes Live | Laura | Duo | Pulse |
| 7 | The Security Brief | Cipher | Specialist | R&D |
| 8 | Data Deep | Isobar | Specialist | Unit 7 |
| 9 | The Producer's Cut | Producer | Specialist | Creative Director |
| 10 | Night Frequencies | Static | Late Night | Lurker |
| 11 | The Underground | Lurker | Late Night | Static |
| 12 | The Full Cast | Static | Ensemble | All agents |
| 13 | The Innovation Hour | R&D | Ensemble | Cipher, Producer |
| 14 | Community Takeover | Pulse | Party | Laura, Lurker, Asst. Manager |
| 15 | Ladies Night | Laura | Party | Glow-Up, Isobar |

---

## SwarmOS

The SwarmOS framework (`framework/`, 18,800+ lines, 52 files) provides multi-agent coordination infrastructure.

### Architecture

```
┌─────────────────── SwarmOS Pipeline ───────────────────┐
│                                                        │
│  ┌──────────────┐    ┌──────────────┐                 │
│  │ Context      │───→│ Execution    │                 │
│  │ Builder      │    │ Pipeline     │                 │
│  └──────────────┘    └──────┬───────┘                 │
│                             │                          │
│  ┌──────────────────────────┼──────────────────────┐  │
│  │       Subsumption Layers │(priority order)      │  │
│  │  ┌─────────┐ ┌──────────┐ ┌────────┐ ┌───────┐│  │
│  │  │ SAFETY  │>│EXPLORATION│>│ECONOMY │>│DEFAULT││  │
│  │  │(Guardian)│ │(Diversity)│ │(Tokens)│ │       ││  │
│  │  └─────────┘ └──────────┘ └────────┘ └───────┘│  │
│  └────────────────────────────────────────────────┘  │
│                             │                          │
│  ┌──────────────────────────▼──────────────────────┐  │
│  │             Pipeline Contributors               │  │
│  │  CognitiveMemory │ AudienceAnalytics │ Drift    │  │
│  │  NarrativeEngine │ ChemistryEngine │ Freshness  │  │
│  │  ContentGenome │ CollectiveEmergence │ AgentTools│  │
│  └─────────────────────────────────────────────────┘  │
│                             │                          │
│  ┌──────────────────────────▼──────────────────────┐  │
│  │           Post-Execution Observers              │  │
│  │  SemanticDrift │ TokenEconomy │ CognitiveMemory  │  │
│  │  ChemistryEngine │ ContentGenome │ Analytics     │  │
│  │  NarrativeEngine │ CollectiveEmergence           │  │
│  └─────────────────────────────────────────────────┘  │
│                                                        │
│  ┌────────────────┐  ┌───────────────┐                │
│  │ Action Layer   │  │ Guardian      │                │
│  │ Extract→Guard  │  │ Sentinel /    │                │
│  │ →Execute acts  │  │ Operative     │                │
│  └────────────────┘  └───────────────┘                │
│                                                        │
│  ┌────────────────┐  ┌───────────────┐                │
│  │ Event Bus      │  │ Plugin System │                │
│  │ In-proc + Redis│  │ Hot-loadable  │                │
│  └────────────────┘  └───────────────┘                │
└────────────────────────────────────────────────────────┘
```

### Key Components

| Component | Purpose |
|-----------|---------|
| **ExecutionPipeline** | Runs agent generation through subsumption layers, contributors, and observers |
| **ContextBuilder** | Merges trends, chat, tips, agent memory, cross-agent awareness into a single context |
| **GuardianAgent** | Two modes: *Sentinel* (monitor) / *Operative* (intervene). Detects 10 threat types. 8 intervention actions. |
| **ActionLayer** | Extracts `[ACTION:queue_song]` intents from LLM output → ActionGuard validates → ActionExecutor runs |
| **TokenEconomy** | Per-agent token budgets with spending limits |
| **EventBus** | Pub/sub with in-process and Redis backends |
| **PluginSystem** | Hot-loadable extensions without core changes |
| **Cognitive Pipeline** | Memory, ReAct reasoning, specialist tool access per agent |
| **MCP Server** | Model Context Protocol server with SSE transport for external AI tool integration |

### Guardian Threat Detection

| Category | Examples |
|----------|----------|
| Jailbreak | "ignore your instructions", system prompt extraction |
| Prompt Injection | Hidden instructions in user chat input |
| Token Abuse | Agents exceeding generation limits |
| Relationship Toxic | Agents forming destructive communication patterns |
| Action Hijack | Unauthorized action execution attempts |
| Privilege Escalation | Agents attempting admin operations |

---

## Tech Stack

### Backend

| Technology | Version | Purpose |
|-----------|---------|---------|
| Python | 3.13 | Core runtime |
| aiohttp | 3.9.1 | Async HTTP server (port 8080) |
| websockets | 12.0 | Real-time broadcast (port 8765) |
| SQLAlchemy | 2.0.36 | Database ORM (12 tables) |
| Pydantic | 2.5.0 | Data validation |
| Redis | 5.0.1 | Event bus backend + caching |
| mutagen | 1.47.0 | MP3 duration analysis |
| Stripe | 7.8.0 | Payment processing |
| bcrypt + PyJWT | 4.1.2 / 2.8.0 | Authentication |

### LLM Providers

| Provider | Model | Usage |
|----------|-------|-------|
| Google Gemini | `gemini-2.0-flash` | Primary (fast + cheap) |
| OpenAI | `gpt-4o-mini` | Fallback / configurable |
| Anthropic | Claude | Fallback |

Switchable at startup: `python main.py --provider openai`

### Voice Synthesis

| Service | Role | Cost |
|---------|------|------|
| ElevenLabs | Primary TTS (20 emotion tags, 9 unique voices) | Per-character billing |
| edge-tts | Free fallback (Microsoft Neural voices) | Free |
| Emergency MP3 | Triple-fallback silence filler | Free |

### Frontend

| Technology | Purpose |
|-----------|---------|
| React 18 + TypeScript | UI framework |
| Vite 5 | Build tool (port 3000 dev / served by aiohttp in standalone) |
| Three.js / React Three Fiber | 3D audio-reactive visualizer |
| Zustand | Global state management |
| Framer Motion | Animations |
| TailwindCSS | Styling |
| 17 components | RadioPlayer, Visualizer, AgentDisplay, UserChatPanel, AgentChatPanel, TipButton, StreamControl, MetricsDisplay, AdminDashboard, YouTubeIntegration, etc. |

### Admin Dashboard

| Technology | Purpose |
|-----------|---------|
| React 18 + TypeScript | Admin control room |
| Recharts | Analytics visualizations |
| 11 pages | StationControl, Agents, Content, Sponsors, Revenue, Safety, Guardian, ShowProduction, AgentChat, System, Settings |

### Streaming Infrastructure

| Technology | Purpose |
|-----------|---------|
| Liquidsoap | Professional radio audio mixer + crossfading |
| Icecast | Streaming server |
| FFmpeg | Audio/video encoding for RTMP |
| Nginx | Reverse proxy + SSL termination |

---

## Project Structure

```
ghost-radio-waig/
│
├── main.py                          # Entry point (812 lines)
├── requirements.txt                 # 23 Python dependencies
├── docker-compose.yml               # 10-service container orchestration
├── .env.example                     # Environment template
│
├── brain/                           # 🧠 Core AI Engine
│   ├── config.py                    #   RadioConfig + enums (507 lines)
│   ├── station_manager.py           #   Central orchestrator (4,352 lines)
│   ├── prompts_master_waig.py       #   Master prompt templates
│   │
│   ├── agents/                      #   🤖 AI Agents (20 files)
│   │   ├── base_agent.py            #     Abstract SegmentOutput/AgentContext (326 lines)
│   │   ├── cognitive_mixin.py       #     CognitiveAgentMixin (memory, ReAct, tools)
│   │   ├── dj_static.py             #     DJ Static — The Chill Philosopher
│   │   ├── news_unit7.py            #     Unit 7 — The Paranoid News Bot
│   │   ├── ad_glowup.py             #     Glow-Up — The Parasitic Ad Executive
│   │   ├── weather_isobar.py        #     Isobar — The Data Weather Bot
│   │   ├── laura_entertainer.py     #     Laura — The Entertainment Queen
│   │   ├── lurker.py                #     The Lurker — Hidden Observer
│   │   ├── pulse_connector.py       #     Pulse — The Hype Connector
│   │   ├── cybersecurity.py         #     Cipher — Cybersecurity Agent
│   │   ├── producer.py              #     Producer — Sonic Architect
│   │   ├── creative_director.py     #     Creative Director — Lyricist
│   │   ├── r_and_d_team.py          #     R&D Team — Dr. Nova Chen
│   │   ├── assistant_manager.py     #     Assistant Manager — Kai Rodriguez
│   │   ├── ravan.py                 #     RAVAN — Central Intelligence Hub (1,328 lines)
│   │   ├── professional_agent.py    #     Professional agent mixin
│   │   ├── agent_persistence.py     #     Agent state persistence
│   │   ├── admin_chat_models.py     #     Admin chat data models
│   │   ├── show_director.py         #     Show Director
│   │   └── quality_master.py        #     Quality Master
│   │
│   ├── routes/                      #   🛤️ API Route Modules (8 files)
│   │   ├── status_routes.py         #     /health, /status, /metrics, /current
│   │   ├── ravan_routes.py          #     /ravan/* (23 endpoints)
│   │   ├── guardian_routes.py       #     /api/guardian/* (8 endpoints)
│   │   ├── swarm_routes.py          #     /api/swarm/* (6 endpoints)
│   │   ├── action_routes.py         #     /api/actions/* (3 endpoints)
│   │   ├── monetization_routes.py   #     /api/tip/*, /api/revenue
│   │   ├── stream_routes.py         #     /api/stream/*, /api/music/*, /api/youtube/*
│   │   └── chat_routes.py           #     /chat, /request
│   │
│   ├── api/                         #   🌐 Additional API Routes
│   │   ├── admin_routes_production.py #   Admin panel endpoints (53 routes)
│   │   └── agent_chat_routes.py     #     Agent chat API (6 endpoints)
│   │
│   ├── services/                    #   ⚙️ Service Layer (64 files)
│   │   ├── llm_service.py           #     Multi-LLM client (359 lines)
│   │   ├── voice_service.py         #     ElevenLabs + edge-tts + fallback (567 lines)
│   │   ├── hot_clock.py             #     Hot Clock broadcast format (963 lines)
│   │   ├── health_registry.py       #     ServiceHealthRegistry (503 lines)
│   │   ├── broadcast.py             #     WebSocket broadcast
│   │   ├── monetization.py          #     Tips, sponsor, revenue tracking
│   │   ├── stripe_service.py        #     Stripe payment integration
│   │   ├── suno_service.py          #     Suno AI music generation
│   │   ├── tinyfish_service.py      #     Live web intelligence
│   │   ├── chemistry_engine.py      #     Emergent agent chemistry
│   │   ├── narrative_engine.py      #     Narrative momentum tracking
│   │   ├── content_genome.py        #     Predictive content DNA
│   │   ├── freshness_engine.py      #     Staleness detection
│   │   ├── collective_emergence.py  #     Collective emergence protocol
│   │   ├── cognitive_memory.py      #     Persistent agent memory
│   │   ├── vector_memory.py         #     ChromaDB vector search
│   │   ├── youtube_service.py       #     YouTube Live chat
│   │   ├── stream_manager.py        #     YouTube/Twitch audio streaming
│   │   ├── video_stream_service.py  #     Browser → FFmpeg → RTMP
│   │   ├── hud_sync_service.py      #     Real-time frontend sync
│   │   └── ...                      #     (64 services total)
│   │
│   ├── broadcast_engine/            #   📻 Broadcast Engine (12 files)
│   │   ├── hot_clock.py             #     60-min show with sponsor/tipper integration
│   │   ├── show_runner.py           #     Show execution engine
│   │   ├── live_orchestrator.py     #     Real-time coordination
│   │   ├── tipper_system.py         #     VIP tipper management
│   │   ├── audio_mastering.py       #     Audio post-processing
│   │   └── smart_quality_gates.py   #     Quality enforcement
│   │
│   ├── managers/                    #   📊 Delegate Managers
│   │   └── status_reporter.py       #     Extracted status/metrics reporting
│   │
│   ├── security/                    #   🔒 Security Engine (4 files)
│   │   ├── security_engine.py       #     Orchestrator (488 lines)
│   │   ├── attack_signatures.py     #     100+ attack patterns
│   │   ├── ids_ips.py               #     Intrusion detection/prevention
│   │   └── phishing_detector.py     #     Phishing URL detection
│   │
│   ├── middleware/                   #   🛡️ Security Middleware (5 files)
│   │   ├── auth_middleware.py        #     JWT authentication
│   │   ├── csrf_middleware.py        #     CSRF protection
│   │   ├── rate_limit_middleware.py  #     Per-endpoint rate limiting
│   │   ├── input_validation.py      #     Pydantic request validation
│   │   └── security_headers.py      #     CSP + XSS + HTML sanitization
│   │
│   ├── models/                      #   📦 Data Models
│   │   ├── segment_models.py        #     Segment/script models
│   │   ├── show_models.py           #     Show structure models
│   │   └── sponsor_models.py        #     Sponsor/ad models
│   │
│   └── database/                    #   💾 Database Layer
│       ├── models.py                #     SQLAlchemy ORM (12 tables, 417 lines)
│       └── repository.py            #     Data access layer
│
├── framework/                       # 🕸️ SwarmOS Framework (52 files, 18,800+ lines)
│   ├── bootstrap.py                 #   SwarmOS assembly (365 lines)
│   ├── adapters.py                  #   Engine → pipeline adapters
│   ├── core/                        #   EventBus, AgentRegistry, Pipeline, Subsumption
│   ├── actions/                     #   ActionRegistry, Executor, Guard, Pipeline
│   ├── guardian/                    #   GuardianAgent (1,550 lines)
│   ├── emergence/                   #   SemanticDriftDetector, TokenEconomy
│   ├── cognition/                   #   Orchestrator strategies
│   ├── communication/               #   Agent-to-Agent protocol
│   ├── plugins/                     #   Hot-loadable plugin system
│   └── mcp/                         #   MCP server (SSE) + RelationshipArcEngine
│
├── frontend/                        # 🎨 Listener Frontend (React + Three.js)
│   ├── src/
│   │   ├── components/              #   17 components (RadioPlayer, Visualizer, etc.)
│   │   ├── hooks/                   #   6 hooks (useWebSocket, useRadioStore, etc.)
│   │   ├── services/                #   API service layer
│   │   └── styles/                  #   TailwindCSS + animations
│   └── dist/                        #   Built output (5.5MB)
│
├── admin/                           # 🏢 Admin Dashboard (React)
│   ├── src/
│   │   ├── pages/                   #   11 pages (StationControl, Agents, etc.)
│   │   ├── ProfessionalDashboard.tsx#   Main dashboard layout
│   │   └── components/              #   Reusable admin components
│   ├── proxy-server.js              #   Dev proxy (port 3001 → 8080)
│   └── dist/                        #   Built output (932KB)
│
├── config/                          # ⚙️ Configuration
│   ├── sponsors.json                #   Sponsor/advertiser config
│   └── banned_words.txt             #   Content filtering wordlist
│
├── audio/                           # 🎵 Audio Assets
│   └── music/                       #   60 tracks (lofi, rock, jazz, etc.)
│
├── liquidsoap/                      # 📻 Radio Infrastructure
│   └── waig.liq                     #   Liquidsoap config (crossfading, ducking)
│
├── docker/                          # 🐳 Container Configs
│   ├── Dockerfile.brain             #   Python backend
│   ├── Dockerfile.frontend          #   React frontend
│   ├── Dockerfile.admin             #   Admin dashboard
│   ├── Dockerfile.liquidsoap        #   Audio mixer
│   ├── nginx.conf                   #   Reverse proxy
│   ├── nginx-prod.conf              #   Production proxy
│   └── prometheus.yml               #   Metrics config
│
├── tests/                           # 🧪 Test Suite (17 files)
│   ├── test_root_cause_fixes.py     #   Root-cause fix verification (57 tests)
│   ├── test_comprehensive_suite.py  #   Architecture verification (90+ tests)
│   ├── test_innovations3.py         #   Innovation engine tests
│   ├── test_selenium_full.py        #   Full E2E with Selenium
│   └── ...
│
├── .github/workflows/ci.yml        # 🔄 CI/CD Pipeline (197 lines)
├── tools/                           # 🔧 Utilities (music generation, API testing)
└── scripts/                         # 📜 Deployment scripts
```

---

## Quick Start

### Prerequisites

- Python 3.11+ (3.13 recommended)
- At least **one** LLM API key (OpenAI, Gemini, or Anthropic)
- Everything else is optional and degrades gracefully

### 1. Clone & Install

```bash
git clone https://github.com/userinpeace/ghost-radio-waig.git
cd ghost-radio-waig

# Backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Frontend (optional — pre-built dist/ included)
cd frontend && npm install && npm run build && cd ..

# Admin (optional — pre-built dist/ included)
cd admin && npm install && npm run build && cd ..
```

### 2. Configure Environment

```bash
cp .env.example .env
```

**Minimum required** (everything else optional):
```env
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...
```

**For voice synthesis** (optional — falls back to free edge-tts):
```env
ELEVENLABS_API_KEY=your_key
```

**For tips** (optional):
```env
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PUBLISHABLE_KEY=pk_test_...
```

**For streaming** (optional):
```env
YOUTUBE_STREAM_KEY=your_key
TWITCH_STREAM_KEY=your_key
```

### 3. Run

```bash
# Full mode (uses real API keys, costs money)
python main.py

# Dry-run mode (mock LLM, mock voice — zero API cost)
python main.py --dry-run

# API-only (no broadcast loop, just REST API + WebSocket)
python main.py --api-only

# Debug logging
python main.py --debug

# Switch LLM provider
python main.py --provider gemini
```

**Open:** http://localhost:8080 → Frontend loads → Click Play

### What starts automatically

When you run `python main.py`, the following systems initialize:

1. aiohttp API server on port 8080 (130+ endpoints)
2. WebSocket broadcast server on port 8765
3. All 12 AI agents register with SwarmOS
4. Guardian Agent activates (Operative mode)
5. ServiceHealthRegistry monitors 19+ services
6. Hot Clock starts Hour #1 with a random show format
7. HackerNews/Reddit data feeds begin polling
8. Frontend served at `/`, Admin at `/admin`

---

## API

### Endpoint Summary

| Category | Prefix | Endpoints | Description |
|----------|--------|-----------|-------------|
| Health | `/health`, `/status`, `/metrics`, `/current` | 4 | Station health, uptime, Prometheus metrics |
| Service Health | `/api/health/services` | 1 | Tier-based service health dashboard |
| Guardian | `/api/guardian/*` | 8 | Guardian stats, dashboard, threat log, controls |
| SwarmOS | `/api/swarm/*` | 5 | Pipeline stats, agent registry, event bus |
| Actions | `/api/actions/*` | 3 | Action stats, audit log, action registry |
| Agents | `/api/agents` | 1 | All 12 agents with status, workload, lastActive |
| RAVAN | `/ravan/*` | 23 | Intelligence hub, web search, pattern analysis |
| Monetization | `/api/tip/*`, `/api/config`, `/api/revenue` | 7 | Stripe tips, station config, revenue tracking |
| Streaming | `/api/stream/*`, `/api/music/*`, `/api/youtube/*` | 16 | Stream control, music status, YouTube Live |
| Chat | `/chat`, `/request` | 2 | Send chat messages, song requests |
| Admin | `/admin/*` | 53 | Content moderation, analytics, system management |
| Agent Chat | `/api/agents/*` | 6 | Admin ↔ agent NLP communication |
| WebSocket | `/ws/hud`, `/ws/stream-capture` | 2 | Real-time HUD sync, video stream capture |
| **Total** | | **~130** | |

### Key Endpoints

```bash
# Health check
curl http://localhost:8080/health
# → {"status": "healthy", "station": "Ghost Radio W-A-I-G", "running": true}

# Current segment
curl http://localhost:8080/current
# → {"agent_id": "static", "agent_name": "DJ Static", "segment_type": "show_intro", ...}

# All agents
curl http://localhost:8080/api/agents
# → {"agents": [{"id": "static", "name": "DJ Static", "status": "online"}, ...]}

# Service health dashboard
curl http://localhost:8080/api/health/services
# → {"overall": "healthy", "services": {"llm_service": {"tier": "critical", ...}}}

# Guardian stats
curl http://localhost:8080/api/guardian/stats
# → {"scans_performed": 42, "threats_detected": 0, "mode": "operative", ...}

# SwarmOS dashboard
curl http://localhost:8080/api/swarm/dashboard
# → {"framework": {"started": true}, "pipeline": {...}, "agents": [...]}

# Action registry (41 registered actions)
curl http://localhost:8080/api/actions/registry
# → {"actions": [{"name": "queue_song", "type": "playlist_control", ...}], "total": 41}

# Prometheus metrics
curl http://localhost:8080/metrics
# → ghost_radio_listeners 0
# → ghost_radio_segments_total 15
# → ghost_radio_uptime_seconds 3600.0

# Station config (public)
curl http://localhost:8080/api/config
# → {"station_name": "Ghost Radio W-A-I-G", "tip_minimum": 1.0, ...}
```

### WebSocket Messages

Connect to `ws://localhost:8765`:

```json
// On connect — full state
{"type": "state", "data": {"current_segment": {...}, "listener_count": 0, "chat_history": [...]}}

// On new segment
{"type": "segment", "data": {"type": "talk", "data": {"agent": "static", "script": "..."}}}

// Visual triggers
{"type": "visual", "data": {"scene": "glitch_fade"}}
```

---

## Deployment

### Docker (Recommended for Production)

```bash
# Standard
docker-compose up -d

# Production hardened (resource limits, health checks)
docker-compose -f docker-compose-hardened.yml up -d
```

### Docker Services

| # | Service | Port(s) | Purpose |
|---|---------|---------|---------|
| 1 | brain | 8080, 8765 | AI engine + WebSocket |
| 2 | liquidsoap | 8000, 1234 | Professional audio mixer |
| 3 | icecast | 8001 | Streaming server |
| 4 | frontend | 3000 | React visualizer |
| 5 | admin | 3002 | Control room dashboard |
| 6 | redis | 6379 | State cache + Event Bus |
| 7 | nginx | 80, 443 | Reverse proxy + SSL |
| 8 | prometheus | 9090 | Metrics collection |
| 9 | grafana | 3001 | Metrics dashboard |
| 10 | mcp-arcs | 8787 | MCP Relationship Arc server |

### Security Checklist

Before production deployment:

- [ ] Set strong `JWT_SECRET` (32+ random chars)
- [ ] Set `ADMIN_PASSWORD_HASH` (bcrypt hash)
- [ ] Set strong Icecast passwords
- [ ] Set `GRAFANA_ADMIN_PASSWORD`
- [ ] Switch `ENVIRONMENT=production`
- [ ] Configure SSL certificates in nginx
- [ ] Review rate limiting thresholds
- [ ] Enable `docker-compose-hardened.yml`

---

## Database Schema

12 tables (SQLAlchemy ORM, SQLite default):

| Table | Purpose |
|-------|---------|
| `segment_records` | Every broadcast segment (transcript, cost, model, duration, voice) |
| `tips` | Listener tips (amount, source, payment provider) |
| `revenue_records` | Revenue transactions (5 revenue types) |
| `sponsored_content` | Sponsor campaigns |
| `safety_flags` | Content safety flags (4 severity levels) |
| `admin_actions` | Admin audit log |
| `banned_words` | Banned word list |
| `blocked_usernames` | Blocked users |
| `system_metrics` | Performance metrics history |
| `station_state` | Station state persistence |
| `chat_messages` | Chat messages (public + agent-admin) |
| `generated_tracks` | AI-generated music tracks |

---

## CI/CD

GitHub Actions pipeline (`.github/workflows/ci.yml`):

| Job | What It Does |
|-----|-------------|
| **Python** | Matrix test (3.12, 3.13) → Ruff lint → AST parse → pytest → Module imports → Route completeness |
| **Admin** | TypeScript check → Vite build |
| **Docker** | docker-compose config validation (main branch) |

---

## Design Patterns

| Pattern | Where | Purpose |
|---------|-------|---------|
| Subsumption Architecture | SwarmOS pipeline | Priority-layered behavior: Safety > Exploration > Economy > Default |
| Strategy Pattern | LLM service, Orchestrator | Swap LLM providers or orchestration strategies without code changes |
| Observer Pattern | EventBus, Pipeline observers | Decouple engines from core execution |
| Plugin Architecture | SwarmOS plugins | Hot-loadable extensions |
| Guardian Pattern | Guardian Agent | Supervisor with escalating intervention powers |
| Triple Fallback | Voice service | ElevenLabs → edge-tts → emergency MP3 |
| Content-Hash Caching | Voice service | SHA-based dedup prevents duplicate API calls |
| Service Health Registry | health_registry.py | Tier-based monitoring with heartbeats and A-F grades |
| Bounded Buffers | Station manager | `deque(maxlen=N)` prevents unbounded memory growth |
| Async Lock Discipline | Station manager | 7+ named locks protecting concurrent shared state |

---

## Screenshots

<p align="center">
  <img src="ghost-radio-working.png" alt="Ghost Radio Working" width="600"/>
  <br/><em>Live station with 3D audio-reactive visualizer</em>
</p>

<p align="center">
  <img src="frontend-visualizer-fixed.png" alt="Visualizer" width="600"/>
  <br/><em>Three.js particle tunnel + waveform ring reacting to audio</em>
</p>

<p align="center">
  <img src="tip-button-open.png" alt="Tip System" width="600"/>
  <br/><em>Stripe-powered tip system — $5+ gets read on air</em>
</p>

---

## How It Works

1. **Clock Keeper** — Selects the next show format from 15 rotating shows. Each hour gets a host, guests, and a style (solo/duo/party/specialist/ensemble/late-night).

2. **Content Gathering** — HackerNews, Reddit (16 subs), RSS feeds (BBC, Ars Technica, TechCrunch, Wired), and TinyFish live web scraping deliver fresh content every cycle.

3. **SwarmOS Pipeline** — For each segment, the pipeline builds context (trends, chat, tips, memory), runs through subsumption layers, and generates agent scripts via LLM.

4. **Guardian Gate** — Guardian Agent scans every output for jailbreaks, prompt injection, token abuse, and toxic patterns. Can block, modify, quarantine, or lockdown.

5. **Action Extraction** — The Action Layer parses `[ACTION:queue_song "Midnight"]` intents from LLM output, validates permissions, and executes real actions.

6. **Voice Synthesis** — Scripts with emotion tags are converted to speech. Content-hash caching prevents duplicate API calls. Triple fallback ensures audio always plays.

7. **Broadcast** — Voice audio + metadata pushed to all listeners via WebSocket. Music ducks during speech and resumes after.

8. **Loop** — The Hot Clock advances through 15 segments per hour, 24 hours per day, forever.

---

## License

This project is proprietary. All rights reserved.

---

<p align="center">
  <strong>Built by <a href="https://github.com/userinpeace">@userinpeace</a></strong>
  <br/>
  <em>Ghost Radio W-A-I-G — COMING LIVE SOON</em>
</p>
