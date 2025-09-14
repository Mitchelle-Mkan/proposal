# proposal
## Final Project Submission

Please fill out:
* Student name: Godfrey Osundwa, Mitchel Mkan, Lameck Odallo, Elizabeth Kiilu
* Student pace: self paced / part time / full time
* Scheduled project review date/time: 
* Instructor name: 
* Blog post URL:


### Beginning

The global film industry is one of the most influential and profitable entertainment sectors, generating billions of dollars annually across theaters, streaming, and merchandising. As audience preferences evolve and competition intensifies, studios and investors must carefully evaluate what drives box office success. This project explores key factors such as **genre trends, studio performance, audience ratings, and blockbuster economics** to identify patterns and opportunities in global cinema revenues.

### Overview

This analysis examines worldwide gross earnings of films across multiple genres, studios, and years, complemented by viewer ratings from IMDb,TMDB, BOM and RT. By combining descriptive analytics, trend visualization, and correlation analysis, the project seeks to uncover:

  **Which genres consistently generate the highest revenue.**

  **How studios differ in performance across genres.**

  **The role of ratings (quality perception) versus franchise power in determining box office success.**

 **The impact of blockbuster outliers on overall market dynamics.**

The findings provide both strategic insights for major studios looking to maximize global returns and practical guidance for smaller studios seeking to identify profitable niches.

### Business Understanding

The central business problem addressed is:
**“What factors most strongly influence worldwide box office performance, and how can studios optimize production and investment decisions accordingly?”**

From a business standpoint, the analysis supports:

Major studios: deciding whether to continue investing heavily in franchises/IP or diversify into new genres.

Mid-tier & independent studios: identifying under-served genres where lower-budget films can still achieve profitability.

Investors & stakeholders: understanding the balance between audience ratings, critical reception, and franchise appeal in driving revenue.

Ultimately, the business objective is to provide actionable insights that help industry players maximize profitability while minimizing risk in an increasingly competitive global film market.

### Data Understanding

The dataset combines information from multiple sources, covering films released worldwide between 2010–2018. The main features include:

**Movie Information: title, release year, runtime, primary genre, and studio.**

**Financial Performance: worldwide gross earnings (box office revenue).**
**Ratings: IMDb and TMDB average ratings, reflecting audience reception.**

**Aggregated Features: computed metrics such as total, mean, and median gross by studio and genre.**

Data Quality & Preparation

Missing values in runtime and ratings were handled by dropping or imputing when necessary.

Gross earnings were filtered to remove zero or invalid entries.

Outliers (extreme blockbusters) were analyzed separately rather than removed, since they are strategically important.

Categorical variables (genre, studio) were standardized for grouping and pivot analysis.

This prepared dataset ensures reliability for both descriptive and exploratory analysis.

### Data Analysis


**Lets Import Necessary Tools**


```python
# Your code here - remember to use markdown cells for comments as well!
import pandas as pd
import numpy as np
import sqlite3
import matplotlib.pyplot as plt
import seaborn as sns
import os

```

### Loading The Data to Jupyter Notebook

**The Numbers**


Data Exploration (What is in the zippedData)


```python
import os

data_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData"

print(os.listdir(data_path))  # See which files are inside
```

    ['bom.movie_gross.csv.gz', 'im.db.zip', 'rt.movie_info.tsv.gz', 'rt.reviews.tsv.gz', 'tmdb.movies.csv.gz', 'tn.movie_budgets.csv.gz']



```python
#Using absolute path

tn = pd.read_csv(
    r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData\tn.movie_budgets.csv.gz"
)
print(tn.shape)
print(tn.head())
```

    (5782, 6)
       id  release_date                                        movie  \
    0   1  Dec 18, 2009                                       Avatar   
    1   2  May 20, 2011  Pirates of the Caribbean: On Stranger Tides   
    2   3   Jun 7, 2019                                 Dark Phoenix   
    3   4   May 1, 2015                      Avengers: Age of Ultron   
    4   5  Dec 15, 2017            Star Wars Ep. VIII: The Last Jedi   
    
      production_budget domestic_gross worldwide_gross  
    0      $425,000,000   $760,507,625  $2,776,345,279  
    1      $410,600,000   $241,063,875  $1,045,663,875  
    2      $350,000,000    $42,762,350    $149,762,350  
    3      $330,600,000   $459,005,868  $1,403,013,963  
    4      $317,000,000   $620,181,382  $1,316,721,747  


### Box Office Mojo

bom = pd.read_csv(f"{data_path}/bom.movie_gross.csv.gz")
print(bom.shape)
print(bom.head())
print(bom.columns)


```python
data_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData"

tn = pd.read_csv(f"{data_path}/tn.movie_budgets.csv.gz")
print(tn.shape)
print(tn.head())
print(tn.columns)
```

    (5782, 6)
       id  release_date                                        movie  \
    0   1  Dec 18, 2009                                       Avatar   
    1   2  May 20, 2011  Pirates of the Caribbean: On Stranger Tides   
    2   3   Jun 7, 2019                                 Dark Phoenix   
    3   4   May 1, 2015                      Avengers: Age of Ultron   
    4   5  Dec 15, 2017            Star Wars Ep. VIII: The Last Jedi   
    
      production_budget domestic_gross worldwide_gross  
    0      $425,000,000   $760,507,625  $2,776,345,279  
    1      $410,600,000   $241,063,875  $1,045,663,875  
    2      $350,000,000    $42,762,350    $149,762,350  
    3      $330,600,000   $459,005,868  $1,403,013,963  
    4      $317,000,000   $620,181,382  $1,316,721,747  
    Index(['id', 'release_date', 'movie', 'production_budget', 'domestic_gross',
           'worldwide_gross'],
          dtype='object')



```python
#lets get to know the names of the coluns in our idmb dataset
db_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData\im.db"

# connect to the database
conn = sqlite3.connect(db_path)

# check available tables
tables = pd.read_sql("SELECT name FROM sqlite_master WHERE type='table';", conn)
print(tables)

conn
```

                name
    0   movie_basics
    1      directors
    2      known_for
    3     movie_akas
    4  movie_ratings
    5        persons
    6     principals
    7        writers





    <sqlite3.Connection at 0x1727f8da6b0>




