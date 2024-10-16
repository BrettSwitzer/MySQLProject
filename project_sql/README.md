# Introduciton
Dive into the data job market. Focusing on data analyst roles, this project explores top-paying jobs, in demand skills, and where high demand meets high salary in data analystics

SQL queries? Check them out here [projects_sql_folder](/project_sql).

# Background
This project was created as part of a [SQL Training Course](https://www.lukebarousse.com/sql) put on by [Luke Barousse](https://www.lukebarousse.com). I wanted to take this course to start to develop SQL skills and knowledge as I am looking to widen my scope of skills from my previous experience of financial analyst into something more techanical such as data or business analyst. 

Data comes from the course provided by Luke, as mentioned above. 

## Questions that I was looking to answer through this course were:

1. What are top-paying data analyst jobs?
2. What skills are requred for these top-paying jobs?
3. What skills are most in-demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used
For this project I was taught and utilized several key tools:

- **SQL**: The backbone of the project and analysis of this project. I was able to query the datbase to pull out insightful information that really widen my awareness job postings and workforce in the data analyst workspace.
- **PostgreSQL**: This was the data management system used for the project as it was ideal for handling the job posting data, but also as informed by Luke in this course, a more common sought after skill.
- **Visual Studio Code**: The code editor system that was also highlighted as a common and sought after skill.
- **Git & Github**: This plateform is being used to manage version control and abling me to share the findings for both data analysis outcomes and my skills improved on and acquired through this course.

# The Analysis
Each query for this project was aimed at investigating specific aspects of the data analyst job market. This is the way each question was approached.

### 1. Top Paying Data Analyst Jobs
In the attempt to pull out the data for highest paying jobs that were posted at the time of the data pull, I had filtered for data analyst posted jobs, in a remote setting so long as the post included a salary. This highlighted the highest paying opportunites at that time.


``` sql
SELECT 
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC
LIMIT 10;
```
The conclusions I found while doing this analysis:
- **Large Salary Varience:** Top paying jobs ranged from $650,000 down to $184,000, showing significant earnings potential.
- **Diverse Employeers:** Industries that are respresented in the top 10 are varied from tech companies (Meta and SmartAsset) to healthcare (uclahheathcareers) and telecom (ATT&T). Worth noting that SmartAsset did make the list twice ($205,000 & $186,000).
- **Job Title Variety:** The titles of the top 10 paying roles are varied including Director and Principal but also three times just 'Data Analyst' is included.

![Top Paying Roles](project_sql\Assets\Query_2_top_paying_job_skills.png)

### 2. Top Paying Data Analyst Skills
The goal of this analysis was to find out which skills are associated with the highest paying roles available in the job market. I utilized a left join in order to combined two data bases together allowing me to have each job posting display all associated skills. 

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        job_location,
        job_schedule_type,
        salary_year_avg,
        job_posted_date,
        name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY 
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY   
    salary_year_avg DESC
```

The conclusions I found while pulling this data was that there are a few specific skills that seem consistent in what employeers are looking for, a database manipulating skill in SQL, a coding language in Python and a visualization tool in Tableau. Summarized totals below:
- **SQL** - Required in 8 job postings
- **Python** - Required in 7 job postings
- **Tableau** - Required in 6 job postings
- **R** - Required in 4 job postings
- **Snowflake, Pandas, and Excel** - Each required in 3 job postings
- **Azure, Bitbucket, and Go** - Each required in 2 job postings

![Top Skills for High Paying Roles](project_sql\Assets\Query_2_top_paying_job_skills.png)


### 3. Most In-Demand Skills
The goal of this investigation was to see out of all the job postings, which skills are asked for the most regardless of salary (included the roles that did not state salaries.) This gave an insight on any skills that may be less specialized and more sought after across all Data Analyst roles. This query allowed me to practice and improve on the inner join function as well as count. In doing so I was able to sort the data by the number of times each skill was mentioned in all of the open roles. 

```sql
SELECT 
    skills,
    COUNT (skills_job_dim.job_id) AS demand_count
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE 
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY
    demand_count DESC
LIMIT 5 
```
The conclusions I found from pulling this data: 
The top skills were not too much different from the highest paying roles:
- **SQL, Python and data visualaztion skills (Tableau & PowerBI):** Still in high demand emphasising the importance to build these skills and become experts with them as they will carry you to higher paying jobs. 
- **Excel:** This skill is mentioned in a far greater amount when not specifically including highest paying jobs. This leads me to conlcude that excel is a great skill for entry-level or less specialized job titles in the Data Analyst field.

| Skill     | Demand Count |
|-----------|--------------|
| SQL       | 7291         |
| Excel     | 4611         |
| Python    | 4330         |
| Tableau   | 3745         |
| PowerBI   | 2609         |


### 4. Top Paying Skills
This query was designed to get knowledge on which specific skills will allow you to get paid the most.This query again allowed me to improve and get more comfortable with join techniques as I joined 3 databases to obtain the proper output I was looking for, filtering by salary and grouping by skills.
```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE 
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```
These results were inline with what I had thought they would be, having more specialized skills be the requirement over general ones for the highest paying. Interesting to note that number 23 on the list of top 25 is postgreSQL, the data management system used for this project.

| Skill          | Avg Salary     |
|----------------|----------------|
| pyspark        | $208,172       |
| bitbucket      | $189,155       |
| couchbase      | $160,515       |
| watson         | $160,515       |
| datarobot      | $155,486       |
| gitlab         | $154,500       |
| swift          | $153,750       |
| jupyter        | $152,777       |
| pandas         | $151,821       |
| elasticsearch  | $145,000       |
| golang         | $145,000       |
| numpy          | $143,513       |
| databricks     | $141,907       |
| linux          | $136,508       |
| kubernetes     | $132,500       |
| atlassian      | $131,162       |
| twilio         | $127,000       |
| airflow        | $126,103       |
| scikit-learn   | $125,781       |
| jenkins        | $125,436       |
| notion         | $125,000       |
| scala          | $124,903       |
| postgresql     | $123,879       |
| gcp            | $122,500       |
| microstrategy  | $121,619       |


### 5. Optimal Skills
This final query for the project is designed to gain insight on what skills are the most optimal, which are in high demand that also pay well. This would give insight on which skills to target learning, and become and expert in first. 

```sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT (skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
    WHERE 
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY 
        skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_job_dim.skill_id, 
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
    WHERE 
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    GROUP BY 
        skills_job_dim.skill_id
)

SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE
    demand_count>10
ORDER BY
    demand_count DESC,
    avg_salary DESC
LIMIT 25
```
| Skill         | Demand Count | Avg Salary  |
|---------------|--------------|-------------|
| sql           | 398          | $97,237     |
| excel         | 256          | $87,288     |
| python        | 236          | $101,397    |
| tableau       | 230          | $99,288     |
| r             | 148          | $100,499    |
| power bi      | 110          | $97,431     |
| sas           | 63           | $98,902     |
| sas           | 63           | $98,902     |
| powerpoint    | 58           | $88,701     |
| looker        | 49           | $103,795    |
| word          | 48           | $82,576     |
| snowflake     | 37           | $112,948    |
| oracle        | 37           | $104,534    |
| sql server    | 35           | $97,786     |
| azure         | 34           | $111,225    |
| aws           | 32           | $108,317    |
| sheets        | 32           | $86,088     |
| flow          | 28           | $97,200     |
| go            | 27           | $115,320    |
| spss          | 24           | $92,170     |
| vba           | 24           | $88,783     |
| hadoop        | 22           | $113,193    |
| jira          | 20           | $104,918    |
| javascript    | 20           | $97,587     |
| sharepoint    | 18           | $81,634     |

This data was interesting as it showed the usual skills at the top:
- **SQL:** the most in demand and showing a fairly high average salary in relation to other more frequent skills.
- **Excel:** this does not surprise me as a higher in demand skill, yet as it is not overlly complicated to get basics down it is not the highests of paying skills, but valuable non-the-less.
- **Tableau & PowerBI:** Quite high in demand as well as higher paying in relation to the other roles posted, Tableau does seem to differiate itself from PowerBI here.
- **Python & R**: The two top programming languages show up in close relation, however similar to the data visualization skills, there is a clear choice for the more optimal skill to pursue, that being Python.

# What I Learned

I started this project as a way to get my feet wet with some technical skills as I am pursing a more technical career path. Starting with limited knowledge on SQL not only was I able to develop some of the basic skills such as "SELECT" and "FROM" but to be able to use Joins, subqueries and CTEs. While this course is labeled an SQL course, the skills it helped develop with PostgreSQL 

# Conclusions



