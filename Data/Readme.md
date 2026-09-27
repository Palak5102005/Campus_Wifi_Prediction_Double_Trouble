# Data Documentation

## 1. Dataset Overview

This project combines two sources of data:

1. Student survey responses
2. Direct Wi-Fi network measurements collected across campus

The survey was used to understand where students experience connectivity problems and how those problems affect their academic and online activities.

The Wi-Fi measurements were collected to quantify actual network behaviour across locations, times and crowd conditions.

---

## 2. Student Survey

- Total responses: 74
- Source: Student survey conducted for this project
- Purpose: Understand user-reported Wi-Fi problems
- Usage: Survey responses were cleaned, normalized and aggregated into location-level profiles.

The repository contains aggregated survey information rather than personally identifiable raw responses.

### Main survey insights

| Survey Category | Mentions |
|---|---:|
| Hostel Room | 49 |
| Academic Buildings | 13 |
| Hostel Common Area | 12 |
| Library | 7 |
| Mess / Canteen | 6 |
| Other | 3 |

---

## 3. Wi-Fi Measurement Dataset

- Total measurements: 429
- Source: Direct Wi-Fi measurements collected during the project
- Measurement type: Observational
- Main variables:
  - Location
  - Time
  - Crowd density
  - Signal strength
  - Ping
  - Download speed
  - Upload speed
  - Connection status

The measurements were collected across multiple campus locations and time periods.

---

## 4. Data Types

### Observed data

The following values were directly observed during data collection:

- Location
- Time
- Crowd density
- Signal strength
- Ping
- Download speed
- Upload speed
- Connection status

### Derived data

Some variables used for modelling were derived from the observed measurements, including:

- Hour
- Session / time period
- Ping failure indicator
- Poor connectivity indicator
- Location-level median metrics
- Location-level failure rates

### Synthetic data

No synthetic network measurements were intentionally generated for model training.

---

## 5. Target Variable

The model predicts whether a Wi-Fi condition represents poor connectivity.

The target is derived from network measurements using the project's connectivity criteria rather than being manually assigned.

---

## 6. AI / Machine Learning Usage

A machine-learning classification model was trained using the collected Wi-Fi measurements.

The current prediction interface uses:

- Location
- Time
- Crowd density

to estimate the probability of poor connectivity.

A Random Forest classification pipeline is used for the current prototype.

The chatbot interface converts a natural-language query such as:

"What will the Wi-Fi be like at LIBRARY at 9 PM with crowd 3?"

into structured inputs for the prediction model.

---

## 7. Limitations

The current dataset is relatively small and was collected over a limited observation period.

Wi-Fi performance can vary significantly depending on:

- Time of day
- Number of connected users
- Physical location
- Network load
- Device differences
- Temporary network conditions

Therefore, a single live measurement should not be interpreted as definitive validation of the model.

Future iterations will expand the dataset across more locations, days, time periods and crowd conditions.

---

## 8. Live Validation

The project includes a planned/current live validation workflow in which model predictions can be compared against real-time measurements from a phone at the same location.

These measurements are intended to provide an independent check of model predictions and will also help improve future iterations of the dataset and model.