```python
#What is the size of our dataset
movie_basics = pd.read_sql("SELECT * FROM movie_basics;", conn)
movie_ratings = pd.read_sql("SELECT * FROM movie_ratings;", conn)

print(movie_basics.shape)
print(movie_ratings.shape)
```

    (146144, 6)
    (73856, 3)



```python
#We will need to pull full data
imdb_full = (
    movie_basics
    .merge(movie_ratings, on="movie_id", how="inner")  # inner keeps only rated films
    .rename(columns={"primary_title": "title", "start_year": "year"})
)

print(imdb_full.shape)
print(imdb_full.head())

```

    (73856, 8)
        movie_id                            title              original_title  \
    0  tt0063540                        Sunghursh                   Sunghursh   
    1  tt0066787  One Day Before the Rainy Season             Ashad Ka Ek Din   
    2  tt0069049       The Other Side of the Wind  The Other Side of the Wind   
    3  tt0069204                  Sabse Bada Sukh             Sabse Bada Sukh   
    4  tt0100275         The Wandering Soap Opera       La Telenovela Errante   
    
       year  runtime_minutes                genres  averagerating  numvotes  
    0  2013            175.0    Action,Crime,Drama            7.0        77  
    1  2019            114.0       Biography,Drama            7.2        43  
    2  2018            122.0                 Drama            6.9      4517  
    3  2018              NaN          Comedy,Drama            6.1        13  
    4  2017             80.0  Comedy,Drama,Fantasy            6.5       119  


### THE MOVIE DB TMDB


```python
tmdb = pd.read_csv(f"{data_path}/tmdb.movies.csv.gz")
print(tmdb.shape)
print(tmdb.head())
print(tmdb.columns)
```

    (26517, 10)
       Unnamed: 0            genre_ids     id original_language  \
    0           0      [12, 14, 10751]  12444                en   
    1           1  [14, 12, 16, 10751]  10191                en   
    2           2        [12, 28, 878]  10138                en   
    3           3      [16, 35, 10751]    862                en   
    4           4        [28, 878, 12]  27205                en   
    
                                     original_title  popularity release_date  \
    0  Harry Potter and the Deathly Hallows: Part 1      33.533   2010-11-19   
    1                      How to Train Your Dragon      28.734   2010-03-26   
    2                                    Iron Man 2      28.515   2010-05-07   
    3                                     Toy Story      28.005   1995-11-22   
    4                                     Inception      27.920   2010-07-16   
    
                                              title  vote_average  vote_count  
    0  Harry Potter and the Deathly Hallows: Part 1           7.7       10788  
    1                      How to Train Your Dragon           7.7        7610  
    2                                    Iron Man 2           6.8       12368  
    3                                     Toy Story           7.9       10174  
    4                                     Inception           8.3       22186  
    Index(['Unnamed: 0', 'genre_ids', 'id', 'original_language', 'original_title',
           'popularity', 'release_date', 'title', 'vote_average', 'vote_count'],
          dtype='object')


### ROTTEN TOMATOES


```python
rt_info = pd.read_csv(f"{data_path}/rt.movie_info.tsv.gz", sep="\t")
print(rt_info.shape)
print(rt_info.head())
print(rt_info.columns)
```

    (1560, 12)
       id                                           synopsis rating  \
    0   1  This gritty, fast-paced, and innovative police...      R   
    1   3  New York City, not-too-distant-future: Eric Pa...      R   
    2   5  Illeana Douglas delivers a superb performance ...      R   
    3   6  Michael Douglas runs afoul of a treacherous su...      R   
    4   7                                                NaN     NR   
    
                                     genre          director  \
    0  Action and Adventure|Classics|Drama  William Friedkin   
    1    Drama|Science Fiction and Fantasy  David Cronenberg   
    2    Drama|Musical and Performing Arts    Allison Anders   
    3           Drama|Mystery and Suspense    Barry Levinson   
    4                        Drama|Romance    Rodney Bennett   
    
                                writer  theater_date      dvd_date currency  \
    0                   Ernest Tidyman   Oct 9, 1971  Sep 25, 2001      NaN   
    1     David Cronenberg|Don DeLillo  Aug 17, 2012   Jan 1, 2013        $   
    2                   Allison Anders  Sep 13, 1996  Apr 18, 2000      NaN   
    3  Paul Attanasio|Michael Crichton   Dec 9, 1994  Aug 27, 1997      NaN   
    4                     Giles Cooper           NaN           NaN      NaN   
    
      box_office      runtime             studio  
    0        NaN  104 minutes                NaN  
    1    600,000  108 minutes  Entertainment One  
    2        NaN  116 minutes                NaN  
    3        NaN  128 minutes                NaN  
    4        NaN  200 minutes                NaN  
    Index(['id', 'synopsis', 'rating', 'genre', 'director', 'writer',
           'theater_date', 'dvd_date', 'currency', 'box_office', 'runtime',
           'studio'],
          dtype='object')


### IMDB


```python
db_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData\im.db"

# connect to database
conn = sqlite3.connect(db_path)

# check available tables
tables = pd.read_sql("SELECT name FROM sqlite_master WHERE type='table';", conn)
print(tables)

conn
```

                name
    0   movie_basics
    1      directors
    2      known_for
    3     movie_akas
    4  movie_ratings
    5        persons
    6     principals
    7        writers





    <sqlite3.Connection at 0x1727fbac4f0>




