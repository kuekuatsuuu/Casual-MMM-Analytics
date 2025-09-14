# Causal Marketing Mix Model for Revenue Optimization

This project analyzes two years of weekly marketing data to build a robust Marketing Mix Model (MMM). The primary goal is to understand the key drivers of revenue and to investigate the causal hypothesis that social media spend influences revenue indirectly through Google Search.

---

## 1. Data Preparation and Feature Engineering

The raw dataset was clean and complete. The preparation phase focused on creating a feature set that could capture the underlying business dynamics.

* **Seasonality and Trend:** To capture time-based patterns, the `week` column was engineered into `month`, `week_of_year`, and a `time_index`. The model's later reliance on `week_of_year` confirmed the importance of this step.
* **Zero-Spend Periods:** The numerous zero-spend periods in the data were handled implicitly through the choice of a Random Forest model, which can natively learn rules from zero-valued data without special preprocessing.
* **Feature Scaling:** No feature scaling was applied. This was an intentional choice as the final tree-based model is scale-invariant, making this step unnecessary.

---

## 2. Modeling Approach and Validation

A rigorous, multi-step modeling approach was taken to ensure the final model was both accurate and interpretable.

* **Model Selection:** An initial **Linear Regression** model served as a baseline but failed to capture the data's complexity (yielding a negative R-squared). A **Random Forest Regressor** was then chosen for its ability to model complex, non-linear relationships and interactions.
* **Hyperparameters:** The model used `n_estimators=100` as a strong default and `random_state=42` to ensure full reproducibility of the results.
* **Validation Plan:** A **chronological 80/20 train-test split** was used. This is essential for time-series data to prevent look-ahead bias and to ensure the model is fairly evaluated on its ability to predict future, unseen data.

---

## 3. Causal Framing: The Two-Stage Model

To test the hypothesis that Google acts as a mediator for social media spend, a **two-stage modeling process** was implemented to isolate the causal pathway: `Socials -> Google -> Revenue`.

1.  **Stage 1 (Mediator Model):** A model was trained to predict `google_spend` using *only* the spend from other social media channels.
2.  **Stage 2 (Revenue Model):** The `predicted_google_spend` from Stage 1 was then used as a feature in the main revenue model, while the *actual* `google_spend` was excluded to prevent data leakage.

This structure allows us to measure the effect of social spend that flows *through* the Google channel.

---

## 4. Model Diagnostics and Performance

The final Random Forest model demonstrated strong predictive power on the out-of-sample test set.

* **Out-of-Sample Performance:**
    * **R-squared:** 0.82 (The model explains 82% of the variance in future revenue).
    * **Mean Absolute Error (MAE):** ~$13,048.

The plot below shows how closely the model's predictions on the unseen test data tracked the actual revenue.



---

## 5. Insights & Strategic Recommendations

The model's feature importance scores were used to derive the following strategic insights.



### Key Insights:

* **Primary Driver:** `instagram_spend` is the most important marketing channel for driving revenue.
* **Critical Levers:** Revenue is highly sensitive to `average_price` and `week_of_year`, indicating that pricing strategy and seasonality are critical business levers.
* **Nuanced Causal Path:** The model assigned a low importance to the `predicted_google_spend` feature, suggesting the *direct* impact of Instagram is a much stronger driver than its *mediated* impact through Google Search for this business.

### Final Recommendation:

The business should prioritize an integrated strategy that **optimizes Instagram spend during key seasonal weeks** and aligns it with a **strategic promotional pricing schedule** to maximize revenue.

---

## How to Run This Project

1.  **Libraries:** This project requires Python with the following libraries: `pandas`, `scikit-learn`, `seaborn`, `matplotlib`.
2.  **Execution:** Place the `MMM.csv` file in the same directory as the notebook. Open the notebook in a compatible environment (like Google Colab or Jupyter) and run the cells from top to bottom.