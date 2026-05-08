# Amazon Sales 2025 — Looker Studio Dashboard Blueprint

This repository now includes a dashboard-ready dataset at:

- `data/amazon_sales_kaggle_dashboard_ready.csv`

## 1) Kaggle Dataset Selection (Single Dataset)
Use **one** dataset only: the Amazon 2025 sales CSV in this project, which is suitable for Kaggle-style ecommerce analysis and contains sales, products, category, payment, geography, and status dimensions.

## 2) Connect in Google Looker Studio
1. Open Looker Studio: https://lookerstudio.google.com/
2. Create **Blank Report**.
3. Add Data Source → **File Upload**.
4. Upload `amazon_sales_kaggle_dashboard_ready.csv`.
5. Confirm field types:
   - `order_date`: Date
   - `year`, `month`, `quantity`, `is_cancelled`: Number
   - `price`, `total_sales`, `avg_order_value`: Currency/Number

## 3) Calculated Fields in Looker Studio
Create these calculated fields:

- **Cancellation Rate**
  - `SUM(is_cancelled) / COUNT(order_id)`
- **Net Sales (Non-Cancelled)**
  - `SUM(CASE WHEN order_status = "Completed" THEN total_sales ELSE 0 END)`
- **Orders**
  - `COUNT(order_id)`

## 4) Dashboard Pages & Visuals

## Page A — Executive Overview
- Scorecards: `Net Sales`, `Orders`, `SUM(quantity)`, `Cancellation Rate`.
- Time series: `order_date` vs `SUM(total_sales)`.
- Bar chart: `category` vs `SUM(total_sales)`.
- Geo chart: `customer_location` vs `SUM(total_sales)`.

## Page B — Product Performance
- Tree map: `product` sized by `SUM(total_sales)` and colored by `SUM(quantity)`.
- Horizontal bar: Top 10 `product` by `SUM(total_sales)`.
- Table: `product`, `category`, `SUM(quantity)`, `SUM(total_sales)`, `avg_order_value`.

## Page C — Customer & Payment Insights
- Donut chart: `payment_method` by `SUM(total_sales)`.
- Stacked bar: `payment_method` by `order_status` with metric `COUNT(order_id)`.
- Heatmap table: `customer_location` x `category` with `SUM(total_sales)`.

## 5) Necessary Filters
Place as report-level controls:
- Date range control (`order_date`)
- Dropdown: `category`
- Dropdown: `customer_location`
- Dropdown: `payment_method`
- Checkbox/dropdown: `order_status`

## 6) Color Grading (Premium Theme)
Use a high-contrast modern palette:

- Background: `#0B1020`
- Card background: `#121A2F`
- Primary accent: `#5B8FF9`
- Secondary accent: `#61DDAA`
- Highlight accent: `#F6BD16`
- Alert accent (cancelled): `#E86452`
- Text primary: `#E5EAF3`
- Text muted: `#9AA4B2`

Gradient recommendation for performance visuals:
- Low → Mid → High: `#1D2A52` → `#5B8FF9` → `#61DDAA`

## 7) UX Polish Checklist
- Enable cross-filtering on bar/treemap charts.
- Turn on chart interaction highlighting.
- Use consistent number formatting (`$#,##0`, `0.0%`).
- Add page-level subtitles with active filter context.
- Keep spacing consistent using 8px grid rhythm.

This blueprint gives a visually strong, filter-rich dashboard in Looker Studio with one focused dataset.
