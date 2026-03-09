# Popalgo: College Basketball Market Inefficiency Analysis

## Project Overview

This project analyzes whether inefficiencies exist in the betting markets for **Division I college basketball** and whether a predictive model can be built to capitalize on those inefficiencies.

The analysis focuses on both **totals** and **spreads**, with particular attention paid to:

- different pace-of-play combinations
- differences between higher-tier and lower-tier matchups
- situations where model projections differ from sportsbook lines
- betting market movement relative to the model

The goal is to determine whether certain game environments create repeatable edges in the college basketball betting market.

---

## Research Questions

This project investigates the following questions:

1. Are there inefficiencies in the **college basketball totals market**?
2. Are there inefficiencies in the **college basketball spreads market**?
3. Can a predictive model identify games where sportsbook lines may be mispriced?
4. Are inefficiencies stronger in certain subsets of games such as pace matchups or tiered conference matchups?

---

## Study Period

The analysis includes **regular season games from the 2022 through 2026 seasons** usign the months DECEMBER through FEBRUARY only.

Games outside these months introduce structural differences such as neutral-court environments, unusual matchup distributions, and higher market efficiency due to increased betting volume.

For these reasons the analysis focuses strictly on **regular season games** between DEC and FEB (Maybe a little into March before the tournaments).

---

## Data Sources

### Game Results and Statistics

Game results, schedules, scores, and team statistics were retrieved using the **ESPN API**.

### Historical Betting Odds

Historical sportsbook totals and spreads were retrieved using **The Odds API**, including opening and closing lines.

All data collection and cleaning was performed entirely within **Python**.

---

## Model Workflow

The predictive model follows a structured workflow:

### 1. Data Collection

Game data is collected from the ESPN API and sportsbook odds are collected from The Odds API.

### 2. Data Cleaning

Datasets are merged and standardized using **Python and Pandas**.

### 3. Feature Engineering

Key model inputs include:

- expected possessions (pace)
- offensive efficiency
- defensive efficiency
- conference tier groupings
- pace matchup combinations

### 4. Model Projection

The model estimates:

- expected total points
- expected point spreads

These projections are compared against sportsbook lines.

### 5. Evaluation

Model performance is evaluated using:

- win percentage
- net betting units
- line movement toward or away from the model
- subset analysis across matchup types

---

# Data Pipeline

This project uses a structured **seven-stage data pipeline** to collect, clean, transform, and combine game data with sportsbook odds before analysis.

Each stage is implemented as a separate cell within the Jupyter Notebook to maintain clarity and reproducibility.

All data cleaning and transformation is performed **entirely in Python**, satisfying the project requirement that external tools such as Excel are not used.

---

# Pipeline Overview

## Cell 1 — Environment and Project Setup

Cell 1 initializes the environment and establishes the core configuration used throughout the pipeline.

Key responsibilities include:

- Loading environment variables including the **Odds API key**
- Defining global parameters such as:
  - season year
  - lookback window
  - backtest start and end dates
- Setting **strict backtesting rules** that prevent future data from leaking into historical calculations
- Defining API endpoints for:
  - ESPN game data
  - Odds API betting markets
- Creating project directories and cache folders
- Implementing reusable helper utilities including:
  - date formatting
  - logging
  - HTTP request retry logic

This cell ensures the entire project runs under a **consistent configuration and data environment**.

---

## Cell 2 — Master Game Data Ingestion

Cell 2 builds and maintains the **season-long master dataset of completed games**.

This dataset serves as the **primary source of game outcomes and statistics** for the entire model.

Key steps include:

- Pulling game schedules and results from the **ESPN API**
- Converting each game into **two rows**, one for each team
- Recording:
  - team IDs
  - opponent IDs
  - home/away status
  - final scores
- Filtering out games that are not completed
- Removing duplicate rows
- Saving the dataset as a **season-level parquet file**

The master dataset becomes the foundation for **all historical feature calculations and backtesting**.

---

## Cell 3 — Possessions and Pace Standardization

Cell 3 standardizes possession estimates for each team-game observation.

Possessions are a critical component of the model because **pace strongly influences scoring totals**.

This cell performs several tasks:

- Validates existing possession values when available
- Calculates possessions from box score statistics when possible
- Applies a **proxy pace estimate** when possession data is missing
- Applies reasonable bounds to eliminate unrealistic pace values
- Tags each row with a **possession source classification**

Possible sources include:

- **given** – possessions already available in the data
- **boxscore** – possessions calculated from game statistics
- **proxy** – possessions estimated from league pace
- **unknown** – possession data could not be determined

The cell also generates **audit summaries** to verify the reliability of possession calculations.

---

## Cell 4 — Daily Slate Feature Builder

Cell 4 constructs the **daily game slate dataset** used by the predictive model.

The goal of this stage is to create **team performance snapshots** that represent the information available **prior to each game being played**.

Key steps include:

- Ensuring the master dataset includes all games through the **AS_OF_DATE**
- Pulling the game schedule from the ESPN scoreboard API
- Identifying all teams participating on that slate
- Filtering the master dataset so only historical data is used
- Building **team performance snapshots** for each team

The snapshots include metrics such as:

- pace
- offensive efficiency
- defensive efficiency
- recent performance windows

These snapshots represent the **model’s understanding of team strength entering the game**.

---

## Cell 5 — Model Projection Builder (EVA Daily)

Cell 5 generates the **game-level projections** used to evaluate sportsbook lines.

This stage merges team snapshots with the slate schedule and calculates expected game outcomes.

Key calculations include:

- blended team pace estimates
- matchup-specific pace adjustments
- offensive and defensive efficiency blending
- expected possessions
- projected home and away scores
- projected game totals
- projected point spreads

The output of this stage is the **EVA Daily dataset**, which contains model projections for every game.

These projections are later compared to sportsbook lines to identify potential market inefficiencies.

---

## Cell 6 — Sportsbook Odds Collection

Cell 6 retrieves historical sportsbook lines for each game.

Odds data is collected using **The Odds API** and focuses on the **DraftKings sportsbook**.

The process includes:

- Mapping ESPN team names to Odds API team names
- Matching games using a deterministic matchup key
- Retrieving historical betting lines at specific time checkpoints

Two key market prices are collected:

### Opening Line

Defined as the earliest available line at:

- 12 hours before tip
- 6 hours before tip
- 3 hours before tip

### Closing Line

Defined as the line **five minutes before tip-off**.

If no earlier opening line is available, the model uses the closing line as a fallback opening line.

All retrieved odds data is cached locally to reduce repeated API requests.

---

## Cell 7 — Final Dataset Construction

Cell 7 merges model projections with sportsbook odds to produce the **final analytical dataset** used in the study.

For each slate date the process:

1. Ensures projection data exists (Cells 4–5)
2. Ensures sportsbook odds exist (Cell 6)
3. Merges the datasets using **event_id as the primary key**

Additional steps include:

- Attaching actual game results from the master dataset
- Calculating actual game totals
- Tagging games with missing projections or missing odds
- Enforcing a consistent dataset schema

The final dataset contains:

- game identifiers
- model projections
- sportsbook opening lines
- sportsbook closing lines
- actual game outcomes

This dataset is used to evaluate **betting performance and market inefficiencies**.

---

# Avoiding Data Leakage in Backtesting

A critical aspect of predictive modeling is avoiding **data leakage**.

Data leakage occurs when a model accidentally uses information that would **not have been available at the time a prediction was made**.

If leakage occurs, model performance will appear artificially strong and the results will not represent real-world performance.

To prevent this problem, this project enforces several strict backtesting rules:

### 1. Historical Cutoff Dates

All model features are calculated using data only from games that occurred **on or before the AS_OF_DATE**.

## Model Evaluation

After projections are generated and sportsbook lines are collected, the model is evaluated by comparing its predictions to both **market prices and actual game outcomes**.

The goal of the evaluation stage is not simply to simulate betting results, but to determine whether the model consistently identifies situations where sportsbook lines may be **systematically mispriced**.

---

### Projection Error Analysis

The first stage of evaluation measures how accurately the model predicts game outcomes.

For each game the following comparisons are calculated:

- **Model Total vs Actual Total**
- **Model Spread vs Actual Margin of Victory**
- **Sportsbook Total vs Actual Total**
- **Sportsbook Spread vs Actual Margin**

These comparisons allow us to determine whether the model produces more accurate estimates than the sportsbook market.

Common metrics used include:

- Mean Absolute Error (MAE)
- Mean Squared Error (MSE)
- Average bias in projections

These metrics provide a baseline measure of **predictive accuracy**.

---

### Market Comparison

The model is also evaluated relative to sportsbook lines.

For each game we compute the difference between:

- **Model projected total and sportsbook total**
- **Model projected spread and sportsbook spread**

This difference is referred to as the **model edge**.

Large model edges indicate games where the model and sportsbook disagree strongly.

The analysis investigates whether these disagreements occur randomly or if they represent **consistent market inefficiencies**.

---

### Line Movement Analysis

Another important evaluation method is analyzing **line movement**.

Sportsbook lines often move between the opening price and the closing price as money enters the market.

This project measures:

- the direction of line movement relative to the model
- the percentage of games where the market moves **toward** the model projection
- the percentage where the market moves **away** from the model projection

If sportsbook lines frequently move toward the model's projection, this suggests that the model may be identifying information that the market later incorporates.

---

### Subset Performance Analysis

The model is also evaluated across specific subsets of games to determine whether inefficiencies are concentrated in particular environments.

These subsets include:

- **Pace-of-play matchups** (fast vs fast, slow vs slow, mixed pace)
- **Conference tier matchups** (power conferences vs mid-majors)
- **Confidence score thresholds**
- **Season segments**

By breaking the results into subsets, we can determine whether certain types of games produce stronger signals than others.

---

### Performance Metrics

To summarize model performance, the following metrics are used:

- Win percentage when the model identifies a significant edge
- Net unit performance based on sportsbook closing prices
- Sample size of qualifying games
- Direction of line movement relative to projections

These metrics help determine whether the model's signals are **statistically meaningful or simply random variation**.

---

### Visualization

The results of the evaluation are presented through several visualizations.

These visualizations include:

- model edge vs win percentage
- line movement toward or away from the model
- performance by pace matchup type
- performance by conference group matchup

These plots allow patterns to be identified visually and help determine whether inefficiencies appear consistently across the dataset.

---

### Interpretation

The final stage of evaluation focuses on interpreting the results of the model.

Key questions include:

- Does the model outperform sportsbook lines in predicting outcomes?
- Do larger model edges correspond to higher prediction accuracy?
- Are inefficiencies concentrated in specific game environments?
- Does the market tend to move toward the model’s projections?

Answering these questions helps determine whether the model captures **real structural patterns in the college basketball betting market**.


## Tools Used

- Python  
- Pandas  
- NumPy  
- Matplotlib  
- Jupyter Notebook  

---

## Author

DJ Barry
