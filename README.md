# 🌐 Elevation-Aware Modeling of the Kelowna Road Network: A Spatial Graph Analysis

This repository contains my final project for **COSC 521 – Network Science** at **UBC Okanagan**.  

---

## 🎯 Project Overview

This project analyzes the Kelowna, BC road network as a **spatial graph**, comparing a traditional planar (2D) representation with an elevation-aware (3D) model derived from **LiDAR-based Digital Elevation Models (DEMs)**.  
Using road centerlines from the **BC Digital Road Atlas (DRA)** and elevation extracted from **LiDAR BC**, the workflow constructs:

- A **2D graph** weighted by geometric (planar) length  
- A **3D graph** weighted by elevation-aware segment length  

Both graphs share identical topology, allowing direct comparison of:

- **Node centrality** (betweenness, PageRank, closeness)  
- **Edge betweenness** and road-class flow patterns  
- **Community structure** using Louvain detection  
- **Network resilience** under targeted node/edge removal  

The repository provides a full, reproducible pipeline from raw spatial data → processed node/edge tables → analytical results and figures used in the final report.

The final report with further analysis and implementation details can be found at [/latex/Cosc521_ProjectReport.pdf](/latex/Cosc521_ProjectReport.pdf).

---

## 📂 Repository Structure

```bash
kelowna_road_analysis/
│
├── data_kelowna/
│   ├── DRA_Kelowna_Subset.gpkg # Cleaned GeoPackage of Kelowna road centrelines
│   ├── final_nodes.csv         # Nodes with coordinates + elevation
│   ├── final_edges.csv         # Edges with 2D + 3D lengths and slopes
│   ├── edges.csv               # Raw edges extracted after cleaning
│   ├── edges_with_nodes.csv    # Edges joined with node IDs
│   ├── nodes.csv               # Node table before DEM filtering
│   └── tiles/                  # (ignored) DEM tiles from LiDAR BC
│
├── latex/
│   ├── svproc.cls              #LaTex format for Springer
│   ├── refs.bib                # References used for report
│   ├── final_report.tex        # Nodes with coordinates + elevation
│   ├── Cosc521_ProjectReport.pdf  #Final Project Report
│   └── plots/                  # Plots and Images used for report
│
├── kelowna_road_data.Rmd       # Downloads road data via WFS + initial exploration
├── data_processing.Rmd         # Main pipeline: cleaning, graph nodes/edges, DEM mosaic, elevations, slope/3D length
├── data_analysis.Rmd           # Analysis of 2D vs 3D for specifc network science metrics
│
├── .gitignore                  # Ignores .DS_Store, .Rhistory, large .tif in tiles/, etc.
└── README.md                   # Project documentation (this file)
```

> **Note:** Large DEM tiles (`data_kelowna/tiles/*.tif`) are intentionally **gitignored**.

---

## 🛠️ R Markdown Workflow

- **`kelowna_road_data.Rmd` — Data download & exploration**  
  Downloads DRA road centerlines via **WFS**, reprojects to **EPSG:3005**, stores as GeoPackage, and performs basic inspection/plotting.

- **`data_processing.Rmd` — Main pipeline**  
  1) Import & standardize road geometry (EPSG:3005)  
  2) Normalize to LINESTRING, compute official vs geometry length, extract attributes  
  3) Generate **edges** with unique IDs  
  4) Extract deduplicated **nodes** from segment endpoints (snapped, 0.5 m)  
  5) Build/plot the **2D graph** for verification  
  6) Load & mosaic **1 m DEM tiles** (reprojected to EPSG:3005)  
  7) Extract **elevation** for nodes within DEM extent  
  8) Filter to **DEM‑covered edges**, compute **elevation difference, slope, and 3D length**  
  Exports: `final_nodes.csv`, `final_edges.csv`

- **`data_analysis.Rmd` — Network analysis & visualization**  
  Performs all graph-theoretic analysis using the processed node/edge tables.

  **Includes:**
  1) Load `final_nodes.csv` and `final_edges.csv` into `igraph`
  2) Construct paired **2D** (planar length) and **3D** (elevation-aware) graphs  
  3) Compute **node centrality**:
    - Betweenness  
    - PageRank  
    - Closeness
  4) Identify **top nodes/edges** in 2D vs 3D and map their spatial distribution  
  5) Compute and visualize **2D → 3D centrality differences** (diff maps) 
  6) Compute **edge betweenness** + join attributes (road class, name, length)
  7) Calculate **mean edge betweenness by road class**  
  8) Apply **Louvain community detection** to both models  
  9) Run **resilience simulations**:
    - Remove top-\(k\) nodes by betweenness  
    - Remove top-\(k\) edges by edge betweenness  
    - Track largest Connected Component(LCC) average path length

> Plots are not saved automatically

---

## ▶️ How to Reproduce

1. Open R / RStudio and install dependencies :  
   ```r
   install.packages(c("sf","tidyverse","dplyr","readr","tidyr","tibble","igraph","terra","ggplot2"))
   ```
2. Run **`kelowna_road_data.Rmd`** to download roads (creates `data_kelowna/DRA_Kelowna_Subset.gpkg`).  
3. Run **`data_processing.Rmd`** to generate outputs:  
   - `data_kelowna/final_nodes.csv`  
   - `data_kelowna/final_edges.csv`  
4. Run **`data_analysis.Rmd`** for all the analysis.

> Coordinate system: **EPSG:3005 (NAD83 / BC Albers)** throughout the pipeline.

---

## 👨‍💻 Author

**Aarav Gosalia**  
M. Sc. Computer Science | B.Sc. Data Science, Minor in Economics  
**University of British Columbia – Okanagan**  
📍 *Kelowna, BC, Canada*  
🌐 [aaravjgosalia.com](https://aaravjgosaliia.com)
