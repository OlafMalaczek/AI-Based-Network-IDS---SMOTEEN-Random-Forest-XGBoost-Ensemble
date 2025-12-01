# AI-Based-Network-IDS---SMOTEEN-Random-Forest-XGBoost-Ensemble
Welcome to this cohesive repository of an Network Intrusion Detection System. This AI NIDS uses SMOTEENN, Random Forest, and XGBoost in a soft-voting ensemble. It detects DDoS, PortScan, and WebAttacks, handling imbalanced data, and provides per-class ROC/PR curves, confusion matrices, feature importances, and metrics like balanced accuracy and MCC

## Introduction
As cyber threats continue to grow in sophistication, effective network security has become essential. Conventional intrusion detection systems (IDS) often fall short in identifying complex and evolving attacks. AI-NIDS tackles this challenge by combining Machine Learning techniques, including SMOTEENN, Random Forest, XGBoost, and a soft-voting ensemble, to accurately detect and classify network intrusions.

This system is trained on real-world datasets covering DDoS, PortScan, and WebAttack traffic. By addressing class imbalances and leveraging ensemble learning, AI-NIDS can reliably identify both known and emerging threats, providing a scalable and adaptive solution for modern network security.

## Dataset Infomation
For this research, the **CIC-IDS2017 dataset** was selected due to its **realistic and comprehensive network traffic captures**, reflecting contemporary attack behaviors. Unlike older IDS datasets, CIC-IDS2017 includes benign traffic alongside modern attacks, captured in a live network environment during standard working hours.

The specific datasets used in this project are:

- **Friday-WorkingHours-Afternoon-DDos:** Captures multiple types of Distributed Denial-of-Service (DDoS) attacks, including UDP, HTTP, and ICMP floods. These attacks target network availability and produce high-volume traffic patterns, making detection challenging.  
- **Friday-WorkingHours-Afternoon-PortScan:** Contains port scanning activity, a reconnaissance technique where attackers probe open ports to identify potential vulnerabilities. This dataset emphasizes subtle traffic anomalies rather than volume-based attacks.  
- **Thursday-WorkingHours-Morning-WebAttacks:** Represents application-layer attacks such as SQL injection and cross-site scripting. These attacks are more difficult to detect due to their low-volume, targeted nature and similarity to benign web traffic.

Each dataset includes **flow-level features** such as:

- Packet counts and sizes (total, forward, backward)  
- Flow duration and timestamps  
- Protocol information and flags  
- Statistical measures (mean, standard deviation, min, max) of inter-arrival times and payload sizes  

To prepare the datasets for AI modeling:

1. **Feature cleaning and preprocessing:** Infinite values replaced, missing values filled with zeros.  
2. **Encoding categorical features:** One-hot encoding for protocol types and services.  
3. **Scaling:** StandardScaler applied for uniform feature ranges.  
4. **Balancing:** SMOTEENN used to address class imbalance, creating a dataset suitable for training high-accuracy models.

The richness of CIC-IDS2017 allows for **detailed performance evaluation** with per-class precision/recall, ROC and reliability curves, feature importance, confusion matrices, and metrics like balanced accuracy, MCC, and Brier score. This ensures the AI NIDS not only performs well overall but also for rare or subtle attack types.

---

## Why Not UNSW-NB15 or NSL-KDD

While **UNSW-NB15** and **NSL-KDD** are popular in IDS research, they were not selected for this project due to several limitations:

- **NSL-KDD:**  
  - Derived from the KDD’99 dataset, which is over two decades old.  
  - Contains limited attack types that are no longer representative of current threats (e.g., DOS attacks are simpler than modern variants).  
  - Lacks modern network traffic patterns and application-layer attack scenarios, reducing relevance for realistic AI model evaluation.

- **UNSW-NB15:**  
  - Although more recent than NSL-KDD, it contains a mix of real and synthetic traffic generated in a controlled lab environment.  
  - Certain attack behaviors are over-simplified and do not fully reflect concurrent, multi-vector attacks seen in real networks.  
  - Feature sets differ from CIC-IDS2017, making it less compatible with ensemble methods, SMOTEENN balancing, and the per-class evaluation metrics implemented in this research.

