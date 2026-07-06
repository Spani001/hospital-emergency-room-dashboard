# Hospital ER Dashboard — Power BI Project

A Power BI dashboard built to track and analyze Hospital Emergency Room (ER) operations — patient volume, wait times, satisfaction scores, referrals, and admission patterns — across a monthly trend view, a consolidated summary view, and a patient-level detail view.

> Built as a practice project during my Data Engineering internship to strengthen data modeling, DAX, and dashboard design skills.

---

## 📌 Project Overview

The goal was to simulate a real hospital operations dashboard — the kind a hospital administrator could use to monitor ER performance day-to-day and month-to-month — while practicing the full Power BI workflow: shaping a clean data model, writing measures, designing visuals, and building a multi-page report that tells a clear story.

**Objectives:**
- Track key ER metrics: number of patients, average wait time, patient satisfaction score, and patients referred
- Break down patient data by demographics (age group, gender, race), admission status, department referral, and wait-time buckets
- Provide both a monthly (trend) view and a consolidated (overall) view of the same metrics
- Support drill-down into individual patient records
- Summarize key insights on a dedicated takeaways page

---

## 🗂️ Data Source & Data Model

| Table | Purpose | Key Columns |
|---|---|---|
| **Hospital ER_Data** | Main fact table — one row per patient visit | Patient Id, Patient Full Name, Patient Gender, Patient Age, Patient Race, Patient Admission Date, Department Referral, Admission Status, Patient Waittime, Waittime Status, Waittime Interval, Satisfaction Score, No of Patients, No of Patients Referred |
| **Date Table** | Dedicated calendar/dimension table for time intelligence | Date, Year, Month Name, Day Name, Date Hierarchy (Year → Quarter → Month → Day) |

**Relationship:** `Date Table[Date]` → `Hospital ER_Data[Patient Admission Date]` (one-to-many, Date Table on the "one" side).

Using a dedicated Date Table (instead of the raw date column on the fact table) keeps every slicer, trend chart, and drill-down hierarchy on the report consistent — this follows the standard **star schema** modeling pattern (one fact table + supporting dimension tables).

---

## 📄 Report Structure

| # | Page | What it shows |
|---|---|---|
| 1 | **Monthly View** | Day/month-wise trend of KPIs, plus demographic and wait-time breakdowns |
| 2 | **Consolidated View** | Same KPIs aggregated as an overall month-wise summary |
| 3 | **Patient Details** | Patient-level table for row-level drill-down |
| 4 | **Key Takeaways** | Summary page highlighting the main insights |

Pages are connected via a **Page Navigator** + action buttons, so the report can be browsed like a mini-app.

### Monthly View
- Year and Month slicers
- 4 KPI cards: No. of Patients, Avg Wait Time, Patient Satisfaction Score, No. of Patients Referred
- Area charts showing day-wise KPI trends
- Matrix comparing patient counts against Admission Status
- Bar/column charts for Admission Status, Age Group, Department Referral
- Donut charts for Waittime Status and Patient Gender split
- Clustered bar chart for Patient Race distribution
- Day-of-week column chart + Waittime Interval matrix

### Consolidated View
Same KPIs as Monthly View, but aggregated month-wise (column charts) instead of a continuous day-wise trend — a quicker, higher-level summary.

### Patient Details
A detailed table: Patient Id, full name, gender, age, admission date (Year/Quarter/Month/Day), race, wait time, department referral, admission status.

### Key Takeaways
Date slicer + trend chart, built to hold the key written insights from the analysis.

---

## 📊 Visuals Used & Why

| Visual | Used For |
|---|---|
| Card | Single KPI at a glance |
| Area Chart | Day-wise trend of a KPI |
| Column / Clustered Column | Comparing a KPI across categories |
| Bar / Clustered Bar | Comparing a KPI across categories with long labels |
| Donut Chart | Proportion/share (wait-time status, gender split) |
| Matrix (Pivot Table) | Cross-tabulating two dimensions against a KPI |
| Table | Row-level patient detail |
| Slicer | Filtering by Year, Month, or Date |
| Buttons / Page Navigator | Navigation between report pages |

---

## 🎨 Design & Formatting

