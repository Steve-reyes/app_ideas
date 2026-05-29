# Dreaming (Comprehensible Input Language Learning) Analysis & Build Plan

> **Date:** 2026-05-29
> **Source:** https://app.dreaming.com
> **Status:** Competitor analysis → MVP build scope

---

## 1. What Dreaming Is

Comprehensible input video library for learning Spanish & French. Users watch leveled videos (superbeginner → advanced) and progress through 7 levels based on cumulative listening hours. Time's Best Inventions 2025, millions of users.

### Their 3-Step Flow
1. **Pick a language** — Spanish or French
2. **Watch leveled videos** — browse/sort by level (superbeginner → advanced), country dialect, guide, topic
3. **Track progress** — daily goal (min/day), cumulative hours unlock new levels (50h → 150h → 300h → 600h → 1000h → 1500h)

### Target Market
Spanish & French learners (primarily English speakers). $8/mo premium. Free tier with limited videos. Web app + Android app. Key competitors: LingQ ($16/mo), LangTrak ($25/mo).

### Key Features Observed
| Feature | Details |
|---------|---------|
| Language selection | Spanish (massive library), French |
| Video browser | Grid with thumbnails, title, duration, level badge |
| Filters | Sort, Level, Country dialect, More (topic, guide) |
| Watch page | Video player, like/dislike, share, download, description, tags, comments |
| Series | Curated video collections (e.g., Pasapalabra tournament) |
| Levels | 7 levels based on cumulative listening hours |
| Daily goal tracking | Goal minutes/day, streak tracking (sign-in required) |
| Premium paywall | Some videos locked behind premium icon |
| Auth | Email-based login/signup |
| Mobile | Android app (Google Play), no iOS |

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|-----------|-----------|-----|
| Video content library | 🔴 Hard | 1000+ hours of original content shot by native speakers. This is the core moat — can't replicate fast |
| Content production pipeline | 🔴 Hard | Scripting, filming, editing, leveling, captioning hundreds of videos per language |
| Language leveling system | 🟡 Medium | Need a reliable rubric to tag content by difficulty (vocab density, speech speed, visual support) |
| Progress tracking | 🟢 Easy | CRUD for watch history, cumulative minutes, daily goals |
| Recommendation engine | 🟡 Medium | "More videos like this" based on tags (level, topic, guide, dialect) |
| Video streaming infra | 🟡 Medium | CDN, transcoding, storage — manageable with Mux/Cloudflare |
| Premium subscription | 🟢 Easy | Stripe + webhooks for access gating |
| Mobile apps | 🟡 Medium | React Native or Flutter wraps the web app for MVP |

### The Moats
- **Original content library** — 1000+ hours of native speaker videos. This takes years and $100k+ to build
- **Brand + community** — millions of users, Reddit community, testimonials from CEO of Yelp, influencer endorsements
- **ALG methodology expertise** — Pablo Román (founder) pioneered the comprehensible input approach for Spanish learners
- **Content in multiple dialects** — Argentina, Spain, Mexico, Colombia, etc.

---

## 3. MVP That CAN Be Built

### MVP Features
1. **Video library with levels** — seed 50-100 videos using AI-generated content (Talking heads with illustrations) + user uploads
2. **Video watch experience** — player, like/dislike, share, download, description, tags, comments
3. **Progress tracking** — cumulative listening time, daily goal, level progression
4. **Auth** — email/password + Google OAuth
5. **Premium subscription** — Stripe, gate premium content
6. **Browse + filter** — by level, topic, dialect, duration
7. **Series** — curated playlists
8. **Mobile web** — PWA first, native wrapper later

