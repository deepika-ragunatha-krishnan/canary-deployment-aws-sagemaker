# Canary Deployment with Guardrails and Shadow Testing on AWS SageMaker

This project demonstrates an end-to-end deployment strategy using Amazon SageMaker. It simulates real-world model deployment scenarios including real-time hosting, canary rollout with rollback, and shadow testing — all on a **single SageMaker endpoint**.

📘 **Notebook**: `My Project Inference endpoint using-canary traffic shifting.ipynb`  
🔁 **Endpoint name**: `weather-prediction-endpoint`

---

## ✅ FINAL SAGEMAKER DEPLOYMENT PLAN
### 🧠 Single Endpoint, Multiple Configurations + Models

---

### 🟦 TASK 0: SETUP & INITIALIZATION

**Deliverables:**
- Library imports and AWS client setup
- Define:
  - Endpoint name: `weather-prediction-endpoint`
  - S3 paths for:
    - `005-model.tar.gz`
    - `009-model.tar.gz`
    - `test_linear.csv`
- Helper functions for logging, plotting, and inference
- Test payloads pulled from S3

---

### 🟦 TASK 1: REAL-TIME DEPLOYMENT (VERSION 1)

**Resources Used:**

| Model Name         | Artifact          | Algorithm      |
|--------------------|-------------------|----------------|
| weather-model-v1-1 | 005-model.tar.gz  | linear-learner |
| weather-model-v1-2 | 005-model.tar.gz  | linear-learner |
| weather-model-v1-3 | 005-model.tar.gz  | linear-learner |

| Endpoint Config Name   | Model Used         |
|------------------------|--------------------|
| weather-config-v1-1    | weather-model-v1-1 |
| weather-config-v1-2    | weather-model-v1-2 |
| weather-config-v1-3    | weather-model-v1-3 |

- ✅ **Endpoint deployed**: `weather-prediction-endpoint`
- 🧪 **Inference tested**
- 📊 **Metrics plotted in CloudWatch**:  
  `5XXError`, `4XXError`, `ModelLatency`

---

### 🟦 TASK 2: GUARDRAIL DEPLOYMENT WITH CANARY & ROLLBACK

**Resources Used:**

| Model Name         | Artifact          | Algorithm      | Purpose         |
|--------------------|-------------------|----------------|-----------------|
| weather-model-v2-1 | 009-model.tar.gz  | linear-learner | Canary ✅ success |
| weather-model-v2-2 | 009-model.tar.gz  | ❌ xgboost     | Canary ❌ fail   |
| weather-model-v2-3 | 009-model.tar.gz  | linear-learner | Canary ✅ success |

| Endpoint Config Name   | Model Used         | Outcome         |
|------------------------|--------------------|-----------------|
| weather-config-v2-1    | weather-model-v2-1 | ✅ Success       |
| weather-config-v2-2    | weather-model-v2-2 | ❌ Failure       |
| weather-config-v2-3    | weather-model-v2-3 | ✅ Success       |

- Canary updates done using `update_endpoint(...)`
- Canary rollout strategy: 1-instance traffic
- Rollback triggered on V2-2 due to algorithm mismatch
- 📊 Metrics plotted for rollout and rollback analysis

---

### 🟦 TASK 3: SHADOW TESTING (VERSION 2 AS SHADOW)

**Resources Used:**

| Production Model   | Shadow Model       | Endpoint Config        |
|--------------------|--------------------|-------------------------|
| weather-model-v1-1 | weather-model-v2-1 | weather-config-shadow-1 |
| weather-model-v1-2 | weather-model-v2-2 | weather-config-shadow-2 |
| weather-model-v1-3 | weather-model-v2-3 | weather-config-shadow-3 |

- ✅ Shadow variants created using `ShadowProductionVariants`
- Only production model prediction is returned
- Shadow predictions are logged to CloudWatch for comparison
- 📊 Metrics analyzed for:
  - Errors
  - Latency
  - Shadow vs Prod prediction quality

---

### 🟩 TASK 4: CLEANUP (Optional)

**Steps:**
- Delete all:
  - Models
  - Endpoint configurations
  - The single endpoint
  - CloudWatch alarms
- 📋 Logs printed to confirm successful deletion

---

### 📘 Final Resource Recap

| Resource Type     | Count | Notes                                |
|-------------------|-------|----------------------------------------|
| Endpoint          | 1     | Same endpoint used for all experiments |
| Models            | 9     | 3 (V1) + 3 (V2/canary) + 3 (shadow)    |
| Endpoint Configs  | 9     | 3 (V1) + 3 (V2) + 3 (shadow)           |
| CloudWatch Alarms | 3     | For 5XXError, 4XXError, ModelLatency   |

---

## 📦 Technologies Used
- **Amazon SageMaker**
- **Amazon S3**
- **AWS CloudWatch**
- **Python** (Boto3, Matplotlib, Pandas)

---

## 🧪 Notebook Output Summary
You can explore the full execution, plots, and error metrics in the Jupyter notebook:
**`My Project Inference endpoint using-canary traffic shifting.ipynb`**
