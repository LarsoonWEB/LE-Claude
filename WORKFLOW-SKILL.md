---
name: project-workflow
description: >
  Full web application project workflow covering all phases from research to deployment.
  Use this skill at the start of any new project to understand the complete process,
  required steps, tools, skills, agents, services, and APIs for each phase.
  Activate when the user says "kick off a new project", "start a new project",
  "what are the project phases", "set up project workflow", or asks how to begin
  a web application project. Covers these development packages: brand guidelines,
  landing page, supporting pages, wiki, dynamic forms, quote generation,
  advanced PDF generation, email delivery, and admin panel.
tools: Read, Write, Bash, Glob, Grep
---

# Web Application Project Workflow

This skill defines the complete development workflow for web application projects.
It covers all phases, tools, Claude skills, subagents, external services, and APIs.
Use it to guide project setup, planning, and execution from day one.

---

## Preferred stack & services

Before starting any project, confirm the following preferred tools are in use.
If the client or team uses different services, update CLAUDE.md accordingly.

| Layer | Preferred choice |
|---|---|
| Frontend | React 19 · TypeScript · Vite · Tailwind CSS v4 · shadcn/ui |
| State | Zustand (UI state) · TanStack Query (server state) |
| Forms | React Hook Form · Zod validation |
| Backend | Node.js · Express · tRPC |
| Database | PostgreSQL via Supabase |
| Auth | Supabase Auth · OAuth (Google) · Magic link |
| PDF engine | Puppeteer (server-side) · react-pdf (browser preview) |
| Email | Zoho Mail (SMTP + API) |
| File storage | Supabase Storage + GitHub (assets + docs) |
| Deployment | Vercel (frontend) · Railway (API server) |
| CI/CD | GitHub Actions |
| Monitoring | Sentry (errors) · PostHog (analytics) |
| Mobile (opt.) | React Native · Expo · EAS Build |
| Source control | GitHub — monorepo, Issues, Milestones, PRs, Actions |
| Design | Figma — brand system, wireframes, hi-fi mockups |
| Image gen | Nano Banana (Google Gemini) — hero images, brand assets |
| Documents | Office 365 Word / Excel — stakeholder deliverables |
| Automation | Zapier MCP — GitHub, Zoho Mail, Slack, Linear |

---

## Development packages in scope

This workflow covers the following delivery packages.
At project kick-off, confirm which packages are active for this specific project
and adjust the phase sequence accordingly.

| Package | Description |
|---|---|
| Brand guidelines | Polish and document the visual identity system |
| Landing page | High-conversion public-facing marketing page |
| Supporting pages | About, Services, Case Studies, Blog, Contact, Legal |
| Wiki pages | Public or internal knowledge base |
| Dynamic forms | Quote request, contact, booking, lead capture |
| Quote generation | Automated PDF quote engine driven by form inputs |
| Advanced PDF docs | Proposals, contracts, installation reports, system specs |
| Email delivery | Transactional emails and notifications to customers |
| Admin panel | Internal CMS, lead management, quote CRM, analytics |
| Mobile app (opt.) | iOS + Android customer portal — activate as Phase M |

---

## Phase overview

```
Phase 01 — Research              → Research.md
Phase 02 — Planning              → CLAUDE.md + ProjectPlan.md + TodoList
Phase 03 — Review & corrections  → Gate: signed-off plan
Phase 04 — Brand identity        → Brand.md + design tokens
Phase 05 — Landing page design   → Figma mockups + component library
Phase 06 — Frontend development  → All pages coded
Phase 07 — Dynamic forms         → Form system live
Phase 08 — PDF & quote engine    → PDF pipeline + quote generator
Phase 09 — Admin panel           → Internal dashboard
Phase 10 — Deployment            → Production + CI/CD
Phase M  — Mobile app (optional) → iOS + Android
```

---

## Phase 01 — Research

**Goal:** Produce Research.md as the factual foundation for all decisions that follow.

