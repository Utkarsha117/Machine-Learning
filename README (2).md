# Employee Data Preprocessing

## Overview
This Jupyter Notebook demonstrates data preprocessing and basic exploratory data analysis (EDA) using an employee dataset. It prepares the data for analysis and machine-learning workflows by checking data quality, handling missing values, standardizing inconsistent categories, exploring relationships, and encoding/scaling features.

## Objectives
- Load and inspect the employee dataset.
- Identify and remove duplicate records.
- Examine columns, data types, unique values, and missing values.
- Clean inconsistent company names.
- Detect potential outliers using the Interquartile Range (IQR) method.
- Impute missing values in selected columns.
- Visualize employee salary, age, and city distributions.
- Encode categorical columns and scale numerical features.

## Technologies and Libraries
- Python
- Jupyter Notebook
- NumPy
- pandas
- Matplotlib
- Seaborn
- scikit-learn

## Dataset
The notebook expects a CSV file named `Employee.csv` with columns such as:
`Company`, `Age`, `Salary`, `Place`, `Country`, and `Gender`.

The original notebook uses a local Windows path:
```python
pd.read_csv(r'C:\Users\Utkarsha\Downloads\Employee.csv')
```
Change this path to wherever your `Employee.csv` file is stored. The dataset is not included in this repository/README.

## Notebook Workflow

1. **Import libraries** — load NumPy, pandas, Matplotlib, and Seaborn.
2. **Load and inspect data** — read the CSV, check dataset shape, preview rows, inspect data types, and review column names.
3. **Remove duplicates** — count duplicate rows, remove them, and reset the index.
4. **Explore values** — inspect unique values and the number of distinct values in columns.
5. **Clean category labels** — standardize company names such as `Infosys Pvt Lmt`, `Tata Consultancy Services`, `CTS`, and `Congnizant`.
6. **Summary statistics** — use descriptive statistics to understand numeric columns.
7. **Check potential outliers** — calculate Q1, Q3, IQR, and lower/upper bounds for `Age` and `Salary`.
8. **Handle missing and invalid values** — fill missing `Company` and `Place` values with the mode; replace zero ages with missing values and impute age and salary using rounded means.
9. **Perform basic analysis** — filter records using age and salary conditions and create a scatter plot of age versus salary and a bar chart of employee counts by city.
10. **Encode categories** — convert gender codes to labels, remove the `Country` column in the notebook, demonstrate one-hot encoding for `Company`, `Place`, and `Gender`, and label-encode `Company` in a sample dataset.
11. **Scale numerical features** — demonstrate `StandardScaler` and `MinMaxScaler`.

## How to Run

1. Install Python 3 and Jupyter Notebook (or use VS Code with the Jupyter extension).
2. Install the required packages:
   ```bash
   pip install numpy pandas matplotlib seaborn scikit-learn jupyter
   ```
3. Place `Employee.csv` in a known location.
4. Open `1_DataPreprocessing(1).ipynb` in Jupyter Notebook or VS Code.
5. Update the `pd.read_csv(...)` path in the notebook to match your file location.
6. Run the cells from top to bottom.

## Output
The notebook produces:
- Dataset previews and data-quality summaries.
- Duplicate and missing-value checks.
- Descriptive statistics and IQR calculations.
- Age-versus-salary scatter plot.
- Employee-count-by-city bar chart.
- Cleaned and encoded data frames.
- Standard-scaled and Min-Max-scaled numerical data.

The notebook also saves a cleaned CSV as `Employee_cleaned.csv` in the current working directory.

## Notes and Limitations
- The notebook uses a local file path, so that path must be updated on another computer.
- The IQR filtering expressions currently use `(value < lower) & (value > upper)`, which cannot be true at the same time for ordinary lower/upper bounds. To identify outliers, use `(value < lower) | (value > upper)`.
- The notebook demonstrates one-hot encoding by manually listing category names. For reliable results, derive output column names from the fitted encoder (for example, `ohe.get_feature_names_out(...)`) so the names always match the generated columns.
- `LabelEncoder` is demonstrated for the `Company` column; for input features, one-hot encoding or `OrdinalEncoder` is often more appropriate depending on the model and data.
- Scaling is demonstrated on selected numeric columns; confirm the selected columns before applying it to a different dataset.

## Author
Add your name, course, or institution here if required.
