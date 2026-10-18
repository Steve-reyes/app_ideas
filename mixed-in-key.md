# Mixed In Key Analysis & Build Plan

> Prepared 19 Oct 2026. Competitor: mixedinkey.com — desktop software suite for DJs and producers.
> All pricing on their site is JS-rendered; figures below are marked **VERIFY** where scraped indirectly.

---

## 1. What Mixed In Key Is

A **desktop application suite** (Windows + macOS), not a SaaS. The core product analyses local audio files and writes musical metadata back into the files themselves.

**Core propositions:**

- **Key detection** — analyses any track, returns musical key mapped to the **Camelot Wheel** (8A, 8B, 9A…), the notation DJs use for harmonic mixing.
- **BPM detection** — tempo, half/double-time aware.
- **Energy Level** — proprietary 1–10 score, plus per-section energy breakdown across the track.
- **Cue points** — automatic detection of interesting positions (drop, breakdown, outro), snapped to playback position.
- **Tag writing** — embeds key, energy, BPM, comments into ID3 / MP4 tags so Serato, Traktor, rekordbox and VirtualDJ display them natively.
- **Playlist management** — folders, nested playlists, filtering by key/tempo/energy/genre.

### Their 3-Step Flow

1. **Point it at a folder** → batch analysis runs offline, in parallel, on the local machine.
2. **Read the dashboard** → sortable table of BPM, Camelot key, energy, cue count.
3. **Export** → tags written to files + playlists built by harmonic compatibility; DJ software picks them up unchanged.

### Product line (they are a suite, not one app)

| Product | What it does | Implication for us |
|---|---|---|
| Mixed In Key 11 | Core key/BPM/energy analysis + tag writing | The thing worth cloning |
| Platinum Notes 10 | Remastering: pitch correction, limiting, harmonic restoration | Separate DSP product, high effort |
| Mixed In Key Live | Real-time analysis of system audio (Spotify, YouTube) via a virtual audio device | OS-level audio capture, different problem |
| Studio Edition | Same key detection as a VST/AU plugin inside a DAW | Plugin SDK work |
| DJ.Studio | Timeline editor that auto-builds DJ sets, exports to rekordbox/Serato | Closest to a cloud/web product |
| Pro | Suggests mashup and transition ideas from a dropped track | AI-adjacent, easy to beat with modern models |
| Captain / Wingman / Pilot / Human | MIDI chord, melody, bass, groove generators | Different market (producers) |
| Mashup | Web mashup tool | Small |

### Target Market

- **Primary:** working club/DJ-bar DJs and bedroom DJs with 2,000–100,000 local files. macOS dominant, rekordbox/Serato ecosystems.
- **Secondary:** mobile/wedding DJs (huge libraries, time-poor), radio/podcast selectors, music producers who need key info for sampling.
- **Willingness to pay:** high relative to other software — it is a tool of the trade, not a toy. One-time purchase culture, upgrades paid.
- **Distribution:** direct download + own store, no App Store, no subscription for the core app.

---

## 2. What Makes It Hard

### The Moats