### Steps
1. Run Claude deep research agent — market, competitors, tech landscape for the project domain
2. Analyse 5+ competitor websites — UX patterns, content strategy, quote/conversion flows
3. Define target user personas — demographics, decision triggers, information needs
4. Review tech landscape — PDF libs, CRM integrations, relevant third-party APIs
5. Research product/service specifics — pricing models, configuration options, common customer questions
6. Compile all findings into `Research.md` and commit to repo root

### Tools
- **Skills:** `web-search` · `file-reading` · `pdf-reading`
- **Agents:** `researcher.md` (Claude deep research) · Web search agent
- **Services:** SimilarWeb · G2 / Capterra · Perplexity · GitHub (competitor repos)
- **APIs:** Web search tool · GitHub API (read)

### Output
- `Research.md` committed to repo root
- Competitor analysis · user personas · feature priority list · risk register

---

## Phase 02 — Planning & Architecture

**Goal:** Produce all planning documents and scaffold the full repo including `.claude/` folder.

### Steps
1. Define final tech stack based on Research.md findings
2. Write `CLAUDE.md` — stack, folder structure, naming conventions, env vars, do-nots
3. Create `ProjectPlan.md` — sitemap, phase dependencies, risks, milestones
4. Set up GitHub repo with monorepo structure — `/apps/web` · `/apps/api` · `/packages/ui`
5. Create GitHub Issues backlog and milestones for each phase
6. Scaffold `.claude/` folder — populate skills and agents from the lists below
7. Create `.mcp.json` with GitHub, Vercel, Zapier MCP server configs
8. Create `TodoList.xlsx` with task ownership, priority, and sprint assignment
9. Write `Brand.md` placeholder with brand information gathered so far
10. Open PR with all planning files for team review

### Skills to install in `.claude/skills/`
Create one folder per skill, each containing a `SKILL.md` file:

| Skill folder | Purpose |
|---|---|
| `frontend-design/` | React components, Tailwind, responsive UI, accessibility |
| `brand-guidelines/` | Brand palette, typography, tone of voice, design tokens |
| `canvas-design/` | Poster-quality visuals, illustrations, infographics |
| `web-artifacts-builder/` | Multi-component React UIs with state, routing, shadcn/ui |
| `pdf/` | PDF creation, merging, server-side render templates |
| `docx/` | Word document generation — proposals, contracts, reports |
| `xlsx/` | Spreadsheet exports — quotes, lead lists, data tables |
| `pdf-reading/` | Extract text and tables from uploaded PDF files |
| `file-reading/` | Router skill — reads any uploaded file type correctly |
| `product-self-knowledge/` | Claude API and Claude Code feature reference |

### Agents to install in `.claude/agents/`
Create one `.md` file per agent with YAML frontmatter (name, description, tools, model):

| Agent file | Role | Tools | Model |
|---|---|---|---|
| `researcher.md` | Deep research — market, tech, competitors | Read, Bash, Grep | sonnet |
| `code-reviewer.md` | Reviews PRs for quality and best practices | Read, Grep, Glob | sonnet |
| `security-scanner.md` | Scans auth, forms, payments for vulnerabilities | Read, Grep, Glob | sonnet |
| `pdf-generator.md` | Generates branded PDFs from Supabase data | Read, Bash | sonnet |
| `test-runner.md` | Runs Vitest + Playwright, reports failures | Read, Bash | haiku |
| `deploy-checker.md` | Checks Vercel build logs and deployment health | Read, Bash | haiku |
| `brand-enforcer.md` | Audits components for Brand.md compliance | Read, Grep, Glob | sonnet |
| `form-validator.md` | Reviews form Zod schemas and UX flow | Read, Grep | sonnet |
| `email-drafter.md` | Drafts transactional email templates | Read, Write | sonnet |
| `admin-builder.md` | Scaffolds admin CRUD pages and data tables | Read, Write, Edit | sonnet |

### Tools
- **Skills:** `product-self-knowledge` · `docx` · `xlsx`
- **Agents:** Claude (Projects) · Claude Code CLI
- **Services:** GitHub (Issues + Milestones) · Office 365 Word / Excel · Figma
- **APIs:** GitHub API (write) · Zapier MCP (GitHub issues) · Office 365 API

