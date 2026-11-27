# Creating Cohorts of Songs

Clustering 1,610 Rolling Stones tracks into sonic cohorts from Spotify audio features, to support music recommendation.

## The Problem

Streaming recommenders work better on how a song sounds than on its genre tag. Two tracks both tagged "rock" can differ completely in energy, mood, tempo and production, and a recommender built on genre alone suggests songs the listener skips.

This project groups songs into cohorts using Spotify's audio features, defines each cohort by the features that characterise it, and examines what relates to song popularity.

## Dataset

Spotify API audio features for the Rolling Stones catalogue: **1,610 tracks across 90 releases, from 1964 to 2022.**

Ten numeric audio features per track:

- Acousticness
- Danceability
- Energy
- Instrumentalness
- Liveness
- Loudness
- Speechiness
- Tempo
- Valence
- Duration

Plus a popularity score and descriptive metadata.

## Method

Inspect, clean, explore, standardise, reduce with PCA, cluster with KMeans, then validate the results against variables that were never used as clustering inputs.

## Key Findings

### The data needed almost no cleaning

No missing values, no duplicated track IDs, and the audio features were already correctly scaled.

An IQR check across every numeric feature flagged values on most features, with instrumentalness highest at 272 songs (16.9%). None were removed: the flagged songs are quiet acoustic tracks, live jams and instrumentals rather than errors.

The one real question was 656 repeated song names. Investigation showed they were separate live performances and remasters averaging 2.77 albums each, with Brown Sugar - Live appearing on 15 different concert albums. Removing them would have eliminated about 60% of the dataset.

### No audio feature explains popularity

The strongest correlation with popularity is liveness at −0.21, while every other feature lies within ±0.16.

No audio feature shows more than a weak relationship with popularity.

### Popularity does not change over time

The fitted trend is −0.08 popularity points per year, small relative to the year-to-year variation.

The larger movements occur in years containing substantial reissues rather than new releases.

### Five cohorts emerged

| Cohort | Songs | Energy | Liveness | Acousticness | Instrumentalness | Mean Popularity | % Popular |
|---|---|---|---|---|---|---|---|
| Loud High-Energy Rock | 431 | 0.93 | 0.87 | 0.20 | 0.12 | 17.67 | 0.0% |
| Live Recordings | 411 | 0.88 | 0.45 | 0.15 | 0.06 | 22.82 | 3.9% |
| Danceable Upbeat Singles | 332 | 0.64 | 0.20 | 0.24 | 0.07 | 19.70 | 3.0% |
| Acoustic & Melancholic | 256 | 0.57 | 0.35 | 0.51 | 0.09 | 23.61 | 5.5% |
| Instrumental Tracks | 180 | 0.85 | 0.44 | 0.26 | 0.77 | 21.59 | 1.7% |

### Headline Result

The largest cohort, Loud High-Energy Rock, contains 431 songs yet not a single track above the popularity threshold.

In contrast, the much smaller Acoustic & Melancholic cohort has the highest share of popular tracks at 5.5%.

The band's signature sound is not what listeners stream most frequently.

### The cohorts are not era-driven

Median release years range from 1967 to 2016 across the five cohorts.

The groups capture musical similarity rather than chronology. A song released in 1969 and another released in 2016 can appear in the same cohort because they sound alike.

### Album Recommendation

Sticky Fingers (Remastered) leads every measure, with 60% of its tracks above the popularity threshold.

Some Girls ranks second on both average popularity and share of popular tracks.

## Methodological Decisions

- **Standardisation was essential.** Tempo's standard deviation is 29.23 while speechiness is 0.052, a spread of roughly 560 times. Without scaling, tempo would dominate the distance calculations.
- **Popularity and release year were excluded from clustering and used only for validation.** This prevents circular conclusions and allows era effects to emerge as a finding.
- **The popularity threshold of 50 was derived from the data.** Only 43 of 1,610 songs reach that level, isolating roughly the top 3% of the catalogue.
- **k = 5 was chosen instead of the silhouette maximum at k = 2.** Two clusters would be too broad to support meaningful recommendations.
- **Silhouette = 0.146 is reported transparently.** The cohorts are distinguishable but overlapping, which is expected for continuous audio features.

## Repository Contents

```
.
├── Creating_Cohorts_of_Songs.ipynb   # complete notebook
├── rolling_stones_spotify.csv        # dataset
└── README.md                         # project summary
```

## How to Run

**Option 1 — Google Colab (no setup):** open the notebook in Colab and upload `rolling_stones_spotify.csv` to the session.

**Option 2 — locally:**

```bash
pip install pandas numpy scikit-learn matplotlib seaborn
jupyter notebook Creating_Cohorts_of_Songs.ipynb
```

## Tools

Python, pandas, NumPy, scikit-learn (StandardScaler, PCA, KMeans, silhouette_score), matplotlib, seaborn.

## Limitations

Single artist, so the cohorts describe this catalogue rather than music generally. The silhouette of 0.146 indicates overlapping cohorts, so songs near a boundary could reasonably belong to a neighbouring group. Popularity is a point-in-time measure and would need periodic refresh in a live system.

## Possible Extensions

A Gaussian mixture model would allow songs to belong to more than one cohort with a probability, which suits continuously varying audio features better than hard assignment. Running the same method on a second artist would test whether these five groups generalise.

