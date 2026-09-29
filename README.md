# Transjakarta-Student-Satisfaction-Analysis
A survey-based data analysis project that investigates the **satisfaction level of university students in West Jakarta toward Transjakarta public transportation**. The project combines survey sampling methods, descriptive and statistical analysis, machine learning classification, feature importance analysis, and keyword-based text mining to understand the factors associated with student satisfaction and the most frequently mentioned service improvements.

> **Note:** This project was developed for academic purposes in a group as part of the Survey and Sampling Methods course.

---

## Project Overview
Transjakarta is one of the main public transportation services used by students in Jakarta. This project aims to understand how students in West Jakarta perceive the quality of Transjakarta services and how satisfied they are based on their experiences.

The survey focuses on students from three universities:
* **Bina Nusantara University (BINUS)**
* **Universitas Trisakti**
* **Universitas Kristen Krida Wacana (UKRIDA)**

The analysis covers four main dimensions:
1. **Reliability**
2. **Comfort**
3. **Safety and Security**
4. **Customer Satisfaction**

In addition to quantitative survey analysis, open-ended responses were analyzed to identify common complaints, suggestions, and service-related topics.

---

## Objectives
* Measure the overall satisfaction level of students toward Transjakarta.
* Analyze students' perceptions of different aspects of Transjakarta services.
* Apply proportional sample weighting to improve the representativeness of the analysis.
* Evaluate the validity and reliability of the survey instrument.
* Identify factors that contribute to satisfaction classification using Random Forest.
* Analyze feature importance to identify the most influential service attributes.
* Analyze open-ended responses to identify frequently mentioned service issues and suggestions.

---

## Survey Design
### Target Population
The target population consists of university students in **West Jakarta who have used Transjakarta**.

The survey focused on students from three universities:
| University                       | Estimated Population |
| -------------------------------- | -------------------: |
| Bina Nusantara University        |               20,000 |
| Universitas Trisakti             |               17,664 |
| Universitas Kristen Krida Wacana |                3,536 |
| **Total**                        |           **41,200** |

### Data Collection
The survey was conducted using an online questionnaire containing:
* Demographic questions
* Screening questions
* Likert-scale questions
* Multiple-choice questions
* An open-ended question for suggestions and feedback

The survey collected **377 responses**, of which **359 respondents had used Transjakarta** and were included in the main analysis.

---

## Constructs and Measurements
The survey measures four main constructs.

### Reliability
Measures the consistency and dependability of Transjakarta services through:
* Bus punctuality
* Schedule consistency
* Bus frequency
* Waiting time
* Route coverage
* Schedule changes

### Comfort
Measures the physical comfort experienced during the journey through:
* Passenger density
* Seat comfort
* Handgrip assistance
* Temperature
* Cleanliness
* Priority seating
* Women's area

### Safety and Security
Measures users' perceptions of safety through:
* Importance of priority seats
* Importance of women's areas
* Driver behavior
* Overall safety

### Customer Satisfaction
Measures users' overall experience through:
* Staff interaction
* Fare compared with service quality
* Intention to continue using Transjakarta
* Likelihood of recommending Transjakarta

---

## Data Preprocessing
### 1. Data Filtering
Only respondents who had previously used Transjakarta were retained. The dataset was reduced from: **377 responses → 359 valid respondents**

### 2. Removing Unnecessary Columns
The following columns were removed from the analysis:
* Timestamp
* Name
* Gender
* Phone number
* Bus route
* Active student status
* Transjakarta usage screening question  

These variables were not used as predictors in the subsequent analysis.

### 3. Duplicate Checking
Duplicate responses were checked, and no duplicate observations were found.

### 4. Missing Value Checking

The analysis found no missing values in the variables used for the main analysis.

---

## Proportional Sample Weighting
The number of respondents from each university did not perfectly reflect the university population proportions. To address this, **proportional sample weighting** was applied. The weight for each university was calculated as:

```text
Population Proportion
---------------------
Sample Proportion
```

The resulting weights were:

| University           | Sample Weight |
| -------------------- | ------------: |
| BINUS                |        1.0132 |
| Universitas Trisakti |          2.08 |
| UKRIDA               |          0.27 |

These weights were incorporated into the statistical analysis and Random Forest training so that the contribution of respondents more closely reflected the population distribution.

---

## Exploratory Data Analysis
The analysis included:
* Respondent distribution by university
* Target satisfaction distribution
* Summary statistics
* Mean and standard deviation for each survey item
* Construct-level analysis

Summary statistics were calculated using the sample weights to better represent the population proportions.

