# France-Job-Market-Analytics-Dashboard
End-to-end job market analytics pipeline — Indeed France data scraped via API, cleaned with Python/Pandas, stored in SQLite, and visualised in Power BI.
# France Job Market Analytics Dashboard

End-to-end data pipeline scraping live job listings from Indeed France, 
cleaning with Python, storing in SQLite, and visualising in Power BI.

---

## What this project does

Helps students and job seekers understand the French data job market by 
analysing 100+ live job listings scraped from Indeed France — covering 
roles like Data Analyst, Business Analyst, and Python Developer across 
cities, contract types, and remote availability.

---

## Pipeline

Apify API → Raw JSON → Python/Pandas cleaning → SQLite → Power BI dashboard

---

## Dashboard covers

- Top 10 hiring companies in France
- Jobs by city
- Remote vs onsite split
- Jobs by contract type (CDI, CDD, Stage, Freelance)
- KPI cards — total jobs, % remote, unique companies

---

## Tech stack

- Data collection — Apify API (Indeed France scraper)
- Cleaning & transformation — Python, Pandas
- Storage — SQLite
- Visualisation — Power BI

---

## Files

- pipeline.ipynb — full cleaning and SQLite pipeline
- dataset_indeed-scraper.json — raw data from Apify
- jobs_clean.csv — cleaned data for Power BI
- jobs.db — SQLite database
- dashboard.pbix — Power BI file

---

## How to run

1. Clone the repo
2. Install dependencies — pip install pandas
3. Open pipeline.ipynb in Jupyter and run all cells
4. Open dashboard.pbix in Power BI Desktop

---

## Author

Khushi Sarpal
MSc Data Science & AI — Toulouse Business School
LinkedIn: linkedin.com/in/khushisarpal
GitHub: github.com/khushisarpal
