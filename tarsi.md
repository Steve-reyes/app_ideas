# Tarsi Analysis & Build Plan

> **Date:** 2026-05-07
> **Source:** https://apps.apple.com/app/tarsi-budget-tracker/id6760278399
> **Developer:** Bryl Kezter Lim / PocketDevs PH (Daniel, Christian, John, Theodore, Paul — team of 6)
> **Status:** Competitor analysis → MVP build scope

---

## 1. What Tarsi Is

Offline-first personal budget tracker. #1 Finance app on App Store within hours of launch. Paid app ($3.49 Android / $6.99 iOS one-time).

### Key Features
- Expense & income logging (manual, voice, OCR receipt scan)
- Multi-account tracking (cash, bank, e-wallets, credit, investments)
- Category budgets with subcategories
- Savings goals with progress tracking
- Debt tracking + IOUs owed to you
- Cashflow forecasting (recurring bills, installments, subscriptions)
- Daily spending summary
- AI chat for financial insights (offline, local LLM?)
- Multi-profile support
- Offline-first — no account, no cloud, no tracking
- Backup/export to CSV/JSON
- Light & dark mode
- Investment tracking (US stocks + crypto with market data)

### Pricing
- **Android:** $3.49 one-time (Play Store)
- **iOS:** $6.99 – $7.99 one-time (App Store, varies by region)
- No subscriptions, no ads, no in-app tracking

### Target Audience
Privacy-conscious individuals, budgeting beginners, users in emerging markets (Philippines-based dev, strong PH community).

### Team
PocketDevs PH — 6 developers (Daniel, Christian, John, Theodore, Paul + Bryl). Solo indie effort that blew up.

---

## 2. What Makes It Hard

| Component | Difficulty | Why |
|---|---|---|
| Offline-first local storage | 🟢 Easy | SQLite/Hive/WatermelonDB |
| Expense/income CRUD | 🟢 Easy | Standard mobile pattern |
| Category budgets & goals | 🟢 Easy | Simple math + progress bars |
| Voice input for transactions | 🟡 Medium | On-device speech-to-text (Apple Speech/Android STT) |
| OCR receipt scanning | 🟡 Medium | ML Kit / Vision framework, need accuracy tuning |
| Cashflow forecasting | 🟡 Medium | Recurring logic, edge cases (variable bills, skipped months) |
| Local AI chat (offline) | 🔴 Hard | Running LLM on-device — CoreML / MLX / TensorFlow Lite, model size vs accuracy tradeoff |
| Investment market data | 🟡 Medium | Third-party API (Yahoo Finance, Alpha Vantage) — need internet but still offline-first for core |
| Multi-profile support | 🟡 Medium | Data isolation per profile, local encryption |
| Multi-currency support | 🟡 Medium | Exchange rate APIs, conversion logic |
| **#1 App Store ranking** | 🔴 Luck | Viral moment / ASO / timing — not replicable on command |

### The Moats
- **Local AI chat** — On-device LLM inference is non-trivial. Tarsi likely uses Apple's CoreML with a small model (e.g., Phi-2 or Llama 3.2 1B quantized)
- **Viral launch** — Hit #1 in hours on App Store via Threads post. Distribution moat, not tech
- **Privacy positioning** — No account, no cloud, no tracking. Strong trust signal, but easy to replicate
- **Offline-first everything** — Real engineering effort. Voice input + AI + full app functionality with zero internet

---

## 3. MVP That CAN Be Built

### MVP Features
1. Expense & income logging (manual, categories, accounts)
2. Multi-account management (cash, bank, e-wallet, credit)
3. Monthly category budgets with visual progress
4. Savings goals
5. Debt tracking + IOUs
6. Recurring transactions
7. Daily spending summary
8. Backup/export (JSON/CSV)
9. Offline-first (SQLite local storage)
10. Light/dark mode

### What We DON'T Build (MVP)
- Voice input (nice-to-have Phase 2)
- OCR receipt scanning (Phase 2)
- Local AI chat (Phase 3 — hardest feature)
- Investment tracking (Phase 3)
- Multi-profile (Phase 2)
- Cashflow forecasting with installments (Phase 2)

