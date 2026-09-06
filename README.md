# Food Affordability Intelligence — Nigeria

> **What does it actually cost to eat a healthy diet in Nigeria?**

Food prices are rising. But looking at food inflation alone doesn't tell the full story.

This project explores food affordability in Nigeria by combining **Food CPI, selected food prices, and the Cost of a Healthy Diet (CoHD)** to understand how the cost of maintaining a healthy diet changes, what drives that cost, and where affordability pressures are most visible.

The analysis is built entirely around official data published by the **National Bureau of Statistics (NBS), Nigeria**.

## What this project is about

The central question behind this project is simple:

**If food prices are changing, what does that actually mean for the cost of eating a healthy diet?**

To answer that, I looked at affordability from five different angles:

* How the estimated cost of a healthy diet changes over time
* What food groups contribute most to that cost
* How healthy-diet costs vary across Nigerian states
* Which individual food items are experiencing the strongest price pressure
* What the combined evidence suggests about food affordability

This is deliberately more than a collection of charts. The dashboard is structured as an **economic intelligence story**.

# Dashboard

The Power BI dashboard is organised into five analytical pages.

### 01 — Affordability Pulse

The executive overview.

It looks at:

* National healthy-diet cost
* Monthly movement
* Food CPI movement
* Changes in affordability over time
* State-level cost context

The purpose is to answer:

> **Is the cost of a healthy diet moving differently from broader food-price pressure?*

### 02 — The Cost of a Healthy Diet

This page looks inside the healthy-diet basket.

It examines:

* Total estimated daily healthy-diet cost
* Food-group cost contribution
* Cost concentration
* Food-group shares
* Largest cost driver
* Urban vs rural healthy-diet cost

The analysis shows that **animal-source foods account for approximately 40.4%** of the represented daily healthy-diet cost, making them the largest cost component in the basket.

### 03 — Where Affordability Breaks

Food affordability is not uniform across Nigeria.

This page compares estimated healthy-diet costs across states and highlights:

* Highest-cost states
* Lowest-cost states
* National average
* State affordability spread

In the available state-level data:

* **Ekiti:** approximately ₦2,036 per adult per day
* **Adamawa:** approximately ₦1,143 per adult per day

That creates an estimated difference of approximately **₦893 per adult per day**.

The difference is treated as a geographic cost variation, not as a direct measure of poverty or household welfare.

### 04 — Price Pressure Lab

This page moves from the overall food basket to individual food items.

The May 2026 Selected Food Price Watch snapshot is used to examine:

* Current food-price levels
* Strongest annual price movements
* Latest monthly movers
* Monthly vs annual price pressure
* State-level high and low prices

This helps identify where food-price pressure is concentrated rather than treating all food items as if they are moving in the same way.

### 05 — Policy & Insight

The final page brings the analysis together.

Instead of simply adding more charts, this page focuses on what the evidence suggests about:

1. Diet composition
2. Geographic targeting
3. Food-price monitoring
4. The difference between headline food inflation and healthy-diet affordability

The objective is to turn the analysis into something decision-makers can actually interpret.

# Key findings

### Healthy-diet cost

The latest CoHD observation used in the dashboard is approximately:

**₦1,589 per adult per day**

This represents the estimated lower-bound cost of meeting the healthy-diet standard. It should not be interpreted as the amount every Nigerian household actually spends on food.

### Largest cost component

**Animal-source foods represent approximately 40.4%** of the represented daily healthy-diet cost.

This makes them the largest cost component in the healthy-diet basket analysed.

### Geographic variation

The estimated cost of a healthy diet varies substantially between states.

**Ekiti — ₦2,036/day**

vs.

**Adamawa — ₦1,143/day**

A difference of approximately:

**₦893/day**

### Food-price pressure

The May 2026 food-price snapshot shows that price pressure differs considerably between individual food items.

Some items experience relatively strong annual movements while others remain comparatively stable or decline.

This reinforces an important point:

> **Food inflation is not experienced equally across every food item.**


# Methodology

The analysis follows this workflow:

**Official NBS data → data preparation → Power BI data model → DAX measures → comparative analysis → dashboard → interpretation**

The methodology covers:

* Source-data selection
* Data cleaning and standardisation
* Power BI data modelling
* DAX calculations
* Food CPI and CoHD indexing
* Affordability divergence
* Food-group analysis
* Geographic analysis
* Urban/rural comparison
* Food-price analysis
* Statistical testing
* Reporting-period alignment
* Limitations and interpretation rules