```python
db_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData\im.db"

# Connect and load the two tables
conn = sqlite3.connect(db_path)

imdb_basics = pd.read_sql("SELECT * FROM movie_basics;", conn)
imdb_ratings = pd.read_sql("SELECT * FROM movie_ratings;", conn)

conn.close()

# Merge datasets
imdb_full = (
    movie_basics
    .merge(movie_ratings, on="movie_id", how="inner")  # inner keeps only rated films
    .rename(columns={"primary_title": "title", "start_year": "year"})
)

print(imdb_full.shape)
print(imdb_full.head())
```

    (73856, 8)
        movie_id                            title              original_title  \
    0  tt0063540                        Sunghursh                   Sunghursh   
    1  tt0066787  One Day Before the Rainy Season             Ashad Ka Ek Din   
    2  tt0069049       The Other Side of the Wind  The Other Side of the Wind   
    3  tt0069204                  Sabse Bada Sukh             Sabse Bada Sukh   
    4  tt0100275         The Wandering Soap Opera       La Telenovela Errante   
    
       year  runtime_minutes                genres  averagerating  numvotes  
    0  2013            175.0    Action,Crime,Drama            7.0        77  
    1  2019            114.0       Biography,Drama            7.2        43  
    2  2018            122.0                 Drama            6.9      4517  
    3  2018              NaN          Comedy,Drama            6.1        13  
    4  2017             80.0  Comedy,Drama,Fantasy            6.5       119  


## Data Selection

### The Numbers

The Number contains box office and budget info, but BOM has better international coverage.

BOM + IMDb + TMDB together already cover financial + popularity + critical aspects.

Using both TN and BOM would cause redundancy (they overlap heavily).

**Decision:** We drop TN in favor of BOM, which is more widely used in financial analysis.

### Rotten Tomatoes

RT scores are already correlated with IMDb ratings (both are critic/audience evaluations).

RT dataset is sparser and noisier in coverage, often missing smaller or older films.

We don’t want redundant metrics → IMDb ratings are more complete and easier to link.

**Decision:**  We drop Rotten Tomatoes because it adds little new information beyond IMDb.


**We keep IMDb, TMDB, BOM** Because

*IMDb - Huge coverage & trusted metadata. It gives us ratings or critical response, titles, years, runtimes → essential backbone.*

*TMDB - Modern coverage especially 2000s+, popularity measures, genres, keywords, production details - helps us see current audience preferences.*

*Box Office Mojo (BOM) -  Strong financial data (domestic plus international grosses, sometimes budget). This is directly tied to our core business question.*


```python
#What is the size of our dataset
movie_basics = pd.read_sql("SELECT * FROM movie_basics;", conn)
movie_ratings = pd.read_sql("SELECT * FROM movie_ratings;", conn)

print(movie_basics.shape)
print(movie_ratings.shape)

```

    (146144, 6)
    (73856, 3)


## Exploratory Data Analysis.

### Cleaning of Box Office Mojo



```python
data_path = r"C:\Users\G-Osundwa\Documents\phase2\g1project\dsc-phase-2-project-v3\zippedData"

bom = pd.read_csv(f"{data_path}\\bom.movie_gross.csv.gz")  
print(bom.shape)
print(bom.head())
print(bom.columns)
print(bom.info())
print(bom.isna().sum())
```

    (3387, 5)
                                             title studio  domestic_gross  \
    0                                  Toy Story 3     BV     415000000.0   
    1                   Alice in Wonderland (2010)     BV     334200000.0   
    2  Harry Potter and the Deathly Hallows Part 1     WB     296000000.0   
    3                                    Inception     WB     292600000.0   
    4                          Shrek Forever After   P/DW     238700000.0   
    
      foreign_gross  year  
    0     652000000  2010  
    1     691300000  2010  
    2     664300000  2010  
    3     535700000  2010  
    4     513900000  2010  
    Index(['title', 'studio', 'domestic_gross', 'foreign_gross', 'year'], dtype='object')
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3382 non-null   object 
     2   domestic_gross  3359 non-null   float64
     3   foreign_gross   2037 non-null   object 
     4   year            3387 non-null   int64  
    dtypes: float64(1), int64(1), object(3)
    memory usage: 132.4+ KB
    None
    title                0
    studio               5
    domestic_gross      28
    foreign_gross     1350
    year                 0
    dtype: int64


This dataset contains **3,387** movies with information on title, studio, domestic gross, foreign gross, and release year. It is mostly complete, with only a few missing values in the studio and domestic_gross columns, but a significant portion **40%** of the foreign_gross data is missing. Additionally, the foreign_gross column is stored as text and will need to be cleaned and converted to numeric for analysis. Overall, the dataset is rich enough for exploring box office performance across studios and years, though attention will be required to handle missing and inconsistent values.

### Cleaning of Box Office Mojo

**Normalizing whitespace & empty-strings**


```python
# convert empty strings to NaN for all object columns
for c in bom.select_dtypes(include="object").columns:
    bom[c] = bom[c].apply(lambda x: x.strip() if isinstance(x, str) else x)
    bom[c].replace({'': pd.NA, 'NA': pd.NA, 'N/A': pd.NA, 'nan': pd.NA}, inplace=True)

print("After normalizing blank strings:\n", bom.isnull().sum())
```

    After normalizing blank strings:
     title                0
    studio               5
    domestic_gross      28
    foreign_gross     1350
    year                 0
    dtype: int64


This means that blank -NA placeholders are successfully normalized into real NaN values

### Cleaning numeric money columns (foreign_gross, domestic_gross)


```python

def clean_money_column(series):
    return (
        series.astype(str)                      # ensure string
              .str.replace(r'[^0-9]', '', regex=True)  # keep only numbers
              .replace('', np.nan)              # convert empty strings to NaN
              .astype(float)                    # convert to float
    )

bom['domestic_gross'] = clean_money_column(bom['domestic_gross'])
bom['foreign_gross']  = clean_money_column(bom['foreign_gross'])

print(bom[['domestic_gross','foreign_gross']].dtypes)
print(bom[['domestic_gross','foreign_gross']].head())
print("Missing counts:\n", bom[['domestic_gross','foreign_gross']].isna().sum())
```

    domestic_gross    float64
    foreign_gross     float64
    dtype: object
       domestic_gross  foreign_gross
    0    4.150000e+09    652000000.0
    1    3.342000e+09    691300000.0
    2    2.960000e+09    664300000.0
    3    2.926000e+09    535700000.0
    4    2.387000e+09    513900000.0
    Missing counts:
     domestic_gross      28
    foreign_gross     1350
    dtype: int64