---

## 4. Complete Project Infra Design

### Architecture Overview
```
┌──────────────────────────────────────────────┐
│           Mobile App (Flutter / RN)           │
│  Screens: Home, Transactions, Budgets,        │
│           Goals, Debts, Settings              │
└──────────────┬───────────────────────────────┘
               │ Local (No backend needed)
               ▼
┌──────────────────────────────────────────────┐
│         Local Storage (SQLite/Hive)           │
│  - Accounts, Transactions, Categories,        │
│    Budgets, Goals, Debts                      │
│  - No cloud sync (privacy-first)              │
└──────────────────────────────────────────────┘
               │
               ▼ (Optional: internet features)
┌──────────────────────────────────────────────┐
│      External APIs (read-only, optional)      │
│  - Exchange rates (if multi-currency)         │
│  - Market data (Phase 3)                      │
└──────────────────────────────────────────────┘
```

### Platform Decision

| Factor | Flutter | React Native | Swift (iOS) | Kotlin (Android) |
|---|---|---|---|---|
| Offline SQLite | ✅ Good (sqflite) | ✅ Good | ✅ Core Data | ✅ Room |
| Voice (on-device) | ⚠️ Partial | ⚠️ Partial | ✅ Native STT | ✅ Native STT |
| Local AI inference | ⚠️ Dart™ Lite | ❌ No native | ✅ CoreML | ✅ ML Kit |
| Code reuse | 95% | 90% | 0% | 0% |
| Dev speed | Fastest | Fast | Moderate | Moderate |
| App size budget | Heavier | Lighter | Lightest | Lightest |

**Verdict:** Flutter for MVP (fastest to ship both platforms). If AI chat becomes core, native path may be better long-term.

### Directory Structure
```
tarsi-clone/
├── mobile/
│   ├── lib/
│   │   ├── app/
│   │   │   ├── main.dart
│   │   │   ├── routes.dart
│   │   │   └── theme.dart
│   │   ├── models/
│   │   │   ├── account.dart
│   │   │   ├── transaction.dart
│   │   │   ├── category.dart
│   │   │   ├── budget.dart
│   │   │   ├── goal.dart
│   │   │   └── debt.dart
│   │   ├── screens/
│   │   │   ├── home/
│   │   │   ├── transactions/
│   │   │   ├── budgets/
│   │   │   ├── goals/
│   │   │   ├── debts/
│   │   │   └── settings/
│   │   ├── services/
│   │   │   ├── database_service.dart
│   │   │   ├── export_service.dart
│   │   │   └── backup_service.dart
│   │   ├── providers/
│   │   │   ├── account_provider.dart
│   │   │   ├── transaction_provider.dart
│   │   │   └── budget_provider.dart
│   │   ├── widgets/
│   │   │   ├── transaction_tile.dart
│   │   │   ├── budget_progress.dart
│   │   │   └── account_card.dart
│   │   └── utils/
│   │       ├── currency_formatter.dart
│   │       └── date_utils.dart
│   ├── assets/
│   │   ├── images/
│   │   └── fonts/
│   ├── test/
│   ├── pubspec.yaml
│   └── analysis_options.yaml
├── docs/
│   └── architecture.md
├── .gitignore
└── README.md
```

### Database Schema (SQLite — Local Only)

No backend. All data stays on-device.

