# DocuSeal Analysis & Build Plan

> **Date:** 2026-05-07
> **Source:** https://www.docuseal.com / https://github.com/docusealco/docuseal
> **Status:** Competitor analysis → MVP build scope

---

## 1. What DocuSeal Is

Open source DocuSign alternative. Create, fill, and sign digital documents. #1 open source eSignature platform on GitHub (14.2K stars).

**Stack:** Ruby on Rails + Vue.js + TailwindCSS + PostgreSQL/MySQL/SQLite

**Team:** 5 core contributors (DocuSeal LLC). Founded 2023.

### Their 3-Step Flow
1. **Upload document** — PDF, DOCX, XLSX, JPEG, PNG
2. **Add fields** — WYSIWYG form builder with 12 field types (signature, date, checkbox, file, etc.)
3. **Send to signers** — Auto-emails via SMTP, step-by-step signing, legally-binding eSignature

### Key Features
- PDF form fields builder (WYSIWYG)
- 12 field types (Signature, Date, File, Checkbox, Cells, etc.)
- Multiple submitters per document
- Automated emails via SMTP
- File storage: local disk, AWS S3, Google Storage, Azure Cloud
- Automatic PDF eSignature (digital certificate)
- PDF signature verification
- User management & roles
- Mobile-optimized signing UI
- 7 UI languages, 14 signing languages
- REST API + Webhooks
- Embedded signing form (React, Vue, Angular, JS)
- Embedded form builder (React, Vue, Angular, JS)
- SSO/SAML (Pro)
- Bulk send with CSV/XLSX (Pro)
- Conditional fields & formulas (Pro)
- SMS identity verification (Pro)
- White-label (Pro)

### Pricing

| Tier | Price | Best For |
|---|---|---|
| OSS (Self-hosted) | Free | Individuals, devs who self-host |
| Pro Cloud | $20/mo ($200/yr) per user | Small teams, hosted |
| Pro On-Premises | $240/yr per user | Enterprise self-hosted |
| API/Embedding | $0.20 per document signed | SaaS integrations |

### Target Market
SMBs replacing DocuSign/PandaDoc, developers needing embedded signing, enterprises needing self-hosted compliance.

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|---|---|---|
| PDF field detection & rendering | 🔴 Hard | Parsing PDF field positions, radio groups, checkboxes programmatically — PDF spec is a nightmare |
| Legally-binding eSignature | 🔴 Hard | ESIGN, UETA, eIDAS compliance — digital certificate generation, audit trails, tamper-evident seals |
| WYSIWYG form builder | 🟡 Medium | Drag-drop field placement, resize, snapping, preview — doable with libraries but polish takes time |
| SMTP email automation | 🟢 Easy | Standard email sending |
| File storage (S3/GCS/Azure) | 🟢 Easy | Standard cloud storage |
| User management + roles | 🟢 Easy | Standard auth |
| Multi-language | 🟡 Medium | i18n set up, translations |
| Embedded signing (iframes) | 🟡 Medium | Cross-origin communication, security |
| API + Webhooks | 🟡 Medium | Rate limiting, webhook retries, event system |
| SSO/SAML | 🟡 Medium | IdP integration, metadata exchange |
| PDF signature verification | 🔴 Hard | PKI, certificate chain validation, LTV (long-term validation) |
| **14K GitHub stars** | 🔴 Brand | Open source community trust — not replicable quickly |

### The Moats
- **PDF internals expertise** — Parsing, annotation, digital signatures at the PDF spec level is deep specialist knowledge
- **eSignature compliance** — ESIGN/UETA/eIDAS requires cryptographic audit trails, qualified certificates (QES in EU), long-term validation. Legal liability makes this a trust moat
- **Open source community** — 14K stars, 1.2K forks, active contributions. Self-hosted trust that closed-source can't match
- **API ecosystem** — Zapier, Make.com, n8n integrations + embedding SDKs in 4 frameworks

---

## 3. MVP That CAN Be Built

### MVP Features
1. Upload PDF & display in browser
2. WYSIWYG field placement (signature, date, text, checkbox)
3. Assign fields to signers
4. Send signing link via email
5. Step-by-step signing UI (mobile-friendly)
6. Generate signed PDF with embedded signature stamp
7. Download signed document
8. Simple audit trail (who signed when, IP, timestamp)
9. User login + document management
10. SQLite for development / PostgreSQL for production

