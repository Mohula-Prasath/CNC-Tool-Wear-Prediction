# CNC Tool Wear Prediction Using Machine Learning (Learning & Prototype Project)

This project explores CNC tool wear prediction using machine learning with a focus on 
feature engineering and time-aware modeling. While sensor signals such as vibration and 
acoustic emission are included, the model’s performance is strongly influenced by a 
manually engineered pseudo-time feature representing tool usage progression.

The project is intended as a learning-oriented implementation of predictive
maintenance rather than a full industrial deployment.

---

## Why this project?

In CNC machining, tools gradually wear out due to continuous cutting. Excessive
tool wear can lead to poor surface quality, dimensional errors, and unexpected
machine downtime.

In many shops, tools are replaced based on fixed schedules or manual inspection.
This project explores whether sensor data can be used to *estimate tool wear
automatically* and support better maintenance decisions.

---

## Data Description

- Sensor signals include vibration and acoustic emission measurements.
- Tool wear is measured as flank wear (`VB_mm`) in millimeters.
- A wear class label is also available:
  - `0` → Healthy  
  - `1` → Moderate  
  - `2` → Worn  

The data is treated as a time series collected during repeated machining
operations using the same tool.

---

## Approach

### 1. Window-based Feature Extraction

Raw sensor signals are segmented into fixed-length windows. For each window,
statistical features are extracted, including:

- Mean, standard deviation
- RMS, minimum, maximum
- Kurtosis
- Frequency-domain features using FFT

This converts raw time-series data into structured inputs suitable for
regression models.

---

### 2. Baseline Method (Non-overlapping Windows)

- Sensor data is divided into non-overlapping windows.
- Features are extracted once per window.
- A linear regression model is trained to predict average tool wear per window.
- Performance is evaluated using time-aware cross-validation.

This serves as a simple baseline for comparison.

---

### 3. Proposed Method (Sliding Windows)

- Overlapping windows (50% overlap) are used to better capture gradual wear
  progression.
- Highly correlated features are automatically selected.
- A `Window_ID` feature is added to represent the sequential progression of
  tool usage.
- Linear regression is trained and evaluated using `TimeSeriesSplit`.

This approach improves temporal sensitivity and stability of predictions.

---

### Window_ID (Pseudo-Time Feature)
Window_ID is a manually engineered feature that represents the sequential progression of 
tool usage. Since tool wear generally increases monotonically with machining time, this 
feature strongly correlates with wear progression.

This results in high prediction accuracy but also means:
- The model relies heavily on temporal progression rather than sensor signals alone
- Performance may not generalize well to unseen tools or real-time sensor-only scenarios

This design choice was intentional for learning purposes and helped demonstrate the 
impact of feature dominance in machine learning models.

---

## Evaluation

Model performance is evaluated using:

- **R² score**
- **RMSE**

Time-series cross-validation is used to avoid data leakage. Results show that
the sliding window approach performs significantly better than the baseline
method for this dataset.

The improvement is primarily driven by the inclusion of the Window_ID feature, highlighting 
how strong temporal proxies can dominate model predictions.

---

## Real-time Monitoring Simulation

A simple simulation demonstrates how predicted tool wear can be used to trigger
actions:

- **OK** → Continue machining  
- **WARNING** → Schedule tool change  
- **CRITICAL FAILURE** → Stop machine  

A predefined wear threshold is used to classify the tool state.

---

## Limitations

- The approach assumes repeated machining operations with similar conditions.
- Performance may degrade for complex, one-off machining tasks.
- The dataset is relatively small.
- Linear regression may not capture highly non-linear wear behavior.

---

## Future Improvements

- Try non-linear models such as Random Forest or XGBoost.
- Use deep learning models (LSTM/GRU) for raw signal modeling.
- Include multiple tools and materials for better generalization.
- Integrate with live sensor streams for real-time deployment.

---

## Conclusion

This project demonstrates a simple and practical workflow for CNC tool wear
prediction using machine learning. While not production-ready, it provides a
solid foundation for understanding predictive maintenance concepts and time-
series modeling in manufacturing systems.

This repository is intended for educational and prototyping purposes.
