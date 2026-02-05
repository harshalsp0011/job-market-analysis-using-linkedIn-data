# 📋 Project Documentation: DIC Job Market Analysis Using LinkedIn Data

## 1. Project Overview

### Project Name and Role
**DIC Job Market Analysis Using LinkedIn Data** – Data Engineer / Big Data Developer

**Course:** CSE587 - Data Intensive Computing (University at Buffalo)

---

## 2. Business Problem & Use Case

### What the Project Does
- **Analyzes job market trends** by processing large-scale LinkedIn job postings and associated skills data
- **Identifies skill demand patterns** across different job roles and industries
- **Supports data-driven decision-making** for job seekers and recruiters through exploratory data analysis
- **Demonstrates scalable data processing** using Big Data technologies for handling large datasets

### Business Value
- Provides insights into emerging job market trends
- Enables skill gap analysis for professionals
- Supports recruitment strategy optimization
- Processes 1000+ job records efficiently at scale

---

## 3. Technology Stack

### Core Technologies
| Category | Technology |
|----------|-----------|
| **Orchestration** | Docker, Docker Compose |
| **Distributed Processing** | Apache Hadoop (HDFS, YARN) |
| **Batch Processing** | Apache Spark (PySpark) |
| **Data Ingestion** | Java (Hadoop API) |
| **Analysis & Visualization** | Jupyter Notebooks, Python |
| **Data Format** | CSV |
| **Language** | Python, Java |

### Supporting Tools
- **HDFS Web UI** (Port 9870) - File system browser and monitoring
- **YARN Resource Manager** - Job scheduling and resource management
- **Docker Desktop** - Containerization and local cluster simulation

---

## 4. Main Components & Modules

### Architecture Overview
```
Data Source (LinkedIn CSV) 
    ↓
Data Ingestion (Java - DataIngestion.java)
    ↓
HDFS Storage (/input/job_dataset.csv)
    ↓
Spark Processing (PySpark jobs)
    ↓
Output Storage (HDFS /output/)
    ↓
Analysis & Visualization (Jupyter Notebooks)
```

### Key Modules

#### 4.1 **Data Ingestion Module** (`phase1/DataIngestion.java`)
- **Purpose:** Upload local CSV files to HDFS
- **Input:** `merged_data_jobs.csv` from local file system
- **Output:** `/input/job_dataset.csv` in HDFS
- **Technology:** Java, Hadoop API
- **Key Operations:**
  - Configures Hadoop FileSystem connection
  - Copies files from local filesystem to HDFS
  - Error handling for failed uploads

#### 4.2 **Distributed Storage Layer** (HDFS)
- **NameNode:** Manages file system namespace
- **DataNodes:** Store actual data blocks (replicated across nodes)
- **Purpose:** Reliable, distributed storage for large datasets
- **Input Directory:** `/input/` - Raw LinkedIn job data
- **Output Directory:** `/output/` - Processed results

#### 4.3 **Processing Engine** (Apache Spark + YARN)
- **Framework:** PySpark running on YARN
- **Deployment Mode:** Cluster mode via YARN
- **Example Job:** Word count analysis on job descriptions
- **Capabilities:**
  - Distributed data processing
  - MapReduce-style transformations
  - Scalable to large datasets

#### 4.4 **Exploratory Data Analysis (EDA)**
- **Notebooks:**
  - `EDA.ipynb` - Main analysis notebook
  - `Local EDA.ipynb` - Local testing
  - `linkedin-jobs-and-skills-eda-project-6.ipynb` - Detailed skills analysis
- **Analytics Performed:**
  - Job distribution by industry
  - Top skills demand analysis
  - Salary trends
  - Company hiring patterns

---

## 5. Key Technical Details

### 5.1 Data Flow Architecture

```
Step 1: LOCAL DATA
└─ merged_data_jobs.csv (LinkedIn dataset)

Step 2: INGESTION (Java)
└─ DataIngestion.java copies file to HDFS
   └─ Config: HDFS URI = hdfs://127.0.0.1:9000
   └─ Target: /input/job_dataset.csv

Step 3: HDFS STORAGE
└─ NameNode (Port 9000): Manages namespace
└─ DataNode: Stores 3x replicated blocks
└─ Web UI (Port 9870): Monitor files

Step 4: SPARK PROCESSING
└─ Read from HDFS: /input/job_dataset.csv
└─ Processing: Word count, data aggregation
└─ Write Output: /output/wordcount_result

Step 5: ANALYSIS
└─ Load results in Jupyter Notebooks
└─ Generate visualizations and insights
```

