# Spotify Advanced SQL Project 
Project Category: Advanced
[Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify_logo_horizontal_black](https://github.com/user-attachments/assets/6258bbd7-b2df-4312-ad76-ffaa340e3e1a)


## Overview
This project involves analysing the Spotify dataset from Kaggle with various attributes about tracks, albums, artists and so on using **PostgreSQL**. It covers an end-to-end process of normalizing a denormalised dataset and performing SQL queries of varying complexity (easy, medium, and advanced). The primary goal of the project is to generate valuable insights from the dataset.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyse the data.

---

## Questions Analysed

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.

```sql
SELECT * 
FROM spotify 
WHERE stream > 1000000000;
```

2. List all albums along with their respective artists.

```sql
SELECT DISTINCT album, 
                artist
FROM spotify
ORDER BY 1;
```

3. Get the total number of comments for tracks where `licensed = TRUE`.

```sql
SELECT sum(comments) as total_comments
FROM spotify
WHERE licensed = 'TRUE';
```

4. Find all tracks that belong to the album type `single`.

```sql
SELECT *
FROM spotify
WHERE album_type = 'single';
```

5. Count the total number of tracks by each artist.

```sql
SELECT artist, 
       COUNT(track) as total_tracks
FROM spotify
GROUP BY artist
ORDER BY total_tracks;
```

6. Calculate the average danceability of tracks in each album.

```sql
SELECT album, 
       avg(danceability) as avg_danceability
FROM spotify
GROUP BY album
ORDER BY avg_danceability DESC;
```

7. Find the top 5 tracks with the highest energy values.

```sql
SELECT track, 
       MAX(energy) as highest_energy
FROM spotify
GROUP BY track
ORDER BY highest_energy DESC LIMIT 5;
```

8. List all tracks along with their views and likes where `official_video = TRUE`.

```sql
SELECT track, 
       SUM(views) as total_views, 
       SUM(likes) as total_likes
FROM spotify
WHERE official_video = 'true'
GROUP BY track
ORDER BY total views DESC;
```

9. For each album, calculate the total views of all associated tracks.

```sql
SELECT album, track,
       SUM(views) as total_views
FROM spotify
GROUP BY album, track
ORDER BY total_views DESC;
```

10. Retrieve the track names that have been streamed on Spotify more than YouTube.

```sql
SELECT artist, 
       COUNT(track) as total_tracks
FROM spotify
GROUP BY artist
ORDER BY total_tracks;
```

11. **Find the top 3 most-viewed tracks for each artist using window functions.**
```sql
WITH artist_ranks 
AS
(SELECT artist, track,
        SUM (views) as total_views,
        DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) as RANK
FROM spotify
GROUP BY artist, track
ORDER BY  artist, total_views DESC)

SELECT *
FROM artist_ranks
WHERE RANK <=3;
```

12. **Write a query to find tracks where the liveness score is above the average.**
```sql
SELECT track, liveness
FROM spotify 
WHERE liveness >
(SELECT AVG(liveness)
FROM spotify ); 
```

13. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte 
AS
(SELECT album,
        MAX(energy) as highest_energy,
        MIN(energy) as lowest_energy
FROM spotify
GROUP BY album)

SELECT album,
       (highest_energy-lowest_energy) as energy_diff
FROM cte
ORDER BY energy_diff DESC;
```
   
14. Find tracks where the energy-to-liveness ratio is greater than 1.2.
```sql
SELECT track, 
       energy_liveness
FROM spotify
WHERE energy_liveness > 1.2;
```

15.**Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.**
```sql
SELECT 
	track,
	SUM(likes) OVER (ORDER BY views) AS cumulative_likes
FROM Spotify
ORDER BY cumulative_likes DESC;
```

---

