# Ecommerce Data Database Schema

**File:** `ecommerce_data (1).db`  
**Type:** SQLite  
**Data Range:** 2024-11-01 to 2024-12-15 (45 days)  
**Architecture:** Star Schema — one fact table joined to four dimension tables

---

## Overview

This database captures daily ecommerce performance metrics across two sales channels, two customer segments, and two product categories over a 45-day period spanning the Black Friday / holiday season.

```
                ┌──────────────┐
                │    dates     │
                └──────┬───────┘
                       │
  ┌──────────┐  ┌──────▼────────┐  ┌──────────────────┐
  │ channels ├──► daily_metrics ◄──┤ product_categories│
  └──────────┘  └──────┬────────┘  └──────────────────┘
                       │
                ┌──────┴──────────┐
                │ customer_types  │
                └─────────────────┘
```

---

## Tables

### `daily_metrics` (Fact Table)

The central table containing all measurable business events. Each row represents one unique combination of date × customer type × channel × product category.

**Row count:** 360 (45 dates × 2 customer types × 2 channels × 2 categories)

| Column | Type | Nullable | Description |
|---|---|---|---|
| `metric_id` | INTEGER | NOT NULL | Primary key (auto-increment) |
| `date_id` | INTEGER | NOT NULL | FK → `dates.date_id` |
| `customer_type_id` | INTEGER | NOT NULL | FK → `customer_types.customer_type_id` |
| `channel_id` | INTEGER | NOT NULL | FK → `channels.channel_id` |
| `category_id` | INTEGER | NOT NULL | FK → `product_categories.category_id` |
| `visitors` | INTEGER | NOT NULL | Unique visitors to the site/channel |
| `page_views` | INTEGER | NOT NULL | Total page views |
| `add_to_carts` | INTEGER | NOT NULL | Number of add-to-cart events |
| `checkouts` | INTEGER | NOT NULL | Number of checkout initiations |
| `purchases` | INTEGER | NOT NULL | Completed purchases |
| `total_revenue` | REAL | NOT NULL | Total revenue generated (currency units) |
| `avg_order_value` | REAL | NOT NULL | Average value per order |
| `return_rate` | REAL | NOT NULL | Rate of returned items (0.0–1.0 float) |
| `support_tickets` | INTEGER | NOT NULL | Number of support tickets opened |

**Key metric ranges:**

| Metric | Min | Max | Avg |
|---|---|---|---|
| visitors | 297 | 11,815 | 1,585 |
| total_revenue | 559.90 | 73,929.48 | 6,375.21 |
| return_rate | 0.0065 | 0.5182 | 0.1119 |

**Conversion funnel averages:**

| Stage | Rate |
|---|---|
| Add-to-Cart rate (add_to_carts / visitors) | 9.03% |
| Purchase rate (purchases / visitors) | 4.54% |
| Checkout conversion (purchases / checkouts) | 84.2% |

---

### `dates` (Dimension)

Calendar dimension with business-relevant flags for the analysis period.

**Row count:** 45

| Column | Type | Nullable | Description |
|---|---|---|---|
| `date_id` | INTEGER | NOT NULL | Primary key |
| `date` | TEXT | NOT NULL | Calendar date in `YYYY-MM-DD` format |
| `is_black_friday_period` | INTEGER | NOT NULL | 1 = Black Friday period, 0 = normal (3 days flagged) |
| `is_weekend` | INTEGER | NOT NULL | 1 = Saturday or Sunday, 0 = weekday (14 days flagged) |
| `day_of_week` | INTEGER | NOT NULL | Day number: 0=Monday … 6=Sunday |

**Date range:** 2024-11-01 → 2024-12-15  
**Black Friday days:** 3  
**Weekend days:** 14

---

### `channels` (Dimension)

Sales / traffic channel dimension.

**Row count:** 2

| Column | Type | Nullable | Description |
|---|---|---|---|
| `channel_id` | INTEGER | NOT NULL | Primary key |
| `channel_name` | TEXT | NOT NULL | Channel label |

**Values:**

| channel_id | channel_name |
|---|---|
| 1 | Direct |
| 2 | Email |

---

### `customer_types` (Dimension)

Customer segmentation dimension.

**Row count:** 2

| Column | Type | Nullable | Description |
|---|---|---|---|
| `customer_type_id` | INTEGER | NOT NULL | Primary key |
| `customer_type_name` | TEXT | NOT NULL | Segment label |

**Values:**

| customer_type_id | customer_type_name |
|---|---|
| 1 | New |
| 2 | Returning |

---

### `product_categories` (Dimension)

Product category dimension.

**Row count:** 2

| Column | Type | Nullable | Description |
|---|---|---|---|
| `category_id` | INTEGER | NOT NULL | Primary key |
| `category_name` | TEXT | NOT NULL | Category label |

**Values:**

| category_id | category_name |
|---|---|
| 1 | Electronics |
| 2 | Home Goods |

---

## Relationships

| Foreign Key (daily_metrics) | References |
|---|---|
| `date_id` | `dates.date_id` |
| `customer_type_id` | `customer_types.customer_type_id` |
| `channel_id` | `channels.channel_id` |
| `category_id` | `product_categories.category_id` |

All foreign keys use `NO ACTION` on update/delete (no cascading).

---

## Example Queries

**Total revenue by channel:**
```sql
SELECT c.channel_name, ROUND(SUM(dm.total_revenue), 2) AS revenue
FROM daily_metrics dm
JOIN channels c ON dm.channel_id = c.channel_id
GROUP BY c.channel_name;
```

**Daily conversion funnel during Black Friday period:**
```sql
SELECT d.date,
       SUM(dm.visitors)      AS visitors,
       SUM(dm.purchases)     AS purchases,
       ROUND(SUM(dm.purchases) * 100.0 / SUM(dm.visitors), 2) AS purchase_rate_pct
FROM daily_metrics dm
JOIN dates d ON dm.date_id = d.date_id
WHERE d.is_black_friday_period = 1
GROUP BY d.date;
```

**Average order value: New vs Returning customers:**
```sql
SELECT ct.customer_type_name,
       ROUND(AVG(dm.avg_order_value), 2) AS avg_order_value
FROM daily_metrics dm
JOIN customer_types ct ON dm.customer_type_id = ct.customer_type_id
GROUP BY ct.customer_type_name;
```
