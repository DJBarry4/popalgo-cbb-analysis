# Popalgo: College Basketball Market Inefficiency Analysis

## Project Overview

This project analyzes whether inefficiencies exist in the betting markets for **Division I college basketball** and whether a predictive model can be built to capitalize on those inefficiencies.

The analysis focuses on both **totals** and **spreads**, with particular attention paid to:

- different **pace-of-play combinations**
- differences between **higher-tier and lower-tier matchups**
- situations where model projections differ from sportsbook lines
- how betting market movement behaves relative to the model

The broader goal is to determine whether certain game environments create repeatable edges in the college basketball betting market.

---

## Research Questions

This project is built around the following research questions:

1. Are there measurable inefficiencies in the **Division I college basketball totals market**?
2. Are there measurable inefficiencies in the **Division I college basketball spreads market**?
3. Can a predictive model identify games where sportsbook lines may be mispriced?
4. Are inefficiencies stronger in specific subsets of games such as:
   - pace matchup combinations
   - higher-tier vs lower-tier matchups
   - different points in the season

---

## Study Period

The analysis includes regular season college basketball games from the **2022 through 2026 seasons**.

Postseason tournaments were intentionally excluded from the dataset:

- NCAA Tournament (March Madness)
- NIT
- CBI

These tournaments introduce structural differences such as neutral-court environments, atypical matchup distributions, and higher market efficiency due to increased betting volume and public attention.

For these reasons, the analysis focuses strictly on **regular season games**.

---

## Data Sources

### Game Results and Team Statistics

Game results, schedules, scores, and team statistics were retrieved using the **ESPN API**.

### Historical Betting Odds

Historical sportsbook totals and spreads were retrieved using **The Odds API**, including opening and closing lines.

All data collection, cleaning, and transformation were performed within **Python**.

---

## Model Workflow

The predictive framework follows a multi-step process designed to estimate expected game totals and spreads using pace-based modeling.

### 1. Data Collection

Game data is retrieved from the ESPN API and sportsbook odds are retrieved from The Odds API.

### 2. Data Cleaning

Datasets are merged and standardized using Python libraries such as **Pandas**.

### 3. Feature Engineering

Key model inputs include:

- expected possessions (pace of play)
- offensive efficiency
- defensive efficiency
- conference group classifications
- pace matchup combinations

### 4. Model Projection

The model generates projections for:

- expected total points
- expected point spreads

These projections are compared against sportsbook lines.

### 5. Evaluation

Model performance is evaluated using:

- win percentage
- net betting units
- line movement toward or away from the model
- subset analysis by pace matchup and conference tier

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