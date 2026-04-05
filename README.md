# LFG-Energy-Analysis-Using-SQL-and-Tableau

<p align="center">
  <img src="https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=sqlite&logoColor=white"/>
  <img src="https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white"/>
  <img src="https://img.shields.io/badge/Tableau-E97627?style=for-the-badge&logo=tableau&logoColor=white"/>
  <img src="https://img.shields.io/badge/EPA%20Data-00A651?style=for-the-badge&logoColor=white"/>
</p>


# 🔵 Key techniques used in this project include:

🔹 Data extraction and transformation using SQL in DB Browser for SQLite

🔹 Aggregation and grouping using GROUP BY, COUNT DISTINCT, and SUM

🔹 Conditional logic using CASE WHEN for calculated metrics

🔹 Data type handling using CAST for mixed type numeric columns

🔹 NULL handling using NULLIF to prevent division by zero errors

🔹 Master dataset creation using CREATE TABLE AS for Tableau integration

🔹 Dashboard development in Tableau Public with 3 connected views


# 🔵 Project Overview

Landfill gas (LFG) is methane produced when organic waste breaks down in landfills.
Left uncaptured, methane is a greenhouse gas 80 times more potent than CO2 in the
short term. LFG energy projects capture this gas and convert it into electricity,
heat, or Renewable Natural Gas (RNG), reducing emissions and generating clean energy.

Despite the scale of this opportunity, significant gaps exist between the gas being
generated at landfills and what is actually being captured and converted. Some states
and projects are performing far below their potential while others represent untapped
opportunity entirely.

This project analyzes EPA landfill gas energy data to answer 3 business questions:

- Which states have the biggest untapped LFG collection opportunity?
- How has LFG energy project growth trended over time and which technology
  types are scaling fastest?
- How efficiently are existing operational projects performing relative to
  their rated capacity and where are the biggest gaps?


# 🔵 Data Source

**EPA Landfill Methane Outreach Program (LMOP) Database**

The dataset contains 760 records covering active and historical landfill gas
energy projects across the United States. Key variables include:

- State, City, and County
- Landfill ID and Landfill Name
- Waste in Place (tons)
- LFG Collected (mmscfd) — gas captured for energy use
- LFG Flared (mmscfd) — gas burned off as waste
- Project Start Date
- Current Project Status
- Project Type Category — Electricity, Direct Use, Renewable Natural Gas
- LFG Energy Project Type — Reciprocating Engine, Gas Turbine, Vehicle Fuel etc
- Actual MW Generation — electricity currently being produced
- Rated MW Capacity — maximum electricity the project was designed to produce
- Current Year Emission Reductions (MMTCO2e/yr) — annual greenhouse gas
  reductions in million metric tons of CO2 equivalent

Source: https://www.epa.gov/lmop/lmop-landfill-and-project-database

# 🔵 Approach

The project follows a structured BI workflow from raw data to dashboard.

🔹 **Data Loading**

The detailed LMOP Excel file was converted to CSV and imported into
DB Browser for SQLite as a single table for SQL analysis.

🔹 **Exploratory Analysis**

Initial PRAGMA and SELECT queries were run to understand column names,
data types, and null patterns. Key data quality issues identified
before analysis including mixed type numeric columns stored as TEXT
requiring CAST conversion.

🔹 **Step 1 — State Level LFG Collection Efficiency Analysis**

Aggregated total landfills, waste in place, LFG collected, and LFG
flared by state. Calculated collection efficiency percentage as:

LFG Collected / (LFG Collected + LFG Flared) x 100

This measures what share of all available gas is being captured
productively rather than burned off as waste.

🔹 **Step 2 — LFG Energy Project Growth Trends Over Time**

Extracted project start year using SUBSTR on the Project Start Date
field. Grouped by year, Project Type Category, and LFG Energy Project
Type to track how new project activity and MW generation has trended
from 1982 to 2025.

🔹 **Step 3 — Operational Project Efficiency Gap by State**

Filtered to operational projects only. Calculated MW efficiency as:

Actual MW Generation / Rated MW Capacity x 100

Identified states where projects are generating significantly below
their rated design capacity.

🔹 **Master Dataset Creation**

A single master table was created using CREATE TABLE AS combining
all key metrics from Queries 1 and 3 at the state level. This single
CSV file powers all Tableau visualizations from one connected source.

# 🔵 Key Findings

🔹 **NC Has the Largest Untapped Opportunity by Scale**

North Carolina has the highest total waste in place at 3,605 tons
across 25 landfills but only 81% collection efficiency. At this scale
the uncaptured gas volume represents a significant daily methane loss.

🔹 **SC, WV, and MT Are the Worst Performing Operational States**

South Carolina at 27.1%, West Virginia at 36.2%, and Montana at 41.9%
MW efficiency are generating less than half their rated capacity despite
being classified as operational. These represent the highest priority
states for performance intervention.

🔹 **Renewable Natural Gas Is the Fastest Growing Segment**

RNG Vehicle Fuel projects grew from 1 to 5 new starts per year before
2018 to 8 to 15 new starts per year from 2018 onward. RNG Other
emerged as a new subcategory from 2021 onward suggesting continued
innovation in gas utilization methods.

🔹 **CA Leads in Total Emission Reductions**

California's 65 operational projects reduce 11.5 MMTCO2e of greenhouse
gas annually. Texas has only 33 projects but reduces 9.7 MMTCO2e
meaning Texas projects are more impactful per project on average.

🔹 **LA and SD Show 0% MW Efficiency Despite Operational Status**

Louisiana has 10 projects with rated capacity but zero actual MW
generation. South Dakota shows the same pattern. Both are flagged
as potential data quality issues or misclassification in the source.

# 🔵 Data Quality Notes

🔹 VT has a NULL collection efficiency score due to missing LFG
Flared data in the source dataset

🔹 LA and SD show 0% MW efficiency despite operational classification
and are flagged as potential source data anomalies

🔹 ME shows 102.2% MW efficiency which exceeds rated capacity and
is physically impossible — flagged as a source data error

All anomalies were retained in the dataset and documented rather
than removed to preserve data integrity and analytical transparency

# 🔵 Conclusion

Landfill gas energy represents a significant and measurable opportunity
to reduce greenhouse gas emissions while generating clean energy from
waste that already exists. This analysis shows that the gap between
potential and actual performance varies dramatically by state and
project type.

States like South Carolina and West Virginia are leaving more than half
their operational capacity unused while states like California and Texas
are demonstrating what high performance LFG energy infrastructure can
deliver at scale.

The shift toward Renewable Natural Gas since 2018 signals where the
industry is heading — and where measurement, monitoring, and performance
optimization will matter most in the coming decade.