### Output
- `CLAUDE.md` · `ProjectPlan.md` · `TodoList.xlsx`
- GitHub repo scaffold with `.claude/` folder fully populated
- GitHub Issues backlog · milestones · sprint 1 ready to start

---

## Phase 03 — Review & Corrections

**Goal:** Stakeholder sign-off before any design or code begins. Nothing moves forward without this gate.

### Steps
1. Share `Research.md` and `ProjectPlan.md` with stakeholders via GitHub PR
2. Conduct review session — validate assumptions, correct scope, confirm priorities
3. Apply all corrections to `CLAUDE.md`, `ProjectPlan.md`, and GitHub Issues backlog
4. Obtain sign-off — merge PR to main as the gate condition
5. Send Zoho Mail notification to team with confirmed plan and sprint start date

### Tools
- **Skills:** `docx` · `pdf` · `xlsx`
- **Agents:** Claude (reviewer) · team stakeholders
- **Services:** GitHub (PR review) · Office 365 Word (comments) · Zoho Mail · Loom (async video)
- **APIs:** GitHub API (PR) · Zoho Mail SMTP · Zapier MCP (Zoho Mail notify)

### Output
- Approved `CLAUDE.md` + `Research.md` + `ProjectPlan.md` — merged PR
- Updated TodoList — corrections applied, priorities confirmed
- Stakeholder sign-off email sent via Zoho Mail

---

## Phase 04 — Brand Identity

**Goal:** Produce `Brand.md` and design tokens that every subsequent phase inherits.

### Steps
1. Review existing brand assets — logo files, colour usage, font usage, any brand guidelines
2. Polish and document colour palette — primary, secondary, accent, neutrals, semantic colours
3. Define typography system — heading font, body font, size scale, line heights, weights
4. Document tone of voice — adjectives, dos and don'ts, example sentences
5. Create icon style guide — style (line/fill), weight, size grid, usage rules
6. Generate hero image concepts using Nano Banana — domain-relevant visuals
7. Export design tokens from Figma, commit to `/packages/ui/tokens/`
8. Write `Brand.md` — commit to repo and upload to Claude Project knowledge base

### Tools
- **Skills:** `canvas-design` · `brand-guidelines` · `frontend-design` · `pdf`
- **Agents:** Claude (design) · Nano Banana image agent · `brand-enforcer.md`
- **Services:** Figma · Nano Banana (Google Gemini) · Coolors · Google Fonts
- **APIs:** Google Fonts API · Nano Banana API (Gemini) · Figma API · GitHub API

### Output
- `Brand.md` — complete identity system documentation
- Design tokens committed to `/packages/ui/tokens/`
- Hero image set — 5+ AI-generated domain-relevant visuals
- Brand PDF one-pager for stakeholder reference

---

## Phase 05 — Landing Page Design

**Goal:** Wireframes, hi-fi Figma designs, and React component library — live on Vercel preview.

### Standard landing page sections
- Hero (headline, subheadline, primary CTA, hero image)
- How it works (3–4 step process)
- Products / Services (feature cards)
- Social proof (testimonials, logos, stats)
- Call to action (secondary CTA section)
- Footer (navigation, legal, social)

### Steps
1. Define all landing page sections and content hierarchy
2. Create wireframes in Figma for desktop and mobile
3. Design hi-fi mockups applying Brand.md tokens
4. Generate hero and section images via Nano Banana
5. Write section copy — headlines, subheadings, CTAs, microcopy
6. Build React component library from mockups using `frontend-design` skill
7. Deploy preview to Vercel for stakeholder review
8. Run `brand-enforcer.md` agent to verify compliance

### Tools
- **Skills:** `frontend-design` · `canvas-design` · `brand-guidelines` · `web-artifacts-builder`
- **Agents:** Claude (design) · Visualizer · Nano Banana image agent · `brand-enforcer.md`
- **Services:** Figma · Nano Banana · Unsplash / Pexels (stock) · Vercel (preview deploy)
- **APIs:** Figma API · Nano Banana API · Vercel MCP · GitHub API · Image search tool

