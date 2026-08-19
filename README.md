# Indian Railway Network and Delays
Indian Railway Network datasets for railway topology, station zones, train routes, schedules, and train delays, including September 2024 operational data.

Indian Railway Network datasets for railway topology, station zones, train routes, schedules, and train delays, including September 2024 operational data.

This repository provides datasets representing the **Indian Railway Network (IRN)** at the station, network, and train-route levels. The datasets combine railway network connectivity, station-to-zone mappings, scheduled train routes, and observed train arrival/departure delays, as described in the following paper:

**Paper Title:** RSTGCN: Railway-centric Spatio-Temporal Graph Convolutional Network for Train Delay Prediction

**Authors:** Koyena Chowdhury, Paramita Koley, Abhijnan Chakraborty, Saptarshi Ghosh

K. Chowdhury, A. Chakraborty, and S. Ghosh are with the Department of Computer Science and Engineering, Indian Institute of Technology Kharagpur, West Bengal - 721302, India. P. Koley is with the International Institute of Information Technology, Bhubaneswar, Odisha - 752054, India. 

The datasets are intended for **research and development in railway analytics, machine learning, graph learning, spatio-temporal modeling, transportation intelligence, and railway network analysis**.

---

## Dataset Contents

The repository currently contains the following datasets:

```text
├── IRN_edges.csv
├── stations_zones_mapping.json
├── train_delays_Sep2024.json
├── train_routes_delays_Sep2024.csv
└── train_routes_Sep2024.csv
```

### 1. `IRN_edges.csv`

This file represents the **railway network connectivity** between stations.

Example:

```csv
from,to,distance,ntrains
AADR,UHL,28,10
AADR,DLPC,15,6
AAG,WKA,6,1
AAG,MKPT,7,1
AAL,BUH,9,14
AAL,APR,13,16
```

#### Columns

| Column     | Description                                             |
| ---------- | ------------------------------------------------------- |
| `from`     | Source station code                                     |
| `to`       | Connected destination station code                      |
| `distance` | Distance between the two connected stations             |
| `ntrains`  | Number of trains associated with the network connection |

The file can be interpreted as an **edge list** of the IRN.

For example:

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

The `distance` and `ntrains` attributes can be used as edge features or weights depending on the research problem.

---

## 2. `stations_zones_mapping.json`

This file maps each railway station code to its corresponding **Indian Railways zone**.

Example:

```json
{
  "AADR": "NR",
  "AAG": "CR",
  "AAL": "SECR",
  "AAM": "SR",
  "AAR": "WR",
  "AAS": "NWR"
}
```

The structure is:

```text
station_code → railway_zone
```

#### Example

```text
AADR → NR
AAG  → CR
AAL  → SECR
```

This mapping can be used to perform:

* Railway-zone-wise network analysis
* Zone-level aggregation of train traffic
* Zone-wise delay analysis
* Visualization and community analysis
* Regional railway studies

### Zone codes

The values correspond to Indian Railways zone abbreviations, for example:

```text
NR    Northern Railway
CR    Central Railway
SECR  South East Central Railway
SR    Southern Railway
WR    Western Railway
NWR   North Western Railway
```

Additional zone mappings are provided directly in the JSON file.

---

# September 2024 Train Data

Three datasets provide detailed train-level operational information for **September 2024**:

1. `train_delays_Sep2024.json`
2. `train_routes_delays_Sep2024.csv`
3. `train_routes_Sep2024.csv`

These datasets complement each other and provide both the original nested representation and flattened/tabular representations of train schedules and delays.

---

## 3. `train_delays_Sep2024.json`

This file stores train delay information in a hierarchical JSON structure.

The hierarchy is:

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

Example:

```json
{
  "12303": {
    "01-09-2024": {
      "HWH": [
        "08:00 AM",
        "08:01 AM",
        "05M",
        "08:05 AM",
        "08:51 AM",
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

### JSON Structure

The outermost key is the **train number**:

```text
"12303"
```

The second-level key is the **date**:

```text
"01-09-2024"
```

The third-level key is the **station code**:

```text
"HWH"
```

The corresponding six-element array contains:

| Position | Meaning                  |
| -------: | ------------------------ |
|        1 | Scheduled arrival time   |
|        2 | Actual arrival time      |
|        3 | Arrival delay            |
|        4 | Scheduled departure time |
|        5 | Actual departure time    |
|        6 | Departure delay          |

For example:

```text
[
  scheduled_arrival,
  actual_arrival,
  arrival_delay,
  scheduled_departure,
  actual_departure,
  departure_delay
]
```

### Missing observations

Some train-date combinations may contain an empty station dictionary:

```json
"01-09-2024": {}
```

This indicates that no station-level records are available for that train on that date in the supplied dataset.

---

# 4. `train_routes_delays_Sep2024.csv`

This is the **tabular/flattened version** of train route and delay information.

Example:

```csv
train,date,station,sch_arr,act_arr,arr_delay,sch_dep,act_dep,dep_delay
12303,2024-09-02,HWH,08:00 AM,08:01 AM,0.0,08:00 AM,08:01 AM,0.0
12303,2024-09-02,BWN,09:05 AM,09:20 AM,15.0,09:08 AM,09:23 AM,15.0
12303,2024-09-02,DGR,09:57 AM,10:00 AM,3.0,09:59 AM,10:02 AM,3.0
12303,2024-09-02,ASN,10:32 AM,10:34 AM,2.0,10:37 AM,10:39 AM,2.0
12303,2024-09-02,CRJ,11:00 AM,11:04 AM,4.0,11:02 AM,11:06 AM,4.0
```

### Columns

| Column      | Description              |
| ----------- | ------------------------ |
| `train`     | Train number             |
| `date`      | Journey/observation date |
| `station`   | Station code             |
| `sch_arr`   | Scheduled arrival time   |
| `act_arr`   | Actual arrival time      |
| `arr_delay` | Arrival delay            |
| `sch_dep`   | Scheduled departure time |
| `act_dep`   | Actual departure time    |
| `dep_delay` | Departure delay          |

### Delay representation

The `arr_delay` and `dep_delay` fields are represented numerically, typically in **minutes**.

For example:

```text
arr_delay = 15.0
```

represents a 15-minute arrival delay.

This CSV representation is particularly convenient for:

* Statistical analysis
* Machine learning
* Time-series modeling
* Exploratory data analysis
* Train-level aggregation
* Station-level aggregation
* Delay prediction

Example:

```python
import pandas as pd

df = pd.read_csv("train_routes_delays_Sep2024.csv")

print(df.head())
print(df["arr_delay"].describe())
```

---

# 5. `train_routes_Sep2024.csv`

This file provides the **scheduled train routes** and station sequence information for September 2024.

Example:

```csv
stnSerialNumber,trainNumber,trainName,station_code,station_name,distance,arrivalTime,departureTime
1,12303,Poorva Express,HWH,Howrah,0,08:00 AM,08:00 AM
2,12303,Poorva Express,BWN,Bardhaman,95,09:05 AM,09:08 AM
3,12303,Poorva Express,DGR,Durgapur,157,09:57 AM,09:59 AM
4,12303,Poorva Express,ASN,Asansol,199,10:32 AM,10:37 AM
5,12303,Poorva Express,CRJ,Chittaranjan,224,11:00 AM,11:02 AM
```

### Columns

| Column            | Description                                             |
| ----------------- | ------------------------------------------------------- |
| `stnSerialNumber` | Sequential position of the station in the train route   |
| `trainNumber`     | Train number                                            |
| `trainName`       | Train name                                              |
| `station_code`    | Railway station code                                    |
| `station_name`    | Railway station name                                    |
| `distance`        | Distance associated with the station in the train route |
| `arrivalTime`     | Scheduled arrival time                                  |
| `departureTime`   | Scheduled departure time                                |


---

# Acknowledgement

This dataset collection is intended to facilitate research on **Artificial Intelligence, Machine Learning, Graph Neural Networks, and Intelligent Transportation Systems for Indian Railways**.

The datasets bring together railway network structure and train-level operational information in a form suitable for computational research and data-driven railway analysis.

