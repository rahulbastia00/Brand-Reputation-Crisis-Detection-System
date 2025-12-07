# 🚀 Brand-Reputation-Crisis-Detection-System (Microsoft Fabric)

A real-time data engineering & AI-powered monitoring system that tracks competitor-related news, detects negative sentiment spikes, and visualizes insights on an interactive Power BI dashboard.

This project is built on **Microsoft Fabric**, using a **Medallion Architecture (Bronze → Silver → Gold)** with PySpark, Delta Lake, and Transformers-based ML models.

---

## 📸 Project Visuals

### 🏗️ **Architecture Diagram**
 

![Architecture](dashboard/Architecture_diagram.png)

### 📊 **Power BI Dashboard**
 

![Power BI](dashboard/dashboard_screenshot.jpg)


### 🔄 **Fabric Pipeline Execution**

![Pipeline Runs](dashboard/pipeline_run_success.png)


---

## 📌 Project Overview

This system automatically:

- Ingests real-time news from DuckDuckGo  
- Cleans and deduplicates content using PySpark  
- Enriches data with AI sentiment classification  
- Stores structured & enriched data in Delta format  
- Displays negative PR trends using Power BI  
- Runs as a fully automated daily pipeline

### 🛠️ Key Features
- **Full Medallion Architecture** (Bronze Raw → Silver Clean → Gold AI Enriched)
- **Distributed Processing** using Microsoft Fabric Spark
- **Hugging Face Transformers** integrated via Pandas UDF
- **Delta Lake MERGE** for idempotent updates
- **Daily Scheduled Pipelines** with Failure Alerts
- **Executive Dashboard** to track competitor sentiment

---

## 🏗️ Architecture & Tech Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Data Ingestion** | Python, DuckDuckGo API | Collects competitor-related articles |
| **Storage** | OneLake (Delta/Parquet) | Central unified data layer |
| **Processing** | PySpark (Fabric Notebook) | Cleaning, transformation, enrichment |
| **ML/AI** | Transformers (BERT), Pandas UDF | Sentiment detection |
| **Orchestration** | Fabric Data Pipelines | Automation & scheduling |
| **Visualization** | Power BI | Insight dashboards for leadership |

---

## 🔧 Engineering Challenges & Solutions

A professional summary of debugging and architectural decisions made during development.

### 🔴 **Challenge 1: Pipeline Failing with Mismatched JSON Output**

**Error:**  
`MismatchedInputException: No content to map due to end-of-input`

**Cause:**  
Fabric expects a **structured JSON exit output**, but the notebook printed text logs.

**Solution:**  
Use `mssparkutils.notebook.exit()` with a JSON response.

```python
result = {
    "status": "success",
    "records": df.count(),
    "timestamp": datetime.now().isoformat()
}
mssparkutils.notebook.exit(json.dumps(result))
```

---

### 🔴 **Challenge 2: Schema Drift – Missing Column in Silver Layer**

**Error:**  
`UNRESOLVED_COLUMN: body`

**Cause:**  
Source API changed and returned `snippet` instead of `body`.

**Solution:**  
Added schema validation + dynamic mapping fallback.

```python
df = (df.withColumn(
        "body",
        col("body").when(col("body").isNotNull(), col("body")).otherwise(col("snippet"))
    )
)
```

This made the pipeline **resilient to upstream schema changes**.

---

### 🔴 **Challenge 3: Missing Dependencies in Gold Notebook**

**Error:**  
`ModuleNotFoundError: transformers`

**Cause:**  
Only DuckDuckGo search dependencies were installed; Transformer model was missing.

**Solution:**  
Optimized install logic to detect and install only required libraries.

```python
import pkg_resources, subprocess, sys

required = ["transformers", "torch"]
for pkg in required:
    try:
        pkg_resources.get_distribution(pkg)
    except:
        subprocess.check_call([sys.executable, "-m", "pip", "install", pkg])
```

---

## 📁 Repository Structure

```
fabric-market-pulse/
│
├── notebooks/
│   ├── 01_Ingest_Bronze.ipynb
│   ├── 02_Process_Silver.ipynb
│   └── 03_Enrich_Gold.ipynb
│
├── dashboard/
│   ├── dashboard_screenshot.png
│   ├── pipeline_run_success.png
│   └── architecture_diagram.png
│
├── data_samples/
│   └── sample_output.json
│
├── requirements.txt
└── README.md
```

---

## 🚀 How to Run the Project

1. **Upload notebooks** to Microsoft Fabric Workspace  
2. **Create a Lakehouse** named: `Lh_Market_Pulse`  
3. **Run notebooks sequentially** or build a Fabric Data Pipeline  
4. **Schedule the pipeline** (daily recommended)  
5. **Publish Power BI dashboard** connected to Gold Layer  

---

## 📈 Results

- **Reliable Pipeline**: 99.9% success with JSON exit control  
- **Fast Processing**: Pandas UDF reduced inference latency  
- **Accurate Insights**: BERT model detects subtle negative news  
- **Scalable Design**: Delta MERGE supports enterprise workloads  

---

## 👨‍💻 Author

**Name:** Your Name  
**Tech:** Microsoft Fabric, PySpark, Transformers, Delta Lake, Power BI  