Some of the highest mean scores included:
| Variable                               | Mean |
| -------------------------------------- | ---: |
| Women's Area (`women`)                 | 4.35 |
| Importance of Women's Area (`pwomen`)  | 4.38 |
| Importance of Priority Seats (`pseat`) | 4.33 |
| Fare vs. Service Quality (`fare`)      | 4.30 |
| Likelihood to Recommend (`recommend`)  | 4.09 |

Meanwhile, some relatively lower-scoring aspects included:
* Bus frequency (`freq`) — 2.69
* Schedule changes (`change`) — 2.69
* Driver behavior (`driver`) — 2.34

---

## Instrument Quality Testing
Before modelling, the survey instrument was evaluated through **validity and reliability testing**.

### Validity Test
The survey items were evaluated using **Corrected Item-Total Correlation**.

The criteria used were:

```text
r > 0.30
p-value < 0.05
```

An iterative validity check was performed within each dimension. Several variables were removed from their respective construct dimensions because they did not meet the validity criteria. However, `safe` and `driver` were retained as individual modelling features because of their conceptual relevance to user satisfaction and their contribution to the final Random Forest model.

### Reliability Test
Internal consistency was evaluated using **Cronbach's Alpha**.

The criteria used were:

* α ≥ 0.70 → Reliable
* α ≥ 0.60 → Sufficiently reliable
* α < 0.60 → Not Reliable

The reliability analysis was performed after the iterative validity-based item refinement.

---

## Response Bias Analysis
A response bias analysis was performed to identify potential patterns such as:
* Excessive selection of middle-scale responses
* Excessive positive responses
* Highly skewed response distributions

The analysis used:
* Percentage of middle-category responses
* Percentage of positive responses
* Skewness

An item was flagged as potentially biased when:

```text
% Middle > 50%
OR
% Positive > 80%
OR
|Skewness| ≥ 1.5
```

---

## Non-Response Bias Analysis
A **wave analysis** was performed to examine whether early and late respondents showed significantly different response patterns.

The respondents were divided into:
* Early respondents → first one-third
* Late respondents → last one-third

The **Mann-Whitney U test** was then applied to compare their responses.

A p-value below 0.05 was treated as an indication of a statistically significant difference between the two response groups.

---

## Target Transformation
The original satisfaction variable contained five categories:
1. Tidak puas
2. Kurang puas
3. Cukup puas
4. Puas
5. Sangat puas

Because the lower satisfaction categories contained very few observations, the target was consolidated into three classes:
| Original Categories      | Final Class           |
| ------------------------ | --------------------- |
| Tidak puas + Kurang puas | **Tidak/Kurang Puas** |
| Cukup puas               | **Cukup Puas**        |
| Puas + Sangat puas       | **Puas/Sangat Puas**  |

The final target encoding was:
```text
1 → Tidak/Kurang Puas
2 → Cukup Puas
3 → Puas/Sangat Puas
```

This transformation was performed to reduce class imbalance and provide a more practical target distribution for classification.

---

## Machine Learning

### Train-Test Split
The dataset was divided using **stratified sampling**:
* **80% Training Set:** 287 observations
* **20% Testing Set:** 72 observations

The stratification preserved the relative distribution of the three satisfaction categories.

### Feature Encoding
Two encoding methods were used.

**One-Hot Encoding**
Applied to the nominal variable: `period`. The encoder used `drop='first'` to avoid redundant categories.

**Ordinal Encoding**
Applied to the Likert-scale variables because their categories have an inherent order. The preprocessing was implemented using a **ColumnTransformer**, with the transformer fitted only on the training data before being applied to the test data.

---

## Random Forest Classification
A **Random Forest Classifier** was used to predict the three satisfaction categories.

The model configuration was:

```python
RandomForestClassifier(
    n_estimators=300,
    max_depth=10,
    class_weight='balanced',
    random_state=42,
    n_jobs=-1
)
```

The model also used the calculated **sample weights** during training.

### Evaluation Metrics
The model was evaluated using:
* Accuracy
* Precision
* Recall
* F1-Score
* Confusion Matrix

---

## Model Results
The Random Forest achieved: **Accuracy: 83.33%**  
| Class                | Precision |   Recall | F1-Score |
| -------------------- | --------: | -------: | -------: |
| Tidak/Kurang Puas    |      1.00 |     0.50 |     0.67 |
| Cukup Puas           |      0.80 |     0.44 |     0.57 |
| Puas/Sangat Puas     |      0.84 |     0.98 |     0.90 |
| **Macro Average**    |  **0.88** | **0.64** | **0.71** |
| **Weighted Average** |  **0.83** | **0.83** | **0.81** |