### Output
- Figma file — wireframes + hi-fi mockups (desktop + mobile)
- React component library in `/packages/ui/components/`
- Landing page live on Vercel preview URL
- Section copy document

---

## Phase 06 — Frontend Development

**Goal:** All pages coded, tested, accessible, and merged to main.

### Pages included for this project
- Landing page (Phase 05 design → code)
- About page — company story, team, certifications, mission
- Services pages — one per primary service offering
- Case Studies — project gallery with specs and outcomes
- Blog / News — CMS-driven articles
- Wiki pages — public knowledge base (powered by Supabase CMS)
- Contact page — map, contact form, contact details
- Legal pages — Privacy Policy, Terms of Service, Cookie Policy

### Steps
1. Scaffold React + Vite project with Tailwind CSS v4 and shadcn/ui
2. Implement routing — all page routes with React Router
3. Build all page components from Figma designs
4. Implement Supabase Auth — OAuth + magic link
5. Connect Blog, Wiki, and Case Studies to Supabase CMS tables
6. SEO setup — meta tags, Open Graph, sitemap.xml, robots.txt
7. Accessibility audit — WCAG 2.1 AA
8. Performance optimisation — lazy loading, image optimisation, Core Web Vitals
9. Run `code-reviewer.md` and `security-scanner.md` before PR

### Tools
- **Skills:** `frontend-design` · `web-artifacts-builder` · `brand-guidelines`
- **Agents:** Claude Code CLI · VS Code extension · GitHub Actions agent · `brand-enforcer.md` · `code-reviewer.md`
- **Services:** GitHub · Vercel · Figma (design handoff) · Supabase
- **APIs:** GitHub API · Vercel MCP · Supabase API · Google Fonts API · Zapier MCP

### Output
- All pages implemented and passing code review
- Supabase Auth working · Blog + Wiki + Case Studies CMS connected
- Lighthouse score 90+ on all pages
- PR merged to main · Vercel production deploy

---

## Phase 07 — Dynamic Forms

**Goal:** All public-facing and internal forms live, validated, spam-protected, and wired to Supabase + Zoho Mail.

### Forms for this project
- **Quote request form** — multi-step (property → requirements → contact) — triggers quote generation
- **Contact form** — name, email, phone, message, preferred contact method
- **Booking / appointment form** — date/time picker, address, visit type
- **Newsletter signup** — email, consent checkbox, interest selection
- **Admin lead intake** — internal form for manually adding leads

### Steps
1. Define Zod schemas for all forms — field types, validation rules, error messages
2. Build multi-step quote form — conditional fields based on previous step answers
3. Add reCAPTCHA v3 to all public forms
4. Wire all submissions to Supabase (leads table) + Zapier (Zoho Mail notification to team)
5. Implement Stripe payment intent on booking form if deposit collection is required
6. Build thank-you pages and email confirmation flows via Zoho Mail
7. Test all forms — Zod schema unit tests + Playwright E2E
8. Run `form-validator.md` agent to review all schemas and UX flow

### Tools
- **Skills:** `web-artifacts-builder` · `frontend-design`
- **Agents:** Claude Code CLI · Claude in artifacts · Zapier agent · `form-validator.md`
- **Services:** Stripe (payments) · Zoho Mail (confirmations) · reCAPTCHA · GitHub
- **APIs:** Stripe API · Zoho Mail SMTP / API · reCAPTCHA API · Zapier MCP · Supabase API

### Output
- All forms live — validated, tested, accessible
- Lead capture → Supabase + Zoho Mail notification pipeline working
- Stripe payment flow active on booking form
- Thank-you pages + customer email confirmations working

---

## Phase 08 — Quote Generation & Advanced PDF Documents

**Goal:** Automated PDF quote engine and full document generation pipeline.

### Documents for this project
- **Customer quote PDF** — line items, pricing, validity, terms, branding
- **System / service proposal** — detailed specification, timeline, financials
- **Contract / agreement** — legal terms, scope of work, payment schedule
- **Confirmation / welcome document** — sent on booking or purchase
- **Report PDF** — post-service report with data, outcomes, recommendations

