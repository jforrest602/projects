*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge


This 30-day Security Operations Center (SOC) Analyst challenge aims to provide free, practical experience for aspiring SOC analysts who may lack hands-on experience. It is designed to bridge the gap between theoretical knowledge and practical application of skills for entry level applicants. 

The challenge consists of daily lessons released on YouTube and will cover topics  such as creating logical diagrams, the ELK stack, attacking machines, detecting and investigating those attacks. Practical skills include creating alerts and dashboards, as well as integrating a ticketing system to track work flow.

For more information about the project creator, visit https://www.mydfir.com/. 

To find out more about the challenge and try it for yourself, check out https://www.youtube.com/@MyDFIR, where you will find the entire video series for the challenge and many other helpful and informative videos for anyone looking to build their cybersecurity knowledge and skillset.

## Day One: Logical Diagram

![MYDFIR 30 Day Challenge Network Diagram drawio](https://github.com/user-attachments/assets/8571246f-1dd1-42db-b6da-3c5a82e15e4a)

The first task of the challenge was to create a logical diagram of the challenge infrastructure. The resource used to create the diagram was [draw.io](http://draw.io/), an intuitive platform that offers numerous tools for customizing diagrams. The process taught me valuable lessons in understanding network setups and visualizing complex infrastructures and I now have a logical representation of the challenge setup. 

From the diagram, we see that the challenge involves six servers, five of which reside on a virtual private cloud (VPC). A VPC is a powerful tool for managing virtual resources efficiently, as it enables you to place virtual servers on the same network. This not only optimizes network performance but also reduces the strain on your local machine’s resources. If these servers were running locally, they would consume significant processing power and memory, which is not ideal for complex challenges like this.

The VPC is secured and connected to the internet through an internet gateway, depicted by an orange cloud in the diagram. This gateway acts as the link between the VPC and the broader internet, much like an Internet Service Provider (ISP) would.

The diagram also includes two important machines:
- The SOC Analyst Machine (blue): This is where we, as defenders, would monitor and respond to any suspicious activity within the network.
- The Attacker’s Machine and Command and Control (C2) Server (red): This represents the tools and systems an attacker would use to infiltrate or compromise the network. In this challenge, the attacker’s machine runs Kali Linux, a widely used distribution for both ethical and unethical hacking due to its comprehensive tools for penetration testing.

This exercise provided me with a clearer understanding of how the network components interact. Using [Draw.io](http://draw.io/) was straightforward, making it a great platform to get comfortable with building network diagrams. It also emphasized the importance of having a clear, organized representation of network setups, especially in cybersecurity scenarios.

## Day Two: Introducing the ELK Stack

The ELK Stack is widely used in the cybersecurity industry and in SOCs specifically. Understanding what it is, its benefits, and how to get the most out of deploying it will help aspiring SOC analysts standout among the competition.

#### [ Elasticsearch](https://www.elastic.co/elasticsearch)
Elasticsearch is a database used for storing and searching log information. It allows the user to search across their data. Users can integrate various applications to interact with the database in a programable way to access information.

#### [Logstash](https://www.elastic.co/logstash)
Logstash is a data processing pipeline for ingesting and normalizing data from multiple sources. It collects telemetry via [Beats](https://www.elastic.co/beats) or [Elastic Agents](https://www.elastic.co/elastic-agent) (this project will utilize Elastic Agents). It outputs the collected data into Elasticsearch. Importantly, it allows users to create and apply custom filters to forward desired data only.

#### [Kibana](https://www.elastic.co/kibana)
Kibana allows users to access Elasticsearch data via a web console to search data, create visual dashboards, and create alerts or reports. 

#### Benefits of the ELK Stack
- Centralized logging, flexible customization, and scalability for large environments⁠
- Powerful visualization tools for creating dashboards and reports⁠
- Extensive ecosystem with numerous integrations and a large user community

The ELK Stack is comparable to using [Splunk](http://www.splunk.com/) in terms of indexing and forwarding data, as well as the ability to access the data via a graphical user interface (GUI).

## Day Three: Setup Elasticsearch
