# Predicting Early Match Success in League of Legends
**Project by:** Gabrielle Despaigne
DSC 259R Final Project

## Introduction

This project analyzes professional *League of Legends* match data from the 2022 competitive season to understand how early-game advantages influence match outcomes. Competitive League of Legends is often described as “snowbally,” meaning that early leads can cascade into decisive victories. However, not all early advantages are equal. This project is centered around one core question:

**Is early objective control or first blood a stronger indicator of match outcome in professional League of Legends matches?**

Understanding this relationship matters for several reasons. From a competitive standpoint, it informs strategic decision-making: should teams prioritize early neutral objectives such as dragons and heralds, or early combat advantages like first blood? From a data science perspective, this dataset provides a structured environment to evaluate how early signals translate into final outcomes, and whether magnitude-based metrics (such as gold difference) provide more predictive power than binary early events alone.


The dataset contains **24,830 team-level observations**, where each row represents one team in a single match.

### Relevant Columns

> - **`result`** — Binary indicator of match outcome (1 = win, 0 = loss).  
> - **`early_objective_control`** — Indicates whether a team secured at least one early neutral objective (first dragon, herald, or first tower).  
> - **`firstblood`** — Indicates whether a team secured the first kill.  
> - **`golddiffat10`** — Gold difference at 10 minutes.  
> - **`killsat10`** — Kills secured at 10 minutes.  
> - **`side`** — Blue or Red side.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The original League of Legends dataset contains match statistics recorded across professional competitions. Each match contains multiple rows corresponding to different entities, including both teams and individual players. Since the goal of this project is to analyze how early-game events affect **match outcomes at the team level**, the dataset was filtered so that each row represents **one team in a single match**.

First, the dataset was filtered to retain only **team-level rows**, removing player-level observations that were not relevant to predicting match outcomes. This ensures that each row corresponds to a team’s performance within a single match.

Next, the response variable **`result`** was converted into a binary classification variable where:

- `1` represents a win  
- `0` represents a loss  

This conversion allows the dataset to be used directly for classification modeling and hypothesis testing.

An engineered feature called **`early_objective_control`** was created to capture whether a team secured at least one early neutral objective. This variable combines early objective events (such as first dragon, herald, or tower) into a single binary indicator. This feature was created to represent early **macro-level advantages** rather than isolated events.

Some columns contained missing values, particularly **`golddiffat10`** and **`killsat10`**, which represent early-game economic and combat advantages. These missing values were handled using **median imputation**, which preserves the overall distribution of the data while allowing models that cannot handle missing values to train properly.

Additionally, certain columns related to game mechanics that were introduced in later patches (such as **void grubs**) were excluded from analysis. Their missingness was tied to the data generating process rather than random absence, since these objectives were not part of the game during the 2022 season.

After cleaning and feature engineering, the dataset contains **24,830 rows**, where each row represents a team’s perspective in a single professional match.

---

### Head of the Cleaned Dataset

The following table shows the first few rows of the cleaned dataset used in the analysis.

<details>
<summary>Click to expand the dataset preview</summary>

|   result | side   |   firstblood |   firstdragon |   firstherald |   firsttower |   void_grubs |   early_objective_control |   golddiffat10 |   killsat10 |
|---------:|:-------|-------------:|--------------:|--------------:|-------------:|-------------:|--------------------------:|---------------:|------------:|
|        0 | Blue   |            1 |             0 |             1 |            1 |          nan |                         1 |           1523 |           3 |
|        1 | Red    |            0 |             1 |             0 |            0 |          nan |                         1 |          -1523 |           0 |
|        0 | Blue   |            0 |             0 |             1 |            0 |          nan |                         1 |          -1619 |           1 |
|        1 | Red    |            1 |             1 |             0 |            1 |          nan |                         1 |           1619 |           3 |
|        1 | Blue   |            0 |             0 |             0 |            1 |            0 |                         1 |            nan |         nan |
|        0 | Red    |            1 |             1 |             0 |            0 |            0 |                         1 |            nan |         nan |
|        1 | Blue   |            0 |             1 |             0 |            1 |          nan |                         1 |           -103 |           0 |
|        0 | Red    |            1 |             0 |             1 |            0 |          nan |                         1 |            103 |           1 |
|        1 | Blue   |            1 |             0 |             0 |            1 |            0 |                         1 |            nan |         nan |
|        0 | Red    |            0 |             1 |             0 |            0 |            0 |                         1 |            nan |         nan |

</details>

---

## Univariate Analysis
<div style="display:flex; justify-content:center;">
<iframe
  src="assets/win_distribution.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>
