### Problem Description:
Assume there are three Spotify tables: `artists`, `songs`, and `global_song_rank`, which contain information about the artists, songs, and music charts, respectively.

Write a query to find the top 5 artists whose songs appear most frequently in the Top 10 of the `global_song_rank` table. Display the top 5 artist names in ascending order, along with their song appearance ranking.

#### Notes:
- If two or more artists have the same number of song appearances, they should be assigned the same ranking.
- The rank numbers should be continuous (i.e. 1, 2, 2, 3, 4, 5).
- If you've never seen a rank order like this before, do the rank window function tutorial.

---

#### `artists` Table:
| Column Name  | Type    |
|--------------|---------|
| `artist_id`  | integer |
| `artist_name`| varchar |
| `label_owner`| varchar |

#### Example Input:
| `artist_id` | `artist_name` | `label_owner`        |
|-------------|---------------|----------------------|
| 101         | Ed Sheeran    | Warner Music Group   |
| 120         | Drake         | Warner Music Group   |
| 125         | Bad Bunny     | Rimas Entertainment  |

---

#### `songs` Table:
| Column Name  | Type    |
|--------------|---------|
| `song_id`    | integer |
| `artist_id`  | integer |
| `name`       | varchar |

#### Example Input:
| `song_id` | `artist_id` | `name`          |
|-----------|-------------|-----------------|
| 55511     | 101         | Perfect         |
| 45202     | 101         | Shape of You    |
| 22222     | 120         | One Dance       |
| 19960     | 120         | Hotline Bling   |

---

#### `global_song_rank` Table:
| Column Name  | Type       |
|--------------|------------|
| `day`        | integer    |
| `song_id`    | integer    |
| `rank`       | integer    |

#### Example Input:
| `day` | `song_id` | `rank` |
|-------|-----------|--------|
| 1     | 45202     | 5      |
| 3     | 45202     | 2      |
| 1     | 19960     | 3      |
| 9     | 19960     | 15     |

---

#### Example Output:
| `artist_name` | `artist_rank` |
|---------------|---------------|
| Ed Sheeran    | 1             |
| Drake         | 2             |

**Explanation:**
- Ed Sheeran's song appeared twice in the Top 10 list of `global_song_rank`, while Drake's song is only listed once. Therefore, Ed is ranked #1 and Drake is ranked #2.
- The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify Top 10 Ranked Songs:** Filter the `global_song_rank` table to include only songs ranked 10 or better.
2. **Join Tables:** Use `INNER JOIN` to link `global_song_rank` with `songs` and then with `artists` to associate songs with their respective artists.
3. **Count Song Appearances:** Group by `artist_name` and count the number of Top 10 appearances for each artist.
4. **Rank Artists by Appearances:** Use the `DENSE_RANK()` window function to rank artists by their number of Top 10 appearances.
5. **Limit to Top 5 Artists:** Filter the results to include only the top 5 artists by rank.
6. **Sort Results:** Ensure the output is ordered by artist rank and artist name alphabetically.

---

### Query:
```sql
WITH top_10_cte AS (
  SELECT 
    artists.artist_name,
    DENSE_RANK() OVER (
      ORDER BY COUNT(songs.song_id) DESC
    ) AS artist_rank
  FROM artists
  INNER JOIN songs
    ON artists.artist_id = songs.artist_id
  INNER JOIN global_song_rank AS ranking
    ON songs.song_id = ranking.song_id
  WHERE ranking.rank <= 10
  GROUP BY artists.artist_name
)

SELECT 
  artist_name,
  artist_rank
FROM top_10_cte
WHERE artist_rank <= 5;
