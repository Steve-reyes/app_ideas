# PlusVibe (formerly pipl.ai) Analysis & Build Plan

> **Date:** 2026-08-12
> **Source:** https://pipl.ai (redirects to https://plusvibe.ai)
> **Status:** Competitor analysis → MVP build scope

---

## 1. What PlusVibe Is

PlusVibe is an AI-driven cold email automation + deliverability platform (rebranded from pipl.ai — same product, same team, same Slack community). It lets businesses send cold outreach at scale while maximizing inbox placement: campaign builder, multi-inbox rotation, AI sequence writer, AI reply agent, lead scraping Chrome extension, data enrichment credits, and a built-in email warm-up engine over a private inbox network.

### Their 3-Step Flow
1. **Connect & Warm** — Connect email accounts (Google Workspace / M365 / any SMTP), auto-configure SPF/DKIM/DMARC, run warm-up on a private pool (simulated human behavior: sends, opens, replies, stars).
2. **Build & Send** — Scrape/import leads (Chrome extension or CSV), write sequences with AI copy assistant + variables, launch multi-inbox campaigns with throttling, rotation, and reply detection.
3. **Track & Scale** — Reply tracking, auto-labeling, AI Reply Agent answers leads, deliverability monitor + placement tests, scale to hundreds of inboxes.

### Target Market
Solo founders, SMBs, agencies, and large sales orgs. "Whether you're sending 50 emails a week or managing 500 inboxes." Positioned as the affordable deliverability-first alternative to Instantly/lemlist/Smartlead.

### Pricing (from plusvibe.ai/plans, Aug 2026)
| Plan | Monthly | Annual (per mo) | What you get |
|---|---|---|---|
| Free Trial | $0 | — | 14 days, 1,000 emails, 3 inboxes, warm-up |
| Starter | $37 | $30.80 | 25,000 emails/mo, 1K-7K enrichment credits (+$8-21), Basic Warm-Up |
| Business (Most Popular) | $77 | $64.20 | 150,000 emails/mo, 3K-50K enrichment (+$15-88), Advanced Warm-Up |
| Agency | $497 | $415 | 500,000+ emails/mo, isolated sending server, dedicated connector IPs, custom enrichment |
| Placement Test (add-on) | $19/$39/$89 | — | 300/1K/5K credits, recurring inbox placement tests |

Managed services: Google Workspace setup $4/inbox/mo · M365 $4.5/inbox/mo · Azure infra $49/domain/mo · custom infra $1,500/mo · done-for-you LinkedIn outreach ~$9,876/mo.

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|---|---|---|
| Campaign engine (sequences, steps, delays, variables) | 🟢 | Standard CRUD + cron scheduler + SMTP/API sending |
| Lead import + Chrome scraper extension | 🟢 | CSV/API import easy; extension is standard Chrome API work |
| AI copy assistant + reply agent | 🟢 | Plain LLM calls (OpenAI/Anthropic) with prompt templates |
| Enrichment credits | 🟡 | Needs data partners (Hunter, Apollo, Dropcontact) — API costs, no proprietary data |
| Multi-inbox rotation + throttling | 🟡 | Scheduler + rate limiter + per-inbox send quotas; must integrate Gmail API + Outlook Graph + SMTP |
| Deliverability monitoring + placement tests | 🟡 | Needs seed inbox network + test mailboxes; mailbox provider cooperation |
| Email warm-up network | 🔴 | Requires thousands of real inboxes exchanging mail 24/7 — infrastructure heavy, anti-abuse risk |
| Sender reputation management | 🔴 | Proprietary operational knowledge; Google/Microsoft aggressively block bulk senders |
| Compliance (CAN-SPAM, GDPR, unsubscribe) | 🔴 | Legal + infra (suppression lists, opt-out headers, list hygiene) |

### The Moats
- **Warm-up network** — private pool of verified inboxes simulating human behavior. Building this is months of infra + a chicken-and-egg user base. The actual hard moat.
- **Deliverability ops knowledge** — domain/IP reputation tuning, BIMI, per-ESP quirks. Know-how, not code.
- **Bulk-sending infrastructure** — Google/Microsoft ToS risk; staying alive at scale is an operational arms race.
- **Network effects** — the bigger the warm-up pool, the better the results for everyone.

