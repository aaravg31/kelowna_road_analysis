# 🌐 Kelowna Road Network as a Spatial Graph: Elevation-Aware Connectivity and Resilience Analysis

This repository contains my final project for **COSC 521 – Network Science** at **UBC Okanagan**.  

The goal is to build an **elevation‑aware road network** for the City of Kelowna by integrating the **BC Digital Road Atlas (DRA)** road centerlines with **LiDAR‑derived DEM** tiles, producing a **3D graph** for terrain‑aware analysis (slopes, elevation change, and 3D lengths).

---

## 🎯 Project Overview  
I construct a graph where **nodes** are road intersections (segment endpoints) and **edges** are road segments with attributes:
- 2D length (from geometry and official DRA length)
- Elevation at both endpoints (from DEM)
- **Slope** and **3D geometric length** (rise/run + Pythagorean length)

This enables **terrain‑aware metrics** and prepares the pipeline for **elevation‑aware shortest paths** and **resilience** analysis.

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
├── kelowna_road_data.Rmd       # Downloads road data via WFS + initial exploration
├── data_processing.Rmd         # Main pipeline: cleaning, graph nodes/edges, DEM mosaic, elevations, slope/3D length
├── data_analysis.Rmd           # Analysis over the 3D graph (centrality, comparisons) – in progress
├── Cosc521_final_report.pdf    # Final Report - in progress
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

- **`data_analysis.Rmd` — 3D network analysis (up next)**  
  Will construct the 3D graph, visualize slope/elevation patterns, compare **2D vs 3D** distances, and compute centrality/resilience metrics.

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
4. (Next) Run **`data_analysis.Rmd`** for the 3D analysis and plots.

> Coordinate system: **EPSG:3005 (NAD83 / BC Albers)** throughout the pipeline.

---

## 👨‍💻 Author

**Aarav Gosalia**  
M. Sc. Computer Science | B.Sc. Data Science, Minor in Economics  
**University of British Columbia – Okanagan**  
📍 *Kelowna, BC, Canada*  
🌐 [aaravjgosalia.com](https://aaravjgosaliia.com)
