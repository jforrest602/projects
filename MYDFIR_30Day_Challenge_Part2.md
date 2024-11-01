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

##### Install Elastic Agent
Once the fleet server policy was generated, I connected to the fleet server via SSH. It's best practice to update the repository by running `apt-get update && apt-get upgrade -y`. This will download and install any OS updates.

Then it was easy to copy and paste the provided policy commands into the SSH session.

<img width="586" alt="day7-6a" src="https://github.com/user-attachments/assets/6a05bc8f-7aa2-4278-9e1b-f582f8f31124">

<img width="947" alt="day7-10" src="https://github.com/user-attachments/assets/c061f361-4e41-4efc-aae6-3879fe36430c">

There was an error. To correct it, it was neccessary to:
- create a new newtork firewall rule to allow TCP connections on the fleet servers public IP address
- to allow port 9200 (the port for Elasticsearch) on the ELK server essentially allowing the fleet server and ELK server to communicate

After running the policy command again, the agent is successfully installed.

<img width="949" alt="day7-12" src="https://github.com/user-attachments/assets/8672fa1d-b654-42a3-904c-74c80dee2ab2">

It is confirmed within the Elastic web GUI.

<img width="1056" alt="day7-13" src="https://github.com/user-attachments/assets/ea8511b2-7b56-4f41-854b-4266fa0ec06a">

#### Adding an Agent on the Fleet Server

<img width="1056" alt="day7-13" src="https://github.com/user-attachments/assets/819e8028-52dd-4f95-96b4-0f0393684d0c">

<img width="965" alt="day7-14" src="https://github.com/user-attachments/assets/89e7457f-68db-4f9d-9ac8-4e56e077b957">

<img width="958" alt="day7-15" src="https://github.com/user-attachments/assets/3067e1f1-01e7-4e9e-aca5-b242ac3e4064">

Once the policy was generated, it is easy again to copy and paste the commands, this time into Powershell in the Windows server.

<img width="944" alt="day7-16" src="https://github.com/user-attachments/assets/96d92577-6cff-45a3-9475-3c2d10ff1b3a">

An error occurred.

<img width="1245" alt="day7-17" src="https://github.com/user-attachments/assets/1f673174-be26-4fd6-88fe-71be1ac06a03">

I was able to successfully ping the fleet server's IP address confirming there is a connection. 

<img width="1149" alt="day7-18" src="https://github.com/user-attachments/assets/b59f2574-21dc-4567-a13f-732f2f041080">

I modified the fleet server's internal firewall to allow port 8220.

<img width="1054" alt="day7-19" src="https://github.com/user-attachments/assets/af1b7add-f794-4379-aa3c-69806b476c0d">

Finally, in the Elastic web GUI, I navigated to `Fleet` under `Management` with the hamburger menu to get to the fleet `Settings`. The `Host URL` is using port 443. This needed to be changed to port 8220.

<img width="1133" alt="day7-20" src="https://github.com/user-attachments/assets/ff8c2960-5e0f-4a4c-8ede-ecbf23f29b69">

<img width="1188" alt="day7-21" src="https://github.com/user-attachments/assets/dc6d895b-33a8-4cf9-834c-600b16d92bcb">

Running a slightly modified command with the updated port of 8220 rather than 443 on the Windows server ended with the Elastic Agent installed.

<img width="1154" alt="day7-23" src="https://github.com/user-attachments/assets/459ecd50-85d3-48b3-b128-ab0a6f44d75d">

<img width="1146" alt="day7-24" src="https://github.com/user-attachments/assets/d6aa0392-fa7c-48f9-8b74-c10d27d61ea6">

<img width="967" alt="day7-25" src="https://github.com/user-attachments/assets/1f8463bc-f523-4c69-b0b9-97e5346c9195">

However, when I navigated to the `Discover` page to look at the logs, I didn't see any. Restarting the Elastic Agent on the Windows server was the solution. 

With the fleet server setup, Elastic Agent running, and communication taking place between the machines, logs will now start piling up.

#### Takeaways
- troubleshooting
- importance of getting the networking right with proper firewall rules
- the significance of using a fleet to manage multilpe endpoints