---

## 3. MVP That CAN Be Built

### MVP Features
1. **Multi-inbox campaign engine** — connect Gmail (IMAP/SMTP or Gmail API), build sequences (steps, delays, variables), send with per-inbox daily caps + round-robin rotation.
2. **Lead management** — CSV import + basic list/segment CRUD, dedupe, unsubscribe/suppression list.
3. **AI sequence writer** — one LLM call generating a 3-5 step cold sequence from a product description + prospect info.
4. **Reply detection & tracking** — Gmail API watch / IMAP polling to mark replies, webhook to Zapier/Make.
5. **Basic warm-up** — scheduled self-sending between the user's own inboxes (Google Workspace sandbox style, conservative) — NOT a private pool.
6. **Deliverability health panel** — SPF/DKIM/DMARC check (free DNS lookup libs), send volume heatmap, bounce/spam-rate dashboard.

### What We DON'T Build (MVP)
- Private warm-up pool / inbox network (Phase 3)
- Enrichment credits (integrate Hunter/Apollo as paid add-on, Phase 2)
- Chrome lead scraper extension (Phase 2)
- AI Reply Agent that actually sends (Phase 2 — reply drafting only in MVP)
- Placement testing network (Phase 3)
- Agency isolated servers / dedicated IPs (Phase 4)

---

## 4. Complete Project Infra Design

### Product Requirements Document (PRD)

**Personas:**
1. **Solo founder** — sends 50-500 emails/wk, wants it to "just work", no tech setup.
2. **SDR/Agency owner** — manages 5-50 inboxes across clients, needs rotation + reporting.
3. **Growth marketer** — wants AI-assisted copy + reply handling, Zapier integration.

**Jobs to be Done:**
- "I want my cold emails to land in inboxes, not spam" (deliverability-first)
- "I want to send 10k personalized emails without manually logging in to 10 inboxes"
- "I want to know who replied without checking every mailbox"

**Key user stories:**
- As a founder, I can connect 3 Gmail accounts and send my first campaign in <15 minutes.
- As an SDR, I can set daily send limits per inbox so I never get flagged.
- As an agency owner, I can see reply rate + spam rate per client in one dashboard.

**RICE prioritization (MVP):**
| Feature | Reach | Impact | Confidence | Effort | RICE |
|---|---|---|---|---|---|
| Campaign engine + rotation | 10 | 3 | 0.9 | 3 | 9.0 |
| Gmail/Outlook connector | 10 | 3 | 0.9 | 2 | 13.5 |
| Reply detection | 9 | 3 | 0.8 | 2 | 10.8 |
| AI sequence writer | 8 | 2 | 0.8 | 1 | 12.8 |
| Warm-up (basic) | 8 | 2 | 0.5 | 2 | 4.0 |
| Placement tests | 5 | 2 | 0.5 | 3 | 1.7 |

**KPIs:** inbox placement rate, reply rate, spam complaint rate, daily sends/inbox, MRR, churn.

### Competitive Landscape Matrix

| Feature (1-5) | PlusVibe | Instantly | Smartlead | lemlist | Mixmax/Outreach |
|---|---|---|---|---|---|
| Price (lower=better) | 5 | 4 | 4 | 3 | 1 |
| Warm-up included | 5 | 5 | 4 | 1 | 1 |
| Multi-inbox rotation | 5 | 5 | 5 | 3 | 3 |
| AI writing | 4 | 3 | 3 | 4 | 4 |
| Enrichment native | 4 | 3 | 3 | 2 | 2 |
| Agency features | 4 | 4 | 5 | 2 | 4 |
| Placement testing | 5 | 3 | 3 | 1 | 1 |

**Positioning gap:** mid-market sweet spot — warm-up + rotation + placement tests at under $100/mo beats lemlist (no warm-up) and undercuts Instantly/Smartlead on agency price. Differentiator for us: **deliverability-first with transparent diagnostics**.

