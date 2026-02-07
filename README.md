# AirBnB CDC Data Ingestion & Automation Project

This project demonstrates an end-to-end data engineering pipeline that simulates AirBnB booking data, ingests it into Azure Synapse Analytics, and triggers AI-driven email workflows using n8n.

## 📂 Project Structure

```text
AirBnB_CDC_Data_Ingestion_Project/
├── MockData/
│   └── mock_data_in_cosmosdb.py    # Generates mock bookings and pushes to Cosmos DB
├── sql_queries/
│   ├── synapse_table_creation.sql  # DDL for Synapse tables (Facts, Dims, Aggregates)
│   └── adf_pipeline_auth.sql       # Setup script for Azure Data Factory authentication
└── n8n_workflow_assets/
    ├── sql_node_query.sql          # Query to identify high-value bookings (> $2500)
    └── chat_model_propmt.txt       # System prompt for LLM email generation

🚀 Key Features
1. Mock Data Generation (Source)

Script: MockData/mock_data_in_cosmosdb.py 

Functionality:

Simulates 500+ confirmed bookings for cities like New York, London, and Mumbai.

Implements seasonality logic (higher prices in June-Aug, Dec).


CDC Simulation: Includes a maintenance function (cancel_existing_bookings) that patches existing records to "Cancelled" to simulate state changes.

2. Data Warehousing (Azure Synapse)

Schema: Creates an airbnb schema with Star Schema architecture:


dim_customer: Customer details and account stats.


fact_booking: Transactional booking data.


BookingCustomerAggregation: Pre-calculated country-level stats (revenue, cancellation rates).


Stored Procedure: airbnb.BookingAggregation truncates and reloads the aggregate table.

3. Workflow Automation (n8n + LLM)

Trigger: Fetches bookings with a total_amount > 2500 and valid customer emails.


AI Action: Uses a Chat Model to generate personalized emails in "Indian English".


Confirmed: Congratulates the user on their trip to specific cities.


Cancelled: Writes an empathetic message offering rebooking assistance.

🛠️ Setup & Configuration
Prerequisites
Python 3.x

Azure Cosmos DB (SQL API)

Azure Synapse Analytics (Dedicated SQL Pool)

n8n (Self-hosted or Cloud)

Step 1: Python Data Generator
Navigate to the MockData folder.

Install dependencies:

Bash

pip install azure-cosmos faker

Configuration: Open mock_data_in_cosmosdb.py and update the following placeholders:

COSMOS_URL: Your Azure Cosmos DB URI.

COSMOS_KEY: Your Azure Cosmos DB Primary Key.


Note: Ensure CustomerData CSV files are present if you want to link specific customer IDs, otherwise the script defaults to IDs 1-101.

Run the script:

Bash

python mock_data_in_cosmosdb.py
Step 2: Database Setup (Synapse)
Authentication: Run sql_queries/adf_pipeline_auth.sql in your Synapse workspace.


Action Required: Replace <your_adf_pipeline_name> with your actual Data Factory name and set a strong password.


Schema Creation: Run sql_queries/synapse_table_creation.sql to create the necessary tables and stored procedures.

Step 3: n8n Workflow

Microsoft SQL Node: Copy the query from n8n_workflow_assets/sql_node_query.sql.

AI Chat Model Node: Use the prompt text from n8n_workflow_assets/chat_model_propmt.txt.

Ensure you map the JSON variables (e.g., {{ $json.customer_name }}) correctly in your n8n expression editor.
