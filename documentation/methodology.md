# Methodology

## Food Affordability Intelligence — Nigeria

This document explains the data sources, preparation process, analytical framework, Power BI data model, calculations, validation checks, and limitations used to build the **Food Affordability Intelligence — Nigeria** dashboard.

The objective was to move beyond headline food inflation and examine food affordability through several connected measures: **Food CPI, Cost of a Healthy Diet (CoHD), food-group costs, state-level variation, urban/rural differences, and individual food-price movements.**

# 1. Analytical Objective

The project was designed around one central question:

> **What does changing food prices actually mean for the cost of maintaining a healthy diet in Nigeria?**

Rather than using a single indicator, the analysis combines multiple perspectives:

1. **Trend** — How does the estimated cost of a healthy diet change over time?
2. **Composition** — Which food groups contribute most to that cost?
3. **Geography** — How does the estimated cost differ across states?
4. **Price pressure** — Which individual food items are experiencing the strongest movements?
5. **Interpretation** — What can these measures tell us about food affordability?

# 2. Data Sources

The project uses three primary data products from the **National Bureau of Statistics (NBS), Nigeria**.

## 2.1 Consumer Price Index

The CPI dataset provides the inflation-index information used for:

* General CPI
* Food CPI
* Monthly food-price movement
* Annual food-price movement
* Food CPI versus general CPI
* State-level CPI comparison

The rebased CPI uses a **2023 weight reference** and **2024 price/base period**.

The CPI framework covers **934 product varieties** across **13 COICOP 2018 divisions**.

The national CPI data used in this project contains observations from:

**January 2023 – May 2026**

## 2.2 Selected Food Price Watch

**Dataset identifier:** `NGA-NBS-FOODPW`

The Selected Food Price Watch is used for the individual food-item price analysis on **Page 04 — Price Pressure Lab**.

The May 2026 source table contains:

* `Item Label`
* `Average of May-25`
* `Average of April-26`
* `Average of May-26`
* `MoM`
* `YoY`
* `Highest`
* `Lowest`

The analysis uses:

* `Average of May-26` as the latest price level
* `MoM` for latest monthly movement
* `YoY` for annual movement
* `Highest` and `Lowest` for state-level price context

## 2.3 Cost of a Healthy Diet

**Dataset identifier:** `NGA-NBS-COHD`

The Cost of a Healthy Diet represents the estimated lower-bound cost of the least expensive combination of locally available foods that meets a healthy-diet standard.

The project uses CoHD data for:

* National monthly cost
* State-level cost
* Urban/rural comparison
* Food-group composition
* Affordability analysis

The monthly CoHD series used in the dashboard covers:

**April 2025 – April 2026**

# 3. Data Preparation

The source workbooks were first inspected to identify the relevant analytical tables.

The preparation process included:

1. Identifying the appropriate source sheets
2. Removing report titles and presentation-only rows
3. Promoting the correct header rows
4. Standardising field names
5. Converting dates to proper date types
6. Converting numeric fields to numeric data types
7. Separating national and state-level datasets
8. Separating food-group and food-item analysis
9. Preserving the original reporting periods
10. Preparing tables for Power BI relationships and DAX calculations

No missing historical values were artificially interpolated.

Where a dataset was only available as a snapshot, it remained a snapshot.


# 4. Power BI Analytical Tables

The Power BI model uses the following core tables.

| Table                 | Purpose                               |
| --------------------- | ------------------------------------- |
| `Dim_Date`            | Central date dimension                |
| `Fact_Affordability`  | National monthly affordability series |
| `Food_Groups`         | Healthy-diet food-group composition   |
| `Food_Prices`         | Selected food-price snapshot          |
| `State_Affordability` | State-level healthy-diet cost         |
| `State_CPI`           | State-level CPI comparison            |
| `Urban_Rural`         | Urban/rural healthy-diet cost         |


# 5. Date Model

`Dim_Date` provides the central date context for the time-series analysis.

It is used to control the national monthly series and dynamic period titles.

The model deliberately distinguishes between:

* **time-series data**, where a date filter is meaningful
* **snapshot data**, where the observation represents a specific reporting period
* **cross-sectional data**, such as state comparisons

This prevents a date slicer from incorrectly implying that a May 2026 snapshot contains a full historical series.


# 6. National Affordability Analysis

The national affordability analysis combines:

* Food CPI
* General CPI
* National CoHD

The primary purpose is to compare their **movement**, rather than their raw values.

Food CPI is an index.

CoHD is a monetary estimate expressed in naira per adult per day.

Because these are different units and concepts, their raw levels are not treated as directly comparable.

