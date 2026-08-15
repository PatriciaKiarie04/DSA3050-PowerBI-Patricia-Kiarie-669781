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

---

## SECTION A: DATASET SELECTION & UNDERSTANDING

### 1. Dataset Source

| Attribute | Details |
|-----------|---------|
| **Source** | Kaggle |
| **Dataset Name** | US Accidents (2016-2023) |
| **URL** | https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents |
| **Date Retrieved** | August 2026 |
| **File Format** | CSV |
| **File Size** | ~3 GB |

### 2. Dataset Description

The US Accidents dataset is a comprehensive collection of traffic accident records across the continental United States. It contains **~2.8 million accident records** collected from February 2016 to March 2023 through multiple traffic APIs from:

- State Departments of Transportation
- Law Enforcement Agencies
- Traffic Sensors
- 911 Dispatch Reports

### 3. Selection Rationale

| Reason | Explanation |
|--------|-------------|
| **Data Volume** | ~2.8 million records far exceeds the 20,000-row minimum |
| **Multiple Variables** | Contains numerical, categorical, and date/time variables |
| **Data Quality Issues** | Presents realistic challenges requiring Power Query transformation |
| **Time Intelligence** | Rich date/time information enables trend analysis |
| **Real-World Impact** | Public safety analysis has clear business value |

### 4. Main Variables Available

| Variable Name | Type | Description |
|---------------|------|-------------|
| `ID` | Text | Unique accident identifier |
| `Severity` | Numerical | Accident severity (1-4 scale) |
| `Start_Time` | Date/Time | Accident start timestamp |
| `End_Time` | Date/Time | Accident end timestamp |
| `Start_Lat` | Numerical | Latitude coordinate |
| `Start_Lng` | Numerical | Longitude coordinate |
| `Distance(mi)` | Numerical | Impact distance |
| `City` | Categorical | City where accident occurred |
| `County` | Categorical | County where accident occurred |
| `State` | Categorical | State where accident occurred |
| `Weather_Condition` | Categorical | Weather description |
| `Temperature(F)` | Numerical | Temperature in Fahrenheit |
| `Humidity(%)` | Numerical | Humidity percentage |
| `Visibility(mi)` | Numerical | Visibility in miles |
| `Wind_Speed(mph)` | Numerical | Wind speed |
| `Junction` | Boolean | Near a junction |
| `Traffic_Signal` | Boolean | Near a traffic signal |
| `Crossing` | Boolean | Near a crossing |
| `Railway` | Boolean | Near a railway |
| `Sunrise_Sunset` | Categorical | Day/Night indicator |

### 5. Business/Analytical Problem

**"How can traffic safety authorities identify high-risk conditions and locations to reduce accident severity and frequency?"**

The analysis aims to provide data-driven recommendations for:
- Targeted police presence during high-risk conditions
- Infrastructure improvements at accident hotspots
- Public safety messaging during hazardous weather
- Emergency resource deployment planning

### 6. Analytical Questions

| # | Question | Category |
|---|----------|----------|
| 1 | What is the overall trend in accident severity over time, and which states have the highest severe accident rates? | Executive |
| 2 | Which cities are accident hotspots with the highest severity levels? | Geographic |
| 3 | What weather conditions are most strongly correlated with high-severity accidents? | Weather |
| 4 | What times of day have the highest accident rates and severity? | Temporal |
| 5 | How do road features impact accident frequency and severity? | Infrastructure |
| 6 | What combination of factors creates the highest accident risk? | Diagnostic |

---

## SECTION B: POWER QUERY - DATA CLEANING & TRANSFORMATION

### Overview

A total of **10 significant transformations** were performed using Power Query to clean and prepare the data for analysis.

### Transformation Documentation

| # | Problem | Transformation | Reason | Result |
|---|---------|----------------|---------|--------|
| 1 | 47 columns, many irrelevant for analysis | Removed unnecessary columns (End_Lat, End_Lng, Number, Source, TMC, etc.) | Reduce file size and simplify model | Dataset reduced to 30 columns |
| 2 | Incorrect data types | Changed data types: Severity→Whole Number, Temperature→Decimal, Boolean columns→True/False | Enable mathematical operations | All columns have correct types |
| 3 | ~15,000 duplicate records | Removed duplicates based on ID column | Prevent double-counting | Clean, unique accident records |
| 4 | Null values in key columns | Replaced nulls: Weather_Condition→"Unknown", Precipitation→0, Visibility→10 | Enable accurate calculations | Complete dataset with no nulls |
| 5 | 60+ inconsistent weather descriptions | Created conditional column Weather_Group (Rain, Snow/Ice, Fog/Mist, Windy, Clear, Unknown) | Enable clear weather analysis | 7 meaningful weather categories |
| 6 | Need time analysis | Extracted Hour, Day of Week, Month, Year from Start_Time | Enable temporal analysis | Time components available |
| 7 | Need broader time periods | Created Time_Period column (Morning, Afternoon, Evening, Night) using conditional column | Enable rush hour analysis | Accidents grouped by time period |
| 8 | Severity numbers not intuitive | Created Severity_Label column (Low Impact, Moderate, High Impact, Critical) | Improve dashboard readability | Human-readable severity labels |
| 9 | Need infrastructure analysis | Created Infrastructure_Count column (sum of Junction, Traffic_Signal, Crossing, Railway) | Assess infrastructure impact | Infrastructure risk score |
| 10 | Need time intelligence | Created DimDate table with continuous dates (2016-2023) using M code | Enable YTD and trend analysis | Complete date dimension |

