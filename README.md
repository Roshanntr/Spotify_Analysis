# Spotify Data Analysis

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. 
This analysis delves into Spotify's music landscape, highlighting the best albums, tracks, and artists. By leveraging data-driven insights, we uncover trends and preferences that shape the listening experience.

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

### Here are the questions I was interested in answering- 

1. Retrieve the names of all tracks that have more than 1 billion streams.
```sql
select * from spotify
select track from spotify
where stream>1000000000
```
2. Count the total number of tracks by each artist.
```sql
select artist,count(*) as Total -- count(*) gives total no.of rows in the table.
from spotify
group by artist
order by total desc
```
3. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
select track,artist,sum(views),sum(likes) from spotify
where official_video=true
group by track,artist
order by 3 desc
```
4. For each album, calculate the total views of all associated tracks.
```sql
select track,album, sum(views) as Total 
from spotify
group by track,album
order by Total desc
```
5. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
select * from 
(select 
     track,
	 coalesce(sum(case when most_played_on='Youtube' then stream end),0) as streamed_on_Youtube,
     coalesce(sum(case when most_played_on='Spotify' then stream end),0) as streamed_on_Spotify
from spotify 
group by track
order by streamed_on_spotify desc) 
where streamed_on_Spotify > streamed_on_Youtube
 	and streamed_on_youtube<>0
```
6. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
with ranking_artist 
as(
select artist,track,sum(views) as Total_View,
dense_rank() over (partition by artist order by sum(views) desc ) as rank
from spotify
group by artist,track -- both artist and track is used to display the tracks of each artist
order by 1,3 desc
)
select * from ranking_artist
where rank<=3
```
7. Write a query to find tracks where the liveness score is above the average.
```sql
select * from spotify

select track,artist,liveness from spotify
where liveness>(select avg(liveness) as Average from  spotify)
```

In conclusion, this analysis reveals the dynamic landscape of music on Spotify, showcasing the top tracks, artists, and albums that resonate most with listeners. The insights highlight emerging trends and preferences, shedding light on the evolving music culture. 


