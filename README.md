# 📊 DIC Job Market Analysis Using LinkedIn Data

Analyze job market trends using LinkedIn data on a Big Data stack (Docker + Hadoop + Spark) for the CSE587 Data Intensive Computing course.

## 🔎 Overview
- Ingest, process, and analyze large-scale job postings and skills data.
- Orchestrated via Docker Compose with a Hadoop (HDFS/YARN) + Spark cluster.
- Includes notebooks for EDA and a sample Spark job (word count) running on HDFS.

## 🧰 Tech Stack
- Docker & Docker Compose
- Hadoop (HDFS, YARN)
- Apache Spark (PySpark)
- Jupyter Notebooks (EDA)

## ✅ Prerequisites
- Docker Desktop installed and running.
- Adequate resources allocated to Docker (CPU/RAM) for Hadoop/Spark.

## 🚀 Quick Start
Start the Hadoop + Spark cluster using the provided compose file.

```bash
docker compose -f CSE587Project/docker-compose.yaml up -d
```

Expected output (abbreviated):
```
[+] Running 5/5
 ✔ Network project_default              Created
 ✔ Container project-resourcemanager-1  Started
 ✔ Container project-namenode-1         Started
 ✔ Container project-datanode1-1        Started
 ✔ Container project-nodemanager1-1     Started
```

To stop and remove the cluster:
```bash
docker compose -f CSE587Project/docker-compose.yaml down
```

## 📂 Data Ingestion to HDFS
Upload input data to HDFS after the cluster is up.

1) Open a shell in the NameNode container:
```bash
docker exec -it project-namenode-1 bash
```

2) Create the input directory and upload a file:
```bash
hdfs dfs -mkdir /input
hdfs dfs -put README.txt /input/wc.txt
```

3) Verify the upload:
```bash
hdfs dfs -cat /input/wc.txt
```

Web UI for HDFS file explorer:
```
http://localhost:9870/explorer.html#/
```

## ⚡ Run a Spark Job (Word Count)
Submit a simple PySpark job against data stored in HDFS.

1) Create `spark.py` (inside the NameNode shell or your workspace):
```python
from pyspark import SparkConf, SparkContext

def main():
    conf = SparkConf().setAppName("WordCountDemo")
    sc = SparkContext(conf=conf)

    input_path = "hdfs://namenode/input/wc.txt"
    output_path = "hdfs://namenode/output/wordcount_result"

    text_file = sc.textFile(input_path)

    counts = (
        text_file
        .flatMap(lambda line: line.split())
        .map(lambda word: (word, 1))
        .reduceByKey(lambda a, b: a + b)
    )

    counts.saveAsTextFile(output_path)
    sc.stop()

if __name__ == "__main__":
    main()
```

2) Submit the job (inside the NameNode shell):
```bash
./spark/bin/spark-submit --master yarn --deploy-mode cluster spark.py
```

3) Check results on HDFS:
```bash
hdfs dfs -ls /output/wordcount_result
hdfs dfs -cat /output/wordcount_result/part-*
```

## 🗂️ Repository Structure
- `CSE587Project/`: Core project files and Docker configuration
- `phase1/`: Phase 1 report and ingestion scripts
- `EDA.ipynb`: Exploratory Data Analysis notebook
- `Local EDA.ipynb`: Local environment analysis
- `linkedin-jobs-and-skills-eda-project-6.ipynb`: Additional EDA notebook

## 📝 Notes
- If containers don’t start, ensure Docker resources are sufficient.
- Use the HDFS Web UI to quickly validate file locations.
- Adapt `spark.py` to your dataset paths and analysis tasks.