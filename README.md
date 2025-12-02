📊 DIC Job Market Analysis Using LinkedIn Data 💼A Data Intensive Computing (CSE587) project for analyzing job market trends using LinkedIn data with a Big Data infrastructure.Project OverviewThis project utilizes a Big Data infrastructure with Docker, Hadoop, and Apache Spark to ingest, process, and analyze large-scale job market datasets.🏗️ Infrastructure SetupThis project requires Docker to spin up a Hadoop cluster and Spark environment.0. PrerequisitesEnsure you have Docker installed on your machine.Get Docker1. Start the Hadoop ClusterRun the following command to start the services defined in docker-compose.yaml:docker compose -f docker-compose.yaml up -d
Expected Output:[+] Running 5/5
 ✔ Network project_default              Created
 ✔ Container project-resourcemanager-1  Started
 ✔ Container project-namenode-1         Started
 ✔ Container project-datanode1-1        Started
 ✔ Container project-nodemanager1-1     Started 
📂 Data Ingestion (HDFS)Once the cluster is running, you need to upload data to the Hadoop Distributed File System (HDFS).1. Access the NameNode Containerdocker exec -it project-namenode-1 bash
You will land in /opt/hadoop.2. Upload FilesCreate an input directory and upload your data (e.g., a text file or dataset).hdfs dfs -mkdir /input
hdfs dfs -put README.txt /input/wc.txt
3. Verify UploadCheck if the file exists on HDFS:hdfs dfs -cat /input/wc.txt
Web Interface: You can also verify files via the browser at http://localhost:9870/explorer.html#/.⚡ Running Spark JobsYou can submit PySpark jobs to the cluster using spark-submit.Example: Word Count (spark.py)1. Create the script:from pyspark import SparkConf, SparkContext

def main():
    conf = SparkConf().setAppName("WordCountDemo")
    sc = SparkContext(conf=conf)
    
    input_path = "hdfs://namenode/input/wc.txt"
    output_path = "hdfs://namenode/output/wordcount_result"

    text_file = sc.textFile(input_path)
    
    counts = (text_file
              .flatMap(lambda line: line.split())
              .map(lambda word: (word, 1))
              .reduceByKey(lambda a, b: a + b))
    
    counts.saveAsTextFile(output_path)
    
    sc.stop()

if __name__ == "__main__":
    main()
2. Submit the Job:Run the following command inside the container:./spark/bin/spark-submit --master yarn --deploy-mode cluster spark.py
3. Check Results:If successful, results will be stored in /output/wordcount_result on HDFS.📁 Repository StructureCSE587Project/: Core project files and Docker configuration.phase1/: Phase 1 project report and scripts.EDA.ipynb: Exploratory Data Analysis notebook.Local EDA.ipynb: Local environment analysis.
