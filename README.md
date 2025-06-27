# U-ASK

**U-ASK** is a spatial-textual query processing system that supports the POWER and Boolean Range algorithms for retrieving geospatial data from PostgreSQL and OpenSearch. This guide walks you through setting up the project from scratch: database setup, data indexing, and running query algorithms. :contentReference[oaicite:0]{index=0}

---

## Table of Contents

- [Features](#features)  
- [System Requirements](#system-requirements)  
- [Installation](#installation)  
  - [Clone the Repository](#clone-the-repository)  
  - [Python Dependencies](#python-dependencies)  
- [Database Setup](#database-setup)  
- [Data Processing & Indexing](#data-processing--indexing)  
- [Running Query Algorithms](#running-query-algorithms)  
- [Contributing](#contributing)  
- [License](#license)  

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


git clone https://github.com/your-username/u-ask.git
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
