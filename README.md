# Data Analyst Job Market Analysis

## Background
The database structure used in this analysis is illustrated below: 
![Database Structure](/assets/3_Database_Structure.png)


### Key Questions Addressed:

1. **What are the top-paying data analyst jobs?**
2. **What skills are required for these top-paying jobs?**
3. **What skills are most in demand for data analysts?**
4. **Which skills are associated with higher salaries?**
5. **What are the most optimal skills to learn?**

## Tools Utilized

For a comprehensive exploration of the data analyst job market, the following tools were employed:

- **SQL:** The primary tool for querying and extracting insights from the database.
- **PostgreSQL:** The database management system used to handle job posting data.
- **Visual Studio Code:** Utilized for writing and executing SQL queries.
- **Git & GitHub:** Ensured version control, collaboration, and project tracking.

## Analysis Breakdown

### 1. Top-Paying Data Analyst Jobs
To uncover the highest-paying roles, I filtered for remote data analyst jobs, sorted by average yearly salary.

```sql
SELECT
  job_id,
  job_title,
  job_location,
  job_schedule_type,
  salary_year_avg,
  job_posted_date,
  name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
  job_title_short = 'Data Analyst'
  AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

#### Insights:
- **Salary Range:** Top salaries range from $184,000 to $650,000.
- **Diverse Employers:** Companies like SmartAsset, Meta, and AT&T offer lucrative roles.
- **Role Variety:** Job titles include "Data Analyst" to "Director of Analytics," showing diverse opportunities.

![Top 10 highest-paying jobs](assets/1_top_paying_roles.png)


### 2. Skills for Top-Paying Jobs
To determine the skills valued in top-paying jobs, I joined job postings with skills data.

```sql
WITH top_paying_jobs AS (
  SELECT
    job_id,
    job_title,
    salary_year_avg,
    name AS company_name
  FROM job_postings_fact
  LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
  WHERE
    job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
  ORDER BY salary_year_avg DESC
  LIMIT 10
)

SELECT
  top_paying_jobs.*,
  skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

#### Insights:
- **Top Skills:** SQL, Python, and Tableau dominate, with 8, 7, and 6 mentions respectively.
- **Other Key Skills:** R, Snowflake, Pandas, and Excel are also in demand.

![skill demand for top-paying jobs](assets/2_top_paying_roles_skills.png)


### 3. In-Demand Skills for Data Analysts
This query identifies the most frequently requested skills in job postings.

```sql
SELECT
  skills,
  COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_title_short = 'Data Analyst'
  AND job_work_from_home = True
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

#### Insights:
- **Foundational Skills:** SQL and Excel remain essential.
- **Technical Tools:** Python, Tableau, and Power BI highlight the growing importance of technical expertise.

| Skill   | Demand Count |
|---------|--------------|
| SQL     | 7,291        |
| Excel   | 4,611        |
| Python  | 4,330        |
| Tableau | 3,745        |
| Power BI| 2,609        |

### 4. Skills Associated with Higher Salaries
This query reveals which skills are linked to higher average salaries.

```sql
SELECT
  skills,
  ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_title_short = 'Data Analyst'
  AND salary_year_avg IS NOT NULL
  AND job_work_from_home = True
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```

#### Insights:
- **Big Data & ML Tools:** PySpark, Couchbase, and DataRobot command top salaries.
- **Cloud Proficiency:** Tools like Databricks and GCP significantly boost earning potential.

| Skills        | Avg Salary ($) |
|---------------|----------------|
| PySpark       | 208,172        |
| Couchbase     | 160,515        |
| DataRobot     | 155,486        |
| Pandas        | 151,821        |
| Elasticsearch | 145,000        |

### 5. Most Optimal Skills to Learn
Combining demand and salary data, this query identifies the most strategic skills to acquire.

```sql
SELECT
  skills_dim.skill_id,
  skills_dim.skills,
  COUNT(skills_job_dim.job_id) AS demand_count,
  ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_title_short = 'Data Analyst'
  AND salary_year_avg IS NOT NULL
  AND job_work_from_home = True
GROUP BY skills_dim.skill_id
HAVING COUNT(skills_job_dim.job_id) > 10
ORDER BY avg_salary DESC, demand_count DESC
LIMIT 25;
```

#### Insights:
- **Programming:** Python and R lead in both demand and salary.
- **Cloud & Data Engineering:** Snowflake, Azure, AWS, and BigQuery are highly valued.
- **BI Tools:** Tableau and Looker highlight the importance of data visualization.

| Skill ID | Skills      | Demand Count | Avg Salary ($) |
|----------|-------------|--------------|----------------|
| 8        | Go          | 27           | 115,320        |
| 234      | Confluence  | 11           | 114,210        |
| 97       | Hadoop      | 22           | 113,193        |
| 80       | Snowflake   | 37           | 112,948        |
| 77       | BigQuery    | 13           | 109,654        |

## Conclusion

This analysis provided actionable insights into the data analyst job market, identifying high-paying opportunities, in-demand skills, and optimal areas for skill development. Aspiring data analysts can leverage these findings to strategically position themselves in the industry, focusing on high-demand and high-salary skills to enhance career prospects.

