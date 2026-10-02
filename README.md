HealthStat: Hip Replacement Hospital Benchmarking Dashboard

An interactive Power BI report that benchmarks hospitals on length of stay (LOS) and cost per discharge for total hip replacement surgery. It helps users see which facilities and regions are more or less efficient than the statewide average, and what patient or hospital characteristics drive those differences.

Table of Contents
Business Questions
Report Pages
Data Source
Data Model
DAX Measures
Calculated Columns and Tables
Power Query Transformations
Visuals Used
How to Open the Project
Project Structure
Possible Next Steps
Business Questions
Which hospitals keep patients in the hospital longer than average after hip replacement, and by how much?
Which hospitals cost more or less per discharge than the statewide average?
Is there a relationship between hospital volume, LOS and cost?
Which factors (age, gender, diagnosis, severity, mortality risk, disposition, region, program size) most influence LOS and cost?
How does one selected hospital's profile compare with the overall benchmark?
Report Pages

The report has four pages with a shared navigation bar at the top.

#	Page	Purpose
1	Home	Landing page with the HealthStat logo and page navigation.
2	LOS Comparison	Compares average length of stay across hospitals and service areas.
3	Cost Comparison	Compares average cost per discharge across hospitals and service areas.
4	Hospital Profile	Single-facility view of a selected hospital against the overall benchmark.
LOS Comparison
Slicer: Health Service Area (dropdown)
KPI cards: Total Surgeons, Average LOS Days, Total Hospitals, Total Discharges
Combo chart (columns and line): Total Discharges by facility with Average LOS Days and Total Surgeons
Bar charts (top and bottom N): Average LOS Days and % variance vs. the overall average, by facility
Key Influencers: What drives higher Average LOS Days (explained by risk of mortality, severity of illness, diagnosis, disposition, gender, age bin, surgical program size and service area)
Cost Comparison
Slicer: Health Service Area (dropdown)
KPI cards: Average LOS Days, Average Cost per Discharge, Total Hospitals, Total Discharges
Scatter chart: Facilities plotted by Average LOS Days vs. Average Cost per Discharge, sized by Total Discharges and colored by service area, with average and percentile reference lines
Bar charts (top and bottom N): Average Cost per Discharge and % variance vs. the overall average, by facility
Key Influencers: What drives higher Average Cost per Discharge (same explanatory fields as above)
Hospital Profile
Slicer: Facility Name (dropdown)
Gauges: Selected hospital's Average LOS Days and Average Cost per Discharge against the overall (ALL) values
Charts for the selected facility:
Discharges by APR severity of illness (column chart)
Discharges by APR risk of mortality (column chart)
Discharges by CCS diagnosis (donut chart)
Discharges by patient disposition (donut chart)
Data Source
Item	Detail
Dataset	Hospital inpatient discharges for total hip replacement
Format	CSV loaded via Power Query (Web.Contents)
URL	https://assets.datacamp.com/production/repositories/6258/datasets/f5df95d11522455215a579dc5baeb0d57dd30caa/hospital_inpatient_discharges_totalhipreplacement.csv
Scope after filtering	26,286 discharges · 151 facilities · 8 health service areas · discharge year 2016
Geography	New York State health service areas (Capital/Adiron, Central NY, Finger Lakes, Hudson Valley, Long Island, New York City, Southern Tier, Western NY)

Key fields in the source include facility, county, patient demographics (age group, gender, race, ethnicity), length of stay, disposition, CCS and APR diagnosis and severity codes, provider license numbers, total_charges and total_costs.

Data Model
Table	Type	Description
hospital_discharges	Imported (Power Query)	Fact table with one row per discharge (26,286 rows).
surgical_program_size_summary	Calculated table (DAX)	One row per facility (151 rows) with total discharges, total surgeons and a surgical program size category. Joined to the fact table by facility_name.
_Measures	Calculated table (DAX)	Holder table for all report measures.
DateTableTemplate_…	Auto-generated	Hidden Power BI date template, not used by the report.
DAX Measures

All measures live in the _Measures table.

Measure	Logic	Description
Total Hospitals	DISTINCTCOUNT(hospital_discharges[facility_id])	Number of unique facilities in the current filter context.
Total Discharges	COUNTROWS(hospital_discharges)	Number of discharge records.
Average LOS Days	AVERAGE(hospital_discharges[length_of_stay])	Mean length of stay in days.
Total Surgeons	DISTINCTCOUNT(hospital_discharges[operating_provider_license_number])	Unique operating surgeons.
Average Cost per Discharge	DIVIDE(SUM(total_costs), [Total Discharges])	Total cost divided by discharges.
Average LOS Days ALL	CALCULATE([Average LOS Days], ALL())	Overall benchmark LOS, ignoring all filters.
Average Cost per Discharge ALL	CALCULATE([Average Cost per Discharge], ALL())	Overall benchmark cost, ignoring all filters.
% Var Average LOS Days	([Average LOS Days] - [LOS ALL]) / [LOS ALL]	Percent difference from the overall LOS average.
% Var Average Cost per Discharge	([Avg Cost] - [Avg Cost ALL]) / [Avg Cost ALL]	Percent difference from the overall cost average.
Title Selected Facility	"Hospital Profile: " & VALUES(facility_name)	Dynamic title text for the selected hospital.
Calculated Columns and Tables

hospital_discharges[Age Bins]: groups patients into two bands.

dax
IF(
    OR(
        hospital_discharges[age_group] = "50 to 69",
        hospital_discharges[age_group] = "70 or Older"
    ),
    "Age 50+",
    "Age <50"
)

surgical_program_size_summary (calculated table): facility-level rollup.

dax
SUMMARIZECOLUMNS(
    Hospital_Discharges[facility_name],
    "Total Discharges", [Total Discharges],
    "Total Surgeons", [Total Surgeons]
)

surgical_program_size_summary[Total Discharges (bins)]: buckets volume in steps of 200.

dax
IF(
  ISBLANK('surgical_program_size_summary'[Total Discharges]),
  BLANK(),
  INT('surgical_program_size_summary'[Total Discharges] / 200) * 200
)

surgical_program_size_summary[Surgical Program Size]: labels each hospital's program size.

Annual discharges	Label
0 to 199	<200
200 to 399	200-399
400 to 599	400-599
600 and above	>=600
Power Query Transformations

The hospital_discharges query performs these steps:

Source: read the CSV from the web (delimiter ,, 30 columns, Windows-1252 encoding).
Promoted Headers: use the first row as column names.
Changed Type: set data types for all 30 columns (text, whole number, decimal for total_charges and total_costs).
Filtered Rows: keep only ccs_procedure_description = "HIP REPLACEMENT,TOT/PRT".
Visuals Used
Visual	Count	Where
Card	8	LOS Comparison, Cost Comparison
Clustered bar chart	4	LOS Comparison, Cost Comparison
Key influencers	2	LOS Comparison, Cost Comparison
Slicer (dropdown)	3	All analysis pages
Page navigator	4	All pages
Gauge	2	Hospital Profile
Donut chart	2	Hospital Profile
Column / clustered column chart	2	Hospital Profile
Line and stacked column combo	1	LOS Comparison
Scatter chart	1	Cost Comparison
Text box	2	Titles

Elman Gasimov, https://www.linkedin.com/in/elman-gasimov-56331a124/.