### 5.2 Data Processing Logic

#### Word Count Job (spark.py)
```python
Input: Text file with job descriptions
Process:
  1. Read from HDFS
  2. Split lines into words
  3. Map each word to (word, 1)
  4. Reduce by key: sum counts for each word
Output: (word, frequency) pairs saved to HDFS
```

#### Key Transformations
- **Flatmap:** Split job descriptions into individual words
- **Map:** Create (word, count) tuples
- **ReduceByKey:** Aggregate word frequencies across the dataset

### 5.3 Database & Data Schema

#### Input Data Format (CSV)
```
job_title, company, location, salary, skills, description, ...
- job_title: STRING
- company: STRING
- location: STRING
- salary: NUMERIC (optional)
- skills: STRING (comma-separated)
- description: TEXT (full job description)
```

#### Output Data Format (Word Count Result)
```
(word, frequency)
Example: ('python', 1523), ('sql', 1204), ('data', 987)
```

#### Data Quality Considerations
- **Handling Missing Values:** EDA identifies null fields in salary, skills
- **Data Type Handling:** Skills parsed from comma-separated strings
- **Duplicate Records:** Can be filtered using job_id or (title, company, location) composite key

---

## 6. Infrastructure & Deployment Setup

### 6.1 Docker Compose Cluster Architecture
```
Services:
┌─────────────────────────────────────┐
│      Docker Network (project_default) │
├─────────────────────────────────────┤
│ NameNode (Port 9000, 9870 UI)       │
│ ResourceManager (Port 8088)         │
│ DataNode 1 (Port 9864)              │
│ NodeManager 1 (Port 8042)           │
└─────────────────────────────────────┘
```

**Configuration:** See `CSE587Project/docker-compose.yaml`

### 6.2 Startup & Deployment
```bash
# Start cluster
docker compose -f CSE587Project/docker-compose.yaml up -d

# Verify services
docker compose -f CSE587Project/docker-compose.yaml ps

# Access interfaces
- HDFS Web UI: http://localhost:9870
- YARN UI: http://localhost:8088

# Stop cluster
docker compose -f CSE587Project/docker-compose.yaml down
```

---

## 7. Collaboration & Teamwork

### Project Team
- **Data Engineers:** Responsible for data ingestion and ETL pipeline
- **Analytics Team:** Performs EDA and generates business insights
- **Course Instructor:** CSE587 instructors and TAs

### Collaboration Tools & Practices
- **Version Control:** GitHub with gitignore for sensitive files (`CSE587Project.zip`)
- **Documentation:** README.md, inline code comments
- **Development Environment:** Local Docker setup for reproducibility
- **Code Organization:**
  - Modular structure (DataIngestion.java, PySpark jobs)
  - Separate EDA notebooks for experimentation
  - Clear file naming conventions

### Communication & Coordination
- **Requirements:** Course project specifications (CSE587)
- **Testing:** Manual testing via HDFS Web UI and notebook outputs
- **Deployment:** Single-developer or small team setup

---

## 8. Key Features & Special Considerations

### 8.1 Security
- **HDFS Default Replication:** 3x data redundancy
- **Local Development:** Uses localhost/127.0.0.1 (not exposed to external networks)
- **Data Handling:** CSV files processed in-cluster; no external API calls for sensitive data
- **Container Isolation:** Services run in isolated Docker containers

### 8.2 Monitoring & Observability
- **HDFS Health Check:** Web UI shows block replication status
- **YARN Job Monitoring:** Resource Manager UI tracks job execution
- **Data Validation:** EDA notebooks verify data quality
- **Error Handling:** Java code includes IOException handling for failed uploads

### 8.3 Scalability & Performance
- **Horizontal Scaling:** Can add more DataNodes and NodeManagers
- **Data Partitioning:** Spark automatically partitions data across executors
- **Processing:** Handles job records in parallel using MapReduce model
- **Performance Optimization:** YAML configuration for cluster resource allocation

### 8.4 Deployment & Containerization
- **Docker:** Complete isolated environment for Hadoop/Spark
- **No Local Dependencies:** All services containerized
- **Reproducibility:** Same docker-compose.yaml produces identical environment
- **Resources:** Configurable memory/CPU allocation for Docker Desktop

