📈 Stock Return Prediction with Machine Learning

Can Machine Learning Predict the Stock Market?

This project explores whether machine learning models can predict next-day stock returns using historical market data.

Rather than focusing only on prediction accuracy, this work takes a quantitative finance approach — evaluating whether model outputs can translate into actionable trading signals.

The project walks through the full pipeline:

- Feature engineering from raw OHLCV data
- Model development (Linear, Ridge, XGBoost, LSTM)
- Proper time-series validation
- Strategy-based evaluation

👉 The key question: Can these models outperform simple baselines in a realistic setting?

🎯 Objective

The goal of this project is to predict the next-day logarithmic return of a stock using historical market data.

Formally, the target variable is defined as:

rₜ₊₁ = log(Pₜ₊₁ / Pₜ)

Where:

- Pₜ is the closing price at time t
- Pₜ₊₁ is the closing price at time t+1

To align features with the prediction target, the return is shifted:

targetₜ = rₜ₊₁

This ensures that all input features at time t are used strictly to predict future returns, avoiding lookahead bias.

---

⚙️ Problem Framing

This is treated as a supervised regression problem, where:

- Inputs (X): Engineered features derived from historical OHLCV data
- Output (y): Next-day log return

However, beyond predicting magnitude, we also evaluate the model’s ability to correctly predict direction, which is critical for trading applications.

🧠 Feature Engineering

Raw OHLCV data contains limited predictive signal on its own. To improve model performance, we engineer features that capture market behavior patterns such as momentum, trend, and volatility.

---

📊 Momentum Features

Momentum reflects the tendency of prices to continue moving in the same direction.

- Lagged returns (e.g., rₜ, rₜ₋₁, rₜ₋₂)
- Captures short-term continuation patterns

---

📈 Trend Features

Trend indicators smooth out noise and highlight the underlying direction of price movement.

- Rolling mean of returns or prices
- Moving averages over different windows

---

📉 Volatility Features

Volatility measures the level of uncertainty or risk in the market.

- Rolling standard deviation of returns
- Helps identify periods of high vs low market instability

---

📦 Volume-Based Features

Volume provides insight into market participation and conviction.

- Percentage change in volume
- Volume spikes as potential signals of strong moves

---

⚠️ Feature Design Principles

- All features are constructed using information available up to time t only
- No future data is used, preventing lookahead bias
- Rolling calculations are carefully aligned to maintain temporal integrity

---

💡 Why This Matters

In financial modeling, feature engineering often matters more than model choice.

Well-designed features can:

- Improve signal extraction
- Reduce noise impact
- Enable simpler models to perform competitively with complex ones

🏗️ Data Splitting & Preprocessing

⏳ Time-Series Train/Test Split

Since this is a time-series problem, we avoid random shuffling and instead split the data chronologically:

- Training set: Earlier portion of the data
- Test set: Most recent observations

This preserves the natural temporal order and ensures that the model is evaluated on unseen future data, mimicking real-world deployment.

---

⚠️ Avoiding Data Leakage

To maintain the integrity of the evaluation:

- All features are constructed using information available up to time t
- The target variable is shifted to represent future returns
- No future information is used during training

---

⚙️ Feature Scaling

Feature scaling is applied where appropriate:

- Models like Linear Regression, Ridge, and LSTM benefit from scaled inputs
- Tree-based models such as XGBoost are scale-invariant and do not strictly require scaling

To prevent leakage:

- The scaler is fit only on the training data
- The same transformation is applied to the test set

---

🧪 Why This Matters

Improper data splitting or preprocessing can lead to overly optimistic results that fail in real-world scenarios.

By enforcing strict time-ordering and eliminating leakage, we ensure that model performance reflects true predictive capability, not artifacts of the data.


🏗️ Modeling Approach

To evaluate different modeling strategies, we implement a range of models spanning linear, tree-based, and deep learning approaches.

---

📐 Linear Regression (Baseline Model)

A simple linear model is used as an initial benchmark.

- Assumes a linear relationship between features and returns
- Highly interpretable
- Serves as a reference point for more complex models

