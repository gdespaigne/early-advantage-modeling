# Predicting Early Match Success in League of Legends
**Project by:** Gabrielle Despaigne |
**DSC 259R** Final Project

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

---

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

<div style="display:flex; justify-content:center;">

|  result | side   |   firstblood |   firstdragon |   firstherald |   firsttower |   void_grubs |   early_obj_cntrl |   golddiffat10 |   killsat10 |
|--------:|:-------|-------------:|--------------:|--------------:|-------------:|-------------:|------------------:|---------------:|------------:|
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

</div>

---

## Univariate Analysis

<iframe
  src="assets/win_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This visualization shows the distribution of match outcomes across the dataset. Since each match contributes one win and one loss at the team level, the distribution is balanced between the two classes. This balance is beneficial for predictive modeling because it prevents classification bias toward one outcome.

---

## Bivariate Analysis

<iframe
  src="assets/objective_winrate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot shows the relationship between **early objective control** and match outcomes. Teams that secure early objectives display a noticeably higher win rate compared to teams that do not. This suggests that early macro advantages may play a meaningful role in determining match outcomes.

---

## Grouped Analysis

The following grouped table summarizes win rates based on early objective control.


|  patch |       0 |       1 |
|-------:|--------:|--------:|
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


This grouped summary reinforces the trend observed in the visualization. Teams that secure early objectives win a substantially larger proportion of matches compared to teams that do not, suggesting that early objective control may be an important indicator of overall match success.

---

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

<iframe
  src="assets/missingness_permutation.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>

The permutation test examines whether the missingness of void_grubs depends on team side (Blue vs Red). Since the observed difference falls well within the permutation distribution, there is no evidence that missingness is related to team side. This supports the conclusion that missing values are instead caused by structural changes in the game, specifically the patch that introduced void grubs.

---

## Hypothesis Testing

The central question of this project is whether early-game advantages are associated with a higher probability of winning a professional League of Legends match. To evaluate this, I performed permutation tests on two early-game indicators: **early objective control** and **first blood**. In both cases, the goal was to determine whether teams that secured these early advantages tended to win more frequently than teams that did not.

### Hypothesis Test 1: Early Objective Control

- **Null Hypothesis (H₀):** Teams that secure early objective control and teams that do not have the same probability of winning.
- **Alternative Hypothesis (H₁):** Teams that secure early objective control have a higher probability of winning.

The **test statistic** used was the **difference in win rate** between the two groups. This statistic is appropriate because the research question directly concerns whether one group wins more frequently than the other. A **significance level of 0.05** was used.

The observed difference in win rate was approximately **0.229**, meaning teams that secured early objective control won about **22.9 percentage points more often** than teams that did not. The permutation test produced a **p-value close to 0**, indicating that such a large difference would be extremely unlikely under the null hypothesis. As a result, the null hypothesis was rejected, suggesting that early objective control is strongly associated with match outcomes.

### Hypothesis Test 2: First Blood

A second permutation test was conducted to evaluate whether securing **first blood** is associated with a higher probability of winning.

- **Null Hypothesis (H₀):** Teams that secure first blood and teams that do not have the same probability of winning.
- **Alternative Hypothesis (H₁):** Teams that secure first blood have a higher probability of winning.

The same **test statistic**—difference in win rate between groups—was used, along with the same **significance level of 0.05**.

The observed difference in win rate was approximately **0.220**, meaning teams that secured first blood won about **22.0 percentage points more often** than teams that did not. The permutation test again produced a **p-value close to 0**, providing strong evidence against the null hypothesis.

### Interpretation

Both hypothesis tests suggest that early-game advantages are meaningfully associated with match outcomes. Teams that secure early objective control or first blood tend to win more frequently than teams that do not. While these tests do not demonstrate causation, they suggest that early-game events contain meaningful information about eventual match outcomes. Because of this, these variables were also used as inputs in the predictive models developed in the following section.

---

## Prediction Problem

The predictive goal of this project is to determine whether **early-game advantages can predict match outcomes** in professional League of Legends games. Specifically, I built a model that predicts whether a team will **win or lose a match** based on information available in the early stages of the game.

### Prediction Type

This is a **binary classification problem** because the response variable has two possible outcomes: a team either **wins (1)** or **loses (0)** the match.