### What We DON'T Build (MVP)
- Digital certificate PKI (use a simpler: image-based signature + hash audit trail)
- SSO/SAML
- SMS verification
- API + webhooks
- Embedded form builder
- Conditional fields/formulas
- Bulk send CSV
- White-label/logo
- Automated reminders

---

## 4. Complete Project Infra Design

### Product Requirements Document (PRD)

**User Personas:**
- **Small business owner** — needs to send contracts for signature without DocuSign pricing
- **Freelancer** — independent contractor agreements, proposals
- **Developer** — wants to embed signing into their own app

**User Stories (MVP):**
```
As a user, I want to upload a PDF so I can prepare it for signing.
As a user, I want to drag fields onto the PDF so I can mark where signers need to fill.
As a signer, I want to click a link and sign on my phone so I don't need to print/scan.
As a sender, I want to download the signed PDF so I have a completed document.
```

**RICE Scoring:**

| Feature | Reach | Impact | Conf | Effort | Score | Priority |
|---|---|---|---|---|---|---|
| PDF upload + display | 10 | 10 | 100% | 2w | 50 | P0 |
| Field placement UI | 10 | 10 | 80% | 4w | 20 | P0 |
| Signing flow | 10 | 10 | 90% | 3w | 30 | P0 |
| Signed PDF generation | 10 | 10 | 70% | 3w | 23 | P0 |
| Email notifications | 8 | 8 | 95% | 1w | 61 | P0 |
| Audit trail | 7 | 8 | 80% | 1w | 45 | P1 |
| Multi-signer | 8 | 9 | 80% | 2w | 29 | P1 |
| API | 7 | 9 | 70% | 4w | 11 | P2 |

### Competitive Landscape

| Feature | DocuSeal | DocuSign | PandaDoc | HelloSign | Us (MVP) |
|---|---|---|---|---|---|
| PDF upload | ✅ | ✅ | ✅ | ✅ | ✅ |
| WYSIWYG fields | ✅ | ✅ | ✅ | ✅ | ✅ |
| eSignature legal binding | ✅ | ✅ | ✅ | ✅ | ⚠️ Basic |
| Mobile signing | ✅ | ✅ | ✅ | ✅ | ✅ |
| API | ✅ | ✅ | ✅ | ✅ | ❌ |
| Self-hosted | ✅ | ❌ | ❌ | ❌ | ✅ |
| Open source | ✅ | ❌ | ❌ | ❌ | ✅ |
| SSO/SAML | Pro only | ✅ | ✅ | ✅ | ❌ |
| White-label | Pro only | ❌ | ❌ | ❌ | ❌ |
| Price | Free-$20 | $30+/mo | $35+/mo | $15+/mo | Free |

**Positioning:** Self-hosted, open source, affordable — same as DocuSeal but lighter.

### Architecture Overview
```
┌──────────────────────────────────────────────────────┐
│              Frontend (Vue.js / React)                 │
│   - Dashboard (documents list, templates)              │
│   - PDF viewer + field placement (WYSIWYG)             │
│   - Signing UI (step-by-step mobile-first)             │
└──────────────────────┬───────────────────────────────┘
                       │ REST API
┌──────────────────────▼───────────────────────────────┐
│              Backend (Rails / FastAPI)                 │
│   - Auth (Devise or JWT)                              │
│   - PDF processing (LibreOffice / pdf-lib / pdftk)    │
│   - Field placement logic                              │
│   - Signature stamp + output PDF                       │
│   - Email notifications (SMTP)                         │
└──────────────────────┬───────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────┐
│           Database (PostgreSQL) + Storage             │
│   - Documents, templates, signers, audit log          │
│   - S3-compatible storage (MinIO for self-hosted)     │
└──────────────────────────────────────────────────────┘
```

### Directory Structure
```
docuseal-clone/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── pdf-viewer/         # PDF display + field canvas
│   │   │   ├── field-tools/        # Field type selector, properties
│   │   │   ├── signing-flow/       # Step-by-step signer UI
│   │   │   └── dashboard/          # Document list
│   │   ├── pages/
│   │   ├── services/               # API client
│   │   └── utils/
│   └── package.json
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── db/
│   │   │   └── models.py
│   │   ├── routers/
│   │   │   ├── auth.py
│   │   │   ├── documents.py
│   │   │   ├── templates.py
│   │   │   ├── submissions.py
│   │   │   └── webhooks.py
│   │   ├── services/
│   │   │   ├── pdf_service.py      # PDF parsing, stamping
│   │   │   ├── signing_service.py  # Signature logic + audit
│   │   │   ├── email_service.py    # SMTP notifications
│   │   │   └── storage_service.py  # File storage abstraction
│   │   └── tasks/
│   │       └── workers.py
│   ├── requirements.txt
│   └── Dockerfile
│
├── docker-compose.yml
└── .env.example
```

