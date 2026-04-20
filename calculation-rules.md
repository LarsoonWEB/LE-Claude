# Larsoon Energy — Calculation Rules & Logic Specification

> **This file is the single source of truth for all calculation logic.**
> The `lib/calculations.ts` and `lib/constants.ts` files must implement exactly what is defined here.
> Any change to pricing, factors, or formulas must be made here first, then reflected in code.

---

## 1. Constants (`lib/constants.ts`)

> **This file is the post-launch configuration interface for the business owner.**
> It must be structured so that a non-developer can find and update any value by reading the comments alone, without understanding the surrounding code.

### Structural rules for `constants.ts`

1. **File header** — include this block at the very top:

```typescript
/**
 * LARSOON ENERGY — BUSINESS CONFIGURATION
 * =========================================
 * This file controls all pricing, tariffs, and calculation parameters.
 * It is safe to edit after deployment — no other files need to change.
 *
 * HOW TO UPDATE AFTER GO-LIVE:
 * 1. Open this file in your code editor or on GitHub.
 * 2. Find the constant you want to change (use the section headings below).
 * 3. Update the numeric value only — do NOT rename the constant.
 * 4. Commit and push — Vercel will auto-deploy within ~1 minute.
 * 5. Log the change in calculation-rules.md (Change Log section at the bottom).
 *
 * UNITS: all monetary values in EUR (€), all energy in kWh, all power in kWp.
 */
```

2. **Section banners** — separate logical groups with a visible divider:

```typescript
// ─── SOLAR SYSTEM ──────────────────────────────────────────────────────────
```

3. **Every constant gets an inline comment** with: what it is, its unit, when a business owner would change it, and (where relevant) the typical market range.

4. **No derived values or formulas** — only raw numbers and simple lookup objects. All arithmetic stays in `calculations.ts`.

5. **`calculations.ts` must import every numeric value from this file** — no numeric literals in `calculations.ts` except array indices and loop counters.

---

All values below must be defined as named, exported constants. No magic numbers anywhere in the codebase.

### 1.1 Solar System

```typescript
// ─── SOLAR SYSTEM ──────────────────────────────────────────────────────────

// National average solar irradiation for Croatia, in kWh per m² per year.
// Used ONLY by the landing page quick calculator (no location input there).
// The Quote Wizard fetches the real value per location from PVGIS — do not change this for that.
// Source: PVGIS average for Croatia. Typical range: 1,300–1,600 kWh/m²/year.
export const DEFAULT_IRRADIATION_KWH_M2_YEAR = 1450

// System performance ratio (dimensionless, 0–1).
// Accounts for all real-world losses: inverter efficiency, wiring resistance,
// soiling, temperature derating, and mismatch losses.
// Industry standard for Croatia: 0.75–0.85. Do not change without engineering review.
export const PERFORMANCE_RATIO = 0.80
```

### 1.2 Financial

```typescript
// ─── ELECTRICITY TARIFFS ────────────────────────────────────────────────────

// Average Croatian residential retail electricity price, in EUR per kWh.
// Used to calculate how much the customer saves by consuming their own solar energy.
// Update when HEP or the regulator changes the tariff. Check: https://www.hep.hr/
// Current HEP tariff (2024): ~0.12 €/kWh (blended, including grid fees and taxes).
export const ELECTRICITY_PRICE_EUR_KWH = 0.12

// Value of electricity exported to the grid (net metering), in EUR per kWh.
// In Croatia, surplus energy is settled at a lower rate than retail.
// Update when the Energy Regulatory Agency (HERA) changes the settlement tariff.
// Current rate: ~0.06 €/kWh. Check: https://www.hera.hr/
export const GRID_EXPORT_TARIFF_EUR_KWH = 0.06

// ─── CONSUMPTION ASSUMPTIONS ────────────────────────────────────────────────

// Fraction of annual solar production consumed directly on-site (dimensionless, 0–1).
// The remainder (1 - this) is exported to the grid at the lower export tariff.
// 0.70 is a reasonable default for a household that is home during the day.
// Increases with battery storage or EV charging. Rarely exceeds 0.85 without a battery.
export const SELF_CONSUMPTION_RATIO = 0.70

// ─── SYSTEM LIFETIME ────────────────────────────────────────────────────────

// Number of years used for lifetime ROI and savings projections.
// Standard warranty period for tier-1 solar panels. Safe to use 25–30 years.
export const SYSTEM_LIFETIME_YEARS = 25
```

