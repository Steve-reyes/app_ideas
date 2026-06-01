# DNA Raw Data Analyzer — Build Plan

> **Date:** 2026-06-01
> **Inspiration:** Genomelink, Promethease, SelfDecode, Genetic Genie, NutraHacker, GenomeInsight, FoundMyFitness
> **Status:** Competitor analysis → MVP build scope

---

## 1. What These Platforms Are

Users get DNA tested by 23andMe / AncestryDNA / MyHeritage → download raw data file (.txt/.csv) → upload to your platform → you match their ~600K SNPs against known research → output visual reports on health risks, allergies, nutrition, traits, drug response.

### The Common 3-Step Flow
1. **Upload** — Drag/drop raw DNA file (auto-detect provider format)
2. **Process** — Parse SNPs, match against variant database, calculate risks/traits
3. **Report** — Visual dashboard with badges, icons, color-coded risk levels, category cards

### Key Competitors

| Platform | Pricing | Reports | Visual Style | Market Position |
|---|---|---|---|---|
| **Genomelink** | Free traits + paid reports ($15-50) | 100+ traits, ancestry, wellness | Icon-based cards, clean UI, badge system | Mass consumer, social sharing |
| **Promethease** | $12-16 one-time | Deep medical variant report | Text-heavy, clinical | Serious health researchers |
| **SelfDecode** | $99-499/yr | 27+ health categories, DNA kits | Dashboard with progress bars | Health optimization |
| **Genetic Genie** | Free / $10 donation | Methylation, detox, ClinVar variants | Minimal, functional | Nutrition-focused |
| **NutraHacker** | $37-395 lifetime | Supplement-nutrition reports | Simple text reports | Supplement industry |
| **GenomeInsight** | $49 one-time | 27 reports (client-side) | Modern dashboard with icons | Privacy-focused, budget |
| **FoundMyFitness** | $25-40 | Science-backed health reports | Article-style with charts | Science-first audience |
| **Codegen.eu** | Free | SNPedia-based variant list | Minimal, text | Budget users |

### Target Market
- 50M+ people who've taken DTC DNA tests globally (23andMe 15M+, AncestryDNA 25M+, MyHeritage 5M+)
- Health-conscious consumers wanting more from their existing $99-199 DNA test
- Biohackers, supplement users, people with unexplained health issues
- Average willingness to pay: $12-99 one-time or $99-199/yr subscription

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|---|---|---|
| **SNP-Health Database Curation** | 🔴 Hard | Need to compile 10K-50K SNP associations from SNPedia/ClinVar/GWAS + keep updated as new research publishes. SNPedia is dying (last update ~2019). ClinVar is massive but not user-friendly. |
| **File Format Parsing** | 🟢 Easy | 23andMe v3/v4/v5, AncestryDNA, MyHeritage, FTDNA, LivingDNA, VCF — formats are known, just regex/CSV parsing. |
| **Visual Trait Report UI** | 🟡 Medium | Genomelink-style icon badges per trait (allergy, skin, sleep, etc.) with animated meters, category cards. React + Tailwind + icon library does this. |
| **Polygenic Risk Scoring** | 🔴 Hard | Combining multiple SNPs into a single risk score requires GWAS statistical models. Simple binary matching is fine for MVP. |
| **Privacy / Client-Side Processing** | 🟡 Medium | Full client-side JS processing (like GenomeInsight) is possible but limits report depth. Server-side is simpler but needs HIPAA-grade security. |
| **Pharmacogenomics (PGx)** | 🔴 Hard | Drug-gene interactions require CPIC guidelines, PharmGKB data. Regulatory risk — medical advice liability. Best as "educational only." |
| **Ancestry DNA Matching** | 🔴 Hard | Genomelink offers cross-platform relative matching. Requires large user base + complex IBD (identity-by-descent) algorithms. Skip for MVP. |
| **Regulatory Compliance** | 🟡 Medium | GINA (US), GDPR (EU), HIPAA if health claims. Must label as "educational/informational only, not medical advice." |

