# MLB Swing and Stance Clustering

### Purpose

This notebook clusters baseball players based on their **stance** and **swing mechanics** to identify distinct hitting archetypes. By combining **data preprocessing, clustering algorithms, visual EDA, and predictive features**, this workflow aims to enable coaches, players, and analysts to understand identify sight player clusters, compare styles, categorize player tendencies, then tailor pitch sequences and matchups.

---

### Main Sections & Responsibilities

1. **Data Import & Setup**

   * Loads player stance and swing datasets from CSVs, scraped directly from *Baseball Savant*
   * Similarly Scrapes extra data: `Batting Run Value`, `Expected Statistics`, `Batted Ball`, `Percent Ranks`, `Player Value`
   * Imports required Python libraries: `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`.
   * Defines display options and initializes reproducibility settings.
   * Combines all of these scraped dataframes into a combined `final_df`

2. **Data Preprocessing**

   * Cleans datasets, handles missing values, ensures numerical consistency, and hardcodes merges to eliminate specific columns to avoid alterations
   * Selects relevant mechanical features (e.g., bat speed, swing tilt, attack angle, stance angle, foot separation).
   * Standardizes features with `StandardScaler` to ensure equal weight in clustering.

3. **Clustering Models**

   * Fits **Gaussian Mixture Models (GMM)** with spherical covariance to capture cluster probabilities.
   * Optionally compares with **K-Means** for baseline segmentation.
   * Displays results from both and all viable options to allow linear decision making from the user to choose correct method + k
   * Uses **PCA** to project high-dimensional features into 2D space of swing clusters since swings have higher variability than stances
   * Creates scatter plots colored by cluster to visualize player groupings.
   * Computes per-cluster mean ± standard deviation for each swing/stance metric.
   * Generates tables that serve as reference profiles for each archetype.


4. **EDA on Swing CLusters**

   * Simple interactive heatmaps that display players' names based on their classified swing/stance cluster and performance tier
   * Simple Success/Fail Rate tables to quickly assess the effectiveness of specific swing/stance archetypes

5. **Analysis on Specific Pitch Types**

   * Using the individual player data on specific pitch types, created `combined_arsenal_df` to assess how specific swing archetypes perform against each pitch
   * `Archetype Strength Analyzer`, `Vulerability Matricies`, `Performance Tier Analysis`, `Archetype Attack Recommenation` Tables to asess the swing clusters from every point of view
   * Added visuals of specific performance heatmaps aimed to provide quick extrapolaiton for coaches and players
   * Full Description of each current Swing Archetype
   * Table of best and worst pitches to throw to specific types of hitters by RV and wOBA
   * Table and Visualization on the Performance of swing archetypes based on Run value per location: `runs_heart`, `runs_shadow`, `runs_chase`, `runs_waste`
   * Resulting Spray Chart of each Swing Archetype
   * Table and Visualization on the Performance of stance archetypes on each pitch type

6. **Predictions**

   1. **Swing Archetype Classification**
      1. The purpose of this component was to develop a flexible and robust model that can automatically classify a baseball player's swing into a specific archetype (e.g., "Slugger," "Contact Hitter") based on key biomechanical and result metrics.
      2. A **Random Forest Classifier** was selected as the primary model.
         1. **Notes**: To address incomplete player records, a **K-Nearest Neighbors (KNN) Imputer** was employed. This technique fills in missing values by averaging the data from the five most similar players in the dataset, preserving the underlying data structure more robustly than simple median imputation.
   2. **Run Value on Pitch Type**
      1. The objective of this model is to build a predictive model that estimates the expected **Run Value** of a given pitch type based on a hitter’s underlying performance metrics. This model aimed to *quantify* the run value contribution of specific pitch-hitter interactions of a batter against a specific pitch type, regardless of handness.
      2. A **Gradient Boosting Regressor** was used to train the model on an 80/20 train-validation split.
          1. I optimized for stability and interpretability with: **500 estimators**, **0.02 learning rate**, and **max_depth=6**. These choices were made to balance overfitting risk and predictive power.
      3. Similar to the previous classifier, I also used a **KNNImputer (k=5)** to address the missing data, rather than relying on global averages, to promote contextual consistency among players with similar performance profiles.
      4. In addition to the model-based feature scaling, I applied **manual feature weighting** to control the how much each statistic effected the overal calculation of the predicted run value.
         1. For instance, **wOBA (0.335)** and **SLG (0.31)** received the highest weights because they most accurately reflect overall offensive value, while **Whiff%, K%, and Hard Hit%** received smaller weights since they explain a much smaller portion of the variance within the subsets of performance.
   3. **Player Tier Progression Model**
      1. This model bridges aims to bridge the gap between predicting outcomes and the directing the decision makers to answer: *“what should a player X do next?”*. It’s designed to **identify the specific, measurable performance or mechanical adjustments** a player should make to move from their current **performance tier** (ex., *Average → Above Average → Elite*) based on the attributes of players who have already succeeded at those higher tiers.
      2. **Background**
         1. **Pitch-by-pitch performance data (arsenal_df)**
         2. **Overall swing, batted-ball, and biomechanical features (final_df)**
         3. Players were categorized into **six tiers** (Poor, Below, Average, Above, Elite, Superstar) based on composite performance scores.
      3. **Feature Engineering**
         1. **Contact quality & results** (wOBA, SLG, Barrel%, Hard Hit%)
         2. **Swing mechanics & bat motion** (Bat Speed, Swing Tilt, Attack Angle, Ideal Attack Angle Rate, Contact Point)
         3. **Pitch-type performance** (Fastball, Breaking Ball, Off-Speed metrics: wOBA_x, Whiff%, Hard Hit%)
      4. **Benchmark & Progression Analysis**
         1. Using these features, the model first computes **benchmark distributions** for Elite/Superstar players — means, standard deviations, and IQRs for every key variable.
         2. Then, via **t-tests and Cohen’s d effect size**, it quantifies how each lower-tier group statistically differs from the top-tier.
         3. This identifies **progression factors**: the most important obsserved attributes that most strongly separate high-tier from low-tier hitters. ***KEY TO NOTE***: these are just **SUGGESTIONS**, every player will naturally be different and different things will work in each case, many of these reccomended adjustments are overly simplified to pinpoint jumpstarts to making tangible change and focus in training.
      5. **Predictive Modeling**
         1. Random Forest: The first model, used as a non-parametric baseline to evaluate overall predictive signal and detect noisy or redundant features. The feature importances served as a quick sanity check to quickly confirm that the chosen metrics I have are predictive of tier separation.
         2. Gradient Boosting Classifier: Once the data’s structure was validated, I moved to this classifier, which sequentially builds trees that correct the errors of prior ones. This model is the driving engine for predicting the adjustments of player X's most probable next-tier classification given their current measurable profile.
         3. Logistic Regression: Finally, I used a multinomial Logistic Regression model to generate my interpretable benchmarks. The linear coefficients and the odds ratios helped me bridge the gap between RF/GBC and give clear communication for a coach or PD staff.
         
