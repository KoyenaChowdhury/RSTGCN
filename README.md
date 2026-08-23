# Indian Railway Network and Delays

Datasets for Indian Railway network topology, station-to-zone mappings, scheduled train routes, and observed train delays — including full operational data for September 2024.

## Overview

This repository provides datasets representing the **Indian Railway Network (IRN)** at the station, network, and train-route levels, spanning **4,735 stations across 17 zones** — one of the most comprehensive Indian railway datasets curated for research to date. The datasets combine railway network connectivity, station-to-zone mappings, scheduled train routes, and observed train arrival/departure delays, as described in the following paper:

> **RSTGCN: Railway-centric Spatio-Temporal Graph Convolutional Network for Train Delay Prediction**
> Koyena Chowdhury, Paramita Koley, Abhijnan Chakraborty, Saptarshi Ghosh
> [arXiv:2510.01262](https://arxiv.org/abs/2510.01262)

> Recently accepted at **IEEE Transactions on Intelligent Transportation Systems (T-ITS)**

K. Chowdhury, A. Chakraborty, and S. Ghosh are with the Department of Computer Science and Engineering, Indian Institute of Technology Kharagpur, West Bengal – 721302, India.
P. Koley is with the International Institute of Information Technology, Bhubaneswar, Odisha – 752054, India.

These datasets are intended for research and development in railway analytics, machine learning, graph learning, spatio-temporal modeling, transportation intelligence, and railway network analysis.

## Table of Contents

- [Dataset Contents](#dataset-contents)
- [Network and Station Datasets](#network-and-station-datasets)
  - [1. `IRN_edges.csv`](#1-irn_edgescsv)
  - [2. `stations_zones_mapping.json`](#2-stations_zones_mappingjson)
- [September 2024 Train-Level Datasets](#september-2024-train-level-datasets)
  - [3. `train_delays_Sep2024.json`](#3-train_delays_sep2024json)
  - [4. `train_routes_delays_Sep2024.csv`](#4-train_routes_delays_sep2024csv)
  - [5. `train_routes_Sep2024.csv`](#5-train_routes_sep2024csv)
- [Citation](#citation)
- [Acknowledgement](#acknowledgement)

## Dataset Contents

```text
├── IRN_edges.csv
├── stations_zones_mapping.json
├── train_delays_Sep2024.json
├── train_routes_delays_Sep2024.csv
└── train_routes_Sep2024.csv
```

## Network and Station Datasets

### 1. `IRN_edges.csv`

The **railway network connectivity** between stations, as an edge list.

We represent the Indian Railway system by a network, where the nodes/vertices are the stations, and there is an edge between two nodes and if and only if they are consecutive stations on at least one train route. In other words, there is a link/edge only if there is a direct track/line connecting the two stations with no train halts in between for a particular train. The Indian Railway Network (IRN) thus formed comprises stations (the major train stations in India) connected by edges (direct lines).

```csv
from,to,distance,ntrains
AADR,UHL,28,10
AADR,DLPC,15,6
AAG,WKA,6,1
```

| Column | Description |
|---|---|
| `from` | Source station code |
| `to` | Connected destination station code |
| `distance` | Distance between the two connected stations |
| `ntrains` | Number of trains associated with the network connection |

`distance` and `ntrains` can be used as edge features or weights, depending on the research problem.

**Loading the network with NetworkX:**

```python
import pandas as pd
import networkx as nx

edges = pd.read_csv("IRN_edges.csv")

G = nx.from_pandas_edgelist(
    edges,
    source="from",
    target="to",
    edge_attr=["distance", "ntrains"]
)

print(G.number_of_nodes())
print(G.number_of_edges())
```

### 2. `stations_zones_mapping.json`

Maps each railway station code to its corresponding **Indian Railways zone**, as a flat `station_code → zone` dictionary.

```json
{
  "AADR": "NR",
  "AAG": "CR",
  "AAL": "SECR",
}
```

For example: `AADR → NR`, `AAG → CR`, `AAL → SECR`.

A few of the zone codes that appear above:

| Code | Zone |
|---|---|
| NR | Northern Railway |
| CR | Central Railway |
| SECR | South East Central Railway |
| SR | Southern Railway |
| WR | Western Railway |
| NWR | North Western Railway |

The full set of zone codes (17 in total) is available directly in the JSON file.

This mapping supports:
- Railway-zone-wise network analysis
- Zone-level aggregation of train traffic
- Zone-wise delay analysis
- Visualization and community analysis
- Regional railway studies

## September 2024 Train-Level Datasets

Three datasets provide detailed train-level operational information for September 2024. They complement each other: the first is a nested, hierarchical representation, and the latter two are flattened/tabular representations of the same underlying schedule and delay information.

### 3. `train_delays_Sep2024.json`

Train delay information in a hierarchical structure:

```text
Train
 └── Date
      └── Station
           ├── Scheduled Arrival
           ├── Actual Arrival
           ├── Arrival Delay
           ├── Scheduled Departure
           ├── Actual Departure
           └── Departure Delay
```

```json
{
  "12303": {
    "01-09-2024": {
      "HWH": [
        "08:00 AM",
        "08:01 AM",
        "01M",
        "08:50 AM",
        "08:55 AM",
        "05M"
      ],
      "BWN": [
        "09:05 PM",
        "09:20 PM",
        "15M",
        "09:08 PM",
        "09:23 PM",
        "15M"
      ]
    }
  }
}
```

Keys, from outermost to innermost, are the **train number** (e.g. `"12303"`), the **date** (e.g. `"01-09-2024"`), and the **station code** (e.g. `"HWH"`). Each station maps to a six-element array:

| Position | Meaning |
|---:|---|
| 1 | Scheduled arrival time |
| 2 | Actual arrival time |
| 3 | Arrival delay |
| 4 | Scheduled departure time |
| 5 | Actual departure time |
| 6 | Departure delay |

Delays in this file are formatted as strings (e.g. `"05M"` = 5 minutes); the flattened CSV below expresses the same delays as plain numeric minutes instead.

**Missing observations:** some train–date combinations contain an empty station dictionary, e.g. `"01-09-2024": {}`, meaning no station-level records are available for that train on that date in the supplied data.

**Loading in Python:**

```python
import json

with open("train_delays_Sep2024.json") as f:
    delays = json.load(f)

print(delays["12303"]["01-09-2024"]["HWH"])
```

### 4. `train_routes_delays_Sep2024.csv`

The **tabular/flattened version** of train route and delay information — one row per train, date, and station.

```csv
train,date,station,sch_arr,act_arr,arr_delay,sch_dep,act_dep,dep_delay
12303,2024-09-02,HWH,08:00 AM,08:01 AM,0.0,08:00 AM,08:01 AM,0.0
12303,2024-09-02,BWN,09:05 AM,09:20 AM,15.0,09:08 AM,09:23 AM,15.0
12303,2024-09-02,DGR,09:57 AM,10:00 AM,3.0,09:59 AM,10:02 AM,3.0
```

| Column | Description |
|---|---|
| `train` | Train number |
| `date` | Journey/observation date |
| `station` | Station code |
| `sch_arr` | Scheduled arrival time |
| `act_arr` | Actual arrival time |
| `arr_delay` | Arrival delay (minutes) |
| `sch_dep` | Scheduled departure time |
| `act_dep` | Actual departure time |
| `dep_delay` | Departure delay (minutes) |

`arr_delay` and `dep_delay` are numeric, typically in minutes — e.g. `arr_delay = 15.0` represents a 15-minute arrival delay.

This representation is convenient for statistical analysis, machine learning, time-series modeling, exploratory data analysis, and train- or station-level aggregation:

```python
import pandas as pd

df = pd.read_csv("train_routes_delays_Sep2024.csv")

print(df.head())
print(df["arr_delay"].describe())
```

### 5. `train_routes_Sep2024.csv`

The **scheduled train routes** and station sequence for each train in September 2024.

```csv
stnSerialNumber,trainNumber,trainName,station_code,station_name,distance,arrivalTime,departureTime
1,12303,Poorva Express,HWH,Howrah,0,08:00 AM,08:00 AM
2,12303,Poorva Express,BWN,Bardhaman,95,09:05 AM,09:08 AM
3,12303,Poorva Express,DGR,Durgapur,157,09:57 AM,09:59 AM
```

| Column | Description |
|---|---|
| `stnSerialNumber` | Sequential position of the station in the train route |
| `trainNumber` | Train number |
| `trainName` | Train name |
| `station_code` | Railway station code |
| `station_name` | Railway station name |
| `distance` | Distance associated with the station in the train route |
| `arrivalTime` | Scheduled arrival time |
| `departureTime` | Scheduled departure time |

```python
import pandas as pd

routes = pd.read_csv("train_routes_Sep2024.csv")

print(routes[routes["trainNumber"] == 12303])
```

## Citation

If you use these datasets in your research, please cite:

```bibtex
@article{chowdhury2025rstgcn,
  title   = {RSTGCN: Railway-centric spatio-temporal graph convolutional network for train delay prediction},
  author  = {Chowdhury, Koyena and Koley, Paramita and Chakraborty, Abhijnan and Ghosh, Saptarshi},
  journal = {arXiv preprint arXiv:2510.01262},
  year    = {2025}
}

}
```

## Acknowledgement

This dataset collection is intended to facilitate research on Artificial Intelligence, Machine Learning, Graph Neural Networks, and Intelligent Transportation Systems for Indian Railways. The datasets bring together railway network structure and train-level operational information in a form suitable for computational research and data-driven railway analysis.