The cleaning worked well, both domestic_gross and foreign_gross are now proper float64 columns, and missing values are clearly counted 28 and 1350.

But notice something interesting:

Domestic gross values look too large.  For example Toy Story 3 shows 4.15e+09 (4.15 billion) instead of the correct 415,000,000.

That happened because the column already came in as numeric, and our cleaning function treated it as a string, stripped non-numeric characters, and dropped the decimal places (basically multiplying by 10).



```python
# Clean only foreign_gross (was object with $, commas)
bom['foreign_gross'] = (
    bom['foreign_gross']
        .astype(str)
        .str.replace(r'[^0-9]', '', regex=True)
        .replace('', np.nan)
        .astype(float)
)

# Domestic_gross was already numeric, just leave it as is
print(bom[['domestic_gross','foreign_gross']].head())
print("Missing counts:\n", bom[['domestic_gross','foreign_gross']].isna().sum())
print("\nSummary stats:\n", bom[['domestic_gross','foreign_gross']].describe())
```

       domestic_gross  foreign_gross
    0    4.150000e+09   6.520000e+09
    1    3.342000e+09   6.913000e+09
    2    2.960000e+09   6.643000e+09
    3    2.926000e+09   5.357000e+09
    4    2.387000e+09   5.139000e+09
    Missing counts:
     domestic_gross      28
    foreign_gross     1350
    dtype: int64
    
    Summary stats:
            domestic_gross  foreign_gross
    count    3.359000e+03   2.037000e+03
    mean     2.874585e+08   7.487284e+08
    std      6.698250e+08   1.374106e+09
    min      1.000000e+03   6.000000e+03
    25%      1.200000e+06   3.700000e+07
    50%      1.400000e+07   1.870000e+08
    75%      2.790000e+08   7.490000e+08
    max      9.367000e+09   9.605000e+09


Domestic gross: Ranges from very small releases **(1,000 dolars)** to blockbusters (9.36 billion dolars — which still looks suspiciously high, probably due to a data entry error; real-world highest domestic gross is under 1B dolars).

Foreign gross: Goes up to **9.6 billion dolars**, also likely due to mis-recorded values. Median **(187M dolars)** and quartiles look reasonable, but the maximums suggest outliers.

Missing data:

Domestic: only 28 missing which is  manageable.

Foreign: 1350 missing - about 40% missing, so we’ll need to decide whether to impute, drop, or analyze separately.


```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Domestic gross
axes[0].hist(bom['domestic_gross'].dropna(), bins=50, log=True)
axes[0].set_title("Domestic Gross (log scale)")
axes[0].set_xlabel("Gross ($)")
axes[0].set_ylabel("Frequency")
axes[0].set_xscale('log')

# Foreign gross
axes[1].hist(bom['foreign_gross'].dropna(), bins=50, log=True)
axes[1].set_title("Foreign Gross (log scale)")
axes[1].set_xlabel("Gross ($)")
axes[1].set_ylabel("Frequency")
axes[1].set_xscale('log')

plt.tight_layout()
plt.show()
```


    
![png](output_38_0.png)
    


### Domestic Gross


The vast majority of movies gross less than 100 million dolars domestically.

There's a steep drop-off as gross revenue increases.

A few movies fall in the $1B to 10B dolars range, which is unusual and likely includes outliers or data errors.

### Foreign Gross


Similar to the domestic plot, most movies gross less than $100 million.

There are also a few movies with gross revenues above 1B dolars, and some nearing $10B, which is extremely rare.


### Extreme Values


The highest-grossing movie of all time, Avatar (when adjusted for inflation or not), grossed around $2.9 billion globally.

A $9B+ gross for a single market (domestic or foreign) is almost certainly:

A data entry error (e.g., wrong units like cents/dollars confusion).

An aggregated value (e.g., across many re-releases or formats).

Or potentially an outlier due to a mislabeling of revenue sources.


### Investigating Outliers


```python
print("Top 10 domestic gross:")
print(bom[['title','domestic_gross']].sort_values(by='domestic_gross', ascending=False).head(10))

print("\nTop 10 foreign gross:")
print(bom[['title','foreign_gross']].sort_values(by='foreign_gross', ascending=False).head(10))
```

    Top 10 domestic gross:
                                 title  domestic_gross
    1872  Star Wars: The Force Awakens    9.367000e+09
    3080                 Black Panther    7.001000e+09
    3079        Avengers: Infinity War    6.788000e+09
    1873                Jurassic World    6.523000e+09
    727          Marvel's The Avengers    6.234000e+09
    2758      Star Wars: The Last Jedi    6.202000e+09
    3082                 Incredibles 2    6.086000e+09
    2323  Rogue One: A Star Wars Story    5.322000e+09
    2759   Beauty and the Beast (2017)    5.040000e+09
    2324                  Finding Dory    4.863000e+09
    
    Top 10 foreign gross:
                                                title  foreign_gross
    328   Harry Potter and the Deathly Hallows Part 2   9.605000e+09
    1875                      Avengers: Age of Ultron   9.464000e+09
    727                         Marvel's The Avengers   8.955000e+09
    3081               Jurassic World: Fallen Kingdom   8.918000e+09
    1127                                       Frozen   8.757000e+09
    2764                               Wolf Warrior 2   8.676000e+09
    1477              Transformers: Age of Extinction   8.586000e+09
    1876                                      Minions   8.234000e+09
    3083                                      Aquaman   8.127000e+09
    1128                                   Iron Man 3   8.058000e+09


This confirms that: the gross numbers are off by a factor of 10.

For example:

Star Wars: The Force Awakens domestic gross is shown as 9.3B, but the real number is about 936M.

Avengers: Endgame worldwide was equivalent to 2.8B, but here values are in the 8–9B range.

So the dataset is inflated by ×10 for many big titles.

**We apply a correction**

If a value is greater than 3 billion, divide it by 10.


