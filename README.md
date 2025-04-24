ها هو الـ README المعدل:

---

# Unicorn Companies Analysis

## Project Description

This project focuses on analyzing the trends of high-growth companies, specifically unicorns (companies valued at over $1 billion). The goal is to identify the industries producing the highest valuations and to track the rate at which new high-value companies are emerging. This analysis provides competitive insights into industry trends and helps investment firms structure their portfolios effectively.

The data is stored in a database with the following tables:
- `dates`: Contains information about the company ID, the date they became a unicorn, and the year the company was founded.
- `funding`: Contains company IDs, their valuation, funding raised, and key investors.
- `industries`: Contains the company ID and the industry the company operates in.
- `companies`: Contains company IDs, names, and locations.

## Objective
- Identify the top 3 performing industries based on the number of unicorns in each industry in the years 2019, 2020, and 2021.
- Calculate the number of unicorns and the average valuation for each of the top industries in those years.
- Return the results sorted by year and the number of unicorns.

## Solution

The solution involves two main queries:

1. **Top Industries Query**: 
   - This query identifies the top 3 industries with the highest number of unicorns in the years 2019, 2020, and 2021. The industries are sorted by the count of unicorns in descending order.

2. **Yearly Rankings Query**: 
   - This query calculates the number of unicorns, the year, and the average valuation for each industry in the years 2019, 2020, and 2021. The valuations are calculated in billions of USD and returned with two decimal precision.

The results are filtered to include only the top 3 industries and are sorted by year and the number of unicorns.

### SQL Query:

```sql
WITH top_industries AS
(
    SELECT i.industry, 
        COUNT(i.*)
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    WHERE EXTRACT(year FROM d.date_joined) in ('2019', '2020', '2021')
    GROUP BY industry
    ORDER BY count DESC
    LIMIT 3
),

yearly_rankings AS 
(
    SELECT COUNT(i.*) AS num_unicorns,
        i.industry,
        EXTRACT(year FROM d.date_joined) AS year,
        AVG(f.valuation) AS average_valuation
    FROM industries AS i
    INNER JOIN dates AS d
        ON i.company_id = d.company_id
    INNER JOIN funding AS f
        ON d.company_id = f.company_id
    GROUP BY industry, year
)

SELECT industry,
    year,
    num_unicorns,
    ROUND(AVG(average_valuation / 1000000000), 2) AS average_valuation_billions
FROM yearly_rankings
WHERE year in ('2019', '2020', '2021')
    AND industry in (SELECT industry
                    FROM top_industries)
GROUP BY industry, num_unicorns, year
ORDER BY year DESC, num_unicorns DESC;
```

## SQL Techniques Used

`WITH`, `COUNT`, `EXTRACT`, `AVG`, `INNER JOIN`, `GROUP BY`, `ORDER BY`, `ROUND`

## Tech Stack

- SQL
