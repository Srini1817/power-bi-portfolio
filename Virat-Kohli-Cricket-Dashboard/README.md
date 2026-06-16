# 🏏 Virat Kohli Cricket Career Dashboard — Power BI

## Overview
A 3-page interactive Power BI dashboard analyzing Virat Kohli's complete cricket career statistics from 2008 to 2022. Built with a custom-themed design, bookmark-based navigation, Row-Level Security (RLS), and advanced DAX measures — this project showcases end-to-end BI development from data modeling to report delivery.

---

## 🖼️ Dashboard Preview

### Page 1 — Career Overview
![Overview](https://raw.githubusercontent.com/Srini1817/power-bi-portfolio/main/Virat-Kohli-Cricket-Dashboard/Virat%20Kohli%20Overview.png)

### Page 2 — ODI ICC Knockouts Insights
![ODI ICC KO](https://raw.githubusercontent.com/Srini1817/power-bi-portfolio/main/Virat-Kohli-Cricket-Dashboard/ODI%20ICC%20KO%20Insights.png)

### Page 2 — T20 ICC Knockouts Insights
![T20 ICC KO](https://raw.githubusercontent.com/Srini1817/power-bi-portfolio/main/Virat-Kohli-Cricket-Dashboard/T20%20ICC%20KO%20Insights.png)

### Page 3 — Top 3 Innings
![Top 3 Innings](https://raw.githubusercontent.com/Srini1817/power-bi-portfolio/main/Virat-Kohli-Cricket-Dashboard/Top%203%20Innings.png)
---

## 📌 Key Features

### 🏠 Page 1 — Career Overview
- **KPI Cards** — Total Runs (24K), Matches (516), Batting Average (45.95), Top Score (254), 100s (77), 50s (129)
- **Runs & Average by Year** — Combo bar and line chart tracking career trajectory from 2008–2022
- **Highest Scoring Opponents** — Donut chart highlighting top opponents: AUS (4,483), ENG (3,903), WI (3,653)
- **Year Slicer** — Dynamic range slider for time-based filtering

### 📊 Page 2 — ODI ICC Knockouts & T20 Insights
- **ODI ICC Knockout Performance** — Runs and Average by tournament year
- **Milestones** — 100s and 50s breakdown via donut chart
- **Boundaries** — Total 4s (212) and 6s (22) visualization
- **Bookmark Navigation** — Toggle between ODI and T20 insights via buttons

### 🥇 Page 3 — Top 3 Innings
- **Dynamic Top 3 Innings** — Displays highest-scoring innings with Runs, Opponent, Format, Stadium, and Date
- **Back Navigation Button** — Returns user to Career Overview page

---

## 🗃️ Data Model
Star schema design with 6 tables:

| Table | Description |
|-------|-------------|
| Virat_Kohli | Core match-level data (runs, format, opponent, stadium, date) |
| VK_Measures | Calculated DAX measures table |
| Z | Date dimension table |
| ODI ICC | ODI ICC Knockout tournament data |
| T20 ICC | T20 ICC tournament data |
| Group By - Opponent | Aggregated runs grouped by opponent |
| RLS | Row-Level Security user access table |

---

## 📐 DAX Measures
```
100s = 
VAR res = SWITCH(TRUE(),
    'Virat_Kohli'[runs]>=100 && 'Virat_Kohli'[runs]<200, 1,
    'Virat_Kohli'[runs]>=200 && 'Virat_Kohli'[runs]<300, 2,
    'Virat_Kohli'[runs]>=300 && 'Virat_Kohli'[runs]<400, 3,
    0)
RETURN res

30+ = IF('Virat_Kohli'[runs]>=30, 1, 0)

50s = 
VAR res = SWITCH(TRUE(),
    'Virat_Kohli'[runs]>=50 && 'Virat_Kohli'[runs]<99, 1,
    'Virat_Kohli'[runs]>=150 && 'Virat_Kohli'[runs]<199, 1,
    'Virat_Kohli'[runs]>=250 && 'Virat_Kohli'[runs]<299, 1,
    0)
RETURN res

TOP 1 DATE = CALCULATE(VALUES('Virat_Kohli'[date]), FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = [TOP 1 RUNS] && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))

TOP 1 format = CALCULATE(VALUES('Virat_Kohli'[format]), FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = [TOP 1 RUNS] && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))

TOP 1 Opponent = CALCULATE(VALUES('Virat_Kohli'[opponent]), FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = [TOP 1 RUNS] && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))

TOP 1 RUNS = 
CALCULATE(MAX('Virat_Kohli'[runs]), ALLEXCEPT('Virat_Kohli','Virat_Kohli'[Year] ))

TOP 1 STADIUM = CALCULATE(VALUES('Virat_Kohli'[Stadium]), FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = [TOP 1 RUNS] && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))

Top 2 date = 
VAR TOP2RUN = [TOP 2 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP2RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[date])

Top 2 format = 
VAR TOP2RUN = [TOP 2 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP2RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[format])

Top 2 Opponent = 

VAR TOP2RUN = [TOP 2 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP2RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[opponent])

TOP 2 RUNS = 
VAR TOP1RUN = CALCULATE(MAX('Virat_Kohli'[runs]), ALLEXCEPT('Virat_Kohli', 'Virat_Kohli'[Year]))
VAR TOP2RUN = CALCULATE(MAX('Virat_Kohli'[runs]),
FILTER('Virat_Kohli', 'Virat_Kohli'[runs] < TOP1RUN &&
'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))
RETURN TOP2RUN 

Top 2 Stadium = 
VAR TOP2RUN = [TOP 2 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP2RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[Stadium])

Top 3 date = 
VAR TOP3RUN = [TOP 3 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP3RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[date])

Top 3 format = 
VAR TOP3RUN = [TOP 3 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP3RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[format])

Top 3 Opponent = 
VAR TOP3RUN = [TOP 3 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP3RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[opponent])

TOP 3 RUNS = 
VAR TOP1RUN = CALCULATE(MAX('Virat_Kohli'[runs]), ALLEXCEPT('Virat_Kohli', 'Virat_Kohli'[Year]))
VAR TOP2RUN = CALCULATE(MAX('Virat_Kohli'[runs]),
FILTER('Virat_Kohli', 'Virat_Kohli'[runs] < TOP1RUN &&
'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))
VAR TOP3RUN = CALCULATE(MAX('Virat_Kohli'[runs]),
FILTER('Virat_Kohli', 'Virat_Kohli'[runs] < TOP2RUN &&
'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year])))
RETURN TOP3RUN 

Top 3 Stadium = 
VAR TOP3RUN = [TOP 3 RUNS]
VAR FILTEREDTABLE = FILTER('Virat_Kohli', 'Virat_Kohli'[runs] = TOP3RUN && 'Virat_Kohli'[Year] = SELECTEDVALUE('Virat_Kohli'[Year]))
VAR TOPROW = TOPN(1, FILTEREDTABLE, 'Virat_Kohli'[date], DESC)
RETURN MAXX(TOPROW, 'Virat_Kohli'[Stadium])

100s = SUM(Virat_Kohli[100s])

30+ = SUM(Virat_Kohli[30+])

50s = SUM(Virat_Kohli[50s])

Avg = AVERAGE(Virat_Kohli[runs])

HighestScore = MAX(Virat_Kohli[runs])

Runs = SUM(Virat_Kohli[runs])

Total_Matches = COUNT(Virat_Kohli[opponent]) 

Z = CALENDAR(
        DATE ( YEAR ( MIN(Virat_Kohli[date]) ),1,1), 
        DATE(YEAR(MAX(Virat_Kohli[date])),12,31)
)

day = FORMAT(Z[Date],"ddd")

day_no = DAY(Z[Date])

month = FORMAT(Z[Date],"mmm")

month_no = MONTH(Z[Date])

year = YEAR(Z[Date])
```

**Top Innings Calculations** — Calculated columns to dynamically surface Top 1, Top 2, and Top 3 innings with full match context (Runs, Opponent, Format, Stadium, Date)

---

## 🔐 Row-Level Security (RLS)
Implemented RLS to restrict data access by year — users can only view data relevant to their assigned year via the RLS table (Email, User Name, Year).

---

## 🛠️ Tools & Techniques
| Area | Detail |
|------|--------|
| Tool | Power BI Desktop |
| Data Source | Excel (Google) |
| Data Model | Star Schema — 6 tables |
| DAX | Measures + Calculated Columns |
| Navigation | Bookmarks + Buttons |
| Security | Row-Level Security (RLS) |
| Design | Custom background, themed visuals |

---

## 💡 Key Insights
- Kohli's **peak years** were 2016–2018, with consistently high runs and batting average
- **Australia** was his highest-scoring opponent with 4,483 runs across all formats
- In **ICC ODI Knockouts**, his best tournament was 2024 with 765 runs at an average of 95.6
- All **Top 3 innings** came in the ODI format during his early career (2009)
- Dataset covers career statistics **up to 2022**

---

## 🚀 How to Use
1. Download the `.pbix` file above
2. Open in **Power BI Desktop**
3. Use the **year slicer** on Page 1 to filter career phases
4. Click **ODI / T20 buttons** on Page 2 to toggle between formats
5. Navigate to **Top 3 Innings** via the page tab or button
6. Use the **back button** on Page 3 to return to Overview

---

## 👤 Author
**Srinivasan N**
Microsoft Certified Power BI Data Analyst (PL-300)
[LinkedIn](https://linkedin.com/in/your-linkedin) | [Email](mailto:nandagopal.srinivasan2011@gmail.com)