### Screenshots

| # | Screenshot | Description |
|---|------------|-------------|
| 1 | [01_Raw_Data.png](Screenshots/01_Raw_Data.png) | Raw dataset preview |
| 2 | [02_PowerQuery_AppliedSteps.png](Screenshots/02_PowerQuery_AppliedSteps.png) | Power Query applied steps |
| 3 | [03_PowerQuery_Weather_Transformation.png](Screenshots/03_PowerQuery_Weather_Transformation.png) | Weather standardization |
| 4 | [04_Cleaned_Data.png](Screenshots/04_Cleaned_Data.png) | Final cleaned data |
| 5 | [05_Model_View.png](Screenshots/05_Model_View.png) | Data model view |
| 6 | [06_DAX_Measures.png](Screenshots/06_DAX_Measures.png) | DAX measures list |

---

## SECTION C: DATA MODELLING

### Star Schema Design

The data model follows a **Star Schema** design with one central Fact table surrounded by Dimension tables.

### Fact Table

| Attribute | Detail |
|-----------|--------|
| **Table Name** | `US_Accidents_March23` |
| **Granularity** | One row per accident event |
| **Row Count** | ~2.8 million records |
| **Purpose** | Contains quantitative measures to be analyzed |

### Dimension Tables

| Dimension | Description | Key Columns |
|-----------|-------------|-------------|
| `DimDate` | Calendar dates (2016-2023) | Date, Year, Month, Quarter |
| `DimSeverity` | Severity levels | Severity, Severity_Label, SeverityID |
| `DimWeather` | Weather conditions | Weather_Group, Weather_Condition |
| `DimLocation` | Geographic locations | City, County, State, LocationID |
| `DimTimeOfDay` | Time periods | Hour, Time_Period |

### Relationships

| Relationship | From (Fact) | To (Dimension) | Cardinality | Filter Direction |
|--------------|-------------|----------------|-------------|------------------|
| 1 | US_Accidents_March23[Severity] | DimSeverity[Severity] | Many to One | Single |
| 2 | US_Accidents_March23[Start_Time] | DimDate[Date] | Many to One | Single |
| 3 | US_Accidents_March23[Weather_Condition] | DimWeather[Weather_Condition] | Many to One | Single |
| 4 | US_Accidents_March23[City] | DimLocation[City] | Many to One | Single |

### Modelling Decisions

| Decision | Rationale |
|----------|-----------|
| **One-to-Many relationships** | Ensures proper filtering from dimensions to fact table |
| **Single filter direction** | Prevents ambiguous filter paths |
| **Surrogate keys** | Integer keys are more efficient for relationships |
| **Separate dimension tables** | Improves performance and clarity |

### Model View

![Model View](Screenshots/13_Model_View.png)

---

## SECTION D: DAX & BUSINESS CALCULATIONS

### Overview

A total of **12 DAX measures** were created across three levels of complexity.

### Level 1 - Core Measures

| # | Measure Name | DAX Formula | Purpose |
|---|--------------|-------------|---------|
| 1 | Total Accidents | `COUNTROWS(US_Accidents_March23)` | Count all accident records |
| 2 | Severe Accidents | `CALCULATE([Total Accidents], US_Accidents_March23[Severity] >= 3)` | Count high-impact accidents |
| 3 | Severity Rate | `DIVIDE([Severe Accidents], [Total Accidents], 0)` | Percentage of accidents that are severe |
| 4 | Avg Severity | `AVERAGE(US_Accidents_March23[Severity])` | Average severity score |
| 5 | Total Distance | `SUM(US_Accidents_March23[Distance(mi)])` | Total impact distance |

### Level 2 - Business Measures

| # | Measure Name | DAX Formula | Purpose |
|---|--------------|-------------|---------|
| 6 | City Accidents | `CALCULATE([Total Accidents], VALUES(US_Accidents_March23[City]))` | Accidents per city |
| 7 | Weather Impact | `CALCULATE([Severity Rate], VALUES(US_Accidents_March23[Weather_Group]))` | Severity rate by weather |
| 8 | Time Accidents | `CALCULATE([Total Accidents], VALUES(US_Accidents_March23[Time_Period]))` | Accidents by time period |
| 9 | Avg Distance by Severity | `CALCULATE(AVERAGE(US_Accidents_March23[Distance(mi)]), VALUES(US_Accidents_March23[Severity_Label]))` | Average distance by severity |
| 10 | Severe Accident % | `DIVIDE([Severe Accidents], [Total Accidents], 0)` | Percentage of severe accidents |
| 11 | Avg Temperature | `AVERAGE(US_Accidents_March23[Temperature(F)])` | Average temperature at accidents |

