# ProspectZero Analysis & Build Plan

> **Date:** 2026-05-07
> **Source:** prospectzero.com
> **Status:** Competitor analysis → MVP build scope

---

## 1. What ProspectZero Is

Signal-based LinkedIn prospecting agent. $99/seat/mo. AI detects buying intent signals on LinkedIn, scores leads against ICP, and launches personalized multi-step LinkedIn DMs automatically.

### Their 3-Step Flow
1. **Detect Intent** — 9+ LinkedIn signals: profile views, post engagement, job changes, funding, competitor activity
2. **Score & Prioritize** — ICP fit + signal strength. Auto-filter wrong-fit prospects
3. **Launch Outreach** — Personalized DMs referencing real context. Human-paced. Auto-pause on reply

### Target Market
Founders, GTM leaders, outbound teams. 50+ customers claimed.

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|---|---|---|
| Signal detection | 🔴 Very Hard | LinkedIn API doesn't expose profile views, post engagement, DMs |
| Automated sending | 🔴 Very Hard | LinkedIn rate limits, bans, no official DM API |
| ICP scoring | 🟢 Easy | Python + LLM |
| Message personalization | 🟢 Easy | GPT/Claude API |
| Sequence engine | 🟡 Medium | DB + cron/webhooks |
| Dashboard | 🟡 Medium | Standard web app |
| Reply detection | 🟡 Medium | Webhook + inbox monitoring |

### The Moats
- **LinkedIn data access** — requires proxy infrastructure, account farms, residential IPs
- **Signal detection** — reverse-engineered browser automation that doesn't get banned
- **Account safety** — human-paced sending, randomization, fingerprint evasion

---

## 3. MVP That CAN Be Built (No LinkedIn API Needed)

A **lite version** with real utility:

### MVP Features
1. **Lead import** — Paste LinkedIn profile URLs or upload CSV
2. **AI research agent** — LLM scrapes public web/social for each profile
3. **ICP scoring engine** — Define ideal customer profile → score each lead
4. **Outreach draft generator** — AI writes personalized LinkedIn/email drafts
5. **Sequence manager** — 3-5 step sequences with follow-up logic
6. **Dashboard** — Pipeline view, leads table, drafts queue
7. **Manual send mode** — User reviews drafts, copies/pastes manually

### What We DON'T Build (MVP)
- Automated LinkedIn signal detection
- Automated DM sending
- LinkedIn API integration

---

## 4. COMPLETE PROJECT INFRA DESIGN

### Architecture Overview
```
┌─────────────────────────────────────────────────────────┐
│                     Frontend (Vercel)                     │
│                    Next.js 14 App Router                  │
│                   Tailwind + Shadcn/ui                    │
└──────────────────────┬──────────────────────────────────┘
                       │ REST/WebSocket
┌──────────────────────▼──────────────────────────────────┐
│                    Backend (Railway/Fly)                  │
│                  FastAPI / Python 3.12+                   │
│              Celery + Redis for async tasks              │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│                   Database (Supabase)                     │
│                PostgreSQL + Row Level Security            │
│         - Users, Orgs, Leads, Sequences, Messages         │
└──────────────────────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│                 AI Layer (OpenAI / Anthropic)              │
│         - Profile research agent                          │
│         - Outreach copy generation                        │
│         - Lead scoring LLM calls                          │
└──────────────────────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│              Background Workers (Celery)                  │
│         - Web scraping tasks                              │
│         - Batch scoring                                   │
│         - Sequence follow-up scheduling                   │
│         - Email sending (if added)                        │
└──────────────────────────────────────────────────────────┘
```

