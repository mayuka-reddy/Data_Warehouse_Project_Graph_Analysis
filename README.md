# Data Warehouse Project: Graph Database Analysis

## Table of Contents
1. [Introduction](#introduction)
2. [Dataset Overview](#dataset-overview)
3. [Data Preparation for Neo4j ](#Data-Preparation-for-Neo4j)
4. [ETL Process](#etl-process)
5. [Node and Relationship Files](#node-and-relationship-files)
6. [Queries](#queries)
    - [Complex Queries](#complex-queries)
    - [Common Query Comparisons](#common-query-comparisons)
7. [Challenges Faced](#challenges-faced)
8. [Future Improvements](#future-improvements)
9. [License](#license)

---

## Introduction
This project explores the use of Neo4j to perform graph-based analysis on the CICIDS 2017 dataset. It complements a previous project that used Amazon Redshift for star-schema-based analysis, demonstrating the differences in query design, performance, and data representation.

The dataset models network traffic and relationships using graph structures to identify patterns and detect intrusions effectively.

---

## Dataset Overview
The **CICIDS 2017 dataset** is a comprehensive benchmark dataset for intrusion detection systems (IDS). It simulates real-world network traffic and includes both normal and malicious activities.

### **Key Features**
- **Time Period**: Captured from July 3 to July 7, 2017.
- **Network Activity**:
  - Normal activities such as HTTP, FTP, and email communications.
  - Simulated attack scenarios, including brute force attacks, DoS, DDoS, and port scans.
- **Features**: 
  - Traffic details like source and destination IPs, ports, and protocols.
  - Packet-level attributes (e.g., packet lengths, packet variances, and total packets).

### **Why CICIDS 2017?**
- **Rich Labeling**: Each traffic record is labeled, making it ideal for supervised analysis.
- **Real-World Relevance**: Covers a wide range of attack types observed in real networks.
- **Diverse Features**: Includes statistical, time-based, and packet-based attributes.

## Data Preparation for Neo4j
The dataset was preprocessed using Apache NiFi, which extracted the following components:
- **Nodes**:
  - **Source**: Contains `Source IP` and `Source Port`.
  - **Destination**: Contains `Destination IP` and `Destination Port`.
  - **Protocol**: Contains `Protocol Name` and `Protocol`.
  - **Label**: Contains `Label Name` to classify the traffic.
  - **Packet**: Contains statistical features like:
    - Packet Length Mean
    - Total Forward Packets
    - Average Packet Size
    - And more (see the full list in the `packet.csv` file under `Data/Graph/Node/`).

- **Relationships**:
  - **Attack Initiated**: Links `Source` to `Label`.
  - **Attacked**: Links `Label` to `Destination`.
  - **Classified As**: Links `Packet` to `Label`.
  - **Commonly Attacked By**: Links `Protocol` to `Label`.

Each relationship includes a `count` property indicating the number of occurrences of the combination.

---

## ETL Process
The ETL process was implemented using **Apache NiFi**, which performed the following:
- Extracted raw data from the CICIDS 2017 dataset.
- Transformed it into node and relationship CSV files.
- Produced optimized files for efficient upload into Neo4j.

See the `scripts/` folder for the NiFi workflow file (`Nifi_Flow.json`)).

---

## Node and Relationship Files
All node and relationship data files are located in the `data/` directory:
- **Nodes**: Found in `Data/Graph/Node/`.
- **Relationships**: Found in `Data/Graph/Relationship/`.

---

## Queries
### Complex Queries
The `queries/` folder contains Cypher scripts (`.cypher`) for all the queries performed on the database. Each query is accompanied by its output in the `queries/query_results/` directory.

### Common Query Comparisons
Some queries were designed to compare performance and results between this graph-based project and the star-schema-based Redshift project. A summary of these comparisons is provided in `docs/common_queries_comparison.md`.

---

## Challenges Faced
1. **Relationship Creation**:
   - Initially, relationships were slow to load despite indexing all nodes.
   - **Solution**: Partitioned the data into smaller files to optimize uploads.

2. **Parallel Processing**:
   - Attempting parallel queries led to memory errors.
   - **Solution**: Avoided parallel processing altogether to ensure stability.

---

## Future Improvements
- Explore advanced indexing strategies to improve query performance.
- Experiment with parallel query execution using optimized configurations.
- Integrate additional features like anomaly scoring into the graph.

---