### Steps
1. Design all PDF templates in Figma applying Brand.md
2. Build server-side Puppeteer render endpoint on Railway API server
3. Create HTML/CSS templates for each document type
4. Implement quote calculation engine — line item logic, pricing rules, discount handling
5. Wire quote form submission → calculation → PDF generation → Zoho Mail delivery
6. Build react-pdf browser preview (live before sending)
7. Store all generated PDFs in Supabase Storage with signed download URLs
8. Implement authenticated PDF download endpoint
9. Run `pdf-generator.md` and `email-drafter.md` agents

### Tools
- **Skills:** `pdf` · `docx` · `xlsx` · `canvas-design` · `brand-guidelines`
- **Agents:** Claude Code CLI · `pdf-generator.md` · `email-drafter.md` · `brand-enforcer.md`
- **Services:** Puppeteer / react-pdf · Vercel (PDF endpoint) · Zoho Mail · Supabase Storage · Office 365
- **APIs:** Vercel MCP · Zoho Mail SMTP / API · Supabase Storage API · GitHub API · Office 365 API

### Output
- Quote PDF engine — branded quote generated in < 3 seconds
- All document templates live and tested
- Quote → PDF → Zoho Mail delivery pipeline working end to end
- PDF browser preview before send
- All PDFs stored in Supabase with signed download links

---

## Phase 09 — Admin Panel

**Goal:** Internal dashboard for lead management, quote CRM, and content management.

### Admin features for this project
- **Lead list** — all form submissions with status (new / contacted / quoted / won / lost)
- **Lead detail view** — full form data, quote history, notes, activity timeline
- **Quote builder** — manual quote creation with PDF preview and Zoho Mail send
- **Content management** — Blog, Wiki, Case Studies CRUD
- **User management** — admin users with roles (admin / sales / operator)
- **Analytics dashboard** — lead volume, conversion rate, quote acceptance rate (PostHog)
- **Email log** — all sent emails with delivery status via Zoho Mail API

### Steps
1. Scaffold admin panel at `/admin` — route protected by Supabase Auth (admin role only)
2. Build data tables with sorting, filtering, and search for leads, quotes, content
3. Implement role-based access control
4. Build quote builder UI with live react-pdf preview pane
5. Connect PostHog analytics to admin dashboard widgets
6. Build email log view — query Zoho Mail API for delivery status
7. Playwright E2E tests for all admin flows
8. Run `admin-builder.md`, `security-scanner.md`, and `code-reviewer.md` agents

### Tools
- **Skills:** `frontend-design` · `web-artifacts-builder` · `brand-guidelines`
- **Agents:** Claude Code CLI · `admin-builder.md` · `code-reviewer.md` · `security-scanner.md`
- **Services:** GitHub · Vercel · Supabase (auth + DB) · PostHog · Zoho Mail
- **APIs:** Supabase API · PostHog API · Zoho Mail API · Vercel MCP · GitHub API

### Output
- Admin panel live at `/admin` — role-protected
- Lead CRM with full quote workflow
- Content management for Blog, Wiki, Case Studies
- Analytics + email log dashboard

---

## Phase 10 — Deployment & Automation

**Goal:** Production live, CI/CD pipeline active, monitoring and alerting in place.

### Steps
1. Configure GitHub Actions CI — lint, typecheck, unit tests, build on every PR
2. Set up Vercel production deployment — auto-deploy on merge to main
3. Configure Sentry error monitoring — source maps, alert rules, Zoho Mail notifications
4. Set up PostHog production workspace — funnels for quote flow and admin usage
5. Configure Zoho Mail domain authentication — SPF, DKIM, DMARC records
6. Run Lighthouse CI in GitHub Actions pipeline — enforce 90+ score gate
7. Run `security-scanner.md` agent across full codebase before go-live
8. Set up uptime monitoring — alert to Zoho Mail on downtime
9. Write `Runbook.md` — incident response, rollback procedure, env var management

### Tools
- **Skills:** `product-self-knowledge` · `file-reading`
- **Agents:** Claude Code agents · GitHub Actions agent · Zapier agent · `deploy-checker.md` · `security-scanner.md`
- **Services:** GitHub Actions (CI/CD) · Vercel (production) · Sentry · PostHog · Zoho Mail (alerts)
- **APIs:** Vercel MCP · GitHub API (Actions) · Zapier MCP (GitHub + Zoho) · Sentry API · PostHog API · Zoho Mail SMTP / API

