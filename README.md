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

| gameid                | datacompleteness   | url                                         | league   |   year | split   |   playoffs | date                |   game |   patch |   participantid | side   | position   |   playername |   playerid | teamname                 | teamid                                  |   firstPick |   champion | ban1     | ban2         | ban3         | ban4     | ban5    | pick1    | pick2     | pick3    | pick4   | pick5     |   gamelength |   result |   kills |   deaths |   assists |   teamkills |   teamdeaths |   doublekills |   triplekills |   quadrakills |   pentakills |   firstblood |   firstbloodkill |   firstbloodassist |   firstbloodvictim |   team kpm |   ckpm |   firstdragon |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs |   dragons (type unknown) |   elders |   opp_elders |   firstherald |   heralds |   opp_heralds |   void_grubs |   opp_void_grubs |   firstbaron |   barons |   opp_barons |   atakhans |   opp_atakhans |   firsttower |   towers |   opp_towers |   firstmidtower |   firsttothreetowers |   turretplates |   opp_turretplates |   inhibitors |   opp_inhibitors |   damagetochampions |     dpm |   damageshare |   damagetakenperminute |   damagemitigatedperminute |   damagetotowers |   wardsplaced |    wpm |   wardskilled |   wcpm |   controlwardsbought |   visionscore |   vspm |   totalgold |   earnedgold |   earned gpm |   earnedgoldshare |   goldspent |        gspd |    gpr |   total cs |   minionkills |   monsterkills |   monsterkillsownjungle |   monsterkillsenemyjungle |     cspm |   goldat10 |   xpat10 |   csat10 |   opp_goldat10 |   opp_xpat10 |   opp_csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   killsat10 |   assistsat10 |   deathsat10 |   opp_killsat10 |   opp_assistsat10 |   opp_deathsat10 |   goldat15 |   xpat15 |   csat15 |   opp_goldat15 |   opp_xpat15 |   opp_csat15 |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   assistsat15 |   deathsat15 |   opp_killsat15 |   opp_assistsat15 |   opp_deathsat15 |   goldat20 |   xpat20 |   csat20 |   opp_goldat20 |   opp_xpat20 |   opp_csat20 |   golddiffat20 |   xpdiffat20 |   csdiffat20 |   killsat20 |   assistsat20 |   deathsat20 |   opp_killsat20 |   opp_assistsat20 |   opp_deathsat20 |   goldat25 |   xpat25 |   csat25 |   opp_goldat25 |   opp_xpat25 |   opp_csat25 |   golddiffat25 |   xpdiffat25 |   csdiffat25 |   killsat25 |   assistsat25 |   deathsat25 |   opp_killsat25 |   opp_assistsat25 |   opp_deathsat25 |   early_objective_control | result_label   | objective_label   |
|:----------------------|:-------------------|:--------------------------------------------|:---------|-------:|:--------|-----------:|:--------------------|-------:|--------:|----------------:|:-------|:-----------|-------------:|-----------:|:-------------------------|:----------------------------------------|------------:|-----------:|:---------|:-------------|:-------------|:---------|:--------|:---------|:----------|:---------|:--------|:----------|-------------:|---------:|--------:|---------:|----------:|------------:|-------------:|--------------:|--------------:|--------------:|-------------:|-------------:|-----------------:|-------------------:|-------------------:|-----------:|-------:|--------------:|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|-----------:|-------------------------:|---------:|-------------:|--------------:|----------:|--------------:|-------------:|-----------------:|-------------:|---------:|-------------:|-----------:|---------------:|-------------:|---------:|-------------:|----------------:|---------------------:|---------------:|-------------------:|-------------:|-----------------:|--------------------:|--------:|--------------:|-----------------------:|---------------------------:|-----------------:|--------------:|-------:|--------------:|-------:|---------------------:|--------------:|-------:|------------:|-------------:|-------------:|------------------:|------------:|------------:|-------:|-----------:|--------------:|---------------:|------------------------:|--------------------------:|---------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|--------------------------:|:---------------|:------------------|
| ESPORTSTMNT01_2690210 | complete           | nan                                         | LCKC     |   2022 | Spring  |          0 | 2022-01-10 07:44:08 |      1 |   12.01 |             100 | Blue   | team       |          nan |        nan | HANJIN BRION Challengers | oe:team:fa3d687a87bcae80362f784a7da571d |           1 |        nan | Karma    | Caitlyn      | Syndra       | Thresh   | Lulu    | Renekton | Samira    | Xin Zhao | LeBlanc | Leona     |         1713 |        0 |       9 |       19 |        19 |           9 |           19 |             0 |             0 |             0 |            0 |            1 |              nan |                nan |                nan |     0.3152 | 0.9807 |             0 |         1 |             3 |                 1 |                     3 |           0 |           0 |        0 |        0 |           0 |          1 |                      nan |        0 |            0 |             1 |         2 |             0 |          nan |              nan |            0 |        0 |            0 |        nan |            nan |            1 |        3 |            6 |               1 |                    1 |              5 |                  0 |            0 |                1 |               56560 | 1981.09 |           nan |                3537.2  |                    2364.73 |             6168 |            74 | 2.5919 |            51 | 1.7863 |                   33 |           197 | 6.9002 |       47070 |        28222 |      988.511 |               nan |       44570 | -0.0283123  |   0.94 |        nan |           680 |            160 |                     nan |                       nan |  29.4221 |      16218 |    18213 |      322 |          14695 |        18076 |          330 |           1523 |          137 |           -8 |           3 |             5 |            0 |               0 |                 0 |                3 |      24806 |    28001 |      487 |          24699 |        29618 |          510 |            107 |        -1617 |          -23 |           5 |            10 |            6 |               6 |                18 |                5 |      31962 |    36874 |      631 |          32906 |        41821 |          715 |           -944 |        -4947 |          -84 |           5 |            10 |            7 |               7 |                22 |                5 |      40224 |    45960 |      767 |          40136 |        49931 |          864 |             88 |        -3971 |          -97 |           6 |            12 |            7 |               7 |                22 |                6 |                         1 | Loss           | Yes               |
| ESPORTSTMNT01_2690210 | complete           | nan                                         | LCKC     |   2022 | Spring  |          0 | 2022-01-10 07:44:08 |      1 |   12.01 |             200 | Red    | team       |          nan |        nan | Nongshim Esports Academy | oe:team:7c64febcd5ccff13dcd035dc6867a00 |           0 |        nan | Lee Sin  | Twisted Fate | Zoe          | Nautilus | Rell    | Jinx     | Viego     | Gragas   | Viktor  | Alistar   |         1713 |        1 |      19 |        9 |        62 |          19 |            9 |             6 |             0 |             0 |            0 |            0 |              nan |                nan |                nan |     0.6655 | 0.9807 |             1 |         3 |             1 |                 3 |                     1 |           2 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 |             0 |         0 |             2 |          nan |              nan |            0 |        0 |            0 |        nan |            nan |            0 |        6 |            3 |               0 |                    0 |              0 |                  5 |            1 |                0 |               79912 | 2799.02 |           nan |                3009.67 |                    2872.33 |            13846 |            93 | 3.2574 |            51 | 1.7863 |                   45 |           205 | 7.1804 |       52617 |        33769 |     1182.8   |               nan |       45850 |  0.0283123  |  -0.94 |        nan |           792 |            184 |                     nan |                       nan |  34.1856 |      14695 |    18076 |      330 |          16218 |        18213 |          322 |          -1523 |         -137 |            8 |           0 |             0 |            3 |               3 |                 5 |                0 |      24699 |    29618 |      510 |          24806 |        28001 |          487 |           -107 |         1617 |           23 |           6 |            18 |            5 |               5 |                10 |                6 |      32906 |    41821 |      715 |          31962 |        36874 |          631 |            944 |         4947 |           84 |           7 |            22 |            5 |               5 |                10 |                7 |      40136 |    49931 |      864 |          40224 |        45960 |          767 |            -88 |         3971 |           97 |           7 |            22 |            6 |               6 |                12 |                7 |                         1 | Win            | Yes               |
| ESPORTSTMNT01_2690219 | complete           | nan                                         | LCKC     |   2022 | Spring  |          0 | 2022-01-10 08:38:24 |      1 |   12.01 |             100 | Blue   | team       |          nan |        nan | T1 Esports Academy       | oe:team:731b7a9fd004cdbe2bcb3da795bce47 |           1 |        nan | Sona     | Jarvan IV    | Caitlyn      | Lulu     | Lucian  | Lee Sin  | Jhin      | Gragas   | Rakan   | Orianna   |         2114 |        0 |       3 |       16 |         7 |           3 |           16 |             0 |             0 |             0 |            0 |            0 |              nan |                nan |                nan |     0.0851 | 0.5393 |             0 |         1 |             4 |                 1 |                     4 |           0 |           1 |        0 |        0 |           0 |          0 |                      nan |        0 |            0 |             1 |         1 |             1 |          nan |              nan |            0 |        0 |            2 |        nan |            nan |            0 |        3 |           11 |               0 |                    0 |              2 |                  3 |            0 |                2 |               59579 | 1690.98 |           nan |                2984.02 |                    3109.61 |             6842 |           119 | 3.3775 |            55 | 1.561  |                   47 |           277 | 7.8619 |       57629 |        34688 |      984.522 |               nan |       53945 | -0.207137   |  -3.23 |        nan |           994 |            215 |                     nan |                       nan |  34.3141 |      14939 |    17462 |      317 |          16558 |        19048 |          344 |          -1619 |        -1586 |          -27 |           1 |             1 |            3 |               3 |                 3 |                1 |      23522 |    28848 |      533 |          25285 |        29754 |          555 |          -1763 |         -906 |          -22 |           1 |             1 |            3 |               3 |                 3 |                1 |      31228 |    38596 |      710 |          36368 |        42069 |          758 |          -5140 |        -3473 |          -48 |           1 |             1 |            5 |               5 |                 6 |                1 |      39335 |    49409 |      895 |          46615 |        57155 |          928 |          -7280 |        -7746 |          -33 |           1 |             1 |            8 |               8 |                13 |                1 |                         1 | Loss           | Yes               |
| ESPORTSTMNT01_2690219 | complete           | nan                                         | LCKC     |   2022 | Spring  |          0 | 2022-01-10 08:38:24 |      1 |   12.01 |             200 | Red    | team       |          nan |        nan | Liiv SANDBOX Youth       | oe:team:e7a7c6bf58eb268ed3f13aac4158aa8 |           0 |        nan | LeBlanc  | Yuumi        | Twisted Fate | Karma    | Alistar | Renekton | Syndra    | Nidalee  | Leona   | Gangplank |         2114 |        1 |      16 |        3 |        39 |          16 |            3 |             1 |             0 |             0 |            0 |            1 |              nan |                nan |                nan |     0.4541 | 0.5393 |             1 |         4 |             1 |                 4 |                     1 |           0 |           2 |        1 |        0 |           0 |          1 |                      nan |        0 |            0 |             0 |         1 |             1 |          nan |              nan |            1 |        2 |            0 |        nan |            nan |            1 |       11 |            3 |               1 |                    1 |              3 |                  2 |            2 |                0 |               74855 | 2124.55 |           nan |                2745.72 |                    2868.42 |            19944 |           129 | 3.6613 |            70 | 1.9868 |                   65 |           346 | 9.8202 |       71004 |        48063 |     1364.13  |               nan |       66410 |  0.207137   |   3.23 |        nan |          1013 |            244 |                     nan |                       nan |  35.6764 |      16558 |    19048 |      344 |          14939 |        17462 |          317 |           1619 |         1586 |           27 |           3 |             3 |            1 |               1 |                 1 |                3 |      25285 |    29754 |      555 |          23522 |        28848 |          533 |           1763 |          906 |           22 |           3 |             3 |            1 |               1 |                 1 |                3 |      36368 |    42069 |      758 |          31228 |        38596 |          710 |           5140 |         3473 |           48 |           5 |             6 |            1 |               1 |                 1 |                5 |      46615 |    57155 |      928 |          39335 |        49409 |          895 |           7280 |         7746 |           33 |           8 |            13 |            1 |               1 |                 1 |                8 |                         1 | Win            | Yes               |
| 8401-8401_game_1      | partial            | https://lpl.qq.com/es/stats.shtml?bmid=8401 | LPL      |   2022 | Spring  |          0 | 2022-01-10 09:24:26 |      1 |   12.01 |             100 | Blue   | team       |          nan |        nan | Oh My God                | oe:team:f4c4528c6981e104a11ea7548630c23 |           1 |        nan | Renekton | Lee Sin      | Caitlyn      | Jayce    | Camille | Jinx     | Jarvan IV | Nautilus | Syndra  | Gwen      |         1365 |        1 |      13 |        6 |        35 |          13 |            6 |           nan |           nan |           nan |          nan |            0 |              nan |                nan |                nan |     0.5714 | 0.8352 |             0 |         2 |             1 |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                        2 |      nan |          nan |             0 |         2 |             0 |            0 |                0 |          nan |        1 |            0 |        nan |            nan |            1 |        8 |            3 |             nan |                  nan |            nan |                nan |            1 |                0 |               40086 | 1762.02 |           nan |                2263.25 |                     nan    |              nan |            79 | 3.4725 |            33 | 1.4505 |                   32 |           162 | 7.1209 |       45468 |        30167 |     1326.02  |               nan |       36908 | -0.00586225 | nan    |        nan |           nan |            172 |                      98 |                        18 | nan      |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |        nan |      nan |      nan |            nan |          nan |          nan |            nan |          nan |          nan |         nan |           nan |          nan |             nan |               nan |              nan |                         1 | Win            | Yes               |

---

## Univariate Analysis

<iframe
  src="assets/win_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

This visualization shows the distribution of match outcomes across the dataset. Since each match contributes one win and one loss at the team level, the distribution is balanced between the two classes. This balance is beneficial for predictive modeling because it prevents classification bias toward one outcome.

---

## Bivariate Analysis

<iframe
  src="assets/earlyadvcompar.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

This plot shows the relationship between **early objective control** and match outcomes. Teams that secure early objectives display a noticeably higher win rate compared to teams that do not. This suggests that early macro advantages may play a meaningful role in determining match outcomes.

---

## Grouped Analysis

The following grouped table summarizes win rates based on early objective control.

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

This grouped summary reinforces the trend observed in the visualization. Teams that secure early objectives win a substantially larger proportion of matches compared to teams that do not, suggesting that early objective control may be an important indicator of overall match success.


<h2>Assessment of Missingness</h2>

<h2>Hypothesis Testing</h2>

<h2>Framing a Prediction Problem</h2>

<h2>Baseline Model</h2>

<h2>Final Model</h2>

<h2>Fairness Analysis</h2>