### What We DON'T Build (MVP)
- Multiple languages (start with Spanish only, add French later)
- AI-generated content pipeline (record with real humans, use whiteboard + illustration style)
- Advanced recommendation engine (simple tag-based filtering is fine)
- iOS native app (PWA + Android later)
- Spaced repetition / vocab tools (Dreaming doesn't have these either)

---

## 4. Complete Project Infra Design

### Product Requirements Document (PRD)

#### User Personas
| Persona | Goal | Pain Point |
|---------|------|------------|
| **María** — 28, intermediate Spanish learner | Wants to understand native speakers naturally | Grammar drills bore her, wants engaging content |
| **Carlos** — 35, beginner from zero | Wants painless entry to Spanish | Existing apps feel like work, needs low-friction immersion |
| **Ana** — 22, advanced learner | Wants native-speed content to reach fluency | Intermediate content is too easy, native media too hard |

#### User Stories
| Story | Acceptance Criteria |
|-------|-------------------|
| As a learner, I want to watch videos sorted by level so I can find content I understand | Browse page shows videos with level badges; filter by level works |
| As a learner, I want my watch time tracked so I can see my progress | Total hours displayed; daily goal updates in real-time |
| As a learner, I want to unlock harder content as I progress | Level gates content; threshold hours calculated server-side |
| As a learner, I want to save/bookmark videos | Save button on watch page; saved tab in profile |
| As a paying user, I want premium-only videos unlocked | Premium badge on thumbnails; Stripe webhook removes gate |

#### Success Metrics
| KPI | Target |
|-----|--------|
| DAU (daily active users) | 500 by month 3 |
| Avg watch time/user/day | 15 min |
| Premium conversion rate | 5% |
| Time to level 3 (150h) | N/A — long-term. Proxy: videos watched/week = 10+ |
| Bounce rate on browse page | < 40% |

### Competitive Landscape Matrix

| Feature | Dreaming (clone) | Dreaming (actual) | LingQ | Duolingo | Babbel |
|---------|:---:|:---:|:---:|:---:|:---:|
| Video library | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ | ⭐ |
| CI methodology | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ | ⭐ |
| Progress levels | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Mobile | ⭐⭐ (PWA) | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Premium $/mo | $8 | $8 | $16 | $7 | $15 |
| Content variety | ⭐⭐ (50-100) | ⭐⭐⭐⭐⭐ (1000+) | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| Speaking practice | ❌ | ❌ | ❌ | ✅ | ✅ |
| Community | ❌ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                   Frontend (VPS or Vercel)                   │
│                Next.js 14 App Router + Tailwind              │
│         Shadcn/ui components + Tailwind CSS styling          │
└───────────────┬─────────────────────────────────────────────┘
                │ REST + WebSocket
┌───────────────▼─────────────────────────────────────────────┐
│              Backend (Express.js + Node.js)                  │
│             PostgreSQL + Redis (queues + caching)            │
│     Stripe webhooks for subscription management              │
└───────────────┬─────────────────────────────────────────────┘
                │
┌───────────────▼─────────────────────────────────────────────┐
│                     CDN / Storage                            │
│            Mux for video transcoding + streaming             │
│     Cloudflare R2 + Mux direct upload for video hosting      │
└───────────────┬─────────────────────────────────────────────┘
                │
┌───────────────▼─────────────────────────────────────────────┐
│                   Background Workers                         │
│       BullMQ for video processing, thumbnail generation      │
└─────────────────────────────────────────────────────────────┘
```

### Directory Structure
```
dreaming-clone/
├── frontend/
│   ├── app/
│   │   ├── (auth)/
│   │   │   ├── login/page.tsx
│   │   │   └── signup/page.tsx
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── [lang]/
│   │   │   │   ├── browse/page.tsx
│   │   │   │   ├── series/page.tsx
│   │   │   │   ├── watch/[id]/page.tsx
│   │   │   │   ├── premium/page.tsx
│   │   │   │   └── progress/page.tsx
│   │   ├── api/
│   │   │   ├── auth/
│   │   │   ├── videos/
│   │   │   ├── progress/
│   │   │   └── subscriptions/
│   │   └── layout.tsx
│   ├── components/
│   │   ├── VideoCard.tsx
│   │   ├── VideoPlayer.tsx
│   │   ├── LevelBadge.tsx
│   │   ├── DailyGoal.tsx
│   │   ├── FilterBar.tsx
│   │   └── PremiumGate.tsx
│   ├── lib/
│   │   ├── api.ts
│   │   ├── auth.ts
│   │   └── types.ts
│   └── package.json
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   │   ├── auth.ts
│   │   │   ├── videos.ts
│   │   │   ├── progress.ts
│   │   │   └── subscriptions.ts
│   │   ├── services/
│   │   │   ├── mux.ts
│   │   │   ├── stripe.ts
│   │   │   ├── leveling.ts
│   │   │   └── auth.ts
│   │   ├── db/
│   │   │   ├── schema.ts
│   │   │   └── migrations/
│   │   ├── middleware/
│   │   │   ├── auth.ts
│   │   │   └── premium.ts
│   │   └── index.ts
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml
└── README.md
```

### Database Schema (PostgreSQL)

```sql
-- Users
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255),
  google_id VARCHAR(255) UNIQUE,
  name VARCHAR(255),
  avatar_url TEXT,
  language VARCHAR(10) DEFAULT 'spanish',
  level INTEGER DEFAULT 1,
  total_minutes INTEGER DEFAULT 0,
  daily_goal INTEGER DEFAULT 15,
  is_premium BOOLEAN DEFAULT false,
  stripe_customer_id VARCHAR(255),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Videos