In contrast, **CIC-IDS2017 provides a highly realistic, modern, and comprehensive dataset** capturing multiple attack types during live network operation hours. Its **rich flow-level features, diversity of attack vectors, and realistic benign traffic** make it ideal for developing and validating AI-based intrusion detection models that need to generalize to real-world network environments.

## Methodology

The development of this AI-based Network Intrusion Detection System (NIDS) was carried out in a structured and chronological manner to ensure reproducibility, robustness, and strong predictive performance. The methodology can be summarized as follows:

### 1. Data Collection and Preparation
- **Loading datasets:** Three CSV files from CIC-IDS2017 were used:  
  - `Friday-WorkingHours-Afternoon-DDos`  
  - `Friday-WorkingHours-Afternoon-PortScan`  
  - `Thursday-WorkingHours-Morning-WebAttacks`  
- **Cleaning:** Extra spaces from column names were removed, and any existing `label` columns were dropped to prevent conflicts.  
- **Label assignment:** Each dataset was annotated with a clear target label (`DDos`, `PortScan`, or `WebAttack`).  
- **Combining datasets:** The three datasets were merged into a single unified dataframe, creating a comprehensive dataset representing multiple attack types.

### 2. Preprocessing
- **Standardization:** Columns were converted to lowercase for consistency.  
- **Handling missing and infinite values:** `NaN`, `inf`, and `-inf` values were replaced with zeros to avoid errors during training.  
- **Encoding categorical features:** Any non-numeric columns (excluding the target label) were one-hot encoded using `pd.get_dummies`.  
- **Label encoding:** Target labels were transformed into numeric classes with `LabelEncoder`, creating a mapping from attack types to integer codes.  
- **Feature scaling:** All numeric features were normalized using `StandardScaler` to ensure uniform ranges and improve model training stability.

### 3. Balancing Classes with SMOTEENN
- **Problem:** The dataset exhibited class imbalance, common in network intrusion detection tasks.  
- **Solution:** `SMOTEENN` (Synthetic Minority Over-sampling Technique combined with Edited Nearest Neighbors) was applied to:  
  - Oversample minority attack classes.  
  - Remove noisy or overlapping samples.  
- The resulting dataset was balanced and better suited for robust per-class classification.

### 4. Train/Test Split
- **Splitting:** The balanced dataset was split into **80% training** and **20% testing** subsets.  
- **Stratification:** Ensured that all attack classes were proportionally represented in both sets.  
- **Saving test data:** The test set was saved for reproducible evaluation and benchmarking.

### 5. Model Development
Two complementary models were trained and tuned to maximize performance:

1. **Random Forest (RF)**  
   - Trained with balanced class weights to account for any remaining imbalance.  
   - Hyperparameters (`n_estimators`, `max_depth`, `min_samples_split`, `min_samples_leaf`) were optimized using `RandomizedSearchCV`.  

2. **XGBoost (XGB)**  
   - Gradient boosting model to capture complex feature interactions.  
   - Hyperparameters (`n_estimators`, `max_depth`, `learning_rate`, `subsample`, `colsample_bytree`) were tuned via `RandomizedSearchCV`.

### 6. Ensemble Model
- A **soft-voting ensemble** was created by combining the best RF and XGB models.  
- Predicted probabilities from both models were aggregated to improve robustness and accuracy across all attack classes.

### 7. Prediction and Threshold Adjustment
- **Predicting probabilities:** The ensemble model generated class-wise probabilities on the test set.  
- **Threshold adjustment:** Class-specific thresholds were applied to enhance detection of minority attacks, particularly `PortScan`.  
- **Label mapping:** Predicted probabilities were converted back to the original attack labels for evaluation.

### 8. Evaluation Metrics
The performance of the AI NIDS was assessed using a comprehensive set of metrics:

- **Classification report:** Per-class precision, recall, and F1-score.  
- **Confusion matrix:** Visualizing correct and incorrect predictions.  
- **Balanced accuracy:** Accounts for class imbalance.  
- **Feature importance:** Top 20 features extracted from the Random Forest model.  
- **Visualization:** ROC curves, precision-recall plots, and reliability plots (calibration curves).  

### 9. Saving Models
- The trained ensemble model, scaler, and label encoder were saved using `joblib` for future inference and reproducibility.

This methodology ensures a **reproducible, interpretable, and high-performing AI-driven network intrusion detection system**, capable of detecting multiple attack types from CIC-IDS2017 datasets while handling class imbalance effectively.
