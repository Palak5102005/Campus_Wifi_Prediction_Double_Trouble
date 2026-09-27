#  Campus Wi-Fi Predictive Assistant

> **Predict campus Wi-Fi reliability before you reach the location.**

An ML-powered campus Wi-Fi intelligence system combining real-world Wi-Fi measurements, student survey insights, contextual factors, and machine learning to predict the likelihood of poor connectivity at a given **location, time, and crowd level**.

---

##  Project Demo
Example query:

> **"What will the Wi-Fi be like at LIBRARY at 9 PM with crowd 3?"**

```text
Location → LIBRARY
Time     → 9:00 PM
Crowd    → 3

↓

Risk        → HIGH
Probability → 82.5%
```

---

#  1. The Problem

Campus Wi-Fi problems are usually **reactive**.

A student reaches a classroom, library, hostel, or common area and only then discovers that:

- the connection is slow,
- the network cannot be accessed,
- downloads are extremely slow,
- the connection keeps dropping,
- or the network becomes unreliable when the area gets crowded.

This led to a simple question:

> ### **Can we predict Wi-Fi reliability before a student reaches a location?**

Instead of only monitoring the network after a problem occurs, we wanted to build a system that could **anticipate connectivity problems from contextual conditions**.

---

#  2. Understanding the User

We first conducted a campus survey to understand whether this was a significant student problem.

## **74 student responses**

The survey captured:

- Where students experience Wi-Fi problems
- What type of problems they experience
- How frequently they experience them
- How those problems affect their work

### Survey Insights

The strongest concentration of reported problems came from **hostel rooms**, followed by academic buildings, hostel common areas, libraries, and mess/canteen areas.

For hostel rooms:

| Problem | Mentions |
|---|---:|
| Slow speed | 32 |
| Unable to connect | 25 |
| Frequent disconnection | 9 |
| Other | 1 |

The survey provided the **user-side view of the problem**.

However, survey responses alone could not tell us exactly how the network was behaving.

This led to the next question:

> **Can we combine student experience with objective network measurements?**

---

#  3. Collecting Real-World Wi-Fi Data

We collected **429 Wi-Fi measurements** across campus.

Each observation captured network and contextual information such as:

| Feature | Description |
|---|---|
| Location | Where the measurement was taken |
| Time | Time of measurement |
| Session | Morning / Night context |
| Crowd Density | Estimated crowd level |
| Signal (%) | Wi-Fi signal percentage |
| Signal (dBm) | Signal strength |
| Ping (ms) | Network latency |
| Link (Mbps) | Connection link speed |
| Download (Mbps) | Actual download throughput |
| Upload (Mbps) | Actual upload throughput |
| Status | Connection state |

---

#  4. From Raw Measurements to Network Profiles

We aggregated measurements to build **location-level network profiles**.

For each location we analysed:

- Number of measurements
- Median signal strength
- Median download speed
- Ping failure rate
- Median crowd density

Example:

| Location | Measurements | Median Download | Ping Failure Rate | Median Crowd |
|---|---:|---:|---:|---:|
| NEW SAC | 101 | 0.00 Mbps | 63.4% | 4 |
| CLASSROOM | 65 | 8.67 Mbps | 100% | 2 |
| LIBRARY | 20 | 0.34 Mbps | 100% | 3 |
| CANTEEN | 26 | 0.34 Mbps | 84.6% | 2 |

These profiles became the bridge between the **raw network data** and the **prediction layer**.

---

#  5. Defining Poor Connectivity

We converted raw network measurements into a binary target:

```text
poor_connectivity
```

A measurement was classified as poor connectivity when it crossed the defined download-speed threshold or experienced a ping failure.

```text
Location
+
Time
+
Crowd Density

↓

Poor Connectivity
YES / NO
```

In the collected dataset:

```text
Poor connectivity → 363
Good connectivity  → 66
```

Therefore, approximately **84.6% of collected measurements** were classified as poor connectivity under our project definition.

---

#  6. Machine Learning Layer

We built a classification pipeline using a **Random Forest model**.

### Model Input

```text
Location
Time
Crowd Density
```

### Model Output

```text
Probability of Poor Connectivity
+
Risk Classification
```

Example:

```text
Location : NEW SAC
Time     : 9:00 PM
Crowd    : 4

↓

Probability of Poor Connectivity
≈ 86.7%

↓

Risk
HIGH
```

Another example:

```text
Location : LIBRARY
Time     : 9:00 PM
Crowd    : 3

↓

Probability
≈ 82.5%

↓

Risk
HIGH
```

The key shift is from **descriptive analysis to prediction**.

Instead of only asking:

> "How was Wi-Fi at the Library?"

the system attempts to answer:

> **"Given this location, time and expected crowd, how likely is poor connectivity?"**

---

#  7. Context-Aware Prediction

Crowd density is included as a contextual input.

For one prediction scenario:

| Crowd | Predicted Poor Connectivity |
|---:|---:|
| 1 | 54.57% |
| 2 | 78.44% |
| 3 | 81.32% |
| 4 | 86.68% |
| 5 | 86.68% |

This demonstrates how the prediction can respond to different contextual conditions rather than treating Wi-Fi quality as a fixed property of a location.

---

#  8. From ML Model to Usable Product

We moved beyond a notebook-based ML model and built an **interactive chatbot using Gradio**.

### Example Query

> **"What will the Wi-Fi be like at LIBRARY at 9 PM with crowd 3?"**

### Processing Flow

```text
Natural Language Query
        ↓
Parameter Extraction
        ↓
Location + Time + Crowd
        ↓
Prediction Engine
        ↓
Random Forest Model
        ↓
Probability
        ↓
Risk Classification
        ↓
User-Friendly Response
```

