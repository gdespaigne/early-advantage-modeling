# Predicting Early Match Success in League of Legends
**Project by:** Gabrielle Despaigne
DSC 259R Final Project

## Introduction

This project analyzes professional *League of Legends* match data from the 2022 competitive season to understand how early-game advantages influence match outcomes. Competitive League of Legends is often described as “snowbally,” meaning that early leads can cascade into decisive victories. However, not all early advantages are equal. This project is centered around one core question:

**Is early objective control or first blood a stronger indicator of match outcome in professional League of Legends matches?**

Understanding this relationship matters for several reasons. From a competitive standpoint, it informs strategic decision-making: should teams prioritize early neutral objectives such as dragons and heralds, or early combat advantages like first blood? From a data science perspective, this dataset provides a structured environment to evaluate how early signals translate into final outcomes, and whether magnitude-based metrics (such as gold difference) provide more predictive power than binary early events alone.


The dataset contains **24,830 team-level observations**, where each row represents one team in a single match.


### Relevant Columns

<div style="background-color:#f5f5f5; padding:15px; border-radius:8px;">

- **`result`** — Binary indicator of match outcome (1 = win, 0 = loss).  
- **`early_objective_control`** — Indicates whether a team secured at least one early neutral objective (first dragon, herald, or first tower).  
- **`firstblood`** — Indicates whether a team secured the first kill.  
- **`golddiffat10`** — Gold difference at 10 minutes.  
- **`killsat10`** — Kills secured at 10 minutes.  
- **`side`** — Blue or Red side.

</div>

<h2>Data Cleaning and Exploratory Data Analysis</h2>

<h2>Assessment of Missingness</h2>

<h2>Hypothesis Testing</h2>

<h2>Framing a Prediction Problem</h2>

<h2>Baseline Model</h2>

<h2>Final Model</h2>

<h2>Fairness Analysis</h2>
