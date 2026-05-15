# VP Weekly Report — Skill Context Document

> This document captures the requirements, structure, and design decisions for the VP Weekly Performance Report. It serves as the source of truth for building the reporting skill.

---

## 1. Purpose & Audience

| Field | Value |
|---|---|
| **Report type** | Weekly performance review |
| **Audience** | All major VPs — Product, Revenue/Sales, Finance, Customer Experience |
| **Delivery** | Self-serve dashboard (VPs pull it themselves, no push cadence) |
| **Refresh cadence** | Weekly — data refreshes every Monday covering the prior week |

---

## 2. Report Structure

**Two-layer layout:**

1. **Shared Executive Summary** — top-line metrics every VP sees, regardless of function
2. **VP-Specific Sections** — each VP gets a drill-down relevant to their domain

This means the report must be scannable: a VP should be able to read only the top section and their section and have everything they need.

---

## 3. Top-Line Metrics (Shared — All VPs)

These appear at the top of every report, visible to all:

| Metric | Why it's top-line |
|---|---|
| **Total Revenue** | Business health signal. Primary output metric. |
| **Total Visitors** | Demand signal. Separates volume from efficiency drivers. |
| **Total Purchases** | Bridge between demand and revenue. Catches funnel collapses. |

**Comparisons always shown:**
- Week-over-Week (WoW)
- Month-over-Month (MoM)

---

## 4. VP-Specific Sections

### Product VP
Focused on funnel health, product quality, and category performance.

| Metric | Purpose |
|---|---|
| Conversion funnel: ATC rate, checkout rate, purchase rate | Stage-by-stage health — identify where drop-offs occur |
| Category performance: Electronics vs Home Goods | Which categories drive or drag overall performance |
| Return rate by category and segment | Product quality signal and post-purchase satisfaction |

### Revenue / Finance VP
Focused on revenue quality, efficiency, and cost signals.

| Metric | Purpose |
|---|---|
| Total revenue + WoW / MoM trend | Top-line attainment and trajectory |
| AOV by channel (Direct vs Email) and segment (New vs Returning) | Revenue quality — are we growing through more orders or better orders? |
| Return rate impact on net revenue | Gross vs net revenue — the true business economics after returns |
| Support ticket volume | Operational cost proxy and early customer friction signal |

### Customer Experience VP *(implied from Finance/CX/Other selection)*
Focused on post-purchase experience and operational health.

| Metric | Purpose |
|---|---|
| Return rate (overall and by category) | Customer satisfaction proxy |
| Support tickets per 100 purchases | Ticket rate normalized to volume |
| New vs Returning customer behavior split | Retention and satisfaction patterns |

---

## 5. Anomaly Detection

**Flagging approach:** Fixed thresholds with a statistical confidence note.

- Trigger a flag when a metric moves beyond a fixed rule (e.g., >10% WoW change)
- Annotate the flag with whether it is statistically significant (e.g., within normal variance vs. a genuine outlier)
- Do **not** auto-adjust numbers — surface the flag and let the VP interpret

**No formal KPI targets defined yet.** Use rolling 4-week historical average as the baseline benchmark for all anomaly detection.

---

## 6. Events Calendar Handling

- The report does **not** auto-adjust numbers for events (promotions, holidays, Black Friday, etc.)
- When a known event falls within the comparison period, display a **contextual banner**: e.g., *"Note: prior week included Black Friday (Nov 29–Dec 1). WoW comparisons reflect this event."*
- The VP applies their own judgment to the adjusted interpretation
- Events calendar must be maintained as a separate input so banners trigger automatically

---

## 7. AI Narrative Rules

The skill generates **partial narrative only** — no full written summaries.

**What the skill DOES generate:**
- Anomaly flags with a one-line explanation (e.g., "Electronics return rate 20.5% on Dec 8 — 2× Sunday average. Timing aligns with Black Friday return window.")
- Statistical confidence annotation on each flag

**What the skill does NOT generate:**
- Executive summary prose
- Strategic recommendations
- Interpretive commentary
- Competitive or market context

VPs form their own narrative from the data and flags.

---

## 8. Data Sources & Dimensions

**Current dataset:** `ecommerce_data (1).db` (SQLite, star schema)

| Dimension | Values |
|---|---|
| Channels | Direct, Email |
| Customer segments | New, Returning |
| Product categories | Electronics, Home Goods |
| Date flags | `is_black_friday_period`, `is_weekend`, `day_of_week` |

**Key computed metrics** (derived from raw fields):
- ATC rate = `add_to_carts / visitors`
- Purchase rate = `purchases / visitors`
- Checkout conversion = `purchases / checkouts`
- Tickets per 100 purchases = `support_tickets / purchases × 100`
- Return rate = `return_rate` field (already a ratio in the data)

---

## 9. Open Questions (To Resolve Before Skill Build)

| # | Question | Priority |
|---|---|---|
| 1 | What is the company name and brand color palette for styling? | High |
| 2 | What are the fixed anomaly thresholds? (e.g., >10% WoW, >15% return rate) | High |
| 3 | What events belong in the calendar? (promotions, holidays beyond BF) | Medium |
| 4 | Does the Finance VP section need gross vs net revenue computed, or just return rate shown separately? | Medium |
| 5 | Who maintains the events calendar — manual input or integrated calendar? | Medium |
| 6 | Should the dashboard support drill-down interaction (click a metric to see breakdown)? | Low |
| 7 | What does the CX VP section look like in detail — same as Finance/CX hybrid or a separate section? | Low |

---

## 10. Skill Design Notes

When this context is converted into a skill:

- **Trigger conditions:** User asks to "generate the VP report," "run the weekly report," "build the performance dashboard," or references any VP-specific metric section
- **Required inputs:** Date range for current week, data source connection
- **Output format:** Self-serve HTML dashboard with shared top section + tabbed or sectioned VP drill-downs
- **Anomaly engine:** Run after data loads, before rendering — flags appear inline with the affected metric
- **Events banner:** Check events calendar against comparison period dates — inject banner if match found
- **Baseline:** Compute 4-week rolling average on first load to establish benchmark for all anomaly comparisons

---

*Document created: May 13, 2026 · Based on context-gathering session for AI Edge Week 2 Bootcamp*
