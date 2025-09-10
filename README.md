# Student Enrollment and Completion Analysis

## Overview

This project analyzes student enrollments and completions in data-related career tracks offered by 365 Data Science. Utilizing SQL for data extraction and Tableau for visualization, the goal is to gain insights into student behavior, track popularity, and completion rates, thereby enhancing 365 Data Science's educational offerings.

## Project Structure

1. **SQL Data Extraction:** Extract data from the SQL database.
2. **Tableau Visualizations:** Generate visual insights from the extracted data.
3. **Analysis and Recommendations:** Analyze results and suggest improvements.

## Repository Files

- `sql_and_tableau.sql`: SQL file for database creation and data population.
- `career_track_completions.csv`: Dataset extracted via SQL for Tableau use.

## SQL Data Extraction

### Database Tables

- **career_track_info**
  - `track_id`: Unique track identifier.
  - `track_name`: Name of the track.

- **career_track_student_enrollments**
  - `student_id`: Unique student identifier.
  - `track_id`: Track identifier.
  - `date_enrolled`: Enrollment date.
  - `date_completed`: Completion date (NULL if incomplete).

### SQL Query

```sql
SELECT 
    ROW_NUMBER() OVER (ORDER BY e.student_id, i.track_name DESC) AS student_track_id,
    e.student_id,
    i.track_name,
    e.date_enrolled,
    IF(e.date_completed IS NULL, 0, 1) AS track_completed,
    DATEDIFF(e.date_completed, e.date_enrolled) AS days_for_completion
FROM
    career_track_student_enrollments e
    JOIN
    career_track_info i ON e.track_id = i.track_id;

SELECT 
    a.*,
    CASE
        WHEN days_for_completion = 0 THEN 'Same day'
        WHEN days_for_completion BETWEEN 1 AND 7 THEN '1 to 7 days'
        WHEN days_for_completion BETWEEN 8 AND 30 THEN '8 to 30 days'
        WHEN days_for_completion BETWEEN 31 AND 60 THEN '31 to 60 days'
        WHEN days_for_completion BETWEEN 61 AND 90 THEN '61 to 90 days'
        WHEN days_for_completion BETWEEN 91 AND 365 THEN '91 to 365 days'
        WHEN days_for_completion > 365 THEN '366+ days'
        ELSE NULL
    END AS completion_bucket
FROM
(
    SELECT 
        ROW_NUMBER() OVER (ORDER BY e.student_id, i.track_name DESC) AS student_track_id,
        e.student_id,
        i.track_name,
        e.date_enrolled,
        IF(e.date_completed IS NULL, 0, 1) AS track_completed,
        DATEDIFF(e.date_completed, e.date_enrolled) AS days_for_completion
    FROM
        career_track_student_enrollments e
        JOIN
        career_track_info i ON e.track_id = i.track_id
) a;
```

### Export

The query results are exported to `career_track_completions.csv`.

## Tableau Visualizations

### Combo Chart
- **Bar Chart:** Number of track enrollments per month.
- **Line Chart:** Percentage of completions per month.

#### Steps:
1. Import `career_track_completions.csv` into Tableau.
2. Create bar and line charts, utilize dual axis and format accordingly.
3. Filter data by career track.

### Completion Bucket Bar Chart
- Bars represent completion buckets by completion count.

#### Steps:
1. Create bar chart with `completion_bucket` and `student_track_id`.
2. Order and filter data appropriately.

## Conclusion

This analysis provides insights into enrollment patterns and completion rates, enabling 365 Data Science to refine their educational offerings and support student success.

[Download README](#)