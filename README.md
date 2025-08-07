# SQL_Netflix-Content-Analysis
This project focused on analyzing Netflix’s content library using SQL to extract key insights about content types, release trends, genre popularity, and regional contributions. The dataset included metadata such as title, type (Movie or TV Show), release year, rating, country, genre, director, cast, and duration.
```SQL
SELECT * FROM Netflix

--Q1) Count the number of movies vs TV Shows
SELECT
	type,
	COUNT(type) AS number_of_movies_Tvshows
FROM
	Netflix
GROUP BY
	type

--Q2 Find the most common rating for movies and TV shows
SELECT
	type,
	rating,
	COUNT(rating) AS number_of_rating
FROM
	Netflix
GROUP BY
	type,
	rating
ORDER BY
	COUNT(rating) DESC

--Q3) List all movies released in a 2020
SELECT
	title,
	release_year
FROM
	Netflix
WHERE
	release_year = 2020 AND type = 'Movie'

--Q4) Find the top 5 countries with the most contect on Netflix

SELECT TOP 5
    LTRIM(RTRIM(s.value)) AS Country,
    COUNT(show_id) AS TitleCount
FROM 
    Netflix
CROSS APPLY 
    STRING_SPLIT(country, ',') AS s
WHERE 
    country IS NOT NULL
GROUP BY 
    LTRIM(RTRIM(s.value))
ORDER BY 
   TitleCount DESC;

--Q5) Identify the longest movie?
SELECT
	title,
	type,
    duration,
    TRY_CAST(LEFT(duration, CHARINDEX(' ', duration) - 1) AS INT) AS duration_number
FROM
    Netflix
WHERE
    duration LIKE '%min%' AND type = 'Movie'
GROUP BY
	title,
	type,
	duration
ORDER BY
	duration_number DESC

--Q6) Find contect added in the last 5 years
SELECT *
FROM Netflix
WHERE 
    TRY_CAST(date_added AS DATE) >= DATEADD(YEAR, -5, GETDATE())
ORDER BY
	TRY_CAST(date_added AS DATE) ASC

--Q7) Find all the movies/TV shows by director 'Rajiv Chilaka'
SELECT
	title,
	director
FROM
	Netflix
WHERE
	director LIKE '%Rajiv Chilaka%'

--Q8) List all TV shows with more than 5 seasons
SELECT	
	title,
	duration,
	TRY_CAST(LEFT(duration, CHARINDEX(' ', duration) - 1) AS INT) AS number_of_seasons
FROM
	Netflix
WHERE
	type = 'TV Show' AND TRY_CAST(LEFT(duration, CHARINDEX(' ', duration) - 1) AS INT) > 5
ORDER BY
	TRY_CAST(LEFT(duration, CHARINDEX(' ', duration) - 1) AS INT) DESC

--Q9) Count the number of content items in each genre
SELECT 
    LTRIM(RTRIM(s.value)) AS Genre,
    COUNT(show_id) AS TitleCount
FROM 
    Netflix
CROSS APPLY 
    STRING_SPLIT(listed_in, ',') AS s
GROUP BY 
    LTRIM(RTRIM(s.value))
ORDER BY 
   TitleCount DESC;

--Q10) Find each year and the average numbers of contect release by India on Netflix.
--Return top 5 year with highest avg contect release
SELECT
    YEAR(date_added) AS year,
    COUNT(*) AS number_of_content,
    CAST(COUNT(*) AS FLOAT) / 
        CAST((SELECT COUNT(*) FROM Netflix WHERE country = 'India') AS FLOAT) * 100 AS avg_number_of_content
FROM
    Netflix
WHERE
    country = 'India'
GROUP BY
    YEAR(date_added)

--Q11) List all movies that are documentaries
SELECT * FROM Netflix
SELECT 
	 LTRIM(RTRIM(s.value)) AS Genre,
	 *
FROM
	Netflix
CROSS APPLY 
    STRING_SPLIT(listed_in, ',') AS s
WHERE
	LTRIM(RTRIM(s.value)) = 'Documentaries'

--Q12) Find all content without a director
SELECT *
FROM
	Netflix
WHERE
	director IS NULL
	
--Q13) Find how amny movies actor 'Salman khan' appeared in last 10 years?
SELECT *
FROM
	Netflix
WHERE
	cast LIKE '%Salman Khan%'
	AND
	release_year >= YEAR(DATEADD(YEAR, -10, GETDATE()))
ORDER BY
	date_added DESC



	
