# UJIIndoorLoc Dataset

This project uses the official **UJIIndoorLoc** indoor positioning dataset.

## Overview

UJIIndoorLoc is a real-world indoor positioning dataset collected from WiFi signal measurements inside multiple university buildings.

Each observation contains received signal strength (RSSI) values collected from up to **520 WiFi Access Points (WAPs)** together with the corresponding building, floor, and location information.

The dataset is one of the most widely used benchmark datasets for indoor localization research.

## Dataset Contents

The dataset contains the following files:

```text
raw/
├── trainingData.csv
└── validationData.csv
```

Each record includes:

- RSSI measurements from 520 WiFi Access Points
- Building identifier
- Floor identifier
- Geographic coordinates
- Additional metadata describing each measurement

A value of **100** indicates that the corresponding WiFi Access Point was **not detected** during data collection.

## Why This Dataset?

UJIIndoorLoc naturally contains sparse and noisy wireless signal measurements, making it well suited for evaluating data reliability under challenging conditions.

It enables the proposed **Data Reliability Score (DRS)** framework to assess data quality in signal-based localization scenarios.

## Download

This dataset is **not included** in this repository because it is publicly available and consists of large static files.

Download the official dataset and place the following files into the `raw/` directory.

```text
raw/
├── trainingData.csv
└── validationData.csv
```

## Notes

The UJIIndoorLoc dataset is a **static public dataset** whose contents do not change over time.

To ensure reproducibility, the original files should remain unchanged before preprocessing.