# 7. Food CPI Index

To compare Food CPI movement with CoHD movement, Food CPI is converted into a relative index.

The calculation is conceptually:

```text
Food CPI Index =
Current Food CPI / Starting Food CPI × 100
```

The selected starting observation is treated as:

```text
Index = 100
```

Subsequent values therefore show how the series has changed relative to that starting point.


# 8. CoHD Index

The same relative-index approach is applied to CoHD.

```text
CoHD Index =
Current CoHD / Starting CoHD × 100
```

This allows the movement of the monetary CoHD series to be compared with the movement of Food CPI without treating the two raw measures as equivalent.


# 9. Affordability Divergence

The project defines an analytical signal called **Affordability Divergence**:

```text
Affordability Divergence =
CoHD Index − Food CPI Index
```

Interpretation:

### Positive value

CoHD has increased more than Food CPI relative to the selected starting point.

### Negative value

CoHD has increased less than Food CPI relative to the selected starting point.

### Near-zero value

The two indexed series have moved broadly together.

This is a **descriptive analytical signal**.

It should not be interpreted as:

* a causal relationship
* a measure of household affordability
* proof that food prices determine CoHD
* proof that the two measures are unrelated


# 10. Food-Group Composition

The `Food_Groups` table contains:

* `Food_Group`
* `Daily_Cost`
* `Share`

The food-group analysis examines how the estimated daily healthy-diet cost is distributed across major food categories.

The current dataset identifies:

**Animal source foods**

as the largest represented component.

Its estimated contribution is approximately:

**₦642.88/day**

or approximately:

**40.4%**

of the represented daily healthy-diet cost.


# 11. Food-Group Share Handling

The source `Share` field is stored as percentage-point values.

For example:

```text
40.446
```

represents:

```text
40.446%
```

Therefore, Power BI display logic divides the value by 100 before applying percentage formatting.

Conceptually:

```text
Food Group Share % =
Share / 100
```

This prevents a value such as `40.446` from being displayed incorrectly as `4,044.6%`.


# 12. Geographic Affordability Analysis

The state-level analysis uses `State_Affordability`.

The purpose is to identify variation in estimated healthy-diet costs across Nigerian states.

Two primary measures are used:

* Highest state CoHD
* Lowest state CoHD

The spread is calculated as:

```text
State Affordability Spread =
Most Expensive State CoHD
−
Least Expensive State CoHD
```

Using the observations in the dataset:

* **Ekiti:** approximately ₦2,036/day
* **Adamawa:** approximately ₦1,143/day

Resulting spread:

**approximately ₦893/day**

This represents geographic variation in the estimated cost of the healthy-diet basket.

It does not represent a direct measure of poverty or household welfare.


# 13. State Comparison

State-level analysis uses dynamic measures rather than hard-coded state names.

This allows the dashboard to identify the highest and lowest observations directly from the data.

The general analytical pattern is:

```text
Highest State
→ state associated with maximum CoHD

Lowest State
→ state associated with minimum CoHD
```

This approach ensures that the insight changes if the underlying dataset or filter context changes.


# 14. Urban vs Rural Analysis

The `Urban_Rural` table provides estimated healthy-diet costs for urban and rural areas.

The primary comparison is:

```text
Urban-Rural Gap =
Urban CoHD − Rural CoHD
```

Interpretation:

* Positive value → urban estimated cost is higher
* Negative value → rural estimated cost is higher
* Zero → costs are equal

The comparison is descriptive and should not automatically be interpreted as evidence that one population has greater or lower welfare.


# 15. Food Price Pressure Analysis

The `Food_Prices` table provides the May 2026 snapshot used on Page 04.

The analysis focuses on three dimensions:

### Current price

```text
Average of May-26
```

### Monthly movement

```text
MoM
```

### Annual movement

```text
YoY
```

This allows the dashboard to distinguish between:

* current price level
* recent monthly movement
* longer annual movement

# 16. Percentage-Point Handling

The Selected Food Price Watch source stores some movement values as percentage points.

For example:

```text
MoM = 3.63
```

means:

```text
3.63%
```

not:

```text
363%
```

Therefore, Power BI display measures divide the source value by 100 before applying percentage formatting.

Conceptually:

```text
Displayed MoM =
Source MoM / 100
```

and:

```text
Displayed YoY =
Source YoY / 100
```

This distinction was important in preventing percentage-formatting errors in the dashboard.


# 17. Food Price Geographic Context

The `Highest` and `Lowest` fields in `Food_Prices` contain state and price information as source text.

