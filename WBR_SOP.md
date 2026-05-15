# Standard Operating Procedure: Weekly Business Review (WBR) Report Generation

**Document type:** SOP  
**Owner:** Analytics / PM Team  
**Cadence:** Weekly — report generated every Monday covering the prior week  
**Audience:** All major VPs — Product, Revenue/Sales, Finance, Customer Experience  
**Last updated:** May 13, 2026

---

## 1. Purpose

This SOP defines the repeatable process for generating the Weekly Business Review (WBR) dashboard. The WBR is a self-serve, always-available report that gives every VP a consistent, structured view of the business's performance. VPs pull the report when they need it — no push cadence, no meeting dependency.

The report answers three questions for every VP:
1. **Is the business healthy this week?** (Top-line section)
2. **What is happening in my domain?** (VP-specific section)
3. **Is anything unusual happening that I should investigate?** (Anomaly flags)

---

## 2. Scope

| In Scope | Out of Scope |
|---|---|
| Weekly performance metrics from the ecommerce database | Real-time or intraday monitoring |
| WoW and MoM period comparisons | Year-over-year comparisons (requires 12+ months of data) |
| Anomaly flagging with statistical context | Root cause analysis (VP-led, not auto-generated) |
| Events calendar banners for known promotional periods | Automatic comparison adjustments for events |
| VP-specific drill-down sections | Individual customer or transaction-level data |
| Self-serve HTML dashboard output | PDF or slide deck generation |

---

## 3. Data Sources

### Primary Source
- **Database:** `ecommerce_data.db` (SQLite, star schema)
- **Grain:** Daily × Channel × Customer Segment × Product Category
- **Refresh:** Every Monday morning, covering Mon–Sun of the prior week

### Schema Reference

| Table | Role | Key Fields |
|---|---|---|
| `daily_metrics` | Fact table (360 rows) | visitors, page_views, add_to_carts, checkouts, purchases, total_revenue, avg_order_value, return_rate, support_tickets |
| `dates` | Date dimension | date, is_black_friday_period, is_weekend, day_of_week |
| `channels` | Channel dimension | channel_name (Direct, Email) |
| `customer_types` | Segment dimension | customer_type_name (New, Returning) |
| `product_categories` | Category dimension | category_name (Electronics, Home Goods) |

### Derived Metrics (computed at report generation time)

```
ATC Rate              = add_to_carts / visitors
Purchase Rate         = purchases / visitors  
Checkout Conversion   = purchases / checkouts
Tickets per 100 purch = support_tickets / purchases × 100
Net Revenue (est.)    = total_revenue × (1 - return_rate)
```

---

## 4. Report Structure

### 4.1 Shared Executive Summary (all VPs see this)

**Top-line KPIs — always displayed:**

| Metric | Display | Comparisons |
|---|---|---|
| Total Revenue | Dollar value | WoW % change · MoM % change |
| Total Visitors | Count | WoW % change · MoM % change |
| Total Purchases | Count | WoW % change · MoM % change |

**Like-for-Like panel** (Mon–Thu of current week vs same days of prior week, excluding any flagged event days):

| Metric | Display |
|---|---|
| LfL Revenue | $ value + % change |
| LfL Visitors | Count + % change |
| LfL AOV | $ value + % change |

**Events calendar banner** — if the current or prior week contains a flagged event (see Section 7), display:
> ⚠ *Note: [Event Name] occurred on [dates] in the [current/prior] week. WoW comparisons reflect this event. Refer to the Like-for-Like panel for adjusted comparison.*

---

### 4.2 VP Product Section

**Audience:** VP of Product  
**Focus:** Funnel health, product category performance, post-purchase quality

| Metric Group | Metrics | Visualization |
|---|---|---|
| Conversion Funnel | Visitors → ATC Rate → Checkout Rate → Purchase Rate | Funnel bar chart with stage drop-off % |
| Category Performance | Revenue, Purchases, AOV by Electronics vs Home Goods | Side-by-side bar chart |
| Return Rate | Return rate by category × customer segment (New vs Returning) | Heatmap grid |
| Week-on-Week Funnel | Each funnel stage rate: current vs prior week | Line chart (7-day, both weeks overlaid) |

**Anomaly triggers for Product section:** See Section 6.

---

### 4.3 VP Revenue / Finance Section

**Audience:** VP of Revenue, VP of Finance  
**Focus:** Revenue quality, basket economics, operational cost signals

| Metric Group | Metrics | Visualization |
|---|---|---|
| Revenue Trend | Daily revenue: current week vs prior week | Dual-line chart |
| Revenue Decomposition | Total Revenue = Purchases × AOV (both period comparison) | Stacked components |
| AOV Breakdown | AOV by channel (Direct vs Email) and segment (New vs Returning) | 2×2 comparison grid |
| Net Revenue Estimate | Gross revenue − estimated return cost (return_rate × revenue) | KPI card with gross and net |
| Support Ticket Rate | Tickets per 100 purchases, current vs 4-week rolling average | Trend sparkline + badge |

**Anomaly triggers for Revenue section:** See Section 6.

---

### 4.4 VP Customer Experience Section

**Audience:** VP of Customer Experience / Support  
**Focus:** Post-purchase satisfaction, support volume, retention signals

| Metric Group | Metrics | Visualization |
|---|---|---|
| Return Rate | Overall rate + by category + by day of week | Bar chart + day-of-week heatmap |
| Support Volume | Daily ticket count, current week vs prior week | Bar chart |
| Ticket Rate Trend | Tickets per 100 purchases vs 4-week baseline | Trend line with baseline band |
| Segment Behavior | New vs Returning: return rate, AOV, purchase rate | Side-by-side comparison table |