### 1.3 Investment Pricing (placeholders — update with real pricing before go-live)

```typescript
// ─── PRICING ────────────────────────────────────────────────────────────────

// Base installation cost per kWp, in EUR.
// Covers panels, inverter, mounting, cabling, and standard installation labour.
// Does NOT include battery, EV charger, permit fees, or grid connection costs.
// ⚠️  PLACEHOLDER — update with your actual price list before launch.
// Typical Croatian market range (2024): 900–1,500 €/kWp depending on system size.
export const BASE_COST_EUR_PER_KWP = 1200

// Battery storage cost per kWh of usable capacity, in EUR.
// Covers the battery unit and installation. Excludes inverter if already included above.
// ⚠️  PLACEHOLDER — update with your actual battery price list before launch.
// Typical range: 350–600 €/kWh depending on brand and capacity.
export const BATTERY_COST_EUR_PER_KWH = 400

// Fixed cost for a single EV charger unit, including installation, in EUR.
// ⚠️  PLACEHOLDER — update with your actual EV charger price before launch.
export const EV_CHARGER_COST_EUR = 1500

// ─── EV LOAD ASSUMPTION ─────────────────────────────────────────────────────

// Additional annual electricity consumption assumed for one electric vehicle, in kWh/year.
// Used to increase the system size recommendation when the customer selects EV charging.
// Average EV in Croatia drives ~15,000 km/year at ~15 kWh/100 km = 2,250 kWh.
// Rounded to 2,000 kWh to stay conservative.
export const EV_ANNUAL_KWH = 2000
```

### 1.4 Roof Type Cost Factors

```typescript
// ─── ROOF TYPE COST FACTORS ─────────────────────────────────────────────────
// Multipliers applied to BASE_COST_EUR_PER_KWP to reflect mounting difficulty.
// Values are relative to tile roof (reference = 1.00).
// Update these if your installer network revises labour surcharges by roof type.

export const ROOF_TYPE_FACTORS: Record<RoofType, number> = {
  flat:     1.05,   // Ravni krov — requires ballast frames or tilt-up mounting (+5%)
  tile:     1.00,   // Crijep — standard hook mounting, reference price
  metal:    0.95,   // Lim/trapezni — easiest to clamp, no drilling needed (-5%)
  shingle:  1.10,   // Šindra — requires extra care and specialised fasteners (+10%)
}
```

### 1.5 Orientation Factors

```typescript
// ─── ORIENTATION FACTORS ────────────────────────────────────────────────────
// Multipliers applied to annual production based on roof direction.
// South (J) is optimal for Croatia (~45°N latitude) = reference value 1.00.
// Values sourced from PVGIS data for 35° tilt across Croatia.
// Do not change without new irradiation modelling data.

export const ORIENTATION_FACTORS: Record<Orientation, number> = {
  S:  0.70,   // Sjever (North) — worst case; roof faces away from sun most of day
  SZ: 0.80,   // Sjeverozapad (North-West)
  SI: 0.80,   // Sjeveroistok (North-East)
  Z:  0.90,   // Zapad (West) — good for afternoon peak production
  I:  0.90,   // Istok (East) — good for morning peak production
  JZ: 0.97,   // Jugozapad (South-West) — near-optimal
  JI: 0.97,   // Jugoistok (South-East) — near-optimal
  J:  1.00,   // Jug (South) — optimal, reference value
}
```

### 1.6 Tilt Factors

```typescript
// ─── TILT FACTORS ───────────────────────────────────────────────────────────
// Breakpoints for linear interpolation of tilt efficiency.
// Optimal tilt for Croatia (~45°N) is ~35°. Values drop toward 0° (flat) and 90° (vertical).
// Do not change without new irradiation modelling data.
// Input range: 0–90°. Default shown to user: 35°.

export const TILT_FACTOR_BREAKPOINTS: Array<{ angle: number; factor: number }> = [
  { angle: 0,  factor: 0.85 },   // Flat — rain does not clean panels, diffuse light only
  { angle: 15, factor: 0.93 },
  { angle: 30, factor: 0.99 },
  { angle: 35, factor: 1.00 },   // ← Optimal for Croatia
  { angle: 45, factor: 0.98 },
  { angle: 60, factor: 0.91 },
  { angle: 90, factor: 0.70 },   // Vertical — facade mounting, very low yield
]
```

### 1.7 Shading Factors

