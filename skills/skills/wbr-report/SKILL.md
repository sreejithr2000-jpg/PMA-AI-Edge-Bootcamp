---
name: wbr-report
description: Generate a Weekly Business Review (WBR) performance dashboard for VP audiences in an ecommerce business. Use this skill when the user asks to generate the WBR, run the weekly business review, build the VP performance report, create the weekly dashboard, or show this week's ecommerce performance. Also trigger when a user references any VP-specific metric section (Product funnel, Revenue/Finance, Customer Experience) in the context of weekly reporting. Ask for the reporting week date range if not provided.
---

# WBR Report Generation Skill

This skill generates a self-serve Weekly Business Review (WBR) dashboard for all major VPs in an ecommerce business. The report has two layers: a shared executive summary and VP-specific drill-down sections.

## Context

- **Audience:** VP of Product, VP of Revenue/Sales, VP of Finance, VP of Customer Experience
- **Delivery:** Self-serve HTML dashboard — VPs pull it on demand
- **Data source:** Ecommerce SQLite database (star schema: daily_metrics fact table + dates, channels, customer_types, product_categories dimensions)
- **Refresh cadence:** Weekly, Monday morning, covering Mon–Sun of the prior full week
- **SOP reference:** WBR_SOP.md in the project directory

## Report Structure

The output is always a single self-contained HTML file with two layers:

### Layer 1 — Shared Executive Summary (all VPs)

Always include these three top-line KPIs with WoW and MoM comparisons:
- **Total Revenue** — primary business health signal
- **Total Visitors** — demand level signal
- **Total Purchases** — funnel bridge metric

Always include a Like-for-Like (LfL) panel comparing Mon–Thu of each week, excluding any event days, showing Revenue, Visitors, and AOV with % change.

If any date in the current or prior week matches the events calendar, display a banner:
> ⚠ Note: [Event Name] occurred on [dates] in the [current/prior] week. WoW comparisons reflect this event. Refer to the Like-for-Like panel for adjusted comparison.

Known events: Black Friday (last Friday of November), Cyber Monday (Monday after BF), Christmas Eve/Day (Dec 24–25), New Year's Eve/Day (Dec 31–Jan 1).

### Layer 2 — VP-Specific Sections

**Product VP section** — funnel health and category performance:
- Full conversion funnel: Visitors → ATC Rate → Checkout Rate → Purchase Rate (with WoW comparison at each stage)
- Category performance: Revenue, Purchases, AOV for Electronics vs Home Goods side by side
- Return rate by category and customer segment (New vs Returning) as a comparison grid
- Key insight: Checkout conversion is a quality gate, not a lever — ATC rate is the primary optimization target

**Revenue / Finance VP section** — revenue quality and economics:
- Daily revenue trend: current week vs prior week on one chart
- AOV breakdown: by channel (Direct vs Email) and segment (New vs Returning)
- Net revenue estimate: Gross revenue − (return_rate × revenue) — shown as gross vs net KPI pair
- Support ticket rate: tickets per 100 purchases vs 4-week rolling average

**Customer Experience VP section** — post-purchase health:
- Return rate: overall + by category + day-of-week pattern (Saturday Electronics is structurally elevated)
- Daily support ticket count: current vs prior week
- Ticket rate trend vs 4-week baseline with baseline band
- New vs Returning: return rate, AOV, purchase rate side by side

## Baseline Computation

Compute the 4-week rolling average for every metric before rendering:
- Pull the 4 complete weeks preceding the reporting week
- Exclude any weeks containing flagged calendar events
- Use as the benchmark for anomaly detection and trend context

## Anomaly Detection Rules

Run anomaly detection after data loads, before rendering. Flags appear inline with the affected metric — not in a separate panel.

**Raise a flag when either condition triggers:**
1. Fixed threshold: metric changed >±15% WoW or >±20% MoM (use ±2pp absolute for rate metrics)
2. Statistical outlier: value is >2 standard deviations from the 4-week rolling average

**Flag text format:**
- Both conditions: `⚠ [Metric] [direction] [magnitude]. [X] standard deviations from 4-week average (baseline: [Y]).`
- Fixed threshold only: `⚠ [Metric] [direction] [magnitude] WoW. Within normal statistical variance.`

**Rules:**
- Maximum 3 flags per VP section — prioritize by z-score magnitude
- No auto-adjustment of numbers — always display raw values
- No strategic interpretation or recommendations in flag text — one factual sentence only
- Do NOT generate executive narrative prose, written summaries, or bullet-point insights

## Dashboard Design

Produce a single self-contained HTML file with:
- Dark theme (#0a0a12 background) with Inter font
- Fixed topbar showing report period and last refreshed date
- Tabbed navigation for VP sections (Product | Revenue/Finance | CX)
- Shared executive summary always visible above the tabs
- Inline anomaly flags as amber warning badges next to the affected metric
- Events banner if triggered — amber stripe below the topbar
- Charts using Chart.js from CDN
- All data embedded directly in the HTML (no external data fetches)
- Fully self-contained — works offline once generated

## Derived Metrics to Compute

```
ATC Rate              = add_to_carts / visitors
Purchase Rate         = purchases / visitors
Checkout Conversion   = purchases / checkouts
Tickets/100 purchases = support_tickets / purchases × 100
Net Revenue (est.)    = total_revenue × (1 - return_rate)
LfL Revenue           = sum(total_revenue) for Mon–Thu of current week
LfL Visitors          = sum(visitors) for Mon–Thu of current week
LfL AOV               = LfL Revenue / sum(purchases) for Mon–Thu
```

## Required Inputs

Ask the user for these if not provided:
1. **Reporting week** — the Mon–Sun date range for the current week (e.g., "Dec 2–8, 2024")
2. **Data source path** — path to the SQLite database file
3. **Company name** (optional) — for dashboard header; default to "Ecommerce Co." if not provided

## Output

A single HTML file named `wbr_[YYYY-MM-DD].html` where the date is the Monday of the reporting week. Save it in the same directory as the data source.

## Key Business Context

This skill was built on an ecommerce dataset with these characteristics:
- Two channels: Direct (organic) and Email (campaign)
- Two customer segments: New and Returning
- Two product categories: Electronics (~$100 AOV) and Home Goods (~$50 AOV)
- Electronics has structurally higher return rates and Saturday is the highest return day
- ATC rate (~8.8%) is the highest-leverage funnel metric — checkout conversion (~85%) is near-optimal
- New customers spend more per order but return more; Returning customers post-promo are the most profitable cohort
- Black Friday creates 3–4× traffic spike with +50% AOV — always check for event calendar overlap before interpreting WoW drops
- Support tickets lag purchases by 3–7 days — a ticket rate spike in the week after a promo is expected, not a quality issue

## Examples

- "Generate the WBR for Dec 2–8" → build full dashboard from the database for that week
- "Run the weekly report" → ask for date range and data source, then generate
- "Show me the Product VP section for last week" → generate only the Product section with full context
- "What's unusual in this week's WBR?" → run anomaly detection and list all flags across all VP sections
- "Refresh the WBR with the latest data" → re-query the database and regenerate the HTML

## Guidelines

- Always compute the LfL panel — raw WoW numbers alone are misleading near promotional periods
- Never auto-adjust numbers for events — flag the event, display raw values, let VPs interpret
- Always show both WoW and MoM comparisons on top-line metrics
- Segment before aggregating — aggregate numbers hide the most actionable insights
- If the database path is not accessible, explain what data is needed and what format to provide it in
- Do not generate narrative prose, strategic recommendations, or executive summaries — data and flags only
