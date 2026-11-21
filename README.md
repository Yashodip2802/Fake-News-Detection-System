# 🛡️ Fake News Detector & Rhetoric Analysis Dashboard

A premium, full-stack intelligence application that scans news headlines and body text to detect misinformation and rhetoric patterns. This system combines **Machine Learning (Scikit-Learn LinearSVC)** with **Explainable AI (XAI)** and **NLP Lexicons** to evaluate the credibility and emotional tone of news articles.

---

## ✨ Features

- **🔍 Misinformation Scan**: Predicts article credibility (`REAL` or `FAKE`) with confidence percentages using a Linear Support Vector Classifier.
- **🧠 Explainable AI (XAI)**: Highlights the specific words that contributed to the model's decision, extracting positive (REAL-supporting) and negative (FAKE-supporting) feature coefficients.
- **🎭 Rhetoric & Emotional Manipulation Scanner**: Evaluates the text for standard manipulation tactics and generates severity scores (0-100%):
  - **Fear-mongering**: Triggers from words associated with terror, crises, and threats.
  - **Rage bait**: Triggers from words designed to cause outrage, disgust, and blame.
  - **Clickbait**: Structural markers (e.g., ALL CAPS, exclamation density, common hook phrases).
  - **Sensationalism**: Triggers from extreme hyperbole and exaggerations.
- **🔄 Training Hub**: Upload a custom dataset (CSV format) directly from the dashboard UI to retrain the ML model and stream live logs.
- **📊 Reading Statistics**: Analyzes lexical diversity, character count, word count, and estimated reading time.
- **💎 Dark-themed UI**: Sleek dashboard styled with modern glassmorphism, glowing micro-animations, and responsive grids.

---

## 🛠️ Technology Stack

- **Backend**: Flask (Python 3), Scikit-Learn (LinearSVC, TF-IDF Vectorizer), Pandas, NumPy.
- **Frontend**: Vanilla HTML5, CSS3 Custom Properties (Glow Design System), JavaScript (ES6+), Lucide Icons.
- **Serverless Hosting**: Optimized and configured for deployment on **Vercel** (`vercel.json`).

---

## 📂 Project Structure

```text
├── models/                     # Saved ML model serialized files (ignored by git, generated on training)
│   ├── model.pkl               # Serialized LinearSVC model
│   ├── vectorizer.pkl          # Serialized TF-IDF Vectorizer
│   ├── feature_coefficients.json # Word coefficients mapping for XAI
│   └── model_status.json       # Metadata of the latest training run
├── static/                     # Frontend Client
│   ├── index.html              # Main dashboard HTML structure
│   ├── script.js               # UI controls, API handlers, and charts
│   └── style.css               # Styling (Aesthetics, CSS Glow effects, Layouts)
├── fake news detection.ipynb   # Jupyter Notebook containing early prototyping & research
├── model_trainer.py            # CLI script to train models from local news.csv
├── requirements.txt            # Python environment dependencies
├── server.py                   # Flask server, prediction API, and streaming training endpoint
└── vercel.json                 # Vercel deployment configuration
```

---

## 🚀 Getting Started

### 1. Prerequisites
Make sure you have **Python 3.8+** installed.

### 2. Clone the Repository
```bash
git clone https://github.com/Meghakundu0701/fake-news-detection.git
cd fake-news-detection
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Train the Model (Optional)
To train the model before launching the server, place your dataset as `news.csv` in the root folder and run:
```bash
python model_trainer.py
```
> **Note**: If you skip this, you can train/retrain the model directly from the UI dashboard inside the **Training Hub** tab.

### 5. Start the Server
```bash
python server.py
```
The server will start on `http://127.0.0.1:5000/`. Open this URL in your web browser.

---

## 🔌 API Documentation

### `POST /predict`
Scans news articles for credibility and manipulation metrics.

- **Payload**:
  ```json
  {
    "title": "Unbelievable Breakthrough! Scientists Reveal Truth About Health Scandal",
    "text": "Factual news content goes here..."
  }
  ```
- **Response**:
  ```json
  {
    "prediction": "FAKE",
    "confidence": 88.42,
    "credibility_score": 11.58,
    "xai": {
      "word_weights": { "unbelievable": -0.82, "factual": 0.41 },
      "top_real_indicators": [ { "word": "factual", "weight": 0.41 } ],
      "top_fake_indicators": [ { "word": "unbelievable", "weight": -0.82 } ]
    },
    "emotions": {
      "scores": {
        "fear_mongering": 20,
        "rage_bait": 30,
        "clickbait": 75,
        "sensationalism": 45
      },
      "triggers": {
        "fear_mongering": ["scandal"],
        "rage_bait": ["scandal"],
        "clickbait": ["unbelievable", "reveal"],
        "sensationalism": ["unbelievable"],
        "structural": {
          "title_clickbait_trigger": true,
          "title_caps_trigger": false,
          "title_exclamation_count": 1
        }
      }
    },
    "statistics": {
      "word_count": 350,
      "char_count": 2100,
      "reading_time": 2,
      "lexical_diversity": 62.4
    }
  }
  ```

### `GET /model_status`
Returns the status, accuracy, and dataset properties of the current active model.

- **Response**:
  ```json
  {
    "accuracy": 98.44,
    "vocab_size": 10000,
    "dataset_size": 45757,
    "last_trained": "2026-06-17 12:27:22",
    "dataset_file": "news.csv"
  }
  ```

### `POST /retrain`
Accepts a CSV upload (`file`) containing `label` and `text` columns to run a training job, hot-swapping the active model on success. It returns a **Server-Sent Events (SSE) plain-text stream** of the process logs.

---

## 🧠 Machine Learning Engine

- **Model**: Linear Support Vector Classifier (`LinearSVC`), optimized for sparse high-dimensional text data.
- **Preprocessing**: Cleans URLs, bracketed notations, special characters, and digits, converting text to lowercase.
- **Vectorization**: TF-IDF (Term Frequency-Inverse Document Frequency) using unigrams and bigrams (`ngram_range=(1,2)`) to extract phrasing features, capped at the top 10,000 most significant vocabulary features.
