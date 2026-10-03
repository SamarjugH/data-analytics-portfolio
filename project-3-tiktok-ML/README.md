# TikTok Claim vs. Opinion Classification

## Project Overview

This project develops machine learning models to classify TikTok videos as either **claims** or **opinions** using metadata, transcription length, creator status, and engagement features.

The project focuses on two different modeling scenarios:

- **Model A:** A performance benchmark that includes post-publication engagement features.
- **Model B:** An early-classification model that removes engagement features and uses information available closer to the time of upload.

The intended use is to **prioritize videos for human review**, not to replace human moderation.

---

## Objectives

- Explore and clean the TikTok dataset.
- Engineer a text-length feature from video transcriptions.
- Prepare numerical and categorical variables for machine learning.
- Compare **Random Forest** and **XGBoost** classifiers.
- Tune model hyperparameters using 5-fold cross-validation.
- Evaluate models using accuracy, precision, recall, and F1-score.
- Examine feature importance.
- Investigate the effect of removing post-publication engagement features.
- Identify limitations related to feature availability and potential target leakage.

---

## Dataset

The dataset contains TikTok video information used to predict the `claim_status` target.

### Original dataset

- **Rows:** 19,382
- **Columns:** 12

### After removing missing values

- **Rows:** 19,084
- **Target classes:**
  - Claim: 9,608
  - Opinion: 9,476

The classes are relatively balanced, which supports the use of standard classification metrics without requiring class rebalancing.

### Main variables

Examples of the variables used include:

- `video_duration_sec`
- `video_view_count`
- `video_like_count`
- `video_share_count`
- `video_download_count`
- `video_comment_count`
- `video_transcription_text`
- `verified_status`
- `author_ban_status`
- `claim_status`

A new `text_length` feature is created from `video_transcription_text`.

---

## Data Preparation

The notebook:

1. Checks the dataset shape and missing values.
2. Removes rows containing missing values.
3. Reviews duplicate rows.
4. Examines descriptive statistics.
5. Reviews engagement-variable outliers using the IQR rule.
6. Creates the `text_length` feature.
7. Encodes the target:
   - `claim` → `1`
   - `opinion` → `0`
8. One-hot encodes categorical variables.
9. Removes identifiers and raw transcription text from the model features.

Engagement outliers were reviewed rather than automatically removed because unusually high engagement can represent legitimate and informative TikTok videos.

---

## Modeling Approach

### Model A — Random Forest with Engagement Features

Model A includes post-publication engagement variables such as:

- Views
- Likes
- Shares
- Downloads
- Comments

These variables provide strong predictive information, but they may not be available immediately when a video is uploaded.

Random Forest was tuned using:

- 5-fold cross-validation
- GridSearchCV
- Accuracy
- Precision
- Recall
- F1-score

Recall was used as the refit metric because missing a claim may reduce the effectiveness of a review-prioritization workflow.

### Model B — Early Classification

Model B removes all post-publication engagement variables:

- `video_view_count`
- `video_like_count`
- `video_share_count`
- `video_download_count`
- `video_comment_count`

It uses information that is more realistic for early classification:

- `video_duration_sec`
- `text_length`
- `author_ban_status`
- `verified_status`

This model provides a more realistic benchmark for a workflow where predictions need to be made soon after upload.

### XGBoost

XGBoost was also tuned using 5-fold cross-validation and evaluated using the same main classification metrics.

---

## Model Results

### Cross-Validation Results

The recorded cross-validation results from the original run were:

| Model | CV Recall | CV Precision |
|---|---:|---:|
| Random Forest | 98.99% | 100.00% |
| XGBoost | 98.87% | 99.79% |

These results show very high performance for both models when engagement features are included.

### Early-Classification Model

Model B achieved approximately:

- **Accuracy:** 67%
- **Precision:** approximately 67%
- **Recall:** approximately 69%
- **F1-score:** approximately 68%

This substantial performance reduction demonstrates the importance of post-publication engagement features in the original dataset.

