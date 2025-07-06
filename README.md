# SummerAnalytics_Project
# 🚗 Dynamic Parking Price Optimization using Pathway

This project implements a real-time dynamic pricing system for parking spaces using the **Pathway** stream processing engine and **Bokeh** for visualization.

We simulate and analyze data from a smart parking system to design two pricing models that respond to real-time demand and traffic conditions.

---

## 📦 Tech Stack Used

- **Python 3.10+**
- **Pathway** (streaming computation framework)
- **Bokeh** (visualization)
- **Panel** (optional for dashboard rendering)
- **Google Colab** (used for implementation and testing)
- **Pandas** (for pre-processing and exporting)
- **Matplotlib / Seaborn** (optional visual checks)

---

## 🏗️ Architecture Diagram (Mermaid)

```mermaid
flowchart TD
    A[Input CSV - Parking Data] --> B[Pathway Stream (replay_csv)]
    B --> C[Model 1 - Linear Occupancy Pricing]
    B --> D[Model 2 - Demand-Based Pricing]
    C --> E[Price Output JSONL]
    D --> E
    E --> F[Static Bokeh Plot]
    F --> G[Final Output for Analysis]