They are therefore treated as text rather than attempting to calculate numerical minimums or maximums from those fields.

The dashboard displays the source-provided state-price context directly.

This avoids incorrectly parsing combined text such as:

```text
State + Price
```

as a single numeric field.


# 18. Statistical Validation

An additional statistical check was performed to examine whether monthly Food CPI changes and monthly CoHD changes moved together during their common reporting period.

The common period was:

**May 2025 – April 2026**

This produced:

**12 monthly observations**

The following tests were performed:

### Pearson correlation

```text
r ≈ -0.04
p ≈ 0.90
```

### Spearman rank correlation

```text
ρ ≈ 0.06
p ≈ 0.86
```

Both tests provide no statistical evidence of a significant relationship within this 12-month sample.

However, the result should be interpreted carefully.

It does **not** prove that Food CPI and CoHD are unrelated in general.

It does **not** establish causation.

The test is an additional validation exercise for the observed common-period data.


# 19. Reporting-Period Alignment

The source datasets do not all end at the same date.

The dashboard therefore preserves the original reporting periods.

| Dataset                   | Latest period used |
| ------------------------- | ------------------ |
| National CPI              | May 2026           |
| State CPI                 | May 2026           |
| Selected Food Price Watch | May 2026           |
| Monthly CoHD              | April 2026         |

The project does not manufacture a May 2026 CoHD observation.

This distinction is important because using the latest available observation from each source does not necessarily mean every measure represents the same month.


# 20. Dashboard Interaction Logic

The dashboard was designed so that filters affect only visuals where the selected dimension is analytically relevant.

### Date filters

Date filters control time-series analysis.

Snapshot and cross-sectional visuals are not forced to behave as historical time series.

### State selection

State selection affects state-related analysis while avoiding unnecessary changes to national KPI cards.

### Food-item selection

Food-item selection affects supporting food-price analysis without changing unrelated national affordability indicators.

### Chart interactions

Where a chart does not provide useful analytical context for another visual, the interaction is disabled.

This reduces accidental cross-filtering and makes the dashboard behave more like an analytical application than a collection of independent charts.

---

# 21. Dynamic Insights

The dashboard uses DAX-generated text for selected analytical insights.

Examples include:

* Largest food-group driver
* Geographic affordability spread
* Urban/rural difference
* Food-price pressure
* Affordability divergence

The purpose of these measures is to make the narrative respond to the underlying data rather than relying on manually typed conclusions.


# 22. Interpretation Framework

The project follows several rules when interpreting the results.

### Rule 1 — Inflation is not affordability

A change in Food CPI does not directly tell us what a household can afford.

### Rule 2 — CoHD is not household expenditure

CoHD represents an estimated lower-bound cost of meeting a healthy-diet standard.

### Rule 3 — Higher cost does not automatically mean lower welfare

State-level CoHD should not be interpreted without considering income and purchasing power.

### Rule 4 — Correlation does not imply causation

Observed relationships between indicators should not be presented as causal mechanisms without appropriate evidence.

### Rule 5 — Snapshot data remains snapshot data

A single reporting period should not be presented as a historical trend.


# 23. Limitations

The analysis does not directly include:

* Household income
* Household-level food expenditure
* Household size
* Individual dietary preferences
* Food waste
* Transport expenditure
* Meal preparation costs
* Household purchasing behaviour
* Poverty rates
* Food insecurity measurements

Consequently, the dashboard should be interpreted as:

> **food-price and healthy-diet cost intelligence**

rather than as a direct measurement of household food insecurity.



# 24. Reproducibility

The project follows this analytical pipeline:

```text
Official NBS datasets
        ↓
Source inspection
        ↓
Data cleaning and standardisation
        ↓
Power BI analytical tables
        ↓
Data model
        ↓
DAX measures
        ↓
Visual analysis
        ↓
Statistical validation
        ↓
Dashboard interpretation
```

The repository documents the analytical structure so another analyst can understand how the dashboard was constructed.

Raw NBS files are not redistributed unless their applicable terms permit redistribution.


# 25. Final Analytical Position

The central conclusion of the methodology is that **food affordability cannot be understood from a single inflation number**.

Food CPI provides information about price movement.

CoHD provides information about the estimated cost of meeting a healthy-diet standard.

Food-group analysis shows what drives that cost.

State-level analysis shows geographic variation.

Food-price analysis shows that individual items can behave very differently from the broader food basket.

Taken together, these measures provide a more useful framework for understanding food affordability than headline food inflation alone.

**The objective is not simply to show that food prices change. It is to understand what those changes mean for the cost of maintaining a healthy diet.**