The full methodology is available here:

[`methodology/methodology.md`](methodology/methodology.md)


# A note on Food CPI vs CoHD

One of the most important methodological decisions in this project was **not to treat Food CPI and CoHD as the same thing**.

Food CPI is an index that measures changes in food prices.

CoHD is a monetary estimate of the least expensive combination of foods required to meet a healthy-diet standard.

Because they measure different things, their raw values should not be directly compared.

Instead, the project indexes both series to their selected starting observation and compares their relative movement.

### Food CPI Index

```text
Current Food CPI / Starting Food CPI × 100
```

### CoHD Index

```text
Current CoHD / Starting CoHD × 100
```

The difference between the two indexed series is used as an **affordability divergence signal**.

This is descriptive analysis, not proof of causation.

# Statistical check

I also performed an additional statistical check using the common **May 2025–April 2026** period.

The analysis compared monthly Food CPI movements with monthly CoHD movements across 12 observations.

Results:

* **Pearson r:** approximately -0.04
* **Pearson p-value:** approximately 0.90
* **Spearman ρ:** approximately 0.06
* **Spearman p-value:** approximately 0.86

Within this 12-month sample, there is **no statistical evidence of a significant relationship** between monthly Food CPI changes and monthly CoHD changes.

This should not be interpreted as proof that the two measures are unrelated in general.

It also does not establish causality.

It is included as an additional analytical check rather than the main conclusion of the project.


# Data periods

The source datasets do not all end in the same month.

| Dataset                   | Period used                               |
| ------------------------- | ----------------------------------------- |
| National CPI              | January 2023 – May 2026                   |
| State CPI                 | May 2025 / April 2026 / May 2026 snapshot |
| Cost of a Healthy Diet    | April 2025 – April 2026                   |
| Selected Food Price Watch | May 2026 snapshot                         |

The project deliberately preserves these reporting periods.

I did **not** create a May 2026 CoHD value where the source data only provides April 2026.

This avoids creating false temporal precision.


# Data sources

The project uses three primary NBS data products:

### Consumer Price Index

**National Bureau of Statistics (NBS), Nigeria**

Used for Food CPI, general CPI, monthly/annual movements, and CPI comparisons.

### Selected Food Price Watch

Dataset identifier:

`NGA-NBS-FOODPW`

Used for the May 2026 food-item price analysis.

### Cost of a Healthy Diet

Dataset identifier:

`NGA-NBS-COHD`

Used for national, state, urban/rural, food-group, and affordability analysis.

# Important limitations

This project is **not a household food-expenditure survey**.

CoHD is an estimated lower-bound cost of meeting a healthy-diet standard. It does not directly measure:

* Household income
* Actual household food expenditure
* Household size
* Food insecurity
* Poverty
* Individual dietary preferences
* Food waste
* Transport costs
* Meal preparation costs
* Household purchasing behaviour

Similarly, a state with a higher estimated CoHD should not automatically be interpreted as a state with worse household welfare.

Income and purchasing power matter, but they are outside the scope of this dataset.


# Power BI model

The main analytical tables in the Power BI model are:

| Table                 | Purpose                               |
| --------------------- | ------------------------------------- |
| `Dim_Date`            | Date dimension                        |
| `Fact_Affordability`  | National monthly affordability series |
| `Food_Groups`         | Healthy-diet food-group composition   |
| `Food_Prices`         | Selected food-price snapshot          |
| `State_Affordability` | State-level healthy-diet cost         |
| `State_CPI`           | State-level CPI                       |
| `Urban_Rural`         | Urban/rural healthy-diet comparison   |

The dashboard uses DAX measures to calculate the analytical indicators and dynamic insights shown throughout the five pages.


# Tools

* Microsoft Power BI
* DAX
* Power Query
* Microsoft Excel
* Data modelling
* Data cleaning
* Statistical analysis
* Data visualization


# Why I built this

I didn't want this project to be another dashboard that simply says:

**"Food inflation is rising."**

That's already easy to find.

I wanted to dig into what sits underneath that headline.

What does a healthy diet actually cost?

What is driving that cost?

Does the pressure look the same across states?

Which food items are moving the most?

And does the movement in food prices necessarily translate into the same movement in the estimated cost of a healthy diet?

Those questions shaped the entire dashboard.

The result is an attempt to move from **reporting numbers** to **understanding what the numbers mean**.


## Author

**Akpan Daniel**

Data Analyst | Power BI | Python | SQL | Data Visualization

This project is part of my professional data analytics portfolio.