</div>
This visualization shows the distribution of match outcomes across the dataset. Since each match contributes one win and one loss at the team level, the distribution is balanced between the two classes. This balance is beneficial for predictive modeling because it prevents classification bias toward one outcome.

---

## Bivariate Analysis
<div style="display:flex; justify-content:center;">
<iframe
  src="assets/earlyadvcompar.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>
</div>

This plot shows the relationship between **early objective control** and match outcomes. Teams that secure early objectives display a noticeably higher win rate compared to teams that do not. This suggests that early macro advantages may play a meaningful role in determining match outcomes.

---

## Grouped Analysis

The following grouped table summarizes win rates based on early objective control.

<details>
<summary>Click to expand the dataset preview</summary>

|   patch |        0 |        1 |
|--------:|---------:|---------:|
|   12.01 | 0.236559 | 0.558566 |
|   12.02 | 0.363328 | 0.559322 |
|   12.03 | 0.246429 | 0.550141 |
|   12.04 | 0.40249  | 0.556901 |
|   12.05 | 0.310606 | 0.551921 |
|   12.06 | 0.288136 | 0.540193 |
|   12.07 | 0.206897 | 0.562963 |
|   12.08 | 0.174603 | 0.568562 |
|   12.09 | 0.210526 | 0.556507 |
|   12.1  | 0.243697 | 0.554856 |
|   12.11 | 0.327652 | 0.567407 |
|   12.12 | 0.346041 | 0.5525   |
|   12.13 | 0.380303 | 0.559488 |
|   12.14 | 0.307947 | 0.565463 |
|   12.15 | 0.195122 | 0.566138 |
|   12.16 | 0.361789 | 0.57173  |
|   12.17 | 0.166667 | 0.547619 |
|   12.18 | 0.190751 | 0.55991  |
|   12.19 | 0.185714 | 0.567901 |
|   12.2  | 0.290323 | 0.542763 |
|   12.21 | 0.225806 | 0.550296 |
|   12.23 | 0.5      | 0.5      |

</details>

This grouped summary reinforces the trend observed in the visualization. Teams that secure early objectives win a substantially larger proportion of matches compared to teams that do not, suggesting that early objective control may be an important indicator of overall match success.


## Missingness Analysis

### Potential NMAR Column

One column in the dataset that exhibits non-trivial missingness is **`void_grubs`**.  
I believe the missingness in this column is **NMAR (Not Missing At Random)**.

The missing values occur because **void grubs were introduced in a later League of Legends patch**, and therefore do not appear in matches played earlier in the 2022 season. This means that the missingness is not random, but instead tied directly to the **data generating process**, specifically the patch version of the game at the time each match was played.

Because the presence of void grubs depends on whether the game was played after the patch that introduced them, the missingness is determined by an external factor (game version). If additional data such as **patch version or match date** were explicitly used to model this missingness, the column could potentially be treated as **Missing At Random (MAR)** instead of NMAR, since the missingness would then be explainable by an observed variable.

For the purposes of this project, the `void_grubs` column was excluded from modeling because its missingness reflects a structural change in the game rather than meaningful gameplay information during the 2022 season.

---

### Missingness Dependency Testing

To further investigate missingness patterns, permutation tests were conducted to determine whether the missingness of `void_grubs` depends on other variables in the dataset.

Two permutation tests were performed:

- One test examined whether missingness in `void_grubs` depends on the **side** variable (blue side vs red side).
- Another test examined whether missingness depends on **patch version**.

The results showed that missingness **does depend on patch version**, which supports the earlier reasoning that missing values occur because the game mechanic did not exist in earlier patches. However, missingness **does not depend on side**, indicating that the presence or absence of void grubs is unrelated to team position in the match.

These results reinforce the conclusion that the missingness is tied to the **game version rather than gameplay characteristics**.

---

### Visualization of Missingness

<div style="display:flex; justify-content:center;">
<iframe
  src="assets/missingness_permutation.html"
  width="1000"
  height="600"
  frameborder="0"
></iframe>
</div>

The permutation test examines whether the missingness of void_grubs depends on team side (Blue vs Red). Since the observed difference falls well within the permutation distribution, there is no evidence that missingness is related to team side. This supports the conclusion that missing values are instead caused by structural changes in the game, specifically the patch that introduced void grubs.

<h2>Hypothesis Testing</h2>

<h2>Framing a Prediction Problem</h2>

<h2>Baseline Model</h2>

<h2>Final Model</h2>

<h2>Fairness Analysis</h2>
