**US Health Insurance Coverage Dashboard**

**Why I built this**: I wanted a simple project that actually shows how I work in Power BI end to end not just pretty charts, but the messy part before that: taking a raw government data export and turning it into something a dashboard can actually use. 

**Data source**: I picked the Census Bureau's health insurance coverage data (Table S2701, 2024 ACS 1-Year Estimates) because it's public, well-documented, and genuinely messy in the way real work files often are.

**#Source: U.S. Census Bureau, ACS Table S2701#**
<a href= "https://github.com/Jaishvani23/US-Census-Data/blob/main/Selected%20Characteristics%20of%20Health%20Insurance%20Coverage%20in%20the%20United%20States.xlsx"> Dataset<a/>

**The problem with the raw file:**
1. If you've ever pulled a table straight off data.census.gov, you know the **export isn't ready to use**.
2. The "Data" sheet has stacked headers across three rows, section labels like "AGE" and "NATIVITY AND U.S. CITIZENSHIP STATUS" sitting inside the table as their own blank rows, numbers formatted as text with commas in them, and margin-of-error columns prefixed with a "±" symbol.
3. None of that loads cleanly into a data model as-is, so most of the actual work here happened in Power Query before I ever touched a visual.

What I did in Power Query:
Pulled in only the "Data" sheet (the "Information" tab is just citation/methodology text, not useful for the model)

Trimmed the junk rows at the top and promoted the real header row

Renamed every column so it's obvious what it holds — Total_Estimate, Insured_Percent, Uninsured_MOE, etc., instead of "Estimate," "Estimate_1," "Estimate_3"

Built a small bit of custom logic to catch those embedded section headers (AGE, SEX, RACE...) and turn them into a proper Category column instead of letting them sit as broken rows in the middle of the data

Cleaned up the "±" and "%" symbols and made sure the numeric conversion used the right locale so commas didn't break the import

Reordered everything so it reads logically before loading it into the model

Full M code is in powerquery_transform.m if you want to see exactly how that's structured.

What's on the dashboard
**Top row** : quick-glance numbers: total US population (335M), national insured rate (91.8%), and national uninsured rate (8.2%), plus a category slicer that drives everything below it.

**Middle section** : a pie chart showing insured rate broken out by whatever category is selected (age groups, in the screenshot above), paired with a horizontal bar chart of uninsured rates for the same slice, so you can see both sides of the coverage story at once.

**Risk segment table** : this is the part I'm most proud of. It's a ranked table showing every demographic segment's insured %, uninsured %, and how far off it is from the national average, with color coding so the worst gaps jump out in red and the better-than-average ones show green. Two callout cards next to it highlight the single highest-risk group (non-citizens, at 29.2% uninsured) and the lowest-risk group (adults 75+, essentially fully covered thanks to Medicare).

**A few things that stood out in the data**
Non-citizens are uninsured at nearly 5x the national rate : 29.2% vs. 8.2%

Coverage climbs steadily with age until Medicare kicks in at 65, then flattens out near-universal

People with a disability are actually more likely to be insured than people without one, which seems backwards until you remember Medicare/Medicaid disability eligibility exists regardless of age

American Indian/Alaska Native and "some other race" populations show meaningfully higher uninsured rates than the national average, while Asian and White populations sit below it

DAX measures
I kept these documented separately in dax_measures.md since there's a bit of nuance worth explaining  particularly around why SELECTEDVALUE based measures work fine on cards but return blank if you drop them straight into a bar chart, and why I had to explicitly exclude the repeated "Civilian noninstitutionalized population" total row from ranking logic so it didn't skew everything.

Worth knowing before you dig in
This is one year of data with no trend line. it's a snapshot, not a time series, so don't expect year-over-year comparisons. Every number here also carries a margin of error since it's survey-based, not a full census count; I kept those MOE columns in the model even though they're not front-and-center on the dashboard, mainly so the underlying uncertainty isn't lost if someone wants to dig deeper.

Files in this repo
health_insurance_clean.csv : the cleaned dataset, ready to load

powerquery_transform.m : the full Power Query transformation

dax_measures.md : measure definitions and the reasoning behind them 
<a href= "https://github.com/Jaishvani23/US-Census-Data/blob/main/dax_measures.md"> DAX Document<a/>

Health_Insurance_Dashboard.pbix : the actual Power BI file

dashboard_preview.png : screenshot for the repo

Source
U.S. Census Bureau. "Selected Characteristics of Health Insurance Coverage in the United States." American Community Survey, ACS 1-Year Estimates Subject Tables, Table S2701, 2024.
