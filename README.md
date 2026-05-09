# 🧭 France Job Market Dashboard — Power BI
 
> A personal job intelligence dashboard built on live-scraped Indeed France data, with a custom relevance scoring engine, application tracking, and market analytics — all automated through GitHub Actions.
 
---
 
## 📌 What This Dashboard Does
 
Most job seekers scroll through listings manually, guess which roles to prioritise, and lose track of where they applied. This dashboard solves all three problems at once.
 
It scrapes job listings from Indeed France automatically every week, scores each job against your personal profile using a multi-dimensional relevance algorithm, and gives you a single command centre to track applications, spot opportunities, and understand the market — all inside Power BI.
 
---
 
## 🗂️ Dashboard Pages
 
### Page 1 — Command Centre (Home)
 
The home page is designed to answer one question the moment you open it: *what should I do today?*
 
Five KPI cards sit at the top of the page, each acting as a clickable filter for the entire report:
 
| KPI Card | What It Shows |
|---|---|
| **Total Jobs** | Total listings in the current dataset |
| **Urgent Hires** | Roles flagged as urgent by Indeed — apply these first |
| **Applied Jobs** | Count of listings you have marked as applied |
| **Strong Matches Pending** | Strong-match roles you haven't applied to yet — your action list |
| **Avg Relevance Score** | Your average match quality across all current listings |
 
Clicking any KPI card filters the entire page to that subset. Clicking "Urgent Hires" shows only urgent roles. Clicking "Strong Matches Pending" surfaces exactly the jobs you should be applying to today.
 
---
 
### Details Table — Top Matches at a Glance
 
The main table on the home page is sorted by Relevance Score descending and shows:
 
- **Job Position** — full title as posted
- **Company Name**
- **Location / City**
- **Relevance Score** — calculated per job (explained below)
- **Avg Salary** — parsed from salary text where available
- **Job Type** — CDI, CDD, Stage, Alternance, etc.
- **Work Mode** — Remote or On-site with icon
- **Apply Link** — direct clickable link to the Indeed listing
A "Top Matches" button in the top right of the table filters instantly to only your highest-scoring jobs, so you never have to scroll through irrelevant listings.
 
---
 
### Job Role Trend — Bar Chart
 
Shows how many listings exist per job category — Data Analyst, Business Analyst, Analyst/Developer, Finance Analyst, Data Science, and others. Tells you at a glance where the market volume is right now and which roles are most actively being hired for.
 
---
 
### Job Nature — Donut Chart
 
Breaks down the full dataset by contract type:
- **Alternance** (currently 64% of listings)
- **CDD**
- **CDI**
- **Other**
Useful for understanding what kind of opportunity dominates the market at any given time, and for filtering to the contract type that matches your situation.
 
---
 
### Location Map
 
An interactive map of France showing job concentration by city. Bubble clusters indicate hiring hotspots. Click any cluster to filter the entire dashboard to that region. Pairs with the **Region** dropdown slicer at the top of the page.
 
---
 
## 🧠 Relevance Score Engine
 
The core feature of this dashboard is the relevance scoring system — a custom-built algorithm written entirely in DAX that scores every job listing against your personal profile on four dimensions.
 
### How It Works
 
Your profile lives in a separate sheet (`My_Profile`) with your target role, preferred location, work mode, salary target, and up to six skills. Every job in the dataset is scored against this profile automatically when you refresh.
 
### Scoring Dimensions
 
**Skills Match — 40% weight**
 
Searches your six profile skills across the full job description text, all attribute columns, occupation tags, and requirement labels simultaneously. Uses word-boundary matching (space-padded search) to avoid false positives from short skill abbreviations appearing inside French words. Returns the percentage of your skills found in the listing, weighted at 40% of the final score.
 
**Role Match — 30% weight**
 
Compares your target role against the job title. Exact match scores 100, partial match on "analyst" scores 40, partial match on "data" scores 20. Handles French job title variations automatically through case-insensitive search.
 
**Location Match — 15% weight**
 
Scores 100 if the job is remote or in your target city, 80 if your city name appears anywhere in the location string, 50 if the work mode matches your preference. Handles both the `isRemote` flag and the `workingSystem` column.
 
**Salary Match — 15% weight**
 
Parses salary from both numeric columns and free-text salary strings (e.g. "30 000 € par an", "De 60 000 € à 70 000 € par an"). Converts your monthly target to annual for comparison. Scores 100 for a match within range, 75 for within 15%, 60 if the job pays above your target, 20 if it pays below. Returns a neutral 50 if no salary is listed.
 
**Urgency Bonus — up to +10 points**
 
Jobs flagged as `isUrgentHire = TRUE` receive a 10-point bonus on top of the composite score, capped at 100. These roles are more likely to respond quickly and are worth prioritising.
 