### Architecture Overview
```
┌─────────────────────────────────────────────────────────┐
│               Frontend (Vercel)                          │
│              Next.js 14 App Router                      │
│             Tailwind + Shadcn/ui                        │
└──────────────────────┬──────────────────────────────────┘
                       │ REST + WebSocket (live stats)
┌──────────────────────▼──────────────────────────────────┐
│              Backend (FastAPI on Railway)                │
│   Auth · Campaigns · Leads · Sequences · Webhooks        │
│         Celery + Redis (send queue, watchers)            │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│             Database (Supabase PostgreSQL)               │
│   orgs, users, leads, lists, campaigns, sequences,       │
│   steps, email_accounts, sends, replies, activity_log    │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│               AI Layer (OpenAI/Anthropic)                │
│   SequenceWriter Agent · ReplyDraft Agent               │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│        Workers (Celery beat)                             │
│   SenderWorker (rotation + caps) · ReplyWatcher ·        │
│   WarmUpWorker · DeliverabilityChecker                  │
└──────────────────────────────────────────────────────────┘
```

### Directory Structure
```
plusvibe-clone/
├── frontend/
│   ├── app/
│   │   ├── (auth)/login, register
│   │   ├── dashboard/campaigns, leads, inboxes, analytics, warmup
│   │   └── api/
│   ├── components/ (sequence builder, lead table, charts)
│   ├── lib/ (api client, hooks)
│   └── package.json
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── db/ (models, migrations)
│   │   ├── routers/ (auth, orgs, leads, campaigns, inboxes, webhooks)
│   │   ├── services/ (gmail, outlook, smtp, dns, ai, enrichment)
│   │   ├── tasks/ (sender, watcher, warmup, deliverability)
│   │   └── utils/
│   ├── tests/
│   ├── Dockerfile
│   └── requirements.txt
│
├── worker/ (celery app, beat schedule)
├── docker-compose.yml
├── .env.example
└── README.md
```

### Database Schema (PostgreSQL)

```sql
CREATE TABLE orgs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  plan TEXT DEFAULT 'free',
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  role TEXT DEFAULT 'member',
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE email_accounts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  email TEXT NOT NULL,
  provider TEXT NOT NULL,             -- gmail | outlook | smtp
  access_token TEXT, refresh_token TEXT,
  daily_limit INT DEFAULT 50,
  is_active BOOLEAN DEFAULT true,
  warmup_enabled BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  email TEXT NOT NULL,
  first_name TEXT, last_name TEXT, company TEXT, title TEXT,
  custom_fields JSONB DEFAULT '{}',
  status TEXT DEFAULT 'new',          -- new | in_campaign | replied | unsubscribed | bounced
  unsubscribed BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT now(),
  UNIQUE (org_id, email)
);

CREATE TABLE lists (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  name TEXT NOT NULL
);

CREATE TABLE list_leads (
  list_id UUID REFERENCES lists(id) ON DELETE CASCADE,
  lead_id UUID REFERENCES leads(id) ON DELETE CASCADE,
  PRIMARY KEY (list_id, lead_id)
);

CREATE TABLE campaigns (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  name TEXT NOT NULL,
  status TEXT DEFAULT 'draft',        -- draft | active | paused | completed
  daily_limit INT DEFAULT 50,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE campaign_inboxes (
  campaign_id UUID REFERENCES campaigns(id) ON DELETE CASCADE,
  email_account_id UUID REFERENCES email_accounts(id) ON DELETE CASCADE,
  PRIMARY KEY (campaign_id, email_account_id)
);

CREATE TABLE steps (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  campaign_id UUID REFERENCES campaigns(id) ON DELETE CASCADE,
  position INT NOT NULL,
  subject TEXT, body TEXT,
  delay_days INT DEFAULT 2,
  type TEXT DEFAULT 'email'           -- email | reply_draft
);

CREATE TABLE sends (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  campaign_id UUID REFERENCES campaigns(id),
  step_id UUID REFERENCES steps(id),
  lead_id UUID REFERENCES leads(id),
  email_account_id UUID REFERENCES email_accounts(id),
  status TEXT DEFAULT 'queued',       -- queued | sent | failed | bounced
  sent_at TIMESTAMPTZ,
  message_id TEXT,
  error TEXT,
  created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE replies (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID REFERENCES orgs(id),
  lead_id UUID REFERENCES leads(id),
  email_account_id UUID REFERENCES email_accounts(id),
  subject TEXT, snippet TEXT, received_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE warmup_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email_account_id UUID REFERENCES email_accounts(id),
  kind TEXT NOT NULL,                 -- sent | received | opened | replied | starred
  partner_email TEXT, created_at TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE activity_log (
  id BIGSERIAL PRIMARY KEY,
  org_id UUID REFERENCES orgs(id),
  user_id UUID REFERENCES users(id),
  action TEXT NOT NULL,
  entity_type TEXT, entity_id TEXT,
  meta JSONB DEFAULT '{}',
  created_at TIMESTAMPTZ DEFAULT now()
);
CREATE INDEX idx_sends_queued ON sends(status) WHERE status = 'queued';
CREATE INDEX idx_activity_org ON activity_log(org_id, created_at DESC);
```

