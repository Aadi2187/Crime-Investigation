# India Crime Analysis Dashboard (Power BI)

An interactive Power BI dashboard that explores about 40,000 crime records across 29 Indian cities from 2020 to 2024. It looks at where crime happens, who the victims are, which weapons are involved, how many cases get closed, and how police resources are deployed.

---

## 1. Project Overview

| Item | Details |
|---|---|
| Tool | Microsoft Power BI Desktop (Power Query + DAX) |
| Domain | Public safety / crime analytics |
| Period | 2020 – 2024 (2024 is a partial year) |
| Grain | One row = one reported crime case |
| Theme | Dark crime-scene background, red bars, yellow tape banners |

### Fields used (inferred from the visuals)
`City`, `Crime Description`, `Crime Domain` (e.g. Violent Crime), `Victim Age`, `Victim Gender` (F / M / X), `Weapon Used`, `Police Deployed`, `Case Closed` (Yes/No), `Date Reported / Date of Occurrence`.

### Business questions answered
1. Which cities report the most crime?
2. Who are the victims by gender and age?
3. Which weapons are used, and in which crimes?
4. How effective are police at closing cases, by city and crime type?
5. How has crime changed over time?
6. Where is police deployment concentrated?

---

## 2. Dashboard Walkthrough (screenshot by screenshot)

### Screenshot 1 – Total Crime Weapon Used (Firearm filter)
`screenshots/01_weapon_firearm.png`
- **Visual:** Horizontal bar chart + `Weapon Used` slicer (Firearm selected).
- **What it shows:** Crime types in which a firearm was recorded: Assault 305, Sexual Assault 304, Illegal Possession 288, Homicide 282, Identity Theft 276, Kidnapping 276.
- **How to read it:** Pick any weapon in the slicer and the bars re-rank the crimes for that weapon.
- **Insight:** Firearm cases are spread almost evenly across crime types (276–305), with no dominant crime.
- **Note:** Firearms linked to Identity Theft is not realistic. This suggests the data is synthetic or randomly generated. The title also has a typo ("Wepon").

### Screenshot 2 – Crime by Victim Gender (Male selected)
`screenshots/02_gender_crime_types.png`
- **Visual:** Column chart + `Victim Gender` slicer (M selected).
- **What it shows:** Top 5 crime types for male victims: Burglary 691, Drug Offense 676, Fraud 661, Counterfeiting 658, Vandalism 655.
- **Insight:** The gap between 1st and 5th is only about 5%, so no crime type is strongly "male-specific".
- **Note:** The axis title has a typo ("Tota Crimes"). The title can also be shortened to "Top 5 Crimes by Victim Gender".

### Screenshot 3 – Violent Crime by Weekday (City = Visakhapatnam)
`screenshots/03_violent_crime_city.png`
- **Visual:** Column chart + `City` slicer (Visakhapatnam selected).
- **What it shows:** Violent crime share by day of week: Thursday 0.75, Monday 0.43, (Blank) 0.29, Saturday 0.20, Wednesday 0.14.
- **Insight:** Thursday is the peak day and Wednesday the lowest for the selected city.
- **Issues to fix:**
  - The title says "City wise" but the X-axis is **day of week**.
  - A **(Blank)** category means some records have no weekday, which points to missing or unmatched dates.
  - Only 5 days appear (probably a Top N filter).
  - Values should be formatted as % (0.75% vs 75%) so the unit is clear.

### Screenshot 4 – Count of Case Closed by City
`screenshots/04_cases_closed_city.png`
- **Visual:** Column chart sorted descending, with a scrollable axis.
- **What it shows:** Delhi 5.4K, Mumbai 4.4K, Bangalore 3.6K, Hyderabad 2.9K, Kolkata 2.5K, Chennai 2.5K, Pune 2.2K, Ahmedabad 1.8K, Jaipur 1.5K, then Lucknow/Kanpur/Surat/Nagpur about 1.1K, down to about 0.4K for Rajkot, Faridabad and others.
- **Important observation:** These numbers are identical to **total crimes per city** (Screenshot 10). Since the closure rate is about 50% (Screenshot 8), this measure is most likely counting *all* rows in the `Case Closed` column, not only "Yes". It should be `CALCULATE(COUNTROWS(...), Case Closed = "Yes")`, which would give roughly half of each bar.

