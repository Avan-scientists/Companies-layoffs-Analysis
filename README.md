# Companies-layoffs-Analysis

### Project Overview
 This data Analysis Project aim to provide the insights into company's layoffs in the world over the past years. By Analyzing various aspects of data layoffs, we seek to identify trends, make data-driven recommendations.

 ### Data Sources
  Layoffs data: The primary dataset used for this analysis is the "layoffs.csv" file, containing detailed information about each company's layoffs.

  ### Tools
    1.mySQL server- Data cleaning and Data Analysis
    2.PowerBI-creating a report

   ### Data cleaning and Preparaion
    1.Data inspections
    2.Remove Duplicates
    3.Standardize the data
    4.Remove Null/blank Values
    5.Remove all rows and columns tha aren't necessary
    
### Exploratory Data Analysis
 EDA involved exploring the layoffs data to answer the key questions, such as:
   1.What is overall layoffs trend?
   2.Which company are top layoffs?
   3.What are the total percentage laid_offs


Let start 

```sql
SELECT *
FROM layoffs;
```
Let create some type of staging
```sql
CREATE TABLE layoffs_stage
LIKE layoffs;
```
Inserting values into our new table
```sql
INSERT INTO layoffs_stage
SELECT *
FROM layoffs;
 ----------
SELECT *
FROM layoffs_stage;
```
Find the row_number that will help to find the matches against all of these columns and help to determine duplicated values
```sql
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company,location,total_laid_off,percentage_laid_off,`date`) AS row_num
FROM layoffs_stage
;
```
Create CTEs(Common Table Expressions) to define sub query block that you can then reference within the main query
```sql
WITH duplicate_cte AS(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company,location,industry,total_laid_off,percentage_laid_off,country,`date`) AS row_num
FROM layoffs_stage
)
SELECT *
FROM duplicate_cte
WHERE row_num > 1;
```
```sql
SELECT *
FROM layoffs_stage
WHERE company='Casper';
```
Let create another staging table
```sql
CREATE TABLE `layoffs_stage1` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```
```sql
SELECT *
FROM layoffs_stage1;
```
Inserting the values into our layoffs_stage1
```sql
INSERT INTO layoffs_stage1
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company,location,industry,total_laid_off,percentage_laid_off,country,`date`) AS row_num
FROM layoffs_stage;
```
```sql
SELECT *
FROM layoffs_stage1;
```
```sql
SELECT *
FROM layoffs_stage1
WHERE row_num >1;
```
Deleting the duplicaed Values
```sql
DELETE
FROM layoffs_stage1
WHERE row_num >1;
```
```sql
SELECT *
FROM layoffs_stage1;
```
### Standardizing of data
Here we will use different functions to standardize layoffs data in each column
```sql
SELECT company,TRIM(company)
FROM layoffs_stage1;
```
```sql
UPDATE layoffs_stage1
SET company = TRIM(company);
```
let look on industry
```sql
SELECT *
FROM layoffs_stage1;
```
```sql
SELECT *
FROM layoffs_stage1
WHERE industry LIKE 'Crypto%';
```
```sql

UPDATE layoffs_stage1
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```
```sql

SELECT DISTINCT industry
FROM layoffs_stage;
```
Let look on location
```sql
SELECT *
FROM layoffs_stage1
WHERE industry LIKE 'United States%'
ORDER BY 1;
```
```sql
SELECT DISTINCT country,TRIM(TRAILING'.'FROM country)
FROM layoffs_stage1
ORDER BY 1;
```
```sql
UPDATE layoffs_stage1
SET country = TRIM(TRAILING'.'FROM country)
WHERE country LIKE 'United States%';
```
```sql
SELECT *
FROM layoffs_stage1;
```
Let look on date
```sql
SELECT `date`,
STR_TO_DATE(`date`,'%m/%d/%Y')
FROM layoffs_stage1;
```
```sql
UPDATE layoffs_stage1
SET `date`=STR_TO_DATE(`date`,'%m/%d/%Y');
```
```sql
SELECT `date`
FROM layoffs_stage1;
```
Remove the Null Values from data

Industries has some missing values
```sql
SELECT *
FROM layoffs_stage1
WHERE industry IS NULL
OR industry ='';
```
```sql
SELECT *
FROM layoffs_stage1
WHERE Company LIKE 'Airbnb';
```
```sql
SELECT *
FROM layoffs_stage1;
```
To check a null value in a total_laid_off and Percentage_laid_off
```sql
SELECT *
FROM layoffs_stage1
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```
Deleting a null values
```sql
DELETE
FROM layoffs_stage1
WHERE total_laid_off IS NULL;
```
```sql
DELETE
FROM layoffs_stage1
WHERE percentage_laid_off IS NULL;
```
```sql
SELECT *
FROM layoffs_stage1;
```
Droping all unnecessary columns from the table
```sql
ALTER TABLE layoffs_stage1
DROP COLUMN row_num;
```
```sql
SELECT *
FROM layoffs_stage1;
```
## EXPLORATORY DATA ANALYSIS

1.To find the total_laid_off number of employees and percentage_laid_off
```sql
SELECT MAX(total_laid_off),MAX(percentage_laid_off)
FROM layoffs_stage1;
```
```sql
SELECT *
FROM layoffs_stage1
WHERE percentage_laid_off=1
ORDER BY total_laid_off DESC;
```
```sql
SELECT Company,SUM(total_laid_off)
FROM layoffs_stage1
GROUP BY Company
ORDER BY Company DESC;
```
Let Explory the data basic on date ranges to determine when the pandemic started or covid-19 started and three years later
```sql
SELECT MIN(date),MAX(date)
FROM layoffs_stage1;
```
```sql
SELECT `date`,SUM(total_laid_off)
FROM layoffs_stage1
GROUP BY `date`
ORDER BY 2 DESC;
```
Number affected people in a year
```sql
SELECT YEAR(`date`),SUM(total_laid_off)
FROM layoffs_stage1
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```
You can also find the sum of the percentage laid_off for the company or industries
This is my end of my project
Am grad to have my fist project
I will do my best to craete another beautiful projects













   