### Level 3 - Advanced Measures

| # | Measure Name | DAX Formula | Purpose |
|---|--------------|-------------|---------|
| 12 | Risk Indicator | `SWITCH(TRUE(), [Severity Rate] > 0.25, "High Risk", [Severity Rate] > 0.15, "Moderate Risk", [Severity Rate] > 0.05, "Low Risk", "Minimal Risk")` | Categorical risk level |

### Most Important Measures (Documentation)

#### 1. Total Accidents
- **Calculation**: `COUNTROWS(US_Accidents_March23)`
- **Purpose**: Foundation for all accident-based measures
- **DAX Functions**: COUNTROWS
- **Filter Context**: Responds to all filters
- **Usage**: KPI cards, trend lines, other measures

#### 2. Severity Rate
- **Calculation**: `DIVIDE([Severe Accidents], [Total Accidents], 0)`
- **Purpose**: Key safety performance indicator
- **DAX Functions**: DIVIDE, CALCULATE
- **Filter Context**: Changes with weather, location, time filters
- **Usage**: Weather impact analysis, city comparisons

#### 3. Risk Indicator
- **Calculation**: `SWITCH(TRUE(), [Severity Rate] > 0.25, "High Risk", ...)`
- **Purpose**: Identifies high-risk locations
- **DAX Functions**: SWITCH, TRUE
- **Filter Context**: Dynamic based on severity rate
- **Usage**: Diagnostic analysis, actionable insights

### DAX Measures Screenshot

![DAX Measures List](Screenshots/14_DAX_Measures.png)

---

## SECTION E: POWER BI DASHBOARDS

### Overview

Three professional dashboard pages were designed to tell a story from overview to detailed analysis to diagnostic insights.

### Page 1: Executive Overview

**Purpose:** Provide management with immediate understanding of accident performance.

**Story:** What happened? → Where did it happen?

**Visual Elements:**

| Visual | Type | Purpose |
|--------|------|---------|
| Total Accidents | KPI Card | Overall accident volume |
| Severe Accidents | KPI Card | High-impact incident monitoring |
| Severity Rate | KPI Card | Risk indicator |
| Risk Indicator | KPI Card | Current risk level |
| Monthly Trend | Line Chart | Time series pattern detection |
| Top 10 Cities | Bar Chart | Geographic performance ranking |
| Severity Distribution | Donut Chart | Distribution of severity levels |

**Interactivity:**
- Cross-filtering between all visuals
- Slicers: Year, Weather_Group, City

![Executive Overview](Screenshots/15_Dashboard_Page1_Executive_Overview.png)

---

### Page 2: Weather & Time Analysis

**Purpose:** Understand how weather and time factors influence accident patterns.

**Story:** When did it happen? → Under what conditions?

**Visual Elements:**

| Visual | Type | Purpose |
|--------|------|---------|
| Weather Impact | Bar Chart | Severity rate by weather |
| 24-Hour Pattern | Column Chart | Hourly accident distribution |
| Time of Day | Donut Chart | Morning/Afternoon/Evening/Night split |
| Day of Week | Bar Chart | Weekly pattern detection |

**Diagnostic Insights:**
- Rain and Snow/Ice show highest severity rates
- Evening rush hour (5-7 PM) has peak accident volume
- Nighttime accidents show higher severity

![Weather & Time Analysis](Screenshots/16_Dashboard_Page2_Weather_Time_Analysis.png)

---

### Page 3: Diagnostic Analysis

**Purpose:** Investigate why accidents happen and identify actionable insights.

**Story:** Why did it happen? → What requires attention?

**Visual Elements:**

| Visual | Type | Purpose |
|--------|------|---------|
| Weather vs Severity | Matrix | Weather-specific severity distribution |
| Temperature vs Severity | Scatter Plot | Temperature correlation analysis |
| High-Risk Locations | Table | Cities requiring attention |
| Key Insight | Card | Actionable recommendation |

**Diagnostic Findings:**
- Rain and Snow/Ice conditions have higher severity rates
- Evening hours show highest accident frequency
- High-risk cities identified for targeted intervention

![Diagnostic Analysis](Screenshots/17_Dashboard_Page3_Diagnostic_Analysis.png)

### Dashboard Design Principles

| Principle | Implementation |
|-----------|----------------|
| **Visual Hierarchy** | Largest visuals = most important insights |
| **Color Palette** | Professional blue/grey theme |
| **White Space** | Consistent padding between elements |
| **Consistent Formatting** | Same font family, sizes, colors across pages |
| **Interactivity** | Slicers, cross-filtering, dynamic titles |

---