| # | Moat | Why it bites | Severity |
|---|---|---|---|
| 1 | **Key detection accuracy** | Their entire brand is "we are the most accurate". Published comparisons put Mixed In Key well above naive algorithms. Getting to parity means a trained model on a large, human-labelled corpus (100k+ tracks, multiple annotators, genre-balanced). | 🔴 Hard |
| 2 | **Camelot notation licence** | Camelot wheel is licensed IP (Mark Davis). You cannot ship "Camelot" branding without a deal. Use **Open Key notation** (Beatport) or plain A minor / F# minor, and map it. | 🔴 Legal |
| 3 | **Energy Level** | Subjective, unstandardised, and they own the definition users trust. Reproducing "MIK energy 7" is a perceptual-modelling problem, not a formula. | 🔴 Hard |
| 4 | **Offline performance at scale** | 50,000-track library must analyse in a reasonable wall-clock time on a laptop. Needs C++/Rust DSP, threading, incremental re-analysis, and no cloud round-trip. | 🟡 Medium |
| 5 | **Tag writing correctness** | ID3v2.3 vs 2.4, MP4 atoms, UTF-16, Serato's own fields, rekordbox's XML, Traktor's NML. Breaking a DJ's tags destroys trust permanently. | 🟡 Medium |
| 6 | **DJ software integration surface** | rekordbox XML, Serato crates (`*.crate` binary), Traktor NML, VirtualDJ database — all undocumented, version-sensitive. | 🟡 Medium |
| 7 | **Brand / community trust** | 15+ years, sponsored DJs, "the pros use it". A no-name tool must earn trust through measured accuracy claims. | 🟡 Medium |
| 8 | **Platform reach** | Code-signed Windows + notarised macOS, both architectures, auto-update. Boring, expensive, unavoidable. | 🟢 Easy-Medium |

**Honest read:** this is a **desktop DSP product**, not a CRUD SaaS. The moat is the model plus years of tag-format field experience. Everything else is replicable in months.

---

## 3. MVP That CAN Be Built

### MVP Features

1. **Library scan** — point at folders, read existing tags, build local index (SQLite).
2. **BPM detection** — onset/beat tracking (aubio / Essentia). Reliable, well-understood.
3. **Key detection v1** — chroma features + Krumhansl-Schmuckler profiles + tuning correction → key + confidence score. Publish the accuracy honestly (~70–80% on electronic music).
4. **Key detection v2 (differentiator)** — small CNN on mel-spectrograms trained on a labelled corpus. Target 85%+ on a 2,000-track benchmark set. This is the moat work.
5. **Energy score** — composite of onset density, RMS/loudness curve, spectral flux, low-band energy; calibrated to a 1–10 scale via a small human-rated set.
6. **Cue points** — energy-change detection (novelty curve) → up to 8 cues per track, editable.
7. **Tag writing** — key, BPM, energy, comments into ID3v2.3/2.4 + MP4, with dry-run diff and full undo (backup of original tags).
8. **Playlist builder** — harmonic suggestions: same key, ±1 on the wheel, relative major/minor, ±3% tempo.
9. **Exports** — rekordbox XML, M3U8, CSV. Serato crate later.
10. **Licence + auto-update** — offline-tolerant licence check, signed update feed.

### What We DON'T Build (MVP)

- Platinum Notes-style remastering (different DSP discipline, big QA burden).
- Real-time system-audio capture (MIK Live) — virtual audio driver per OS is a project of its own.
- VST/AU/AAX plugins.
- DAW plugin hosting, chord generation (Captain/Wingman).
- Cloud library sync — privacy-sensitive; DJs hate uploading their crates.
- Streaming integrations (Beatport/Beatsource) — partner approvals.

---

## 4. Complete Project Infra Design

### Product Requirements Document (PRD)

**Primary user story**
> As a DJ with a 20,000-track library, I want to drop a folder onto the app and get accurate key, BPM and energy for every track, written into the files, so my existing DJ software shows them without me touching anything.

**Secondary stories**

- Re-analyse only new/changed files (incremental, by mtime + hash).
- Sort/filter the whole library by key, tempo band, energy — instantly.
- Build a playlist of 30 tracks that flows harmonically from A minor 124 BPM.
- Undo any tag write and get the original file back byte-identical.
- Work fully offline on a plane; licence validates for 30 days without network.

**KPIs**

| KPI | Target v1 | Target v2 |
|---|---|---|
| Key accuracy (benchmark, human-labelled) | ≥ 78% | ≥ 87% |
| BPM octave error rate | < 2% | < 1% |
| Analysis throughput | ≥ 40 tracks/min (M2 laptop) | ≥ 100/min |
| Crash-free sessions | ≥ 99.0% | ≥ 99.7% |
| Tag-write undo success | 100% | 100% |
| D30 retention (paid) | ≥ 55% | ≥ 65% |
| Trial → paid | ≥ 6% | ≥ 10% |

