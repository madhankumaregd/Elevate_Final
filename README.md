# Elevate_Final
Movie Analysis
```markdown
# 🎬 Movie Success Prediction & Sentiment Analysis Project

![Project Banner](https://img.shields.io/badge/Python-3.8%2B-blue)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow)
![MySQL](https://img.shields.io/badge/MySQL-Database-orange)
![License](https://img.shields.io/badge/License-MIT-green)

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [Dataset](#-dataset)
- [Tools & Technologies](#-tools--technologies)
- [Project Structure](#-project-structure)
- [Installation Guide](#-installation-guide)
- [Phase 1: Data Preparation & SQL Analysis](#phase-1-data-preparation--sql-analysis)
- [Phase 2: Sentiment Analysis with VADER](#phase-2-sentiment-analysis-with-vader)
- [Phase 3: Feature Engineering](#phase-3-feature-engineering)
- [Phase 4: Machine Learning Models](#phase-4-machine-learning-models)
- [Phase 5: Visualizations](#phase-5-visualizations)
- [Phase 6: Power BI Dashboard](#phase-6-power-bi-dashboard)
- [Phase 7: Results Export](#phase-7-results-export)
- [Phase 8: Model Summary](#phase-8-model-summary)
- [Results & Findings](#-results--findings)
- [Interview Questions](#-interview-questions)
- [License](#-license)

---

## 🎯 Project Overview

This comprehensive data analytics project analyzes IMDb movie data to predict box office success and understand audience sentiment. The project combines **Python-based machine learning**, **VADER sentiment analysis**, **MySQL database queries**, and an interactive **Power BI dashboard** to provide actionable insights for filmmakers and movie enthusiasts.

### 🎯 Objectives
- Predict movie box office success using regression models
- Analyze viewer sentiment from reviews using VADER
- Create interactive dashboards for visual exploration
- Identify genre-wise sentiment trends over decades
- Determine key factors influencing movie success
- Perform SQL-based data analysis and transformation

---

## 📊 Dataset

- **Source**: IMDb Top 1000+ Movies
- **Records**: 1,000 movies
- **Features**: 9 columns

### Column Description
| Column Name | Description | Data Type |
|-------------|-------------|-----------|
| Rank_no | Movie ranking | Integer |
| Movie_name | Title of the movie | String |
| Release_Year | Year of release | Integer |
| Certificate | Rating certificate (R, PG-13, etc.) | String |
| Runtime_in_min | Movie duration in minutes | Integer |
| Genre | Movie genre(s) | String |
| Metascore | Critic score (0-100) | Float |
| Gross_in_$_M | Box office gross in millions | String |
| Rating_from_10 | IMDb user rating (0-10) | Float |

---

## 🛠️ Tools & Technologies

| Category | Tools Used |
|----------|------------|
| **Programming** | Python 3.8+, Jupyter Notebook |
| **Data Processing** | Pandas, NumPy |
| **Sentiment Analysis** | VADER (NLTK) |
| **Machine Learning** | Scikit-learn (Linear/Ridge/Lasso Regression, Random Forest) |
| **Visualization** | Matplotlib, Seaborn, WordCloud |
| **Database** | MySQL |
| **Dashboard** | Microsoft Power BI |
| **Version Control** | Git, GitHub |

---

## 📁 Project Structure

```
movie_success_prediction/
│
├── 📂 data/
│   ├── imdb.csv                          # Raw dataset
│   └── movie_data_with_sentiment.csv      # Processed data with sentiment
│
├── 📂 notebooks/
│   ├── 01_data_cleaning.ipynb             # Data preprocessing
│   ├── 02_sentiment_analysis.ipynb        # VADER sentiment analysis
│   ├── 03_exploratory_analysis.ipynb      # EDA and visualizations
│   ├── 04_predictive_modeling.ipynb       # ML models training
│   └── 05_results_export.ipynb            # Export summaries
│
├── 📂 powerbi/
│   ├── movie_dashboard.pbix                # Power BI dashboard file
│   └── dashboard_screenshots/              # Dashboard images
│
├── 📂 outputs/
│   ├── 📂 charts/
│   │   ├── sentiment_visuals_comprehensive.png
│   │   ├── genre_sentiment_boxplot.png
│   │   ├── genre_sentiment_heatmap.png
│   │   ├── feature_importance.png
│   │   └── model_evaluation.png
│   ├── 📂 reports/
│   │   ├── model_summary.txt
│   │   ├── model_summary_table.csv
│   │   └── movie_sentiment_analysis_results.xlsx
│   └── 📂 models/
│       └── trained_model.pkl
│
├── 📂 sql/
│   └── movie_analysis_queries.sql          # MySQL queries
│
├── README.md                                # Project documentation
├── requirements.txt                          # Python dependencies
├── .gitignore                                # Git ignore file
└── LICENSE                                   # MIT License
```

---

## 📦 Installation Guide

### Prerequisites
- Python 3.8 or higher
- MySQL Server 8.0+
- Power BI Desktop (free)
- Git

### Step 1: Clone Repository
```bash
git clone https://github.com/yourusername/movie-success-prediction.git
cd movie-success-prediction
```

### Step 2: Create Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# Mac/Linux
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Python Dependencies
```bash
pip install -r requirements.txt
```

Create `requirements.txt`:
```txt
pandas==1.5.3
numpy==1.24.3
scikit-learn==1.2.2
matplotlib==3.7.1
seaborn==0.12.2
vaderSentiment==3.2.1
nltk==3.8.1
wordcloud==1.9.2
openpyxl==3.1.2
jupyter==1.0.0
mysql-connector-python==8.0.33
```

---

## 🔄 Phase 1: Data Preparation & SQL Analysis

### MySQL Setup and Table Creation

```sql
-- Create database
CREATE DATABASE movie_analysis;
USE movie_analysis;

