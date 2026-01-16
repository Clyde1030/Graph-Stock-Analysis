<p align="center">
  <img src="slides/GraphStock.png" alt="Analyzing Congressional Stock Transaction with Graph Algorithms" width="800"/>
</p>

<p align="center">
  Check out our <a href="slides/Presentation%20-%20Project%203.pdf">presentation</a>
</p>

# Analyzing Congressional Stock Transactions with Graph Algorithms
**Team:** Yusheng Lee, Kobby Hanson, Maxwell Bowman, William Crosby

---

## Overview

This project investigates **anomalous congressional equity trading behavior** using **graph-based network analysis**. By modeling transactional attributes—such as representatives, political affiliation, stock tickers, industries, and sectors—as nodes, and encoding trading similarities as weighted edges, we apply community detection and centrality algorithms to surface structural patterns that may warrant further investigation.

### Core Algorithms
- **Betweenness Centrality**
- **Louvain Modularity**
- **PageRank**

### Why Redis and MongoDB?

**MongoDB**
- Efficient storage of semi-structured transaction records  
- Flexible schema supports evolving financial disclosure formats  
- Well-suited for historical querying and aggregation of trading events  

**Redis**
- Low-latency caching for intermediate graph results  
- Accelerates repeated centrality computations and similarity lookups  
- Enables interactive or near–real-time analytical workflows  

---

## Methodology

### 1) Data

#### Data Source
- U.S. Congressional equity transaction disclosures (CSV-based ingestion)
- Retrieved from the [House Stock Watcher API](https://housestockwatcher.com/api)
- Publicly available transaction records

#### Description
Each record represents a disclosed equity transaction made by a congressional representative.

#### Important Columns
- `transaction_date`
- `representative` – congressional member name or ID  
- `state` – representative’s state  
- `ticker` – traded equity symbol  
- `industry`
- `sector`
- `party` – political affiliation  
- `transaction_type` – buy / sell  
- `amount_range` – disclosed trade size bucket  

#### Preprocessing Steps
- Deduplication  
- Null handling  
- Standardization of representative names and tickers  
- Transaction aggregation at the representative level  

---

### 2) Graph Construction and What the Algorithms Tell Us

#### Louvain Modularity

Louvain modularity iteratively assigns nodes into communities by maximizing edge density within groups relative to the overall graph. In this project, Louvain is used to **summarize trading behavior into clusters** based on similarity across political and industry dimensions.

##### Graph Construction Logic
`Representative` ── traded ──> `Ticker` ── belongs to ──> `Industry` ── belongs to ──> `Sector`

##### Weighting Strategy
A `SIMILAR_TO` edge is created between representatives based on:
- **Political affiliation**
  - Same party
  - Same state
- **Industry and sector exposure**
  - Weighted by transaction frequency

##### Application:
This enables identification of representative clusters with similar trading profiles and political alignment.

---

#### Betweenness Centrality

Betweenness centrality is applied as a **targeted anomaly signal**, highlighting representatives or assets that bridge otherwise weakly connected trading clusters.

##### Graph Construction Logic
The trading network is modeled as a **bipartite graph** in Neo4j:
- Nodes
  - `Representative` (blue nodes)
  - `Company` / `Stock Ticker` (red nodes)
- Edges
  - `(:Representative)-[:PURCHASED]->(:Company)`
- Edge Properties
  - `transaction_type`
  - `amount`
  - `date`

##### Application:
Betweenness centrality measures how often a node lies on the **shortest paths** between other nodes.

In the context of congressional trading:

A **high-betweenness representative** may:
- Act as a bridge between otherwise unrelated trading groups  
- Exhibit trading behavior aligned with multiple clusters  
- Occupy structurally critical positions in the trading network  

A **high-betweenness stock ticker** may:
- Serve as a shared asset connecting disparate politicians  
- Indicate focal points of convergent or coordinated trading interest  

---

#### PageRank

PageRank is used to measure **node influence** within the trading network by accounting for both the quantity and quality of connections. Representatives with high PageRank scores are those connected to other influential actors, helping prioritize nodes for further review.

##### Graph Construction Logic
- Nodes
  - Representative
    - Properties: name, party, state
- Stock
  - Properties: ticker, sector
- Edges
  - (:Representative)-[:TRANSACTED]->(:Stock)
    - Treated as undirected for PageRank
    - Edge weight: max_amount (aggregated transaction amount)

##### Application
In the context of congressional trading:

A high-PageRank representative:
- Trades with stocks that are widely traded by other influential representatives
- Is embedded in highly connected and information-dense regions of the network
- May exert outsized structural influence even without acting as a bridge

A high-PageRank stock:
- Is commonly traded by influential representatives
- Serves as a focal asset within the congressional trading ecosystem
- May indicate sectors or companies attracting disproportionate political attention

---

## Tools and Technology

- **psycopg2, pandas, numpy** – data manipulation, transaction shaping, and inspection  
- **Neo4j** – graph storage and algorithm execution  
- **MongoDB** – transaction-level data persistence  
- **Redis** – caching graph relationships and metrics  
- **Jupyter Notebook** – exploratory analysis and visualization  

---

## Notes and Limitations

- Trading similarity does **not** imply causality or wrongdoing  
- Graph-based methods are sensitive to:
  - Edge-weighting assumptions  
  - Similarity thresholds  
- Structural centrality ≠ unethical behavior  
- Results should be interpreted as **signals for further investigation**, not conclusions  
- Findings are intended to inform additional qualitative, legal, or statistical review  

---