**RICE (top items)**

| Item | Reach | Impact | Confidence | Effort | Score |
|---|---|---|---|---|---|
| Key-detection CNN | 10 | 3 | 0.6 | 8 | 2.25 |
| Tag write + undo | 10 | 3 | 0.9 | 3 | 9.0 |
| Playlist harmonic builder | 8 | 2 | 0.9 | 2 | 7.2 |
| rekordbox XML export | 7 | 2 | 0.8 | 2 | 5.6 |
| Cue point detection | 7 | 2 | 0.7 | 3 | 3.27 |

### Competitive Landscape Matrix

| Capability | Mixed In Key 11 | rekordbox (built-in) | Serato | Traktor | KeyFinder (OSS) | **Us (target)** |
|---|---|---|---|---|---|---|
| Key detection | ★★★★★ | ★★★☆ | ★★★☆ | ★★★☆ | ★★☆ | ★★★★ |
| BPM | ★★★★★ | ★★★★ | ★★★★ | ★★★★★ | ★★★ | ★★★★ |
| Energy score | ★★★★★ | ✕ | ✕ | ★★ | ✕ | ★★★★ |
| Auto cue points | ★★★★ | ★★★ | ★★★ | ★★ | ✕ | ★★★ |
| Writes tags | ★★★★★ | partial | partial | partial | ★★★ | ★★★★★ |
| Works standalone | ★★★★★ | ✕ | ✕ | ✕ | ★★★★ | ★★★★★ |
| Batch 50k tracks | ★★★★★ | ★★★ | ★★★ | ★★★ | ★★ | ★★★★ |
| Price | one-time | bundled | bundled | bundled | free | one-time, lower |
| Modern UI | ★★★ | ★★★ | ★★★ | ★★ | ★ | ★★★★★ |

**Positioning:** beat them on **UI/UX, transparency of accuracy, undoable tag writes, and price** — not on being "more accurate" on day one. Publish a public benchmark and a re-analysis report so users see the diff before committing.

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│  Desktop app (Tauri 2 · Rust core · React/TS UI)            │
│                                                             │
│  ┌────────────┐   ┌──────────────┐   ┌──────────────────┐   │
│  │ Library    │   │ Analysis     │   │ Tag engine       │   │
│  │ indexer    │──▶│ worker pool  │──▶│ ID3 / MP4 / undo │   │
│  │ (SQLite)   │   │ (Rust + DSP) │   │ + backups        │   │
│  └────────────┘   └──────┬───────┘   └──────────────────┘   │
│         │                │                                  │
│         │         ┌──────▼────────┐   ┌──────────────────┐  │
│         └────────▶│ Model runtime │   │ Exporters        │  │
│                   │ ONNX (key CNN)│   │ rkbx XML/M3U8/CSV│  │
│                   └───────────────┘   └──────────────────┘  │
└───────────────┬─────────────────────────────────────────────┘
                │ HTTPS (licence, updates, telemetry opt-in)
