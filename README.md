# Completion Optimization – Predicting Well Performance

**NSC 325 | UT Inventors Program – Spring 2025**  
**Team:** Isaac Xu, Alex Huynh, Shamiya Lin, Shahrukh Ahmed, Xzavier Barajas

## READ BEFORE RUNNING

The project code consists of 3 parts: the ipynb notebook files (there are two, one with the main modeling code, the other with the graphing used in the manuscript), the manuscript, and the accompanying .csv files (the code takes a while to run, to demo the code it is best to download these and are under the data folder.
The code to create the csv's under the data folder is within the modeling code, named Completions_Final.ipynb

## Objective

This project investigates the relationship between hydraulic fracturing completion design and well productivity. Using data from over 10,000 unconventional wells, we apply exploratory data analysis and machine learning techniques to model 12-month cumulative production, aiming to:

- Determine the optimal number of completion stages  
- Evaluate proppant and fluid volume impacts  
- Identify optimal drilling areas and formations  
- Quantify the influence of completion vs. location on production  
- Recommend completion configurations for future operations  


## 1. Data Cleaning and Preprocessing

**Dataset includes:**
- Completion configurations: `Stages`, `Total Proppant`, `Total Fluid`, `Fluid Type`, `Completions Type`, `Operator`
- Location: `Latitude`, `Longitude`, `Township`, `Range`, `Section`, `Formation`
- Production: `Best Monthly Production`, `12-Month Cumulative Production`

**Preprocessing steps:**
- Dropped: `Township`, `Range`, `Section`, `Fluid Type`, `Best Monthly Production` due to collinearity or missingness
- Dropped `SPACING_CAPPED` (potentially biased), replaced with `Neighbors_Count_UpToDate`
- Created new features: 
  - `Proppant_per_Stage` = `Total Proppant / Stages`
  - `Fluid_per_Stage` = `Total Fluid / Stages`
- Dropped rows with missing data (~200 records)
- Applied label encoding to all categorical variables


## 2. Exploratory Data Analysis

**Insights:**
- **Stage Count:** Positive correlation with production up to ~40 stages; diminishing returns beyond
- **Proppant & Fluid Volume:** Correlate with higher production but exhibit diminishing gains after optimal thresholds
- **Formation Impact:** TF1 and UTFH formations deliver highest returns; TF4 underperforms
- **Well Spacing:** Close proximity causes interference; optimal spacing is critical
- **Sweet Spots:** Specific geographic clusters outperform others; fewer stages needed in those regions
- **Operator Efficiency:** Significant performance variance; Operator 12 demonstrated highest fluid efficiency
- **Fluid Type:** SW-XL Hybrid shows highest efficiency (limited by missing data)

## 3. Modeling

**Goal:** Predict 12-month cumulative production  

**Model Used:** CatBoost Regressor

**Performance Metrics:**
- R²: 0.7645  
- Mean Absolute Percentage Error (MAPE): 27.03%

CatBoost was chosen for its ability to handle categorical features and deliver high accuracy without extensive hyperparameter tuning.


## 4. Modeling Uncertainty and Precision

- Bootstrapped predictions for interval estimation
- Most predictions fall within 90% confidence intervals
- Wider intervals for high-production wells indicate greater variability
- Confidence interval widths mostly under 40 units, representing an estimated ~$2.6 million range in output


## 5. SHAP Analysis and Spatial Interpolation

- SHAP values used for model interpretation
- Key features: `Formation`, `Proppant_per_Stage`, `Fluid_per_Stage`, spatial coordinates
- Applied kriging with spherical variogram to interpolate SHAP values across the field
- Resulting spatial heatmap highlights predictive importance of location


## 6. Results

The following heatmap shows the **Kriging Interpolation** of average SHAP values across the study region, highlighting spatially driven predictive variance:

![Average Kriging Interpolation](https://github.com/user-attachments/assets/1b5ae749-6bfb-4d2c-9c77-63737c440bd1)


## Conclusion

This study highlights the impact of both design and location in hydraulic fracturing efficiency. Key takeaways:

- Stage count, fluid, and proppant volume must be optimized—not maximized
- Location and formation type are primary production drivers
- The CatBoost model achieved high predictive performance (R² = 0.7645)
- SHAP and kriging enable interpretable, spatially aware decision-making

Future work could incorporate more geological and time-dependent features to enhance both prediction accuracy and uncertainty quantification.
