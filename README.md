# FMD Outbreak Risk Prediction for South African Provinces

A machine learning project that predicts the risk of a Foot and Mouth Disease (FMD) outbreak in each South African province, month by month. This was built as a final year BSc Data Science capstone project at Sol Plaatje University.

## Project Goal

FMD is a serious livestock disease. Early warning can help farmers and veterinary services act before it spreads. This project asks one question: can we use rainfall, livestock numbers, past outbreaks and location to predict which provinces are most at risk in a given month?

## Data Sources

* **WOAH outbreak records:** 2,800 raw outbreak events with coordinates and dates.
* **FAO climate data:** Monthly rainfall by province.
* **FAO livestock data:** Yearly national numbers of cattle, goats and sheep.
* **GADM shapefile (level 2):** Used to match outbreak coordinates to provinces.

The data covers 2005 to 2024.

## Method

1. **Cleaning and spatial join.** Outbreak points that had no province name were matched to a province using the GADM map.
2. **Province by month table.** All data was merged into one table with 2,151 rows. Each row is one province in one month.
3. **Target.** `Outbreak` is 1 if at least one outbreak happened in that province and month, otherwise 0. Only about 4.7 percent of rows are outbreaks, so the classes are very unbalanced.
4. **Feature engineering.** The final model uses 13 features:
   * Rainfall, 3 month average rainfall, last month rainfall, and rainfall change
   * National cattle, goat and sheep numbers
   * Outbreak last month, and outbreak count over the last 3 months
   * Month as sine and cosine, so December and January are close together
   * Wildlife proximity score (set by hand: Limpopo, Mpumalanga and KwaZulu Natal = 3, North West and Gauteng = 2, others = 1)
   * Neighbouring province outbreaks last month
5. **Time based split.** Training uses 2005 to 2020. Testing uses 2021 to 2024. This avoids using the future to predict the past.

## Models Compared

Logistic Regression, Decision Tree, Random Forest and XGBoost were tested on the same split.

## Final Results (Test Set, 2021 to 2024)

The chosen model is a Logistic Regression with balanced class weights.

* Accuracy: 0.711
* Precision: 0.300
* Recall: 0.787
* F1 score: 0.434
* ROC AUC: 0.815

Logistic Regression was chosen because it caught the most real outbreaks (highest recall), which matters most for an early warning tool. The tree based models had higher accuracy, but they missed many outbreaks.

The strongest drivers in the model were the wildlife proximity score, the outbreak count over the last 3 months, and whether there was an outbreak last month.

## Predicted Risk by Province

Latest predicted outbreak probability:

* KwaZulu Natal: 0.991
* Limpopo: 0.671
* Mpumalanga: 0.656
* North West: 0.171
* Gauteng: 0.154
* Eastern Cape: 0.081
* Western Cape: 0.020
* Northern Cape: 0.018
* Free State: 0.018

## Limitations

* Livestock numbers are national, not provincial, so they do not change between provinces.
* The wildlife proximity score is set by hand and is not measured data.
* There are few outbreak examples, so results can change a lot with small changes in the data.
* Precision is low, which means many warnings will be false alarms.

## How to Run

1. Install the packages:

   `pip install -r requirements.txt`

2. Put the data files in the folder the notebook expects:
   * `fmd_outbreaks.csv`
   * `climate_rain.csv`
   * `livestock_stocks.csv`
   * `datasets/gadm41_ZAF_2.shp` (and its matching `.shx`, `.dbf` and `.prj` files)

3. Open the notebook in the second cell and change the folder path in `os.chdir(...)` to the folder on your own computer.

4. Run all cells in Jupyter Notebook or JupyterLab.

## Tools Used

Python, pandas, NumPy, GeoPandas, scikit-learn, XGBoost, Matplotlib and Seaborn.

## Author

Moseki Mere, BSc Data Science, Sol Plaatje University.
