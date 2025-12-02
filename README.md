# DIC Job Market Analysis Using LinkedIn Data 📊💼

**A Data Intensive Computing (CSE587) project for analyzing job market trends using LinkedIn data.**

This project utilizes a Big Data infrastructure with **Docker**, **Hadoop**, and **Apache Spark** to ingest, process, and analyze large-scale job market datasets.

---

## 🏗️ Infrastructure Setup

This project requires **Docker** to spin up a Hadoop cluster and Spark environment.

### 0. Prerequisites
Ensure you have Docker installed on your machine.
* [Get Docker](https://docs.docker.com/get-started/get-docker/)

### 1. Start the Hadoop Cluster
Run the following command to start the services defined in `docker-compose.yaml`:

```bash
docker compose -f docker-compose.yaml up -d

## Expected Output:

[+] Running 5/5
 ✔ Network project_default              Created
 ✔ Container project-resourcemanager-1  Started
 ✔ Container project-namenode-1         Started
 ✔ Container project-datanode1-1        Started
 ✔ Container project-nodemanager1-1     Started
