# Larsoon Energy d.o.o. — Web Platform: Project Description

> **Instruction document for Claude Code**
> Language: Croatian (HR) throughout all user-facing content
> Currency: EUR (€)
> Market: Croatia
> **See also: `calculation-rules.md`** — the complete specification for all formulas, constants, and logic. `lib/calculations.ts` and `lib/constants.ts` must implement it exactly.

---

## 1. Project Overview

Build a full-stack web platform for **Larsoon Energy d.o.o.**, a Croatian company that designs, installs, monitors and maintains photovoltaic solar systems for residential and industrial clients.

The platform has four major sections:

| Section | Purpose |
|---|---|
| **Landing Page** | Brand visibility, product/service presentation, dynamic savings calculator, conversion to quote |
| **Wiki** | Public educational resource on solar energy (technical, legal, financial, practical) |
| **Quote Wizard** | Multi-step interactive form → on-screen results → PDF sent to customer email |
| **AI Chat Agent** | Live AI-powered chat widget available on all pages for customer questions and guidance |

---

## 2. Tech Stack Recommendation

Use **Next.js 14+ (App Router)** with the following:

- **Frontend:** React, Tailwind CSS, shadcn/ui components
- **Backend:** Next.js API Routes (no separate server needed)
- **PDF generation:** `@react-pdf/renderer` or `puppeteer` (Claude Code to choose best fit)
- **Email sending:** Resend (resend.com) — simple API, free tier covers MVP needs
- **CMS for Wiki:** MDX files stored in `/content/wiki/` — no external CMS needed for v1; content is managed by editing MDX files directly
- **Database:** None required for v1 — lead data is captured via email only
- **Maps / Solar irradiation:** Google Maps JavaScript API for location picker; irradiation data via [PVGIS API](https://re.jrc.ec.europa.eu/pvg_tools/en/) (free EU Commission API, no key required)
- **AI Chat Agent:** Claude API (`claude-sonnet-4-5` model) via Anthropic SDK — streaming chat responses, solar-domain system prompt
- **Deployment:** Vercel (matches existing prototype environment)

---

## 3. Brand & Design

### Identity
- **Company name:** Larsoon Energy d.o.o.
- **Tagline (suggested):** *Energija budućnosti, danas.*
- **Language:** Croatian only
- **Logo:** ⚠️ Logo will be provided by the client during a separate brand guideline phase. For v1, use a text-based logotype: "Larsoon Energy" in Inter Bold with the primary orange colour. Reserve a `public/images/logo.svg` and `public/images/logo-white.svg` placeholder path so swapping is a single file replacement.

### Visual Style
- Carry over the existing prototype's visual identity:
  - **Primary colour:** Orange `#F97316` (orange-500)
  - **Accent/gradient:** Orange → Yellow (`from-orange-500 to-yellow-500`)
  - **Background:** White / light grey `#F9FAFB`
  - **Text:** `#111827` (gray-900)
  - **Font:** Inter (Google Fonts)
- Style should feel modern, trustworthy, clean — not corporate heavy
- Use Lucide React for all icons (already in prototype)

---

## 4. Section A — Landing Page (`/`)

### 4.1 Navigation (sticky header)
- Logo left
- Nav links: Rješenja | Usluge | Wiki | O nama | Kontakt
- CTA button right: **"Zatražite ponudu"** → scrolls to or links to `/quote`

### 4.2 Hero Section
- Full-width headline + subheadline
- Two CTA buttons: **"Zatražite besplatnu ponudu"** (primary) + **"Saznajte više"** (secondary, scrolls down)
- Background: gradient or high-quality solar/rooftop image

### 4.3 Solutions Section ("Naša rješenja")
Three product cards:
1. **Fotonaponske elektrane** — on-grid and off-grid
2. **Baterijski sustavi** — energy storage
3. **Punjači za električna vozila (EV)** — EV charging

Each card: icon, short description, "Saznajte više" link.

### 4.4 Services Section ("Naše usluge")
Four service cards with icons:
1. **Projektiranje** — system design
2. **Instalacija** — through certified installer network
3. **Aktivno praćenje** — real-time monitoring
4. **Proaktivno održavanje i čišćenje panela**

### 4.5 Why Us / Stats Bar
Highlight 3–4 key trust numbers (use placeholder values, easy to update):
- npr. `150+` instaliranih elektrana
- `2.5 MW` ukupne instalirane snage
- `100%` zadovoljnih klijenata
- `25 god.` jamstvo na panele

### 4.6 How It Works ("Kako funkcionira")
Simple numbered timeline (horizontal on desktop, vertical on mobile):
1. Zatražite ponudu
2. Tehnička analiza
3. Instalacija
4. Praćenje i održavanje

### 4.7 Dynamic Savings Calculator ("Izračunajte Vašu Solarnu Uštedu")

An interactive calculator widget embedded on the landing page — visible without navigating to `/quote`. Replicates the widget currently on larsoon.com. This is a **conversion tool**: it gives an instant result to hook the visitor, then drives them to the full Quote Wizard for a personalised offer.

**UI Layout (two-column card, matching the screenshot):**
- Left column: inputs
- Right column: live result metric cards

**Inputs (left side):**
- **Veličina Sustava** — horizontal slider, range 1–20 kWp, step 0.5 kWp, default 5 kWp. Current value displayed in green next to label.
- **Veličina Baterijskog Paketa** — horizontal slider, range 0–20 kWh, step 5 kWh, default 0 kWh. Current value displayed in blue.
- **Dodaj EV Punjač** — checkbox toggle with label and price displayed inline (e.g., `€1,500`). Checked state shows in green.

**Result metric cards (right side, 2×3 grid):**

| Card | Icon colour | Label | Value source |
|---|---|---|---|
| Početna Investicija | green | `€X,XXX` | `system_kWp × BASE_COST_EUR_PER_KWP + battery_kWh × BATTERY_COST_PER_KWH + (EV_CHARGER_COST if checked)` |
| Godišnja Proizvodnja | yellow | `X,XXX kWh` | `system_kWp × DEFAULT_IRRADIATION × PERFORMANCE_RATIO` |
| Godišnja Ušteda | blue | `€X,XXX` | standard savings formula from `calculations.ts` |
| Razdoblje Otplate | purple | `X.X god` | `investment / annual_savings` |
| Cijena Punjača | orange | `€X,XXX` or `€0` | shown only if EV checked; otherwise `—` |

> **Note:** The landing page calculator uses `DEFAULT_IRRADIATION = 1450 kWh/m²/year` (Croatian national average) as a constant — it does **not** call PVGIS. The full Quote Wizard fetches real irradiation per location.

**Visual style:** Rounded card with a soft multicolour top border gradient (matching the larsoon.com screenshot). Metric cards have light pastel backgrounds matching their icon colour. Values update instantly on every slider/checkbox change with no submit button.

**CTA below the calculator:**
- Text: *"Ovo je procjena. Za točnu ponudu prilagođenu Vašoj lokaciji i krovu:"*
- Button: **"Zatražite detaljnu ponudu"** → `/quote`

### 4.9 Wiki Teaser
Short intro + 3 featured wiki article cards linking to `/wiki`.

### 4.10 CTA Banner
Full-width orange gradient banner:
- "Počnite štediti danas"
- Button: **"Zatražite besplatnu informativnu ponudu"** → `/quote`

### 4.11 Contact Section ("Kontakt")
Simple contact form: Ime, Email, Telefon, Poruka + Submit button.
On submit: send email via the same Resend integration used for the quote.

### 4.12 Footer
- Logo, short description
- Links: Rješenja, Usluge, Wiki, Kontakt, Politika privatnosti
- Social icons (LinkedIn, Instagram)
- © 2025 Larsoon Energy d.o.o.

---

## 5. Section B — Wiki (`/wiki` and `/wiki/[slug]`)

### 5.1 Wiki Index Page (`/wiki`)
- Page header with search input (client-side filtering)
- Articles organised into **4 categories**:

| Category | HR Label | Example Topics |
|---|---|---|
| Technical | Tehničke osnove | Kako rade solarni paneli, inverteri, baterijski sustavi, off-grid vs on-grid |
| Legal | Pravni aspekti | Zakonodavstvo u HR, dozvole, mrežni priključak, feed-in tarifa |
| Financial | Financijski aspekti | ROI kalkulator, poticaji, EU fondovi, leasing, porezne olakšice |
| Practical | Praktični savjeti | Čišćenje panela, monitoring, što napraviti kada padne snijeg, koga zvati |

- Each category shown as a section with article cards (title, excerpt, read time, category badge)

### 5.2 Article Page (`/wiki/[slug]`)
- MDX-based — content stored in `/content/wiki/[slug].mdx`
- Frontmatter fields: `title`, `excerpt`, `category`, `readTime`, `publishedAt`, `author`
- Sidebar: table of contents (auto-generated from H2/H3 headings), related articles
- Breadcrumb navigation
- "Povratak na Wiki" link

### 5.3 Initial Content
Create **8 placeholder articles** (2 per category) with realistic titles and ~3 paragraphs of placeholder body text in Croatian. These demonstrate the structure and are ready for the client to replace with real content.

---

## 6. Section C — Quote Wizard (`/quote`)

This is the most complex section. Replicate and improve upon the existing prototype at `https://v0-modern-saa-s-landing-page-88bd5y4bo.vercel.app/quote`.

### 6.1 Layout
- **Left column (2/3 width):** Sequential form cards, one per step
- **Right column (1/3 width, sticky):** Live "Pregled sustava" summary panel that updates as user fills in data
- Mobile: single column, sticky mini-summary bar at top showing current production estimate

### 6.2 Form Steps

**Step 1 — Lokacija elektrane**
- Interactive map (Google Maps) with click-to-pin or address autocomplete
- When location is selected: call PVGIS API to fetch the annual solar irradiation (`H_year` in kWh/m²/year) for that exact coordinate. Store this value — it drives all production calculations.
- Display fetched irradiation value as a badge: e.g., *"Godišnja insolacija: 1,487 kWh/m²"*

**Step 2 — Odabir snage elektrane**
Two mutually exclusive modes (selectable cards):
- **Mode A: "Znam snagu"** — slider or numeric input, range 1–100 kWp (step 0.5 kWp)
- **Mode B: "Procijeni prema potrošnji"** — input annual consumption in kWh/year OR monthly bill in €; system calculates recommended kWp:
  - Formula: `recommended_kWp = annual_kWh / (irradiation * performance_ratio)`
  - `performance_ratio = 0.80` (constant, accounts for losses)
  - If monthly bill entered: `annual_kWh = monthly_bill / 0.12 * 12` (using 0.12 €/kWh as Croatian avg tariff — define as a named constant so it's easy to update)

**Step 3 — Tip krova**
Selectable card grid (affects installation cost factor):
- Ravni krov (`factor: 1.05`)
- Crijep (`factor: 1.00`)
- Lim/trapezni (`factor: 0.95`)
- Šindra (`factor: 1.10`)

**Step 4 — Orijentacija krova**
Circular compass UI (8 directions: S, SZ, Z, JZ, J, JI, I, SI).
Each direction has an `orientation_factor`:
- J (South): `1.00`
- JI / JZ: `0.97`
- I / Z: `0.90`
- SI / SZ: `0.80`
- S (North): `0.70`

**Step 5 — Nagib krova**
Slider 0°–90°, default 35°.
`tilt_factor` lookup (approximate cosine correction for Croatia latitude ~45°N):
- 0°: `0.85`, 15°: `0.93`, 30°: `0.99`, 35°: `1.00` (optimal), 45°: `0.98`, 60°: `0.91`, 90°: `0.70`
Interpolate linearly between breakpoints.

**Step 6 — Zasjenjenost**
Four levels (selectable icons + slider):
- Nije zasjenjen: `shadow_factor = 1.00`
- Slabo zasjenjen: `shadow_factor = 0.92`
- Jako zasjenjen: `shadow_factor = 0.80`
- Vrlo jako zasjenjen: `shadow_factor = 0.65`

**Step 7 — Vrsta priključka**
Three cards (affects max allowed system size):
- Trofazni (3f): max 30 kWp for residential
- Jednofazni (1f): max 3.68 kWp for residential
- Off-grid (bez priključka): no limit, but battery is required

**Step 8 — Punjač za električno vozilo (EV)**
Yes / No cards. If Yes: note that system sizing recommendation increases by 2,000 kWh/year (average EV annual consumption). This feeds back into the sizing in Step 2 Mode B.

**Step 9 — Baterijski sustav**
Yes / No cards. If Yes: show a secondary selector for desired battery capacity (5, 10, 15, 20 kWh — selectable cards).

**Step 10 — Vaši podaci** *(contact details — unlocks investment cost)*
Fields: Ime i prezime\*, Email\*, Telefon\*
Validation: all fields required, email format, phone format.

### 6.3 Calculation Engine

All calculations happen client-side in a dedicated `lib/calculations.ts` module.

```
// Core production formula
annual_production_kWh =
  system_kWp
  × irradiation_kWh_m2_year   // from PVGIS, fetched live
  × performance_ratio          // 0.80
  × orientation_factor
  × tilt_factor
  × shadow_factor
```

```
// Financial calculations (use named constants, easy to update)
ELECTRICITY_PRICE_EUR_KWH = 0.12        // Croatian avg residential tariff
SELF_CONSUMPTION_RATIO = 0.70           // 70% of production used on-site
GRID_EXPORT_TARIFF = 0.06               // feed-in / net metering value €/kWh

annual_savings_EUR =
  (annual_production_kWh × SELF_CONSUMPTION_RATIO × ELECTRICITY_PRICE_EUR_KWH)
  + (annual_production_kWh × (1 - SELF_CONSUMPTION_RATIO) × GRID_EXPORT_TARIFF)

// Investment cost (shown ONLY in PDF after contact details submitted)
BASE_COST_EUR_PER_KWP = 1200            // placeholder — update with real pricing
battery_cost = selected_battery_kWh × 400  // €400/kWh placeholder
ev_charger_cost = 800                   // placeholder if EV charger selected

total_investment_EUR =
  system_kWp × BASE_COST_EUR_PER_KWP × roof_type_factor
  + battery_cost
  + ev_charger_cost

// ROI
simple_payback_years = total_investment_EUR / annual_savings_EUR
roi_25_years_EUR = (annual_savings_EUR × 25) - total_investment_EUR
```

### 6.4 Live Summary Panel (right sidebar)

Update in real time as user interacts:
- ☀️ Godišnja proizvodnja: `X,XXX kWh`
- 💰 Godišnja ušteda: `X,XXX €` *(visible immediately)*
- 📊 Povrat investicije: `XX godina` *(visible immediately)*
- 🔋 Sustav: `X kWp` + selected add-ons badges
- 🔒 **Investicija: Prijavite se za prikaz** *(locked until contact details entered)*

### 6.5 Results Screen

After Step 10 (contact details submitted), show a full results screen **before** generating the PDF:

- Large production number: `X,XXX kWh/godina`
- Savings chart: bar chart showing yearly savings over 25 years (using recharts)
- Key metrics grid: Sustav | Godišnja ušteda | Povrat investicije | Ukupna ušteda (25 god.)
- Add-ons summary (battery, EV charger if selected)
- **Total investment amount now revealed** (was locked before)
- Button: **"Preuzmite PDF ponudu"** + note that PDF was sent to their email

### 6.6 PDF Generation

Generate a professional PDF quote with the following pages:

**Page 1 — Naslovna stranica**
- Larsoon Energy logo and brand header
- Title: "Informativna ponuda"
- Customer name, date, quote reference number (auto-generated: `LE-YYYYMMDD-XXXX`)

**Page 2 — Konfiguracija sustava**
- Summary table of all inputs (location, system size, roof type, orientation, tilt, shading, connection type, add-ons)
- Map thumbnail or location coordinates

**Page 3 — Analiza proizvodnje i financije**
- Annual production: `X,XXX kWh`
- Annual savings: `X,XXX €`
- Simple payback period: `XX godina`
- 25-year total savings: `XX,XXX €`
- Bar chart (simplified for PDF): production per month (use PVGIS monthly data if available, otherwise distribute annual proportionally using Croatian monthly solar profile)
- **Investment breakdown table:**
  - Osnovna instalacija (X kWp × Y €/kWp)
  - Baterijski sustav (if applicable)
  - EV punjač (if applicable)
  - **Ukupna investicija: XX,XXX €**

**Page 4 — O nama & Sljedeći koraci**
- Brief company description
- Next steps: "Naš tim će Vas kontaktirati unutar 24h za dogovor tehničkog pregleda"
- Contact info, logo, footer

**PDF Delivery:**
- Send via Resend API to customer's email immediately on form submission
- Also offer a "Preuzmi PDF" download button on the results screen
- Store no data to any database — email is the only persistence mechanism

### 6.7 Email to Customer (sent via Resend)
- Subject: `Vaša informativna ponuda — Larsoon Energy`
- Body: branded HTML email summarising key figures, with attached PDF
- Reply-to: info@larsoonenergy.hr (placeholder — update with real address)

### 6.8 Internal Notification Email (sent via Resend)
- On every quote submission, send a plain-text internal notification email to the company
- Content: customer name, phone, email, system size, location, add-ons, timestamp
- To: same reply-to address (internal inbox)

## 7. Section D — AI Chat Agent (global widget)

### 7.1 Overview

A floating chat bubble fixed to the bottom-right corner of every page. When clicked, it opens a chat panel where visitors can ask questions about solar energy, the quote process, Larsoon Energy's services, or Croatian regulations — and receive instant, helpful AI-powered answers.

This is **not** a generic chatbot. The AI has a detailed system prompt that makes it behave as a knowledgeable Larsoon Energy consultant.

### 7.2 UI / UX

- **Trigger:** Floating circular button, bottom-right, orange background, chat icon. Shows a pulsing notification dot on first load to attract attention.
- **Panel:** Slides up from the bottom-right (mobile: full-screen overlay). Header: "Larsoon AI Asistent" + close button. Chat messages in a scrollable area. Input field at bottom with send button.
- **Appearance:** Consistent with the brand — orange accents, white background, Inter font. Mobile: full-screen takeover with back arrow. Desktop: 380×520px panel.
- **Messages:** User messages right-aligned (orange bubble). AI messages left-aligned (white card with subtle shadow). Timestamps optional.
- **Streaming:** AI responses stream token by token so the user sees text appearing in real time — do not wait for the full response before rendering.
- **Loading state:** Animated typing indicator (three dots) shown while the first tokens arrive.

### 7.3 AI System Prompt

The API route must inject the following system prompt (Croatian):

```
Ti si AI asistent tvrtke Larsoon Energy d.o.o., stručnjak za fotonaponske solarne sustave u Hrvatskoj.

Tvoja uloga:
- Odgovarati na pitanja o solarnim elektranama, baterijskim sustavima i EV punjačima
- Educirati korisnike o tehničkim, financijskim i pravnim aspektima solarne energije u Hrvatskoj
- Pomoći korisnicima razumjeti proces nabave i ugradnje sustava
- Usmjeravati zainteresirane korisnike prema besplatnoj informativnoj ponudi na /quote

Pravila ponašanja:
- Uvijek odgovaraj na hrvatskom jeziku
- Budi stručan, prijateljski i koncizan
- Nikada ne izmišljaj konkretne cijene — uputi korisnika na kalkulator ili ponudu
- Nikada ne obećavaj konkretne rokove isporuke — uputi na kontakt tim
- Za kompleksna tehnička pitanja predloži tehnički pregled objekta
- Ako ne znaš odgovor, reci to iskreno i predloži kontakt s timom

Tvrtka Larsoon Energy d.o.o.:
- Usluge: projektiranje, instalacija, aktivno praćenje, proaktivno održavanje i čišćenje panela
- Rješenja: on-grid i off-grid elektrane, baterijski sustavi, EV punjači
- Tržište: Hrvatska
- Kontakt: info@larsoonenergy.hr
```

### 7.4 API Route

`POST /api/chat` — accepts `{ messages: ChatMessage[] }`, returns a **streaming** response using the Vercel AI SDK (`ai` package) with Anthropic provider.

```typescript
// Simplified structure
import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'

export async function POST(req: Request) {
  const { messages } = await req.json()
  const result = streamText({
    model: anthropic('claude-sonnet-4-5'),
    system: SYSTEM_PROMPT,
    messages,
    maxTokens: 800,
  })
  return result.toDataStreamResponse()
}
```

Use the **Vercel AI SDK** (`npm install ai @ai-sdk/anthropic`) — it handles streaming, message history, and the React `useChat` hook on the frontend.

### 7.5 Frontend Integration

Use the `useChat` hook from the Vercel AI SDK in the chat panel component:

```typescript
import { useChat } from 'ai/react'
const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
  api: '/api/chat',
  initialMessages: [{
    id: 'welcome',
    role: 'assistant',
    content: 'Dobar dan! Ja sam Larsoon AI asistent. Kako Vam mogu pomoći s pitanjima o solarnoj energiji?'
  }]
})
```

### 7.6 New Environment Variable

```
ANTHROPIC_API_KEY=    # Anthropic API key for the AI chat agent
```

---

Create a `.env.local` file with these required variables:

```
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=       # Google Maps JavaScript API key
RESEND_API_KEY=                         # Resend API key (server-side only)
RESEND_FROM_EMAIL=ponude@larsoonenergy.hr
RESEND_INTERNAL_EMAIL=info@larsoonenergy.hr
ANTHROPIC_API_KEY=                      # Anthropic API key for AI chat agent
```

All `NEXT_PUBLIC_` vars are safe to expose client-side. Never expose `RESEND_API_KEY` or `ANTHROPIC_API_KEY`.

---

## 9. API Routes

| Route | Method | Purpose |
|---|---|---|
| `/api/pvgis` | GET | Server-side proxy for PVGIS API (avoids CORS); params: `lat`, `lon` → returns `H_year` and monthly breakdown |
| `/api/quote/send` | POST | Receives quote data + contact info, generates PDF, sends both emails via Resend |
| `/api/contact` | POST | Receives landing page contact form, sends notification email |
| `/api/chat` | POST | Streaming AI chat endpoint using Vercel AI SDK + Anthropic |

---

## 10. File Structure

```
/
├── app/
│   ├── page.tsx                  # Landing page
│   ├── wiki/
│   │   ├── page.tsx              # Wiki index
│   │   └── [slug]/page.tsx       # Article page
│   ├── quote/
│   │   └── page.tsx              # Quote wizard
│   └── api/
│       ├── pvgis/route.ts
│       ├── quote/send/route.ts
│       ├── contact/route.ts
│       └── chat/route.ts         # AI chat streaming endpoint
├── components/
│   ├── landing/                  # Landing page sections
│   │   ├── Hero.tsx
│   │   ├── Solutions.tsx
│   │   ├── Services.tsx
│   │   ├── SavingsCalculator.tsx # Dynamic landing page calculator widget
│   │   ├── HowItWorks.tsx
│   │   ├── WikiTeaser.tsx
│   │   └── ContactForm.tsx
│   ├── wiki/                     # Wiki components
│   ├── quote/                    # Quote wizard steps + summary panel
│   ├── chat/
│   │   ├── ChatWidget.tsx        # Floating chat bubble + panel (global)
│   │   └── ChatMessage.tsx       # Individual message bubble
│   └── ui/                       # shadcn/ui + custom shared components
├── lib/
│   ├── calculations.ts           # All calculation logic (pure functions) — see calculation-rules.md
│   ├── constants.ts              # All pricing, tariff, and ratio constants — single source of truth
│   ├── pdf.tsx                   # PDF template using @react-pdf/renderer
│   └── email.ts                  # Resend email templates
├── content/
│   └── wiki/                     # MDX article files
│       ├── kako-rade-solarni-paneli.mdx
│       ├── on-grid-vs-off-grid.mdx
│       ├── dozvole-i-zakonodavstvo.mdx
│       ├── mrežni-priključak-hrvatska.mdx
│       ├── roi-i-povrat-investicije.mdx
│       ├── eu-fondovi-i-poticaji.mdx
│       ├── čišćenje-i-održavanje-panela.mdx
│       └── monitoring-sustava.mdx
└── public/
    └── images/
        ├── logo.svg              # Placeholder — swap when brand guidelines are ready
        └── logo-white.svg        # Placeholder — white version for dark backgrounds
```

---

## 11. Key Implementation Notes

1. **Mobile-first, always.** Every component must be designed for a 375px screen first, then enhanced for larger viewports. Use Tailwind's `sm:`, `md:`, `lg:` prefixes in that direction. Touch targets minimum 44px. No hover-only interactions.

2. **Calculation logic lives in `calculations.ts` only.** The landing page calculator, the quote wizard, and the PDF all import from the same pure functions. There must be no duplicated calculation code anywhere else in the project. See `calculation-rules.md` for the full specification.

3. **`constants.ts` is the post-launch configuration file — design it for non-developers.** All values that the business owner may need to adjust after go-live must live here and nowhere else: pricing (€/kWp, battery €/kWh, EV charger cost), electricity tariff, export tariff, self-consumption ratio, performance ratio, default irradiation, system lifetime, roof type factors, orientation factors, tilt breakpoints, shading factors, battery capacity options, connection type limits. The file must be structured as follows:

   - **Group constants into clearly labelled sections** using comment banners (e.g. `// ─── PRICING ──────────────────────────────────────────`).
   - **Every constant must have an inline comment** explaining what it is, what unit it is in, and when/why a business owner would change it. Example:
     ```typescript
     // Cost per installed kilowatt-peak (kWp), in EUR.
     // Update this when your supplier pricing changes.
     // Typical Croatian market range: 900–1500 €/kWp.
     export const BASE_COST_EUR_PER_KWP = 1200
     ```
   - **No derived values in `constants.ts`** — only raw scalars and simple lookup objects. All formulas stay in `calculations.ts`.
   - **`calculations.ts` imports exclusively from `constants.ts`** for any numeric value. There must be no numeric literals in `calculations.ts` itself (except array indices and loop counters).
   - The top of `constants.ts` must include this comment block:
     ```typescript
     /**
      * LARSOON ENERGY — BUSINESS CONFIGURATION
      * =========================================
      * This file controls all pricing, tariffs, and calculation parameters.
      * It is safe to edit after deployment — no other files need to change.
      *
      * HOW TO UPDATE AFTER GO-LIVE:
      * 1. Open this file in your code editor or on GitHub.
      * 2. Find the constant you want to change (use the section headings).
      * 3. Update the value.
      * 4. Commit and push — Vercel will auto-deploy within ~1 minute.
      * 5. Log the change in calculation-rules.md (Change Log section).
      *
      * DO NOT change variable names — only values.
      * DO NOT move constants to other files.
      */
     ```

4. **PVGIS integration is critical.** The production calculation must use real irradiation data for the selected location — this is what differentiates the quote from a generic estimate. The landing page calculator uses the national average constant; the Quote Wizard fetches real data per location from PVGIS.

5. **The investment cost is the main conversion driver.** It must be hidden until the customer provides contact details. Do not render it at all until the contact form is submitted — do not hide it via CSS opacity/visibility only.

6. **PDF must be generated server-side** (in the API route) to avoid exposing pricing logic on the client and to ensure consistent rendering regardless of browser.

7. **The quote wizard must work without page reloads.** Use React state for all step transitions. Use `useReducer` or Zustand for the wizard state if it becomes complex.

8. **AI chat is non-blocking.** The ChatWidget loads lazily (dynamic import with `next/dynamic`). If the Anthropic API is unavailable, the chat panel shows a graceful error: *"Asistent trenutno nije dostupan. Kontaktirajte nas na info@larsoonenergy.hr."*

9. **Croatian language throughout.** All user-facing strings, form labels, validation messages, error states, loading states, email content, and AI chat responses must be in Croatian. No English fallbacks visible to end users.

10. **Do not store PII anywhere except the Resend email logs.** There is no database in v1. If a future CRM integration is needed, it will be added later.

---

## 12. Stretch Goals (v1.1 — do not implement in v1)

- Admin dashboard to view submitted quotes
- CRM integration (HubSpot / Pipedrive)
- Bilingual HR/EN toggle
- GDPR cookie consent banner
- Blog section separate from Wiki
- Customer portal (portal for monitoring data)
- AI chat conversation history persistence

