# BrewGPT

**BrewGPT** is a Streamlit-based machine learning application for coffee recipe recommendation and comparison. It translates selected taste goals such as **Sweet**, **Caramel**, **Nutty**, **Citrus**, or **Dark.chocolate** into practical brewing settings using a **two-stage machine learning pipeline**.

The system is designed as a **decision-support tool** for coffee personalization. It helps users move from taste preference to recommended brew settings, compare alternative recipes, inspect model quality, and collect brew feedback for future evaluation.

## Table of Contents
- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [How the Recommendation Engine Works](#how-the-recommendation-engine-works)
- [Dataset Requirements](#dataset-requirements)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [How to Run](#how-to-run)
- [Using the App](#using-the-app)
- [Modeling Approach](#modeling-approach)
- [Exports and Logs](#exports-and-logs)
- [Optional Dependencies](#optional-dependencies)
- [Troubleshooting](#troubleshooting)
- [Team](#team)
- [Disclaimer](#disclaimer)

## Project Overview
Coffee preference is highly personal, but brewing decisions are often based on manual trial and error. BrewGPT addresses this by building a machine learning workflow that:

1. accepts selected taste targets and user-defined taste weights,
2. predicts process outcomes from controllable brewing variables,
3. estimates the probability of each taste profile,
4. searches for candidate recipes that best align with the desired sensory outcome, and
5. presents the best recipes in an interactive dashboard.

This project includes:
- a **main recipe app** for generating and comparing recipes,
- a **model comparison app/page** for benchmarking algorithms,
- **local explainability** using **SHAP** (SHapley Additive exPlanations) when available,
- export tools for **CSV** (Comma-Separated Values) and optional **PDF** (Portable Document Format), and
- feedback and experiment logging for iterative quality tracking.

## Key Features

### 1. Single Recipe Generation
Users can:
- choose one or more target taste profiles,
- assign custom taste weights,
- select the machine learning algorithm,
- define optimization settings such as search candidates and penalties,
- apply scenario presets, and
- generate top candidate coffee recipes.

### 2. Recipe Comparison
The app can compare two separate recipe preference setups side by side. This makes it useful for:
- A/B recipe exploration,
- sensory trade-off analysis,
- comparing preference profiles, and
- exporting comparison reports.

### 3. Quality Snapshot
The app includes a model monitoring section with quality indicators such as:
- **AUC** (Area Under the ROC Curve),
- **F1 score**,
- **Brier score**,
- **ECE** (Expected Calibration Error),
- **RMSE** (Root Mean Squared Error),
- **MAE** (Mean Absolute Error), and
- **R²** (Coefficient of Determination).

It also tracks saved experiment runs and user feedback ratings.

### 4. Explainability
When SHAP is installed, the app can provide SHAP-based local explanations for recipe recommendations. If SHAP is not available, the app falls back to a local approximation approach.

### 5. Export and Logging
The app supports:
- exporting recipe outputs to CSV,
- exporting recipe and comparison summaries to PDF if `reportlab` is installed,
- saving experiment runs to a log file, and
- saving brew feedback to a separate feedback log.

### 6. Scenario Presets
The app includes preset flavor scenarios such as:
- Sweet Caramel Comfort
- Bright Citrus Lift
- Dark Chocolate Roast
- Floral Tea Clarity
- Juicy Fruit Burst
- Smooth Nutty Balance
- Roasted Bitter Punch
- Bright Clean Cup
- Chocolate Caramel Dessert
- Body Heavy Sweet
- Low Bitterness Friendly

These presets serve as fast starting points for guided optimization.

## How the Recommendation Engine Works
BrewGPT uses a **two-stage machine learning pipeline**.

### Stage 1: Process Prediction
From controllable brew settings, the app predicts process variables such as:
- `90Sec Temp`
- `Brew Mass`
- `TDS__1`
- `Percent Extraction`

### Stage 2: Taste Probability Prediction
Using the controllable settings plus predicted process values, the app estimates the probability of each taste attribute.

### Optimization
The app then generates many candidate recipes and ranks them using:
- weighted probabilities of selected tastes,
- penalties for non-selected tastes, and
- a distance penalty to keep recommendations close to realistic recipes in the dataset.

This makes the system more practical than simply maximizing one isolated target.

## Dataset Requirements
The app expects a CSV file named:

```text
cotter_dataset.csv
```

It should be placed in the same project root as `app.py`.

### Required columns

#### Taste columns
```text
Tea.floral
Fruit
Citrus
Green.veg
Paper.wood
Burnt
Cereal
Nutty
Dark.chocolate
Caramel
Bitter
Astringent
Roasted
Sour
Thick.viscous
Sweet
Rubber
```

#### Controllable brew columns
```text
Volume
Brew Temperature
Pour Temp
Dose
Grind
```

#### Process columns
```text
90Sec Temp
Brew Mass
TDS__1
Percent Extraction
```

Rows with missing values in required fields are dropped during training.

## Project Structure
A practical project layout for this repository is:

```text
BrewGPT/
├── app.py
├── cotter_dataset.csv
├── logs/
│   ├── experiment_log.csv
│   └── feedback_log.csv
├── pages/
│   └── Model_Comparison.py
├── assets/
│   ├── Altonaga_Lorenzo_V,_Photo-MSDS2026.jpg
│   ├── Sarceda_Jemiry_Royce-MSDS2026.jpg
│   ├── Sunga_Bob_Mathew-MSDS2026.jpg
│   └── Torres__Kendrick_Francis_-_MSDS_2026.jpg
└── README.md
```

## Important note about the comparison page
Your current file is named:

```text
app_model_comparison.py
```

However, the navigation inside `app.py` points to:

```text
pages/Model_Comparison.py
```

So if you want the built-in Streamlit page navigation to work exactly as coded, place the comparison file inside a `pages/` folder and rename it to:

```text
pages/Model_Comparison.py
```

If you keep `app_model_comparison.py` as a separate standalone file, you can still run it independently.

## Installation
Create and activate a virtual environment first.

### Windows PowerShell
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Windows Command Prompt
```bat
python -m venv .venv
.venv\Scripts\activate.bat
```

### macOS / Linux
```bash
python3 -m venv .venv
source .venv/bin/activate
```

Then install the required packages:

```bash
pip install streamlit pandas numpy scikit-learn matplotlib seaborn
```

### Optional packages
Install these for extra features:

```bash
pip install xgboost shap reportlab
```

## How to Run

### Run the main app
```bash
streamlit run app.py
```

### Run the comparison app as a standalone file
```bash
streamlit run app_model_comparison.py
```

### Run as a multipage Streamlit app
If you reorganize the comparison page into `pages/Model_Comparison.py`, then run:

```bash
streamlit run app.py
```

and Streamlit will expose the comparison page through the multipage interface.

## Using the App

### Main tabs in `app.py`
The main application includes these tabs:
- **Single Recipe**
- **Compare Two Recipes**
- **Quality Snapshot**
- **About This Project**
- **Proponents**

### Typical user workflow
1. Select one or more target taste profiles.
2. Assign weights to the selected tastes.
3. Choose an algorithm such as Random Forest, Gradient Boosting, Neural Network, or XGBoost if available.
4. Adjust search settings and recipe constraints.
5. Generate the top candidate recipes.
6. Inspect predicted process outputs and taste probabilities.
7. Review confidence, uncertainty, and explanations.
8. Export the results or submit brew feedback.

## Modeling Approach

### Supported algorithms
- **RandomForest**
- **GradientBoosting**
- **NeuralNetwork**
- **XGBoost** if installed

### Main optimization controls
The app exposes several useful controls:
- **Top recipes to keep**
- **Search candidates**
- **Non-selected taste penalty**
- **Distance penalty**
- **Random seed**
- recipe constraints for volume, brew temperature, pour temperature, dose, and grind

### Quality metrics used
For classification tasks on taste probabilities:
- AUC
- F1
- Brier score
- Expected Calibration Error

For regression tasks on process prediction:
- RMSE
- MAE
- R²

## Exports and Logs
The app automatically uses a `logs/` directory.

### Log files
- `logs/experiment_log.csv`
- `logs/feedback_log.csv`

### What gets saved
**Experiment log** may include information such as:
- timestamp,
- run identifier,
- algorithm,
- selected tastes,
- objective scores, and
- recommendation details.

**Feedback log** may include:
- timestamp,
- recipe details,
- rating,
- optional brew notes.

### Export options
Depending on the installed packages, users can export:
- recipe CSV files,
- comparison CSV files,
- recipe PDF reports,
- comparison PDF reports.

## Optional Dependencies
Some features only activate when certain libraries are installed.

### `xgboost`
Enables the **XGBoost** model option.

### `shap`
Enables SHAP-based local explanations.

### `reportlab`
Enables PDF export for recipe and comparison reports.

If these packages are missing, the app still works, but some options will be hidden or replaced with fallbacks.

## Troubleshooting

### 1. `Could not find dataset: cotter_dataset.csv`
Make sure `cotter_dataset.csv` is in the same folder as `app.py`.

### 2. Comparison page link does not work
Move `app_model_comparison.py` into a `pages/` folder and rename it to `Model_Comparison.py`.

### 3. SHAP explanations are unavailable
Install SHAP:

```bash
pip install shap
```

### 4. PDF download is unavailable
Install ReportLab:

```bash
pip install reportlab
```

### 5. XGBoost does not appear in the algorithm list
Install XGBoost:

```bash
pip install xgboost
```

## Team
This project identifies the following proponents:
- **Lorenzo V. Altonaga**
- **Jemiry Royce Sarceda**
- **Bob Mathew Sunga**
- **Kendrick Francis Torres**

## Disclaimer
BrewGPT is a **decision-support prototype**. Its outputs should be validated with real brewing sessions, tasting panels, and operational testing before full deployment in café or production settings.

The application is best used as a guided experimentation and personalization tool, not as a replacement for sensory expertise.