```python
def fix_scale(series):
    return series.apply(lambda x: x/10 if pd.notna(x) and x > 3_000_000_000 else x)

bom['domestic_gross'] = fix_scale(bom['domestic_gross'])
bom['foreign_gross']  = fix_scale(bom['foreign_gross'])

# Check again
print("Top domestic gross after correction:")
print(bom[['title','domestic_gross']].sort_values(by='domestic_gross', ascending=False).head(10))

print("\nTop foreign gross after correction:")
print(bom[['title','foreign_gross']].sort_values(by='foreign_gross', ascending=False).head(10))
```

    Top domestic gross after correction:
                                                title  domestic_gross
    2     Harry Potter and the Deathly Hallows Part 1     296000000.0
    3                                       Inception     292600000.0
    732       The Twilight Saga: Breaking Dawn Part 2     292300000.0
    1135                                 Man of Steel     291000000.0
    1880        The Hunger Games: Mockingjay - Part 2     281700000.0
    331       The Twilight Saga: Breaking Dawn Part 1     281300000.0
    1134                                      Gravity     274100000.0
    3096                 Dr. Seuss' The Grinch (2018)     270600000.0
    2334                                         Sing     270400000.0
    1133                          Monsters University     268500000.0
    
    Top foreign gross after correction:
                                 title  foreign_gross
    343       The Adventures of Tintin    296400000.0
    2338                    La La Land    295000000.0
    741          Les Miserables (2012)    293000000.0
    8                    Despicable Me    291600000.0
    2351                        Dangal    290500000.0
    1495        Penguins of Madagascar    289700000.0
    2786                The Great Wall    289400000.0
    2342  Independence Day: Resurgence    286500000.0
    735               The Hunger Games    286400000.0
    1889  Kingsman: The Secret Service    286100000.0


The numbers look smaller than before, but they’re still too large to be realistic.

For example:

Inception domestic gross = 2.9B in our data.

Reality: about 292M USD.

That’s still inflated by ×10 again.

La La Land foreign gross = 2.95B in our data.

Reality: about 280M USD.

Again, about ×10 inflated.


```python
bom['domestic_gross'] = bom['domestic_gross'] / 10
bom['foreign_gross']  = bom['foreign_gross'] / 10

# Re-check the top grosses
print("Top 10 domestic gross (fully corrected):")
print(bom[['title','domestic_gross']].sort_values(by='domestic_gross', ascending=False).head(10))

print("\nTop 10 foreign gross (fully corrected):")
print(bom[['title','foreign_gross']].sort_values(by='foreign_gross', ascending=False).head(10))

```

    Top 10 domestic gross (fully corrected):
                                                title  domestic_gross
    2     Harry Potter and the Deathly Hallows Part 1     296000000.0
    3                                       Inception     292600000.0
    732       The Twilight Saga: Breaking Dawn Part 2     292300000.0
    1135                                 Man of Steel     291000000.0
    1880        The Hunger Games: Mockingjay - Part 2     281700000.0
    331       The Twilight Saga: Breaking Dawn Part 1     281300000.0
    1134                                      Gravity     274100000.0
    3096                 Dr. Seuss' The Grinch (2018)     270600000.0
    2334                                         Sing     270400000.0
    1133                          Monsters University     268500000.0
    
    Top 10 foreign gross (fully corrected):
                                 title  foreign_gross
    343       The Adventures of Tintin    296400000.0
    2338                    La La Land    295000000.0
    741          Les Miserables (2012)    293000000.0
    8                    Despicable Me    291600000.0
    2351                        Dangal    290500000.0
    1495        Penguins of Madagascar    289700000.0
    2786                The Great Wall    289400000.0
    2342  Independence Day: Resurgence    286500000.0
    735               The Hunger Games    286400000.0
    1889  Kingsman: The Secret Service    286100000.0


The grosses look realistic and line up with what we’d expect from actual box office numbers.

 Inception at 292M dolars domestic matches the real ~292M.


 La La Land at 295M dolars foreign matches the real ~280–300M.

 
 Big hits are in the hundreds of millions instead of billions.

Standardizing titles for later merging with IMDB/TMDB:


```python
bom['title'] = bom['title'].str.strip().str.lower()
```

**Performing Cleaning on IMDB**

**Checking for missing values**


```python
# Check total missing values per column
missing_data = imdb_full.isnull().sum().sort_values(ascending=False)
print(missing_data)

# Check percentage of missing values per column
missing_percent = (imdb_full.isnull().mean() * 100).sort_values(ascending=False)
print(missing_percent)
```

    runtime_minutes    7620
    genres              804
    movie_id              0
    title                 0
    original_title        0
    year                  0
    averagerating         0
    numvotes              0
    dtype: int64
    runtime_minutes    10.317374
    genres              1.088605
    movie_id            0.000000
    title               0.000000
    original_title      0.000000
    year                0.000000
    averagerating       0.000000
    numvotes            0.000000
    dtype: float64


Runtime_minutes - 7,620 missing values (10.3% of our dataset).

genres - 804 missing values (1.1%).

All other columns (movie_id, title, original_title, year, averagerating, numvotes) - no missing data.

Missing runtime_minutes (10%) is noticeable but not catastrophic.


```python
# Drop rows with missing year or runtime if needed
imdb_full = imdb_full.dropna(subset=["year", "runtime_minutes"])

# Ensure year is integer
imdb_full["year"] = imdb_full["year"].astype(int)

# Keep only plausible years
imdb_full = imdb_full[(imdb_full["year"] >= 1900) & (imdb_full["year"] <= 2023)]
```

**Join with Ratings so each movie has its average_rating and num_votes.**