### Composite Formula
 
```
Relevance Score = 
  (Skills Match × 0.40) + 
  (Role Match × 0.30) + 
  (Location Match × 0.15) + 
  (Salary Match × 0.15) + 
  Urgency Bonus
```
 
All component scores are calculated as DAX calculated columns on the Jobs table, not measures — this ensures they work correctly inside table visuals, can be sorted, filtered, and used as slicer values without context errors.
 
---
 
## 🪣 Match Buckets
 
Every job is assigned a Match Bucket label based on its Relevance Score:
 
| Score Range | Label |
|---|---|
| 97 and above | 🟢 Good Match |
| 85 – 96 | 🟡 Medium Match |
| Below 85 | 🔴 Least Match |
 
The Match Bucket column is used for conditional formatting in the details table and as a slicer to instantly filter the dashboard to only your best opportunities. The "Strong Matches Pending" KPI card references this column to surface Good Match jobs you haven't applied to yet.
 
---
 
## 📍 Filters and Slicers
 
The dashboard has four global filters that affect all visuals simultaneously:
 
| Filter | Type | What It Controls |
|---|---|---|
| **Remote / On-site** | Button toggle | Filters by work mode |
| **Days Old** | Dropdown | Filters by posting age — e.g. "Last 7 days" only |
| **Region** | Dropdown | Filters by city or region |
| **Top Matches button** | Table filter | Shows only highest-scoring jobs in the details table |
 
All KPI cards are also cross-filters — clicking any card applies it as a filter across the whole page.
 
---
 
## 💡 Tooltips
 
Hovering over the job trend bar chart provides insights into the distribution of jobs across Good Match, Medium Match, and Low Match categories.

 

Tooltips are built as a separate report page and linked to the details table, giving you rich context without cluttering the main view.
 
---
 ## Automated Data Pipeline
 
The data behind this dashboard is refreshed automatically every week using a GitHub Actions pipeline — no manual scraping required.
 
### How the Pipeline Works
 
1. **GitHub Actions triggers** every Monday at 8am UTC (or manually via the workflow dispatch button in GitHub)
2. **Python scraper** using the `jobspy` library searches Indeed France for your target roles across multiple search terms
3. **Data is cleaned** — duplicate listings removed by job URL, salary text parsed, metadata added
4. **Existing applications protected** — any job you have marked as Applied is preserved across scrapes and never overwritten
5. **CSV committed back to GitHub** — the updated file is pushed automatically to `data/jobs_france.csv`
6. **Power BI reads the raw GitHub URL** — hitting refresh in Power BI pulls the latest scrape instantly
### Search Terms Covered
 
- Data Analyst (France)
- Business Analyst (France)
- Power BI (France)
- Data Analyst Stage (France)
- Analyste de données (France)
Search terms are fully configurable in `scraper.py`.

## 🚀 How to Use This Dashboard
 
### First-Time Setup
 
1. Clone or fork this repository
2. Open `My_Profile.xlsx` and fill in your target role, location, preferred work mode, salary target, and up to six skills
3. Open the `.pbix` file in Power BI Desktop
4. Update the data source path for `My_Profile.xlsx` to your local file location
5. The GitHub raw CSV URL for job data is already connected — hit Refresh to pull the latest scrape
### Weekly Usage
 
1. Open Power BI Desktop and hit **Refresh** — pulls the latest scraped data from GitHub automatically
2. Check the **Strong Matches Pending** KPI — these are your priority applications for the week
3. Use the **Top Matches** button to filter the details table to your best opportunities
4. Click **Apply Link** on any row to go directly to the Indeed listing
5. Mark applied jobs by updating the `Applied` column in your local Excel tracker
### Updating Your Profile
 
To update your skills, target role, salary, or location — edit `My_Profile.xlsx` and hit Refresh in Power BI. All relevance scores recalculate automatically based on your new profile. No DAX edits required.

## 📊 Skills Demonstrated
 
This project was built to showcase end-to-end data engineering and analytics skills:
 
- **Data pipeline design** — automated scrape → clean → store → visualise cycle
- **Advanced DAX** — multi-column calculated columns, LOOKUPVALUE cross-table references, IFERROR-wrapped SEARCH for French text, word-boundary matching, SWITCH-based classification
- **Power Query ** — data shaping, column type management, source configuration
- **Data modelling** — two-table architecture with profile config pattern, no unnecessary relationships
- **Product thinking** — dashboard designed around a real workflow (what do I apply to today?) not just data display
- **Automation** — GitHub Actions YAML, cron scheduling, git commit workflows, Python scripting
---
 
*Built by Khushi Sarpal — MSc Data Science & AI, Toulouse Business School*  
*GitHub: [github.com/Khushisarpal](https://github.com/Khushisarpal)*
