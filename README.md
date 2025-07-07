# SA-CAPSTONE-PROJECT
# Smart Parking Dynamic Pricing Model

## Project Overview

This project aims to develop a dynamic pricing model for parking spaces that updates in real-time. The goal is to optimize parking prices based on various influencing factors to ensure efficient space utilization, manage demand, and maximize revenue, while providing a smooth and explainable pricing variation.

The system simulates real-time data ingestion and processing using Pathway, allowing for continuous price predictions and real-time visualization.

## Core Features

* **Real-time Price Updates:** Prices are dynamically adjusted based on live data streams.
* **Multi-factor Pricing Logic:**
    * Historical occupancy patterns
    * Queue length
    * Nearby traffic conditions
    * Special events/days
    * Vehicle type
    * Competitor parking prices (optional, advanced model)
* **Base Price Integration:** All pricing starts from a base price of $10.
* **Smooth & Explainable Price Variation:** Designed to avoid erratic price changes, ensuring transparency.
* **Optional Rerouting Suggestions:** The system can suggest alternative nearby lots if the current lot is overburdened (part of Model 3).
* **Real-time Data Simulation:** Utilizes Pathway for ingesting and processing data streams.
* **Real-time Visualization:** Employs Bokeh for interactive, live dashboards.

## Pricing Models Implemented

The project implements three distinct pricing models, increasing in complexity:

### Model 1: Baseline Linear Model

A foundational model where the price for the next time step is a linear function of the previous price and the current occupancy rate.
* **Formula Example:** `Price(t+1) = Price(t) + α * (Occupancy / Capacity)`
* **Purpose:** Serves as a simple reference point to observe basic price elasticity.

### Model 2: Demand-Based Price Function

A more sophisticated model that constructs a mathematical demand function incorporating multiple key features. The calculated demand then directly influences the price adjustment from the base price.
* **Demand Factors:** Occupancy rate, Queue length, Traffic level, Special day, Vehicle type.
* **Demand Function Example:** `Demand = α·(Occupancy/Capacity) + β·QueueLength − γ·Traffic + δ·IsSpecialDay + ε·VehicleTypeWeight`
* **Price Adjustment Example:** `Price(t) = BasePrice * (1 + λ * NormalizedDemand)`
* **Constraints:** Ensures demand is normalized and price variations are smooth and bounded (e.g., between 0.5x and 2x the base price).

### Model 3 (Optional): Competitive Pricing Model

This advanced model introduces location intelligence and simulates real-world competition by factoring in prices of nearby parking spaces.
* **Logic:**
    * Calculates geographic proximity of nearby parking spaces using Haversine distance.
    * Determines competitor prices (using Model 2 logic for competitors).
    * Adjusts own lot's price and potentially suggests rerouting based on lot fullness and competitor pricing strategies (e.g., if full and competitors are cheaper, suggests rerouting or slightly lowers price; if competitors are expensive, own price can increase).

## Technology Stack

* **Python:** Core programming language.
* **Pandas & NumPy:** For data manipulation and numerical operations.
* **Pathway:** For real-time data ingestion, stream processing, and continuous prediction emission.
* **Bokeh:** For interactive, real-time data visualization.

## Getting Started

To run this project, you will need a Google Colab environment and the `dataset.csv` file.

### Setup (Google Colab)

1.  **Upload `dataset.csv`:** Upload your `dataset.csv` file directly to your Google Colab environment (e.g., by dragging it to the files sidebar).
2.  **Open a New Notebook:** Create a brand new Google Colab notebook.
3.  **Install Dependencies:** Run the following commands in the first code cell of your Colab notebook to install all necessary libraries:
    ```bash
    !pip install pandas numpy scipy pathway bokeh
    ```
    * **Important Note on Pathway:** If you encounter `AttributeError` issues with Pathway, please try running `!pip install pathway` in a **fresh Colab notebook** as the very first step. Environmental conflicts can occur.

4.  **Copy Code:** Copy the code provided cell-by-cell (from Phase 1 to Phase 5) into corresponding cells in your Colab notebook.

### Running the Project

Follow the phase-by-phase structure provided:

1.  **Phase 1: Data Preprocessing and Exploration:** Run these cells first to prepare your data.
    * (Optional) Uncomment the `matplotlib` and `seaborn` plotting code in Phase 1, Cell 5 to visualize initial data insights.
2.  **Phase 2: Pricing Model Development:** Run these cells to define the pricing functions. No output is expected, as these are function definitions.
3.  **Phase 3: Real-Time Simulation with Pathway:**
    * After running previous cells, go to **Phase 3, Cell 6**.
    * **Uncomment the line `pw.run()`** to start the Pathway streaming pipeline.
    * Observe the `final_output_stream.print()` output in real-time as data is processed.
    * **Note:** The stateful implementation for Model 1 and the competitive logic for Model 3 within Pathway are conceptual demonstrations of how such operations would be structured. Full, production-ready implementation of complex state and spatial joins in Pathway would require more advanced patterns.
4.  **Phase 4: Real-Time Visualization with Bokeh (Conceptual):**
    * The Bokeh integration is **conceptual** and demonstrates how you *would* set up real-time dashboards.
    * Running a true, live Bokeh server within Colab that dynamically updates from a Pathway stream is complex and typically requires port forwarding or dedicated server setup beyond simple script execution. The provided code gives you the structure for it.
5.  **Phase 5: Reporting and Future Enhancements:** This section provides an outline for conceptual reporting and discusses potential future improvements to the system.

## Project Structure (by Provided Cells)

The project code is structured into the following conceptual phases:

* **Phase 1: Data Preprocessing and Exploration:** Loading, cleaning, feature engineering (`Timestamp`, `IsSpecialDay`, `VehicleType`), and initial EDA.
* **Phase 2: Pricing Model Development:** Python functions for `Model 1 (Baseline Linear)`, `Model 2 (Demand-Based)`, and `Model 3 (Competitive)`. Includes necessary constants and mappings.
* **Phase 3: Real-Time Simulation with Pathway:** Ingesting data into Pathway, applying UDFs for Model 1 (conceptual state), Model 2, and Model 3 (conceptual complex joins), and demonstrating Pathway's `print` sink.
* **Phase 4: Real-Time Visualization with Bokeh:** Conceptual framework for setting up Bokeh plots and updating them in a real-time server environment.
* **Phase 5: Reporting and Future Enhancements:** Discussion points for generating reports (performance, anomalies, business insights) and outlining potential next steps for the project.

## Assumptions & Limitations

* **Dataset:** Assumes the availability and structure of `dataset.csv` as provided.
* **Pathway Environment:** Requires a stable Pathway installation. Environmental issues are outside the scope of direct AI troubleshooting.
* **Conceptual Complexity:** Some advanced Pathway operations (e.g., complex state management for Model 1's `previous_price`, true spatial joins for Model 3 across a global state) and Bokeh real-time server setup are described conceptually due to their complexity in a simple Colab environment.
* **Model Parameters:** Coefficients (alpha, beta, gamma, etc.) for the pricing models are set as constants for demonstration. In a real-world scenario, these would be tuned using historical data, optimization algorithms, or A/B testing.
* **`IsSpecialDay`:** Currently a dummy feature; in production, this would be derived from a comprehensive holiday calendar or event API.

## Future Enhancements

* **Data Source Integration:** Connect Pathway to live data sources (e.g., Kafka, MQTT, cloud storage events) for true real-time ingestion.
* **Machine Learning Models:** Implement more advanced ML models (e.g., ARIMA for demand forecasting, Reinforcement Learning for dynamic pricing optimization).
* **Optimization:** Use linear programming or other optimization techniques to determine optimal pricing strategies given constraints.
* **User Interface:** Develop a full-stack web application for end-users and administrators to interact with the system.
* **Deployment:** Deploy the Pathway pipeline on robust cloud infrastructure (AWS, GCP, Azure) for scalability and fault tolerance.
* **Comprehensive Monitoring:** Implement detailed logging, metrics, and alerting for the entire system.
* **A/B Testing Framework:** Systematically test different pricing strategies against each other.

## License

This project is open-source and available under the [MIT License](LICENSE.md) (or specify your preferred license).