-- Create movies table
CREATE TABLE movies (
    Rank_no INT,
    Movie_name VARCHAR(255),
    Release_Year INT,
    Certificate VARCHAR(50),
    Runtime_in_min INT,
    Genre VARCHAR(255),
    Metascore FLOAT,
    Gross_in_$_M VARCHAR(50),
    Rating_from_10 FLOAT
);

-- Load data from CSV (adjust path)
LOAD DATA INFILE 'C:/path/to/your/imdb.csv'
INTO TABLE movies
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

-- Clean Gross column (remove # and convert to numeric)
ALTER TABLE movies ADD COLUMN Gross_numeric FLOAT;

UPDATE movies 
SET Gross_numeric = CAST(
    REPLACE(REPLACE(Gross_in_$_M, '#', ''), ',', '') 
    AS FLOAT
)
WHERE Gross_in_$_M != '' AND Gross_in_$_M IS NOT NULL;

-- Handle missing values
UPDATE movies SET Gross_numeric = (SELECT AVG(Gross_numeric) FROM movies) 
WHERE Gross_numeric IS NULL;

-- Create decade column
ALTER TABLE movies ADD COLUMN Decade INT;
UPDATE movies SET Decade = FLOOR(Release_Year / 10) * 10;
```

### Key SQL Analysis Queries

```sql
-- 1. Top 10 highest-grossing movies
SELECT Movie_name, Release_Year, Gross_numeric, Rating_from_10
FROM movies
WHERE Gross_numeric IS NOT NULL
ORDER BY Gross_numeric DESC
LIMIT 10;

-- 2. Genre-wise Performance
SELECT 
    SUBSTRING_INDEX(Genre, ',', 1) as primary_genre,
    COUNT(*) as movie_count,
    AVG(Rating_from_10) as avg_rating,
    AVG(Gross_numeric) as avg_gross,
    SUM(Gross_numeric) as total_gross
FROM movies
GROUP BY primary_genre
ORDER BY avg_rating DESC;

-- 3. Yearly Trends
SELECT 
    Release_Year,
    COUNT(*) as movies_count,
    AVG(Rating_from_10) as avg_rating,
    AVG(Gross_numeric) as avg_gross,
    SUM(Gross_numeric) as total_gross
FROM movies
WHERE Release_Year >= 2000
GROUP BY Release_Year
ORDER BY Release_Year;

-- 4. Success Categories
SELECT 
    CASE 
        WHEN Rating_from_10 >= 8.5 THEN 'Hit'
        WHEN Rating_from_10 >= 7.5 THEN 'Average'
        ELSE 'Flop'
    END as success_category,
    COUNT(*) as count,
    AVG(Gross_numeric) as avg_gross,
    AVG(Metascore) as avg_metascore,
    AVG(Runtime_in_min) as avg_runtime
FROM movies
GROUP BY success_category;

-- 5. Runtime Analysis
SELECT 
    CASE 
        WHEN Runtime_in_min < 90 THEN 'Short (<90 min)'
        WHEN Runtime_in_min BETWEEN 90 AND 120 THEN 'Medium (90-120)'
        WHEN Runtime_in_min BETWEEN 121 AND 150 THEN 'Long (121-150)'
        ELSE 'Epic (>150 min)'
    END as runtime_category,
    COUNT(*) as movie_count,
    AVG(Rating_from_10) as avg_rating,
    AVG(Gross_numeric) as avg_gross
FROM movies
GROUP BY runtime_category
ORDER BY avg_rating DESC;

-- 6. Decade Analysis
SELECT 
    Decade,
    COUNT(*) as movie_count,
    AVG(Rating_from_10) as avg_rating,
    AVG(Gross_numeric) as avg_gross,
    AVG(Metascore) as avg_metascore
FROM movies
GROUP BY Decade
ORDER BY Decade;

-- 7. Certificate Analysis
SELECT 
    Certificate,
    COUNT(*) as movie_count,
    AVG(Rating_from_10) as avg_rating,
    AVG(Gross_numeric) as avg_gross
FROM movies
WHERE Certificate != ''
GROUP BY Certificate
ORDER BY movie_count DESC;

-- 8. Correlation between Metascore and Rating
SELECT 
    CORR(Metascore, Rating_from_10) as metascore_rating_correlation,
    CORR(Metascore, Gross_numeric) as metascore_gross_correlation,
    CORR(Rating_from_10, Gross_numeric) as rating_gross_correlation
FROM movies;
```

### Export Cleaned Data for Python

```sql
-- Export cleaned data to CSV
SELECT 
    Rank_no,
    Movie_name,
    Release_Year,
    Certificate,
    Runtime_in_min,
    Genre,
    Metascore,
    Gross_numeric as Gross_in_$_M,
    Rating_from_10,
    Decade
INTO OUTFILE 'C:/path/to/export/cleaned_movies.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM movies;
```

---

## 🔄 Phase 2: Sentiment Analysis with VADER

```python
# ============================================
# PHASE 2: SENTIMENT ANALYSIS WITH VADER
# ============================================

# Import required libraries
import pandas as pd
import numpy as np
import random
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import nltk
nltk.download('vader_lexicon')
nltk.download('punkt')
nltk.download('stopwords')

print("="*60)
print("PHASE 2: SENTIMENT ANALYSIS WITH VADER")
print("="*60)

# Initialize VADER
analyzer = SentimentIntensityAnalyzer()

# Load data
df = pd.read_csv('data/imdb.csv')
print(f"\n✅ Data loaded: {len(df)} movies")

# Function to generate synthetic reviews based on ratings
def generate_review(rating, movie_name, genre):
    """
    Generate realistic synthetic reviews based on movie rating
    """
    if pd.isna(rating):
        rating = 7.5
    
    if rating >= 8.5:
        templates = [
            f"{movie_name} is an absolute masterpiece! The {genre} elements are perfectly executed. A must-watch!",
            f"Brilliant filmmaking at its finest. One of the best {genre} movies I've ever seen.",
            f"This movie deserves all the praise. Incredible performances and gripping narrative.",
            f"Absolutely loved it! The direction, acting, and storytelling are top-notch.",
            f"A cinematic gem! The {genre} genre has a new classic.",
            f"Mind-blowing! Every scene is perfectly crafted. A true masterpiece.",
            f"One of the greatest films ever made. The {genre} aspects are phenomenal.",
            f"Outstanding! The performances, the direction, everything is perfect."
        ]
    elif rating >= 7.5:
        templates = [
            f"{movie_name} is a solid {genre} film. Entertaining with good performances.",
            f"Enjoyed watching this. Great {genre} elements and decent storyline.",
            f"A good watch for {genre} fans. Not perfect but definitely worth your time.",
            f"Pretty good movie with some memorable moments.",
            f"Well-made and engaging. The {genre} aspects work well.",
            f"Solid entertainment! Good performances and nice direction.",
            f"Worth watching! A good addition to the {genre} genre.",
            f"Enjoyable from start to finish. Great for a movie night."
        ]
    else:
        templates = [
            f"{movie_name} was disappointing. The {genre} aspects felt weak.",
            f"Not impressed with this one. Expected much more from this film.",
            f"Below average {genre} film. Could have been better.",
            f"Waste of time. Poor execution and weak storyline.",
            f"Disappointing effort. The {genre} elements fall flat.",
            f"Could have been so much better. Poorly executed.",
            f"Not worth the hype. Disappointing in every way.",
            f"A letdown. The {genre} genre deserves better films."
        ]
    
    return random.choice(templates)

# Generate reviews
print("\n📝 Generating synthetic reviews...")
df['Synthetic_Review'] = df.apply(
    lambda row: generate_review(
        row['Rating_from_10'], 
        row['Movie_name'], 
        str(row['Genre']).split(',')[0] if pd.notna(row['Genre']) else 'Unknown'
    ), axis=1
)
print(f"✅ Generated {len(df)} synthetic reviews")

# Apply VADER sentiment analysis
def get_vader_scores(text):
    """Get VADER polarity scores"""
    if pd.isna(text):
        return pd.Series([0, 0, 0, 0])
    scores = analyzer.polarity_scores(str(text))
    return pd.Series([scores['neg'], scores['neu'], scores['pos'], scores['compound']])

# Get sentiment scores
print("\n🔍 Applying VADER sentiment analysis...")
df[['VADER_Negative', 'VADER_Neutral', 'VADER_Positive', 'VADER_Compound']] = \
    df['Synthetic_Review'].apply(get_vader_scores)

# Classify sentiment
df['VADER_Sentiment'] = df['VADER_Compound'].apply(
    lambda x: 'Positive' if x >= 0.05 else ('Negative' if x <= -0.05 else 'Neutral')
)

# Display sentiment distribution
print("\n📊 Sentiment Distribution:")
sentiment_counts = df['VADER_Sentiment'].value_counts()
for sentiment, count in sentiment_counts.items():
    percentage = (count/len(df))*100
    print(f"   {sentiment}: {count} ({percentage:.1f}%)")

# Calculate correlation
correlation = df['Rating_from_10'].corr(df['VADER_Compound'])
print(f"\n📈 Correlation between IMDb Rating and VADER Compound Score: {correlation:.3f}")

# Save intermediate results
df.to_csv('data/movie_data_with_reviews.csv', index=False)
print("\n✅ Saved intermediate results to 'data/movie_data_with_reviews.csv'")
```

---

## 🔄 Phase 3: Feature Engineering

```python
# ============================================
# PHASE 3: FEATURE ENGINEERING
# ============================================

import pandas as pd
import numpy as np
from sklearn.preprocessing import LabelEncoder
import re

print("="*60)
print("PHASE 3: FEATURE ENGINEERING")
print("="*60)

# Load data with reviews
df = pd.read_csv('data/movie_data_with_reviews.csv')
print(f"\n✅ Data loaded: {len(df)} movies")

# 1. Clean Gross column
def clean_gross(value):
    if pd.isna(value) or value == '':
        return np.nan
    cleaned = str(value).replace('#', '').replace(',', '')
    try:
        return float(cleaned)
    except:
        return np.nan

print("\n💰 Cleaning Gross column...")
df['Gross_numeric'] = df['Gross_in_$_M'].apply(clean_gross)
print(f"   Gross range: ${df['Gross_numeric'].min():.2f}M - ${df['Gross_numeric'].max():.2f}M")

# 2. Clean Release_Year
def clean_year(year_val):
    if pd.isna(year_val):
        return np.nan
    year_str = str(year_val)
    # Extract 4-digit year
    year_match = re.search(r'\b(19|20)\d{2}\b', year_str)
    if year_match:
        return float(year_match.group())
    # If no 4-digit year, try to find any digits
    digits = re.findall(r'\d+', year_str)
    if digits:
        return float(digits[0])
    return np.nan

print("\n📅 Cleaning Release_Year column...")
df['Release_Year'] = df['Release_Year'].apply(clean_year)
print(f"   Year range: {df['Release_Year'].min():.0f} - {df['Release_Year'].max():.0f}")

# 3. Extract primary genre
def extract_primary_genre(genre_string):
    if pd.isna(genre_string) or genre_string == '':
        return 'Unknown'
    try:
        genre_str = str(genre_string).strip().strip('"').strip("'")
        if ',' in genre_str:
            return genre_str.split(',')[0].strip()
        else:
            return genre_str.strip()
    except:
        return 'Unknown'

print("\n🎭 Extracting primary genre...")
df['Primary_Genre'] = df['Genre'].apply(extract_primary_genre)
print(f"   Unique genres: {df['Primary_Genre'].nunique()}")
print(f"   Top genres: {df['Primary_Genre'].value_counts().head(5).to_dict()}")

# 4. Create decade column
df['Decade'] = (df['Release_Year'] // 10) * 10
print(f"\n📊 Created Decade column: {sorted(df['Decade'].unique())}")

# 5. Create success category
df['Success_Category'] = pd.cut(
    df['Rating_from_10'], 
    bins=[0, 7.5, 8.5, 10], 
    labels=['Flop', 'Average', 'Hit']
)
print(f"\n🏆 Success categories:")
print(df['Success_Category'].value_counts())

# 6. Create runtime category
df['Runtime_Category'] = pd.cut(
    df['Runtime_in_min'], 
    bins=[0, 90, 120, 150, 1000], 
    labels=['Short (<90min)', 'Medium (90-120min)', 
            'Long (121-150min)', 'Epic (>150min)']
)

# 7. Handle missing values
print("\n🔄 Handling missing values...")
numeric_cols = ['Metascore', 'Gross_numeric', 'Runtime_in_min', 'Rating_from_10']
for col in numeric_cols:
    if col in df.columns:
        median_val = df[col].median()
        df[col].fillna(median_val, inplace=True)
        print(f"   {col}: filled missing with median ({median_val:.2f})")

# 8. Encode categorical variables
print("\n🔢 Encoding categorical variables...")
le_genre = LabelEncoder()
le_cert = LabelEncoder()

df['Genre_Encoded'] = le_genre.fit_transform(df['Primary_Genre'].fillna('Unknown'))
df['Certificate_Encoded'] = le_cert.fit_transform(df['Certificate'].fillna('Unknown'))

print(f"   Genre encoded: {df['Genre_Encoded'].nunique()} unique values")
print(f"   Certificate encoded: {df['Certificate_Encoded'].nunique()} unique values")

# 9. Final check
print("\n✅ Feature Engineering Complete!")
print(f"   Final shape: {df.shape}")
print(f"   Columns: {df.columns.tolist()}")

# Save processed data
df.to_csv('data/movie_data_with_features.csv', index=False)
print("\n✅ Saved processed data to 'data/movie_data_with_features.csv'")
```

---

## 🔄 Phase 4: Machine Learning Models

```python
# ============================================
# PHASE 4: MACHINE LEARNING MODELS
# ============================================

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LinearRegression, Ridge, Lasso
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
from sklearn.metrics import mean_squared_error, r2_score, mean_absolute_error
import warnings
warnings.filterwarnings('ignore')

print("="*60)
print("PHASE 4: MACHINE LEARNING MODELS")
print("="*60)

# Load processed data
df = pd.read_csv('data/movie_data_with_features.csv')
print(f"\n✅ Data loaded: {len(df)} movies")

# Prepare features
feature_columns = ['Release_Year', 'Runtime_in_min', 'Metascore', 
                   'Rating_from_10', 'Genre_Encoded', 'Certificate_Encoded',
                   'VADER_Compound', 'VADER_Positive', 'VADER_Negative']

X = df[feature_columns].copy()
y = df['Gross_numeric'].copy()

print(f"\n📊 Feature matrix shape: {X.shape}")
print(f"🎯 Target vector shape: {y.shape}")
print(f"\nFeatures used: {feature_columns}")

# Check for missing values
print("\n🔍 Checking for missing values:")
print(X.isnull().sum())

# Handle any remaining missing values
X = X.fillna(X.median())

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

print(f"\n📊 Training set: {X_train.shape}")
print(f"📊 Test set: {X_test.shape}")

# Scale features
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

print("\n✅ Features scaled successfully")

# Train multiple models
models = {
    'Linear Regression': LinearRegression(),
    'Ridge Regression': Ridge(alpha=1.0),
    'Lasso Regression': Lasso(alpha=0.01),
    'Random Forest': RandomForestRegressor(n_estimators=100, random_state=42),
    'Gradient Boosting': GradientBoostingRegressor(n_estimators=100, random_state=42)
}

results = {}
predictions = {}

print("\n" + "="*40)
print("TRAINING MODELS")
print("="*40)

for name, model in models.items():
    print(f"\n▶ Training {name}...")
    
    # Train model
    model.fit(X_train_scaled, y_train)
    
    # Make predictions
    y_pred = model.predict(X_test_scaled)
    predictions[name] = y_pred
    
    # Calculate metrics
    r2 = r2_score(y_test, y_pred)
    rmse = np.sqrt(mean_squared_error(y_test, y_pred))
    mae = mean_absolute_error(y_test, y_pred)
    
    # Cross-validation score
    cv_scores = cross_val_score(model, X_train_scaled, y_train, cv=5, scoring='r2')
    
    results[name] = {
        'R2': r2,
        'RMSE': rmse,
        'MAE': mae,
        'CV_Mean': cv_scores.mean(),
        'CV_Std': cv_scores.std()
    }
    
    print(f"   R² Score: {r2:.4f}")
    print(f"   RMSE: ${rmse:.2f}M")
    print(f"   MAE: ${mae:.2f}M")
    print(f"   CV Score: {cv_scores.mean():.4f} (+/- {cv_scores.std()*2:.4f})")

# Display results comparison
results_df = pd.DataFrame(results).T
print("\n" + "="*40)
print("MODEL PERFORMANCE COMPARISON")
print("="*40)
print(results_df.round(4))

# Find best model
best_model = results_df['R2'].idxmax()
print(f"\n🏆 Best Model: {best_model}")
print(f"   R² Score: {results_df.loc[best_model, 'R2']:.4f}")
print(f"   RMSE: ${results_df.loc[best_model, 'RMSE']:.2f}M")
print(f"   MAE: ${results_df.loc[best_model, 'MAE']:.2f}M")

# Feature importance (for tree-based models)
if 'Random Forest' in models:
    rf_model = models['Random Forest']
    feature_importance = pd.DataFrame({
        'Feature': feature_columns,
        'Importance': rf_model.feature_importances_
    }).sort_values('Importance', ascending=False)
    
    print("\n📊 Feature Importance (Random Forest):")
    print(feature_importance.to_string(index=False))

# Model evaluation visualization
plt.figure(figsize=(15, 10))

# Plot 1: Actual vs Predicted (Best Model)
plt.subplot(2, 3, 1)
best_pred = predictions[best_model]
plt.scatter(y_test, best_pred, alpha=0.5)
plt.plot([y_test.min(), y_test.max()], [y_test.min(), y_test.max()], 'r--', lw=2)
plt.xlabel('Actual Gross ($M)')
plt.ylabel('Predicted Gross ($M)')
plt.title(f'{best_model}: Actual vs Predicted')
plt.grid(True, alpha=0.3)

# Plot 2: Residuals
plt.subplot(2, 3, 2)
residuals = y_test - best_pred
plt.scatter(best_pred, residuals, alpha=0.5)
plt.axhline(y=0, color='r', linestyle='--')
plt.xlabel('Predicted Gross ($M)')
plt.ylabel('Residuals')
plt.title('Residual Plot')
plt.grid(True, alpha=0.3)

# Plot 3: Residual Distribution
plt.subplot(2, 3, 3)
plt.hist(residuals, bins=30, edgecolor='black', alpha=0.7)
plt.xlabel('Residuals')
plt.ylabel('Frequency')
plt.title('Residual Distribution')
plt.grid(True, alpha=0.3)

# Plot 4: Model Comparison
plt.subplot(2, 3, 4)
models_list = list(results.keys())
r2_scores = [results[m]['R2'] for m in models_list]
colors = ['#2ecc71' if m == best_model else '#3498db' for m in models_list]
plt.bar(models_list, r2_scores, color=colors)
plt.xlabel('Model')
plt.ylabel('R² Score')
plt.title('Model Comparison - R² Score')
plt.xticks(rotation=45, ha='right')
plt.grid(True, alpha=0.3, axis='y')

# Plot 5: Feature Importance
plt.subplot(2, 3, 5)
plt.barh(feature_importance['Feature'], feature_importance['Importance'], color='#e67e22')
plt.xlabel('Importance')
plt.title('Feature Importance')
plt.gca().invert_yaxis()
plt.grid(True, alpha=0.3, axis='x')

# Plot 6: Error Comparison
plt.subplot(2, 3, 6)
x_pos = np.arange(len(models_list))
rmse_values = [results[m]['RMSE'] for m in models_list]
mae_values = [results[m]['MAE'] for m in models_list]
plt.bar(x_pos - 0.2, rmse_values, width=0.4, label='RMSE', color='#e74c3c')
plt.bar(x_pos + 0.2, mae_values, width=0.4, label='MAE', color='#f39c12')
plt.xlabel('Model')
plt.ylabel('Error ($M)')
plt.title('Model Errors Comparison')
plt.xticks(x_pos, models_list, rotation=45, ha='right')
plt.legend()
plt.grid(True, alpha=0.3, axis='y')

plt.tight_layout()
plt.savefig('outputs/charts/model_evaluation.png', dpi=300, bbox_inches='tight')
plt.show()
print("\n✅ Model evaluation charts saved to 'outputs/charts/model_evaluation.png'")

# Save the best model
import joblib
joblib.dump(models[best_model], 'outputs/models/best_model.pkl')
joblib.dump(scaler, 'outputs/models/scaler.pkl')
print(f"\n✅ Best model saved to 'outputs/models/best_model.pkl'")
```

---

## 🔄 Phase 5: Visualizations

```python
# ============================================
# PHASE 5: VISUALIZATIONS
# ============================================

import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np
from wordcloud import WordCloud

print("="*60)
print("PHASE 5: VISUALIZATIONS")
print("="*60)

# Load processed data
df = pd.read_csv('data/movie_data_with_features.csv')
print(f"\n✅ Data loaded: {len(df)} movies")

# Set style
plt.style.use('seaborn-v0_8-darkgrid')
sns.set_palette("husl")

# Create figure for comprehensive sentiment visuals
plt.figure(figsize=(20, 12))

# 1. Sentiment Distribution Pie Chart
plt.subplot(2, 3, 1)
sentiment_counts = df['VADER_Sentiment'].value_counts()
colors = {'Positive': '#2ecc71', 'Neutral': '#f39c12', 'Negative': '#e74c3c'}
pie_colors = [colors.get(sent, '#95a5a6') for sent in sentiment_counts.index]
plt.pie(sentiment_counts.values, labels=sentiment_counts.index, 
        autopct='%1.1f%%', colors=pie_colors, startangle=90, 
        wedgeprops={'edgecolor': 'white', 'linewidth': 2})
plt.title('Overall Sentiment Distribution', fontsize=16, fontweight='bold', pad=20)

# 2. Sentiment vs Rating Scatter
plt.subplot(2, 3, 2)
scatter = plt.scatter(df['Rating_from_10'], df['VADER_Compound'], 
                      c=df['Gross_numeric'], alpha=0.6, cmap='viridis', 
                      s=50, edgecolors='white', linewidth=0.5)
plt.colorbar(scatter, label='Gross Revenue ($M)')
plt.xlabel('IMDb Rating', fontsize=12)
plt.ylabel('VADER Compound Score', fontsize=12)
plt.title('Sentiment vs Rating', fontsize=16, fontweight='bold', pad=20)
plt.grid(True, alpha=0.3)

# 3. Sentiment by Decade
plt.subplot(2, 3, 3)
decade_sentiment = df.groupby('Decade').agg({
    'VADER_Compound': 'mean',
    'Movie_name': 'count'
}).reset_index()
plt.plot(decade_sentiment['Decade'], decade_sentiment['VADER_Compound'], 
         marker='o', linewidth=3, markersize=10, color='#3498db', 
         markerfacecolor='white', markeredgewidth=2, markeredgecolor='#2980b9')
plt.xlabel('Decade', fontsize=12)
plt.ylabel('Average Sentiment Score', fontsize=12)
plt.title('Sentiment Trend Over Decades', fontsize=16, fontweight='bold', pad=20)
plt.grid(True, alpha=0.3)

# Add value labels
for i, row in decade_sentiment.iterrows():
    plt.annotate(f'{row["VADER_Compound"]:.2f}', 
                (row['Decade'], row['VADER_Compound']),
                textcoords="offset points", xytext=(0,10), 
                ha='center', fontsize=9, fontweight='bold')

# 4. Positive Reviews Word Cloud
plt.subplot(2, 3, 4)
positive_reviews = ' '.join(df[df['VADER_Sentiment'] == 'Positive']['Synthetic_Review'].tolist())
wordcloud_pos = WordCloud(width=800, height=400, background_color='black', 
                         colormap='Greens', max_words=100).generate(positive_reviews)
plt.imshow(wordcloud_pos, interpolation='bilinear')
plt.axis('off')
plt.title('Positive Reviews Word Cloud', fontsize=16, fontweight='bold', pad=20)

# 5. Negative Reviews Word Cloud
plt.subplot(2, 3, 5)
negative_reviews = ' '.join(df[df['VADER_Sentiment'] == 'Negative']['Synthetic_Review'].tolist())
if negative_reviews.strip():
    wordcloud_neg = WordCloud(width=800, height=400, background_color='black',
                             colormap='Reds', max_words=100).generate(negative_reviews)
    plt.imshow(wordcloud_neg, interpolation='bilinear')
plt.axis('off')
plt.title('Negative Reviews Word Cloud', fontsize=16, fontweight='bold', pad=20)

# 6. Sentiment by Success Category
plt.subplot(2, 3, 6)
sns.boxplot(x='Success_Category', y='VADER_Compound', data=df,
            palette={'Flop': '#e74c3c', 'Average': '#f39c12', 'Hit': '#2ecc71'},
            width=0.5, linewidth=2)
plt.xlabel('Success Category', fontsize=12)
plt.ylabel('VADER Compound Score', fontsize=12)
plt.title('Sentiment Distribution by Success Category', fontsize=16, fontweight='bold', pad=20)
plt.grid(True, alpha=0.3, axis='y')

plt.tight_layout()
plt.savefig('outputs/charts/sentiment_visuals_comprehensive.png', dpi=300, bbox_inches='tight')
plt.show()
print("\n✅ Sentiment visuals saved to 'outputs/charts/sentiment_visuals_comprehensive.png'")

# Genre Sentiment Analysis
plt.figure(figsize=(16, 10))

# Top 15 genres by count
top_genres = df['Primary_Genre'].value_counts().head(15).index
genre_data = df[df['Primary_Genre'].isin(top_genres)]

# Box plot
plt.subplot(1, 2, 1)
sns.boxplot(x='Primary_Genre', y='VADER_Compound', data=genre_data, 
            palette='viridis', width=0.5)
plt