### Database Schema (PostgreSQL)

```sql
-- Users / Accounts
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    name TEXT NOT NULL,
    password_hash TEXT NOT NULL,
    role TEXT DEFAULT 'member',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Documents (original uploads + metadata)
CREATE TABLE documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    title TEXT NOT NULL,
    original_filename TEXT NOT NULL,
    file_path TEXT NOT NULL,          -- path in storage
    file_size BIGINT,
    page_count INT DEFAULT 0,
    status TEXT DEFAULT 'draft',      -- draft, sent, completed, expired
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Document Fields (per-page field placements)
CREATE TABLE document_fields (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id UUID REFERENCES documents(id) ON DELETE CASCADE,
    page_number INT NOT NULL,
    field_type TEXT NOT NULL,          -- signature, date, text, checkbox, file
    x REAL NOT NULL,                   -- position (percentage-based for responsive)
    y REAL NOT NULL,
    width REAL NOT NULL,
    height REAL NOT NULL,
    required BOOLEAN DEFAULT true,
    signer_id UUID REFERENCES signers(id),
    options JSONB,                     -- placeholder, formatting, validation
    sort_order INT DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Signers (people who need to sign)
CREATE TABLE signers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id UUID REFERENCES documents(id) ON DELETE CASCADE,
    email TEXT NOT NULL,
    name TEXT NOT NULL,
    status TEXT DEFAULT 'pending',     -- pending, viewed, completed
    signing_order INT DEFAULT 1,
    token TEXT UNIQUE NOT NULL,        -- unique signing link token
    signed_at TIMESTAMPTZ,
    completed_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Audit Trail
CREATE TABLE audit_log (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id UUID REFERENCES documents(id),
    signer_id UUID REFERENCES signers(id),
    action TEXT NOT NULL,              -- viewed, field_filled, signed, completed, emailed
    ip_address TEXT,
    user_agent TEXT,
    metadata JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Signed Documents (outputs)
CREATE TABLE signed_documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    document_id UUID REFERENCES documents(id),
    file_path TEXT NOT NULL,
    signature_hash TEXT,               -- SHA256 of signed PDF
    certificate_data JSONB,            -- signing cert metadata
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Templates (reusable forms)
CREATE TABLE templates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    name TEXT NOT NULL,
    document_id UUID REFERENCES documents(id),  -- source document
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### API Endpoints

```
POST   /api/auth/register
POST   /api/auth/login
GET    /api/auth/me

GET    /api/documents              # List user's documents
POST   /api/documents              # Upload PDF
GET    /api/documents/:id          # Get document detail
DELETE /api/documents/:id

GET    /api/documents/:id/fields   # Get field placements
POST   /api/documents/:id/fields   # Save field placements
PUT    /api/fields/:id             # Update single field

POST   /api/documents/:id/signers  # Add signer
GET    /api/documents/:id/signers  # List signers
DELETE /api/signers/:id

POST   /api/documents/:id/send     # Send signing request emails

GET    /sign/:token                # Signer landing page (public)
POST   /sign/:token/submit         # Signer submits complete
GET    /api/sign/:token/fields     # Get fields for this signer

