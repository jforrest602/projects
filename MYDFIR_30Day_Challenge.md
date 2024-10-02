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
![elk stack](https://github.com/user-attachments/assets/df1977ed-f7b5-42c8-a6c4-26a611f3d350)

In today’s cybersecurity landscape, the ELK Stack has become an essential tool for Security Operations Centers (SOCs). Comprising Elasticsearch, Logstash, and Kibana, the ELK Stack offers powerful features that enable SOC analysts to gain critical insights from system logs and telemetry data. Understanding what the ELK Stack is, its benefits, and how to effectively deploy it can help aspiring SOC analysts stand out in a competitive field.

The ELK Stack is an open-source platform that enables organizations to search, analyze, and visualize log data from various sources in real-time. The three core components work together to provide a streamlined solution for log management and data visualization:

#### [ Elasticsearch](https://www.elastic.co/elasticsearch)
A distributed, scalable database designed for storing and searching log information. It allows users to perform quick, real-time searches across large datasets. Through various integrations, Elasticsearch enables programmable access to the database, making it easy to retrieve and manipulate data.

#### [Logstash](https://www.elastic.co/logstash)
A robust data processing pipeline that ingests, processes, and normalizes data from multiple sources. Logstash can gather telemetry via [Elastic Agents](https://www.elastic.co/elastic-agent) and output that data into Elasticsearch. Its customizable filters allow users to forward only the data that matters most..

#### [Kibana](https://www.elastic.co/kibana)
A powerful web interface that allows users to interact with Elasticsearch data. Kibana provides features for searching logs, creating visual dashboards, and generating real-time alerts or reports, making data analysis straightforward and effective.

#### Benefits of the ELK Stack for SOCs

The ELK Stack offers several advantages that make it an excellent choice for SOCs:
- Centralized Logging: By consolidating logs from various sources into a single platform, the ELK Stack provides a centralized hub for managing and analyzing telemetry data.
- Flexible Customization: Logstash's filtering and the broad range of Elasticsearch integrations make the ELK Stack highly adaptable to different environments and use cases.
- Scalability: As organizations grow, the ELK Stack can scale to handle increasing amounts of log data without compromising performance.
- Visualization & Reporting: Kibana's dashboard tools allow SOC analysts to easily visualize trends and anomalies within data, helping to uncover potential security threats. Analysts can create custom reports and set up alerts to stay on top of critical events in real time.
- Extensive Ecosystem: With a large user community and support for numerous integrations, the ELK Stack is continuously evolving. Whether you're integrating with security information and event management (SIEM) tools or using machine learning to enhance threat detection, the ecosystem supports a wide array of functionalities.

#### ELK Stack vs. Splunk

The ELK Stack is often compared to Splunk, another popular log management platform. Both systems offer similar capabilities in terms of indexing and forwarding data, as well as the ability to search through logs via a graphical user interface (GUI). However, the ELK Stack is open-source, which can lead to lower costs and greater customization for organizations.

#### Final Thoughts

For aspiring SOC analysts, understanding the ELK Stack can be a valuable asset. Its ability to centralize and visualize large amounts of log data, combined with the flexibility to customize data processing workflows, makes it an indispensable tool in the cybersecurity industry. Mastering the ELK Stack will not only boost your technical skills but also enhance your ability to respond to cyber threats effectively.

## Day Three: Setup Elasticsearch

![elastic1](https://github.com/user-attachments/assets/0ffc7f43-8e02-4c68-91e6-555bf4f009a6)


Today felt like the true beginning of this challenge, combining both hands-on work and technical tasks.

I successfully completed three primary tasks:
- Established a VPC network
- Deployed a server running Ubuntu
- Installed Elasticsearch on the server

#### The Network
Setting up the network was relatively easy. I opted to use Google VPC instead of Vultr, as Steven does in his tutorials. This meant I couldn’t simply follow the video step-by-step; I had to slow down and figure out some details on my own. The setup involved creating a network name and assigning an IP range. Other configuration options were available, but I left them as default for now.

##### What is a VPC network?

A Virtual Private Cloud (VPC) network is a logically isolated network environment within a cloud provider's infrastructure (such as AWS, Google Cloud, or Microsoft Azure). It lets you define and manage your own private network space in the cloud, giving you full control over IP address ranges, subnets, routing, and access controls.

Key features of a VPC include isolating your network from others, organizing resources through subnetting, and ensuring security by controlling traffic using security groups and access control lists (ACLs).

In short, a VPC gives you control over your cloud infrastructure, allowing you to design how your resources communicate securely and efficiently within the cloud provider’s global network.

#### The Server

Deploying the server was straightforward. However, I encountered an error when trying to establish an SSH connection via the GUI.

`Connection via Cloud Identity-Aware Proxy Failed`

`Code: 4003`

`Reason: failed to connect to backend`

It took some time, but I resolved the issue by creating a firewall rule to allow Cloud Identity-Aware Proxy (IAP) to connect to port 22 on the instance.

There is extensive troubleshooting documentation available, and once I found the relevant information, it guided me through a step-by-step solution. The built-in AI chatbot also proved to be helpful during the process.

#### The Install
Once the server was created, the next step was to update the repositories and install Elasticsearch, all via the command line.

<img width="659" alt="day3-1" src="https://github.com/user-attachments/assets/a4273dd4-407c-4250-9f37-73565396f2c5">

It’s important to note that you should save the `Security autoconfiguration information` provided after the initial installation, as it contains the password for the built-in Elasticsearch superuser. If you miss this information, there is a way to reset the password later.

<img width="582" alt="day3-2" src="https://github.com/user-attachments/assets/f67879f6-193f-4dc8-af81-803508a015c4">

Next, I needed to make edits to the `elasticsearch.yml` file. I changed the network host IP address to my public IP address to allow the SOC analyst laptop (which will be created) to access the Elasticsearch instance. I deleted the comment marker before the network host IP address and port to activate it, making it part of the YAML structure.

Finally, I started Elasticsearch and confirmed that it was running successfully.

<img width="957" alt="day3-3" src="https://github.com/user-attachments/assets/3a41c706-53d3-4ca9-a6da-21948b83d2b2">

#### Takeaways
- Today was more challenging than the first two days, but I enjoyed troubleshooting and figuring things out on my own. 
- I found Vultr to be much more user-friendly, with a simpler user interface that is easier to navigate than Google VPC.
- I’m excited to move forward and look forward to deploying more machines on the network to observe how they interact with each other.