---

#  9. System Architecture

```text
                 ┌─────────────────────┐
                 │   Student Survey    │
                 │    74 Responses     │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ User Problem &      │
                 │ Experience Analysis │
                 └──────────┬──────────┘
                            │
                 ┌──────────▼──────────┐
                 │ 429 Wi-Fi           │
                 │ Measurements        │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Data Cleaning &     │
                 │ Feature Engineering │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Location-Level      │
                 │ Network Profiles    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Random Forest       │
                 │ Classification      │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Prediction Engine   │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Gradio Chatbot      │
                 └─────────────────────┘
```

---

#  10. Results

| Metric | Value |
|---|---:|
| Student survey responses | **74** |
| Wi-Fi measurements | **429** |
| Measurement locations | **11** |
| ML approach | **Random Forest Classification** |
| Interface | **Gradio Chatbot** |
| Prediction inputs | **Location + Time + Crowd** |
| Prediction output | **Poor-connectivity probability + risk** |

### Connectivity Distribution

```text
Actual poor connectivity
84.6%

Good connectivity
15.4%
```

The model's predictions across the evaluated dataset resulted in:

```text
Predicted poor connectivity
75.8%

Predicted good connectivity
24.2%
```

These are class distributions, **not model accuracy**.

---

#  11. Connecting Survey + Network Data

The two datasets serve different purposes.

### Survey answers:

> **Where and how do students experience the problem?**

### Network measurements:

> **What is the network actually doing?**

Together:

```text
Student Experience
       +
Network Behaviour
       ↓
Contextual Understanding
       ↓
Predictive Wi-Fi Assistant
```

The survey acts as the **user research layer**, while the Wi-Fi measurements form the **technical modelling layer**.

---


#  12. Repository Structure

```text
campus-wifi-predictor/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── wifi_measurements.csv
│   └── survey_location_profile.csv
│
├── notebooks/
│   ├── 01_data_collection_analysis.ipynb
│   ├── 02_survey_analysis.ipynb
│   ├── 03_ml_prediction.ipynb
│   └── 04_chatbot.ipynb
│
├── screenshots/
│   ├── chatbot.png
│   ├── survey_analysis.png
│   ├── location_profile.png
│   └── prediction.png
│
└── presentation/
    └── presentation.pdf
```

---

#  13. Tech Stack

### Data Processing
- Python
- Pandas
- NumPy

### Machine Learning
- Scikit-learn
- Random Forest
- Feature Engineering
- Classification

### Product Layer
- Gradio
- Python

### Development
- Kaggle
- Jupyter Notebook

---

#  15. Running the Project

### Clone the repository

```bash
git clone <YOUR-REPOSITORY-URL>
cd campus-wifi-predictor
```

### Install dependencies

```bash
pip install -r requirements.txt
```

### Run the chatbot notebook

Open:

```text
notebooks/04_chatbot.ipynb
```

Run the notebook cells sequentially to launch the interactive Wi-Fi assistant.

---

#  16. Demo

###  Live Demo

**https://3cbb47174c25d82818.gradio.live/**

#  17. Limitations

### Limited temporal coverage
The measurements were collected during the project period rather than continuously across many days and weeks.

### Dataset size
429 measurements provide an initial dataset, but a larger and more diverse dataset would support more robust modelling.

### Crowd density
Crowd is represented using a discrete scale rather than the exact number of connected devices/users.

### Spatial coverage
Predictions are most meaningful for locations represented in the collected dataset.

### Model validation
More extensive real-world validation across different days, locations and time periods is required before treating the predictions as production-grade.

---

#  18. Future Scope

## Continuous Live Data Collection

Collect Wi-Fi measurements across:

- Different days
- Different times
- Weekdays vs weekends
- Different crowd levels
- Academic and non-academic periods

## Live Prediction Validation

```text
Predicted Condition
        ↓
Real-Time Phone Measurement
        ↓
Actual Condition
        ↓
Prediction Match / Mismatch
```

## Confidence & Evidence Layer

Future versions can combine:

```text
Prediction
+
Confidence
+
Historical Measurements
+
Survey Evidence
```

## Recommendation Engine

The system can eventually move from prediction to recommendation:

> "Library is predicted to have poor connectivity at 9 PM. Consider another available campus location."

## Continuous Learning

New measurements can continuously update location profiles and periodically retrain the ML model.

---

#  19. From Prediction to Decision Support

The current system answers:

> **"What is the expected Wi-Fi condition here?"**

The future system can answer:

> **"Where should I go if I need reliable Wi-Fi right now?"**

```text
Monitoring
      ↓
Analysis
      ↓
Prediction
      ↓
Validation
      ↓
Recommendation
```

---

#  20. Project Journey

```text
Problem Discovery
       ↓
Student Survey
       ↓
74 User Responses
       ↓
Identify Problem Locations
       ↓
Collect Real Wi-Fi Measurements
       ↓
429 Network Observations
       ↓
Data Cleaning
       ↓
Feature Engineering
       ↓
Location-Level Network Profiles
       ↓
Define Poor Connectivity
       ↓
ML Classification
       ↓
Scenario-Based Prediction
       ↓
Natural Language Interface
       ↓
Interactive Wi-Fi Chatbot
       ↓
Live Validation
       ↓
Future Recommendation Layer
```

The goal was to move from:

> **Understanding the problem → Measuring the problem → Predicting the problem → Making the prediction usable**

---

#  Core Idea

## **Don't wait for bad Wi-Fi to happen. Predict it before it happens.**

---


Add an appropriate open-source license if you intend to make the repository publicly reusable.