### Output
- Live production site on Vercel
- CI/CD pipeline — automated test + deploy on PR merge
- Sentry error monitoring active · Zoho Mail alerts configured
- PostHog analytics tracking · funnel dashboards live
- `Runbook.md` committed to repo

---

## Phase M — Mobile App (Optional)

**Goal:** iOS and Android apps sharing web codebase logic. Activate after Phase 10 when in scope.

### Recommended approach
React Native + Expo — shares business logic, tRPC API calls, Zod schemas, Zustand state,
and Brand.md design tokens from the monorepo. Use EAS Build for signed binaries.
Only switch to native Swift/Kotlin if the project requires heavy device hardware access.

### Mobile sub-phases
1. **M1 — Mobile UX & screen design** — Figma screens for iOS + Android conventions
2. **M2 — Expo setup + shared code** — scaffold `/apps/mobile` in monorepo
3. **M3 — Core screens** — auth, home, quote request, document download, notifications
4. **M4 — Push notifications** — APNs (iOS via Expo) · FCM (Android via Firebase)
5. **M5 — Build, test, submit** — EAS Build → TestFlight + Play Store internal track → review

### Preconditions before writing mobile code
- Apple Developer Program enrolled ($99/yr) — takes 2–5 days to approve
- Google Play Console created ($25 one-time)
- Firebase project created — FCM configuration requires SHA fingerprints from signed build

### Tools
- **Skills:** `frontend-design` · `brand-guidelines` · `canvas-design` · `file-reading`
- **Agents:** Claude Code CLI · VS Code extension · GitHub Actions agent · `brand-enforcer.md`
- **Services:** Expo · EAS Build · Figma · Nano Banana · Sentry · PostHog · Zoho Mail
- **APIs:** Apple Developer API · App Store Connect API · Google Play API · Firebase FCM API · Expo EAS API · Supabase API · Stripe Mobile SDK · GitHub API · Vercel MCP (shared API)

### Output
- React Native screens sharing web component logic and Brand.md tokens
- iOS `.ipa` — signed, uploaded to TestFlight via EAS Build
- Android `.aab` — uploaded to Google Play internal track via EAS Build
- Push notifications — APNs for iOS, FCM for Android
- OTA update pipeline via Expo

---

## Repository structure

Every project using this workflow follows this structure:

```
/
├── CLAUDE.md                     ← Claude Code project context
├── Research.md                   ← Phase 01 output
├── ProjectPlan.md                ← Phase 02 output
├── Brand.md                      ← Phase 04 output
├── Runbook.md                    ← Phase 10 output
├── .claude/
│   ├── skills/
│   │   ├── frontend-design/SKILL.md
│   │   ├── brand-guidelines/SKILL.md
│   │   ├── canvas-design/SKILL.md
│   │   ├── web-artifacts-builder/SKILL.md
│   │   ├── pdf/SKILL.md
│   │   ├── docx/SKILL.md
│   │   ├── xlsx/SKILL.md
│   │   ├── pdf-reading/SKILL.md
│   │   ├── file-reading/SKILL.md
│   │   └── product-self-knowledge/SKILL.md
│   ├── agents/
│   │   ├── researcher.md
│   │   ├── code-reviewer.md
│   │   ├── security-scanner.md
│   │   ├── pdf-generator.md
│   │   ├── test-runner.md
│   │   ├── deploy-checker.md
│   │   ├── brand-enforcer.md
│   │   ├── form-validator.md
│   │   ├── email-drafter.md
│   │   └── admin-builder.md
│   ├── settings.json             ← hooks · permissions · env config
│   └── .mcp.json                 ← GitHub · Vercel · Zapier MCP servers
├── apps/
│   ├── web/                      ← React + Vite frontend
│   └── api/                      ← Node.js + Express + tRPC backend
├── packages/
│   ├── ui/                       ← Shared React component library
│   │   └── tokens/               ← Design tokens from Figma + Brand.md
│   └── types/                    ← Shared TypeScript types
└── tests/
    └── e2e/                      ← Playwright end-to-end tests
```