### The Moats
- **SNPedia's stagnation** — the best public SNP-to-health database is dying. You need to build your own curated variant database or license one.
- **Trust/Privacy** — People are terrified of their DNA data being leaked/sold. Privacy-first marketing is a moat if executed well.
- **Accuracy** — If your reports are wrong (e.g., labeling harmless variants as "high risk"), you lose all credibility instantly.
- **Network effects** — Genomelink's DNA matching requires a large user database. Hard to replicate early.

---

## 3. MVP That CAN Be Built

### MVP Features
1. **File Upload + Parser** — Accept 23andMe TXT, AncestryDNA TXT, MyHeritage CSV, VCF. Auto-detect format. Show file stats (total SNPs, data quality).
2. **SNP Database** — Pre-loaded 5,000-10,000 curated SNP associations from SNPedia/ClinVar covering: disease risk, allergies, nutrition, traits, drug metabolism.
3. **Visual Report Dashboard** — Category cards with icons (allergy icon, heart icon, sleep icon, etc.), color-coded risk bars (low/medium/high), badge system like Genomelink.
4. **Free Trait Reports** — 25-50 free traits (earwax type, caffeine metabolism, lactose tolerance, etc.) to hook users.
5. **Premium Health Reports** — Paid reports: disease risk panel, nutrition panel, pharmacogenomics, detox/methylation.
6. **User Accounts** — Email auth, save reports, re-upload for updated analysis.
7. **Privacy-First Messaging** — "Your DNA stays on your device" (if client-side) or "We encrypt and never share."

