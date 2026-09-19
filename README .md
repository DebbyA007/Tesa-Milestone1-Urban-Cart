# UrbanCart, Milestone 1: Predicting Next Month's Customer Spend

## The story so far

UrbanCart is a mid-size online retailer selling home goods and electronics. They have been in business long enough to build up a solid customer database, but they have always made decisions on gut feeling. Lately that has started to hurt them. Some of their best customers quietly stop spending as much, and by the time finance notices, the damage to cash flow and inventory planning is already done.

The finance team put it plainly: *"We keep getting blindsided by how much customers spend month to month. Some of our best customers go quiet for no obvious reason, and we cannot plan inventory or cash flow on guesswork anymore. Can you help us see this coming?"*

That is why UrbanCart brought in a freelance machine learning engineer. This repository is that work.

The finance team handed over a spreadsheet of about 20,000 past customers. For each one, we know how long they have been active, what their average order looks like, how many orders they placed last quarter, and which region they are in. We also know what they actually went on to spend the following month, `NextMonthSpend`, since this is historical data. That last column is the number we are trying to predict for future customers, where we will not have the luxury of already knowing the answer.

## What's in this repository

| File | What it is |
|---|---|
| `milestone-1-customer-spend.ipynb` | The full analysis and modeling notebook |
| `milestone-1-customer-spend.csv` | The dataset, about 20,000 historical customers |
| `README.md` | This file |

## How to run it

1. Keep `milestone-1-customer-spend.ipynb` and `milestone-1-customer-spend.csv` in the same folder. The notebook loads the CSV by filename alone, so they need to sit side by side.
2. Open the folder in VS Code, or any editor with Jupyter support.
3. Install the required packages if you don't already have them:
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn
   ```
4. Open the notebook, select a Python kernel, and run all cells from top to bottom.

## How the notebook is organized

The notebook tells the whole story in order, and solves the problem twice, fully and independently, before ever comparing the two attempts.

| # | Section | What happens |
|---|---|---|
| 1 | Setup | Import the libraries needed |
| 2 | Load and inspect | First look at the data |
| 3 | Data quality checks | Missing values, duplicates, impossible values |
| 4 | Outlier check | Look, understand, and keep them |
| 5 | Exploratory analysis | Charts to understand relationships |
| 6 | Feature engineering | Build new features from what we know about the business |
| 7 | Choosing our evaluation metrics | Decide how we will judge success, before we see any results |
| 8 | Feature and target split | Baseline features versus engineered features |
| 9 | Encoding categorical columns | Turn text into numbers, safely |
| 10 | Three way split | Train, validation, and test, done before any fitting |
| 11 | Model 1, Linear Regression | Choose the best setup on validation data, then check it once on the test set |
| 12 | Model 2, Random Forest | Same process, tuned and checked independently |
| 13 | Head to head comparison | Both models' test set results, side by side |
| 14 | Recommendation | The final call, backed by evidence, with an honest tradeoff |

A few decisions worth knowing about before reading the notebook itself:

- No missing values, duplicates, or impossible numbers turned up in Section 3, so the dataset is used as is.
- Outliers are found and charted in Section 4, but never removed. The unusually high or low spenders are exactly the customers the finance team is worried about.
- Section 6 builds three new features, `OrdersPerMonthActive`, `ImpliedQuarterSpend`, and `TenureBucket`, and none of them are assumed to help. Section 11.1 and Section 12.1 test that assumption honestly on the validation set for each model separately.
- Section 10 splits the data three ways rather than two: about 70% for training, 15% for validation, and 15% for a test set that is touched exactly once, at the very end, after every decision has already been made.
- Section 12.2 tunes Random Forest's `max_depth` setting across four options (4, 8, 12, and unrestricted), scored only on the validation set, rather than picking a depth by guesswork.

## The result

Linear Regression, trained on the engineered feature set, is the recommended model. On the held-out test set it scored an MAE of about $9.66 against Random Forest's $9.95, and it also won on RMSE and R². That same ranking already showed up on the validation set before the test set was ever touched, which makes the result more convincing than a single comparison would be.

Random Forest was given a genuinely fair chance, the same engineered features and a real search over tree depth, but it did not out-predict the simpler model here. The notebook's own explanation: the relationship between these features and next month's spend is close to a straight line already, so Random Forest's ability to bend around curves had little to actually grab onto. On top of that, Linear Regression's coefficients can be read out loud and explained to the finance team directly, something Random Forest's feature importances cannot do in the same way.

Full reasoning, charts, and every number behind these conclusions are in `milestone-1-customer-spend.ipynb`.