### Directory Structure
```
prospectzero/
├── frontend/                    # Next.js 14 App
│   ├── app/
│   │   ├── (auth)/             # Login/signup routes
│   │   ├── dashboard/          # Main app
│   │   │   ├── leads/          # Lead management
│   │   │   ├── sequences/      # Sequence builder
│   │   │   ├── drafts/         # Outreach drafts queue
│   │   │   ├── settings/       # ICP settings, profile
│   │   │   └── analytics/      # Pipeline stats
│   │   └── api/                # Next.js API routes (BFF layer)
│   ├── components/
│   │   ├── ui/                 # Shadcn components
│   │   ├── leads/              # Lead-specific components
│   │   ├── sequences/          # Sequence components
│   │   └── shared/             # Layout, nav, etc.
│   ├── lib/
│   │   ├── api-client.ts       # Backend API client
│   │   ├── supabase.ts         # Supabase client
│   │   └── utils.ts            # Helpers
│   ├── tailwind.config.ts
│   ├── next.config.js
│   └── package.json
│
├── backend/                    # FastAPI backend
│   ├── app/
│   │   ├── main.py             # App entry + middleware
│   │   ├── config.py           # Settings (pydantic-settings)
│   │   ├── db/
│   │   │   ├── models.py       # SQLAlchemy models
│   │   │   ├── schemas.py      # Pydantic schemas
│   │   │   └── session.py      # DB session
│   │   ├── routers/
│   │   │   ├── auth.py         # Auth endpoints
│   │   │   ├── leads.py        # Lead CRUD
│   │   │   ├── sequences.py    # Sequence CRUD
│   │   │   ├── drafts.py       # Draft generation
│   │   │   └── analytics.py    # Stats endpoints
│   │   ├── services/
│   │   │   ├── lead_scoring.py     # ICP scoring engine
│   │   │   ├── research_agent.py   # AI profile research
│   │   │   ├── copy_generator.py   # Outreach copy gen
│   │   │   ├── sequence_engine.py  # Sequence logic
│   │   │   └── web_scraper.py      # Public web scraping
│   │   ├── tasks/
│   │   │   ├── celery_app.py   # Celery config
│   │   │   └── workers.py      # Background task defs
│   │   └── utils/
│   │       ├── llm.py          # LLM client wrapper
│   │       └── auth.py         # JWT/auth helpers
│   ├── tests/
│   ├── alembic/                # DB migrations
│   ├── Dockerfile
│   ├── requirements.txt
│   └── pyproject.toml
│
├── supabase/
│   └── migrations/             # SQL migrations
│
├── docker-compose.yml          # Local dev stack
├── .env.example
├── README.md
└── .github/
    └── workflows/
        ├── deploy-frontend.yml
        └── deploy-backend.yml
```

### Database Schema (PostgreSQL)

```sql
-- Organizations / Teams
CREATE TABLE orgs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Users
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    name TEXT NOT NULL,
    org_id UUID REFERENCES orgs(id),
    role TEXT DEFAULT 'member', -- 'admin', 'member'
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- ICP Definitions (per org)
CREATE TABLE icp_definitions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    org_id UUID REFERENCES orgs(id),
    name TEXT NOT NULL,
    industries TEXT[],          -- target industries
    company_size_min INT,
    company_size_max INT,
    job_titles TEXT[],          -- target titles
    seniority TEXT[],            -- e.g. ['Director', 'VP', 'C-Level']
    geography TEXT[],
    keywords TEXT[],            -- e.g. 'SaaS', 'AI', 'enterprise'
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Leads
CREATE TABLE leads (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    org_id UUID REFERENCES orgs(id),
    icp_id UUID REFERENCES icp_definitions(id),
    linkedin_url TEXT,
    first_name TEXT,
    last_name TEXT,
    job_title TEXT,
    company TEXT,
    company_size TEXT,
    industry TEXT,
    location TEXT,
    email TEXT,
    phone TEXT,
    bio TEXT,
    icp_score DECIMAL(3,2) DEFAULT 0.00, -- 0.00 - 1.00
    signal_strength DECIMAL(3,2) DEFAULT 0.00,
    status TEXT DEFAULT 'new',  -- new, researched, scored, drafted, contacted, replied, converted, lost
    research_data JSONB,        -- raw AI research output
    source TEXT DEFAULT 'manual', -- manual, csv_import, api
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Sequences
CREATE TABLE sequences (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    org_id UUID REFERENCES orgs(id),
    name TEXT NOT NULL,
    channel TEXT DEFAULT 'linkedin', -- linkedin, email, both
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Sequence Steps
CREATE TABLE sequence_steps (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sequence_id UUID REFERENCES sequences(id) ON DELETE CASCADE,
    step_number INT NOT NULL,
    delay_hours INT DEFAULT 48,  -- hours before this step fires
    action TEXT NOT NULL,         -- 'message', 'connection_request', 'inmail'
    template TEXT,                -- Message template with {{placeholders}}
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Lead-Sequence Assignment
CREATE TABLE lead_sequences (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    lead_id UUID REFERENCES leads(id),
    sequence_id UUID REFERENCES sequences(id),
    current_step INT DEFAULT 0,
    status TEXT DEFAULT 'active',  -- active, paused, completed, failed
    assigned_at TIMESTAMPTZ DEFAULT NOW(),
    completed_at TIMESTAMPTZ
);

-- Generated Drafts
CREATE TABLE drafts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    lead_id UUID REFERENCES leads(id),
    sequence_step_id UUID REFERENCES sequence_steps(id),
    content TEXT NOT NULL,
    status TEXT DEFAULT 'pending', -- pending, approved, sent, skipped
    ai_model TEXT,                 -- model used (gpt-4, claude-opus)
    score DECIMAL(3,2),           -- quality score if available
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Activity Log
CREATE TABLE activity_log (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    lead_id UUID REFERENCES leads(id),
    action TEXT NOT NULL,        -- researched, scored, draft_generated, approved
    meta JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### API Endpoints

```
POST   /api/auth/register       # Sign up
POST   /api/auth/login          # Sign in (JWT)
GET    /api/auth/me             # Current user
PATCH  /api/auth/me             # Update profile

