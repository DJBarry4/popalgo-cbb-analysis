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

## Tools Used

- Python  
- Pandas  
- NumPy  
- Matplotlib  
- Jupyter Notebook  

---

## Author

DJ Barry
