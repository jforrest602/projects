*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge (Part 2)

![part 2 header](https://github.com/user-attachments/assets/07c3cb89-0e88-49b9-b549-d18fdcf80148)

## Day 6: Elastic Agent & Fleet Server Intro
Elastic Agent provides a single, unifies method to add monitoring for logs, metrics, and other data. It simplifies the collection, monitoring, and protection of data across various environments while serving as a central point for data ingestion, allowing organizations to collect logs, metrics, and security events from their systems and applications. It is a versatile tool that helps organizations enhance their observability and security posture by providing a comprehensive solution for data collection and monitoring.

Elastic Agent operates by performing several key functions to gather and send data to a centralized system for analysis and visualization.
#### Installation and Deployment 
Agents are installed on systems (servers, endpoints, etc.) and can be deployed manually or via automation tools.

#### Data Collection 
Agents identify data sources (logs, metrics, etc.) and collect various types of data using methods like monitoring log files or polling APIs.

#### Data Processing 
Before transmission, agents may preprocess the data to filter unnecessary information, parse formats, and enrich it with context.

#### Data Transmission 
Agents send the collected and processed data to a central system (like Elasticsearch) securely, often using encryption.

#### Central Management
Agents are centrally managed via a console (e.g., Kibana), allowing for configuration, updates, and monitoring.

#### Integration with Analytics 
Data ingested into the central system can be indexed for retrieval and analyzed using visualization tools like Kibana.

#### Updating and Scaling
Agents can be updated for new features or patches and scaled as data volumes grow or new systems are added.

For the purpose of this project, it's important to know that agents work based off policies that can be updated and take on additional integrations and protections which can all be used to tell the endpoint what logs to forward to your Elasticsearch or Logstash instance.

This project will integrate the Elastic Agent as part of a fleet. A fleet server facilitates the management and coordination of Elastic Agents across an organization’s infrastructure. It acts as a central point for agent communication, providing functionalities for deployment, configuration, and monitoring of agents and allowing for the management of multiple endpoints simultaneously making it easy to update policies across the spectrum of an organization's managed devices.

## Day 7: Elastic Agent & Fleet Server Setup

Now, it is time to install the Elastic Agent on the Windows server and enroll the server into a fleet.

#### Fleet Server
First, it is necessary to deploy a new server to act as the fleet server. This server is:
- optimized for cloud compute
- has a dedicated CPU
- an Ubuntu 22.04 LTS x64 image
- 4GB of storage
- VPC 2.0 enabled
- part of my VPC 2.0 network

#### Connect the Fleet Server & Elastic Agent
After deploying the fleet server, I logged into the Elastic web GUI and navigated to `Fleet` under `Management` within the hamburger menu.

<img width="949" alt="day7-3" src="https://github.com/user-attachments/assets/f4746a5b-0e0f-4b98-94c2-326f9600f682">

Once there, I chose `Add Fleet Server` using the `Quick Start` option. It's important to note here that `port 8220` is used by default.

<img width="960" alt="day7-4" src="https://github.com/user-attachments/assets/52bb3def-c0e3-4ab1-be4b-f2a2aaf76ba6">

##### Fleet Server Policy

A fleet server policy is a component of the Elastic Stack that defines how Elastic Agents should behave and what data they should collect. 

<img width="960" alt="day7-5" src="https://github.com/user-attachments/assets/72edc815-1e43-4e8a-aa5d-8657344d73f9">

<img width="957" alt="day7-6" src="https://github.com/user-attachments/assets/7287eb64-a676-4efc-aebf-7fe2f5f0a063">

Once the fleet server policy was generated, I connected to the fleet server via SSH. It's best practice to update the repository by running `apt-get update && apt-get upgrade -y`. This will download and install any OS updates.

Then it was easy to copy and paste the provided policy commands into the SSH session.

<img width="586" alt="day7-6a" src="https://github.com/user-attachments/assets/6a05bc8f-7aa2-4278-9e1b-f582f8f31124">

<img width="947" alt="day7-10" src="https://github.com/user-attachments/assets/56183d93-e941-4789-9f59-e0cdd1998893">

*Here it was neccessary to create a new newtork firewall rule to allow TCP connections on the fleet servers public IP address and to allow port 9200 (the port for Elasticsearch) on the ELK server.*  