┌───────────────▼─────────────────────────────────────────────┐
│  Cloud (small, boring, cheap)                               │
│  API: Fastify/Node or Go      DB: Postgres                  │
│  Licence signing · device binding · update manifest          │
│  Telemetry sink (anonymous, opt-in) · Stripe webhooks        │
│  Model distribution (signed, versioned ONNX bundles)         │
└─────────────────────────────────────────────────────────────┘
```

**Non-negotiables:** analysis never touches the network; the app is fully functional offline; cloud is only licence/update/telemetry.

### Directory Structure

```
mixkey/
├── apps/
│   ├── desktop/                  # Tauri shell
│   │   ├── src/                  # React + TS UI
│   │   └── src-tauri/            # Rust bridge, commands, updater
│   └── web/                      # marketing site, blog, free key tool
├── crates/
│   ├── audio-io/                 # decode, resample, metadata read
│   ├── dsp/                      # onsets, tempo, chroma, novelty, energy
│   ├── keymodel/                 # ONNX inference + post-processing
│   ├── tags/                     # id3/mp4 read-write + undo journal
│   ├── exporters/                # rekordbox xml, m3u8, csv, serato crate
│   └── store/                    # sqlite schema + migrations
├── services/
│   ├── api/                      # licence + account + telemetry
│   └── admin/                    # internal dashboard
├── ml/
│   ├── datasets/                 # labelled corpus manifests
│   ├── train/                    # training pipeline (PyTorch)
│   └── eval/                     # benchmark harness + reports
├── ops/
│   ├── docker-compose.yml
│   └── release/                  # sign + notarise + publish scripts
└── docs/
```

### Database Schema (PostgreSQL — cloud side)

```sql
CREATE TABLE users (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email         CITEXT UNIQUE NOT NULL,
  password_hash TEXT,
  name          TEXT,
  country       TEXT,
  created_at    TIMESTAMPTZ NOT NULL DEFAULT now(),
  deleted_at    TIMESTAMPTZ
);