### Screenshot 5 – Total Crimes by Weapon Used + Crime × Weapon Matrix
`screenshots/05_weapon_matrix.png`
- **Visuals:** Bar chart (left) and Matrix table (right). Rows = Crime Description, columns = Weapon.
- **What it shows:** Knife 5.8K, None 5.8K, Explosives 5.8K, Blunt Object 5.7K, Poison 5.7K, Other 5.7K, Firearm 5.6K. The matrix lists 21 crime types (Arson, Assault, Burglary … Vehicle-Stolen) with counts per weapon. Column totals include Blunt Object 5,737 and Explosives 5,751.
- **Insight:** All 7 weapon categories hold about 14% each (5.6K–5.8K), so weapon type is **not a useful differentiator** in this dataset. About 5.8K cases have no weapon.

### Screenshot 6 – Total Crimes by Age Group and Victim Gender
`screenshots/06_age_gender.png`
- **Visual:** Clustered column chart (blue = F, dark blue = M, orange = X).
- **What it shows:**

| Age group | F | M | X |
|---|---|---|---|
| "70" (oldest bucket) | 6.9K | 4.3K | 1.4K |
| 57–65 | 3.5K | 2.0K | 0.7K |
| 36–45 | 3.3K | 1.9K | 0.6K |
| 46–56 | 3.2K | 1.9K | 0.6K |
| "26–25" (typo) | 2.6K | 1.6K | 0.5K |
| 18–25 | 1.6K | 1.0K | 0.3K |
| 0–12 | 1.0K | 0.6K | 0.2K |
| 13–17 | 0.3K | ~0.1K | 0.1K |

- **Insight:** Females are the largest victim group in **every** age band. The oldest bucket has the most victims, but it is probably the widest age range, so the comparison is not like-for-like.
- **Issues to fix:** Labels are mis-typed ("70" should be "66–70+", "26–25" should be "26–35"), the buckets overlap or leave gaps (46–56 and 57–65), and the bars are sorted by value instead of age order.

### Screenshot 7 – Total Police Deployed by City
`screenshots/07_police_deployed.png`
- **Visual:** Column chart.
- **What it shows:** Pune 22.0K, Surat 11.2K, Visakhapatnam 7.3K, Thane 7.2K, Patna 6.9K, Srinagar 3.7K, Nashik/Varanasi/Vasai 3.6K, Rajkot 3.1K.
- **Insight:** Deployment is roughly **10 officers per case** (Pune: 22K for 2.2K cases; Surat: 11.2K for 1.1K cases). Deployment simply scales with case volume.
- **Note:** Large cities (Delhi, Mumbai, etc.) are missing, so a visual filter or Top N is applied. Because the measure is a SUM of a per-case field, it reflects volume more than real staffing. An average per case, or police per 100 cases, would be more meaningful.

### Screenshot 8 – Closure Rate % and Top 10 Crimes
`screenshots/08_closure_rate.png`
- **Visual:** Horizontal bar chart, Top 10 by closure rate.
- **What it shows:** Public Intoxication 52.30%, Shoplifting 51.10%, Illegal Possession 50.61%, Identity Theft 50.31%, Vehicle-Stolen 50.21%, Traffic Violation 50.13%, Robbery 49.84%, Kidnapping 49.79%, Vandalism 48.86%, Sexual Assault 48.46%.
- **Insight:** Every crime type closes at about 50%, with a spread of under 4 percentage points. Serious crimes (Kidnapping, Sexual Assault) are not closed more often than minor ones (Shoplifting). That is an outcome-quality concern worth flagging.

### Screenshot 9 – Crime % by Victim Gender (Donut)
`screenshots/09_gender_donut.png`
- **Visual:** Donut chart with a 100% centre card.
- **What it shows:** Female 55.83%, Male 33.38%, X (other/unspecified) 10.79%.
- **Insight:** Females account for more than half of all recorded victims. This ties to Screenshot 6, where F is the top bar in every age group.
- **Note:** Labels show the percentage twice, "55.83% (55.83%)". Set the label to show only Percent of total.

### Screenshot 10 – Total Crime, Top 5 Cities
`screenshots/10_top5_cities.png`
- **Visual:** Column chart with a Top 5 filter.
- **What it shows:** Delhi 5.4K, Mumbai 4.4K, Bangalore 3.6K, Hyderabad 2.9K, Kolkata 2.5K.
- **Insight:** These 5 cities together make up about 18.8K of roughly 40.2K cases, which is about **47%**. Rankings follow city population, so these are raw counts and not crime rates.

### Screenshot 11 – Total Crimes by Year and Month
`screenshots/11_monthly_trend.png`
- **Visual:** Line chart with data labels on peaks and troughs and a date-range slider (2020–2024).
- **What it shows:** Monthly counts swing between about 21 and 40, with peaks of 40 (early 2021), 39 (2022), 38 (several) and lows of 21 (2021), 22 (2023), 23 (2024).
- **Insight:** There is no clear trend or seasonality. The series just moves randomly around about 30 per month.
- **Issue:** The monthly values add up to only about 360 per year, while the other pages total about 40K. So this visual does not reconcile with the rest of the report. Check the date relationship and measure (it may be counting a different grain or a distinct count).

