*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge (Part 2)

![part 2 header](https://github.com/user-attachments/assets/07c3cb89-0e88-49b9-b549-d18fdcf80148)

## Day 6: Elastic Agent & Fleet Server Intro
Elastic Agent provides a single, unifies method to add monitoring for logs, metrics, and other data. It simplifies the collection, monitoring, and protection of data across various environments while serving as a central point for data ingestion, allowing organizations to collect logs, metrics, and security events from their systems and applications. It is a versatile tool that helps organizations enhance their observability and security posture by providing a comprehensive solution for data collection and monitoring.

Elastic Agent operates by performing several key functions to gather and send data to a centralized system for analysis and visualization.
#### -Installation and Deployment 
Agents are installed on systems (servers, endpoints, etc.) and can be deployed manually or via automation tools.

#### -Data Collection 
Agents identify data sources (logs, metrics, etc.) and collect various types of data using methods like monitoring log files or polling APIs.

#### -Data Processing 
Before transmission, agents may preprocess the data to filter unnecessary information, parse formats, and enrich it with context.

#### -Data Transmission 
Agents send the collected and processed data to a central system (like Elasticsearch) securely, often using encryption.

#### -Central Management
Agents are centrally managed via a console (e.g., Kibana), allowing for configuration, updates, and monitoring.

#### -Integration with Analytics 
Data ingested into the central system can be indexed for retrieval and analyzed using visualization tools like Kibana.

#### -Updating and Scaling
Agents can be updated for new features or patches and scaled as data volumes grow or new systems are added.
