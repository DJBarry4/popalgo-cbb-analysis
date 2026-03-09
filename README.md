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

The analysis includes **regular season games from the 2022 through 2026 seasons**, focusing primarily on the months **December through February**.

Games outside this window introduce structural differences such as:

- neutral-court environments
- unusual matchup distributions
- higher betting volume and improved market efficiency during postseason play

For these reasons, the analysis focuses on **regular season games between December and February**.

---

## Data Sources

### Game Results and Statistics

Game results, schedules, scores, and team statistics were retrieved using the **ESPN API**.

### Historical Betting Odds

Historical sportsbook totals and spreads were retrieved using **The Odds API**, including both opening and closing lines.

All data collection and cleaning were performed entirely within **Python**.

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

- loading environment variables including the **Odds API key**
- defining global parameters such as:
  - season year
  - lookback window
  - backtest start and end dates
- enforcing strict backtesting rules to prevent future data leakage
- defining API endpoints for:
  - ESPN game data
  - Odds API betting markets
- creating project directories and cache folders
- implementing helper utilities including:
  - date formatting
  - logging
  - HTTP request retry logic

---

## Cell 2 — Master Game Data Ingestion

Cell 2 builds and maintains the **season-long master dataset of completed games**.

Key steps include:

- retrieving game schedules and results from the **ESPN API**
- converting each game into **two rows** (one per team)
- recording team IDs, opponent IDs, home/away status, and scores
- filtering out incomplete games
- removing duplicate rows
- saving the dataset as a **season-level parquet file**

This dataset becomes the foundation for **all historical feature calculations**.

---

## Cell 3 — Possessions and Pace Standardization

Cell 3 standardizes possession estimates for each team-game observation.

Tasks include:

- validating existing possession values
- calculating possessions from box score statistics
- applying proxy pace estimates when data is missing
- enforcing realistic pace bounds
- tagging each row with a **possession source classification**

Possible sources include:

- **given**
- **boxscore**
- **proxy**
- **unknown**

Audit summaries are generated to verify possession reliability.

---

## Cell 4 — Daily Slate Feature Builder

Cell 4 constructs the **daily slate dataset** used by the predictive model.

This stage builds **team performance snapshots** using only information available **before each game**.

Snapshots include metrics such as:

- pace
- offensive efficiency
- defensive efficiency
- recent performance windows

These snapshots represent the model's understanding of team strength entering the game.

---

## Cell 5 — Model Projection Builder (EVA Daily)

Cell 5 generates game-level projections used to evaluate sportsbook lines.

Key calculations include:

- blended team pace estimates
- matchup-specific pace adjustments
- offensive and defensive efficiency blending
- expected possessions
- projected scores
- projected totals
- projected spreads

The output is the **EVA Daily dataset** containing model projections.

---

## Cell 6 — Sportsbook Odds Collection

Cell 6 retrieves sportsbook betting lines using **The Odds API**.

The process includes:

- mapping ESPN team names to Odds API team names
- matching games using deterministic matchup keys
- retrieving historical betting lines

Two market prices are recorded:

**Opening Line**

Earliest available line approximately:
- 12 hours before tip
- 6 hours before tip
- 3 hours before tip

**Closing Line**

Defined as the line **five minutes before tip-off**.

Odds data is cached locally to reduce repeated API requests.

---

## Cell 7 — Final Dataset Construction

Cell 7 merges projections with sportsbook odds to produce the **final analytical dataset**.

Steps include:

1. ensuring projections exist
2. ensuring sportsbook odds exist
3. merging using **event_id**

Additional processing:

- attaching final game scores
- calculating actual totals
- tagging missing projections or odds
- enforcing a consistent schema

---

## Cell 8 — Results Construction

This stage produces the final analysis-ready dataset.

It calculates:

- projected totals and spreads
- model confidence scores
- betting direction
- line movement
- game results
- pace matchup groups
- conference tier groups

The dataset is exported as a CSV for visualization and analysis.

---

# Avoiding Data Leakage in Backtesting

Preventing **data leakage** is critical in predictive modeling.

Data leakage occurs when a model accidentally uses information that would **not have been available at the time predictions were made**, resulting in unrealistic performance.

To prevent this, the project enforces strict rules:

- all features are calculated using data available **on or before the AS_OF_DATE**
- no future games are included in model inputs
- projections are generated before results are known

This ensures model evaluation reflects **real-world predictive performance**.

---

## Model Evaluation

Model projections are compared against both **sportsbook lines and actual outcomes**.

Evaluation focuses on identifying situations where sportsbook lines may be **systematically mispriced**.

### Projection Error Analysis

Comparisons include:

- model total vs actual total
- model spread vs actual margin
- sportsbook total vs actual total
- sportsbook spread vs actual margin

Metrics used:

- Mean Absolute Error (MAE)
- Mean Squared Error (MSE)
- average bias

---

### Market Comparison

For each game, the **model edge** is calculated:

- model projected total vs sportsbook total
- model projected spread vs sportsbook spread

Large edges represent strong disagreement between the model and the market.

---

### Line Movement Analysis

The analysis also tracks how betting lines move between **open and close**.

Measurements include:

- percentage of games where the market moves toward the model
- percentage where it moves away from the model

---

### Subset Performance Analysis

Results are also analyzed across subsets including:

- pace-of-play matchups
- conference tier matchups
- confidence thresholds
- season segments

This helps determine whether inefficiencies are concentrated in specific environments.

---

### Visualization

Results are presented using visualizations including:

- model edge vs win percentage
- line movement relative to projections
- performance by pace matchup
- performance by conference group

---

### Interpretation

The final stage evaluates whether the model captures real patterns in the betting market.

Key questions include:

- does the model outperform sportsbook predictions?
- do larger edges correspond to better accuracy?
- are inefficiencies concentrated in certain game environments?
- does the market tend to move toward the model's projections?

---

## Tools Used

- Python  
- Pandas  
- NumPy  
- Matplotlib  
- Jupyter Notebook  

---

## Author

DJ Barry