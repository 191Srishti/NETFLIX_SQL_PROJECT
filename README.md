# NETFLIX MOVIES AND SHOWS DATA ANALYSIS USING SQL

![LOGO](https://github.com/191Srishti/NETFLIX_SQL_PROJECT/blob/main/logo.png)
# Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. 
The goal is to extract valuable insights and answer various business questions based on the dataset. 
The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

# Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset
The data for this project is sourced from the Kaggle dataset:

## Dataset Link: Movies Dataset
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
### Q1 COUNT NUMBER OF MOVIES VS TV SHOWS
...sql
SELECT COUNT(*) AS NUMBER_OF_CONTENT, TYPE
FROM NETFLIX_SHOWS
GROUP BY TYPE;
...

### Q2 FIND THE MOST COMMON RATING FOR MOVIES AND TV SHOWS
...sql
SELECT TYPE, RATING FROM(
	SELECT COUNT(*) AS NUMBER_OF_CONTENT, TYPE, RATING,
	RANK() OVER(PARTITION BY TYPE ORDER BY COUNT(*) DESC ) AS RANKING
	FROM NETFLIX_SHOWS
	GROUP BY TYPE, RATING
	)AS T1
WHERE RANKING = 1;
...

### Q3 LIST ALL MOVIES IN A SPECIFIC YEAR EG(2020)
...sql
SELECT * FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
AND
RELEASE_YEAR = '2020'
;
...
-- Q4 FIND THE TOP 5 MOST COUNTRIES WITH MOST CONTENT

SELECT COUNT(SHOW_ID) AS TOTAL_CONTENT,
UNNEST(STRING_TO_ARRAY(COUNTRY,',')) AS NEW_COUNTRY
FROM NETFLIX_SHOWS
GROUP BY 2
ORDER BY 1 DESC
LIMIT 5;

--  Q5 IDENTIFY LONGEST MOVIE
SELECT *
FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
and duration = (select max(duration)from netflix_shows)

--  Q6 FIND CONTENT ADDED IN LAST FIVE YEARS

SELECT * FROM NETFLIX_SHOWS
WHERE 
TO_DATE(DATE_ADDED, 'Month DD YYYY')>= CURRENT_DATE - INTERVAL '5 Year'

--  Q7 FIND ALL THE MOVIES AND SHOWS DIRECTED BY 'RAJIV CHILAKA'

SELECT * FROM NETFLIX_SHOWS
WHERE DIRECTOR like '%Rajiv Chilaka%'


-- Q8 LIST ALL TV SHOWS MORE THAN 5 SEASON

SELECT *
FROM NETFLIX_SHOWS
WHERE TYPE = 'TV Show'
and split_part(duration, ' ', 1):: numeric > 5 

 
-- Q9 COUNT NUMBER OF CONTENT OF EACH GENRE

SELECT UNNEST(STRING_TO_ARRAY(LISTED_IN , ',')) AS GENRE,
COUNT(*) 
FROM NETFLIX_SHOWS
GROUP BY 1;


-- Q10 FIND EACH YEAR AND AVG NUMBER OF CONTENT RELEASE BY INDIA ON NETFLIX.
-- RETURN TOP 5 YEAR WITH MAX AVG CONTENT RELEASE

SELECT extract(year from to_date(date_added , 'Month DD YYYY')) as yearly_content,
round(
		count(*)::numeric /( select count(*) as total from netflix_shows
        where country = 'India' ):: numeric * 100 , 2)
		as "avg/year"
from netflix_shows
where country = 'India'
group by 1 

-- Q11  LIST ALL MOVIES THAT ARE DOCUMENTAROY

SELECT * FROM NETFLIX_SHOWS
WHERE TYPE = 'Movie'
and listed_in like '%Documentaries%'

-- Q12 FIND ALL CONTENT WITHOUT DIRECTOR
SELECT * FROM NETFLIX_SHOWS
WHERE DIRECTOR IS NULL

-- Q13 FIND IN HOW MANY MOVIES ACTOR SALMAN KHAN APPEARED IN LAST 10 YEARS
SELECT *
FROM NETFLIX_SHOWS
WHERE  
casts ILIKE '%Salman Khan%'
and release_year > extract(year from  current_date) - 10

-- Q14 FIND TOP 10 ACTORS WHO APPEARED IN MAX NUMBER OF MOVIES PRODUCED IN INDIA
SELECT 
UNNEST(STRING_TO_ARRAY(CASTS, ',')) AS ACTORS,
COUNT(*)
FROM NETFLIX_SHOWS
WHERE COUNTRY like '%India%'
GROUP BY 1
ORDER BY 2 DESC


-- Q15 CATEGORISED THE CONTENT BASSED ON KEYWORDS LIKE "KILL" , "VOILENCE" . and lebled as "GOOD" and  "BAD"
--  COUNT HOW MANY SHOWS FALL IN THIS CATEGORIES


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
)


SELECT CATEGORY,
COUNT(*)
FROM NEW_TABLE
GROUP BY 1









Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.