```sql
-- Accounts
CREATE TABLE accounts (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL, -- cash, bank, ewallet, credit, investment
    balance REAL DEFAULT 0.0,
    currency TEXT DEFAULT 'PHP',
    icon TEXT,
    sort_order INT DEFAULT 0,
    is_archived INT DEFAULT 0,
    created_at TEXT,
    updated_at TEXT
);

-- Transactions
CREATE TABLE transactions (
    id TEXT PRIMARY KEY,
    account_id TEXT REFERENCES accounts(id),
    type TEXT NOT NULL, -- expense, income, transfer
    amount REAL NOT NULL,
    category_id TEXT REFERENCES categories(id),
    description TEXT,
    date TEXT NOT NULL,
    is_recurring INT DEFAULT 0,
    recurring_frequency TEXT, -- daily, weekly, monthly, yearly
    attachment_path TEXT,
    created_at TEXT,
    updated_at TEXT
);

-- Categories
CREATE TABLE categories (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    icon TEXT,
    color TEXT,
    type TEXT NOT NULL, -- expense, income
    parent_id TEXT REFERENCES categories(id), -- subcategories
    sort_order INT DEFAULT 0
);

-- Budgets
CREATE TABLE budgets (
    id TEXT PRIMARY KEY,
    category_id TEXT REFERENCES categories(id),
    amount REAL NOT NULL,
    period TEXT NOT NULL, -- weekly, monthly
    spent REAL DEFAULT 0.0,
    created_at TEXT,
    updated_at TEXT
);

-- Goals
CREATE TABLE goals (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    target_amount REAL NOT NULL,
    current_amount REAL DEFAULT 0.0,
    deadline TEXT,
    account_id TEXT REFERENCES accounts(id),
    is_completed INT DEFAULT 0,
    created_at TEXT
);

-- Debts
CREATE TABLE debts (
    id TEXT PRIMARY KEY,
    type TEXT NOT NULL, -- owe, owed
    person_name TEXT,
    amount REAL NOT NULL,
    remaining REAL NOT NULL,
    description TEXT,
    due_date TEXT,
    is_paid INT DEFAULT 0,
    created_at TEXT
);

-- Recurring Transactions
CREATE TABLE recurring (
    id TEXT PRIMARY KEY,
    transaction_id TEXT REFERENCES transactions(id),
    frequency TEXT NOT NULL,
    interval_count INT DEFAULT 1,
    next_date TEXT NOT NULL,
    end_date TEXT,
    is_active INT DEFAULT 1
);
```

### API Endpoints

**None for MVP.** Tarsi clone is fully offline-first. Zero backend.

**Optional external APIs (Phase 2+):**
- Exchange rate API (free tier: exchangerate-api.com)
- Stock/crypto market data (Yahoo Finance, Alpha Vantage)
- OCR processing (Google ML Kit / Apple Vision — on-device)

### UI/UX Key Screens

| Screen | Description | Key Elements |
|---|---|---|
| Onboarding | First-launch intro | Feature carousel, privacy promise, optional welcome |
| Home Dashboard | Net worth, daily summary, quick actions | Balance card, income/expense today, recent transactions |
| Transactions | List + add | Search, filter by date/category, calendar view |
| Add Transaction | Quick entry form | Amount, category, account, description, date, receipt attach |
| Accounts | All accounts list | Balance per account, type icon, swipe to edit |
| Budgets | Category budgets | Progress bars, percentage, remaining, overspent alert |
| Goals | Savings goals | Target amount, progress bar, deadline, add contribution |
| Debts | Debt tracking | Owe vs owed tabs, person name, amount, remaining, due date |
| Insights | Spending breakdown | Pie charts, bar charts, daily summary, category breakdown |
| Settings | App config | Currency, theme, export backup, import, about |

### Third-Party Integrations (Optional)

| Service | Purpose | Cost |
|---|---|---|
| (None required) | Offline-first | $0 |
| ExchangeRate API | Multi-currency (Phase 2) | Free tier |
| Alpha Vantage | Market data (Phase 3) | Free tier |
| RevenueCat | In-app purchase (if adding premium features) | Free tier |

### Tech Stack Summary

