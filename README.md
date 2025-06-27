# U-ASK

**U-ASK** is a spatial-textual query processing system that supports the POWER and Boolean Range algorithms for retrieving geospatial data from PostgreSQL and OpenSearch. This guide walks you through setting up the project from scratch: database setup, data indexing, and running query algorithms.

---

## Project Description

### Motivation & Problem Statement  
With the rapid growth of location-tagged social media and IoT data, there’s a pressing need to answer queries that combine **“where”** and **“what”**—for example,  
> “Show me the top-10 coffee shops near me that mention ‘outdoor seating’ but not ‘busy’.”  

Traditional spatial databases excel at proximity queries, but struggle to incorporate rich textual criteria efficiently, and vice versa. **U-ASK** bridges that gap by offering a unified framework for **spatial-textual** querying over large-scale geospatial repositories.

### Objectives  
- **Expressive Queries**: Support top-k scoring that blends spatial distance with keyword relevance, plus Boolean filters.  
- **High Throughput & Low Latency**: Handle millions of indexed points and thousands of concurrent queries with sub-50 ms response times.  
- **Modular & Extensible**: Easy to plug in new ranking functions, indexing strategies, or even external engines (e.g., Elasticsearch, OpenSearch).

---

## System Architecture


1. **Preprocessing**  
   - Clean and normalize text (stopword removal, stemming)  
   - Extract geocoordinates and keyword lists (with TF-IDF weights)  

2. **Indexing**  
   - **PostGIS** for spatial indexing (R-tree)  
   - **OpenSearch** for inverted-index on keywords  
   - A custom **TEQ (Textual-Enhanced Quadtree)** index links spatial cells ↔ keyword postings for fast joint pruning  

3. **Query Engine**  
   - Parses incoming queries into spatial (range/k-NN) + textual (positive/negative keywords) components  
   - Invokes one of three algorithms (see below), merging results on the fly  
   - Returns ranked results via a simple REST API or Streamlit demo  

---

## Core Algorithms

1. **POWER (POint-weighted wEighted Ranking)**  
   - **Goal**: Return top-k points that maximize   
   - Uses dual-priority queues—one for spatial nearest candidates, one for high keyword relevance—and interleaves expansions until the top-k threshold is guaranteed.

2. **Boolean Range Queries**  
   - Supports clauses like  
     ```
     INCLUDE: ["park", "playground"]
     EXCLUDE: ["crowded", "fees"]
     RADIUS: 2 km
     ```  
   - Leverages bitmaps from the inverted index to filter out undesirable points *before* any spatial checks—greatly reducing I/O.

3. **RCA (Range-Constrained Aggregation)**  
   - For analytics-style queries (e.g., “average sentiment score of tweets within 5 km of each coffee shop”), it traverses spatial neighborhoods and aggregates textual metrics in one pass, exploiting locality in the TEQ index to avoid full scans.

---

## Evaluation & Results

- **Datasets**: 5 M geotagged tweets across a metropolitan region  
- **Index Build Time**: ~45 min on a 16-core, 32 GB machine  
- **Query Throughput**:  
  - POWER (top-10): ~2,000 QPS  
  - Boolean Range: ~3,500 QPS  
  - RCA (per-object aggregation): ~200 ops/s  
- **Latency**: 95th-percentile response ≈ 30 ms for POWER, ≈ 20 ms for Boolean Range  

---

## Use Cases

- **Real-Time POI Search** for mobile apps (“find me quiet cafes within 1 mile”)  
- **Disaster Response** (“show tweets mentioning ‘flooded’ but not ‘drill’ within affected zones”)  
- **Targeted Marketing** (“list stores within 3 km whose reviews include ‘vegan’ but not ‘expensive’”)  

---

## Future Work

- **Semantic Ranking**: Incorporate transformer-based embeddings for deeper text understanding.  
- **Dynamic Updates**: Support low-latency incremental indexing for streaming data.  
- **Multi-Modal Queries**: Extend to image tags or sensor readings alongside text.  

---

## Features

- **POWER Algorithm:** Top-k spatial-textual queries  
- **Boolean Range Queries:** Negative keyword filtering  
- **RCA Algorithm:** Range-constrained aggregation  

---

## System Requirements

- **Operating System:** Windows / Linux / macOS  
- **Python:** 3.11  
- **PostgreSQL:** ≥ 13 (with PostGIS)  
- **OpenSearch:** ≥ 2.x  
- **Tools:** pip, VS Code (or any Python IDE)  

---

## Installation

### Clone the Repository


git clone https://github.com/RanjithaNarasimhamurthy/U-ASK.git

cd u-ask

## Python Dependencies
Install required packages:

pip install psycopg2-binary geopy scikit-learn numpy concurrent.futures streamlit opensearch-py pandas

If you need PostGIS:

CREATE EXTENSION IF NOT EXISTS postgis;

## Database Setup
Create & Connect to Database

CREATE DATABASE tweet_data;

\c tweet_data

Enable PostGIS

CREATE EXTENSION IF NOT EXISTS postgis;
## Create tweets Table

CREATE TABLE tweets (

  tweet_id        BIGINT PRIMARY KEY,
  
  latitude        DOUBLE PRECISION,
  
  longitude       DOUBLE PRECISION,
  keywords        TEXT[],
  
  keyword_weights FLOAT[]
);
## Data Processing & Indexing
Process Raw Tweets

python process_tweets.py

Clean Processed Tweets

python clean.py

Index to OpenSearch

python teq_indexing.py

Load into PostgreSQL

python load_indexed_data.py

Running Query Algorithms

POWER Algorithm (Top-k Spatial-Textual Queries)

python power_algorithm.py

Boolean Range Queries

python power_boolean_range.py

RCA Algorithm

python rca_algorithm.py
