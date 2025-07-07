# Dynamic Pricing for Urban Parking Lots using Pathway

## Project Overview

This project develops a dynamic pricing model for urban parking spaces, designed to adjust prices in real-time based on various influencing factors. The primary goal is to optimize parking prices to ensure efficient space utilization, manage demand, and maximize revenue, while maintaining a smooth and explainable pricing variation.

The system simulates real-time data ingestion and processing using **Pathway**, allowing for continuous price predictions and output.

## Background & Motivation

Urban parking spaces are a limited and highly demanded resource. Static pricing often leads to either underutilization or overcrowding. This project addresses these challenges by simulating a real-time pricing system using:
* Real-time streaming with Pathway
* Demand-based pricing logic (Model 2)
* Basic economic factors like queue length, traffic, and vehicle type

## Core Features

* **Real-time Price Calculation:** Prices are dynamically computed based on incoming data streams using Pathway.
* **Multi-factor Pricing Logic (Demand-Based):** The pricing model incorporates current parking conditions, including:
    * Occupancy rate
    * Queue length
    * Nearby traffic conditions
    * Special event indicators
    * Vehicle type
* **Base Price Integration:** All pricing calculations stem from a defined base price (e.g., $10).
* **Output to CSV:** The computed real-time prices are continuously written to a `pricing_output.csv` file.
* **Basic Visualization:** Includes a simple `matplotlib` plot to visualize price trends from the generated output CSV.

## Pricing Model Implemented

This notebook specifically implements a **Demand-Based Price Function (referred to as Model 2)**. This model constructs a mathematical demand function that incorporates multiple key features. The calculated demand then directly influences the price adjustment from the base price.

### Model 2: Demand-Based Price Function

This model dynamically calculates price based on a set formula using the incoming data.

* **Demand Factors Used:** Occupancy, Capacity, QueueLength, TrafficConditionNearby, IsSpecialDay, and VehicleType.
* **Traffic Mapping:**
    * `low`: 1
    * `medium`: 2
    * `high`: 3
* **Vehicle Type Weights:**
    * `car`: 1.0
    * `bike`: 0.7
    * `truck`: 1.5
* **Conceptual Demand Calculation (within `compute_price` UDF):**
    `demand = (0.5 * (Occupancy / Capacity) + 0.3 * QueueLength - 0.2 * traffic + 0.1 * IsSpecialDay + 0.05 * vehicle_weight)`
* **Price Calculation:** The demand is normalized (clamped between 0 and 1), and then the price is calculated as `round(10 * (1 + 0.2 * normalized), 2)`. This ensures prices fluctuate around the base price ($10) based on demand.

## Technology Stack

* **Python:** Core programming language.
* **Pandas & NumPy:** For data manipulation and numerical operations.
* **Pathway:** For real-time data ingestion, stream processing, and continuous prediction emission.
* **Matplotlib:** For basic visualization of price trends.

## Getting Started

To run this project, you will need a Google Colab environment and the `dataset.csv` file.

### Setup (Google Colab)

1.  **Upload `dataset.csv`:** Upload your `dataset.csv` file directly to your Google Colab environment (e.g., by dragging it to the files sidebar).
2.  **Open a New Notebook:** Create a brand new Google Colab notebook.
3.  **Install Dependencies:** Run the following commands in the first code cell of your Colab notebook to install all necessary libraries:
    ```bash
    !pip install pandas numpy scipy pathway matplotlib
    ```
    * **Important Note on Pathway:** If you encounter `AttributeError` issues with Pathway, please try running `!pip install pathway` in a **fresh Colab notebook** as the very first step. Environmental conflicts can occur.

4.  **Copy Code:** Copy the code provided cell-by-cell from the `SA_CAPSTONE_PROJECT.ipynb` notebook into corresponding cells in your Colab notebook.

### Running the Project

Follow the structure of the provided notebook:

1.  **Initial Setup and Data Loading:** Run the cells that import libraries, load `dataset.csv`, and perform initial data preprocessing (including `timestamp`, `lot_id` creation, and `dropna`).
2.  **Define Pathway Schema and UDF:** Run the cells that define the `ParkingInput` schema and the `compute_price` Pathway UDF.
3.  **Ingest Data and Compute Prices with Pathway:** Run the cells that use `pw.debug.table_from_pandas` to ingest your prepared DataFrame into Pathway and apply the `compute_price` UDF to create a `pricing` stream.
4.  **Write Output:** Run the cell that uses `pw.io.csv.write` to specify the output CSV file (`pricing_output.csv`).
5.  **Start Pathway Execution:** **Uncomment and run the `pw.run()` cell**. This will start the Pathway pipeline, process the data, and continuously write the pricing results to `pricing_output.csv`.
6.  **Visualize Results:** Run the final cells that use `matplotlib` to load `pricing_output.csv` and plot the price trends for a selected parking lot.

## Assumptions Made in the Notebook

* **Dataset Availability:** Assumes the `dataset.csv` file is present in the Colab environment with expected columns (`LastUpdatedDate`, `LastUpdatedTime`, `SystemCodeNumber`, `Occupancy`, `Capacity`, `QueueLength`, `TrafficConditionNearby`, `IsSpecialDay`, `VehicleType`, `Latitude`, `Longitude`).
* **Vehicle Type Weights:** `car = 1.0`, `bike = 0.7`, `truck = 1.5`.
* **Traffic Mapping:** `low = 1`, `medium = 2`, `high = 3`.
* **Base Price:** The base price for calculations is hardcoded as `10`.
* **Coefficients:** The coefficients in the `demand` calculation (e.g., `0.5`, `0.3`, `-0.2`, `0.1`, `0.05`) are fixed for demonstration.
* **Simulated Stream:** `pw.debug.table_from_pandas` is used to simulate a real-time stream from a static DataFrame.

## Future Enhancements (Conceptual)

While not fully implemented in the provided notebook, future directions for this project could include:

* **Full Implementation of Other Models:** Integrate `Model 1 (Baseline Linear)` and `Model 3 (Competitive Pricing)` with Pathway's stateful and join capabilities, which would involve more complex Pathway operations for `previous_price` and spatial lookups.
* **Live Data Source Integration:** Connect Pathway to actual live data streams (e.g., Kafka, IoT platforms) for continuous, real-time operation.
* **Advanced Analytics:** Incorporate more sophisticated machine learning models for predictive analytics (e.g., predicting future occupancy) to inform pricing decisions.
* **Interactive Dashboard:** Develop a comprehensive, real-time interactive dashboard using Bokeh (beyond the basic `matplotlib` plot) or other visualization libraries, connected directly to the Pathway stream.
* **Optimization Algorithms:** Implement algorithms to optimize pricing for revenue maximization or demand management, possibly using Reinforcement Learning.
* **Deployment:** Set up the Pathway pipeline on a scalable cloud infrastructure for production use.
