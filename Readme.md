## Introduction

Welcome to my SQL Project Portfolio, my dive into the data job market focusing on `Data Analyst` jobs. This project is my exploration into grasping the top-paying jobs, skills that are in-demand, and the junction between high demand and high paying in this job field.

## Background

The motivation behind this project is to enhance my skills in diving into data as well as allowing me to better understand the data analyst job market better. Through recognizing the most paid and in demand skills, it allows me to enhance myself suitably for the current job market and nagivate through it with more precise targeting.

The data for this analysis is from [Luke Barousse’s SQL Course](https://www.lukebarousse.com/sql). This data includes details on job titles, salaries, locations, and required skills. 

The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn for a data analyst looking to maximize job market value?

## Tools I Used

In this project, I utilized a variety of tools to conduct my analysis:

- **SQL** (Structured Query Language): Enabled me to interact with the database, extract insights, and answer my key questions through queries.
- **PostgreSQL**: As the database management system, PostgreSQL allowed me to store, query, and manipulate the job posting data.
- **Visual Studio Code:** This open-source administration and development platform helped me manage the database and execute SQL queries.

## Analysis

Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

### 1. Top Paying Data Analyst Jobs

To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date
FROM
    job_postings_fact
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
ORDER BY
    salary_year_avg DESC
LIMIT 10
```

![Average Salary Distribution of the Top 10 Paying Data Analyst Jobs in 2023](<SQL data visualization/1.Average Salary Distribution of the Top 10 Paying Data Analyst Jobs in 2023.png>)
*Bar graph visualizing the salary distribution of the top 10 Data Analyst Jobs, produced using Python*

Here's the breakdown of the top data analyst jobs in 2023:
- **Unique Salary Range:** Top 10 paying data analyst roles span from $184,000 to $650,000, however, the large range stems mostly from the top 2 being outliers, indicating salary in the field is competive across companies and similar positions.
- **Job Title Variety:** There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

### 2. Skills for Top Paying Jobs

To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg
    FROM
        job_postings_fact
    WHERE
        job_title_short ='Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_location = 'Anywhere'
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)
SELECT
    top_paying_jobs.job_id,
    job_title,
    salary_year_avg,
    skills
FROM
    top_paying_jobs
INNER JOIN skills_job_dim ON skills_job_dim.job_id = top_paying_jobs.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
ORDER BY
    salary_year_avg DESC
```

 ![Skill Count for Top 10 Paying Data Analyst Roles in 2023](<SQL data visualization/2.Skill Count for Top 10 Paying Data Analyst Roles in 2023.png>)   
 *Bar graph visualizing the frequencies of demanded skills of the top 10 Data Analyst Jobs, produced using Python*


Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:
- Core specialized skills such as **SQL**, **Python**, **Tableu** still remains in high demand, showing the importance of fundamentals.
- Other skills like **R**, **Snowflake**, **Pandas**, and **Excel** shows varying degree of appearance rate, with only **Excel** being a common skill, indicating more specialized skillsets is a factor for high-paying jobs.


### 3. In-Demand Skills for Data Analysts

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(sd.skill_id) AS skill_counts
FROM
    skills_dim AS sd
INNER JOIN skills_job_dim AS sjd ON sjd.skill_id = sd.skill_id
INNER JOIN job_postings_fact AS jpf ON sjd.job_id = jpf.job_id
WHERE
    job_title_short = 'Data Analyst'
GROUP BY
    skills
ORDER BY
    skill_counts DESC
LIMIT 10
```

| skills   | skill_counts |
|----------|--------------|
| sql      | 92628        |
| excel    | 67031        |
| python   | 57326        |
| tableau  | 46554        |
| power bi | 39468        |

*Table of the demand for the top 5 skills in data analyst job postings*

Here's the breakdown of the most demanded skills for data analysts in 2023
- Demand for **SQL** is the highest, which indicates the tools & related skill set to be an absolute necessity in today's data analyst roles.
- **Excel** follows closely behind, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
- Programming and Visualization Tools like **Python**, **Tableau**, and **Power BI** are essential, pointing towards the increasing importance of technical skills in data storytelling and decision support.

### 4. Skills Based on Salary

Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg),0) AS avg_salary_skill
FROM
    skills_dim
INNER JOIN skills_job_dim ON skills_job_dim.skill_id = skills_dim.skill_id
INNER JOIN job_postings_fact ON skills_job_dim.job_id = job_postings_fact.job_id
WHERE
    salary_year_avg IS NOT NULL
    AND job_title_short = 'Data Analyst'
GROUP BY
    skills
ORDER BY
    avg_salary_skill DESC
LIMIT 15
```



| skills    | avg_salary_skill |
|-----------|------------------|
| svn       | 400000           |
| solidity  | 179000           |
| couchbase | 160515           |
| datarobot | 155486           |
| golang    | 155000           |
| mxnet     | 149000           |
| dplyr     | 147633           |
| vmware    | 147500           |
| terraform | 146734           |
| twilio    | 138500           |
| gitlab    | 134126           |
| kafka     | 129999           |
| puppet    | 129820           |
| keras     | 127013           |
| pytorch   | 125226           |

*Table of the average salary for the top 10 paying skills for data analysts*

Here's a breakdown of the results for top paying skills for Data Analysts:

- High Demand for Big Data & ML Skills: Top salaries are commanded by analysts skilled in big data technologies (PySpark, Couchbase), machine learning tools (DataRobot, Jupyter), and Python libraries (Pandas, NumPy), reflecting the industry's high valuation of data processing and predictive modeling capabilities.
- Software Development & Deployment Proficiency: Knowledge in development and deployment tools (GitLab, Kubernetes, Airflow) indicates a lucrative crossover between data analysis and engineering, with a premium on skills that facilitate automation and efficient data pipeline management.
- Cloud Computing Expertise: Familiarity with cloud and data engineering tools (Elasticsearch, Databricks, GCP) underscores the growing importance of cloud-based analytics environments, suggesting that cloud proficiency significantly boosts earning potential in data analytics.

- Big Data & ML Skills: Overall, the top paying skills for analysts are dominated by data technologies skills (SVN, Couchbase), machine learning tools (Datarobot, Mxnet) and programming libraries (dplyr, keras), showcasing an increasing trend in interest of predictive modeling capabilities and enduring high valuation of data processing
- Software Development & Deployment Proficiency: Development and deployment tools (GitLab, Golang, Solidity) indicates that specific companies or fields prefer usage of niche tools that suits their specific developmental and data pipeline management needs.
- Cloud Computing Expertise: Usage of cloud and data engineering tools (Vmware, Twilio) along with general increase in usage of cloud provider services such as Amazon indicate that needs for expertise in cloud tools, opening more opportunities and potential boost for earnings among data analysts.

### 5. Most Optimal Skills to Learn

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
SELECT
    sd.skill_id,
    skills,
    COUNT(sd.skill_id) AS skill_counts,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
    skills_dim AS sd
INNER JOIN skills_job_dim AS sjd ON sjd.skill_id = sd.skill_id
INNER JOIN job_postings_fact AS jpf ON sjd.job_id = jpf.job_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    sd.skill_id
HAVING
    COUNT(sd.skill_id) > 10
ORDER BY
    avg_salary DESC
LIMIT 15
```

| skill_id | skills     | skill_counts | avg_salary |
| :------- | :--------- | :----------- | :--------- |
| 8        | go         | 27           | 115320     |
| 234      | confluence | 11           | 114210     |
| 97       | hadoop     | 22           | 113193     |
| 80       | snowflake  | 37           | 112948     |
| 74       | azure      | 34           | 111225     |
| 77       | bigquery   | 13           | 109654     |
| 76       | aws        | 32           | 108317     |
| 4        | java       | 17           | 106906     |
| 194      | ssis       | 12           | 106683     |
| 233      | jira       | 20           | 104918     |
| 79       | oracle     | 37           | 104534     |
| 185      | looker     | 49           | 103795     |
| 2        | nosql      | 13           | 101414     |
| 1        | python     | 236          | 101397     |
| 5        | r          | 148          | 100499     |

*Table of the most optimal skills for data analyst sorted by salary*

- High-Demand Programming Languages: Among these, Python and R stands out the most, with demand counts of 236 and 148 respectively. Along with their high demands, their average salaries are around $101,397 for Python and $100,499 for R, indicating they are an important staple of data analyst roles.
- Cloud Tools and Technologies: Skills in specialized technologies such as Snowflake, Azure, AWS, and BigQuery show significant demand with relatively high average salaries, indicating growing interest in the possibilities of cloud platforms and big data technologies in data analysis.
- Business Intelligence and Visualization Tools: Tableau and Looker, with demand counts of 230 and 49 respectively, and average salaries around $99,288 and $103,795, highlighting the critical role of data visualization and business intelligence in extracting actionable insights from data.
- Database Technologies: The demand for skills in traditional and NoSQL databases (Oracle, SQL Server, NoSQL) with average salaries ranging from $97,786 to $104,534, reflects the continued need for data storage, retrieval, and management expertise.

# What I Learned

Throughout this adventure, I've enhanced my SQL toolkit with varios additions:

- **Complex Query Crafting:** Integration of advanced SQL practices, such as merging tables and WITH clauses allowing the usage of temp tables.
- **Data Aggregation:** Increase familiarity of GROUP BY() and turned aggregate functions such as COUNT() and AVG() to optimize my data-summary.
- **Analytical Mindset:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions

### Insights
From the analysis, several general insights emerged:

1. **Top-Paying Data Analyst Jobs**: The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries, the highest at $650,000!
2. **Skills for Top-Paying Jobs**: High-paying data analyst jobs require advanced proficiency in SQL, suggesting it’s a critical skill for earning a top salary.
3. **Most In-Demand Skills**: SQL is also the most demanded skill in the data analyst job market, thus making it essential for job seekers.
4. **Skills with Higher Salaries**: Specialized skills, such as SVN and Solidity, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value.

### Closing Thoughts

This project enhanced my SQL skills and provided valuable insights into the data analyst job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data analysts can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerging trends in the field of data analytics.