### API Endpoints

```
POST   /api/auth/register
POST   /api/auth/login
GET    /api/orgs/me

GET    /api/leads?list_id=&q=&page=
POST   /api/leads/import            (CSV)
PATCH  /api/leads/:id
POST   /api/leads/bulk              (tag, unsubscribe, delete)

GET    /api/lists
POST   /api/lists
DELETE /api/lists/:id

GET    /api/inboxes
POST   /api/inboxes                 (OAuth connect Gmail/Outlook)
PATCH  /api/inboxes/:id             (daily_limit, active, warmup)
POST   /api/inboxes/:id/test        (send test email)

GET    /api/campaigns
POST   /api/campaigns
GET    /api/campaigns/:id
PATCH  /api/campaigns/:id
POST   /api/campaigns/:id/activate
POST   /api/campaigns/:id/pause
POST   /api/campaigns/:id/steps     (sequence builder)
GET    /api/campaigns/:id/stats     (sent, replies, bounces, spam)

POST   /api/ai/write-sequence       (product + persona -> steps)
POST   /api/ai/draft-reply          (thread -> reply draft)

GET    /api/deliverability/check?domain=     (SPF/DKIM/DMARC)
GET    /api/warmup/stats

POST   /api/webhooks/zapier         (campaign events out)
POST   /api/webhooks/reply          (inbound reply from IMAP watcher)
```

### AI Agent Design

**Agent 1 — SequenceWriter:**
```
Input: product/service description, target persona, tone, lead company context
Steps:
  1. Extract value props + objections from description
  2. Generate 3-5 step sequence (intro → value → proof → CTA → break-up)
  3. Add {{first_name}}, {{company}} variable slots + 1-line personalization opener
  4. Enforce length caps + unsubscribe line
Output: JSON array of steps {subject, body, delay_days}
```

**Agent 2 — ReplyDraft (MVP: draft-only):**
```
Input: original thread + latest prospect reply + lead profile
Steps:
  1. Classify intent (interested / question / not now / out of office)
  2. Draft contextual reply in sender's voice, under 150 words
  3. Flag high-intent replies for immediate human review
Output: {intent, draft, suggested_action}
```

**Agent 3 — CampaignOptimizer (Phase 2):**
```
Input: campaign stats (open, reply, spam) per variant
Steps: suggest subject/body A/B variants, recommend pacing changes
Output: optimization suggestions list
```

### SEO & Content Strategy

**Keyword clusters:**
1. **"cold email software"** (high volume, commercial) — comparison pages: vs Instantly, vs Smartlead, vs lemlist, vs Apollo.
2. **"email warm-up"** (medium, commercial) — what is it, how to warm up Gmail/Outlook, warm-up tools, free checker.
3. **"cold email deliverability"** (medium, informational) — SPF/DKIM/DMARC guides, spam score checkers, inbox placement tests, bounce rate benchmarks.
4. **"cold email templates / AI cold email"** (high volume, informational) — template libraries, AI sequence examples per industry.

**Funnel:** TOFU = deliverability guides + free SPF/DKIM checker tool (lead magnet) · MOFU = templates + case studies · BOFU = pricing/comparison pages + free trial CTA.

**30-day sprint:** 2 cluster-1 pages, 4 deliverability guides, 8 template posts, free DNS-check tool, 1 case study, backlinks via "cold email benchmarks" original research.

### Growth & Viral Loops
- **Referral loop:** give 1 month free per successful referral (target K = 0.15 → push K > 0.3 via double-sided rewards).
- **Tool-led growth:** free SPF/DKIM/DMARC checker + free placement test = top-of-funnel capture (email-gated).
- **Agency channel:** agency plan with white-label reports = 1 agency brings 5-20 clients.
- **Content loop:** every campaign's benchmark data becomes a published report → links → leads.
- **Product loop:** warm-up success = users connect more inboxes = higher sending limits = stickier.

