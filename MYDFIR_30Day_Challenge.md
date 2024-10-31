*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge (Part 1)


This 30-day Security Operations Center (SOC) Analyst challenge aims to provide free, practical experience for aspiring SOC analysts who may lack hands-on experience. It is designed to bridge the gap between theoretical knowledge and practical application of skills for entry level applicants. 

The challenge consists of daily lessons released on YouTube and will cover topics  such as creating logical diagrams, the ELK stack, attacking machines, detecting and investigating those attacks. Practical skills include creating alerts and dashboards, as well as integrating a ticketing system to track work flow.

For more information about the project creator, visit https://www.mydfir.com/. 

To find out more about the challenge and try it for yourself, check out https://www.youtube.com/@MyDFIR, where you will find the entire video series for the challenge and many other helpful and informative videos for anyone looking to build their cybersecurity knowledge and skillset.

## Day 1: Logical Diagram

![MYDFIR 30 Day Challenge Network Diagram drawio](https://github.com/user-attachments/assets/8571246f-1dd1-42db-b6da-3c5a82e15e4a)

The first task of the challenge was to create a logical diagram of the challenge infrastructure. The resource used to create the diagram was [draw.io](http://draw.io/), an intuitive platform that offers numerous tools for customizing diagrams. The process taught me valuable lessons in understanding network setups and visualizing complex infrastructures and I now have a logical representation of the challenge setup. 

From the diagram, we see that the challenge involves six servers, five of which reside on a virtual private cloud (VPC). A VPC is a powerful tool for managing virtual resources efficiently, as it enables you to place virtual servers on the same network. This not only optimizes network performance but also reduces the strain on your local machine’s resources. If these servers were running locally, they would consume significant processing power and memory, which is not ideal for complex challenges like this.

The VPC is secured and connected to the internet through an internet gateway, depicted by an orange cloud in the diagram. This gateway acts as the link between the VPC and the broader internet, much like an Internet Service Provider (ISP) would.

The diagram also includes two important machines:
- The SOC Analyst Machine (blue): This is where we, as defenders, would monitor and respond to any suspicious activity within the network.
- The Attacker’s Machine and Command and Control (C2) Server (red): This represents the tools and systems an attacker would use to infiltrate or compromise the network. In this challenge, the attacker’s machine runs Kali Linux, a widely used distribution for both ethical and unethical hacking due to its comprehensive tools for penetration testing.

This exercise provided me with a clearer understanding of how the network components interact. Using [Draw.io](http://draw.io/) was straightforward, making it a great platform to get comfortable with building network diagrams. It also emphasized the importance of having a clear, organized representation of network setups, especially in cybersecurity scenarios.

## Day 2: Introducing the ELK Stack
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

#### Takeaways

For aspiring SOC analysts, understanding the ELK Stack can be a valuable asset. Its ability to centralize and visualize large amounts of log data, combined with the flexibility to customize data processing workflows, makes it an indispensable tool in the cybersecurity industry. Mastering the ELK Stack will not only boost your technical skills but also enhance your ability to respond to cyber threats effectively.

## Day 3: Setup Elasticsearch

![elastic1](https://github.com/user-attachments/assets/0ffc7f43-8e02-4c68-91e6-555bf4f009a6)


Today felt like the true beginning of this challenge, combining both hands-on work and technical tasks.

I successfully completed three primary tasks:
- Established a VPC network
- Deployed a server running Ubuntu
- Installed Elasticsearch on the server

#### The Network
Setting up the network was straightforward. The Vultr GUI is well organized and user friendly. Still, it is important to slow down and make sure you get it right or you may find yourself troubleshooting later. The setup involved creating a network name and assigning an IP range. Other configuration options were available, but they were left as default for now.

##### What is a VPC network?

A Virtual Private Cloud (VPC) network is a logically isolated network environment within a cloud provider's infrastructure (such as AWS, Google Cloud, or Microsoft Azure). It lets you define and manage your own private network space in the cloud, giving you full control over IP address ranges, subnets, routing, and access controls.

Key features of a VPC include isolating your network from others, organizing resources through subnetting, and ensuring security by controlling traffic using security groups and access control lists (ACLs).

In short, a VPC gives you control over your cloud infrastructure, allowing you to design how your resources communicate securely and efficiently within the cloud provider’s global network.

#### The Server

Deploying the server was straightforward. I made sure to select the same location as my VPC network, chose an Ubuntu image, and a plan with 80GB and 4 vCPU cores. Next, was to ensure that the newly created VPC network was selected so that this server will be a part of that network. Finally, I gave the host a name and deployed.

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

## Day Four: Kibana Setup
If you recall from day two, Kibana is a web interfce that allows users to interact with Elasticsearch data providing features for searching logs, creating dashboards, and generating alerts and reports helping the data analysis process.

The first step of this process is to download Kibana [here](https://www.elastic.co/downloads/kibana). and install. Be sure to to download to right file for your system and right click the blue download button to copy the download link.

<img width="535" alt="day4-1" src="https://github.com/user-attachments/assets/fa438b13-52ec-4b4c-bff7-6b2dc091aa7a">

Next, in your server's Command Prompt type `wget` and paste the download link and hit enter.

<img width="641" alt="day4-2" src="https://github.com/user-attachments/assets/d9af363b-25f2-4511-a597-3ec8dc219f69">

You may want to confirm that the file downloaded successfully. You can do this by running `ls` to list the contents of the current directory (make sure that you are in the right directory). You will see a file named `kibana-8.15.0-amd.64-deb`. Note that depending on when you're reading this and what OS you're using the version of Kibana may be different. 

Next, two changes to the configuration file were necessary. To do so, open the configuration file by running `nano /etc/kibana/kibana.yml`. 

<img width="575" alt="day4-4" src="https://github.com/user-attachments/assets/4d7f149a-b6c2-493d-b50d-2ad89a0c7f46">

- One: activate the server port 5601 by deleting the hash sign at the beginning of the line.
- Two: change the server host from 'localhost' to the public IP address of the virtual machine.

Once those changes are made, run the following commands to enable and start the Kibana service:
`systemctl enable kibana.service`
`systemctl start kibana.service`
`systemctl status kibana.service`

If everyhting went as planned, you should see the service listed as `active (running)`.

<img width="958" alt="day4-5" src="https://github.com/user-attachments/assets/764ac787-f340-4c9f-bc6c-5aae8610b6a7">

Now that the Kibana service is downlaoded and running, it is time to set it up. The first step in this process is to generate an Elasticsearch enrollment token. Navigate to `/usr/share/elasticsearch/bin` where you will find `elasticsearch-create-enrollment-token`. To generate a token, run `./elasticsearch-create-enrollment-token --scope kibana`.

<img width="962" alt="day4a-1" src="https://github.com/user-attachments/assets/2f252361-7ff2-481c-9e44-88848c122d47">

Once the enrollemnt token is generated, save it someplace safe and in a web browser navigate to your server's public IP address on port 5601, e.g. `http://104.198.122.33:5601/`.

*If you experience connection issues here, you may need to update the server's firewall rules to allow inbound TCP traffic from your IP. Furthermore, configure the firewall on the ELK server to allow incoming connections on port 5601 by running `ufw allow 5601`.*

When your web browser is able to establish a connection, it's needs that enrollment token that was just generated. Paste it into the box and click `Configure Elastic`.

<img width="526" alt="day4b-1" src="https://github.com/user-attachments/assets/bdfff740-b2b4-4b14-a4ba-05de2b0369da">

Next up, a verification code is required. This is done by navigating to `/usr/share/kibana/bin` and executing `./kibana-verification-code`.

<img width="486" alt="day4b-2" src="https://github.com/user-attachments/assets/6a33114d-7410-47ce-ae06-31111a57cdbc">

<img width="478" alt="day4b-3" src="https://github.com/user-attachments/assets/fadc703a-1204-409a-92de-904364cd2aa5">

After entering the verification code, the login information can be retrieved from the *Security autoconfiguration information* that was saved when installing Elastic.

<img width="597" alt="day4b-4" src="https://github.com/user-attachments/assets/8b6d9781-29f6-4200-b6de-d6a7da2bb0d1">

<img width="960" alt="day4b-5" src="https://github.com/user-attachments/assets/9a042879-d0e7-465c-85df-6fdbed12f863">

<img width="960" alt="day4b-6" src="https://github.com/user-attachments/assets/860a1e9d-b4c7-46bd-bbdd-7c3001e01078">

Finally, it is necessary to add keystores. A keystore is essentially a place to keep security certificates and private keys used in cruptographic functions. By clicking on the hamburger menu in the top left corner, scrolling down and selecting `Alerts` under `Security` you should come to the following page where *API integration key required*. 

<img width="960" alt="day4b-7" src="https://github.com/user-attachments/assets/74c41b02-8dee-4c35-9e15-82999fce6b26">

This is accomplished by navigating to the `/usr/share/kibana/bin` directory and executing `./kibana-encryption-keys generate` which will provide three different keys.

<img width="495" alt="day4b-8" src="https://github.com/user-attachments/assets/92a963de-9c5b-48ff-8546-e0d0122c553a">

Continuing work within the `/usr/share/kibana/bin` directory, there is a `kibana-keystore` binary. To add the the keys, execute `./kibana-keystore add xpack.encryptedSavedObjects.encryptionKey`. You'll then be prompted to provide the key value which you can copy and paste. 

<img width="482" alt="day4b-9" src="https://github.com/user-attachments/assets/ae0c984d-91ab-45e9-80bd-08ea001bf9cd">

Repeat for the remaining two keys.

<img width="468" alt="day4b-10" src="https://github.com/user-attachments/assets/254fd727-944b-49db-9015-8bcfa7c7d0a0">

Finally, restart the Kibana service by running `systemctl restart kibana.service`. To confirm success, refresh the web browser and log into Elastic. The *API integration key required* message will no longer appear.

#### Takeaways
I was able to successfully install and configure Kibana, a critical component of the ELK stack that provides interface to explore, analyze, and visualize data stored in Elasticsearch, a commonly used tool in SOCs enabling security analysts to gain insights into security logs, track potential security incidents, and respond to them quickly.

I was also introduced to `.yml` files, or configuration files written in YAML (Yet Another Markup Language). It’s similar to JSON and XML but is more concise and easier to read. In the context of the ELK Stack, .yml files (like elasticsearch.yml or kibana.yml) are used to configure each component, specifying settings such as network ports, authentication, and paths.

Finally, I learned about keystores. A keystore is a secure storage solution for sensitive information, such as encryption keys, passwords, certificates, or tokens, often used in applications and systems where data security is critical. There are several types of keystores. In this case, it was an application-specific keystore. The Elasticsearch keystore stores sensitive settings like passwords and tokens in a secure way, preventing them from being exposed in plain text within configuration files which can be managed through a command line utility to add, update, or remove items securely.

## Day 5: Windows Server Setup

Today's goal is to setup a Windows server with Remote Desktop Protocol (RDP) exposed to the internet. This machine will serve as the target machine for this project.

If you're using Vultr, navigate to your account and click `Deploy +` in the top right corner and choose `Deploy New Server`.

<img width="960" alt="day5-1" src="https://github.com/user-attachments/assets/e823abdf-e058-4dd7-b965-40beee14246f">

This machine only requires Cloud Compute with shared CPU and a Windows Standard image with 1 vCPU and 2GB of memory. This server will not be part of the VPC network that we have created. Additionally, this server will not be added to any firewall group.

#### Updated Network Diagram

![MYDFIR 30 Day Challenge Network Diagram_Update Day 5 drawio](https://github.com/user-attachments/assets/6b4ce55e-fb0d-4624-87df-37bd6e8e0b8a)
This updated diagram reflects the decision to leave this windows server outside of the VPC network. Segmenting it this way means that if this Windows server is compromised, the rest of the network will not neccessarily also be compromised.

Once the server is ready to go, it is possible to log into this Windows server.

#### Confirm RDP is Exposed
To confirm that RDP is exposed to the internet, open the Remote Desktop app and enter the server's IP address and click connect. If it allows a connection, it is exposed to the internet.

<img width="725" alt="day5-6" src="https://github.com/user-attachments/assets/db31586a-c6da-44ba-bee3-2ab3fca8713c">

<img width="810" alt="day5-7" src="https://github.com/user-attachments/assets/4a746d6d-dcf7-4d91-aa34-6e65e9aaf7be">

Now that I have this Windows server machine in the cloud with RDP exposed to the internet, I am expecting unsuccessful login attempts from the internet to begin. This traffic should generate plenty of logs to dig into in the coming days.

*In order to prevent this post from becoming too long, I will continue this project in a separate post labled Part 2* 
