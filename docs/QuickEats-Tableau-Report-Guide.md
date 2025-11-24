# QuickEats Multi-Page Tableau Report Blueprint

This guide documents how to build the requested three-page Tableau report for QuickEats and the accompanying strategic analysis. It captures calculated fields, page layouts, and interaction requirements so the packaged workbook (`SurajKumarDas_GradedAssignment_Submission.twbx`) can be assembled consistently.

## 1) Data Modeling & Enrichment
Create these calculated fields in Tableau (Data pane → **Create Calculated Field**):

### Order Cost Category (`order_cost_category`)
Categorize orders by cost using quartiles (adjust thresholds to your dataset):
```tableau
IF [Cost of the order] <= {FIXED : PERCENTILE([Cost of the order], 0.25)} THEN "Low"
ELSEIF [Cost of the order] <= {FIXED : PERCENTILE([Cost of the order], 0.75)} THEN "Medium"
ELSE "High" END
```

### Customer Satisfaction (`customer_satisfaction`)
```tableau
IF [Rating] >= 4 THEN "Satisfied"
ELSEIF [Rating] = 3 THEN "Neutral"
ELSE "Unsatisfied" END
```

### Delivery Efficiency (`delivery_efficiency`)
```tableau
IF [Total delivery time] <= 35 THEN "Fast"
ELSEIF [Total delivery time] <= 50 THEN "Standard"
ELSE "Slow" END
```

### Satisfaction Rate (Measure) (`Satisfaction Rate`)
Percent of satisfied orders among rated orders:
```tableau
SUM( IIF([customer_satisfaction] = "Satisfied", 1, 0) )
/ SUM( IIF(ISNULL([Rating]), 0, 1) )
```
Format as percentage. Alternatively, wrap in `* 100` if you prefer a numeric percentage.

## 2) Page Designs & Visual Specs
Create three dashboards with navigation buttons (objects with Dashboard → Actions → Go to Sheet) so users can move between pages.

### Page 1: Executive Summary (Audience: C-Level)
1. **KPI Banner**: Tiles for Total Revenue (`SUM([Cost of the order])`), Total Orders (`COUNT([Order ID])`), Avg Total Delivery Time (`AVG([Total delivery time])`), and Overall Satisfaction Rate (measure above). Use big fonts and consistent coloring.
2. **Orders by Day**: Line/area chart with Day of Week on columns and `COUNT([Order ID])` on rows.
3. **Revenue by Cuisine**: Bar chart with Cuisine Type on rows and `SUM([Cost of the order])` on columns.
4. **Satisfaction Breakdown**: Pie chart using `customer_satisfaction` and `COUNT([Order ID])`.
5. **Interactive Filters**: Action filter on cuisine (e.g., click a cuisine bar to filter other views across all pages). Set Dashboard Actions → Filter → Apply to selected sheets on all dashboards.

### Page 2: Operations Deep Dive (Audience: Head of Operations)
1. **Efficiency KPIs**: Cards showing Average Food Preparation Time and Average Total Delivery Time. Use LODs if needed to separate prep vs delivery metrics.
2. **Restaurant Performance Table**: Text table with Restaurant Name, Total Orders (`COUNT([Order ID])`), Average Total Delivery Time (`AVG([Total delivery time])`). Add conditional formatting on Avg Total Delivery Time: green for <30, red for >50 (use color legend or calculated field like `IF [Avg Total Delivery Time] < 30 THEN "Good" ELSEIF ...`).
3. **Delivery Efficiency Breakdown**: Stacked bar by Day of Week with counts of `delivery_efficiency`. Put Day on rows, `COUNT([Order ID])` on columns, and color by `delivery_efficiency`.
4. **Enhanced Tooltip**: On the stacked bar, add Tooltip text showing top 3 restaurants for that segment. Example tooltip:
   - Create a set or table calc: `INDEX()` ordered by `COUNT([Order ID])` per restaurant within the hovered Day/Efficiency. Use Tooltip with a `Worksheet` that filters to `delivery_efficiency` and Day via tooltip action, or use `WINDOW_MAX`/`RANK` to list top 3 names.

### Page 3: Customer & Cuisine Insights (Audience: Marketing)
1. **Satisfaction by Cuisine**: Horizontal bar sorted ascending by Satisfaction Rate (`[Satisfaction Rate]` per Cuisine Type). Use reference lines to highlight targets (e.g., 80%).
2. **Cost vs. Rating Analysis**: Scatter plot with `Cost of the order` on X and `Rating` on Y. Add `customer_satisfaction` color and maybe trendline to see correlation.
3. **Order Cost Distribution**: Histogram of `Cost of the order` using bins; show counts of orders.
4. **Detailed Customer Feedback Table**: Filter for `customer_satisfaction = "Unsatisfied"`; include Order ID, Restaurant Name, Cuisine Type, Total Delivery Time, Rating, and any comments/notes. Sort by Rating ascending or Delivery Time descending to surface pain points.

## 3) Interactivity & Navigation
- Add navigation buttons (shapes or text) on each page that trigger **Go to Dashboard** actions for the other two pages.
- Ensure cuisine action filter applies across all dashboards (Dashboard → Actions → Filter → Run on Select → Target Sheets: all pages).
- Keep consistent color palettes for satisfaction (e.g., Green=Satisfied, Gray=Neutral, Red=Unsatisfied) and delivery efficiency (Green=Fast, Amber=Standard, Red=Slow).

## 4) Strategic Analysis Template
Once the visuals are built, use the dashboards to answer:
1. **Operational Excellence Opportunity**: Identify top 3 restaurants for an Express Delivery pilot using high order volume + low average delivery time (use Restaurant Performance table and Delivery Efficiency breakdown).
2. **Customer Retention Risk**: Find the cuisine with lowest Satisfaction Rate and high order share (compare Satisfaction by Cuisine vs Revenue/Orders by Cuisine).
3. **Overall Strategic Recommendation**: Combine insights from pages 1–3 to propose one initiative (e.g., speed-focused ops fix, cuisine-specific quality program, promo targeting slow delivery cohorts). Cite KPIs and charts directly.

Document your written responses in a separate markdown/docx alongside the packaged workbook. Name the Tableau export **`SurajKumarDas_GradedAssignment_Submission.twbx`** before submission.

## 5) Delivery Checklist
- All calculated fields created and reused across worksheets.
- Three dashboards with navigation and cuisine action filters working globally.
- Tooltips include context (top restaurants for slow/fast segments on Page 2).
- Export and submit `SurajKumarDas_GradedAssignment_Submission.twbx` plus the strategic analysis document.