---

## 9. Impact & Results

### Deliverables
✅ **Data Ingestion Pipeline** - Automated file upload to HDFS
✅ **Processing Infrastructure** - Hadoop + Spark cluster setup
✅ **Sample Jobs** - Word count and aggregation demos
✅ **Exploratory Analysis** - Multiple EDA notebooks with visualizations
✅ **Documentation** - Comprehensive setup and usage guides

### Performance Metrics
- **Data Processed:** 1000+ job records
- **Processing Time:** Reduced through parallelization on Spark
- **System Efficiency:** Distributed processing across multiple nodes
- **Replication Factor:** 3x data redundancy for fault tolerance

### Learning Outcomes
- ✔️ Implemented Big Data architecture patterns
- ✔️ Worked with distributed file systems (HDFS)
- ✔️ Executed distributed computing jobs (Spark on YARN)
- ✔️ Conducted data analysis on large datasets
- ✔️ Demonstrated DevOps practices with Docker

---

## 10. Files & Directory Structure

```
.
├── README.md                                      # Quick start guide
├── PROJECT_DOCUMENTATION.md                       # This file
├── EDA.ipynb                                      # Main analysis notebook
├── Local EDA.ipynb                                # Local testing notebook
├── linkedin-jobs-and-skills-eda-project-6.ipynb  # Detailed skills analysis
├── abc.ipynb                                      # Additional analysis
│
├── CSE587Project/
│   ├── docker-compose.yaml                        # Cluster configuration
│   ├── README.md                                  # Project README
│   ├── test.sh                                    # Test scripts
│   └── config/                                    # Hadoop/Spark configs
│
└── phase1/
    ├── DataIngestion.java                         # Data upload utility
    └── script.sh                                  # Ingestion scripts
```

---

## 11. Getting Started Guide

### Prerequisites
- Docker Desktop (with 4+ GB allocated)
- Java 11+ (for running DataIngestion.java)
- Python 3.8+ (for Jupyter notebooks)
- 10+ GB free disk space

### Quick Start (5 minutes)

**1. Start the cluster**
```bash
cd CSE587Project
docker compose -f docker-compose.yaml up -d
```

**2. Verify services are running**
```bash
docker compose ps
```

**3. Upload data to HDFS**
```bash
docker exec -it project-namenode-1 bash
hdfs dfs -mkdir /input
hdfs dfs -put /path/to/merged_data_jobs.csv /input/
```

**4. Run a Spark job**
```bash
./spark/bin/spark-submit --master yarn --deploy-mode cluster spark.py
```

**5. View results in Jupyter**
```bash
jupyter notebook
# Open EDA.ipynb
```

**6. Access monitoring UIs**
- HDFS: http://localhost:9870
- YARN: http://localhost:8088

---

## 12. Troubleshooting & FAQs

### Issue: Port already in use
```bash
docker compose down --remove-orphans
# Or change port mappings in docker-compose.yaml
```

### Issue: HDFS data not visible
- Check replication factor: `hdfs dfs -stat %r /input/file.csv`
- Wait for replication to complete (3 nodes)

### Issue: Spark job fails with "Cannot connect to namenode"
- Verify NameNode is running: `docker compose logs namenode`
- Check HDFS URI in spark.py matches docker-compose config

---

## 13. Future Enhancements

- [ ] Implement data warehouse schema (Star/Snowflake)
- [ ] Add incremental data loading (SCD Type 2)
- [ ] Integrate with visualization tools (Tableau, Power BI)
- [ ] Implement streaming pipeline with Apache Kafka
- [ ] Add machine learning models (skill prediction, salary estimation)
- [ ] Setup monitoring/alerting (Prometheus, Grafana)
- [ ] Production deployment (AWS EMR, Databricks)

---

## 14. References & Useful Links

- [Apache Hadoop Documentation](https://hadoop.apache.org/docs/)
- [Apache Spark Documentation](https://spark.apache.org/docs/)
- [Docker Compose Guide](https://docs.docker.com/compose/)
- [HDFS Architecture](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)
- [Jupyter Notebook Tutorial](https://jupyter.org/)

---

**Last Updated:** February 2026  
**Project Status:** Active (Course Project)  
**Maintainers:** CSE587 Project Team
