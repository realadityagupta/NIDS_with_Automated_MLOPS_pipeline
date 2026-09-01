# Network Security - Phishing Detection System

<div align="center">

![Network Security](https://img.shields.io/badge/NetworkSecurity-ML%20Pipeline-brightgreen)
![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Status](https://img.shields.io/badge/Status-Active-success)

**An automated ML-Ops pipeline for detecting phishing URLs using machine learning**

[Overview](#overview) • [Architecture](#architecture) • [Features](#features) • [Installation](#installation) • [Usage](#usage)

</div>

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Why This Project?](#why-this-project)
- [How It Works](#how-it-works)
- [Architecture & Components](#architecture--components)
- [Build Flow & Pipeline](#build-flow--pipeline)
- [Component Map](#component-map)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [API Documentation](#api-documentation)
- [Conclusion](#conclusion)

---

## 📌 Project Overview

**Network Security** is an end-to-end machine learning operations (MLOps) pipeline designed to classify and detect phishing URLs in web traffic. The system automates the entire workflow from data ingestion to model deployment, enabling organizations to identify malicious URLs and protect users from phishing attacks.

### Key Capabilities:
- **Automated ML Pipeline**: Orchestrated data processing, validation, and model training
- **Real-time Prediction**: REST API for predicting whether a URL is phishing or legitimate
- **Model Monitoring**: Integration with MLflow and DagsHub for experiment tracking and model versioning
- **Cloud Integration**: AWS S3 support for model artifact storage and synchronization
- **Production-Ready**: Containerized with Docker for seamless deployment

---

## 🎯 Why This Project?

### The Problem:
Phishing attacks are one of the most prevalent cybersecurity threats, causing billions of dollars in damage annually. Attackers use:
- Fake domain registrations
- SSL certificate manipulation
- URL obfuscation techniques
- DOM-based attacks

Traditional rule-based systems struggle to keep pace with evolving attack patterns.

### The Solution:
This project implements a **data-driven machine learning approach** to:
1. **Learn patterns** from a dataset of 31 phishing indicators
2. **Generalize** to new, unseen URLs using ensemble methods
3. **Scale** through automated pipeline orchestration
4. **Monitor** model performance with built-in MLOps infrastructure
5. **Deploy** with confidence using containerization and cloud storage

---

## 🔧 How It Works

### End-to-End Workflow:

```
Raw Data (MongoDB)
    ↓
[Data Ingestion] → Split into Train/Test
    ↓
[Data Validation] → Check schema, detect drift
    ↓
[Data Transformation] → Encode, scale, impute missing values
    ↓
[Model Training] → Train ensemble models, select best
    ↓
[Model Evaluation] → Track metrics with MLflow
    ↓
[Model Deployment] → Save to final_model/
    ↓
REST API
    ↓
Predictions on New URLs
```

### Phishing Detection Features (31 indicators):

The model analyzes these URL characteristics:

**Domain Features:**
- `having_IP_Address` - Uses IP instead of domain name
- `URL_Length` - Suspicious if overly long
- `Prefix_Suffix` - Contains hyphen in domain
- `having_Sub_Domain` - Multiple subdomains present
- `SSLfinal_State` - SSL certificate status
- `Domain_registeration_length` - Domain age
- `DNSRecord` - DNS record presence

**URL Structure:**
- `having_At_Symbol` - @ symbol used to obfuscate domain
- `Shortining_Service` - URL shortening service used
- `double_slash_redirecting` - Double slash for redirection
- `Abnormal_URL` - Non-standard URL format
- `Redirect` - URL redirect behavior

**Web Content Features:**
- `Favicon` - Custom favicon presence
- `port` - Non-standard port used
- `HTTPS_token` - Inconsistent HTTPS usage
- `Request_URL` - Requests from different domain
- `URL_of_Anchor` - Anchor links to different domain
- `Links_in_tags` - External links in meta tags
- `SFH` (Server Form Handler) - Form submission endpoint
- `Submitting_to_email` - Mail link in form
- `on_mouseover` - Mouseover event handlers
- `RightClick` - Right-click disabled
- `popUpWidnow` - Popup windows used
- `Iframe` - Iframes embedded

**Reputation Features:**
- `web_traffic` - Traffic statistics
- `Page_Rank` - Google PageRank score
- `Google_Index` - Google indexing status
- `Links_pointing_to_page` - Backlink count
- `age_of_domain` - Domain registration age
- `Statistical_report` - Statistical phishing reports

**Target:**
- `Result` - Classification (Legitimate: 1, Phishing: -1)

---

## 🏗️ Architecture & Components

### Component Overview:

```
┌─────────────────────────────────────────────────────┐
│          NetworkSecurity Package (ML Pipeline)      │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │   1. Data Ingestion Component                │   │
│  │   ─ MongoDB → Pandas DataFrame               │   │
│  │   ─ Train/Test split (80/20)                 │   │
│  └──────────────────────────────────────────────┘   │
│                     ↓                                │
│  ┌──────────────────────────────────────────────┐   │
│  │   2. Data Validation Component               │   │
│  │   ─ Schema validation (YAML)                 │   │
│  │   ─ Data drift detection                     │   │
│  │   ─ Separate valid/invalid data              │   │
│  └──────────────────────────────────────────────┘   │
│                     ↓                                │
│  ┌──────────────────────────────────────────────┐   │
│  │   3. Data Transformation Component           │   │
│  │   ─ Handle missing values (KNN Imputation)   │   │
│  │   ─ Feature encoding & scaling               │   │
│  │   ─ Save preprocessing object                │   │
│  └──────────────────────────────────────────────┘   │
│                     ↓                                │
│  ┌──────────────────────────────────────────────┐   │
│  │   4. Model Training Component                │   │
│  │   ─ Train multiple algorithms:               │   │
│  │     • Logistic Regression                    │   │
│  │     • Random Forest                          │   │
│  │     • Gradient Boosting                      │   │
│  │     • Decision Tree                          │   │
│  │     • AdaBoost                               │   │
│  │   ─ Hyperparameter tuning                    │   │
│  │   ─ Model evaluation & selection             │   │
│  │   ─ MLflow tracking                          │   │
│  └──────────────────────────────────────────────┘   │
│                     ↓                                │
│  ┌──────────────────────────────────────────────┐   │
│  │   5. Model Estimator                         │   │
│  │   ─ Combines preprocessor + trained model    │   │
│  │   ─ Handles prediction pipeline              │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │   6. Cloud Integration (S3)                  │   │
│  │   ─ Sync models to AWS S3                    │   │
│  │   ─ Versioning & backup                      │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │   7. Logging & Exception Handling            │   │
│  │   ─ Custom exception class                   │   │
│  │   ─ Logger instance                          │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘
                         ↓
          ┌──────────────────────────────┐
          │   FastAPI REST Application   │
          │   ─ Training endpoint        │
          │   ─ Prediction endpoint      │
          │   ─ CORS middleware support  │
          └──────────────────────────────┘
```

### Core Modules:

| Module | Path | Purpose |
|--------|------|---------|
| **Data Ingestion** | `components/data_ingestion.py` | Fetch data from MongoDB, perform train-test split |
| **Data Validation** | `components/data_validation.py` | Validate schema, detect data drift |
| **Data Transformation** | `components/data_transformation.py` | Handle missing values, scale features |
| **Model Training** | `components/model_trainer.py` | Train & evaluate multiple ML algorithms |
| **Estimator** | `utils/ml_utils/model/estimator.py` | Production model wrapper |
| **Classification Metrics** | `utils/ml_utils/metric/classification_metric.py` | Compute F1, Precision, Recall |
| **Utilities** | `utils/main_utils/utils.py` | Helper functions (save/load objects) |
| **Training Pipeline** | `pipeline/training_pipeline.py` | Orchestrate full pipeline |
| **Batch Prediction** | `pipeline/batch_prediction.py` | Batch prediction on datasets |
| **Config Entity** | `entity/config_entity.py` | Configuration classes |
| **Artifact Entity** | `entity/artifact_entity.py` | Artifact classes for component outputs |
| **Exception Handling** | `exceptionHandling/exception.py` | Custom exception class |
| **Logger** | `logging/logger.py` | Logging utility |

---

## 🚀 Build Flow & Pipeline

### Execution Steps:

#### **Step 1: Data Ingestion**
```python
# Retrieve phishing data from MongoDB
# Input: MongoDB connection URL, database name, collection name
# Process:
#   - Connect to MongoDB
#   - Load collection as Pandas DataFrame
#   - Remove MongoDB _id field
#   - Replace "na" strings with NaN
#   - Split into train (80%) and test (20%)
# Output: DataIngestionArtifact
#   - train.csv → artifacts/data_ingestion/ingested/
#   - test.csv → artifacts/data_ingestion/ingested/
#   - Full data → artifacts/data_ingestion/feature_store/
```

#### **Step 2: Data Validation**
```python
# Validate data against schema and detect drift
# Input: DataIngestionArtifact, schema.yaml
# Process:
#   - Load schema from data_schema/schema.yaml
#   - Validate column names and data types
#   - Check for missing values
#   - Detect data drift using statistical tests
#   - Separate valid and invalid records
# Output: DataValidationArtifact
#   - valid data → artifacts/data_validation/validated/
#   - invalid data → artifacts/data_validation/invalid/
#   - drift report → artifacts/data_validation/drift_report/report.yaml
```

#### **Step 3: Data Transformation**
```python
# Transform and preprocess data
# Input: DataValidationArtifact
# Process:
#   - Separate features (X) and target (y)
#   - Handle missing values using KNN Imputer
#   - Encode categorical variables (if any)
#   - Scale numerical features using StandardScaler
#   - Save preprocessing pipeline as .pkl
# Output: DataTransformationArtifact
#   - transformed_train → .npy (NumPy array)
#   - transformed_test → .npy (NumPy array)
#   - preprocessor.pkl → artifacts/data_transformation/transformed_object/
```

#### **Step 4: Model Training**
```python
# Train and evaluate multiple ML algorithms
# Input: DataTransformationArtifact
# Process:
#   - Load transformed training data
#   - Train 5 algorithms:
#     • LogisticRegression(max_iter=1000)
#     • RandomForestClassifier(verbose=1)
#     • GradientBoostingClassifier(verbose=1)
#     • DecisionTreeClassifier()
#     • AdaBoostClassifier()
#   - Perform hyperparameter tuning (GridSearchCV)
#   - Evaluate on test set
#   - Log metrics to MLflow (F1, Precision, Recall)
#   - Select best model based on F1-score
# Output: ModelTrainerArtifact
#   - model.pkl → artifacts/model_trainer/trained_model/
#   - Metrics logged to DagsHub/MLflow
```

#### **Step 5: Model Deployment**
```python
# Save model to production location
# Input: ModelTrainerArtifact
# Process:
#   - Copy trained model to final_model/model.pkl
#   - Copy preprocessor to final_model/preprocessor.pkl
#   - Sync artifacts to AWS S3 (optional)
# Output: Production-ready models in final_model/
```

### Pipeline Execution Diagram:

```
┌─────────────────────────────────────────────────────────────┐
│                  Training Pipeline Flow                     │
└─────────────────────────────────────────────────────────────┘

START
  │
  ├─→ Initialize TrainingPipelineConfig
  │     └─ Set artifact directory with timestamp
  │
  ├─→ start_data_ingestion()
  │     ├─ Load phisingData.csv from MongoDB
  │     ├─ Split into train (80%) & test (20%)
  │     └─ Save → artifacts/data_ingestion/ingested/
  │
  ├─→ start_data_validation()
  │     ├─ Validate schema against schema.yaml
  │     ├─ Check data types and columns
  │     ├─ Detect drift
  │     └─ Save → artifacts/data_validation/validated/
  │
  ├─→ start_data_transformation()
  │     ├─ Impute missing values (KNN)
  │     ├─ Scale features (StandardScaler)
  │     └─ Save → artifacts/data_transformation/transformed/
  │
  ├─→ start_model_trainer()
  │     ├─ Load transformed data
  │     ├─ Train 5 algorithms
  │     ├─ Evaluate models
  │     ├─ Log to MLflow
  │     └─ Save best model → artifacts/model_trainer/trained_model/
  │
  ├─→ start_model_pusher()
  │     ├─ Copy model to final_model/model.pkl
  │     ├─ Copy preprocessor to final_model/preprocessor.pkl
  │     └─ Sync to S3 (optional)
  │
  └─→ END (Success/Failure notification)
```

---

## 🗺️ Component Map

```
📦 networkSecurity/
│
├── 📂 components/              # Core ML pipeline components
│   ├── data_ingestion.py       # Fetch & split data
│   ├── data_validation.py      # Validate schema & detect drift
│   ├── data_transformation.py  # Impute, encode, scale features
│   └── model_trainer.py        # Train & evaluate models
│
├── 📂 pipeline/                # High-level orchestration
│   ├── training_pipeline.py    # Main pipeline orchestrator
│   └── batch_prediction.py     # Batch prediction interface
│
├── 📂 entity/                  # Configuration & artifact classes
│   ├── config_entity.py        # Config classes for each component
│   └── artifact_entity.py      # Artifact classes for outputs
│
├── 📂 utils/                   # Utility functions
│   ├── 📂 main_utils/
│   │   └── utils.py            # Save/load objects, evaluate models
│   └── 📂 ml_utils/
│       ├── 📂 model/
│       │   └── estimator.py    # Production model wrapper
│       └── 📂 metric/
│           └── classification_metric.py  # F1, Precision, Recall
│
├── 📂 cloud/                   # Cloud integration
│   └── s3_syncer.py            # AWS S3 sync utility
│
├── 📂 constant/                # Constants & configuration
│   └── 📂 training_pipeline/
│       └── __init__.py         # Pipeline constants
│
├── 📂 exceptionHandling/       # Custom exceptions
│   └── exception.py
│
├── 📂 logging/                 # Logging utility
│   └── logger.py
│
└── 📂 __init__.py
```

---

## 🛠️ Tech Stack

| Category | Technology | Purpose |
|----------|-----------|---------|
| **Language** | Python 3.8+ | Core programming language |
| **ML/Data** | scikit-learn | Training algorithms, preprocessing |
| | pandas | Data manipulation |
| | NumPy | Numerical computing |
| **Database** | MongoDB | Data storage (with MongoDB Atlas) |
| **API** | FastAPI | REST API framework |
| | Uvicorn | ASGI web server |
| | Starlette | Web framework (FastAPI dependency) |
| **MLOps** | MLflow | Experiment tracking & model registry |
| | DagsHub | ML collaboration platform |
| **Cloud** | AWS S3 | Model artifact storage |
| **Containerization** | Docker | Production deployment |
| **Utilities** | python-dotenv | Environment variable management |
| | PyYAML | Schema configuration |
| | dill | Object serialization |
| | certifi | SSL certificate verification |

---

## 📋 Prerequisites

### System Requirements:
- **Python 3.8 or higher**
- **pip** or **conda** package manager
- **Docker** (for containerized deployment)
- **Git** (for version control)

### External Services (Required):
1. **MongoDB Atlas Account**
   - Create free cluster at https://www.mongodb.com/cloud/atlas
   - Obtain connection string (MONGODB_URL_KEY)

2. **AWS Account** (Optional, for S3 integration)
   - AWS Access Key ID
   - AWS Secret Access Key
   - S3 bucket for model storage

3. **DagsHub Account** (Optional, for MLOps)
   - DagsHub MLflow remote URI
   - Repository credentials

### Environment Variables:
Create a `.env` file in the project root:
```env
MONGODB_URL_KEY=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/?retryWrites=true&w=majority

# Optional: AWS S3 credentials
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_BUCKET_NAME=your_bucket_name

# Optional: DagsHub credentials
DAGSHUB_REPO_OWNER=your_username
DAGSHUB_REPO_NAME=your_repo_name
```

---

## 📦 Installation & Setup

### 1. Clone the Repository:
```bash
git clone https://github.com/realadityagupta/NIDS_with_Automated_MLOPS_pipeline.git
cd networksecurity
```

### 2. Create Virtual Environment:
```bash
# Using venv
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Using conda
conda create -n networksecurity python=3.8
conda activate networksecurity
```

### 3. Install Dependencies:
```bash
pip install -r requirements.txt

# Or install in development mode:
pip install -e .
```

### 4. Set Up Environment:
```bash
# Create .env file with MongoDB connection
echo "MONGODB_URL_KEY=your_mongodb_uri" > .env
```

### 5. Prepare Data:
```bash
# Ensure phisingData.csv is in Network_Data/ directory
# Or configure MongoDB connection with phishing dataset
```

---

## 🎯 Usage

### Option 1: Run Training Pipeline (main.py)
```bash
python main.py
```
This executes the full ML pipeline:
- Data ingestion → Validation → Transformation → Model Training

### Option 2: Run via FastAPI Web Application (app.py)

#### Start the server:
```bash
python app.py
# or
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```

#### Access the API:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

#### Training Endpoint:
```bash
curl -X GET "http://localhost:8000/train"
```

#### Prediction Endpoint:
```bash
# Upload CSV file for batch prediction
curl -X POST "http://localhost:8000/predict" \
  -F "file=@test.csv"
```

### Option 3: Docker Deployment
```bash
# Build Docker image
docker build -t networksecurity:latest .

# Run container
docker run -p 8000:8000 \
  -e MONGODB_URL_KEY="your_mongodb_uri" \
  networksecurity:latest
```

### Example Prediction:
```python
from networkSecurity.utils.main_utils.utils import load_object
from networkSecurity.utils.ml_utils.model.estimator import NetworkModel
import pandas as pd

# Load trained model and preprocessor
preprocessor = load_object("final_model/preprocessor.pkl")
model = load_object("final_model/model.pkl")

# Create NetworkModel wrapper
network_model = NetworkModel(preprocessor=preprocessor, model=model)

# Load test data
df = pd.read_csv("test.csv")

# Make predictions
predictions = network_model.predict(df)
print(predictions)  # Output: [1, -1, 1, ...] (1 = Legitimate, -1 = Phishing)
```

---

## 📁 Project Structure

```
networksecurity/
├── app.py                          # FastAPI application
├── main.py                         # Training pipeline entry point
├── push_data.py                    # MongoDB data upload utility
├── test_mongodb.py                 # MongoDB connection test
├── setup.py                        # Package setup configuration
├── requirements.txt                # Python dependencies
├── Dockerfile                      # Docker configuration
├── README.md                       # This file
│
├── networkSecurity/                # Main package
│   ├── __init__.py
│   │
│   ├── components/                 # ML pipeline components
│   │   ├── data_ingestion.py
│   │   ├── data_validation.py
│   │   ├── data_transformation.py
│   │   └── model_trainer.py
│   │
│   ├── pipeline/                   # Pipeline orchestration
│   │   ├── training_pipeline.py
│   │   └── batch_prediction.py
│   │
│   ├── entity/                     # Config & artifact classes
│   │   ├── config_entity.py
│   │   └── artifact_entity.py
│   │
│   ├── utils/                      # Utility functions
│   │   ├── main_utils/
│   │   │   └── utils.py
│   │   └── ml_utils/
│   │       ├── model/
│   │       │   └── estimator.py
│   │       └── metric/
│   │           └── classification_metric.py
│   │
│   ├── cloud/                      # Cloud integration
│   │   └── s3_syncer.py
│   │
│   ├── constant/                   # Constants
│   │   └── training_pipeline/
│   │       └── __init__.py
│   │
│   ├── exceptionHandling/          # Exception handling
│   │   └── exception.py
│   │
│   └── logging/                    # Logging
│       └── logger.py
│
├── data_schema/                    # Schema configuration
│   └── schema.yaml
│
├── Network_Data/                   # Input data
│   └── phisingData.csv
│
├── Artifacts/                      # Generated artifacts (timestamped)
│   └── mm_dd_yyyy_hh_mm_ss/
│       ├── data_ingestion/
│       ├── data_validation/
│       ├── data_transformation/
│       └── model_trainer/
│
├── final_model/                    # Production models
│   ├── model.pkl
│   └── preprocessor.pkl
│
├── prediction_output/              # Prediction results
│   └── output.csv
│
├── valid_data/                     # Validated test data
│   └── test.csv
│
├── templates/                      # HTML templates
│   └── table.html
│
├── notebooks/                      # Jupyter notebooks
│
└── NetworkSecurity.egg-info/       # Package metadata
```

---

## 🔌 API Documentation

### Base URL:
```
http://localhost:8000
```

### Endpoints:

#### 1. **Training Endpoint**
```
GET /train
```
**Description**: Trigger the full ML pipeline training

**Response** (Success):
```json
{
  "message": "Training is successful"
}
```

**Response** (Error):
```json
{
  "detail": "Error message"
}
```

#### 2. **Prediction Endpoint**
```
POST /predict
```
**Description**: Upload CSV file and get phishing predictions

**Parameters**:
- `file` (multipart/form-data): CSV file with 30 features (no Result column)

**Response** (Success):
- Returns HTML table with predictions
- Saves results to `prediction_output/output.csv`
- Prediction column: `1` (Legitimate), `-1` (Phishing)

**Example Request**:
```bash
curl -X POST "http://localhost:8000/predict" \
  -H "accept: text/html" \
  -F "file=@test.csv"
```

#### 3. **Auto Documentation**
```
GET /docs          # Swagger UI
GET /redoc         # ReDoc
```

---

## 📊 Model Performance

The system trains and compares 5 classification algorithms:

| Algorithm | Speed | Accuracy | Robustness |
|-----------|-------|----------|------------|
| Logistic Regression | ⚡⚡⚡ | ⭐⭐ | ⭐⭐⭐ |
| Decision Tree | ⚡⚡⚡ | ⭐⭐ | ⭐⭐ |
| Random Forest | ⚡⚡ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Gradient Boosting | ⚡ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| AdaBoost | ⚡⚡ | ⭐⭐⭐ | ⭐⭐⭐ |

**Evaluation Metrics**:
- **F1-Score**: Harmonic mean of precision and recall
- **Precision**: True positives / (True positives + False positives)
- **Recall**: True positives / (True positives + False negatives)

---

## 🔒 Security Considerations

1. **Environment Variables**: Store sensitive credentials in `.env`, never commit
2. **MongoDB**: Use MongoDB Atlas with network access restrictions
3. **AWS S3**: Use IAM roles with minimal required permissions
4. **CORS**: API allows all origins (`*`), restrict in production
5. **Input Validation**: Always validate and sanitize user inputs

---

## 🐛 Troubleshooting

### Issue: MongoDB Connection Failed
```
Error: Unable to connect to MongoDB
```
**Solution**: 
- Verify MONGODB_URL_KEY in .env
- Check MongoDB Atlas network access settings
- Ensure cluster is running

### Issue: Missing Dependencies
```
Error: ModuleNotFoundError: No module named 'sklearn'
```
**Solution**:
```bash
pip install -r requirements.txt
```

### Issue: Port 8000 Already in Use
```bash
# Use different port
uvicorn app:app --port 8080
```

---

## 📈 Future Enhancements

- [ ] Real-time model monitoring and retraining triggers
- [ ] Explainable AI (SHAP) for prediction interpretability
- [ ] Kubernetes deployment orchestration
- [ ] Advanced feature engineering with domain knowledge
- [ ] Multi-model ensemble with voting mechanisms
- [ ] A/B testing framework for model versions
- [ ] GraphQL API alternative to REST
- [ ] Mobile app for predictions

---

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the MIT License - see LICENSE file for details.

---

## 👤 Author

**Aditya Gupta**
- Email: ajaygupta995566@gmail.com
- GitHub: [@realadityagupta](https://github.com/realadityagupta)

---

## 🙏 Acknowledgments

- **Data Source**: Phishing URL dataset
- **MLOps Framework**: MLflow + DagsHub
- **Inspiration**: Modern ML pipeline best practices

---

## 📞 Support

For issues, questions, or suggestions:
1. Open an issue on GitHub
2. Check existing issues for solutions
3. Email: ajaygupta995566@gmail.com

---

## 🗂️ Related Documentation

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [scikit-learn Guide](https://scikit-learn.org/stable/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [MLflow Documentation](https://mlflow.org/docs/latest/)
- [AWS S3 Documentation](https://docs.aws.amazon.com/s3/)

---

## 📝 Conclusion

**Network Security** demonstrates an enterprise-grade ML pipeline for cybersecurity applications. By combining:
- **Automated data processing** via orchestrated components
- **Ensemble machine learning** with multiple algorithms
- **MLOps best practices** using MLflow and cloud integration
- **Production readiness** through containerization and REST APIs

This system provides a robust, scalable solution for phishing URL detection. The modular architecture allows easy extension with new features, algorithms, or data sources while maintaining code quality and reproducibility.

The project serves as a reference implementation for building production-ready ML systems that can:
✅ Process data at scale  
✅ Train models reliably  
✅ Monitor performance continuously  
✅ Deploy confidently to production  

**Deploy this system to protect your organization from phishing attacks today!**

---

<div align="center">

**Last Updated**: September 2026  
**Version**: 1.0.0  
**Status**: ✅ Production Ready

[⬆ Back to Top](#network-security---phishing-detection-system)

</div>