GET    /api/leads               # List leads (paginated, filtered)
POST   /api/leads               # Import single lead
POST   /api/leads/batch         # CSV/URL batch import
GET    /api/leads/:id           # Get lead detail
PATCH  /api/leads/:id           # Update lead
DELETE /api/leads/:id           # Remove lead
POST   /api/leads/:id/research  # Trigger AI research
POST   /api/leads/:id/score     # Trigger ICP scoring

GET    /api/icp                 # List ICP definitions
POST   /api/icp                 # Create ICP
PATCH  /api/icp/:id             # Update ICP

GET    /api/sequences           # List sequences
POST   /api/sequences           # Create sequence
PATCH  /api/sequences/:id       # Update
POST   /api/sequences/:id/assign # Assign to leads
POST   /api/sequences/:id/generate # Generate all drafts

GET    /api/drafts              # List drafts
PATCH  /api/drafts/:id          # Approve/skip/edit
GET    /api/drafts/:id          # Get single draft

GET    /api/analytics/pipeline  # Pipeline stats
GET    /api/analytics/conversion # Conversion rates
```

### AI Agent Design

**Research Agent** (one-shot per lead):
```
Input:  lead.linkedin_url, lead.job_title, lead.company
Steps:
  1. Scrape LinkedIn public profile (via browserless/proxy)
  2. Scrape company website for product/positioning
  3. Scrape recent news/crunchbase for funding/events
  4. LLM: Synthesize into structured research_data JSON
Output: research_data JSON
```

**Scoring Agent:**
```
Input:  research_data, icp_definition
Logic:  LLM + weighted rule-based scoring
   - Industry match (30%)
   - Title/seniority match (25%)
   - Company size match (15%)
   - Keywords match (15%)
   - Signal/event recency (15%)
Output: icp_score (0.00-1.00)
```

**Copy Generator:**
```
Input:  research_data, sequence_step template
Logic:  LLM fills {{placeholders}} with context from research
        - References trigger events
        - Keeps tone consistent with brand voice
        - Generates subject line + body
Output: draft.content
```

### Tech Stack Summary

| Layer | Choice | Reason |
|---|---|---|
| Frontend | Next.js 14 + Tailwind + Shadcn | Fast dev, good DX, Vercel deploy |
| Backend | FastAPI (Python) | Async, auto-docs, easy AI integration |
| Database | Supabase (PostgreSQL) | Managed DB, auth, RLS, real-time |
| AI | OpenAI / Anthropic API | Best-in-class LLM for research + copy |
| Queue | Celery + Redis | Async batch scoring, scraping |
| Auth | Supabase Auth + JWT | Built-in, RLS integration |
| Hosting | Vercel (frontend) + Railway/Fly (backend) | Simple, scalable |
| Monitoring | Sentry + Logfire | Error + performance tracking |

### Estimated MVP Build Time

| Phase | Time | Deliverable |
|---|---|---|
| Setup + Auth | 2 days | Project scaffold, auth, org model |
| Lead CRUD + Import | 3 days | Lead management, batch import |
| ICP Definition + Scoring | 4 days | ICP builder, scoring engine |
| Research Agent | 5 days | Web scraping + LLM synthesis |
| Sequence Builder | 3 days | Step editor, template system |
| Draft Generator | 4 days | AI copy gen, draft queue |
| Dashboard | 3 days | Pipeline view, analytics |
| Polish + Deploy | 2 days | Error handling, docs, deploy |
| **Total** | **26 days** | **Working MVP** |

### Cost Estimates (Monthly)

| Service | Cost | Notes |
|---|---|---|
| Vercel (Pro) | $20 | Frontend hosting |
| Railway (Starter) | $5 | Backend hosting |
| Supabase (Pro) | $25 | DB + Auth + 500MB |
| OpenAI API | $50-200 | LLM calls (variable) |
| Redis (Upstash) | $0-10 | Celery queue |
| Sentry (Free) | $0 | Error tracking |
| Domain | $10/yr | prospectzero-like.com |
| **Total** | **$100-260/mo** | Scales with usage |

### Future Phases

**Phase 2 (LinkedIn Automation)** — Requires proxy infrastructure
- Browser automation with residential proxies (BrightData, Oxylabs)
- Account rotation system
- Human-paced sending engine
- Signal detection via browser automation

**Phase 3 (Multi-Channel)**
- Email outreach (SendGrid, SES)
- Twitter/X DM
- Multi-channel sequences

**Phase 4 (Revenue Features)**
- Team seats + roles
- CRM integrations (HubSpot, Salesforce)
- API for partners
- Advanced analytics

---

*This doc serves as the source of truth for the ProspectZero clone project.*
*Last updated: 2026-05-07*
