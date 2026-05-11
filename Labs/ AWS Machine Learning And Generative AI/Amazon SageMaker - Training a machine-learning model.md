
# Lab: Training a Machine Learning Model with Amazon SageMaker

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon SageMaker — Data Splitting, XGBoost Training, Jupyter Notebook  
**Duration:** ~30 min

---

## Overview

Used Amazon SageMaker to train a machine learning classification model on a biomechanical vertebral column dataset. The workflow covered the full supervised learning pipeline: data splitting → model training → evaluation setup.

**Algorithm used:** XGBoost (Extreme Gradient Boosting) — a high-performance ensemble method for structured/tabular data classification and regression.

---

## Dataset

**Biomechanical Vertebral Column Dataset**  
Each record represents measurements from a patient's spine, with the target variable classifying the condition as normal or abnormal.

| Feature examples | Target |
|---|---|
| Pelvic incidence, pelvic tilt, lumbar lordosis angle, etc. | Normal / Abnormal (binary classification) |

---

## Task 1 — Access SageMaker Notebook Instance

In **SageMaker Console → Notebook instances → MyNotebook → Open JupyterLab**

Opened: `en_us/3_4-machinelearning.ipynb`  
Kernel: `conda_python3`

> **Screenshot:** JupyterLab — notebook file open in SageMaker

![jupyter-open](screenshots/01-jupyter-open.png)

---

## Task 2 — Split Data into Train / Validation / Test Sets

```python
import pandas as pd
from sklearn.model_selection import train_test_split

# Load dataset
df = pd.read_csv('vertebral_column_data.csv')

# Split: 70% train, 15% validation, 15% test
train, temp = train_test_split(df, test_size=0.30, random_state=42)
validation, test = train_test_split(temp, test_size=0.50, random_state=42)

print(f"Train: {len(train)} rows")
print(f"Validation: {len(validation)} rows")
print(f"Test: {len(test)} rows")
```

**Why three splits?**

| Split | Purpose |
|---|---|
| **Train** (70%) | Model learns patterns from this data |
| **Validation** (15%) | Tunes hyperparameters; detects overfitting during training |
| **Test** (15%) | Final unbiased evaluation after training is complete |

> **Screenshot:** Notebook — data split output showing row counts for each set

![data-split](screenshots/02-data-split.png)

---

## Task 3 — Train XGBoost Model in SageMaker

```python
import boto3
import sagemaker
from sagemaker import image_uris
from sagemaker.inputs import TrainingInput

session = sagemaker.Session()
role = sagemaker.get_execution_role()
region = boto3.Session().region_name
bucket = session.default_bucket()

# Get XGBoost container image URI
xgboost_container = image_uris.retrieve('xgboost', region, version='1.5-1')

# Configure the estimator
xgb_model = sagemaker.estimator.Estimator(
    image_uri=xgboost_container,
    role=role,
    instance_count=1,
    instance_type='ml.m4.xlarge',
    output_path=f's3://{bucket}/output',
    sagemaker_session=session
)

# Set hyperparameters
xgb_model.set_hyperparameters(
    objective='binary:logistic',
    num_round=100,
    max_depth=5,
    eta=0.2,
    subsample=0.8
)

# Point to training and validation data in S3
train_input = TrainingInput(f's3://{bucket}/train/', content_type='csv')
val_input   = TrainingInput(f's3://{bucket}/validation/', content_type='csv')

# Launch training job
xgb_model.fit({'train': train_input, 'validation': val_input})
```

SageMaker launched a **managed training job** on a separate `ml.m4.xlarge` instance. Training logs streamed directly into the notebook.

> **Screenshot:** Notebook — training job output showing loss metrics per round

![training-output](screenshots/03-training-output.png)

> **Screenshot:** SageMaker Console → Training jobs — job status = **Completed**

![training-job-complete](screenshots/04-training-job-complete.png)

---

## SageMaker Training Architecture

```
JupyterLab Notebook (MyNotebook)
        │
        │  fit() call
        ▼
SageMaker Training Job (ml.m4.xlarge)
        │                        │
   Reads data from S3       Writes model artifact to S3
   (train + validation)     (output/model.tar.gz)
        │
   XGBoost container
   (managed by AWS)
```

The notebook instance and the training instance are **separate** — SageMaker spins up a dedicated compute instance for training, then terminates it when done. This is more cost-efficient than running training on the notebook instance itself.

---

## Key XGBoost Hyperparameters

| Parameter | Value | Meaning |
|---|---|---|
| `objective` | `binary:logistic` | Binary classification with probability output |
| `num_round` | 100 | Number of boosting iterations |
| `max_depth` | 5 | Maximum tree depth per round |
| `eta` | 0.2 | Learning rate (step size shrinkage) |
| `subsample` | 0.8 | Fraction of rows used per tree (reduces overfitting) |

---

## Reflection

In this lab I learned how to:

- **Split a dataset into three parts** — understanding why a separate validation set is needed during training (to tune hyperparameters) versus a held-out test set (for final honest evaluation); using only one split would lead to overfitting or optimistic accuracy estimates
- **Use SageMaker's managed training infrastructure** — the `Estimator.fit()` call provisions a separate training instance, runs the XGBoost container, streams logs to the notebook, and saves the model artifact to S3, all without manual infrastructure management
- **Configure XGBoost hyperparameters** — each parameter controls a different aspect of the model's learning behavior; `eta` controls how aggressively the model learns, `max_depth` controls model complexity, and `subsample` reduces overfitting by using random row subsets
- **Work with SageMaker's data flow** — training data is stored in S3, the container reads from S3 during training, and the output model artifact is written back to S3; understanding this S3-centric data flow is essential for scaling ML workloads

> Key takeaway: Amazon SageMaker abstracts away infrastructure management for ML — provisioning compute, running containers, saving artifacts, and streaming logs are all handled automatically. The data scientist focuses on data preparation and model configuration, not on managing servers. This is the cloud-native approach to machine learning.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
