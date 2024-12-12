# XRPrism Data Architecture and Deployment Documentation

## **1. Data Ingestion Layer**
The Data Ingestion Layer is responsible for collecting transaction data from the public XRPScan API. This data is used for downstream AI training and prediction processes.

### Design Overview
**File:** `collector.py`

- **Core Functionality:**  
  - Fetches real-time transaction data from XRPScan for a predefined set of accounts.
  - Supports retries for robust data fetching.
  - Saves JSON-formatted transaction data to the `/data/raw` directory.

### Features & Enhancements
- **Account Management:**  
  - Account addresses are specified through environment variables (`ACCOUNT`).
  - Multiple accounts are supported.

- **Robust Fetching:**  
  - Configurable `FETCH_DURATION` and `SLEEP_INTERVAL` determine fetching and cooldown intervals.
  - Uses the `requests` library with retry support for stable data collection.

- **Data Integrity:**  
  - Atomic file writes using `fcntl` ensure no partial file corruption.
  - Merges new data with existing records, removing duplicates.

### Configurable Parameters

| Environment Variable | Description                    | Default Value |
|----------------------|--------------------------------|---------------|
| `ACCOUNT`            | List of XRP accounts          | Test Accounts  |
| `FETCH_DURATION`     | Fetching cycle duration       | 10 seconds     |
| `SLEEP_INTERVAL`     | Sleep time between cycles     | 30 seconds     |
| `OUTPUT_DIR`         | Directory to save JSON        | `/data/raw`    |

---

## **2. Data Formatting Layer**
The Data Formatting Layer processes raw JSON data collected from the XRPScan API into a structured CSV format.

### Design Overview
**File:** `format.py`

- **Core Functionality:**  
  - Reads JSON transaction files from the `/data/raw` directory.  
  - Extracts and formats relevant transaction details into a structured CSV file.
  - Saves the processed data to `/data/processed/processed_transactions.csv`.

### Features & Enhancements

- **Data Extraction:**  
  - Extracts transaction attributes such as `currency`, `issuer`, `value`, and `Destination`.  

- **Data Processing Workflow:**  
  - Opens raw JSON files, extracts fields, handles errors, writes to CSV.

### Configurable Parameters

| Environment Variable | Description                    | Default Value                      |
|----------------------|--------------------------------|------------------------------------|
| `OUTPUT_DIR`         | Directory for raw JSON files   | `/data/raw`                        |
| `OUTPUT_CSV`         | Processed CSV file path        | `/data/processed_transactions.csv` |

---

## **3. AI Model Training & Management Layer**

### Design Overview
**File:** `trainer.py`

- **Core Functionality:**  
  - Extracts features from processed transaction data.
  - Trains a PyTorch-based neural network (`PrismModel`).
  - Automatically versions models and maintains a model directory.

### Model Training Workflow
- **Data Source:** `/data/processed/processed_transactions.csv`
- **Features Extracted:**  
  - Total transaction value, average value, count, volatility, growth rate, rolling averages.

### Training Parameters

| Parameter              | Value                     |
|----------------------|-----------------------------|
| **Batch Size**       | 128 (train), 64 (test)      |
| **Loss Function**    | Custom `FocalLoss`          |
| **Optimizer**        | `AdamW` (L2 regularization) |
| **Learning Rate**    | 0.00005                     |
| **Scheduler**        | `ReduceLROnPlateau`         |
| **Epochs**           | 100                         |

---

## **4. AI Inference & Prediction Layer**

### Design Overview
**Files:**  
- `ai_inference.py`  
- `model_inspection.py`  
- `prediction_test.py`  

### Real-Time Inference Workflow
- **Model Loading:** Automatically loads the latest trained model and associated scaler.

### Prediction Logic
- **Input Data:** Processed transactions from `/data/processed_transactions.csv`.  
- **Data Preparation:** Extracts relevant features, applies scaling, and runs inference using PyTorch.  

### Supporting Tools & Functions

| File                  | Purpose                 |
|-----------------------|-------------------------|
| `model_inspection.py` | Inspect model details   |
| `prediction_test.py`  | Test predictions        |

---

## **5. Deployment Layer**

### Deployment Architecture
**Files:**  
- `docker-compose.yml`  
- `format.Dockerfile`  
- `prism_collector.Dockerfile`  
- `tasks.py`  

### Dockerfile Breakdown

| File                         | Base Image                | Description                             |
|------------------------------|---------------------------|-----------------------------------------|
| `prism_collector.Dockerfile` | `python:3.13-slim`        | Runs data collection service.           |
| `format.Dockerfile`          | `python:3.13-slim`        | Runs data formatting service.           |

### Deployment Workflow Using Invoke (`tasks.py`)

| Command                 | Description                 |
|-------------------------|-----------------------------|
| `invoke train`          | Trains the AI model.        |
| `invoke infer`          | Runs inference on the model.|
| `invoke test`           | Runs model tests.           |
| `invoke docker_up`      | Starts Docker containers.   |
| `invoke docker_down`    | Stops Docker containers.    |
| `invoke docker_rebuild` | Rebuilds Docker containers. |

### Environment Variables

| Variable              | Description                | Default Value                      |
|-----------------------|----------------------------|------------------------------------|
| `ACCOUNT`             | XRP accounts to monitor    | Test accounts                      |
| `OUTPUT_DIR`          | Directory for raw JSON     | `/data/raw`                        |
| `FETCH_DURATION`      | Fetch duration             | `10` seconds                       |
| `SLEEP_INTERVAL`      | Sleep interval             | `30` seconds                       |
| `MODEL_BASE_PATH`     | Model saving path          | `/models/`                         |
| `CSV_FILE_PATH`       | Processed CSV path         | `/data/processed_transactions.csv` |