### Revenue Model & Unit Economics

**Model:** SaaS subscriptions + usage add-ons (enrichment credits, placement test credits) + managed services (higher margin, low volume).

| Tier | Price | COGS/mo | Margin |
|---|---|---|---|
| Starter | $37 | ~$4 (send API + DB) | ~89% |
| Business | $77 | ~$10 | ~87% |
| Agency | $497 | ~$40 (isolated infra) | ~92% |

**CAC estimate:** paid ads (cold email keyword) $60-90 · SEO $15-25 · referral $0-10 → blended ~$45.
**LTV:** avg revenue/user $55/mo × 14-mo lifetime = **$770** → LTV:CAC ≈ 17:1 (very healthy; expect it to decay as you scale ads).
**12-month projection (optimistic):** 300 users × $55 = $16.5K MRR by month 12; break-even at ~100 paying users ($5.5K MRR).

### Tech Stack Summary

| Layer | Choice | Reason |
|---|---|---|
| Frontend | Next.js 14 + Tailwind + Shadcn | Fast dev, Vercel deploy |
| Backend | FastAPI (Python) | Async, auto-docs, AI-native |
| Database | Supabase (PostgreSQL) | Managed, auth, RLS, real-time |
| Queue | Celery + Redis (Railway) | Sends, watchers, warmup |
| Sending | Gmail API + Outlook Graph + SMTP | Native APIs beat raw SMTP for deliverability |
| AI | OpenAI (gpt-4o-mini class) | Cheap, good sequence writing |
| Auth | Supabase Auth + JWT | Built-in |
| DNS checks | dns-python / dnspython | SPF/DKIM/DMARC free |
| Hosting | Vercel + Railway | Simple, scalable |
| Monitoring | Sentry + Logfire | Errors + perf |

### Estimated MVP Build Time

| Phase | Time | Deliverable |
|---|---|---|
| Setup + Auth + Orgs | 3 days | Scaffold, org model, Stripe-ready billing |
| Leads + Lists + Import | 4 days | CSV import, dedupe, suppression |
| Inboxes (Gmail OAuth + SMTP) | 5 days | Connect, test send, daily caps |
| Campaign engine + sender worker | 7 days | Sequences, rotation, throttling, retries |
| Reply detection + webhooks | 4 days | IMAP watch, reply log, Zapier webhook |
| AI sequence writer | 3 days | Prompt pipeline, step generation |
| Deliverability panel + warm-up basic | 4 days | DNS checks, self-warmup scheduler, dashboard |
| Polish + Deploy | 3 days | Docs, seed data, deploy |
| **Total** | **~33 days (1-2 devs)** | **Working MVP** |

### Cost Estimates (Monthly)

| Service | Cost | Notes |
|---|---|---|
| Vercel Pro | $20 | Frontend |
| Railway (API + workers) | $30 | 2 services + Redis |
| Supabase Pro | $25 | DB + Auth |
| OpenAI API | $30-100 | Sequence writing, low volume |
| Gmail/Outlook API | $0 | Free tiers |
| Enrichment partner (Phase 2) | $0.01-0.05/lead | Pay per use |
| Sentry | $0 | Free tier |
| **Total** | **~$105-175/mo** | Scales with send volume |

### Future Phases

**Phase 2 — Differentiate ($2-3K infra)**
- Chrome lead scraper extension · AI Reply Agent (auto-send with human-in-loop) · enrichment credits (Hunter/Apollo resale) · A/B testing · Zapier/Make native integration · white-label agency reports

**Phase 3 — The moat attempt ($10-20K + ops)**
- Private warm-up pool (seed 50-100 partner inboxes, then user-supplied network) · placement testing seed network · deliverability monitor with alerts · Outlook/Google deliverability diagnostics

**Phase 4 — Scale**
- Agency isolated servers (per-client sub-IPs) · dedicated connector IP rotation · multi-region sending · SOC2/GDPR compliance pack · enterprise SSO

---

*This doc serves as the source of truth for the PlusVibe clone project.*
*Last updated: 2026-08-12*
