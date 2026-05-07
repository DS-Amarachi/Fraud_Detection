# Data Cleaning Documentation
## Fraudulent Transaction Detection Project - Nova Pay Dataset


## Overview

This document outlines all data cleaning and preprocessing steps performed on the Nova Pay dataset to prepare it for fraudulent transaction detection modeling. The cleaning process is divided into two main stages: foundational cleaning and data quality validation.


## Stage 1: Foundational Data Cleaning

### 1.1 Address Data Type Issues

**Columns Affected:** `timestamp`, `amount_src`

**Actions:**
- **timestamp:** Converted to datetime format using `pd.to_datetime()` with `errors='coerce'` to handle invalid entries gracefully
- **amount_src:** Converted to float data type using `pd.to_numeric()` with `errors='coerce'`

**Reason:** Incorrect data types prevent proper analysis and modeling. These conversions enable time-based operations and numerical calculations.


### 1.2 Handle Duplicated Rows

**Action:** Removed all duplicate rows using `df.drop_duplicates()`

**Reason:** Duplicate transactions inflate the dataset and can bias model training and evaluation metrics.


### 1.3 Handle Missing Values

The dataset contained approximately **16% missing values**, distributed across multiple columns. Rather than dropping data (which would reduce dataset quality), missing values were imputed strategically based on column context.

#### 1.3.1 Missing Values in `amount_usd`

**Approach:** 
1. Calculated exchange rates by grouping transactions by `source_currency`
2. For each currency, computed the mean ratio of `amount_usd` to `amount_src`
3. Stored exchange rates in a dictionary for lookup: `exchange_rate_dict`
4. Filled missing `amount_usd` values by multiplying `amount_src` with the corresponding exchange rate

**Rationale:** Since `amount_src` had no missing values, exchange rates could be reliably calculated and used to impute missing USD amounts.

#### 1.3.2 Missing Values in `fee`

**Approach:**
1. If `channel` column exists, filled missing fees per channel using the **channel median**
2. Remaining missing fees filled with the **overall dataset median**

**Rationale:** Transaction fees are channel-dependent, so grouping by channel provides more accurate imputation.

#### 1.3.3 Missing Values in `ip_country`

**Approach:** Filled missing values with corresponding `home_country` as a fallback

**Rationale:** When IP country is unavailable, the customer's home country provides a reasonable default.

#### 1.3.4 Missing Values in `kyc_tier`

**Approach:** Filled missing values with the **mode (most frequent value)** of the column

**Rationale:** KYC tier is categorical; using the mode preserves the statistical distribution of the data.

#### 1.3.5 Missing Values in `device_trust_score`

**Approach:** 
1. Grouped by `['new_device', 'kyc_tier']`
2. Filled missing values within each group using the **group median**
3. Any remaining missing values filled with the overall median

**Rationale:** Device trust scores depend on device type and KYC tier; grouping ensures contextually appropriate imputation.

#### 1.3.6 Final Missing Values Cleanup

**Columns Dropped:** `ip_address`

**Rows Dropped:** All rows with remaining null/NaT values in:
- `timestamp` (conversion failures)
- `amount_src` (conversion failures)
- `ip_address` (dropped column)

**Rationale:** These values could not be reliably imputed and represent data quality issues.


## Stage 2: Data Quality Validation & Further Cleaning

### 2.1 Numerical Columns - Negative Values

**Issue Identified:** Negative values found in amount and score columns where values should be ≥ 0

**Columns Cleaned:** `amount_src`, `fee`, `device_trust_score`, `txn_velocity_1h`

**Action:** Filtered out rows where any of these columns contained negative values

**Rationale:** Transaction amounts, fees, and velocity metrics cannot be negative; these likely represent data entry errors or conversion artifacts.


### 2.2 Categorical Columns - Data Standardization

#### 2.2.1 `home_country`

**Issues Fixed:**
- Removed leading/trailing whitespace

**Method:** `df['home_country'].str.strip()`

#### 2.2.2 `channel`

**Issues Fixed:**
1. Removed whitespace
2. Standardized to lowercase for consistency
3. Corrected spelling errors:
   - `mobille` → `mobile`
   - `weeb` → `web`

**Method:**
```python
df['channel'] = df['channel'].str.strip()\
                              .str.lower()\
                              .str.replace('mobille', 'mobile')\
                              .str.replace('weeb', 'web')
```

#### 2.2.3 `ip_country`

**Issues Fixed:**
- Removed leading/trailing whitespace

**Method:** `df['ip_country'].str.strip()`

#### 2.2.4 `kyc_tier`

**Issues Fixed:**
1. Removed whitespace
2. Standardized to lowercase
3. Corrected spelling errors:
   - `standrd` → `standard`
   - `enhancd` → `enhanced`

**Method:**
```python
df['kyc_tier'] = df['kyc_tier'].str.strip()\
                                .str.lower()\
                                .str.replace('standrd', 'standard')\
                                .str.replace('enhancd', 'enhanced')
```


## Summary of Changes

| Metric | Value |
|--------|-------|
| **Initial Missing Values** | ~16% |
| **Rows Removed (Duplicates)** | N/A (tracked in notebook) |
| **Rows Removed (Data Quality)** | N/A (tracked in notebook) |
| **Columns Modified** | 8 |
| **Imputation Methods Used** | 6 |


## Outcome

After data cleaning, the dataset is:
- ✅ **Complete:** No remaining missing values (except intentionally dropped columns)
- ✅ **Consistent:** Standardized formats across categorical columns
- ✅ **Valid:** Removed invalid negative values and conversion failures
- ✅ **Reliable:** Ready for exploratory data analysis and model training


## Files & Code References

**Notebook:** `fraud_detect_project.ipynb`

**Stages in Notebook:**
- Stage 1: Rows 135-250 (Foundational Cleaning)
- Stage 2: Rows 253-415 (Data Quality Validation)