---

## 5. Baseline Computation

Since no formal KPI targets are defined, all anomaly detection uses a **4-week rolling average** as the baseline benchmark.

**Computation rules:**
- Calculate for each metric: `avg(value over the 4 weeks preceding the current reporting week)`
- Exclude weeks that contain flagged events from the baseline calculation
- Recompute baseline every Monday when data refreshes
- Store baseline values alongside the current week values for all anomaly comparisons

---

## 6. Anomaly Detection & Flagging

### 6.1 Flagging Logic

Two conditions must be evaluated for each metric. A flag is raised if **either** triggers:

| Condition | Rule | Example |
|---|---|---|
| **Fixed threshold** | Metric changed >±15% WoW or >±20% MoM | Revenue down 18% WoW → flag |
| **Statistical outlier** | Value is >2 standard deviations from 4-week rolling average | Return rate at 20.5% when avg is 10.9% → flag |

When both conditions are met, the flag shows:  
> `⚠ [Metric] [direction] [magnitude]. This is [X] standard deviations from the 4-week average (baseline: [Y]).`

When only the fixed threshold triggers:  
> `⚠ [Metric] [direction] [magnitude] WoW. Change is within normal statistical variance.`

### 6.2 Flagging Rules

- **No auto-adjustment of numbers** — raw values are always displayed as-is
- **Flags appear inline** with the affected metric, not in a separate alerts panel
- **Maximum 3 flags per VP section** — prioritize by standard deviation magnitude
- **No written narrative or strategic interpretation** — one-line factual description only

### 6.3 Recommended Anomaly Thresholds (to be confirmed by stakeholders)

| Metric | Fixed Threshold |
|---|---|
| Revenue (WoW) | ±15% |
| Visitors (WoW) | ±20% |
| Purchases (WoW) | ±15% |
| ATC Rate | ±2pp (absolute) |
| Checkout Conversion | ±3pp (absolute) |
| Return Rate | ±3pp (absolute) or >15% absolute value |
| Support Tickets per 100 | ±1.0 (absolute) |
| AOV | ±10% |

> **Note:** These thresholds require sign-off from each VP before the skill goes live.

---

## 7. Events Calendar

The events calendar is maintained as a structured list of dates that affect comparability. When any date in the current or prior reporting week matches a calendar entry, the events banner (Section 4.1) is triggered automatically.

### Known Events (seed list)

| Event | Dates | Impact |
|---|---|---|
| Black Friday | Last Friday of November | 3–4× traffic spike, +50% AOV, elevated tickets |
| Cyber Monday | Monday after Black Friday | 2–3× traffic spike |
| Christmas | Dec 24–25 | Traffic drops, high gift returns follow Jan 1–7 |
| New Year | Dec 31–Jan 1 | Low traffic, high return activity |

**Maintenance:** Events calendar is updated manually by the Analytics team before each major promotional period. Format:
```
{ "name": "Black Friday", "dates": ["2024-11-29"], "note": "3-4x traffic. AOV +50%. Compare LfL." }
```

---

## 8. Report Generation Steps

### Step 1: Data validation (automated)
- Confirm current week data is present for all 7 days
- Check row count: should be 360 rows per week (7 days × 2 channels × 2 segments × 2 categories × ... )
- Flag if any dimension combination is missing

### Step 2: Baseline computation (automated)
- Pull prior 4 weeks of data
- Compute rolling average for each metric × segment combination
- Exclude event weeks from baseline

### Step 3: Current week aggregation (automated)
- Aggregate `daily_metrics` to weekly totals and rates
- Compute all derived metrics (ATC rate, checkout conversion, net revenue, ticket rate)
- Split by all dimension combinations for VP sections

### Step 4: Anomaly detection (automated)
- Run fixed threshold check on all tracked metrics
- Run statistical outlier check (z-score vs 4-week rolling average)
- Generate inline flag text for triggered anomalies

### Step 5: Events calendar check (automated)
- Check if current week or prior week dates match any events calendar entry
- If match: prepare events banner text

### Step 6: Dashboard render (automated)
- Render shared executive summary section with all three top-line KPIs
- Render LfL panel
- Inject events banner if triggered
- Render each VP section with metrics, charts, and inline flags

### Step 7: Manual review (optional, by Analytics lead)
- Verify anomaly flags are accurate before VPs access
- Check that events banners are displaying correctly
- Confirm baseline values look reasonable

---

## 9. Skill Trigger Conditions

The WBR skill should activate when a user says any of the following (or close variants):

- "Generate the WBR" / "Run the weekly business review"
- "Build the VP report" / "Create the weekly performance report"
- "Show me this week's performance dashboard"
- "What does the [Product/Revenue/CX] VP section look like?"
- "Run the weekly report for [date range]"
- "Refresh the WBR"

The skill should ask for the reporting week date range if not provided.

---

## 10. Open Questions (Pre-launch Blockers)

| # | Question | Owner | Status |
|---|---|---|---|
| 1 | Company name and brand color palette for dashboard styling | Design / Brand | Open |
| 2 | Confirm anomaly threshold values with each VP | Analytics + VPs | Open |
| 3 | Who owns and maintains the events calendar? | Analytics Lead | Open |
| 4 | Does Finance VP want a separate gross vs net revenue line, or just return rate displayed? | Finance VP | Open |
| 5 | Should the CX VP section be combined with Finance or fully separate? | CX VP | Open |

---

## 11. Revision History

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 | 2026-05-13 | Initial SOP created from context-gathering session | AI Edge Week 2 Bootcamp |
