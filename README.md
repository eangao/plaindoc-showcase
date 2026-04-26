# PlainDoc

> **Live:** [plaindoc.app](https://plaindoc.app)
> **Tagline:** *Documents made plain.*
> **Status:** In production · Real users · ₱ revenue
> **Role:** Solo founder · Designer · Engineer · Operator

PlainDoc is an AI-powered legal document analyzer for consumers — production SaaS at plaindoc.app. Upload a contract — get a plain-language breakdown with risk flags, missing protections, and action recommendations in about 30 seconds.

This repository documents the system at a high level. The application code lives in a private repository at [github.com/eangao/plain-doc](https://github.com/eangao/plain-doc) — kept private because PlainDoc is a live commercial SaaS, not an open-source side project. For a public open-source AI document analysis tool I built using the same Claude API + Next.js patterns, see [ai-document-analyzer](https://github.com/eangao/ai-document-analyzer).

## The problem

Filipino workers — kasambahay, OFWs, BPO staff, agency hires, rank-and-file employees — sign employment contracts they cannot fully read. Legal English is dense, clauses are buried, and the consequences of missed protections (excessive hours, unfair termination, illegal deductions) can take years of pay or rights to recover. Hiring a lawyer for every contract review is not realistic for the people who need it most.

PlainDoc closes that gap.

## How it works

1. **Upload your contract.** Take a photo or upload a PDF of your employment contract. Multi-page documents up to 10MB.
2. **AI reads every clause.** The system reads the contract end-to-end and cross-references it against Philippine labor law, minimum wage orders, and standard employment practices.
3. **Get your analysis.** A plain-language breakdown is returned in about 30 seconds.

## What every analysis includes

- **Plain-language summary** — complex legal jargon translated into everyday language
- **Compensation breakdown** — salary, bonuses, deductions, and minimum wage comparison for your region
- **Risk flags** — clauses that could harm the worker: excessive hours, unfair termination, illegal deductions
- **Action recommendations** — specific steps to take before signing, based on the issues found

## Privacy posture

Contracts are processed in memory and immediately discarded. PlainDoc does not store user contracts and does not use them to train AI models. The privacy guarantee is core to the product and is not optional.

## Pricing model

| Tier | Price | Pages | Per-Analysis Cap | Save / Export |
|---|---|---|---|---|
| Free | ₱0 / month | 4 / month | 2 pages max | Analysis only — no save, no export |
| Pay-per-use | ₱49 / pack | 3 per pack · never expires | No cap | Save to dashboard · Export single PDF |
| Standard | ₱1,499 / month | 150 / month (~₱9.99/page) | No cap | Save · Export PDF · Bulk JSON export |
| Agency Pro | ₱5,999 / month | 1,200 / month (~₱5.00/page) | No cap | Save · Export PDF · Bulk JSON export |

A page = one page of the contract (PDF or camera photo). A 3-page contract uses 3 pages from your balance. Subscriptions burn monthly pages before purchased packs.

**Sample sizing:** a 3-page kasambahay or short offer letter costs ₱49 (1 pack). A 5-page OFW or rank-and-file contract with a rider costs ₱98 (2 packs). A 10-page BPO or executive package costs ₱196 (4 packs).

Payments processed via PayMongo: GCash, Maya, QR Ph, and cards.

## Tech stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (App Router, Turbopack) |
| Language | TypeScript (strict) |
| Styling | Tailwind CSS v4 |
| AI / Document Analysis | Claude API (Anthropic, Sonnet) |
| Database | Supabase (PostgreSQL) |
| Auth | Clerk |
| Payments | PayMongo (GCash, Maya, QR Ph, cards) |
| Rate limiting | Upstash Redis |
| Hosting | Vercel (edge-deployed) |
| File handling | In-memory processing (no contract storage) |

## Architecture (high level)

```
                 ┌─────────────┐
                 │   Browser   │
                 └──────┬──────┘
                        │
                        ▼
              ┌──────────────────┐
              │ Next.js (Vercel) │
              │  App Router      │
              └────┬─────────┬───┘
                   │         │
        ┌──────────┘         └──────────┐
        ▼                                ▼
┌──────────────┐                 ┌──────────────────┐
│  Clerk Auth  │                 │   Claude API     │
└──────────────┘                 │ (contract        │
                                 │  analysis,       │
                                 │  in-memory only) │
        ▼                        └──────────────────┘
┌─────────────────────┐                   │
│  Supabase           │◄──────────────────┘
│ (Postgres — stores  │
│  results + user     │
│  data; NOT          │
│  contracts)         │
└─────────────────────┘
        │
        ▼
┌──────────────┐
│  PayMongo    │
│ (GCash/Maya/ │
│  QR Ph/cards)│
└──────────────┘

         ┌──────────────────────┐
         │  Upstash Redis       │
         │  (rate limiting,     │
         │   page-quota state)  │
         └──────────────────────┘
```

## What I built end-to-end

- **Product** — discovered the problem, validated the market, scoped the MVP, designed the UX, made every product decision
- **Frontend** — every screen, every Tailwind utility, every animation
- **Backend** — API routes, the contract ingestion pipeline, the Claude prompt engineering for PH labor-law cross-referencing, the rate-limiting and page-quota logic
- **Database** — Postgres schema design, RLS policies, indexing
- **Auth** — Clerk integration, session handling, webhooks
- **Payments** — PayMongo integration (GCash/Maya/QR Ph/cards), webhook handling, subscription state machine, page-pack accounting
- **Infrastructure** — Vercel deployment config, Supabase setup, Cloudflare DNS, Upstash Redis, monitoring
- **Operations** — customer support, content updates, ongoing improvements
- **Marketing** — landing page, pricing page, blog, How It Works, FAQ — all written and designed solo

This is the closest analogue I have to "solo end-to-end product ownership." Every line of code, every infrastructure decision, every product decision is mine.

## Why closed-source

PlainDoc is a live, revenue-generating SaaS with real customers. The repository contains:

- Production credentials and integration tokens
- Proprietary prompt engineering for Philippine labor-law cross-referencing
- Customer data handling logic regulated under PH RA 10173 (Data Privacy Act of 2012)
- PCI-relevant payment integration flow

Open-sourcing this would create security risk and undermine the live product. The README + the live site at [plaindoc.app](https://plaindoc.app) provide the verification that recruiters and collaborators need. The companion repo [ai-document-analyzer](https://github.com/eangao/ai-document-analyzer) is open-source and demonstrates the same Claude API integration patterns.

## Disclaimer

PlainDoc is AI-powered analysis to support professional judgment. It is not legal advice. For complex legal matters, users are encouraged to consult a licensed Philippine attorney.

## See it live

→ [plaindoc.app](https://plaindoc.app)

## Contact

**Elmar Angao** — Fullstack Developer | AI Integration Specialist

- Email: elmarcera@gmail.com
- GitHub: [@eangao](https://github.com/eangao)
- LinkedIn: [elmar-angao](https://www.linkedin.com/in/elmar-angao/)
