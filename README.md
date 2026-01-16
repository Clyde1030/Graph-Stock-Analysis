<p align="center">
  <img src="slides/GraphStock.png" alt="Analyzing Congressional Stock Transaction with Graph Algorithms" width="800"/>
</p>

<p align="center">
  Check out our <a href="slides/Presentation%20-%20Project%203.pdf">presentation</a> &nbsp; | &nbsp;
  Read our <a href="">Medium article</a>
</p>

# Analyzing Congressional Stock Transaction with Graph Algorithms
**Team:** Yusheng Lee, Kobby Hanson, Maxwell Bowman, William Crosby

## Overview
This project investigates anomalous congressional equity trading behavior using graph-based network analysis. By modeling transactional properties (including representative, its affliated party, ticker, industry) as nodes and trading similarities as weighted edges, we apply community detection and centrality algorithms to visualize representative trading behavior that may worth further investigation.

Core Algorithms:
- **Betweenness centrality**
- **Louvain Modularity**
- **PageRank**

Why Redis and MongoDB?

- MongoDB
    - Stores semi-structured transaction records efficiently
    - Flexible schema supports evolving financial disclosures
    - Ideal for historical querying and aggregation of trading events
- Redis
    - Used as a low-latency cache for intermediate graph results
    - Accelerates repeated centrality computations and similarity lookups
    - Suitable for real-time or interactive analytical workflows



## Methodology

### 1) Data
Data Source
- U.S. Congressional equity transaction disclosures (CSV-based ingestion) from the [House Stock Watcher API](https://housestockwatcher.com/api), 
- Publicly available transaction records

Description
Each record represents a disclosed equity transaction made by a congressional representative, including asset, transaction type, and transaction amount.

Important Columns
- transaction_date
- representative – congressional member name or ID
- state – representative’s state
- ticker – traded equity symbol		
- industry
- sector
- party – political affiliation
- transaction_type – buy / sell
- amount_range – disclosed trade size bucket

Initial preprocessing includes:
- Deduplication
- Null handling
- Standardization of tickers and names
- Transaction aggregation at the representative level


### 2) Graph Construction and What Algorithms Tell US
#### Louvain Modularity

Louvain will try different grouping methods and assign the nodes to the best groupings based on their weights and densities, thereby measuring how well a node is assigned to a group. In our case, we use Louvain to summarize transactions into clusters and measure the similarity between these transactions based on their `industry`, `sector`, and political affiliation (`party` and `state`).

Base Graph:
representative [traded] Ticker [belongs to] Industry [belongs to] Sector

Weighting Methods:
Create a [similar_to] edge between representatives to account for
  - political affliation:
    - same party
    - same state
  - industry and sector:
    - weighted by transaction count

#### Betweenness Centrality

#### PageRank



 
### 3) Tools and Technology
- psycopg2, pandas, numpy: data manipulation
- Neo4j: Graph storage and algorithm execution
- MongoDB: Transaction-level data persistence
- Redis: Caching graph relationships and metrics
- Jupyter Notebook

### 4) Notes and Limitations
- Similarity does not imply causality or wrongdoing
- Graph-based methods are sensitive to:
  - Edge weighting assumptions
  - Similarity thresholds
- Results should be interpreted as signals for further investigation, not conclusions