---

## Preconditions checklist

When starting a new project, confirm all of the following before Phase 01:

### Accounts to create
- [ ] GitHub — organisation account, monorepo created
- [ ] Vercel — project connected to GitHub, custom domain configured
- [ ] Supabase — project created, DB URL and anon key noted
- [ ] Zoho Mail — business email configured, SMTP credentials + API key obtained
- [ ] Stripe — account verified, publishable + secret keys noted
- [ ] Google reCAPTCHA v3 — site key + secret key obtained
- [ ] Figma — team workspace with project created
- [ ] Sentry — project created, DSN key noted
- [ ] PostHog — project created, API key noted
- [ ] Nano Banana / Google AI Studio — API key for image generation
- [ ] Apple Developer Program — $99/yr (Phase M only, allow 2–5 days)
- [ ] Google Play Console — $25 one-time (Phase M only)

### Environment variables (.env.local — never commit)
Claude Code will generate `.env.example` with these names. Values must be supplied manually.

```
VITE_SUPABASE_URL=
VITE_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
DATABASE_URL=
VITE_STRIPE_PUBLISHABLE_KEY=
STRIPE_SECRET_KEY=
ZOHO_SMTP_HOST=
ZOHO_SMTP_USER=
ZOHO_SMTP_PASSWORD=
ZOHO_MAIL_API_KEY=
VITE_RECAPTCHA_SITE_KEY=
RECAPTCHA_SECRET_KEY=
SENTRY_DSN=
VITE_POSTHOG_KEY=
GITHUB_TOKEN=
VERCEL_TOKEN=
ZAPIER_MCP_KEY=
NANO_BANANA_API_KEY=
```

---

## Project rules

Apply these rules to every project using this workflow. Document any project-specific
overrides in `CLAUDE.md`.

### Code
- Always use TypeScript — no plain `.js` files in `src/`
- No `any` types — use `unknown` and narrow properly
- All async functions must handle errors — try/catch or Result types
- No `console.log` in production — use the `logger` utility
- No raw SQL — use Drizzle ORM query builder
- No raw `fetch()` in components — use tRPC hooks only
- Never skip Zod validation on any user input including URL params
- Never use `// @ts-ignore` — fix the type error
- Mobile-first responsive — `sm:` `md:` `lg:` breakpoints

### Design
- All components must comply with `Brand.md` — run `brand-enforcer.md` before every PR
- Dark mode supported — always test both modes
- WCAG 2.1 AA accessibility — colour contrast, keyboard nav, ARIA labels
- Use `cn()` from `lib/utils.ts` to merge Tailwind classes
- Never hardcode colours — always use design tokens from `/packages/ui/tokens/`

### Git
- Never commit directly to `main` — always open a PR with at least one review
- Never commit `.env.local` or any file containing secrets or tokens
- Commit format: `type(scope): description` — e.g. `feat(forms): add quote step 3`
- PR title must reference the GitHub Issue — e.g. `[#42] Add PDF quote generation`
- All PRs must pass CI (lint + typecheck + tests) before merge

### Security
- Run `security-scanner.md` on every PR touching auth, forms, or payments
- All public forms require reCAPTCHA v3
- PDFs must be served via signed Supabase Storage URLs — never public
- Admin routes must be protected by Supabase Auth role check server-side

---

## How to use this skill at project kick-off

When starting a new project, Claude will:

1. Ask for the project name, company, domain, and brief description
2. Ask which development packages from the list above are in scope
3. Ask which packages from the preferred stack need to change for this client
4. Confirm the optional Phase M (mobile) is in or out of scope
5. Begin Phase 01 — Research — using the researcher.md agent
6. On completion of Research.md, proceed to Phase 02 — Planning
7. Generate all planning documents, scaffold the repo, and populate `.claude/`
8. Prompt for Phase 03 review before any design or code work begins

To kick off: say "start a new project" or "kick off [project name]" and this skill activates.