### What We DON'T Build (MVP)
- ❌ Cross-platform DNA relative matching (requires massive user base + complex algorithms)
- ❌ YourRoots-style ancestry map or GEDCOM tree
- ❌ AI ancestor finder/deep research (Genomelink's AI feature)
- ❌ Custom DNA test kits (SelfDecode's model — too capital intensive)
- ❌ Real medical diagnoses (legal minefield)
- ❌ Mobile apps (start with responsive web)

---

## 4. Complete Project Infra Design

### Architecture Overview
```
┌──────────────────────────────────────────────────────────────────┐
│                      Frontend (Vercel)                           │
│                     Next.js 14 App Router                        │
│          Tailwind CSS + Shadcn/ui + Recharts for graphs          │
│         Client-side SNP parser option (browser WASM)             │
└──────────────────────┬─────────────────────────────────────────┘
                       │ REST API
┌──────────────────────▼─────────────────────────────────────────┐
│                   Backend (Railway/Fly.io)                       │
│                  FastAPI (Python 3.12+)                          │
│         SNP matching engine — PostgreSQL + pgvector              │
│         Async Celery + Redis for large file processing           │
└──────────────────────┬─────────────────────────────────────────┘
                       │
┌──────────────────────▼─────────────────────────────────────────┐
│                      Database (Supabase)                         │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────────────┐     │
│  │  Users/Auth  │  │  SNP Archive  │  │  Report Templates    │     │
│  │  (Supabase)  │  │  (Postgres)  │  │  (Postgres)          │     │
│  └─────────────┘  └──────────────┘  └─────────────────────┘     │
└──────────────────────────────────────────────────────────────────┘
                       │
┌──────────────────────▼─────────────────────────────────────────┐
│                 External Data Sources (Batch)                    │
│  ┌───────────┐ ┌─────────┐ ┌────────────┐ ┌───────────────┐    │
│  │  SNPedia   │ │ ClinVar  │ │ GWAS Catalog│ │  PharmGKB      │    │
│  │  (scrape)  │ │ (FTP)   │ │ (API)      │ │  (download)    │    │
│  └───────────┘ └─────────┘ └────────────┘ └───────────────┘    │
└──────────────────────────────────────────────────────────────────┘
                       │
┌──────────────────────▼─────────────────────────────────────────┐
│              Payment (Stripe)                                    │
│         One-time $X per report / Bundles / Subscription          │
└──────────────────────────────────────────────────────────────────┘
```

### Product Requirements Document (PRD)

#### User Personas

| Persona | Description | Needs |
|---|---|---|
| **DNA Explorer** | Took 23andMe for ancestry, wants more | Free traits, fun facts, shareable badges |
| **Health Optimizer** | Has health issues, wants root cause | Deep disease risk, supplement recs, PGx |
| **Biohacker** | Already tracking everything | Nutrition pathways, methylation, raw data export |

#### User Stories

| ID | Story | Priority | Effort |
|---|---|---|---|
| US-01 | As a user, I want to upload my raw DNA file so I can get analyzed | P0 | 3 days |
| US-02 | As a user, I want to see a visual dashboard of my traits with icons | P0 | 5 days |
| US-03 | As a user, I want to see my allergy risks with visual indicators | P1 | 3 days |
| US-04 | As a user, I want to unlock paid reports | P1 | 2 days |
| US-05 | As a user, I want my data deleted permanently if I choose | P1 | 1 day |
| US-06 | As a user, I want to share a trait card on social media | P2 | 2 days |

### Competitive Landscape Matrix

| Feature | Your MVP | Genomelink | Promethease | SelfDecode | Genetic Genie |
|---|---|---|---|---|---|
| Free traits | ✅ (25-50) | ✅ (100+) | ❌ | ❌ | ❌ |
| Disease risk | ✅ (premium) | ✅ | ✅ (deep) | ✅ | ✅ |
| Visual icons/badges | ✅ | ✅ | ❌ | 🟡 (partial) | ❌ |
| Client-side processing | 🟡 (MVP: server) | ❌ | ❌ | ❌ | ❌ |
| Nutrition reports | ✅ (premium) | ✅ | ❌ | ✅ | ✅ |
| Pharmacogenomics | ✅ (premium) | ❌ | 🟡 | ✅ | ❌ |
| DNA relative matching | ❌ | ✅ | ❌ | ❌ | ❌ |
| Price | Free + $X | Free + $15-50 | $12-16 | $99-499/yr | Free/$10 |
| Privacy | Strong | ISO 27001 | Weak (MyHeritage) | Moderate | Deleted 24h |

### Directory Structure
```
dna-analyzer/
├── frontend/
│   ├── app/
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   ├── register/
│   │   │   └── upload/
│   │   ├── dashboard/
│   │   │   ├── page.tsx              # Main dashboard with category cards
│   │   │   ├── traits/
│   │   │   ├── health-risk/
│   │   │   ├── nutrition/
│   │   │   ├── allergies/
│   │   │   ├── pharmacogenomics/
│   │   │   └── raw-data/
│   │   └── api/
│   │       ├── auth/                 # NextAuth or Supabase auth
│   │       ├── upload/
│   │       ├── analyze/
│   │       └── reports/
│   ├── components/
│   │   ├── ui/                       # Shadcn components
│   │   ├── TraitCard.tsx            # Genomelink-style badge card
│   │   ├── RiskMeter.tsx            # Color-coded risk indicator
│   │   ├── CategoryGrid.tsx         # Category icon grid
│   │   ├── FileUploader.tsx         # Drag-drop with format detection
│   │   └── ReportPDF.tsx            # PDF generation
│   ├── lib/
│   │   ├── snp-parser.ts            # Client-side parser (optional)
│   │   └── api.ts
│   ├── public/
│   │   └── icons/                    # Category/trait SVG icons
│   ├── tailwind.config.ts
│   ├── package.json
│   └── README.md
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── db/
│   │   │   ├── models.py
│   │   │   └── migrations/
│   │   ├── routers/
│   │   │   ├── auth.py
│   │   │   ├── upload.py
│   │   │   ├── analyze.py
│   │   │   └── reports.py
│   │   ├── services/
│   │   │   ├── parser_service.py     # File format detection + parsing
│   │   │   ├── snp_matcher.py        # Core matching engine
│   │   │   ├── risk_calculator.py    # Risk scoring logic
│   │   │   ├── report_generator.py   # PDF/HTML report builder
│   │   │   └── data_importer.py      # Import SNPedia/ClinVar data
│   │   ├── tasks/
│   │   │   └── analyze_task.py       # Celery async analysis
│   │   └── utils/
│   │       └── file_utils.py
│   ├── scripts/
│   │   ├── import_snpedia.py         # One-time SNPedia data import
│   │   ├── import_clinvar.py         # ClinVar variant import
│   │   └── import_gwas.py            # GWAS catalog import
│   ├── tests/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── celery_worker.py
│
├── data/
│   ├── snpedia_dump/                 # Local copy of SNPedia
│   └── clinvar_archive/              # Monthly ClinVar downloads
│
├── docker-compose.yml
├── .env.example
└── README.md
```

### Database Schema (PostgreSQL)

```sql
-- Users (Supabase Auth handles this, plus custom fields)
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    display_name TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    data_consent BOOLEAN DEFAULT FALSE,
    data_deleted_at TIMESTAMPTZ
);

-- Raw DNA file upload metadata
CREATE TABLE dna_uploads (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    provider TEXT NOT NULL, -- '23andme', 'ancestry', 'myheritage', 'ftdna', 'vcf'
    file_hash TEXT NOT NULL, -- SHA256 for dedup
    file_size_bytes INTEGER,
    snp_count INTEGER,      -- Total SNPs in file
    matched_snps INTEGER,   -- SNPs matched in our DB
    status TEXT DEFAULT 'pending', -- pending, processing, completed, failed
    created_at TIMESTAMPTZ DEFAULT NOW(),
    processed_at TIMESTAMPTZ,
    UNIQUE(user_id, file_hash)
);

-- Master SNP variant database (pre-loaded from SNPedia/ClinVar)
CREATE TABLE snp_variants (
    id SERIAL PRIMARY KEY,
    rsid TEXT NOT NULL UNIQUE,          -- rs1801133
    gene TEXT,                          -- MTHFR
    chromosome TEXT,
    position INTEGER,
    ref_allele TEXT,
    alt_allele TEXT,
    significance TEXT,                  -- pathogenic, risk_factor, protective, benign
    risk_allele TEXT,                   -- The allele that causes the risk
    magnitude INTEGER DEFAULT 0,        -- 0-10 importance scale (like SNPedia)
    reputation TEXT,                    -- 'Good', 'Bad', 'Unknown'
    summary TEXT,                       -- Human-readable description
    category TEXT NOT NULL,             -- disease, nutrition, allergy, trait, drug, detox
    subcategory TEXT,                   -- methylation, cardiovascular, etc.
    citations JSONB,                    -- Array of PMIDs or URLs
    source TEXT DEFAULT 'snpedia',      -- snpedia, clinvar, gwas, manual
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Polygenic scoring models (multiple SNPs = one condition)
CREATE TABLE polygenic_models (
    id SERIAL PRIMARY KEY,
    condition_name TEXT NOT NULL,       -- "Type 2 Diabetes"
    category TEXT NOT NULL,
    description TEXT,
    rsids TEXT[] NOT NULL,              -- Array of relevant rsIDs
    scoring_logic JSONB,               -- Weight per SNP, formula
    source TEXT,
    citation_url TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- User analysis results (cached per upload)
CREATE TABLE analysis_results (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    dna_upload_id UUID NOT NULL REFERENCES dna_uploads(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    category TEXT NOT NULL,             -- disease, nutrition, allergy, etc.
    subcategory TEXT,
    rsid TEXT NOT NULL REFERENCES snp_variants(rsid),
    user_genotype TEXT NOT NULL,        -- AA, AG, GG
    risk_allele TEXT,
    has_risk BOOLEAN,                   -- TRUE if user has risk allele
    magnitude INTEGER,
    summary TEXT,
    citations JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Report templates (define what reports are available)
CREATE TABLE report_templates (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,                 -- "Allergy Panel", "Nutrition Report"
    description TEXT,
    category TEXT NOT NULL,
    is_free BOOLEAN DEFAULT FALSE,
    price_cents INTEGER DEFAULT 0,      -- Price in cents ($0 = free)
    icon_url TEXT,
    sort_order INTEGER DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- User purchased reports
CREATE TABLE user_reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_profiles(id) ON DELETE CASCADE,
    report_template_id INTEGER REFERENCES report_templates(id),
    dna_upload_id UUID REFERENCES dna_uploads(id),
    stripe_payment_id TEXT,
    is_paid BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    expires_at TIMESTAMPTZ              -- NULL = lifetime access
);

-- Activity log
CREATE TABLE activity_log (
    id BIGSERIAL PRIMARY KEY,
    user_id UUID REFERENCES user_profiles(id) ON DELETE SET NULL,
    action TEXT NOT NULL,               -- upload, analyze, purchase, delete_data, share
    metadata JSONB,
    ip_address INET,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_snp_variants_rsid ON snp_variants(rsid);
CREATE INDEX idx_snp_variants_category ON snp_variants(category);
CREATE INDEX idx_analysis_results_user ON analysis_results(user_id);
CREATE INDEX idx_analysis_results_upload ON analysis_results(dna_upload_id);
CREATE INDEX idx_activity_log_user ON activity_log(user_id);
```

### API Endpoints

```
# Auth
POST   /api/auth/register              # Email + password
POST   /api/auth/login                 # JWT token
POST   /api/auth/reset-password
GET    /api/auth/me                    # Current user profile

# File Management
POST   /api/upload                     # Upload raw DNA file (multipart)
GET    /api/uploads                    # List user's uploads
GET    /api/uploads/:id                # Upload details
DELETE /api/uploads/:id                # Delete upload + associated analysis
GET    /api/uploads/:id/stats          # File stats (snp count, quality)

# Analysis
POST   /api/analyze/:upload_id         # Start analysis job
GET    /api/analyze/:upload_id/status  # Poll analysis status
GET    /api/analyze/:upload_id/results # Get all analysis results

# Reports
GET    /api/reports                    # List available report templates
GET    /api/reports/free               # Free traits for non-logged-in users
POST   /api/reports/:id/purchase       # Purchase report (Stripe)
GET    /api/reports/:id/download       # Download PDF report
GET    /api/reports/my                 # User's purchased reports

# Categories
GET    /api/categories                 # List all categories with icons
GET    /api/categories/:slug/traits    # Traits in a category
GET    /api/traits/:rsid               # Single trait detail

# User Data Management
DELETE /api/user/data                  # Delete all user data (GDPR)
GET    /api/user/export                # Export all data (JSON)
POST   /api/user/consent               # Update data consent preferences

# Admin
GET    /api/admin/stats                # Usage statistics
POST   /api/admin/import/snpedia       # Trigger SNPedia re-import
POST   /api/admin/import/clinvar       # Trigger ClinVar update
```

### AI Agent Design

**Agent 1: SNP Curator (Data Import Agent)**
```
Input: SNPedia page / ClinVar record dump
Steps:
  1. Parse rsID, gene, alleles, summary text
  2. Categorize (disease/nutrition/allergy/trait/drug/detox)
  3. Assign magnitude score (0-10) and reputation (good/bad/unknown)
  4. Extract PMID citations and GWAS links
  5. Validate against known allele frequencies (gnomAD)
  6. Flag ambiguous or contradictory findings for human review
Output: Structured snp_variants row ready for DB insert
```

**Agent 2: Report Writer (Content Agent)**
```
Input: User's matched SNP variants for a category (e.g., allergy: rs7775228 C allele)
Steps:
  1. Group matched variants by subcategory (food allergy, pet allergy, etc.)
  2. Check if user has 0, 1, or 2 risk alleles
  3. Generate human-readable explanation:
     - What the gene does
     - How the variant affects function
     - What it means for the user
     - Actionable recommendations (diet, supplements, lifestyle)
  4. Assign risk level (green/yellow/red) based on magnitude + genotype
  5. Compile section for the report
Output: JSON with risk level, explanation, recommendations, citations
```

**Agent 3: SEO Content Writer**
```
Input: Category name, trait name, keywords
Steps:
  1. Research latest genetic studies for the trait (PubMed search)
  2. Write blog-style article: "Is [trait] genetic? What your DNA says about [topic]"
  3. Include the science: GWAS studies, specific SNPs, heritability stats
  4. Optimize for keywords: "DNA [trait] test", "raw data [trait] analysis"
  5. Internal link to relevant report/trait page
Output: SEO-optimized blog post with citation links
```

### SEO & Content Strategy

#### Keyword Clusters

| Cluster | Keywords | Search Intent | Volume Est. |
|---|---|---|---|
| **Raw DNA Analysis** | "upload 23andme raw data", "analyze my dna", "raw dna analysis free" | Transactional | 40K/mo |
| **Health Genetics** | "mthfr gene", "methylation genes", "dna health test", "genetic disease risk" | Informational | 60K/mo |
| **Trait Genetics** | "caffeine metabolism gene", "lactose intolerance genetics", "alcohol flush gene" | Informational | 30K/mo |
| **Allergy Genetics** | "dog allergy gene", "gluten sensitivity genetics", "food allergy dna test" | Informational | 15K/mo |
| **Nutrition Genetics** | "nutrigenomics", "dna diet plan", "genetic based nutrition" | Commercial | 25K/mo |

#### Content Funnel

- **TOFU:** "What your 23andMe data can tell you about your health" (blog), free trait preview
- **MOFU:** "5 hidden health risks in your DNA you should check" (email lead magnet), free allergy panel
- **BOFU:** Premium health panel ($37), pharmacogenomics report ($49), annual subscription ($99/yr)

#### 30-Day Content Calendar
```
Week 1: "How to download your 23andMe raw data" + free traits page
Week 2: "Top 10 health risks hidden in your DNA" (listicle)
Week 3: "MTHFR gene explained: what your rs1801133 means"
Week 4: "Is gluten sensitivity genetic? DNA test guide"
+ Ongoing: Weekly new trait additions (25 free traits → 50 → 100)
```

### Growth & Viral Loops

| Loop | Mechanics | Expected K-Factor |
|---|---|---|
| **Social Trait Cards** | User gets trait result → shares image card to IG/Twitter → friends upload their DNA | 0.15 |
| **Referral** | "Unlock a free premium report for each friend who uploads their DNA" | 0.25 |
| **Free Trait Preview** | Non-logged-in users can see 5 free traits → email capture → full report | 10-15% conversion |
| **Content SEO** | Blog posts rank for genetic queries → organic uploads | 0.05 (but compounding) |

### Revenue Model & Unit Economics

#### Pricing Tiers

| Tier | Price | What You Get | Target |
|---|---|---|---|
| **Free** | $0 | 25 free traits, basic dashboard | Hook users, collect emails |
| **Traits Plus** | $19 one-time | 100+ traits (all free tier + more) | Casual curious |
| **Health Panel** | $37 one-time | Disease risk + nutrition + allergy + detox | Health-focused users |
| **Pharmacogenomics** | $49 one-time | Drug response, 50+ medications | Medicated users |
| **All Access** | $99/yr | Everything + new reports + updates | Serious optimizers |

#### Unit Economics

| Metric | Value |
|---|---|
| TAM | 50M DNA-tested users globally |
| SAM | 10M interested in third-party analysis |
| Free → Paid conversion | 5% (cautious) |
| Monthly churn (subscribers) | 5% |
| Avg revenue per paid user | $35 one-time / $99/yr |
| CAC (SEO + organic) | $5-10/user |
| LTV (one-time buyers) | $35 |
| LTV (annual subscribers) | $99 × 0.80 GM × 20mo = $1,584 |

#### 12-Month Projection

| Month | Free Users | Paid One-Time | Annual Subs | MRR |
|---|---|---|---|---|
| 1 | 500 | 25 | 5 | $1,320 |
| 3 | 3,000 | 150 | 30 | $8,220 |
| 6 | 10,000 | 500 | 100 | $27,400 |
| 12 | 50,000 | 2,500 | 500 | $137,000 |

### Tech Stack Summary

| Layer | Choice | Reason |
|---|---|---|
| **Frontend** | Next.js 14 + Tailwind + Shadcn/ui | Fast dev, server components, Vercel deploy |
| **Icons/Visuals** | Lucide React + custom SVG icons | Genomelink-style badge system |
| **Charts** | Recharts | Risk meters, trait distributions |
| **Backend** | FastAPI (Python) | Best bioinformatics libs, async support |
| **SNP Parsing** | `pandas` + custom parser Python libs | Handles 600K SNP files in <2s |
| **Database** | Supabase (PostgreSQL) | Managed, auth, RLS row-level security |
| **Task Queue** | Celery + Redis | Async analysis for large files |
| **Payments** | Stripe | Standard, supports subscriptions + one-time |
| **Hosting** | Vercel (frontend) + Railway (backend) | Simple, scalable, cost-effective |
| **Auth** | Supabase Auth + JWT | Built-in, social login options |
| **File Storage** | Supabase Storage / S3 | Raw file storage (encrypted) |
| **Email** | Resend | Transactional + marketing emails |
| **Error Tracking** | Sentry | Error + performance monitoring |
| **Data Import** | Python scripts (bs4, requests) | Periodic SNPedia/ClinVar updates |

### Estimated MVP Build Time

| Phase | Time | Deliverable |
|---|---|---|
| Setup + Auth | 3 days | Next.js + FastAPI scaffold, Supabase, login/register |
| SNP Database Import | 5 days | Import 5K curated variants from SNPedia + ClinVar |
| File Upload + Parser | 4 days | Drag-drop upload, server-side parsing (handle all formats) |
| SNP Matching Engine | 5 days | Core logic: match user SNPs → variant DB → risk calculation |
| Visual Report Dashboard | 7 days | Category grid, trait cards, risk meters, Genomelink-style UI |
| Free Traits (25) | 3 days | First batch of fun/shareable traits |
| Premium Reports | 5 days | Health panel, allergy panel, PDF download |
| Stripe Integration | 2 days | Payment flow, report unlock |
| Admin Panel | 2 days | Usage stats, manual data import trigger |
| Privacy/Legal | 2 days | Privacy policy, GDPR tools, data deletion |
| Polish + Deploy | 3 days | Error handling, performance, launch |
| **Total** | **~41 days** | **Working MVP** |

### Cost Estimates (Monthly)

| Service | Cost | Notes |
|---|---|---|
| Vercel Pro | $20 | Frontend hosting |
| Railway Starter | $5 | Backend API + Celery worker |
| Supabase Pro | $25 | DB + Auth + Storage |
| Redis (Upstash) | $0 (free tier) | Celery broker |
| Stripe | 2.9% + $0.30 | Per transaction |
| Sentry Free | $0 | Error tracking |
| Resend Free | $0 (100 emails/day) | Transactional emails |
| Domains + Email | $15 | DNS, custom domain |
| **Total Base** | **~$65/mo** | Scales with traffic |

### Future Phases

**Phase 2 — Client-Side Processing + Deeper Reports (Month 2-3)**
- WASM-based client-side SNP parsing (zero-upload privacy option)
- Bump to 15K+ curated SNP variants
- Polygenic risk scores for top 10 conditions
- Pharmacogenomics panel (50+ drugs)
- Shareable trait cards for social media (viral loop)
- Email sequence: "New trait available based on your DNA"

**Phase 3 — Subscription Model + Content Engine (Month 4-5)**
- Monthly subscription ($99/yr or $14.99/mo)
- Auto-email new traits as they're added
- Weekly blog posts ranking for genetic keywords
- Supplement recommendation engine (partner affiliate program)
- Doctor/coach referral system (B2B angle)
- DNA file comparison tool (compare with family members)

**Phase 4 — Advanced Features (Month 6+)**
- Methylation pathway visualization (like StrateGene's color-coded charts)
- AI-powered report writer (personalized, not templated)
- Health dashboard trends (compare across time/re-uploads)
- B2B white-label for doctors/naturopaths
- Mobile apps (React Native)
- Cross-platform relative matching (requires critical mass)

---

*This doc serves as the source of truth for the DNA Raw Data Analyzer project.*
*Last updated: 2026-06-01*