CREATE TABLE videos (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  language VARCHAR(10) NOT NULL,
  title VARCHAR(500) NOT NULL,
  description TEXT,
  duration_seconds INTEGER NOT NULL,
  level VARCHAR(50) NOT NULL, -- superbeginner, beginner, intermediate, advanced
  dialect VARCHAR(100), -- argentina, spain, mexico, colombia
  guide VARCHAR(255), -- agustina, andres, pablo, etc.
  topic VARCHAR(255), -- language, history, travel, culture, etc.
  is_premium BOOLEAN DEFAULT false,
  mux_asset_id VARCHAR(255),
  mux_playback_id VARCHAR(255),
  thumbnail_url TEXT,
  video_url TEXT,
  series_id UUID REFERENCES series(id),
  sort_order INTEGER DEFAULT 0,
  published_at TIMESTAMPTZ DEFAULT NOW(),
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Series (curated playlists)
CREATE TABLE series (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  language VARCHAR(10) NOT NULL,
  title VARCHAR(500) NOT NULL,
  description TEXT,
  level VARCHAR(50),
  thumbnail_url TEXT,
  video_count INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Tags (for filtering)
CREATE TABLE tags (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(100) NOT NULL,
  type VARCHAR(50) NOT NULL -- topic, dialect, guide, level
);

CREATE TABLE video_tags (
  video_id UUID REFERENCES videos(id) ON DELETE CASCADE,
  tag_id UUID REFERENCES tags(id) ON DELETE CASCADE,
  PRIMARY KEY (video_id, tag_id)
);

-- Watch History
CREATE TABLE watch_history (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  video_id UUID REFERENCES videos(id) ON DELETE CASCADE,
  seconds_watched INTEGER DEFAULT 0,
  completed BOOLEAN DEFAULT false,
  liked BOOLEAN,
  watched_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, video_id)
);

-- Daily Progress
CREATE TABLE daily_progress (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  minutes_watched INTEGER DEFAULT 0,
  UNIQUE(user_id, date)
);

-- Bookmarks
CREATE TABLE bookmarks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  video_id UUID REFERENCES videos(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, video_id)
);

-- Subscriptions
CREATE TABLE subscriptions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  stripe_subscription_id VARCHAR(255) UNIQUE,
  status VARCHAR(50) DEFAULT 'active', -- active, cancelled, past_due
  current_period_start TIMESTAMPTZ,
  current_period_end TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### API Endpoints

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | /api/auth/signup | No | Email/password registration |
| POST | /api/auth/login | No | Email/password login |
| POST | /api/auth/google | No | Google OAuth |
| GET | /api/videos | No | List videos (filters: level, dialect, topic, guide, series) |
| GET | /api/videos/:id | No | Single video details |
| POST | /api/videos/:id/like | Yes | Like/dislike a video |
| GET | /api/series | No | List all series |
| GET | /api/series/:id | No | Series details + videos |
| GET | /api/progress | Yes | User progress (total min, level, daily goal) |
| POST | /api/progress/watch | Yes | Log watch session (video_id, seconds_watched) |
| GET | /api/progress/daily | Yes | Daily progress |
| GET | /api/bookmarks | Yes | List user bookmarks |
| POST | /api/bookmarks | Yes | Add bookmark |
| DELETE | /api/bookmarks/:id | Yes | Remove bookmark |
| POST | /api/subscriptions/create | Yes | Create Stripe checkout session |
| POST | /api/subscriptions/webhook | No | Stripe webhook |
| GET | /api/subscriptions/status | Yes | Check premium status |

### AI Agent Design

| Agent | Input | Processing | Output |
|-------|-------|------------|--------|
| **Content Leveler** | Video transcript, duration, vocab complexity | Analyze speech speed, unique word count, CEFR level mapping | Level tag (superbeginner → advanced) |
| **Transcript Generator** | Raw video audio | Whisper/Deepgram transcription with speaker diarization | Timestamped transcript, subtitles |
| **Tag Recommender** | Video title + description + transcript | NLP keyword extraction, topic clustering | Suggested tags (topic, dialect, guide) |
| **Content Script Writer** | Level + topic + dialect brief | Generate script outline for native speaker video | Script with visual cues, whiteboard notes |

### SEO & Content Strategy

| Channel | Tactic |
|---------|--------|
| Blog | "Best comprehensible input resources for [language]", method explainers |
| YouTube | Free sample videos driving to app for full library |
| Product Hunt | Launch MVP as "Dreaming Spanish alternative" |
| Reddit | r/dreamingspanish, r/Spanish, r/languagelearning — genuinely useful posts |
| Social | TikTok/IG shorts of CI method tips, testimonials |
| Referral | "Share with a friend, both get 1 week free premium" |

