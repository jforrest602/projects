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

## Day 8: Sysmon

Logging is enabled by default on Windows endpoints. However, that default logging is not enough. The default logging does not monitor process creation, for example. Monitor for process creation is crucial because it can help identify malware activity, spot privilege escalation, or help SOC teams establish a baseline of normal activities enabling proactive detection of potential threats. 

Sysmon (System Monitor) is a free tool from Microsoft's Sysinternals suite that provides detailed and continuous monitoring of various system activities on Windows systems. It enhances the visibility of system activity, such as process creation, network connections, and changes to file creation timestamps, which are often crucial for detecting cyber threats and suspicious activity. 

Key logging features of Sysmon include process monitoring, network connections, file creation, and registry changes. In cybersecurity, Sysmon is used in threat hunting, incident response, anomoly detection, and even compliance.  

Sysmon is highly customizable through its configuration file, allowing security teams to specify exactly what types of events to log and filter out noise that might otherwise overwhelm their logging infrastructure. This configuration flexibility is a core feature that makes Sysmon adaptable to different environments and security requirements.

#### Useful Sysmon Event IDs
| Sysmon Event ID	| Event Name | Significance |
| --------------- | ---------- | ------------ |
| 1 |	Process Creation | Detects suspicious or malicious processes, often the first step in malware execution. |
| 3	| Network Connections |	Monitors unauthorized network connections, which may indicate data exfiltration or C2 activity.|
| 7	| Image Load | Identifies malicious DLLs or driver injections into legitimate processes.|
| 11 | File Creation | Detects dropped or created files, helping identify malware payloads.|
| 10 | Process Access |	Tracks inter-process access, helping detect credential dumping and privilege escalation techniques.|

## Day 9: Sysmon Setup
Today's objective is to install Sysmon onto a Windows server and confirm that it generates telemetry. 

Often used in situations where it's impractical or impossible to have human presence at the data collection site, telemetry is the process of remotely collecting and transmitting data from one location to another for monitoring, analysis, or control. It enables real-time, remote monitoring, making it a valuable tool in many industries and applications.

I started by establishing a remote desktop connection to my Windows server using the server's public IP address and user credentials.

<img width="680" alt="day9-1" src="https://github.com/user-attachments/assets/232f9210-2341-4143-b80a-1a834d49fe56">

<img width="412" alt="day9-2" src="https://github.com/user-attachments/assets/7e14624b-9524-47bc-9726-dca59cfe6605">

Once the session was established, I opened up the web browser and Googled Sysmon. 

<img width="942" alt="Day9-2a" src="https://github.com/user-attachments/assets/6d7e453e-cec7-4f6b-bf48-bb88ab9dc052">

The [first result](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) will take you to the download page. Note that this page provides the download link and, if you scroll down, more information about how to use Sysmon, examples, and important Sysmon Event IDs.

<img width="960" alt="day9-3" src="https://github.com/user-attachments/assets/efea9937-ca66-4c53-a42a-ecbec998e032">

After downloading Sysmon, I extracted the compressed files. 

<img width="597" alt="day9-4" src="https://github.com/user-attachments/assets/1174aa40-7793-4714-9429-5986d5fe327c">

Next up, I needed to download the Sysmon configuration file. A popular configuration is Olaf's Sysmon configuration. This can be found quickly by Googling "sysmon olaf config".

<img width="962" alt="day9-5" src="https://github.com/user-attachments/assets/a1d537f8-8333-46d0-bf43-1777b5820491">

Scrolling down on the Github page, you will find the sysmonconfig.xml file. 

<img width="882" alt="day9-6" src="https://github.com/user-attachments/assets/3368a9e0-b0b7-4653-844e-3e70ccb19bf0">

Once inside there, select raw.

<img width="954" alt="day9-7" src="https://github.com/user-attachments/assets/15cf1fa8-017e-455d-a250-3113f705e88d">

Now, right click on the page and select "Save As". 

<img width="958" alt="day9-8" src="https://github.com/user-attachments/assets/fa7be764-46b3-4343-b66d-9a57b3f0460a">

Note that this file should be saved inside the Sysmon directory (folder).

<img width="960" alt="day9-9" src="https://github.com/user-attachments/assets/3ed1798d-ce56-4958-921c-f314c0f47593">

Next, I opened up a PowerShell window as Administrator. To navigate to the Sysmon directory I ran `cd C:\Users|Administrator\Downloads\Sysmon`. To confirm that I was in the right place, I ran `dir`.  I ran `.\Sysmon64.exe` and hit enter. This provides the flags that you can use.

<img width="481" alt="day9-10" src="https://github.com/user-attachments/assets/ee259574-d98b-4f72-8aea-c769df083b3c">

<img width="466" alt="day9-11" src="https://github.com/user-attachments/assets/e22dc0e2-a4de-402b-8704-5802e1fa85b5">

To install Sysmon, I ran `.\Sysmon.64.exe -i sysmonconfig.xml`.

<img width="723" alt="day9-12" src="https://github.com/user-attachments/assets/70971a4d-6942-4e0b-864a-710cba108df2">

It only takes a few seconds for it to finish installing. Once it was finished I opened up Services, part of the Microsfot Management Console which can be found by typing in "services" in your computer's search bar. Scrolling down, I did see that Sysmon was there and running.

<img width="959" alt="day9-13" src="https://github.com/user-attachments/assets/59c987af-b817-4980-9655-b750518f8791">

