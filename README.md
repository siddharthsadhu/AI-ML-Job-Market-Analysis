# 📊 AI / ML Job Market Analysis Dashboard

> A portfolio-grade Power BI dashboard analyzing global salary trends, top-paying roles, and career trajectories across **151,000+ real-world AI & Data Science job records** spanning 2020–2025.

<br>

[![Live Dashboard](https://img.shields.io/badge/Power%20BI-Live%20Dashboard-00b4d8?style=for-the-badge&logo=powerbi&logoColor=white)](https://app.powerbi.com/links/xJWdsYU1uE?ctid=452a0083-a6e2-46aa-8082-480002eeb4b5&pbi_source=linkShare)
[![Dataset](https://img.shields.io/badge/Kaggle-Dataset-20BEFF?style=for-the-badge&logo=kaggle&logoColor=white)](https://www.kaggle.com/datasets/adilshamim8/salaries-for-data-science-jobs)
[![Website](https://img.shields.io/badge/Project-Website-0d1b2a?style=for-the-badge&logo=github&logoColor=white)](https://ai-ml-job-market-analysis.netlify.app/)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Siddharth%20Sadhu-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/siddharth-sadhu-b67551274/)

<br>

---

## 🔗 Quick Links

| Resource | Link |
|---|---|
| 🖥️ Live Power BI Dashboard | [View Dashboard](https://app.powerbi.com/links/xJWdsYU1uE?ctid=452a0083-a6e2-46aa-8082-480002eeb4b5&pbi_source=linkShare) |
| 📦 Kaggle Dataset | [Data Science, AI & ML Job Salaries 2025](https://www.kaggle.com/datasets/adilshamim8/salaries-for-data-science-jobs) |
| 🌐 Project Website | [View Website](https://ai-ml-job-market-analysis.netlify.app/) |
| 👤 LinkedIn | [Siddharth Sadhu](https://www.linkedin.com/in/siddharth-sadhu-b67551274/) |

---

## 📌 Project Overview

This project was built as part of the **Data Visualization (DV) Lab — Week 12 Project** at Adani University. The goal was to select a real-world dataset, clean and transform it using Power Query, build a data model, write DAX measures, and create an interactive single-page Power BI dashboard.

I chose the AI/ML job market domain intentionally — as a pre-final year student targeting Data Science and AI/ML roles, this data directly informs my own career strategy.

---

## 🎯 Key Insights

| # | Insight | Value |
|---|---|---|
| 1 | Highest paying AI/ML role | Research Team Lead — **$350K+** avg |
| 2 | Experience salary gap | Executive earns **~2×** Entry Level |
| 3 | Remote work reality | Only **20.76%** of AI/ML jobs are fully remote |
| 4 | Most common salary band | **67%** of roles fall in $60K–$120K mid band |
| 5 | Company size premium | Large companies pay **61% more** than small firms |
| 6 | Market growth | Salaries grew **every year** from 2020–2025 |

---

## 📊 Dashboard Visuals

| Visual | Type | Story it tells |
|---|---|---|
| KPI Cards × 4 | Card | Median Salary · Max Salary · Total Jobs · Remote % |
| Top 10 Paying Roles | Clustered Bar Chart | Which job titles earn the most |
| Salary Growth Trend | Line Chart | How salaries grew 2020–2025 by experience level |
| Salary Band Distribution | Donut Chart | Low / Mid / High salary tier breakdown |
| Avg Salary by Experience | Column Chart | Experience as the #1 salary driver |
| Global Job Distribution | Map Visual | Geographic concentration of AI/ML jobs |
| Avg Salary by Company Size | Bar Chart | Compensation comparison by org size |
| Interactive Slicers × 4 | Slicer | Work Year · Experience · Company Size · Work Mode |

---

## 🗂️ Dataset

| Field | Detail |
|---|---|
| **Name** | Data Science, AI & ML Job Salaries 2025 |
| **Source** | [Kaggle — adilshamim8](https://www.kaggle.com/datasets/adilshamim8/salaries-for-data-science-jobs) |
| **Records** | 151,000+ rows |
| **Year Range** | 2020 – 2025 |
| **Coverage** | 60+ countries globally |
| **License** | CC0 Public Domain |
| **Currency** | All salaries normalized to USD |

> ⚠️ The dataset is not included in this repo due to size. Download it directly from the Kaggle link above.

---

## 🧹 Data Cleaning — Power Query

All transformations were performed in **Power Query Editor** before loading into the data model:

- ✅ Removed `salary` (raw multi-currency — not comparable across countries)
- ✅ Removed `salary_currency` (not needed after USD normalization)
- ✅ Decoded `experience_level` → `EN` → Entry Level · `MI` → Mid Level · `SE` → Senior Level · `EX` → Executive Level
- ✅ Decoded `remote_ratio` → `0` → Onsite · `50` → Hybrid · `100` → Remote
- ✅ Decoded `company_size` → `S` → Small · `M` → Medium · `L` → Large
- ✅ Decoded `employment_type` → `FT` → Full-Time (filtered to FT only)
- ✅ Renamed all 9 columns for readability
- ✅ Set correct data types per column

---

## 🧮 DAX Measures & Calculated Columns

### Measures (stored in `_Measures` table)

```dax
-- Average Salary
Avg Salary = AVERAGE(AI_Job_Salaries[Salary (USD)])

-- Median Salary (used as KPI — resistant to outlier skew)
Median Salary = MEDIAN(AI_Job_Salaries[Salary (USD)])

-- Maximum Salary
Max Salary = MAX(AI_Job_Salaries[Salary (USD)])

-- Total Job Records
Total Jobs = COUNTROWS(AI_Job_Salaries)

-- Remote Jobs % (uses CALCULATE to apply conditional filter)
Remote Jobs % =
DIVIDE(
    CALCULATE(COUNTROWS(AI_Job_Salaries),
    AI_Job_Salaries[Work Mode] = "Remote"),
    COUNTROWS(AI_Job_Salaries), 0
) * 100

-- Senior & Executive Average Salary
Senior+ Avg Salary =
CALCULATE(
    AVERAGE(AI_Job_Salaries[Salary (USD)]),
    AI_Job_Salaries[Experience Level] IN {"Senior Level", "Executive Level"}
)

-- Entry Level Average Salary
Entry Level Avg Salary =
CALCULATE(
    AVERAGE(AI_Job_Salaries[Salary (USD)]),
    AI_Job_Salaries[Experience Level] = "Entry Level"
)
```

### Calculated Columns (stored in fact table)

```dax
-- Salary Band (for Donut Chart categorization)
Salary Band =
IF(AI_Job_Salaries[Salary (USD)] < 60000, "Low (< $60K)",
IF(AI_Job_Salaries[Salary (USD)] < 120000, "Mid ($60K–$120K)",
"High (> $120K)"))

-- Is Senior (binary flag for filtering)
Is Senior =
IF(AI_Job_Salaries[Experience Level] IN {"Senior Level", "Executive Level"},
"Senior+", "Junior/Mid")
```

---

## 🏗️ Project Methodology

```
1. Dataset Selection    →  Identified CC0 Kaggle dataset · 151K+ records
2. Data Import          →  Power BI Desktop · Get Data → CSV
3. Power Query          →  Cleaned, decoded, renamed, filtered
4. Data Modeling        →  Single flat table · _Measures table created
5. DAX                  →  7 measures + 2 calculated columns
6. Dashboard Design     →  7 visuals + 4 slicers · single page layout
7. Review & Polish      →  Updated visual types · verified cross-filtering
```

---

## 🛠️ Tech Stack

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=flat-square&logo=microsoft&logoColor=white)
![Power Query](https://img.shields.io/badge/Power%20Query-217346?style=flat-square&logo=microsoft&logoColor=white)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=flat-square&logo=kaggle&logoColor=white)

---

## 📁 Repository Structure

```
AI-ML-Job-Market-Analysis/
│
├── DV_Project.pbix          # Power BI dashboard file
├── index.html               # Project portfolio website
├── README.md                # This file
└── assets/
    └── dashboard-preview.png  # Dashboard screenshot (optional)
```

---

## 🚀 How to Run

1. Download the dataset from [Kaggle](https://www.kaggle.com/datasets/adilshamim8/salaries-for-data-science-jobs)
2. Clone this repository
   ```bash
   git clone https://github.com/siddharthsadhu/AI-ML-Job-Market-Analysis.git
   ```
3. Open `DV_Project.pbix` in **Power BI Desktop**
4. If prompted, update the data source path to point to your downloaded `salaries.csv`
5. Click **Refresh** — the dashboard will load with all visuals

---

## 👤 Author

**Siddharth Sadhu**
B.Tech ICT · Adani University, Ahmedabad · 2024–2027
CGPA: 8.77

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/siddharth-sadhu-b67551274/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github)](https://github.com/siddharthsadhu)
[![Email](https://img.shields.io/badge/Email-Contact-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:siddharthsadhu28@gmail.com)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

The dataset is sourced from Kaggle under the **CC0 Public Domain** license.

---

<div align="center">
  <sub>Built with ♥ and DAX · Data Visualization Project · Adani University · 2026</sub>
</div>
