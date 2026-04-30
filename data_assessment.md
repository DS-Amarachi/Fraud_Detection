# Data Assessment Report
## Fraudulent Transaction Detection for Digital Money Transfer


## 1. Dataset Overview

### Source
- **Dataset File**: `nova_pay_combined.csv`
- **Location**: Fraud Detection Project Directory
- **Purpose**: Training data for fraud detection model

### Dataset Dimensions
- **Rows**: 11400
- **Columns**: 26
- **Data Type**: CSV (Comma-Separated Values)


## 2. Data Structure & Columns

### Target Variable
- **Column Name**: `is_fraud`
- **Type**: Binary Classification (0 = Legitimate, 1 = Fraudulent)
- **Description**: Identifies whether a transaction is fraudulent or legitimate



## 3. Data Quality Assessment

### Dataset Characteristics
- **Data Types**: Mix of numeric and categorical variables

### Missing Values
- **Missing Data Count**: timestamp:29; amount_usd:305; fee:295; ip_address: 305; ip_country:301; kyc_tier:300; device_trust_score:295
- **Handling Strategy**: To be determined during data cleaning

### Duplicate Records
- **Total Duplicates**: 200

### Data Imbalance (Target Variable Distribution)
- **Legitimate Transactions (0)**: 91%
- **Fraudulent Transactions (1)**: 9%


## 4. Data Type Analysis

| Column Name | Data Type | Observations |
|---|---|---|
| timestamp | object | Time variable |
| amount_src | object | Floating value |


## 5. Key Findings

### Issues Identified
1. **Missing Values**: less than 50% missingness in seven column.
2. **Duplicates**: There are about 200 duplicated rows out of 11400
3. **Class Imbalance**: The dataset is highly imbalance with less than 10% flagged as fraud
4. **Data Type Issues**: timestamp and amount_src column have incorrect data types