Furthermore, I opened my Windows Event Viewer, which can also be located by searching your computer for "event viewer". Once opened, using the dropdown menus on the left side, I navigated to `Applications and Service Logs > Microsoft > Windos > Sysmon > Operational`. This brought up a list of events, proving that Sysmon telemetry is being generated.

<img width="964" alt="day9-14" src="https://github.com/user-attachments/assets/2fd95478-1e3a-428d-8d01-0f3bc4b86611">

To recap, Sysmon provides detailed, real-time monitoring of various system activities, focusing on capturing low-level events that are often useful for detecting suspicious or malicious activity on a system. Sysmon and its logs are incredibly valuable for:

**Incident detection and response:** Offering visibility into processes, network activity, and file operations that might otherwise go unnoticed.

**Proactive defense and threat hunting:** Allowing analysts to search for indicators of compromise or early signs of attack.

**Forensics and root cause analysis:** Assisting investigators in reconstructing what happened during an attack or breach.

Sysmon is an essential tool for security professionals looking to maintain situational awareness, detect advanced threats, and respond quickly to incidents on Windows systems.

## Day 10: Ingest Data with Elastic Search
The goal for today is to learn how to ingest Sysmon and Windows Defender logs into Elasticsearch. After logging into my Elasticsearch instance, I selected "Add Integrations" on the homepage.

<img width="946" alt="day10-1" src="https://github.com/user-attachments/assets/eae44b1a-f769-4175-8334-cfbdad04559b">

Searching for "Windows events", there is a "Custom Windows Event Logs" option that collects and parses logs from any Windows event log channel with Elastic Agent. 

 <img width="947" alt="day10-2" src="https://github.com/user-attachments/assets/ab316b7f-9e15-4839-a75b-0661259c2760">

I selected that and then chose "Add Custom Windows Events Logs".

<img width="952" alt="day10-3" src="https://github.com/user-attachments/assets/a0f367e6-0889-4860-916d-651387a1a18c">

Following the prompts, I named and described the integration. 

<img width="941" alt="day10-6" src="https://github.com/user-attachments/assets/4669a871-be94-4ec4-9f64-7eb68bcdeb38">

For the Channel Name, I opened up Event Viewer on my Windows server and navigated to `Applications and Services Logs > Microsoft > Windows > Sysmon`, right clicked on `Operational` and selected `Properties`.

<img width="780" alt="day10-4" src="https://github.com/user-attachments/assets/5dae5348-b1de-46ab-8dbc-53fb399a490e">

The field `Full Name` field provides the information to use for the `Channel Name`. 

<img width="913" alt="day10-5" src="https://github.com/user-attachments/assets/dece34ef-62b7-48a4-9131-5f856816c25a">

<img width="750" alt="day10-7" src="https://github.com/user-attachments/assets/426ae58e-a5b8-4886-b8c3-358865daa788">

I left all other configuration options as default.

Finally, I added the integration to an existing host, selecting the proper agent policy, saved and deployed.

<img width="947" alt="day10-8" src="https://github.com/user-attachments/assets/2e6af218-7ab0-4e6d-851d-9f64b3815264">

<img width="1032" alt="day10-9" src="https://github.com/user-attachments/assets/06f0a8c2-636b-4727-b491-c741bc962dee">

TO ingest logs from Windows Defender, I followed the same procedure. To located the `Channel Name` information for this integration, I navigated to `Applications and Services Logs > Microsoft > Windows > Windows Defender`, right clicked on `Operational` and selected `Properties`. Again, this part is being done within Event Viewer on my Windows server. 

<img width="730" alt="day10-10" src="https://github.com/user-attachments/assets/274cf23d-589c-416b-ab44-76d61cfed6a9">

To avoid ingesting non-security related information into my SIEM, I set a filter to only ingest event IDs 1116, 1117, and 5001. These event IDs are all security related and I want this information ingested into my SIEM.
* [1116](https://learn.microsoft.com/en-us/defender-endpoint/troubleshoot-microsoft-defender-antivirus) alerts the user to malware or other potentially unwanted software being detected.
* [1117](https://learn.microsoft.com/en-us/defender-endpoint/troubleshoot-microsoft-defender-antivirus) informs the user that the antimalware platform took action to protect the system.
* [5001](https://learn.microsoft.com/en-us/defender-endpoint/troubleshoot-microsoft-defender-antivirus) alerts the user that real-time portection has been disabled.

All of these are cause for concern.

<img width="713" alt="day10-11" src="https://github.com/user-attachments/assets/0efafda1-ec73-47f0-a7c9-be95c484adc1">

Finally, I added the integration to an existing host, selecting the proper agent policy, saved and deployed.

<img width="968" alt="day10-12" src="https://github.com/user-attachments/assets/24fbe065-10fc-40b2-a12c-5e6aff332d74">

I did experience an issue where I wasn't seeing any of these logs when I searched for them in my Elasticsearch instance. After some troubleshooting, I restarted the Elastic Agent on my Windows server by opening up `Services`, right-clicking on `Elastic Agent`, and choosing `Restart`. 

<img width="960" alt="day10-14" src="https://github.com/user-attachments/assets/57057a5b-4603-4275-a568-0af685d4100b">

I did also have to configure a rule to allow incoming connections on port 9200 in my VPC firewall group policy. After these two changes, I was able to confirm that the desired data was being ingested into my Elasticsearch instance.

<img width="966" alt="day10-15" src="https://github.com/user-attachments/assets/57024c15-6c43-4dcc-b312-eb3af5ce5bc7">

Now, I can start exploring further and begin to better my understanding of how to interpret these logs.