---

🧮 Ridge Regression (Regularized Linear Model)

Ridge regression extends linear regression by adding L2 regularization.

- Helps reduce overfitting
- Stabilizes coefficients in the presence of noisy financial data
- Often performs better than plain linear models in practice

---

🌳 XGBoost (Tree-Based Model)

A powerful gradient boosting model designed for tabular data.

- Captures non-linear relationships
- Handles feature interactions automatically
- Typically performs strongly on structured datasets

---

🔁 LSTM (Long Short-Term Memory Network)

A recurrent neural network designed for sequential data.

- Captures temporal dependencies across multiple time steps
- Suitable for modeling patterns in financial time series
- Requires properly structured input sequences

---

⚖️ Baseline Comparisons

To ensure meaningful evaluation, model performance is compared against simple baselines:

- Zero Return Model: Assumes no price movement
- Naïve Momentum Model: Uses the previous return as the prediction

These baselines help answer a critical question:

«Are the models actually learning signal, or just fitting noise?»

---

🧪 Why Multiple Models?

Financial data is inherently noisy and complex. Different models capture different aspects:

- Linear models → simple, interpretable relationships
- Tree models → non-linear interactions
- Deep learning → temporal structure

Comparing them provides a more complete understanding of what works—and what doesn’t.

📏 Evaluation & Trading Strategy

Traditional regression metrics alone are not sufficient for financial applications. A model can have low error but still be useless for decision-making.

To address this, we evaluate models across three dimensions:

---

📉 1. Regression Performance

We measure prediction accuracy using:

- RMSE (Root Mean Squared Error)
  Captures the average magnitude of prediction errors

While useful, RMSE does not indicate whether predictions are directionally correct.

---

🔄 2. Directional Accuracy ⭐

In trading, correctly predicting the direction of returns is often more important than exact magnitude.

Directional accuracy is defined as:

- Percentage of times:
  
  sign(predicted return) = sign(actual return)

This provides a more practical measure of model usefulness.

---

💰 3. Trading Strategy Simulation

To evaluate real-world applicability, we simulate a simple trading strategy:

- Go long if predicted return > 0
- Go short if predicted return < 0

Strategy returns are computed as:

- strategy_returnₜ = sign(predictionₜ) × actual_returnₜ

We then analyze:

- Cumulative returns over time
- Performance relative to a passive benchmark

---

📊 Why This Matters

A model is only valuable if it leads to better decisions.

By combining:

- Statistical accuracy (RMSE)
- Directional performance
- Strategy-based evaluation

we ensure that results reflect practical financial usefulness, not just mathematical fit.

📊 Results & Visualizations

📋 Model Performance Summary

Model| RMSE| Direction Accuracy| Key Insight
Baseline (Zero Return)| —| ~50%| Random benchmark
Naïve Momentum| —| —| Captures short-term persistence
Linear Regression| —| —| Limited by linear assumptions
Ridge Regression| —| —| Improved stability over linear
XGBoost| —| —| Strong performance on tabular data
LSTM| —| —| Depends on sequence quality

Note: Replace placeholders with actual results from the notebook.

---

📈 Predicted vs Actual Returns

This plot compares model predictions against true returns.

- Highlights the noise-dominated nature of financial data
- Even strong models show wide dispersion

"Predicted vs Actual" (images/pred_vs_actual.png)

---

📉 Cumulative Strategy Returns

We evaluate model outputs in a trading context by simulating cumulative returns.

- Strategy based on model predictions
- Compared against a passive benchmark

"Cumulative Returns" (images/cumulative_returns.png)

---

🌳 Feature Importance (XGBoost)

Understanding which features drive predictions:

- Identifies dominant signals
- Provides interpretability in an otherwise complex system

"Feature Importance" (images/feature_importance.png)

---

🔍 Key Observations

- Predicting exact return magnitude is highly challenging
- Some models show improved directional accuracy over baseline
- Tree-based models capture non-linear relationships effectively
- Strategy performance depends heavily on signal consistency, not just accuracy