| Layer | Choice | Reason |
|---|---|---|
| Mobile Framework | Flutter | Fastest to ship both iOS + Android, good SQLite support |
| State Management | Riverpod | Simple, testable, no boilerplate |
| Local Database | drift (SQLite) | Type-safe, reactive streams, migrations |
| Local Storage | SharedPreferences | Settings, onboarding flags |
| Charts | fl_chart | Budget visuals, spending breakdown |
| Voice Input | speech_to_text (plugin) | On-device STT (iOS+Android) |
| Receipt OCR | google_ml_kit / apple_vision | On-device, no API calls |
| CI/CD | Codemagic / GitHub Actions | Flutter-specific, auto build + test |
| App Analytics | Firebase (opt-in) | Crashlytics only, no analytics tracking by default |
| Distribution | App Store + Play Store | One-time paid app model |

### Estimated MVP Build Time

| Phase | Time | Deliverable |
|---|---|---|
| Flutter scaffold + theme | 2 days | Navigation, theme, models |
| Account management | 3 days | CRUD accounts, balance tracking |
| Transaction CRUD | 5 days | Add/edit/delete/list transactions |
| Categories + budgets | 4 days | Category CRUD, budget progress |
| Savings goals | 2 days | Goal CRUD, add contribution |
| Debt tracking | 2 days | Debt CRUD, remaining calc |
| Home dashboard | 3 days | Summary cards, daily overview |
| Recurring transactions | 3 days | Recurring logic, auto-add |
| Export/backup | 2 days | CSV/JSON export, file backup |
| Settings + polish | 3 days | Theme, currency, about screen |
| Testing + bug fixes | 5 days | Unit tests, integration tests |
| App store prep | 3 days | Assets, screenshots, description, ASO |
| **Total** | **~37 days** | **Working MVP** |

### Cost Estimates

| Item | One-Time | Monthly |
|---|---|---|
| Flutter dev (self) | $0 | $0 |
| Apple Developer Account | $99/yr | $8.25 |
| Google Play Account | $25 | - |
| App icons / design (Figma) | $0 | $0 |
| CI/CD (Codemagic free tier) | $0 | $0 |
| APIs (Phase 2+) | $0 | $0 |
| **Total** | **$124/yr** | **$8.25/mo** |

### App Store Submission Checklist

- [ ] Apple Developer Program ($99/yr)
- [ ] Google Play one-time $25 fee
- [ ] App icons (all sizes: 1024×1024, iOS asset catalog, Android adaptive)
- [ ] Screenshots (6.7", 6.5", 5.5" + Android 8+ sizes)
- [ ] App description + keywords (ASO: "budget tracker, expense manager, finance, money")
- [ ] Privacy policy URL (even though no data collected — required by Apple)
- [ ] No crashes on iPhone SE through iPhone 16 Pro Max
- [ ] TestFlight internal + external testers
- [ ] iOS 17+ minimum deployment target
- [ ] Android 12+ minimum
- [ ] Offline mode tested (airplane mode — all features work)
- [ ] Review guidelines compliance (5.1.1 — data collection: none)

### Launch & Marketing Strategy

- **Product Hunt** launch with "offline-first AI budget tracker" angle
- **Threads/Twitter/X** — same viral strategy as Tarsi: "I built this in X weeks, now #1"
- **Reddit** — r/phinvest, r/budgeting, r/personalfinance, r/SideProject
- **Indie Hackers** — build-in-public thread
- **ASO** — Keywords: budget tracker, expense manager, personal finance, money manager, savings
- **Pricing:** $3.99 Android / $6.99 iOS one-time (undercut Tarsi slightly)
- **Privacy angle** — "No account. No tracking. No cloud. Your data never leaves your phone."

### Future Phases

**Phase 2 — Polish & Features**
- Voice input for transactions
- OCR receipt scanning
- Multi-profile support
- Multi-currency support
- Cashflow forecasting

**Phase 3 — AI & Market**
- Local AI chat (on-device LLM via CoreML/MLX)
- Investment tracking (stock + crypto portfolios)
- Spending insights & recommendations
- Spending pattern detection (ML on-device)

**Phase 4 — Distribution**
- Web version (Flutter Web) for desktop users
- iCloud/Google Drive backup for premium
- Community features (optional opt-in)
- Widget (iOS home screen + Android)

---

*This doc serves as the source of truth for the Tarsi clone project.*
*Last updated: 2026-05-07*