### Growth & Viral Loops

1. **Content virality** — individual videos are shareable (link, embed). Great video → shares → signups
2. **Daily streak** — "I've watched 30 days straight" badges, shareable to social
3. **Referral program** — free premium days for inviting friends
4. **Level up celebrations** — "I reached Level 4!" share card

### Revenue Model & Unit Economics

| Tier | Price | Features |
|------|-------|----------|
| Free | $0 | Limited video library (50 newest), no progress tracking, ads |
| Premium | $8/mo | Full library, progress tracking, no ads, downloads, series |

**Unit Economics:**
| Metric | Estimate |
|--------|----------|
| CAC (paid ads) | $15-25 |
| LTV (6mo avg retention) | $48 |
| Gross margin | ~85% (CDN + Stripe + Mux) |
| Breakeven users | 500 premium users = $4,000/mo revenue |
| Server cost/user/mo | ~$0.15 (CDN-heavy at scale) |

### Tech Stack Summary

| Layer | Choice | Why |
|-------|--------|-----|
| Frontend | Next.js 14 + Tailwind + Shadcn | Fast dev, great DX, PWA support |
| Backend | Express.js + TypeScript | Matches existing infra, fast iteration |
| DB | PostgreSQL | Reliable, great for relational video/playlist data |
| Cache | Redis | Session store, rate limiting, leaderboard caching |
| Video | Mux | Transcoding, streaming, thumbnails — no infra management |
| Auth | NextAuth.js | Google OAuth + email/password out of box |
| Payments | Stripe | Subscription management, webhooks |
| Hosting | VPS (existing) or Railway | Docker-compose deploy |
| Storage | Cloudflare R2 | S3-compatible, cheap, no egress fees |
| Queue | BullMQ + Redis | Video processing jobs |

### Estimated MVP Build Time

| Phase | Duration | Deliverable |
|-------|----------|-------------|
| Setup + auth | 3 days | DB, auth flow, user model |
| Video upload + player | 5 days | Mux integration, upload flow, player component |
| Browse + filters | 3 days | Video grid, filter bar, tag system |
| Progress tracking | 3 days | Watch history, daily goal, level calculation |
| Watch page | 2 days | Player, like, share, download, comments |
| Premium | 3 days | Stripe checkout, webhook, content gating |
| Series | 2 days | Playlist curation UI |
| Content seed | 5 days | Record/edit 50 videos, upload |
| Polish + bug fixes | 4 days | Responsive, loading states, error handling |
| **Total** | **30 days** | **Full MVP** |

### Cost Estimates (Monthly)

| Service | Cost |
|---------|------|
| VPS (existing) | ~$15 |
| Mux (video streaming) | ~$50/1000 videos watched |
| Cloudflare R2 | ~$10 |
| Stripe (2.9% + 30¢) | ~$30 at 500 users |
| PostgreSQL (existing) | $0 |
| Redis (existing) | $0 |
| **Total** | **~$105/mo** |

### Future Phases

| Phase | When | Features |
|-------|------|----------|
| Phase 2 | Month 3 | French language support, iOS app (React Native) |
| Phase 3 | Month 6 | AI content generator (synthetic talking head videos), community uploads |
| Phase 4 | Month 9 | Speaking practice (AI tutor), spaced repetition, vocab tracking |
| Phase 5 | Month 12 | More languages (German, Japanese, Portuguese), tutor marketplace |

---

## 5. Quick Start for Development

```bash
# Clone
git clone https://github.com/Steve-reyes/dreaming-clone.git
cd dreaming-clone

# Install
cd backend && npm install
cd ../frontend && npm install

# Environment
cp backend/.env.example backend/.env  # Fill in: DATABASE_URL, MUX_TOKEN, STRIPE_KEY
cp frontend/.env.example frontend/.env

# Database
cd backend && npm run db:migrate

# Run
docker-compose up -d  # PostgreSQL + Redis
cd backend && npm run dev
cd frontend && npm run dev

# Open
open http://localhost:3000
```

## 6. Docker Compose

```yaml
version: '3.8'
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: dreaming_clone
      POSTGRES_PASSWORD: changeme
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - 5432:5432

  redis:
    image: redis:7-alpine
    ports:
      - 6379:6379

  backend:
    build: ./backend
    ports:
      - 4000:4000
    env_file: ./backend/.env
    depends_on:
      - db
      - redis

  frontend:
    build: ./frontend
    ports:
      - 3000:3000
    env_file: ./frontend/.env
    depends_on:
      - backend

volumes:
  pgdata:
```