The model achieved its strongest performance on the **Puas/Sangat Puas** class, which also had the largest number of observations. The smaller classes remained more challenging, particularly **Cukup Puas**, where the model frequently predicted respondents as belonging to the higher satisfaction category.

### Confusion Matrix
The test-set predictions showed:
* **1 of 2** Tidak/Kurang Puas respondents correctly classified
* **8 of 18** Cukup Puas respondents correctly classified
* **51 of 52** Puas/Sangat Puas respondents were correctly classified

The largest source of misclassification occurred between **Cukup Puas** and **Puas/Sangat Puas**.

---

## Feature Importance
Feature importance was extracted from the Random Forest model to identify which service attributes contributed most to the model's predictions.

### Top 5 Features

| Rank | Feature                            | Importance |
| ---: | ---------------------------------- | ---------: |
|    1 | `recommend`                        |   0.097737 |
|    2 | `seat`                             |   0.087853 |
|    3 | `staff`                            |   0.072700 |
|    4 | `temp`                             |   0.071963 |
|    5 | `clean`                            |   0.070217 |

> Feature importance represents the contribution of a feature within the trained Random Forest model; it does not by itself establish a causal relationship.

---

## Text Mining
The survey also included an open-ended question where respondents could provide suggestions or feedback about Transjakarta services.

### Comment Filtering
The raw comments were cleaned by:
* Converting text to lowercase
* Removing unnecessary whitespace
* Filtering out empty or non-informative responses

Examples of filtered responses included:
* `-`
* `tidak ada`
* `none`
* `oke`
* `sudah bagus`
* `nihil`

After filtering, **94 valid comments** remained for text analysis.

### Keyword-Based Categorization
The comments were categorized using predefined keyword groups. A single comment could belong to more than one category when it contained keywords associated with multiple topics.

The categories included:
* Armada dan Frekuensi Bus
* Ketepatan Waktu dan Jadwal
* Aplikasi dan Informasi Real-Time
* Jalur Khusus Busway
* Keamanan dan Keselamatan
* Bus Khusus Perempuan
* Kepadatan Penumpang
* Kenyamanan dan AC
* Kebersihan
* Rute dan Jangkauan
* Halte dan Fasilitas
* Pengemudi dan Pelayanan
* Lainnya

---

## Text Mining Results
The most frequently mentioned categories were:
| Category                         | Total | Percentage |
| -------------------------------- | ----: | ---------: |
| Armada dan Frekuensi Bus         |    28 |     29.79% |
| Rute dan Jangkauan               |    19 |     20.21% |
| Lainnya                          |    18 |     19.15% |
| Keamanan dan Keselamatan         |    15 |     15.96% |
| Aplikasi dan Informasi Real-Time |    13 |     13.83% |
| Kepadatan Penumpang              |    13 |     13.83% |
| Ketepatan Waktu dan Jadwal       |    12 |     12.77% |
| Halte dan Fasilitas              |    12 |     12.77% |
| Kenyamanan dan AC                |    12 |     12.77% |
| Pengemudi dan Pelayanan          |     9 |      9.57% |
| Jalur Khusus Busway              |     7 |      7.45% |
| Kebersihan                       |     6 |      6.38% |
| Bus Khusus Perempuan             |     4 |      4.26% |

Because comments could be assigned to multiple categories, the percentages do not sum to 100%. The most frequently mentioned issue was **bus fleet and frequency**, with respondents commonly suggesting an increase in the number of buses and service frequency, particularly during rush hours. Other frequently mentioned topics included route coverage, safety, real-time information, passenger density, punctuality, facilities, and comfort.

---

## Overall Findings
The analysis combines quantitative survey results, machine learning, and open-ended response analysis. The descriptive analysis shows generally positive perceptions across many Transjakarta service attributes, while some operational aspects such as bus frequency, schedule changes, and driver behavior received relatively lower or more varied ratings.

The Random Forest model achieved an **83.33% test accuracy** and **0.71 Macro F1-Score**. The feature importance analysis highlighted recommendation likelihood, seat comfort, staff interaction, temperature, cleanliness, driver behavior, and safety among the most influential features in the model. Meanwhile, the text mining analysis identified **bus fleet and frequency**, **route coverage**, and **safety** as frequently mentioned topics in respondents' suggestions.

Together, these analyses provide a broader view of student perceptions by combining structured survey responses with qualitative feedback.