```python
# 1. Merge basics + ratings into full IMDb dataset
imdb_full = imdb_basics.merge(imdb_ratings, on="movie_id", how="left")

# 2. Create cleaned title column in both datasets
imdb_full['title_clean'] = imdb_full['primary_title'].str.lower().str.strip()
bom['title_clean'] = bom['title'].str.lower().str.strip()

# 3. Check overlap of titles
exact_matches = set(imdb_full['title_clean']).intersection(set(bom['title_clean']))
print("Exact matches:", len(exact_matches))
print(list(exact_matches)[:20])

# 4. Merge IMDb + BOM using cleaned titles
merged = pd.merge(
    imdb_full,
    bom,
    on="title_clean",
    how="inner",
    suffixes=('_imdb', '_bom')
)

print(merged.info())
print(merged.head())
```

    Exact matches: 2701
    ['just a breath away', 'beastly', 'captain underpants: the first epic movie', 'lost in thailand', 'first man', 'rio, i love you', 'juan of the dead', 'judy moody and the not bummer summer', "god's not dead 2", 'the escape', 'thelma', 'love the coopers', 'krrish 3', 'the protector 2', "i'm still here", 'den of thieves', 'kill the messenger', 'marjorie prime', 'planes: fire & rescue', 'when the game stands tall']
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3487 entries, 0 to 3486
    Data columns (total 14 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   movie_id         3487 non-null   object 
     1   primary_title    3487 non-null   object 
     2   original_title   3487 non-null   object 
     3   start_year       3487 non-null   int64  
     4   runtime_minutes  3313 non-null   float64
     5   genres           3447 non-null   object 
     6   averagerating    3133 non-null   float64
     7   numvotes         3133 non-null   float64
     8   title_clean      3487 non-null   object 
     9   title            3487 non-null   object 
     10  studio           3484 non-null   object 
     11  domestic_gross   3462 non-null   float64
     12  foreign_gross    2106 non-null   float64
     13  year             3487 non-null   int64  
    dtypes: float64(5), int64(2), object(7)
    memory usage: 381.5+ KB
    None
        movie_id primary_title original_title  start_year  runtime_minutes  \
    0  tt0315642         Wazir          Wazir        2016            103.0   
    1  tt0337692   On the Road    On the Road        2012            124.0   
    2  tt2404548   On the Road    On the Road        2011             90.0   
    3  tt3872966   On the Road    On the Road        2013             87.0   
    4  tt4339118   On the Road    On the Road        2014             89.0   
    
                        genres  averagerating  numvotes  title_clean        title  \
    0       Action,Crime,Drama            7.1   15378.0        wazir        Wazir   
    1  Adventure,Drama,Romance            6.1   37886.0  on the road  On the Road   
    2                    Drama            NaN       NaN  on the road  On the Road   
    3              Documentary            NaN       NaN  on the road  On the Road   
    4                    Drama            6.0       6.0  on the road  On the Road   
    
        studio  domestic_gross  foreign_gross  year  
    0  Relbig.       1100000.0            NaN  2016  
    1      IFC        744000.0      8000000.0  2012  
    2      IFC        744000.0      8000000.0  2012  
    3      IFC        744000.0      8000000.0  2012  
    4      IFC        744000.0      8000000.0  2012  


This dataset shows the results of matching IMDb movie information with box office and studio data. Out of the merged records, there are 2,701 exact title matches, including films like Beastly, Captain Underpants: The First Epic Movie, and First Man. After merging, the final dataset contains 3,487 movies with details such as title, release year, runtime, genres, ratings, number of votes, studio, and domestic/foreign gross. Most fields are well populated, though some, like runtime_minutes, averagerating, and foreign_gross, have missing values. The preview also illustrates cases where multiple entries exist for the same title (On the Road appears in several years with different runtimes and genres), showing the complexity of handling remakes, re-releases, or duplicate entries when analyzing the data.

We fill the missing values with genre median


```python
imdb_full['runtime_minutes'] = imdb_full.groupby('genres')['runtime_minutes'].transform(
    lambda x: x.fillna(x.median())
)
```


```python
Verifying Cleaning
```


```python
print(imdb_full.isnull().sum())
```

    movie_id               0
    primary_title          0
    original_title        21
    start_year             0
    runtime_minutes       57
    genres                 0
    averagerating      72288
    numvotes           72288
    title_clean            0
    dtype: int64



```python
Our Data looks better, but lets do further cleaning
```


```python
imdb_full['original_title'] = imdb_full['original_title'].fillna(imdb_full['primary_title'])
```

For runtime_minutes       57


```python
imdb_full['runtime_minutes'] = imdb_full['runtime_minutes'].fillna(
    imdb_full['runtime_minutes'].median()
)
```


```python
print(imdb_full.isnull().sum())
```

    movie_id               0
    primary_title          0
    original_title         0
    start_year             0
    runtime_minutes        0
    genres                 0
    averagerating      72288
    numvotes           72288
    title_clean            0
    dtype: int64


Average rating - 72,288 missing

Numvotes - 72,288 missing

This is large (98% of our dataset). 

This likely comes from movies in movie_basics that don’t have ratings in movie_ratings

We drop them


```python
imdb_full = imdb_full.dropna(subset=['averagerating', 'numvotes'])

print(imdb_full.isnull().sum())
```

    movie_id           0
    primary_title      0
    original_title     0
    start_year         0
    runtime_minutes    0
    genres             0
    averagerating      0
    numvotes           0
    title_clean        0
    dtype: int64


Awsome

### Cleaning of The Movie Db


```python
print(tmdb.shape)
print(tmdb.columns)
print(tmdb.head())
print(tmdb.info())
print(tmdb.isna().sum())
```

    (26517, 10)
    Index(['Unnamed: 0', 'genre_ids', 'id', 'original_language', 'original_title',
           'popularity', 'release_date', 'title', 'vote_average', 'vote_count'],
          dtype='object')
       Unnamed: 0            genre_ids     id original_language  \
    0           0      [12, 14, 10751]  12444                en   
    1           1  [14, 12, 16, 10751]  10191                en   
    2           2        [12, 28, 878]  10138                en   
    3           3      [16, 35, 10751]    862                en   
    4           4        [28, 878, 12]  27205                en   
    
                                     original_title  popularity release_date  \
    0  Harry Potter and the Deathly Hallows: Part 1      33.533   2010-11-19   
    1                      How to Train Your Dragon      28.734   2010-03-26   
    2                                    Iron Man 2      28.515   2010-05-07   
    3                                     Toy Story      28.005   1995-11-22   
    4                                     Inception      27.920   2010-07-16   
    
                                              title  vote_average  vote_count  
    0  Harry Potter and the Deathly Hallows: Part 1           7.7       10788  
    1                      How to Train Your Dragon           7.7        7610  
    2                                    Iron Man 2           6.8       12368  
    3                                     Toy Story           7.9       10174  
    4                                     Inception           8.3       22186  
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 26517 entries, 0 to 26516
    Data columns (total 10 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   Unnamed: 0         26517 non-null  int64  
     1   genre_ids          26517 non-null  object 
     2   id                 26517 non-null  int64  
     3   original_language  26517 non-null  object 
     4   original_title     26517 non-null  object 
     5   popularity         26517 non-null  float64
     6   release_date       26517 non-null  object 
     7   title              26517 non-null  object 
     8   vote_average       26517 non-null  float64
     9   vote_count         26517 non-null  int64  
    dtypes: float64(2), int64(3), object(5)
    memory usage: 2.0+ MB
    None
    Unnamed: 0           0
    genre_ids            0
    id                   0
    original_language    0
    original_title       0
    popularity           0
    release_date         0
    title                0
    vote_average         0
    vote_count           0
    dtype: int64


The dataset contains 26,517 movie entries with 10 columns. All fields are complete with no missing values, which means the dataset is clean and ready for analysis. The available variables provide rich information, including movie identifiers (id), titles (title, original_title), genres (genre_ids), language, release date, popularity score, and user engagement metrics (vote_average, vote_count). Since there are no null values, no major cleaning is required, and analysis can focus on exploring trends in popularity, ratings, languages, or genre distributions.

## Data Merging

Before merging, we need titles and years in the same format across IMDB and BOM

**To Merge we first Create a title_clean column**


```python
import re

def clean_title(title):
    if pd.isna(title):
        return ""
    title = title.lower().strip()
    title = re.sub(r"\(.*\)", "", title)  # remove things in brackets e.g. (2010)
    title = re.sub(r"[^a-z0-9 ]", "", title)  # keep only alphanumeric + space
    return title.strip()

# Apply cleaning
imdb_full["title_clean"] = imdb_full["primary_title"].apply(clean_title)
bom["title_clean"] = bom["title"].apply(clean_title)

# Preview
print(imdb_full[["primary_title", "title_clean"]].head())
print(bom[["title", "title_clean"]].head())
```

                         primary_title                      title_clean
    0                        Sunghursh                        sunghursh
    1  One Day Before the Rainy Season  one day before the rainy season
    2       The Other Side of the Wind       the other side of the wind
    3                  Sabse Bada Sukh                  sabse bada sukh
    4         The Wandering Soap Opera         the wandering soap opera
                                             title  \
    0                                  toy story 3   
    1                   alice in wonderland (2010)   
    2  harry potter and the deathly hallows part 1   
    3                                    inception   
    4                          shrek forever after   
    
                                       title_clean  
    0                                  toy story 3  
    1                          alice in wonderland  
    2  harry potter and the deathly hallows part 1  
    3                                    inception  
    4                          shrek forever after  



```python
print(imdb_full.columns)
```

    Index(['movie_id', 'primary_title', 'original_title', 'start_year',
           'runtime_minutes', 'genres', 'averagerating', 'numvotes',
           'title_clean'],
          dtype='object')


**Our Dataset is now ready to merge (IMDB and BOM)**


```python
imdb_bom = pd.merge(
    imdb_full,
    bom,
    left_on=["title_clean", "start_year"],   # use start_year here
    right_on=["title_clean", "year"],        # bom still has year
    how="inner"
)

print(imdb_bom.shape)
imdb_bom.head()
```

    (2172, 14)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
      <th>title_clean</th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0315642</td>
      <td>Wazir</td>
      <td>Wazir</td>
      <td>2016</td>
      <td>103.0</td>
      <td>Action,Crime,Drama</td>
      <td>7.1</td>
      <td>15378.0</td>
      <td>wazir</td>
      <td>wazir</td>
      <td>Relbig.</td>
      <td>1100000.0</td>
      <td>NaN</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0337692</td>
      <td>On the Road</td>
      <td>On the Road</td>
      <td>2012</td>
      <td>124.0</td>
      <td>Adventure,Drama,Romance</td>
      <td>6.1</td>
      <td>37886.0</td>
      <td>on the road</td>
      <td>on the road</td>
      <td>IFC</td>
      <td>744000.0</td>
      <td>8000000.0</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0359950</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>2013</td>
      <td>114.0</td>
      <td>Adventure,Comedy,Drama</td>
      <td>7.3</td>
      <td>275300.0</td>
      <td>the secret life of walter mitty</td>
      <td>the secret life of walter mitty</td>
      <td>Fox</td>
      <td>58200000.0</td>
      <td>129900000.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0365907</td>
      <td>A Walk Among the Tombstones</td>
      <td>A Walk Among the Tombstones</td>
      <td>2014</td>
      <td>114.0</td>
      <td>Action,Crime,Drama</td>
      <td>6.5</td>
      <td>105116.0</td>
      <td>a walk among the tombstones</td>
      <td>a walk among the tombstones</td>
      <td>Uni.</td>
      <td>26300000.0</td>
      <td>26900000.0</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0369610</td>
      <td>Jurassic World</td>
      <td>Jurassic World</td>
      <td>2015</td>
      <td>124.0</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>7.0</td>
      <td>539338.0</td>
      <td>jurassic world</td>
      <td>jurassic world</td>
      <td>Uni.</td>
      <td>65230000.0</td>
      <td>10194.0</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
</div>



**Now to complet our merge, we merge TMDB, by first making a title_clean column like we did for IMDb & BOM, and extract the year from release_date.**


```python
# Clean title
tmdb["title_clean"] = tmdb["title"].apply(clean_title)

# Extract year from release_date
tmdb["year"] = pd.to_datetime(tmdb["release_date"], errors="coerce").dt.year

print(tmdb[["title", "title_clean", "year"]].head())
```

                                              title  \
    0  Harry Potter and the Deathly Hallows: Part 1   
    1                      How to Train Your Dragon   
    2                                    Iron Man 2   
    3                                     Toy Story   
    4                                     Inception   
    
                                       title_clean  year  
    0  harry potter and the deathly hallows part 1  2010  
    1                     how to train your dragon  2010  
    2                                   iron man 2  2010  
    3                                    toy story  1995  
    4                                    inception  2010  



```python
imdb_bom_tmdb = pd.merge(
    imdb_bom,
    tmdb,
    on=["title_clean", "year"],
    how="inner",
    suffixes=("_imdbbom", "_tmdb")
)

print(imdb_bom_tmdb.shape)
imdb_bom_tmdb.head()
```

    (2056, 24)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title_imdbbom</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
      <th>title_clean</th>
      <th>title_imdbbom</th>
      <th>...</th>
      <th>Unnamed: 0</th>
      <th>genre_ids</th>
      <th>id</th>
      <th>original_language</th>
      <th>original_title_tmdb</th>
      <th>popularity</th>
      <th>release_date</th>
      <th>title_tmdb</th>
      <th>vote_average</th>
      <th>vote_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0315642</td>
      <td>Wazir</td>
      <td>Wazir</td>
      <td>2016</td>
      <td>103.0</td>
      <td>Action,Crime,Drama</td>
      <td>7.1</td>
      <td>15378.0</td>
      <td>wazir</td>
      <td>wazir</td>
      <td>...</td>
      <td>18152</td>
      <td>[53, 28, 80, 18, 9648]</td>
      <td>275269</td>
      <td>hi</td>
      <td>वज़ीर</td>
      <td>3.881</td>
      <td>2016-01-08</td>
      <td>Wazir</td>
      <td>6.6</td>
      <td>63</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0337692</td>
      <td>On the Road</td>
      <td>On the Road</td>
      <td>2012</td>
      <td>124.0</td>
      <td>Adventure,Drama,Romance</td>
      <td>6.1</td>
      <td>37886.0</td>
      <td>on the road</td>
      <td>on the road</td>
      <td>...</td>
      <td>5350</td>
      <td>[12, 18]</td>
      <td>83770</td>
      <td>en</td>
      <td>On the Road</td>
      <td>8.919</td>
      <td>2012-12-21</td>
      <td>On the Road</td>
      <td>5.6</td>
      <td>518</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0359950</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>2013</td>
      <td>114.0</td>
      <td>Adventure,Comedy,Drama</td>
      <td>7.3</td>
      <td>275300.0</td>
      <td>the secret life of walter mitty</td>
      <td>the secret life of walter mitty</td>
      <td>...</td>
      <td>7998</td>
      <td>[12, 35, 18, 14]</td>
      <td>116745</td>
      <td>en</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>10.743</td>
      <td>2013-12-25</td>
      <td>The Secret Life of Walter Mitty</td>
      <td>7.1</td>
      <td>4859</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0365907</td>
      <td>A Walk Among the Tombstones</td>
      <td>A Walk Among the Tombstones</td>
      <td>2014</td>
      <td>114.0</td>
      <td>Action,Crime,Drama</td>
      <td>6.5</td>
      <td>105116.0</td>
      <td>a walk among the tombstones</td>
      <td>a walk among the tombstones</td>
      <td>...</td>
      <td>11053</td>
      <td>[80, 18, 9648, 53]</td>
      <td>169917</td>
      <td>en</td>
      <td>A Walk Among the Tombstones</td>
      <td>19.373</td>
      <td>2014-09-19</td>
      <td>A Walk Among the Tombstones</td>
      <td>6.3</td>
      <td>1685</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0369610</td>
      <td>Jurassic World</td>
      <td>Jurassic World</td>
      <td>2015</td>
      <td>124.0</td>
      <td>Action,Adventure,Sci-Fi</td>
      <td>7.0</td>
      <td>539338.0</td>
      <td>jurassic world</td>
      <td>jurassic world</td>
      <td>...</td>
      <td>14193</td>
      <td>[28, 12, 878, 53]</td>
      <td>135397</td>
      <td>en</td>
      <td>Jurassic World</td>
      <td>20.709</td>
      <td>2015-06-12</td>
      <td>Jurassic World</td>
      <td>6.6</td>
      <td>14056</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 24 columns</p>
</div>



## EDA ON OUR MERGERD DATASET

### **Step 1** Create worldwide_gross


```python
imdb_bom_tmdb["domestic_gross"] = pd.to_numeric(imdb_bom_tmdb["domestic_gross"], errors="coerce")
imdb_bom_tmdb["foreign_gross"] = pd.to_numeric(imdb_bom_tmdb["foreign_gross"], errors="coerce")

imdb_bom_tmdb["worldwide_gross"] = (
    imdb_bom_tmdb["domestic_gross"].fillna(0) + 
    imdb_bom_tmdb["foreign_gross"].fillna(0)
)
```

### **Step 2** Check Missing Values in Key Columns


```python
imdb_bom_tmdb[["domestic_gross", "foreign_gross", "worldwide_gross"]].isna().sum()
```




    domestic_gross       8
    foreign_gross      529
    worldwide_gross      0
    dtype: int64



Domestic_gross - only 8 missing values

Foreign_gross -530 missing values (not surprising, many films don’t report intl gross)

Worldwide_gross -no missing values (we filled NaNs with 0)

For our analysis:

We will keep all movies, but keep in mind that worldwide_gross for missing foreign data = domestic_gross only.

Later, when we compare international vs domestic performance, we will filter out movies with missing foreign_gross.

**Step 3**  Perform Basic Stats


```python
print("Worldwide gross (non-zero):", (imdb_bom_tmdb["worldwide_gross"] > 0).sum())
print("Mean worldwide gross:", imdb_bom_tmdb["worldwide_gross"].mean())
print("Median worldwide gross:", imdb_bom_tmdb["worldwide_gross"].median())
```

    Worldwide gross (non-zero): 2056
    Mean worldwide gross: 80122337.15175097
    Median worldwide gross: 37200000.0


Now we know the money landscape:

Out of our merged dataset, 2,046 movies actually made money.

Mean worldwide gross is equivalent to $123M - pulled up by massive blockbusters.

Median worldwide gross = $37.6M - shows the “typical” film earns far less than the mean.

### GENRE CLEANING

### **Step 1** Clean & Standardize Genres