```typescript
// ─── SHADING FACTORS ────────────────────────────────────────────────────────
// Multipliers applied to annual production based on shading level.
// Shading has a disproportionate effect on string inverter systems.
// Values assume standard string inverter (no optimisers or microinverters).
// Update if your standard installation includes power optimisers (losses are lower).

export const SHADING_FACTORS = {
  none:     1.00,   // Nije zasjenjen — clear sky view all day
  light:    0.92,   // Slabo zasjenjen — minor obstruction (e.g. small chimney, antenna)
  heavy:    0.80,   // Jako zasjenjen — significant trees or neighbouring buildings
  extreme:  0.65,   // Vrlo jako zasjenjen — heavily obstructed; system may not be viable
} as const
```

### 1.8 Connection Type Constraints

```typescript
// ─── CONNECTION TYPE CONSTRAINTS ────────────────────────────────────────────
// Maximum allowed system size per connection type, in kWp.
// Limits set by Croatian distribution grid regulations (HEP ODS).
// Do not change without verifying current HERA/HEP ODS rules.

export const CONNECTION_MAX_KWP = {
  three_phase:  30,         // Trofazni (3f) — max 30 kWp for standard residential connection
  single_phase: 3.68,       // Jednofazni (1f) — limited by single-phase inverter max (16A × 230V)
  off_grid:     Infinity,   // Off-grid — no grid limit; battery is required
}

// ─── BATTERY OPTIONS ────────────────────────────────────────────────────────
// Available battery capacity tiers shown to the user, in kWh.
// Add or remove values to match your product portfolio.
// 0 = no battery (only valid for on-grid systems).
export const BATTERY_CAPACITY_OPTIONS_KWH = [0, 5, 10, 15, 20] as const
export type BatteryCapacityKWh = typeof BATTERY_CAPACITY_OPTIONS_KWH[number]
```

---

## 2. Input Types

```typescript
export type RoofType = 'flat' | 'tile' | 'metal' | 'shingle'
export type Orientation = 'S' | 'SZ' | 'Z' | 'JZ' | 'J' | 'JI' | 'I' | 'SI'
export type ShadingLevel = 'none' | 'light' | 'heavy' | 'extreme'
export type ConnectionType = 'three_phase' | 'single_phase' | 'off_grid'
export type BatteryCapacityKWh = 0 | 5 | 10 | 15 | 20

export interface CalculatorInputs {
  systemKWp: number              // Selected or recommended system size
  irradiationKWhM2Year: number   // From PVGIS (Quote Wizard) or DEFAULT_IRRADIATION (landing calculator)
  roofType: RoofType
  orientation: Orientation
  tiltDegrees: number            // 0–90
  shadingLevel: ShadingLevel
  connectionType: ConnectionType
  batteryKWh: BatteryCapacityKWh
  includeEvCharger: boolean
}
```

---

## 3. Core Calculation Functions (`lib/calculations.ts`)

All functions must be **pure** (no side effects, no API calls, no imports from React).

### 3.1 Tilt Factor Interpolation

```typescript
function getTiltFactor(degrees: number): number
```

Linear interpolation between the breakpoints in `TILT_FACTOR_BREAKPOINTS`.
- Clamp input to [0, 90].
- Find the two surrounding breakpoints and interpolate linearly.

**Example:** `getTiltFactor(35)` → `1.00`, `getTiltFactor(40)` → `0.99` (midpoint between 35→1.00 and 45→0.98)

---

### 3.2 Recommended System Size (from consumption)

```typescript
function recommendSystemKWp(
  annualConsumptionKWh: number,
  irradiationKWhM2Year: number,
  includeEv: boolean
): number
```

**Formula:**

```
adjusted_consumption = annualConsumptionKWh + (includeEv ? EV_ANNUAL_KWH : 0)
recommended_kWp = adjusted_consumption / (irradiationKWhM2Year × PERFORMANCE_RATIO)
```

Round result to nearest 0.5 kWp. Minimum 1 kWp.

---

### 3.3 Monthly Bill to Annual kWh Conversion

```typescript
function monthlyBillToAnnualKWh(monthlyBillEUR: number): number
```

**Formula:**

```
annual_kWh = (monthlyBillEUR / ELECTRICITY_PRICE_EUR_KWH) × 12
```

---

### 3.4 Annual Production

```typescript
function calculateAnnualProduction(inputs: CalculatorInputs): number
```

**Formula:**

