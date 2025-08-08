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

5. **Analysis on SPecific Pitch Types**

   * Using the individual player data on specific pitch types, created `combined_arsenal_df` to assess how specific swing archetypes perform against each pitch
   * `Archetype Strength Analyzer`, `Vulerability Matricies`, `Performance Tier Analysis`, `Archetype Attack Recommenation` Tables to asess the swing clusters from every point of view
   * Added visuals of specific performance heatmaps aimed to provide quick extrapolaiton for coaches and players
   * Full Description of each current Swing Archetype
   * Table of best and worst pitches to throw to specific types of hitters by RV and wOBA
   * Table and Visualization on the Performance of swing archetypes based on Run value per location: `runs_heart`, `runs_shadow`, `runs_chase`, `runs_waste`
   * Resulting Spray Chart of each Swing Archetype
   * Table and Visualization on the Performance of stance archetypes on each pitch type

6. **Predictions**

   * Given specific swing metrics, classify a sight player into their respective siwng archetype
   * Given simple performance metrics on a specific pitch, predict the player's projected Run Value on a specified Pitch (Handedness neutral)
   * Given the swing archetype and respective performance metrics of a specific fuzzy matched MLB player name, results in specific recommendations to move into the next performance tier
