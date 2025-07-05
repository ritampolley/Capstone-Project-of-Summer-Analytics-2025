# Capstone-Project-of-Summer-Analytics-2025
# Capstone Project: Dynamic Pricing for Urban Parking Lots

**Program**: Summer Analytics 2025  
**Organized by**: Consulting & Analytics Club × Pathway  
**Prepared by**: Ritam Polley

---

## 1. Introduction

Urban parking lots are high-demand resources that often suffer from inefficiencies due to static pricing. This project aims to solve that by implementing a dynamic pricing system that adapts in real-time using live data and machine learning logic. The final solution uses Pathway to simulate streaming data and updates parking prices based on demand and competition.

---

## 2. Dataset Overview

The dataset includes 14 parking spaces observed over 73 days at 18 time points per day (8:00 AM to 4:30 PM). Each record includes:

- **Location**: Latitude, Longitude  
- **Parking Info**: Capacity, Occupancy, Queue Length  
- **Vehicle Info**: Type of incoming vehicle (car, bike, truck)  
- **External Info**: Traffic condition, Special day indicator  
- **Time**: LastUpdatedDate and LastUpdatedTime (combined into `timestamp`)  

---

## 3. Data Preprocessing Steps

- **Combine Date & Time**: Created a `timestamp` column by merging LastUpdatedDate and LastUpdatedTime.  
- **Rename Columns**: Standardized column names like `lot_id`, `occupancy`, `queue_length`, etc.  
- **Cleaned CSV**: Saved cleaned data as `cleaned_dataset.csv` for Pathway streaming.  

---

## 4. Model Implementation

### Model 1: Baseline Linear Pricing

- **Formula**:  
  `price_t+1 = price_t + α × (occupancy / capacity)`  
  - Base price = $10  
  - α = 2.0 (tunable factor)  
- **Justification**: Provides a simple benchmark by increasing price linearly with occupancy.

---

### Model 2: Demand-Based Pricing

- **Demand Function**:  
  `demand = α × occ_ratio + β × queue_norm - γ × traffic_norm + δ × is_special_day + ε × vehicle_weight`

- **Vehicle weights**:  
  - Car = 1.0  
  - Bike = 0.7  
  - Truck = 1.5

- **Price Formula**:  
  `price = base_price × (1 + λ × normalized_demand)`  
  (clipped between $5 and $20)

- **Justification**: Captures real-world demand factors like traffic, event days, and vehicle type.

---

### Model 3: Competitive Pricing Model

- **Logic**:
  - Calculates geographical distance between lots using `geopy`
  - If lot is full and nearby lots are cheaper → lower price or reroute
  - If nearby lots are expensive → increase price

- **Adjustment**:  
  `price += adjustment_factor × competitor_signal`

- **Justification**: Encourages business-aware pricing and optimal utilization.

---

## 5. Real-Time Streaming with Pathway

- Used `pathway.io.csv.read()` to stream `cleaned_dataset.csv`
- Defined schema using `pw.Schema` for structured input
- Applied pricing logic using `@pw.udf`
- Emitted output to `output.jsonl` using `pathway.io.jsonlines.write()`

---

## 6. Visualization

- Used **Bokeh** to create a real-time line plot for each parking lot:
  - **X-axis**: timestamp  
  - **Y-axis**: price  
- Real-time simulation with `ColumnDataSource.stream()`

---

## 7. Summary

| Model  | Key Feature             | Output Style        |
|--------|--------------------------|----------------------|
| 1      | Linear price vs occupancy | Simple benchmark     |
| 2      | Multi-variable demand     | Smooth dynamic price |
| 3      | Competitive geo-pricing   | Smart rerouting      |

This project demonstrates how dynamic pricing can be built, streamed in real time, and visualized effectively using only Python and essential data tools.

---

## 8. Future Work

- Add real-time rerouting suggestions  
- Improve geo-pricing with distance-weighted logic  
- Deploy using Pathway’s cloud API  

---

## 9. References

- [Pathway Developer Docs]
  (https://pathway.com/developers/user-guide/introduction/welcome/)  
- [Summer Analytics 2025 Problem Statement] 
- [Bokeh Docs]
  (https://docs.bokeh.org/en/latest/)  
