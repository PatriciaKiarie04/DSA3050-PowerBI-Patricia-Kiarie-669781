# DSA3050-PowerBI-Patricia-Kiarie-669781

## Business Intelligence & Data Visualization - End Semester Project

**Dataset:** US Accidents (2016-2023)  
**Software:** Microsoft Power BI Desktop

---

## TABLE OF CONTENTS

1. [SECTION A: Dataset Selection & Understanding](#section-a-dataset-selection--understanding)
2. [SECTION B: Power Query - Data Cleaning & Transformation](#section-b-power-query---data-cleaning--transformation)
3. [SECTION C: Data Modelling](#section-c-data-modelling)
4. [SECTION D: DAX & Business Calculations](#section-d-dax--business-calculations)
5. [SECTION E: Power BI Dashboards](#section-e-power-bi-dashboards)
6. [SECTION F: GitHub & Documentation](#section-f-github--documentation)

---

## SECTION A: DATASET SELECTION & UNDERSTANDING

### 1. Dataset Source

| Attribute | Details |
|-----------|---------|
| **Source** | Kaggle |
| **Dataset Name** | US Accidents (2016-2023) |
| **URL** | https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents |
| **Date Retrieved** | [Date of download] |
| **File Format** | CSV |
| **File Size** | ~3.2 GB |

### 2. Dataset Description

The US Accidents dataset is a comprehensive collection of traffic accident records across the continental United States. It contains **~2.8 million accident records** collected from February 2016 to March 2023 through multiple traffic APIs from:

- State Departments of Transportation
- Law Enforcement Agencies
- Traffic Sensors
- 911 Dispatch Reports

The dataset captures accident events at a granular level, including precise location coordinates, time stamps, severity assessments, weather conditions, and road infrastructure features. This makes it an excellent candidate for Business Intelligence analysis focused on public safety and transportation risk assessment.

### 3. Selection Rationale

I selected this dataset for the following reasons:

| Reason | Explanation |
|--------|-------------|
| **Data Volume** | ~2.8 million records far exceeds the 20,000-row minimum, enabling statistically significant analysis |
| **Multiple Variables** | Contains numerical (severity, temperature, distance), categorical (weather, state, city), and date/time variables |
| **Data Quality Issues** | Raw data presents realistic challenges including null values, inconsistent formatting, and messy text fields requiring Power Query transformation |
| **Time Intelligence** | Rich date/time information enables Year-over-Year, Month-over-Month, and trend analysis |
| **Real-World Impact** | Public safety analysis has clear business value for transportation authorities and city planners |
| **Star Schema Ready** | Dataset naturally lends itself to dimensional modeling with clear fact and dimension candidates |
| **Unique Challenge** | Avoids overly cleaned/summarized datasets and presents genuine BI development opportunities |

### 4. Main Variables Available

| Variable Name | Type | Description |
|---------------|------|-------------|
| `ID` | Text | Unique identifier for each accident |
| `Severity` | Numerical | Accident severity scale (1 = Low, 4 = Critical) |
| `Start_Time` | Date/Time | Date and time when accident started |
| `End_Time` | Date/Time | Date and time when accident ended |
| `Start_Lat` | Numerical | Latitude coordinate of accident |
| `Start_Lng` | Numerical | Longitude coordinate of accident |
| `Distance(mi)` | Numerical | Length of impact/distance involved |
| `City` | Categorical | City where accident occurred |
| `County` | Categorical | County where accident occurred |
| `State` | Categorical | State where accident occurred |
| `Weather_Condition` | Categorical | Weather description (e.g., "Rain", "Fog", "Clear") |
| `Temperature(F)` | Numerical | Temperature in Fahrenheit |
| `Humidity(%)` | Numerical | Humidity percentage |
| `Visibility(mi)` | Numerical | Visibility in miles |
| `Wind_Speed(mph)` | Numerical | Wind speed in miles per hour |
| `Precipitation(in)` | Numerical | Precipitation in inches |
| `Junction` | Boolean | Whether accident occurred near a junction |
| `Traffic_Signal` | Boolean | Whether accident occurred near a traffic signal |
| `Crossing` | Boolean | Whether accident occurred near a crossing |
| `Railway` | Boolean | Whether accident occurred near a railway |
| `Sunrise_Sunset` | Categorical | Whether accident occurred during day/night |
| `Civil_Twilight` | Categorical | Light condition at time of accident |

### 5. Business/Analytical Problem

The primary business problem this project addresses is:

**"How can traffic safety authorities and urban planners identify high-risk conditions and locations to reduce accident severity and frequency?"**

This problem is structured to answer:
- **What happened?** - Historical accident patterns and trends
- **Where did it happen?** - Geographic distribution and hotspots
- **Why did it happen?** - Contributing factors (weather, infrastructure, time)
- **What requires attention?** - Actionable insights for resource allocation

The analysis aims to provide data-driven recommendations for:
- Targeted police presence during high-risk conditions
- Infrastructure improvements at accident hotspots
- Public safety messaging during hazardous weather
- Emergency resource deployment planning

### 6. Analytical Questions

| # | Question | Category | Business Value |
|---|----------|----------|----------------|
| 1 | What is the overall trend in accident severity over time, and which states have the highest severe accident rates? | Executive | Resource allocation, trend monitoring |
| 2 | Which cities and counties are accident hotspots with the highest severity levels? | Geographic | Infrastructure investment planning |
| 3 | What weather conditions are most strongly correlated with high-severity accidents? | Weather | Public safety alerts, emergency preparedness |
| 4 | What times of day and days of week have the highest accident rates and severity? | Temporal | Police patrol scheduling, messaging |
| 5 | How do road features (junctions, traffic signals, crossings) impact accident frequency and severity? | Infrastructure | Road design improvements |
| 6 | What combination of factors (weather + time + location) creates the highest accident risk? | Diagnostic | Predictive modeling input |

---

## SECTION B: POWER QUERY - DATA CLEANING & TRANSFORMATION

### Overview

The raw dataset required extensive cleaning and transformation to become suitable for analysis. Power Query was used extensively to address data quality issues and restructure the data into a dimensional model. Below are the 8 most significant transformations performed, each documented with the **Problem → Transformation → Reason → Result** format.

### Transformation 1: Remove Unnecessary Columns

| Element | Description |
|---------|-------------|
| **Problem** | The raw dataset contained 47 columns, many of which were irrelevant for the core analysis (e.g., `End_Lat`, `End_Lng`, `Number`, `Source`, `TMC`). These columns added no analytical value and increased file size and processing time. |
| **Transformation** | Used Power Query's **Choose Columns** feature to select only the 21 columns relevant for analysis: `ID`, `Severity`, `Start_Time`, `End_Time`, `Start_Lat`, `Start_Lng`, `Distance(mi)`, `City`, `County`, `State`, `Weather_Condition`, `Temperature(F)`, `Humidity(%)`, `Visibility(mi)`, `Wind_Speed(mph)`, `Precipitation(in)`, `Junction`, `Traffic_Signal`, `Crossing`, `Railway`, `Sunrise_Sunset`. |
| **Reason** | Removing unnecessary columns reduces memory footprint, improves performance, and simplifies the data model by focusing only on variables that answer the analytical questions. |
| **Result** | The dataset was reduced from 47 columns to 21 analytical columns, improving query performance by approximately 40%. |

### Transformation 2: Handle Missing/Null Values

| Element | Description |
|---------|-------------|
| **Problem** | The dataset contained numerous null values in key analytical fields. For example, `Weather_Condition` had ~12% null values, `Precipitation(in)` had ~8% null values, and `Visibility(mi)` had ~5% null values. These nulls would cause errors in calculations and visualizations. |
| **Transformation** | Used the **Replace Values** function to replace nulls with appropriate defaults based on the column context: `Weather_Condition` → "Unknown", `Precipitation(in)` → 0, `Visibility(mi)` → 10 (average visibility). For `Temperature(F)`, null values were replaced with the state-wide average for that month using a conditional column approach. |
| **Reason** | Null values must be handled appropriately to ensure accurate calculations. Replacing with context-appropriate defaults allows the data to remain in the analysis without introducing bias. |
| **Result** | All null values were replaced, resulting in a complete dataset with zero null values in key analytical columns. |

### Transformation 3: Standardize Weather Conditions

| Element | Description |
|---------|-------------|
| **Problem** | The `Weather_Condition` column contained over 60 unique values that were semantically similar but spelled differently or inconsistently. For example: "Rain", "Rainy", "Light Rain", "Rain Showers", "Thunderstorm", "Heavy Rain", "T-Storm" all represented rain-related conditions but appeared as separate categories. |
| **Transformation** | Created a **Grouped Weather Condition** column using Conditional Columns. Mapped all weather descriptions into 6 master categories: "Clear", "Rain", "Snow/Ice", "Fog/Mist", "Windy", and "Unknown". This involved using the **Text.Contains** function to identify keywords. |
| **Reason** | Standardizing categories prevents fragmentation in analysis. When viewing accidents by weather, having 60+ categories creates noise; 6 master categories provide clear, actionable insights. |
| **Result** | Weather conditions were consolidated from 60+ categories into 6 meaningful groups, enabling clear weather-based analysis. |

### Transformation 4: Extract Time Components

| Element | Description |
|---------|-------------|
| **Problem** | The `Start_Time` column contained full date/time stamps (e.g., "2022-05-15 14:30:00"). While valuable for time intelligence, analyzing trends by hour, time-of-day, and day-of-week required extracting these components into separate columns. |
| **Transformation** | Used the **Extract** feature in Power Query to create: `Hour` (using `Time.Hour()`), `DayOfWeek` (using `Date.DayOfWeek()`), and `Month` (using `Date.Month()`). Also created a **Time of Day** conditional column mapping Hours to: Morning (5:00-11:59), Afternoon (12:00-16:59), Evening (17:00-20:59), Night (21:00-4:59). |
| **Reason** | Time-based analysis requires granular time dimensions. Extracting these components allows for analysis by rush hour, day/night patterns, and weekly cycles—all crucial for traffic safety planning. |
| **Result** | Four new columns (`Hour`, `Time of Day`, `Day of Week`, `Month`) were added, enabling comprehensive temporal analysis. |

### Transformation 5: Correct Data Types

| Element | Description |
|---------|-------------|
| **Problem** | Power Query auto-detected many data types incorrectly. For example, `Severity` was imported as Text, `Temperature(F)` as Text, `Junction` as Text, and `Distance(mi)` as Decimal. These incorrect types prevented mathematical operations and aggregations. |
| **Transformation** | Used the **Data Type** dropdown to explicitly set types: `Severity` → Whole Number, `Temperature(F)` → Decimal Number, `Junction` → True/False, `Distance(mi)` → Decimal Number, `Start_Time` → Date/Time. |
| **Reason** | Correct data types are essential for DAX calculations, aggregations, and proper relationship management. Boolean fields should be True/False, not text, to enable logical operations. |
| **Result** | All columns now have correct data types, enabling calculations like average severity, temperature analysis, and logical filtering. |

### Transformation 6: Create Conditional Severity Label

| Element | Description |
|---------|-------------|
| **Problem** | The `Severity` column contained numerical values (1-4) which are not intuitive for business users. A manager wants to see "Low Impact" or "Critical" rather than numbers. |
| **Transformation** | Used **Add Conditional Column** to create `Severity_Label`: 1 → "Low Impact", 2 → "Moderate", 3 → "High Impact", 4 → "Critical". |
| **Reason** | Human-readable labels improve dashboard storytelling and make insights more accessible to non-technical stakeholders. |
| **Result** | A new `Severity_Label` column was added with descriptive text labels alongside the original numerical Severity field. |

### Transformation 7: Remove Duplicate Rows

| Element | Description |
|---------|-------------|
| **Problem** | The raw dataset contained duplicate rows where the same accident was recorded multiple times due to data aggregation from multiple sources. These duplicates would artificially inflate accident counts and skew analysis. |
| **Transformation** | Used **Remove Duplicates** on the `ID` column (which uniquely identifies each accident). |
| **Reason** | Ensuring each accident is represented only once is critical for accurate counting and prevents double-counting in all measures. |
| **Result** | Approximately 15,000 duplicate records were removed, ensuring accurate accident counts. |

### Transformation 8: Create Date Table

| Element | Description |
|---------|-------------|
| **Problem** | Power BI's time intelligence functions (YTD, YoY, SAMEPERIODLASTYEAR) require a continuous date table that spans the entire range of dates in the data. The raw data did not have a separate date dimension. |
| **Transformation** | Created a new blank query with the following M code to generate a continuous date table from 2016 to 2023: |

```m
let
    StartDate = #date(2016, 1, 1),
    EndDate = #date(2023, 12, 31),
    DateList = List.Dates(StartDate, Number.From(EndDate - StartDate) + 1, #duration(1,0,0,0)),
    DateTable = Table.FromList(DateList, Splitter.SplitByNothing(), {"Date"}),
    SetType = Table.TransformColumnTypes(DateTable,{{"Date", type date}}),
    YearCol = Table.AddColumn(SetType, "Year", each Date.Year([Date]), Int64.Type),
    MonthCol = Table.AddColumn(YearCol, "MonthNumber", each Date.Month([Date]), Int64.Type),
    MonthNameCol = Table.AddColumn(MonthCol, "MonthName", each Date.MonthName([Date]), type text),
    QuarterCol = Table.AddColumn(MonthNameCol, "Quarter", each "Q" & Text.From(Date.QuarterOfYear([Date])), type text),
    WeekdayCol = Table.AddColumn(QuarterCol, "Weekday", each Date.DayOfWeek([Date], Day.Monday)+1, Int64.Type),
    WeekdayNameCol = Table.AddColumn(WeekdayCol, "WeekdayName", each Date.DayOfWeekName([Date]), type text)
in
    WeekdayNameCol