### Response Variable

The response variable for the model is:

- **`result`** — a binary variable indicating whether a team won the match (1) or lost (0).

This variable was chosen because it directly represents the outcome that teams ultimately care about: winning the game. Predicting match outcomes based on early-game signals helps evaluate whether early advantages meaningfully influence eventual results.

### Features Used for Prediction

The model uses several features that represent early-game conditions, including:

- `early_objective_control`
- `firstblood`
- `golddiffat10`
- `killsat10`

These features were selected because they capture **early momentum within a match**, such as objective control, early kills, and economic advantage.

### Time of Prediction

An important constraint in building the model is ensuring that only information available **early in the match** is used for prediction. This means the model only uses features that would realistically be known at the time a prediction is made. For example, metrics like **gold difference at 10 minutes or early objective control** occur early enough in the game to plausibly inform predictions about the final outcome. Later-game statistics such as total damage or final gold totals were intentionally excluded because they would not be available at prediction time.

### Evaluation Metric

To evaluate model performance, I used two metrics:

- **Accuracy**, which measures the proportion of correctly predicted match outcomes.
- **ROC-AUC**, which evaluates how well the model distinguishes between winning and losing teams across all possible classification thresholds.

ROC-AUC was included because accuracy alone can be misleading for classification problems that depend on a decision threshold. ROC-AUC instead evaluates the model’s ability to rank winning teams above losing teams, providing a more complete measure of predictive performance.

---

## Baseline Model

To establish a benchmark for predictive performance, I first trained a **baseline logistic regression classifier**. Logistic regression is a common starting point for binary classification tasks because it produces interpretable coefficients and performs well when relationships between predictors and the response variable are approximately linear.

### Model Features

The baseline model was trained using two early-game indicators:

- **`early_objective_control`** – whether the team secured an early neutral objective (dragon or herald)
- **`firstblood`** – whether the team secured the first kill of the match

Both variables are **binary indicators** that capture early momentum in the game.

Feature types:

| Feature | Type | Description |
|------|------|------|
| early_objective_control | Ordinal / binary | Indicates whether a team secured early objective control |
| firstblood | Ordinal / binary | Indicates whether a team secured the first kill |

Because both variables were already encoded as **0/1 binary indicators**, no additional categorical encoding was required. The features were therefore passed directly into the logistic regression model.

### Model Performance

The baseline model achieved the following performance on the held-out test dataset:

| Metric | Score |
|------|------|
| Accuracy | 0.619 |
| ROC-AUC | 0.644 |

Accuracy measures the proportion of matches where the model correctly predicted the winning team. ROC-AUC evaluates how well the model ranks winning teams above losing teams across all possible classification thresholds.

### Interpretation

The baseline model performs **better than random guessing**, indicating that early-game events contain some predictive information about match outcomes. However, the model’s accuracy and ROC-AUC values remain relatively modest, suggesting that early objective control and first blood alone do not fully explain match results. League of Legends matches involve many interacting factors, including team composition, gold advantages, and macro strategy.

Because of this, the baseline model serves primarily as a **reference point**. In the next section, additional early-game features and feature transformations are introduced to determine whether predictive performance can be improved.

---

## Final Model

To improve upon the baseline classifier, I expanded the feature set to include additional early-game indicators that capture **economic and combat advantages** within the first 10 minutes of a match.

### Feature Engineering

Two additional features were introduced:

- **`golddiffat10`** – the difference in total team gold at the 10-minute mark  
- **`killsat10`** – the number of kills secured by the team at 10 minutes

Both features represent **quantitative measures of early-game advantage**. In professional League of Legends matches, gold accumulation and early kills are key signals of momentum. Teams with a gold lead typically gain stronger items and map pressure, which can translate into control over later objectives and team fights. Similarly, early kills often reflect successful lane pressure or coordinated early-game strategies.

From the perspective of the **data generating process**, these variables capture early structural advantages that naturally emerge from gameplay. Because they quantify the magnitude of early advantage rather than simply indicating whether an event occurred, they provide additional information beyond the binary indicators used in the baseline model.

The final feature set therefore included:

| Feature | Type | Description |
|------|------|------|
| early_objective_control | Ordinal / binary | Indicates whether a team secured early neutral objectives |
| firstblood | Ordinal / binary | Indicates whether a team secured the first kill |
| golddiffat10 | Quantitative | Team gold difference at the 10-minute mark |
| killsat10 | Quantitative | Number of kills secured by the team at 10 minutes |

### Model and Hyperparameter Selection

The final model uses **logistic regression**, consistent with the baseline model, in order to maintain interpretability while evaluating the impact of additional features.

To improve model performance and ensure appropriate scaling of quantitative features, the following transformations were included within a single sklearn Pipeline:

- **Median imputation** to handle missing values in early-game statistics
- **Standard scaling** for quantitative variables (`golddiffat10`, `killsat10`)
- Logistic regression as the final classifier

Hyperparameters for the logistic regression model were selected using **GridSearchCV**, which performs cross-validation to identify the combination of parameters that generalizes best to unseen data. The grid search evaluated several values of the regularization strength parameter **C**, which controls the tradeoff between model flexibility and overfitting.

### Model Performance

The final model produced the following results on the held-out test dataset:

| Metric | Baseline Model | Final Model |
|------|------|------|
| Accuracy | 0.619 | 0.675 |
| ROC-AUC | 0.644 | 0.745 |

The confusion matrix shows how the final classifier performs when predicting match outcomes. The diagonal values represent correct predictions, while the off-diagonal values represent misclassifications. Most predictions fall along the diagonal, indicating that the model correctly identifies a majority of winning and losing teams, though some misclassification remains due to the complexity of match outcomes.

<iframe
  src="assets/confusion_matrix_final_model.html"
  width="700"
  height="550"
  frameborder="0">
</iframe>

### Interpretation

The final model improves substantially over the baseline model, particularly in terms of **ROC-AUC**, which increased from **0.644 to 0.745**. This indicates that the model is significantly better at distinguishing between winning and losing teams when additional early-game indicators are included.

This improvement aligns with expectations from the gameplay mechanics of League of Legends. While early objectives and first blood provide signals of early advantage, variables such as gold difference and early kill counts capture **the scale of a team's advantage**, which more directly reflects a team's position within the match.

Overall, the final model demonstrates that incorporating multiple forms of early-game information provides a stronger signal for predicting match outcomes.

---

## Fairness Analysis (of Final Model)

### Groups and Metric

To evaluate whether the final model performs differently across match contexts, I compared predictive performance across two groups defined by early-game macro outcomes:

- **Group X:** Teams that **secured early objective control** (`early_objective_control = 1`)
- **Group Y:** Teams that **did not secure early objective control** (`early_objective_control = 0`)

The evaluation metric used was **test accuracy**, since the prediction task is binary (win vs loss).

---

### Hypotheses, Test Statistic, and Significance Level

- **Null Hypothesis (H₀):** The model is fair with respect to early objective control. Any difference in test accuracy between Group X and Group Y is due to random chance.
- **Alternative Hypothesis (H₁):** The model is not fair with respect to early objective control. The model’s test accuracy differs between Group X and Group Y.

- **Test statistic:** Difference in test accuracy  
  \[
  \text{Accuracy(Group X)} - \text{Accuracy(Group Y)}
  \]
- **Significance level:** \(\alpha = 0.05\)

A **permutation test** was performed by randomly shuffling group labels (X/Y) while keeping the model predictions fixed, then recomputing the accuracy difference to form an empirical null distribution.

---

### Results

| Metric | Value |
|---|---:|
| Accuracy (Early objective secured) | 0.680 |
| Accuracy (No early objective) | 0.659 |
| Observed accuracy difference (Secured − Not) | 0.0215 |
| Difference (percentage points) | 2.15 pp |
| Permutation test p-value | 0.0865 |
| Decision (α = 0.05) | Fail to reject H₀ |

The model achieves slightly higher accuracy when early objectives are secured (0.680) compared to when they are not (0.659), producing an observed difference of 0.0215.

**Conclusion:** The model achieves slightly higher accuracy in matches where early objectives are secured; however, the permutation test yields a p-value of 0.0865, indicating that this difference is not statistically significant at the 0.05 level. Overall, the results suggest that the model’s predictive performance remains relatively consistent across these early-game conditions.

<iframe
  src="assets/fairness_permutation.html"
  width="900"
  height="600"
  frameborder="0"
></iframe>
