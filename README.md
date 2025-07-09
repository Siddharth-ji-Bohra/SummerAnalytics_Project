# SummerAnalytics_Project
# Dynamic Pricing System for Smart Parking

This project implements a real-time **dynamic pricing system** for urban parking spaces using live stream simulation. It ingests parking lot data and computes optimal prices based on:

- Live occupancy and queue length
- Traffic conditions and special day signals
- Vehicle types and nearby competitor pricing

We implemented three models:
1. **Model 1** – Linear pricing based on occupancy
2. **Model 2** – Multi-feature demand-based pricing
3. **Model 3** – Competition-aware adaptive pricing

Each model was designed to work on real-time data streams simulated via **Pathway**, with prices visualized using **Bokeh** in a notebook-based dashboard.

---

## Tech Stack Used

| Tool / Library      | Purpose                                   |
|---------------------|--------------------------------------------|
| **Python 3.10+**     | Core programming language                 |
| **Pathway**          | Stream processing and reactive pipelines |
| **Bokeh**            | Data visualization (line plots)          |
| **Panel** (optional) | Interactive dashboard rendering (locally)|
| **Pandas**           | Data preprocessing and export            |
| **Google Colab**     | Notebook development & testing platform  |
| **Jupyter Notebook** | Local option for live streaming dashboard|

## Architecture Diagram
![Untitled diagram _ Mermaid Chart-2025-07-09-090303](https://github.com/user-attachments/assets/1f319381-025f-4d51-8c94-0c3d6e07e9b0)


##  Detailed Explanation of Project Architecture & Workflow

This project builds a real-time dynamic pricing system for parking lots using **Pathway**, a stream processing engine. It simulates incoming smart parking data and applies three pricing models that adapt to live occupancy, demand features, and competitor behavior. Prices are visualized using Bokeh for easy analysis.

---

###  1. Data Ingestion Layer

- The system begins with `dataset.csv`, which contains simulated smart parking data.
- Key columns include:
  - `Occupancy`, `Capacity` → real-time usage
  - `QueueLength`, `TrafficConditionNearby` → congestion indicators
  - `IsSpecialDay`, `VehicleType` → external demand signals
  - `Latitude`, `Longitude`, `SystemCodeNumber` → spatial & identity context
  - `LastUpdatedDate`, `LastUpdatedTime` → merged into a `Timestamp` column

- The processed data is saved into `Parking_stream.csv` for use in streaming.

---

###  2. Stream Simulation with Pathway

- Pathway’s `replay_csv()` function simulates a real-time data stream from the CSV file.
- A schema (`ParkingSchema`) defines expected column types.
- The `input_rate` controls how fast data is streamed (e.g., 1000 rows/second).
- This enables us to test real-time logic within a notebook environment like Colab.

---

###  3. Model 1: Linear Occupancy-Based Pricing

- A basic pricing model based on how full a lot is:

  \[
  \text{Price} = \text{BasePrice} + \alpha \cdot \left(\frac{\text{Occupancy}}{\text{Capacity}}\right)
  \]

- Price is clamped between 0.5× and 2× base.
- Output is saved to `model1_output.jsonl`.

---

###  4. Model 2: Demand-Based Multi-Feature Pricing

- This model incorporates multiple features:

| Feature                | Description                             |
|------------------------|-----------------------------------------|
| Occupancy Ratio        | Real-time usage                         |
| Queue Length           | Signals congestion                      |
| Traffic Condition      | Mapped as low = 0, medium = 1, high = 2 |
| Special Day Indicator  | Boosts price on holidays or events      |
| Vehicle Type Weighting | e.g., car = 1.0, bike = 0.5, truck = 2.0|

- Computes a weighted demand score and normalizes it:

  \[
  \text{Price} = \text{BasePrice} \cdot (1 + \lambda \cdot \text{NormalizedDemand})
  \]

- Output is saved to `model2_output.jsonl`.

---

###  5. Model 3: Competition-Aware Dynamic Pricing

- This model builds on Model 2 and adds **competitor awareness**:
  - Computes **Haversine distance** between parking lots using lat/long
  - For each lot, finds other lots within a 500m radius at the same timestamp
  - Compares its Model 2 price and occupancy to competitor averages

- Adjusts final price based on:
  - If the lot is **more expensive but less occupied**, it suggests a **discount**
  - If it’s **cheaper and high in demand**, it can **raise the price**
  - Otherwise, the price stays close to Model 2 output

- Final output is written to `model3_output.jsonl`.

---

###  6. Visualization with Bokeh

- All models are visualized using **Bokeh** plots.
- Graphs show how pricing changes over time using the `t` timestamp.
- While Colab only allows static visualization, real-time plotting is possible with `Panel` and `slide_window` in local environments.

---

###  7. Workflow Summary

1. Preprocess data and create `Timestamp`
2. Stream data using Pathway
3. Apply Model 1 logic
4. Apply Model 2 logic (multi-feature demand)
5. Apply Model 3 logic (competition-aware adjustments)
6. Visualize pricing evolution using Bokeh

---

###  Architecture Layers

- **Ingestion Layer**: Data loading and timestamp parsing
- **Streaming Layer**: Real-time simulation using `replay_csv`
- **Modeling Layer**:
  - Model 1: Linear pricing
  - Model 2: Demand-driven pricing
  - Model 3: Competition-adjusted pricing
- **Output Layer**:
  - Writes to `modelX_output.jsonl`
  - Visualized with Bokeh
- **Optional UI Layer**: Real-time plots using Panel (locally)

---

###  Why This Architecture Works

- Modular and extensible pricing design
- Fast real-time computation using Pathway's streaming engine
- Works in both static (Colab) and dynamic (local) environments
- Easy to plug in future models (e.g., revenue optimization, time-of-day pricing)
