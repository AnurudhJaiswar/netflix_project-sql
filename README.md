# netflix_project-sql
🚀 Netflix Data Analysis Project using SQL! Analyzed Netflix content, genres, ratings, countries, and trends using PostgreSQL. Used CTEs, Window Functions, Aggregations &amp; String Functions to solve real-world business problems and strengthen my SQL &amp; data analytics skills. 📊  #SQL #DataAnalytics #PostgreSQL #DataAnalyst
![Netflix Logo](https://github.com/AnurudhJaiswar/netflix_project-sql/blob/main/Netflix_Logomark.png)
## Objective
-- Netflix Project
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
  show_id      VARCHAR(6),
  type	       VARCHAR(10), 
  title	       VARCHAR(150),
  director	   VARCHAR(208),
  casts	       VARCHAR(1000),
  country      VARCHAR(150),
  date_added   VARCHAR(50),
  release_year  INT,	
  rating	   VARCHAR(10),
  duration	   VARCHAR(15),
  listed_in    VARCHAR(100),
  description  VARCHAR(250)
);

SELECT * FROM netflix;

SELECT 
     COUNT(*) as total_content
FROM netflix;	 


SELECT 
     DISTINCT type
FROM netflix;	 

SELECT * FROM netflix

-- 15 Business Problems 

--1. Count the number of movies vs TV Shows

SELECT 
     type,
	 COUNT(*)
FROM netflix
GROUP BY type 

--2. Find the most common rating for movies and TV shows
SELECT
	TYPE RATING
FROM
	(
	SELECT
			TYPE,
			RATING,
			COUNT(*),
			RANK() OVER (
				PARTITION BY
					TYPE
				ORDER BY
					COUNT(*) DESC
			) AS RANKING
		FROM
			NETFLIX
		GROUP BY
			1, 2
	) AS T1
WHERE
	RANKING = 1

--3. List all movies release in a specific year (e.g., 2020)	
-- filter 2020
-- movies

SELECT * FROM netflix
WHERE 
    type = 'Movie'
    AND
	release_year = 2020

--4. Find the top 5 countries with the most content on netflix 

SELECT 
    UNNEST(STRING_TO_ARRAY(country, ',')) as new_country,
	COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5

SELECT 
     UNNEST(STRING_TO_ARRAY(country, ',')) as new_country
FROM netflix	 
  
--5. Identify the longest movie

SELECT * FROM netflix
WHERE 
     type = 'Movie'
	 AND 
	 duration = (SELECT MAX(duration) FROM netflix)

--6. Find content added in the last 5 years

SELECT 
      * 
FROM netflix
WHERE
    TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years'

SELECT CURRENT_DATE - INTERVAL '5 years'

--7. Find all the movies/TV show by director 'Rajiv Chilaka'!

SELECT * FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%'

--8. List all TV shows with more than 5 seasons

SELECT 
     *
FROM netflix
WHERE 
	 type = 'TV Show'
	 AND
     SPLIT_PART(duration, ' ', 1)::numeric > 5 
	 
-- SELECT
--      SPLIT_PART('Apple Banana Cherry', ' ', 1)

--9. Count the number of content items in each genre 

SELECT      
	  UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
	  COUNT(show_id) as total_content
FROM netflix
GROUP BY 1


--10. Find each year and the average number of content release in India on Netflix.
-- return top 5 year with highest avg content release !

-- total content 333/972

SELECT 
     EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) as year,
	 COUNT(*) as yearly_content,
	 ROUND(
	 COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100
	 ,2)as avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1

--11. List all movies that are documentaries

SELECT * FROM netflix
WHERE
     listed_in ILIKE '%documentaries%'

--12. Find all content without a director

SELECT * FROM netflix
WHERE
    director IS NULL

--13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

SELECT * FROM netflix
WHERE 
     casts ILIKE '%Salman Khan%'
	 AND 
	 release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10 

--14. Find the top 10 actors who have appeared in the highest number od movies product in india.

SELECT  
-- show_id,
-- casts,
UNNEST(STRING_TO_ARRAY(casts, ',')) as actors,
COUNT(*) as total_content
FROM netflix 
WHERE country ILIKE '%india'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10

/* 15. Categorize the content based on the presence of the keywords 'kill' and 'voilence' in the 
 description field. Label content containing these keywords as 'Bad' and all other
 content as 'Good'. Count how many items fall into each category. */

WITH new_table
AS
(
SELECT *,
     CASE 
	 WHEN 
	      description ILIKE '%kill%' OR 
	      description ILIKE '%violence%' THEN 'Bad_Content'
          ELSE 'Good Content'
	END category	  
FROM netflix
)
SELECT 
     category,
	 COUNT(*) as total_content
FROM new_table
GROUP BY 1



WHERE
     description ILIKE '%kill%'
     OR 
	 description ILIKE '%violence%'