### Screenshot 12 – Total Crimes by Year
`screenshots/12_yearly_trend.png`
- **Visual:** Line chart.
- **What it shows:** 2020: 360, 2021: 379, 2022: 371, 2023: 365, 2024: 222.
- **Insight:** Crime is flat from 2020 to 2023 (±3%). The drop in 2024 reflects an **incomplete year** and not a real decline.
- **Issue:** The Y-axis starts at about 230, which makes the 2024 fall look dramatic. Start it at 0 and label 2024 as "YTD". The same scale issue as Screenshot 11 applies.

---

## 3. Key Insights (summary)

1. **Delhi, Mumbai, Bangalore, Hyderabad and Kolkata account for about 47% of all crime.**
2. **Females are 55.8% of victims**, and they are the largest group in every age bracket.
3. **Weapon use is evenly spread** (about 14% each across 7 types), so it is not a strong signal.
4. **Closure rate is about 50% for every crime type**, ranging from 48.5% to 52.3%. Serious crimes do not get better outcomes.
5. **Police deployment scales with case count** at roughly 10 officers per case.
6. **Yearly crime is stable** from 2020 to 2023. The 2024 dip is due to partial-year data.

---

## 4. Data Quality Issues Found (and fixes)

| # | Issue | Where | Fix |
|---|---|---|---|
| 1 | "Count of Case Closed" equals total crimes | Screenshot 4 | Filter to `Case Closed = "Yes"` |
| 2 | Time-series totals (~360/yr) don't match ~40K total | Screenshots 11, 12 | Check the date table relationship and the measure used |
| 3 | (Blank) weekday | Screenshot 3 | Fix null or unmatched dates in Power Query |
| 4 | Title says "City wise" but axis is weekday | Screenshot 3 | Rename the visual |
| 5 | Age group labels wrong ("70", "26–25") and overlapping | Screenshot 6 | Rebuild the buckets and sort by age order |
| 6 | Typos: "Wepon", "Tota Crimes" | Screenshots 1, 2, 6 | Correct the titles |
| 7 | 2024 is a partial year | Screenshot 12 | Label as YTD or compare the same months |
| 8 | Y-axis not starting at 0 | Screenshot 12 | Set min = 0 |
| 9 | Duplicated % labels | Screenshot 9 | Show Percent of total only |
| 10 | Police chart seems filtered | Screenshot 7 | Remove the Top N filter or state it in the title |
| 11 | Unrealistic crime–weapon combos | Screenshots 1, 5 | Note that the data is likely synthetic |

---

## 5. Suggested DAX Measures

```DAX
Total Crimes = COUNTROWS ( Crime )

Cases Closed =
CALCULATE ( COUNTROWS ( Crime ), Crime[Case Closed] = "Yes" )

Closure Rate % = DIVIDE ( [Cases Closed], [Total Crimes] )

Violent Crime % =
DIVIDE (
    CALCULATE ( [Total Crimes], Crime[Crime Domain] = "Violent Crime" ),
    [Total Crimes]
)

Total Police Deployed = SUM ( Crime[Police Deployed] )

Avg Police per Case = AVERAGE ( Crime[Police Deployed] )

Crime % by Gender =
DIVIDE ( [Total Crimes], CALCULATE ( [Total Crimes], ALL ( Crime[Victim Gender] ) ) )

Age Group =
SWITCH (
    TRUE (),
    Crime[Victim Age] <= 12, "0-12",
    Crime[Victim Age] <= 17, "13-17",
    Crime[Victim Age] <= 25, "18-25",
    Crime[Victim Age] <= 35, "26-35",
    Crime[Victim Age] <= 45, "36-45",
    Crime[Victim Age] <= 55, "46-55",
    Crime[Victim Age] <= 65, "56-65",
    "66+"
)
```

---

## 6. How to Use

1. Open the `.pbix` file in Power BI Desktop.
2. Use the slicers (Weapon Used, Victim Gender, City, Year) to filter.
3. Hover over any bar for tooltips; use the focus-mode icon to expand a visual.
4. Use the date slider on the trend chart to narrow the period.

---

## 7. Interview Questions & Answers

**Q1. What is this project about?**
A Power BI dashboard on about 40K Indian crime records (2020–2024). It shows crime by city, victim profile, weapon, closure performance, police deployment and time trends, so decision-makers can see where to focus resources.

**Q2. Which tools and skills did you use?**
Power BI Desktop, Power Query for cleaning, DAX for measures, slicers and Top N filters for interactivity, and a custom dark theme with background images for presentation.