GET    /api/documents/:id/signed   # Download signed PDF
GET    /api/documents/:id/audit    # View audit trail
```

### AI Agent Design

Not applicable for MVP. DocuSeal doesn't use AI — it's a document signing tool.

### SEO & Content Strategy

**Keyword Clusters:**
| Cluster | Head Keyword | Volume |
|---|---|---|
| eSignature | "open source DocuSign alternative" | Medium |
| Document signing | "free document signing" | High |
| Self-hosted | "self-hosted document signing" | Low |
| PDF forms | "fillable PDF form builder" | Medium |

**Content Funnel:**
- TOFU: "DocuSign alternatives in 2026", "Why self-host your document signing"
- MOFU: "DocuSeal vs DocuSign vs PandaDoc", "How to legally eSign PDFs"
- BOFU: "Deploy DocuSeal in 5 minutes with Docker"

**Distribution:** GitHub (14K stars is the main channel), Docker Hub, Hacker News, Reddit r/selfhosted

### Growth & Viral Loops

**Primary loop:** Open source → GitHub stars → Docker pulls → users → contributors → more features → more stars

**Acquisition channels:**
| Channel | Cost | Impact |
|---|---|---|
| GitHub (open source) | $0 | Very High |
| Docker Hub | $0 | High |
| Hacker News launch | $0 | Very High |
| Reddit (r/selfhosted, r/devops) | $0 | Medium |
| Product Hunt | $0 | High |
| Google "DocuSign alternatives" | SEO | Medium |

**Referral:** No built-in referral — growth is purely open source virality + SEO.

### Revenue Model & Unit Economics

**Tiers:**
| Tier | Price | Revenue Driver |
|---|---|---|
| OSS (self-hosted) | Free | Community growth, brand awareness |
| Pro Cloud | $20/mo | Hosted convenience |
| Pro On-Premises | $240/yr/user | Enterprise compliance |
| API/Embedding | $0.20/document | Volume-based, high margin |

**DocuSeal's actual model:** Most revenue from Pro On-Premises (enterprise self-hosted) + API/Embedding usage fees. Cloud is lower volume.

**CAC:** Very low — organic GitHub + SEO driven. Estimated blended CAC < $50.

### Tech Stack Summary

| Layer | Choice | Reason |
|---|---|---|
| Frontend | Vue.js + TailwindCSS | Proven with PDF-heavy apps, lightweight |
| PDF Viewer | PDF.js (Mozilla) | Best open source PDF rendering |
| Field Canvas | Fabric.js / Interact.js | Drag-drop positioning on PDF pages |
| Backend | Ruby on Rails | DocuSeal's actual stack — or FastAPI for Python devs |
| Database | PostgreSQL | Production, or SQLite for simple self-hosted |
| PDF Processing | pdf-lib (JS) + LibreOffice (server) | Field insertion, signature stamp, conversion |
| File Storage | Local disk or S3-compatible | S3 for cloud, disk for self-hosted |
| Email | SMTP (SendGrid / Mailgun / self-hosted) | Transactional signing emails |
| Auth | Devise (Rails) / JWT (FastAPI) | Standard auth with email/password |
| Container | Docker + docker-compose | Easy self-hosted deployment |
| Background Jobs | Sidekiq (Rails) / Celery (FastAPI) | Email sending, PDF processing |

### Estimated MVP Build Time

| Phase | Time | Deliverable |
|---|---|---|
| Auth + user management | 2 days | Signup, login, profile |
| PDF upload + display | 3 days | Upload, PDF.js viewer, pagination |
| WYSIWYG field placement | 10 days | Drag-drop fields, 5 types, resize, save positions |
| Signer management | 2 days | Add signers, token generation |
| Signing flow UI | 5 days | Step-by-step form, mobile-responsive |
| Signed PDF generation | 8 days | Stamp fields, embed signature, output PDF |
| Email notifications | 2 days | SMTP config, send signing link |
| Dashboard + document list | 3 days | CRUD, status, filtering |
| Audit trail | 2 days | Log actions, display timeline |
| **Total** | **~37 days** | **Working MVP** |

### Cost Estimates (Monthly)

| Service | Cost | Notes |
|---|---|---|
| VPS (self-hosted MVP) | $10-20 | DigitalOcean / Hetzner |
| SMTP (SendGrid free tier) | $0 | 100 emails/day free |
| S3-compatible storage | $0-5 | MinIO self-hosted or Backblaze B2 |
| Domain + SSL | $10/yr | |
| PostgreSQL (self-hosted) | $0 | Included in VPS |
| **Total** | **$10-25/mo** | |

### Future Phases

**Phase 2 — Growth Features**
- Email reminders / auto-reminders
- Template system (reusable forms)
- Webhook callbacks
- Multi-language (UI + signing)
- Image/file attachments per field
- Signed PDF verification page

**Phase 3 — Monetization**
- White-label / custom branding
- SSO / SAML (Google, Microsoft)
- SMS identity verification
- Stripe payment collection
- API keys + rate limiting
- Usage-based billing

**Phase 4 — Scale**
- Embedded signing (iframes + SDK for React, Vue, Angular)
- Bulk send from CSV/XLSX
- Conditional fields + formulas
- Advanced PDF certificate signatures (QES level)
- Real-time collaboration / in-person signing
- Mobile apps (iOS + Android)

---

*This doc serves as the source of truth for the DocuSeal clone project.*
*Last updated: 2026-05-07*