CREATE TABLE orgs (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name       TEXT NOT NULL,
  owner_id   UUID NOT NULL REFERENCES users(id),
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE licenses (
  id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id        UUID NOT NULL REFERENCES users(id),
  product        TEXT NOT NULL,                -- 'core' | 'pro' | 'bundle'
  plan           TEXT NOT NULL DEFAULT 'perpetual',
  seats          INT  NOT NULL DEFAULT 1,
  license_key    TEXT UNIQUE NOT NULL,
  purchased_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
  expires_at     TIMESTAMPTZ,                   -- null = perpetual
  stripe_pi_id   TEXT,
  status         TEXT NOT NULL DEFAULT 'active'
);

CREATE TABLE devices (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id       UUID NOT NULL REFERENCES users(id),
  license_id    UUID REFERENCES licenses(id),
  fingerprint   TEXT NOT NULL,
  os            TEXT,
  app_version   TEXT,
  first_seen_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  last_seen_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (license_id, fingerprint)
);

CREATE TABLE libraries (           -- optional cloud metadata (opt-in)
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID NOT NULL REFERENCES users(id),
  name        TEXT NOT NULL,
  track_count INT NOT NULL DEFAULT 0,
  analyzed_at TIMESTAMPTZ,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE tracks (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  library_id    UUID NOT NULL REFERENCES libraries(id) ON DELETE CASCADE,
  content_hash  TEXT NOT NULL,
  path_hint     TEXT,                 -- optional, privacy-gated
  title         TEXT, artist TEXT, album TEXT,
  duration_ms   INT,
  bpm           NUMERIC(6,2),
  key_openkey   TEXT,                 -- 1d..12d / 1m..12m
  key_camelot   TEXT,                 -- internal mapping only
  energy        SMALLINT,             -- 1..10
  confidence    NUMERIC(4,3),
  model_version TEXT,
  analyzed_at   TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (library_id, content_hash)
);

CREATE TABLE playlists (
  id         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  library_id UUID NOT NULL REFERENCES libraries(id) ON DELETE CASCADE,
  name       TEXT NOT NULL,
  parent_id  UUID REFERENCES playlists(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE playlist_tracks (
  playlist_id UUID NOT NULL REFERENCES playlists(id) ON DELETE CASCADE,
  track_id    UUID NOT NULL REFERENCES tracks(id) ON DELETE CASCADE,
  position    INT NOT NULL,
  PRIMARY KEY (playlist_id, track_id)
);

CREATE TABLE jobs (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID NOT NULL REFERENCES users(id),
  kind        TEXT NOT NULL,           -- 'set_build' | 'tag_cleanup' | 'mashup'
  status      TEXT NOT NULL DEFAULT 'queued',
  input_json  JSONB NOT NULL,
  output_json JSONB,
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  finished_at TIMESTAMPTZ
);

CREATE TABLE activity_log (
  id         BIGSERIAL PRIMARY KEY,
  user_id    UUID REFERENCES users(id),
  device_id  UUID REFERENCES devices(id),
  event      TEXT NOT NULL,
  meta       JSONB,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX ON tracks (library_id, key_openkey, bpm);
CREATE INDEX ON devices (license_id);
CREATE INDEX ON activity_log (user_id, created_at DESC);
```

**Local (SQLite) mirrors** `libraries`, `tracks`, `playlists`, `playlist_tracks`, plus `tag_journal` (path, original tag blob, new blob, timestamp) for byte-level undo.

### API Endpoints

```
POST   /v1/auth/signup
POST   /v1/auth/login                  -> {access, refresh}
POST   /v1/auth/refresh
DELETE /v1/auth/session

GET    /v1/me
GET    /v1/licenses
POST   /v1/licenses/activate           {license_key, fingerprint, os, app_version}
POST   /v1/licenses/validate           {license_key, fingerprint} -> {valid, offline_until}
POST   /v1/licenses/deactivate         {device_id}
GET    /v1/licenses/:id/devices

GET    /v1/updates/:channel/:platform  -> signed manifest
GET    /v1/models/latest?product=core  -> {version, sha256, url}

POST   /v1/jobs                        {kind, input} -> {job_id}
GET    /v1/jobs/:id
GET    /v1/jobs?status=&limit=

POST   /v1/telemetry/batch             (opt-in, anonymised, batched)

POST   /v1/billing/checkout            -> Stripe session
POST   /v1/billing/webhook             (Stripe)
GET    /v1/billing/invoices

GET    /v1/libraries
POST   /v1/libraries
PUT    /v1/libraries/:id
GET    /v1/libraries/:id/tracks?key=&bpm_min=&energy_min=&limit=
```

Conventions: JSON, `Authorization: Bearer`, idempotency keys on POST that create money or licences, cursor pagination on lists, RFC7807 problem+json errors.

### AI Agent Design

Four agents, all optional and cloud-side except the last (local, no LLM).

**1. Set Builder Agent**
- *Input:* library subset, desired duration, start key/BPM, energy arc (warm-up → peak → close).
- *Steps:* filter by key wheel adjacency and tempo window → score energy trajectory against requested arc → solve ordering with a greedy + local-search pass → return track ids with transition notes (key move, tempo delta, cue in/out).
- *Output:* ordered set, per-transition rationale, export to rekordbox XML.

**2. Mashup Idea Agent**
- *Input:* one source track (analysis metadata + stem tags if available).
- *Steps:* find tracks within compatible key/tempo; rank by vocal presence (via stem separation confidence), arrangement overlap, energy contrast; generate 3 concrete ideas with the bar ranges to blend.
- *Output:* ranked ideas with timestamps and rationale.

**3. Tag Cleanup Agent**
- *Input:* messy library metadata dump.
- *Steps:* normalise artist/title patterns (`Artist - Title (Remix)`), strip promo junk, dedupe near-identical entries by hash + fuzzy title, map genre to a canonical list.
- *Output:* proposed diffs, human-approved, applied through the tag engine with undo.

**4. Local Analysis Assistant (no LLM)**
- Rules engine that explains *why* a key was chosen: top chroma peaks, tuning offset, confidence band. Builds trust — most competitors show a number and nothing else.

### SEO & Content Strategy

**Clusters**

- *Harmonic mixing* — "harmonic mixing", "camelot wheel explained", "what key is my song", "how to mix in key" → the guide is the top-of-funnel workhorse.
- *Key/BPM tools* (free, indexable) — "key and bpm finder", "song key detector online", "bpm counter" → free web tool as the lead magnet (upload → key/BPM, no signup, cap at 3 files/day).
- *Comparisons* — "mixed in key alternative", "mixed in key vs rekordbox key detection", "is mixed in key worth it" → high-intent, converts.
- *Format/how-to* — "rekordbox xml export", "serato crates", "ID3 key tag field" — engineers search this, it earns links.
- *Genre pages* — "techno key chart", "house bpm ranges".

**Funnel:** free tool → email → 14-day trial (full analysis, watermark-free but caps at 500 tracks) → one-time purchase.

**Link magnets:** public accuracy benchmark (downloadable dataset + our numbers vs theirs), Camelot/Open Key wheel poster, "energy level explained".

### Growth & Viral Loops

- **Free web key/BPM detector** — no signup, shareable results page (each result page is indexable and carries "analysed by …").
- **Playlist share cards** — an exported set generates an image/URL with the key/tempo path; DJs post these.
- **"Before/after tag report"** — users screenshot the clean library and post it. Build the report to look good.
- **Community seeding** — r/DJs, r/Beatmatch, DJ TechTools, Discord servers; give lifetime keys to 50 respected users for honest accuracy testing.
- **Affiliate** — DJ schools, YouTube educators (30% first-year).
- **Referral** — 1 month of Pro cloud features, or 20% off for both sides on one-time licences.

### Revenue Model & Unit Economics

**Model:** one-time perpetual licence per major version + paid upgrades, plus an optional small subscription for cloud extras (set builder, sync, mashup agent).

| Tier | Price | Notes |
|---|---|---|
| Core (analysis + tags) | **$39** launch / $49 list | Undercut MIK's core price |
| Pro (+ cue points, playlist AI, exports) | **$79** | The volume tier |
| Studio (+ plugin, stem tools, later) | **$149** | Pro users |
| Cloud add-on | **$4.99/mo** or $39/yr | Set builder, sync, mashup agent |
| Upgrade (major version) | 50% of list | MIK-style, keeps cashflow |

*Competitor reference (VERIFY before quoting publicly): Mixed In Key 11 sits around $58 list with recurring paid upgrades; DJ.Studio tiers observed at $149 / $169 / $209 / $347; bundles in the $200–$550 range. Their model proves DJs pay one-time for utility.*

**Unit economics (steady state, year 2)**

| Metric | Value |
|---|---|
| Blended ASP | $62 |
| Stripe + tax fees (≈6%) | -$3.7 |
| Support cost / sale (0.35 tickets × $4) | -$1.4 |
| Refund rate 4% | -$2.5 |
| **Net per sale** | **≈ $54** |
| CAC (content + ads blended) | $18–24 |
| Payback | immediate |
| Cloud add-on attach | 12% → +$0.60/mo per buyer cohort |

**Break-even:** at $6k/mo fixed cost (2 engineers + infra), ~115 net sales/month. Achievable at ~3k visitors/day with a 2.5% conversion on the free-tool funnel.

### Tech Stack Summary

| Layer | Choice | Why |
|---|---|---|
| Shell | **Tauri 2** (Rust) | 10–20 MB installers vs 120 MB Electron; native file system and audio performance |
| UI | React + TypeScript + Tailwind | Fast iteration, virtualised tables for 100k rows |
| Audio decode | Symphonia (Rust) + ffmpeg fallback | Broad codec coverage |
| DSP | Rust (`aubio`-style onset/tempo port, custom chroma) | Speed, no Python runtime in the shipped app |
| ML inference | ONNX Runtime (Rust bindings) | Ship the key model without a Python stack |
| Training | PyTorch + Essentia/librosa features | Offline, in `ml/` |
| Local DB | SQLite (WAL) | Single file, fast, portable |
| Cloud API | Fastify (Node/TS) or Go | Small surface: licence, updates, jobs |
| Cloud DB | PostgreSQL 16 | Boring, reliable |
| Jobs | BullMQ + Redis (or Postgres queue) | Only for cloud agents |
| Payments | Stripe (+ Paddle as merchant-of-record for VAT) | Global tax handling matters |
| Updates | Tauri updater, signed manifest on R2 | Cheap, no vendor lock |
| Signing | Windows EV cert + Apple Developer ID + notarisation | Non-negotiable for trust |
| Marketing site | Astro + Tailwind, on Cloudflare Pages | Fast, cheap, SEO-friendly |
| Free tool backend | Small Rust/Go service + ffmpeg, rate-limited | Must handle anonymous uploads safely |

### Estimated MVP Build Time

| Phase | Weeks | Deliverable |
|---|---|---|
| 0. Spike: decode → BPM → key on 100 tracks | 2 | Proof the DSP chain works end-to-end |
| 1. Core analysis engine (Rust) | 5 | BPM, chroma key, energy, incremental rescan |
| 2. Key model v1 (CNN) + benchmark harness | 6 | 85%+ on benchmark, ONNX shipped |
| 3. Tag engine + undo journal | 3 | ID3/MP4 write, byte-level rollback |
| 4. Desktop UI (library, filters, playlists) | 5 | Usable app on macOS + Windows |
| 5. Exports (rekordbox XML, M3U8, CSV) | 2 | DJ software hand-off works |
| 6. Licence, updater, signing, notarisation | 3 | Shippable installers |
| 7. Website, free tool, checkout | 3 | Funnel live |
| 8. Beta with 50 DJs, accuracy report | 4 | Public benchmark + fixes |

**Total: ~30 weeks (7 months)** with **2 engineers + 1 part-time ML**. Solo, realistically 12–14 months, and the key model is the risk.

### Cost Estimates (Monthly)

| Item | MVP phase | Post-launch |
|---|---|---|
| Cloud API + DB (small VPS/managed) | $40 | $90 |
| R2 storage + egress (installers, models) | $15 | $60 |
| CDN / marketing site | $0–20 | $40 |
| Stripe/Paddle fees | usage | ~6% of revenue |
| Apple Developer + Windows EV cert | $8 + ~$25 amortised | same |
| GPU training (spot/rented) | $120–300 | $60 |
| Email (transactional) | $15 | $40 |
| Sentry + analytics | $30 | $80 |
| **Total infra** | **≈ $230–450/mo** | **≈ $400–600/mo** |
| Team (2 eng + 0.5 ML) | $18k–30k/mo | same |

### Future Phases

- **Phase 2 (post-launch, +3 mo):** Serato crate write, Traktor NML, VirtualDJ DB; real-time BPM tap; cloud set builder.
- **Phase 3 (+6 mo):** stem separation (Demucs-class, on-device) → vocal/instrumental tagging, which unlocks genuinely better mashup ideas than MIK Pro.
- **Phase 4 (+9 mo):** Platinum Notes competitor — loudness-matching and pitch-correction remaster with A/B preview.
- **Phase 5 (+12 mo):** VST3/AU plugin (key display in-DAW), and a live-analysis mode via loopback capture (no virtual driver on Windows, CoreAudio tap on macOS 14.4+).
- **Phase 6:** mobile companion (library triage, set rehearsal), Beatport/Beatsource streaming links.

---

## 5. Bottom Line

- The **software is buildable**; the **accuracy reputation is not**, not in year one.
- Win the first 12 months on **UX, undoable tag safety, honest published accuracy, and price** — then close the accuracy gap with the CNN + a public benchmark that nobody else publishes.
- Biggest single risk: key-detection parity. Budget it as its own 6-week workstream with a real labelled dataset, not a side task.
- Second risk: **Camelot trademark** — ship Open Key notation and a mapping toggle.
- Realistic MVP: **7 months, 2 engineers + part-time ML, ~$400/mo infra**, launching at $39–$79 one-time.
