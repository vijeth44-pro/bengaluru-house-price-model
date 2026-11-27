# Bengaluru House Price Project

This is a student project about Bengaluru house prices. It looks at past property listings and explores whether details such as area, location, and number of bedrooms can help estimate a property's listed price.

This project is for learning. Its estimates are not official property valuations and may not match today's market prices.

## What question does the project explore?

Can we estimate a listed property's price from its location, property type, bedroom count, bathrooms, balconies, and area?

- **What we predict:** `price`, recorded in lakh rupees.
- **One prediction means:** an estimate for one property listing.
- The original file has **13,320 rows**. The current cleaned file has **11,947 rows**.

## Where does the data come from?

The file `Bengaluru_House_Data.csv` came from this Kaggle dataset page:

<https://www.kaggle.com/datasets/amitabhajoy/bengaluru-house-price-data>

The page lists the dataset under the Apache-2.0 license. Check the page for the latest terms before reusing or sharing the data.

## What is in the project folder?

```text
data/
  raw/                 Original data file
  processed/           Cleaned data and data reports
notebooks/             Phase 1 and Phase 2 notebooks
models/                Model results and saved prediction model
reports/charts/        Charts made during the analysis
phase1_analysis.py     Cleans and studies the data
phase2_modeling.py     Trains and compares prediction models
```

## Set up the project

Use Python 3.10 or newer. Open a terminal in this project folder and create a separate Python environment:

```bash
python -m venv .venv
```

Turn on the environment:

```powershell
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
```

```bash
# macOS or Linux
source .venv/bin/activate
```

Install the project's Python packages:

```bash
python -m pip install -r requirements.txt
```

## Phase 1: clean and understand the data

Run this command from the project folder:

```bash
python phase1_analysis.py
```

The script reads the original CSV and creates an analysis copy. It does not change the original file. It makes:

- A cleaned dataset: `data/processed/bengaluru_house_cleaned.csv`
- A data dictionary that explains the columns
- Reports showing missing data and cleaning decisions
- Summary numbers and a statistical test result
- Eight charts in `reports/charts/`

Some data needs to be made easier to work with. For example, the script gets the bedroom count from text such as `2 BHK`, converts area values to square feet, and uses the middle of an area range such as `1115 - 1130`. It also removes repeated rows and some records that fail the project's data checks. The cleaning decisions and reasons are recorded in `data/processed/cleaning_decision_log.csv`.

## Phase 2: train and compare prediction models

Run Phase 1 first if the cleaned data file is missing. Then run:

```bash
python phase2_modeling.py
```

The models use these property details:

- `location_grouped` — the locality, with uncommon localities grouped as `other`
- `area_type` — the type of area listing
- `bhk` — number of bedrooms
- `bath` — number of bathrooms
- `balcony` — number of balconies
- `total_sqft_numeric` — area in square feet

The value being predicted is `price`. The script does **not** use `price_per_sqft` as an input because that value is calculated from the price we are trying to predict. Using it would give the model information about its answer.

The data is split into training and test groups. The models learn from the training group; the test group is held aside to check how well they predict listings they did not train on. The script compares Ridge Regression, Random Forest, and Gradient Boosting. It also tries different Random Forest settings using cross-validation, a way to compare settings across several training-data splits.

The results are saved in:

- `models/model_comparison.csv` — scores for the models
- `models/random_forest_tuning.json` — the chosen Random Forest settings and scores
- `models/feature_importance.csv` — which inputs the Random Forest relied on most
- `models/bengaluru_house_price_model.joblib` — the saved Random Forest model and its input-processing steps

### Results so far

On the current test data:

- **Gradient Boosting** has the lowest RMSE (**103.91 lakh**) and highest R² (**0.591**) among the three original comparisons.
- The **tuned Random Forest** has MAE **36.03 lakh**, RMSE **109.09 lakh**, and R² **0.549**.

MAE and RMSE describe prediction errors in lakh rupees; lower is better. R² describes how much of the price variation the model explains; higher is better. The saved model file is currently the tuned Random Forest, even though Gradient Boosting has the better RMSE in the current comparison. The team still needs to decide which model to choose and explain why. These scores are overall test results, not promises about an individual property's estimate.

## Notebooks and slides

You can open the notebooks using Jupyter or the Jupyter extension in VS Code. Choose the project's Python environment as the notebook kernel, then run cells from top to bottom:

- `notebooks/Bengaluru_House_Price_Phase1_EDA.ipynb`
- `notebooks/Bengaluru_House_Price_Phase2_Modeling.ipynb`

## What is done and what is still planned?

The project currently includes data cleaning, charts and statistics, and an initial comparison of house-price prediction models. A Random Forest model has also been tuned and saved.

The following parts are **not built yet**: a neural-network model, a GenAI assistant, a FastAPI service, a Streamlit website, Docker packaging, or cloud deployment.

The dataset does not have a reliable date for each listing, so this project cannot yet test its predictions on a later time period. Prices vary a lot, including some very expensive listings, which can make large prediction errors affect RMSE. Feature importance describes how this Random Forest used the data; it does not prove that a feature causes a price to change.