### Final Test Evaluation

The cleaned notebook evaluates the final Random Forest model on the independent test set after model selection.

The final test metrics are intentionally generated when the cleaned notebook is run from start to finish rather than being manually entered into the README.

---

## Feature Importance

The original Random Forest model identified engagement variables as the strongest predictors.

The recorded feature importance results were led by:

| Feature | Importance |
|---|---:|
| `video_view_count` | 0.6055 |
| `video_like_count` | 0.2460 |
| `video_share_count` | 0.0695 |
| `video_download_count` | 0.0605 |
| `video_comment_count` | 0.0143 |
| `text_length` | 0.0019 |

This indicates that engagement metrics explain much of the predictive performance of the engagement-based model.

---

## Key Findings

1. The dataset contains **19,084 complete observations** after missing-value removal.
2. Claim and opinion classes are relatively balanced.
3. Random Forest and XGBoost achieve very high cross-validation performance when engagement features are included.
4. Engagement variables are the dominant predictors in the original model.
5. Removing engagement variables reduces performance substantially, with the early-classification model achieving approximately 67% holdout accuracy.
6. Feature availability is therefore an important consideration when interpreting model performance.

---

## Limitations

### Post-publication engagement

Views, likes, shares, downloads, and comments can accumulate after a video is published. A model that relies heavily on these variables may not be suitable for immediate classification at upload time.

### Generalization

The reported results are based on this dataset and its train/validation/test splits. Performance should be tested on newer and independently collected data before being considered for operational use.

### Human oversight

The model is intended to support **human review prioritization**. Predictions should not automatically determine moderation decisions.

### Fairness and monitoring

Future work should evaluate performance across different creator and content groups and monitor whether model performance changes over time.

---

## Project Structure

```text
project-2-tiktok-analysis/
│
├── README.md
├── TikTok_Claim_vs_Opinion_ML_FINAL.ipynb
├── tiktok_dataset.csv
└── images/
    ├── model_b_confusion_matrix.png
    └── model_b_feature_importances.png
```

The cleaned notebook automatically creates the `images/` directory when it is run.

---

## Technologies

- Python
- Pandas
- Matplotlib
- Seaborn
- Scikit-learn
- XGBoost
- Jupyter Notebook

### Machine Learning Techniques

- Feature engineering
- One-hot encoding
- Stratified train/validation/test splitting
- Random Forest
- XGBoost
- GridSearchCV
- 5-fold cross-validation
- Confusion matrices
- Classification reports
- Feature importance analysis

---

## How to Run

### 1. Clone or download the project

Place the notebook and dataset in the same project folder.

### 2. Install dependencies

```bash
pip install pandas matplotlib seaborn scikit-learn xgboost jupyter
```

### 3. Add the dataset

Make sure the dataset is named:

```text
tiktok_dataset.csv
```

and is located in the same folder as the notebook.

### 4. Run the notebook

Open:

```text
TikTok_Claim_vs_Opinion_ML_FINAL.ipynb
```

and run all cells from beginning to end.

The notebook will generate:

- Model evaluation metrics
- Confusion matrices
- Feature-importance visualizations
- Model comparison results

---

## Conclusion

This project demonstrates an end-to-end machine learning workflow for classifying TikTok videos as claims or opinions. Random Forest and XGBoost achieved very high cross-validation performance when post-publication engagement variables were included, while the early-classification model showed that performance decreases when those variables are removed.

The comparison highlights an important practical consideration: **a highly accurate model is not necessarily a practical real-time model if its strongest features are only available after publication**.

For a real moderation workflow, machine learning predictions should support—not replace—human review. Future work should test the models on newer data, evaluate performance across different content and creator groups, and determine which features are reliably available at the intended prediction time.

---

## Reproducibility

The cleaned notebook is designed to be run from the first cell to the last cell. It creates the required `images/` directory automatically and generates model metrics and visualizations directly from the analysis code.
