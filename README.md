# Striim Recipe for real-time data analytics and multi-cloud Migration

## Overview

Transportation services like Uber and Lyft require streaming data with real-time processing for actionable insights on a minute-by-minute basis. While batch data can provide powerful insight on medium or long-term trends, in this age live data analytics is an essential component of enterprise decision making. It is said data loses its importance within 30 minutes of generation. To facilitate better performance for companies that hugely depend on live data, Striim offers continuous data ingestion from databases and other sources in real-time. With Striim's powerful log-based  Change Data Capture platform, database transactions can be captured and processed in real-time along with data migration to multiple clouds. This technology can be used by all e-commerce, food-delivery platforms, transportation services, and many others that harnesses real-time analytics to generate value. In this blog, I have shown how real-time cab booking data can be streamed to Striim's platform and processed in-flight for real-time visualization through Striim's dashboard as well as migration to BigQuery at the same time

## Tools Required

### Striim
Striim’s unified data integration and streaming platform connects clouds, data, and applications. Please follow these instructions to see how to get started with Striim 
### BigQuery
BigQuery is a serverless, highly scalable multi-cloud data warehouse

## Using Striim for CDC
Change Data Capture has gained popularity in the last decade as companies have realized the power of real-time data analysis from OLTP databases. In this example, data is acquired from a CSV file and streamed with a window of 30 minutes. Due to memory limitations in the CSV file, the window was kept for 30 minutes for better visualization. Ideally, Striim can handle a window of even 1 second when the amount of data is huge. The data was also processed inside Striim and the change was captured and migrated to BigQuery data warehouse. 
The dataset used in this example contains 4.5 million uber booking data that contains 5 features, DateTime, latitude, longitude, and  TLC base company at NYC. The goal is to stream the data through Striim's CDC platform and detect the areas that have more bookings (hotspots) every 30 minutes. The latitude and longitude value was  converted using the following query to cluster them into certain areas.

## Step 1: Reading Data from CSV and Streaming into Continuous Query through stream
In this step, the data is read from the data source using a delim-separated parser (cabCsvSource) with FileReader adapter used for CSV source.  The data is then streamed into cabCSvSourceStream which reaches cabCQ for continuous query. The SQL query at CabCQ2 converts the incoming data into required format

## Step 2: Sending the data for processing in Striim as well as into BigQuery
The data returned from the continuous query is then sent for processing through a 30-minute window and also migrated to BigQuery for storage. This is a unique feature of the Striim platform that allows data migration and processing at the same time. The data transferred to Bigquery multi-cloud can be used by various teams for analytics while Striim's processing gives valuable insights through its dashboard.
The connection between Striim and BigQuery is set up through a service account that allows Striim application to access BigQuery. A table structure is created within BigQuery instance that replicates the incoming stream of data.

## Step 3: Aggregating Data using Continuous Query
After the data is captured, a query is applied on each window that clusters the latitudes and longitudes of pickup locations into discrete areas and returns the aggregate demand of each area in those 30 minutes. There is a slight difference between the clustering query that goes into BigQuery(AggregateCQ) and the one that goes into StriimWaction(LatLonCQ). The data in StriimWaction is used for dashboard tracking hotspots, so the first latitude and longitude value is taken as the estimate of area. The data that goes into BigQuery returns all latitude longitude values and could be used for further analytics

## Dashboard For Real-Time Analysis
The dashboard ingested data from two different streams. The window30CabData provided data to the bar chart that tracked the number of vehicles from each company every 30 mins and the map fetched data from LatLon2dashboardWaction that had the aggregated count of bookings for every area in a 30-minute window. As seen from the dashboard snippet below, the dashboard was configured to return red for high demand (>30) , yellow for medium demand (between 15-30), and green for low demand(<15). This can be very useful to companies for real-time tracking and data migration. The dashboard has two configuration, a query where data is fetched from app and processed as required and configure, where specifications on visualization are entered. The SQL queries below shows the query for my map and bar chart. The two snippets from dashboard shows an instant of booking at different locations and number of vehicles from each TCL company.

## Migrating Data to BigQuery
Finally, the aggregated data along with the source data was also migrated to BigQuery. Migration to BigQuery followed the same process of creating an empty table with same schema that ingests data from Striim
