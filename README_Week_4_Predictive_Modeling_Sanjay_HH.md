# Week 4 — Predictive Modeling Framework

**Student:** SANJAY H H  
**USN:** 1MJ24CD041  
**Programme:** 5th Semester, CSE (Data Science)  
**College:** MVJ College of Engineering, Bengaluru

## What this project contains

- `Week_4_Predictive_Modeling_Framework_Sanjay_HH.docx` — the single Word report intended for the assignment portal. Submit this DOCX separately; the ZIP is a supporting project bundle for GitHub/backup.
- `data/crop_statistics_selected_all_india.csv` — the five-year all-India benchmark extract used in the earlier EDA work.
- `data/maize_national_benchmark_with_calculations.csv` — maize benchmark with derived yield and year-on-year calculations.
- `data/feature_dictionary.csv` — planned model input features, rationale, sources and availability rules.
- `code/analyze_national_benchmark.py` — reproduces the descriptive maize calculations from the supplied five-year national file.
- `code/train_yield_model.py` — a leakage-aware training template for a future real district-level panel.
- `templates/district_maize_panel_template.csv` — the expected schema for the real district-year input panel.
- `templates/illustrative_input_row_NOT_TRAINING_DATA.csv` — a single hypothetical feature row for explanation only; do not use it to train a model.
- `figures/` — workflow, model-split and benchmark graphics.
- `notebooks/01_national_maize_benchmark.ipynb` — reproducible notebook for national benchmark calculations.
- `MODEL_CARD_TEMPLATE.md` and `DATA_COLLECTION_CHECKLIST.md` — documentation for responsible model development.

## Important: this is a framework proposal, not a trained model

The provided national maize benchmark contains only five annual observations and no district-level variation. It is suitable for demonstrating yield units and descriptive trends, but it is not sufficient for a credible machine-learning train/validation/test exercise. No fitted-model accuracy, validation score or test score is claimed in the Word report.

The Python training template expects a larger, authentic district-level maize panel named:

`data/district_maize_panel.csv`

You must assemble and quality-check that panel from the sources listed below before running the script. The included one-row illustrative template is synthetic and must never be used as model training data.

## Planned model target and forecast time

- Crop: Kharif maize (initial pilot)
- Geography: eligible Indian districts with consistent records
- Target: final district yield in tonnes per hectare (t/ha)
- Forecast time: pre-harvest; the design uses a prototype cutoff of 31 August, adjusted to a verified district crop calendar when required
- Proposed data window: 2005–2024, only where source coverage and overlap are adequate
- Proposed chronological split: training 2005–2017, validation 2018–2019, final test 2020–2024

## How to assemble the real panel

1. Download district-, crop-, season- and year-level area/production/yield data from the DES APY report or OGD crop-production statistics.
2. Harmonize district identifiers and boundary changes; do not join on district names alone.
3. Join IMD rainfall and temperature summaries to district polygons using a documented spatial aggregation method.
4. Add irrigation, soil or sowing-progress features only where the values are genuine, comparable and available by the forecast issue date.
5. Calculate target yield as production (tonnes) divided by harvested area (hectares). Do not impute a missing target.
6. Generate lag-1 and lag-2 yield within each district and season. Make sure only completed past seasons are used.
7. Audit every feature timestamp. Exclude final production, final yield, post-cutoff weather and any other future information from predictors.
8. Save the complete panel to `data/district_maize_panel.csv` and then run the training template.

## Run the code

Install the required packages:

```bash
python -m pip install -r requirements.txt
```

After the authentic panel is assembled:

```bash
python code/train_yield_model.py
```

The script will stop with a clear message if the district panel is missing, too small, has duplicate district-season-year keys, or lacks the expected train/validation/test periods. If the data pass the checks, it compares a lag-1 persistence baseline with Ridge, Random Forest and Histogram Gradient Boosting. It chooses using validation MAE and evaluates the final holdout only after selection.

Outputs are written to `outputs/`: validation/test metric tables, test predictions, permutation importance, and a fitted pipeline only if an ML candidate beats the persistence baseline on validation.

## Source register

1. DES, DA&FW — District APY report interface: https://data.desagri.gov.in/website/crops-apy-report-web
2. OGD India — District-wise season-wise crop production statistics from 1997: https://data.gov.in/resource/district-wise-season-wise-crop-production-statistics-1997
3. Tata-Cornell Institute / ICRISAT — District-Level Database for Indian Agriculture and Allied Sectors: https://tci.cornell.edu/?projects=district-level-database-for-indian-agriculture-and-allied-sectors
4. IMD — Daily gridded rainfall, 0.25°: https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_Bin.html
5. IMD — Daily gridded maximum temperature, 1°: https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html
6. IMD — Daily gridded minimum temperature, 1°: https://www.imdpune.gov.in/cmpg/Griddata/Min_1_Bin.html
7. OGD India / AGMARKNET — Daily mandi market prices: https://data.gov.in/catalog/current-daily-price-various-commodities-various-markets-mandi

## Honest reporting rules

- Clearly distinguish verified source values from proposed inputs and hypothetical examples.
- Do not state that a model is trained until the authentic district panel has been assembled and the code has been run.
- Report the number of districts, years, rows and missing values actually used.
- Keep a final chronological test block untouched during model selection.
- Report MAE, RMSE, R², bias, prediction-interval coverage and subgroup errors.
- If a machine-learning model does not beat the persistence baseline, report that result and do not deploy the more complex model.