- Base theme: Power BI's built-in **CY26SU05** theme, customized per visual
- Consistent accent blue (`#118DFF`) for primary KPI numbers; green/red used only where a good-vs-bad meaning was intended
- Identical card sizing/spacing across pages for easy comparison
- Manually tuned donut/matrix colors for readability
- Repeated header banner and page-title textbox across all 4 pages for a consistent look
- Page Navigator + buttons for guided navigation

---

## 🧩 Challenges Faced & How I Solved Them

Most of the real challenges came from the data modeling side — deciding when to build a **calculated column** vs. a **measure**, and designing new fields that actually made the visuals easier to read.

- **Bucketing raw wait time into a readable category (`Waittime Status`)** — the raw `Patient Waittime` column is just a number in minutes, not very useful directly in a donut chart. Solved with a calculated column using nested `IF`/`SWITCH` logic. Conceptually, this had to be a *column* (not a measure) since the classification is evaluated per row before it can be aggregated.
- **Creating wait-time buckets/intervals (`Waittime Interval`)** — needed a binned field for the wait-time matrix instead of one row per exact minute value. Took a few attempts to get bucket sizes that kept the matrix readable.
- **Calculated column vs. measure** — the biggest conceptual hurdle. A calculated column is computed once per row and stored (good for `Waittime Status`). A measure is computed on the fly based on filter context (good for average wait time or total patients, which need to change with slicer selections). Mixing these up early gave wrong or static-looking numbers.
- **Getting "No. of Patients" to count correctly** — a simple `SUM` wasn't right since the data is one row per visit; needed a proper count measure, and in places a distinct count so patients weren't double-counted on the Patient Details page. Learned when to use `COUNT` vs. `DISTINCTCOUNT`.
- **Aggregating Avg Wait Time and Satisfaction Score cleanly** — needed real measures (not just "summarize as average" on the column) so numbers recalculate correctly with every slicer/filter change, and had to watch for blank/null values skewing the average.
- **Keeping measures consistent across Monthly View and Consolidated View** — built each measure once and reused it on both pages, so the "same" KPI never quietly showed different numbers.
- **Donut chart colors clashing** — fixed with manual per-category color formatting.
- **Matrix readability** — simplified the Waittime Interval matrix to just the key dimensions (Waittime Interval × Day Name × No. of Patients).

---

## 🧠 Key Concepts

| Concept | Notes |
|---|---|
| **Fact vs. Dimension table** | `Hospital ER_Data` = fact (event-level); `Date Table` = dimension (describes/slices the fact table) |
| **Star schema** | One fact table connected to dimension tables via one-to-many relationships |
| **Calculated column vs. measure** | Column = per-row, stored; Measure = computed on the fly per filter context |
| **Date Table best practice** | Use a dedicated marked date table for consistent time intelligence & drill-down hierarchies |
| **Slicer vs. Filter pane** | Slicer = on-canvas, end-user interactive; Filter pane = author-set, less visible |
| **Cross-filtering** | Clicking one visual filters others on the same page by default |
| **Drill-down hierarchy** | Year → Quarter → Month → Day, built into the Date Table |
| **Matrix visual** | Power BI's pivot table — cross-tabulates fields against a measure |
| **Page Navigator** | Auto-generates navigation buttons for report pages |
| **Theme** | JSON-based report-wide color/font definition for visual consistency |

---

## 🚀 Learnings & Next Steps

- Setting up the Date Table relationship early made every later visual easier — worth doing first on any future dashboard
- Consistent card formatting across pages is a small effort with a big visual payoff
- **Next:** add DAX measures for month-over-month growth %, a tooltip page for deeper context on hover, and a KPI target/goal comparison using conditional formatting

---

## 🛠️ Tech Used

`Power BI Desktop` · `DAX` · `Data Modeling (Star Schema)`

---

## 📁 Repo Contents

```
├── Hospital_ER_Dashboard.pbix     # Power BI project file
├── docs/
│   └── Project_Documentation.docx # Detailed write-up
└── README.md                      # This file
```

---

*Note: This dashboard uses a sample/practice Hospital ER dataset for learning purposes — no real patient data is involved.*