```
annual_production_kWh =
  inputs.systemKWp
  × inputs.irradiationKWhM2Year
  × PERFORMANCE_RATIO
  × ORIENTATION_FACTORS[inputs.orientation]
  × getTiltFactor(inputs.tiltDegrees)
  × SHADING_FACTORS[inputs.shadingLevel]
```

Return rounded to nearest integer kWh.

---

### 3.5 Annual Savings

```typescript
function calculateAnnualSavings(annualProductionKWh: number): number
```

**Formula:**

```
self_consumed_kWh = annualProductionKWh × SELF_CONSUMPTION_RATIO
exported_kWh      = annualProductionKWh × (1 - SELF_CONSUMPTION_RATIO)

annual_savings_EUR =
  (self_consumed_kWh × ELECTRICITY_PRICE_EUR_KWH)
  + (exported_kWh × GRID_EXPORT_TARIFF_EUR_KWH)
```

Return rounded to nearest integer EUR.

---

### 3.6 Total Investment

```typescript
function calculateTotalInvestment(inputs: CalculatorInputs): number
```

**Formula:**

```
base_installation = inputs.systemKWp × BASE_COST_EUR_PER_KWP × ROOF_TYPE_FACTORS[inputs.roofType]
battery_cost      = inputs.batteryKWh × BATTERY_COST_EUR_PER_KWH
ev_cost           = inputs.includeEvCharger ? EV_CHARGER_COST_EUR : 0

total_investment_EUR = base_installation + battery_cost + ev_cost
```

Return rounded to nearest integer EUR.

> ⚠️ **Security note:** This value must NEVER be sent to or computed on the client before contact details are submitted. It is computed server-side in the `/api/quote/send` route for inclusion in the PDF. The result panel on the client may receive it only after the API returns successfully.

---

### 3.7 Simple Payback Period

```typescript
function calculatePaybackYears(totalInvestmentEUR: number, annualSavingsEUR: number): number
```

**Formula:**

```
payback_years = totalInvestmentEUR / annualSavingsEUR
```

Return rounded to one decimal place.

---

### 3.8 Lifetime Return on Investment

```typescript
function calculateLifetimeROI(annualSavingsEUR: number, totalInvestmentEUR: number): number
```

**Formula:**

```
lifetime_savings = annualSavingsEUR × SYSTEM_LIFETIME_YEARS
roi_EUR = lifetime_savings - totalInvestmentEUR
```

Return rounded to nearest integer EUR. This is the "net gain" over the system lifetime after subtracting investment.

---

### 3.9 Monthly Production Profile

```typescript
function getMonthlyProductionProfile(annualProductionKWh: number, pvgisMonthly?: number[]): number[]
```

Returns an array of 12 values (Jan–Dec) summing to `annualProductionKWh`.

- **If `pvgisMonthly` is provided** (from PVGIS API monthly breakdown): scale those values proportionally to match the calculated `annualProductionKWh`.
- **If not provided** (landing page calculator): distribute using the Croatian solar profile weights below.

**Croatian monthly distribution weights (approximate, based on latitude ~45°N):**

```typescript
const CROATIAN_MONTHLY_WEIGHTS = [
  0.047,  // Jan
  0.063,  // Feb
  0.085,  // Mar
  0.095,  // Apr
  0.107,  // May
  0.115,  // Jun
  0.120,  // Jul  (peak)
  0.113,  // Aug
  0.090,  // Sep
  0.068,  // Oct
  0.048,  // Nov
  0.049,  // Dec
]
// Sum = 1.000
```

---

### 3.10 Full Quote Result (composite)

```typescript
interface QuoteResult {
  annualProductionKWh: number
  annualSavingsEUR: number
  totalInvestmentEUR: number      // only populated server-side
  paybackYears: number            // only populated server-side
  lifetimeROI_EUR: number         // only populated server-side
  monthlyProductionKWh: number[]  // 12 values
}

function calculateQuoteResult(inputs: CalculatorInputs): Omit<QuoteResult, 'totalInvestmentEUR' | 'paybackYears' | 'lifetimeROI_EUR'>
```

The client-side result **excludes** investment figures. The server-side API route calls `calculateTotalInvestment`, `calculatePaybackYears`, and `calculateLifetimeROI` additionally before building the PDF.

---

## 4. Landing Page Calculator Rules

The landing page calculator is a **simplified** version that:

- Uses `DEFAULT_IRRADIATION_KWH_M2_YEAR` (not PVGIS — no location input)
- Accepts only: `systemKWp` (slider), `batteryKWh` (slider), `includeEvCharger` (checkbox)
- Assumes: South orientation (`J`), 35° tilt, no shading, tile roof (all at optimal/reference values)
- **Shows investment cost immediately** — this is a teaser, not the full quote
- Uses the same `calculateAnnualProduction`, `calculateAnnualSavings`, `calculateTotalInvestment` functions from `calculations.ts`

**Displayed metrics:**

| Metric | Function called | Displayed as |
|---|---|---|
| Početna Investicija | `calculateTotalInvestment` | `€X,XXX` |
| Godišnja Proizvodnja | `calculateAnnualProduction` | `X,XXX kWh` |
| Godišnja Ušteda | `calculateAnnualSavings` | `€X,XXX` |
| Razdoblje Otplate | `calculatePaybackYears` | `X.X god` |
| Cijena Punjača | `EV_CHARGER_COST_EUR` constant | `€1,500` or `—` |

---

## 5. Quote Wizard Calculator Rules

The Quote Wizard uses all inputs and fetches real irradiation from PVGIS:

### 5.1 PVGIS API Call

When location is selected, call (via the `/api/pvgis` proxy to avoid CORS):

```
GET https://re.jrc.ec.europa.eu/api/v5_3/PVcalc
  ?lat={lat}
  &lon={lon}
  &peakpower=1
  &loss=20
  &outputformat=json
  &browser=0
```

Extract from response:
- `outputs.totals.fixed.H(h)_m` → annual irradiation in kWh/m²/year (use as `irradiationKWhM2Year`)
- `outputs.monthly.fixed` → array of 12 monthly values → use as `pvgisMonthly` in `getMonthlyProductionProfile`

Cache this value in wizard state — do not re-fetch unless location changes.

### 5.2 System Size Recommendation (Mode B)

If the user enters **monthly bill in €**:
1. Call `monthlyBillToAnnualKWh(monthlyBillEUR)` to get `annualConsumptionKWh`
2. Call `recommendSystemKWp(annualConsumptionKWh, irradiationKWhM2Year, includeEv)`
3. Display recommended kWp and pre-fill the system size slider

If the user enters **annual consumption in kWh** directly:
1. Call `recommendSystemKWp(annualConsumptionKWh, irradiationKWhM2Year, includeEv)` directly

### 5.3 Connection Type Validation

After system size is set (manually or recommended), validate against connection type limit:

```typescript
const maxKWp = CONNECTION_MAX_KWP[inputs.connectionType]
if (inputs.systemKWp > maxKWp) {
  // Show warning: "Jednofazni priključak omogućuje maksimalno 3.68 kWp. Razmotrite trofazni priključak."
  // Do NOT auto-correct silently — warn the user and let them decide
}
```

### 5.4 Off-Grid Battery Requirement

If `connectionType === 'off_grid'` and `batteryKWh === 0`:
- Automatically set battery selector to minimum (5 kWh)
- Show info message: *"Off-grid sustav zahtijeva baterijski sustav. Automatski odabran minimalni kapacitet."*

---

## 6. Validation Rules

### System size
- Min: 1 kWp
- Max: 100 kWp (residential + industrial combined limit for this tool)
- Step: 0.5 kWp

### Tilt
- Min: 0°, Max: 90°, Default: 35°

### Battery capacity
- Options: 0, 5, 10, 15, 20 kWh
- 0 = no battery (except off-grid, see above)

### Monthly bill input
- Min: €1, Max: €10,000
- Must produce a positive `annual_kWh` and a `recommended_kWp >= 1`

### Annual consumption input
- Min: 100 kWh, Max: 1,000,000 kWh

---

## 7. Rounding & Display Rules

| Value | Rounding | Display format |
|---|---|---|
| System size (kWp) | 1 decimal | `5.0 kWp` |
| Annual production (kWh) | Nearest 10 | `6,500 kWh` |
| Annual savings (€) | Nearest 10 | `€975` |
| Investment (€) | Nearest 100 | `€7,500` |
| Payback period (years) | 1 decimal | `7.7 god` |
| Lifetime ROI (€) | Nearest 100 | `€18,500` |
| Monthly production (kWh) | Nearest integer | `540 kWh` |

Use Croatian locale number formatting: period as thousands separator, comma as decimal: `7.500,00 €`
In code: `value.toLocaleString('hr-HR')`.

---

## 8. Change Log

| Date | Change | Author |
|---|---|---|
| 2025-04-20 | Initial specification created | Project setup |

> When any constant or formula is changed, add a row here with the date, description, and reason.
