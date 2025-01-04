# NETFLIX MOVIES AND SHOWS DATA ANALYSIS USING SQL

![LOGO](https://github.com/191Srishti/NETFLIX_SQL_PROJECT/blob/main/logo.png)
# Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. 
The goal is to extract valuable insights and answer various business questions based on the dataset. 
The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

# Objectives
* Analyze the distribution of content types (movies vs. TV shows).
* Identify the most common ratings for movies and TV shows.
* List and analyze content based on release years, countries, and durations.
* Explore and categorize content based on specific criteria and keywords.

## Dataset
#### The data for this project is sourced from the Kaggle dataset:
### Dataset Link: [ Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema
```sql
CREATE TABLE NETFLIX_SHOWS
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```
##  BUSINESS PROBLEMS AND SOLUTIONS

### Q1 Count the Number of Movies vs TV Shows.
```sql
SELECT COUNT(*) AS NUMBER_OF_CONTENT, TYPE
FROM NETFLIX_SHOWS
GROUP BY TYPE;
```
#### Objective: Determine the distribution of content types on Netflix.

### Q2  Find the Most Common Rating for Movies and TV Shows.
```sql
SELECT TYPE, RATING FROM(
	SELECT COUNT(*) AS NUMBER_OF_CONTENT, TYPE, RATING,
	RANK() OVER(PARTITION BY TYPE ORDER BY COUNT(*) DESC ) AS RANKING
	FROM NETFLIX_SHOWS
	GROUP BY TYPE, RATING
	)AS T1
WHERE RANKING = 1;
```
#### Objective: Identify the most frequently occurring rating for each type of content.

### Q3 List All Movies Released in a Specific Year (e.g., 2020).
```sql
SELECT * FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
AND
RELEASE_YEAR = '2020'
;
```
#### Objective: Retrieve all movies released in a specific year.

### Q4 Find the Top 5 Countries with the Most Content on Netflix.
```sql
SELECT COUNT(SHOW_ID) AS TOTAL_CONTENT,
UNNEST(STRING_TO_ARRAY(COUNTRY,',')) AS NEW_COUNTRY
FROM NETFLIX_SHOWS
GROUP BY 2
ORDER BY 1 DESC
LIMIT 5;
```
#### Objective: Identify the top 5 countries with the most content items.

### Q5 Identify the Longest Movie
```sql
SELECT *
FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
and duration = (select max(duration)from netflix_shows);
```
#### Objective: Find the movie with the longest duration.

### Q6 Find Content Added in the Last 5 Years.
```sql
SELECT * FROM NETFLIX_SHOWS
WHERE 
TO_DATE(DATE_ADDED, 'Month DD YYYY')>= CURRENT_DATE - INTERVAL '5 Year';
```
#### Objective: Retrieve content added to Netflix in the last 5 years.

### Q7 Find All Movies/TV Shows by Director 'Rajiv Chilaka'.
```sql
SELECT * FROM NETFLIX_SHOWS
WHERE DIRECTOR like '%Rajiv Chilaka%';
```
#### Objective: List all content directed by 'Rajiv Chilaka'.

### Q8 List All TV Shows with More Than 5 Seasons.
```sql
SELECT *
FROM NETFLIX_SHOWS
WHERE TYPE = 'TV Show'
and split_part(duration, ' ', 1):: numeric > 5 ;
```
#### Objective: Identify TV shows with more than 5 seasons.
 
### Q9 Count the Number of Content Items in Each Genre.
```sql
SELECT UNNEST(STRING_TO_ARRAY(LISTED_IN , ',')) AS GENRE,
COUNT(*) 
FROM NETFLIX_SHOWS
GROUP BY 1;
```
#### Objective: Count the number of content items in each genre.

### Q10 Find each year and the average number of content released in India on netflix.
```sql
SELECT extract(year from to_date(date_added , 'Month DD YYYY')) as yearly_content,
round(
		count(*)::numeric /( select count(*) as total from netflix_shows
        where country = 'India' ):: numeric * 100 , 2)
		as "avg/year"
from netflix_shows
where country = 'India'
group by 1; 
```
#### Objective: Calculate and rank years by the average number of content releases by India.
### Q11  List All Movies that are Documentaries
```sql
SELECT * FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
and listed_in like '%Documentaries%';
```
#### Objective: Retrieve all movies classified as documentaries.
### Q12 Find All Content Without a Director
```sql
SELECT * FROM NETFLIX_SHOWS
WHERE DIRECTOR IS NULL;
```
#### Objective: List content that does not have a director.
### Q13 Find how many movies actor Salman Khan appeared in the last 10 years.
```sql
SELECT *
FROM NETFLIX_SHOWS
WHERE  
casts ILIKE '%Salman Khan%'
and release_year > extract(year from  current_date) - 10;
```
#### Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.
### Q14 Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.
```sql
SELECT 
UNNEST(STRING_TO_ARRAY(CASTS, ',')) AS ACTORS,
COUNT(*)
FROM NETFLIX_SHOWS
WHERE COUNTRY like '%India%'
GROUP BY 1
ORDER BY 2 DESC;
```
#### Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.
### Q15 Categorized the content based on keywords like "KILL", and "VIOLENCE" and labeled them as "GOOD" or "BAD". Count how many shows fall in this category.
```sql

CREATE TABLE NEW_TABLE
AS
(
	SELECT *,
		CASE
			WHEN DESCRIPTION ILIKE '%Kill%' or
		    DESCRIPTION ILIKE '%Voilence%' then  'BAD_CONTENT'
			ELSE  'GOOD_CONTENT'
		END  AS CATEGORY
	FROM NETFLIX_SHOWS
);


SELECT CATEGORY,
COUNT(*)
FROM NEW_TABLE
GROUP BY 1;
```
#### Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.


## Findings and Conclusion
### **Content Distribution:** The dataset contains various movies and TV shows with varying ratings and genres.
### **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
### **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
### **Content Categorization:** Categorizing content based on specific keywords helps understand the nature of content available on Netflix.

#### This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.
