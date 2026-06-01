# Titanic Survival Prediction

## Project Overview

This project uses the Titanic dataset to build and evaluate machine learning models that predict whether a passenger survived or did not survive. The target variable is binary:

- `1` = Survived
- `0` = Did not survive

The main goal is to compare several classification models and select the most reliable model based on performance, especially the **F1-score for the survived class**.

## Team

**Group Name:** The Outliers

| Member | Main Task |
|---|---|
| Thanh Hoang | Data exploration and transformation |
| Nhi Nguyen | KNN, Decision Tree, Naïve Bayes |
| Davis Nguyen | Linear Regression, Logistic Regression, SVM |

## Dataset Understanding

The dataset contains passenger-level information from the Titanic, including:

- Demographic information: `Age`, `Sex`
- Socioeconomic information: `Pclass`, `Fare`
- Family-related information: `SibSp`, `Parch`
- Travel information: `Embarked`
- Target variable: `Survived`

During the exploratory analysis, missing values were found in `Age`, `Cabin`, and `Embarked`. The analysis also showed that some features had clear relationships with survival. In particular, female passengers and passengers in higher classes had higher survival rates.

## Data Preprocessing

Several preprocessing steps were completed before model training:

### 1. Handling Missing Values

- Missing `Age` values were filled using the median.
- Missing `Embarked` values were filled using the mode.
- `Cabin` was dropped because it had too many missing values and limited predictive usefulness.

### 2. Feature Selection

Columns with limited predictive value or data quality issues were removed:

- `Name`
- `PassengerId`
- `Ticket`
- `Cabin`

The remaining features were used to train the models.

### 3. Encoding Categorical Variables

Categorical features were converted into numerical format:

- `Sex` and `Embarked` were encoded using one-hot encoding.
- `Pclass` was encoded as an ordinal feature because passenger class has a ranked meaning.

### 4. Train, Validation, and Test Split

The dataset was split into:

- 70% training set
- 15% validation set
- 15% testing set

### 5. Feature Scaling

Min-max scaling was applied to numerical features that were not one-hot encoded.

## Models Used

The following models were trained and evaluated:

1. K-Nearest Neighbors
2. Decision Tree
3. Linear Regression as a baseline classification approach
4. Naïve Bayes
5. Logistic Regression
6. Support Vector Machine

Some models used `GridSearchCV` for hyperparameter tuning, and several models used threshold optimization to improve the F1-score for the positive class.

## Model Performance

The project used **F1-score for class 1**, meaning the survived class, as the main evaluation metric. This metric was chosen because it balances precision and recall and focuses on how well the model identifies survivors.

| Model | Main Tuning / Method | F1-Score for Survived Class |
|---|---|---:|
| K-Nearest Neighbors | Best K = 3 | 0.727 |
| Decision Tree | Best max depth = 3 | 0.785 |
| Linear Regression | Threshold = 0.5 | 0.748 |
| Naïve Bayes | Optimized threshold = 0.462 | 0.724 |
| Logistic Regression | Best C = 10, optimized threshold = 0.588 | 0.750 |
| Support Vector Machine | C = 0.1, RBF kernel, optimized threshold = 0.175 | 0.730 |

## Final Model Selection

The **Decision Tree model** was selected as the final model because it achieved the highest F1-score of **0.785** for the survived class. This means it provided the best balance between precision and recall compared with the other models.

## Key Findings

The analysis showed that survival was strongly influenced by several important features:

- **Sex:** Female passengers had a much higher survival rate.
- **Passenger class:** Passengers in higher classes had better survival outcomes.
- **Fare:** Higher fare values were associated with better survival chances.
- **Age and family-related variables:** Features such as `Age`, `SibSp`, and `Parch` also helped the models separate survivors from non-survivors.

These patterns show that demographic and socioeconomic factors played an important role in survival outcomes.

## Conclusion

This project successfully developed and compared multiple machine learning models for Titanic survival prediction. After cleaning the data, transforming features, tuning models, and evaluating performance, the Decision Tree model was chosen as the best-performing model.

Overall, the project demonstrates the full machine learning workflow, including:

- Data exploration
- Missing value treatment
- Feature selection
- Encoding and scaling
- Model training
- Hyperparameter tuning
- Model evaluation and selection

The final result suggests that a simple and interpretable model such as Decision Tree can perform strongly on this dataset when the data is properly prepared.

## Tools and Libraries

This project was completed using Python and common data science libraries, including:

- pandas
- numpy
- matplotlib
- seaborn
- scikit-learn

## Repository Structure

```text
titanic-survival-prediction/
│
├── README.md
├── Titanic_Report.pdf
├── notebooks/
│   └── Titanic_Project.ipynb
└── data/
    └── titanic.csv
```

## Future Improvements

Possible future improvements include:

- Testing more advanced models such as Random Forest, Gradient Boosting, or XGBoost.
- Adding feature engineering, such as family size or title extraction from passenger names.
- Comparing model performance using additional metrics such as ROC-AUC and accuracy.
- Creating visual dashboards to communicate survival patterns more clearly.