**Q3. Which city has the highest crime?**
Delhi with about 5.4K cases, followed by Mumbai (4.4K), Bangalore (3.6K), Hyderabad (2.9K) and Kolkata (2.5K). Together they make up about 47% of all cases.

**Q4. Are these numbers per capita?**
No. They are raw counts, so large cities naturally rank high. For fair comparison I would add city population and show crimes per 100,000 people.

**Q5. Who are the main victims?**
Females at 55.83%, males at 33.38% and X at 10.79%. Females lead in every age group.

**Q6. Which age group has the most victims?**
The oldest bucket (6.9K female, 4.3K male, 1.4K X). It is a wide age range, so the comparison needs care. The next highest groups are 57–65 and 36–45.

**Q7. What is the overall case closure rate?**
About 50%. By crime type it ranges from 48.46% (Sexual Assault) to 52.30% (Public Intoxication) among the top 10.

**Q8. How did you calculate closure rate?**
Closure Rate % = Cases Closed ÷ Total Crimes, using `DIVIDE` to avoid divide-by-zero errors. Cases Closed counts rows where `Case Closed = "Yes"`.

**Q9. Which weapon is used most?**
They are nearly equal: Knife, None and Explosives at 5.8K each, down to Firearm at 5.6K. Weapon type is not a strong differentiator in this data.

**Q10. Which crimes involve firearms most?**
Assault (305) and Sexual Assault (304), followed by Illegal Possession (288) and Homicide (282). The differences are small.

**Q11. Is crime increasing or decreasing?**
It is flat from 2020 to 2023 (360, 379, 371, 365). The 2024 value of 222 is a partial year, so it can't be read as a decline.

**Q12. Why does 2024 look like a sharp drop?**
The data for 2024 is incomplete, and the Y-axis starts around 230, which exaggerates the fall. To be fair I would show a same-period comparison or label 2024 as YTD.

**Q13. How does police deployment relate to crime?**
Almost proportionally: about 10 officers per case (Pune 22K for 2.2K cases). This means deployment tracks volume, not need or severity.

**Q14. Why is "Count of Case Closed by City" the same as total crimes?**
Counting the `Case Closed` column counts every non-blank row, both Yes and No. It should be filtered to "Yes". This is a measure bug I would fix, and the corrected bars should be about half as tall.

**Q15. What does the "(Blank)" bar in the weekday chart mean?**
Some records have a missing or unmatched date, so no weekday could be assigned. I would fix this in Power Query or check the relationship to the date table.

**Q16. What data cleaning did you do?**
Fixed data types, handled nulls and missing dates, standardised city and crime names, created age-group buckets and a date table. I would also correct the age labels that currently show "70" and "26–25".

**Q17. Why use a donut chart for gender?**
There are only 3 categories that sum to 100%, so a donut shows part-to-whole clearly. The centre card confirms the total.

**Q18. Why bar charts for most visuals?**
Bars are the best way to compare categories like cities, crimes and weapons. Horizontal bars suit long labels such as crime names. Lines are used only for time.

**Q19. What is the role of slicers?**
They let users filter the whole page by Weapon, Victim Gender or City without changing the report, so one visual answers many questions.

**Q20. What are the main limitations?**
The data looks synthetic (uniform weapon distribution, implausible crime–weapon pairs), there are no per-capita rates, 2024 is incomplete, the time-series totals do not reconcile with the other pages, and age buckets are inconsistent.

**Q21. What would you recommend to the police department?**
Concentrate resources in the top 5 cities, review why serious crimes close at the same ~50% as minor ones, investigate the Thursday peak in violent crime, and add victim-support programmes since women are the majority of victims.

**Q22. What improvements would you make next?**
Fix the issues in section 4, add crime rate per 100K, a city map, a Year/Month drill-through, closure time (days to close) and a forecast of monthly crime.

**Q23. What would you do to validate the dashboard?**
Reconcile totals across pages (they should all sum to about 40K), compare against row counts in the source, and test slicers to make sure all visuals respond correctly.

**Q24. How would you handle a stakeholder who asks "which weapon is most dangerous?"**
I would explain that this dataset can't answer that, because weapon use is almost uniform and there is no injury or severity field. I'd suggest adding severity or casualty data.

---

## 8. Future Enhancements
- Map visual with city bubbles
- Crime rate per 100K population
- Average days to close a case
- Year-over-year and same-period comparisons
- Drill-through from city to crime type to weapon
- Forecasting with Power BI's analytics pane
- Tooltip pages and bookmarks for navigation

---

## 9. Author
*Aditya Prajapati
