*This project is under construction.* 
# MyDFIR 30 Day SOC Analyst Challenge (Part 4)

<img width="896" alt="Part4banner" src="https://github.com/user-attachments/assets/42b2ae0a-0cc6-4fc6-9b95-d7c80b7e5b9e" />

## Day 16: Creating Alerts and Dashboards in Kibana (2/4)
Let's examine the authentication logs from my Windows server and create another brute force alert.

To get started, I opened up the `Discover` page within the Elastic web GUI. With the timeframe set to `Today`, I began narrowing down the events filtering by `agent.name` selecting only events logged by my `MYDFIR-WIN-jf` machine.

<img width="960" alt="day16-1" src="https://github.com/user-attachments/assets/46117632-7ae6-4483-abe9-653622246021" />

This machine has logged over 34,000 events today. Focusing on brute force activity, I can narrow these events down further by looking at event IDs that are associated with failed authentications. If you're not familiar with Windows event IDs, you can always Google it. In this case, [event ID 4625](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4625) documents every failed logon attempt. 

<img width="955" alt="day16-2" src="https://github.com/user-attachments/assets/3d59c82c-a019-47a5-baa2-f3d82ca64fa5" />

Searching for "event.code: 4625" narrows the number of events down further to 6,600. To start making sense of these results, I updated the search resutls table to include columns for `source.ip` and `user.name`.

<img width="962" alt="day16-5" src="https://github.com/user-attachments/assets/b0991f4b-2785-4139-8875-43f45da0a87f" />

Now that the search parameters are defined, a new alert can be configured. Navigating to `Alerts`, choosing `Create search threshold rule`, it is straightforward to name the rule, customize the configurations, test the rule, and save.
<img width="960" alt="day16-5a" src="https://github.com/user-attachments/assets/736d52d3-14d2-4ff0-b6de-66092a5499a7" />
<img width="958" alt="day16-5b" src="https://github.com/user-attachments/assets/034688eb-f380-4cca-9880-b147e06b3e13" />
<img width="960" alt="day16-5c" src="https://github.com/user-attachments/assets/f92fe2f0-a932-47de-b77a-19c3d04d2f19" />

#### Viewing Alerts
To view the alerts that these rules are now generating, under the hamburger icon, navigate to `Management` > `Stack Management` > `Alerts`. To view the alert details, click the ellipsis icon next to the rule name and select `View alert details`.
<img width="961" alt="day16-6b" src="https://github.com/user-attachments/assets/547a959a-e15e-49fe-a81b-6975cf86bbfb" />

The problem here is that it doesn't provide many useful details as to what has taken place. You may need to know what user was affected or where the attack came from.
<img width="959" alt="day16-6c" src="https://github.com/user-attachments/assets/0fe53363-d596-47d5-92dd-a7def0c6f2f6" />

#### SIEM Detection Rules
Let's create a rule that will provide us with more useful details. Under the hamburger icon, navigate to `Security` > `Rules` > `Detection rules (SIEM)`.
<img width="958" alt="day16-7" src="https://github.com/user-attachments/assets/3d0752f5-89c2-4ada-8192-9649395e1079" />

Here, you can load Elastic prebuilt detection rules and create custom rules. 
<img width="962" alt="day16-7a" src="https://github.com/user-attachments/assets/5b1bb91c-cbd2-4fb1-95ef-1517b989e5dd" />

Choosing `Create new rule` provides several options. In this case, I am using `Threshold`. 
<img width="957" alt="day16-7b" src="https://github.com/user-attachments/assets/e7522f3f-87d9-42e4-9e7f-c717ee927a19" />

The `Index Patterns` are left as default and the `Custom query` is defined by copying the previously saved search with an additional parameter of `user.name: root`. Next, decide how to organize the results in the `Group by` field. In this case, by username and source IP address. `User.name` and `source.ip` are also included as `Required fields`.
<img width="948" alt="day16-7c" src="https://github.com/user-attachments/assets/ffa63482-b8e4-4f69-b632-6021cc21a2d7" />

Next, the name will need a rule and brief description followed by severity and risk score
<img width="949" alt="day16-7d" src="https://github.com/user-attachments/assets/f3710d60-d163-4dc0-b9c7-1cab16eeb7aa" />

There are other optional settings to consider here under `Advanced settings` where items like URLs or the MITRE ATT&CK tactic can be included. You can also choose a custom highlighted field, include a setup guide, or even provide steps for investigating the alert. This is great for a SOC environment because it helps analysts understand exactly what they should and should not do when the alert has been triggered.

Next, shcedule the time interval on which the rule should run and decide how far back you want it to look.
<img width="949" alt="day16-7f" src="https://github.com/user-attachments/assets/c6573230-3390-447b-923a-38daea9d78f5" />

Now, you have the ability to choose what kind of action you want to take when the rule is triggered. Finish by clicking `Create & enable rule`.
<img width="947" alt="day16-7h" src="https://github.com/user-attachments/assets/b94f1f27-4269-4105-9614-5d76105176b5" />

This should now provide me with more details to work with, making investigating failed SSH logon attempts much easier. These steps were repeated to create a similar security alert for failed RDP attempts.

Although this rule is quite basic, it is just the beginning of understanding how to tune effective rules within a SOC environment. Good security rules in the ELK Stack are essential for effectively detecting and mitigating threats. They enable precise filtering, correlation, and analysis of security events, reducing false positives and highlighting genuine risks. Well-designed rules help SOC analysts prioritize incidents, respond faster, and maintain a secure environment, making the ELK Stack a powerful tool for cybersecurity operations.


## Day 17: Creating Alerts and Dashboards in Kibana (3/4)
Today's objective is to create a dashboard for RDP activity, similar to what was created on Day 14. Start by navigating to `Analytics` > `Maps` under the hamburger icon within the Elastic web GUI. Once there, it's time to define the query parameters. Luckily, these parameters have been already been saved as "RDP Failed Activity". That query searches `event.code: 4625`. To ensure this search only returns data from the RDP enabled Window server I need to include the agent's name, making the complete query `event.code: 4625 and agent.name: MYDFIR-WIN-jf`.

<img width="958" alt="day17-1" src="https://github.com/user-attachments/assets/cf855a49-613b-443f-a5b3-057ec5273fd4" />

Adding a layer to the map, I again chose Chloropleth, which means that the map uses colors to represent statistical data across geographic areas. The EMS boundaries are world countries, data view begins with .alerts, the Join field is source.geo.country_iso_code. 

<img width="952" alt="day17-1a" src="https://github.com/user-attachments/assets/55fbf531-c0e7-49d5-9025-4832f66d8948" />

I can already see more than 91,000 events originating from Russia alone. 

Next, the map was named and added to the existing dashboard.

<img width="960" alt="day17-1b" src="https://github.com/user-attachments/assets/e302bba3-b9c3-4fd8-8a37-4b4d1b89096c" />

Now, these same steps can be repeated to create a map to visualize successful authentications. Successful RDP logons can be either logon type 7 or logon type 10. Logon type 7 indicates that a user has unlocked a previously locked workstation using RDP. Type 10 indicates a successful RDP logon. 

With that in mind, this query needs to focus on logon types 7 and 10 as well as event ID 4624, a successful attempt at logging on to a local computer.

It's also critical to identify the proper field name when creating these queries. One way to do this is to go into the details of an event, in this case a successful logon event and see what kind of information is there. In the screenshot below, we can see that the field name for logon types is `winlog.event_data.LogonType`. 

<img width="961" alt="day17-2" src="https://github.com/user-attachments/assets/fe197930-dff8-4a7f-a84f-916ce175b140" />

Now that I know what field name to include in my search parameters for successful RDP logons, the full query in `event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`. Appropriately, when I search using these parameters, only 2 events are reutrned. Successful attempts should only be coming from me. If they were coming from anywhere else, I would know that I have a security problem that would need to be addressed immediately. 

<img width="959" alt="day17-2a" src="https://github.com/user-attachments/assets/a5d5ea17-ca84-4a74-b2fb-21f714d8bee1" />

After naming and saving this search, I went to the dashboard and duplicated the previous map. 

<img width="948" alt="day17-3" src="https://github.com/user-attachments/assets/be4da1af-5419-4b13-be24-c965446163c7" />

Updated the search parameters.

<img width="958" alt="day17-3a" src="https://github.com/user-attachments/assets/699f2628-5165-4a07-98a2-8f84135aacee" />

Now, I have a dashboard showing failed and successful SSH authentications and failed and successful RDP authentications.

<img width="948" alt="day17-3b" src="https://github.com/user-attachments/assets/0f4331e4-1c66-4e6d-b1ec-0a60d94f8cbe" />

This dashboard is helpful, but it can be much better. To upgrade it, I will a table to go along with the map. The table will include user name, count and source IP address, and source country providing even more useful information. 

Under the hamburger menu, I navigated to `Dashboards > Creaye visualization`. 

<img width="959" alt="day17-4a" src="https://github.com/user-attachments/assets/32dee010-7025-4a33-b6fd-ad4e5cb10dff" />

Paste in the search parameters, i.e., `system.auth.ssh.event: * and agent.name: MYDFIR-Linux-jf and system.auth.ssh.event: Failed`. Update the timeframe to `Last 7 days` and drag and drop the desired field names. Change the `Visulaization type` from bar graph to table. Now, rearrange the columns to suit your needs. It also possible to edit the individual columns, which I did to show the top 10 instead of the top 3 and I unchecked “group remaining as other”. I sorted the records in descending order. Finally, I saved it and renamed the table to `SSH Failed Activity`. 

<img width="947" alt="day17-4f" src="https://github.com/user-attachments/assets/932bd9b7-98b8-4170-84f2-beda8be24a00" />

This process was duplicated to create similar tables for successful SSH activity as well as both failed and successful RDP activity. Keep in mind that the search parameters must be updated for each of these tables to match what data should be displayed. 

The tables provide detailed, granular data to complement the map's high-level overview. While the map visually highlights patterns and trends (e.g., geographic clusters of activity), the data table offers precise information such as IP addresses, timestamps, usernames, and success/failure counts. In a SOC environment, this allows analysts to:
1) __Drill Down for Investigation:__ Quickly identify suspicious activity with exact details for further analysis.
2) __Correlate Data:__ Cross-reference the table's data with other sources or tools to confirm potential threats.
3) __Improve Incident Reporting:__ Present both visual insights and specific evidence for comprehensive incident reports.
4) __Support Automation:__ Export structured table data for automated analysis or threat intelligence tools.

Combining visual and tabular data ensures a more efficient and thorough analysis process.

## Day 18: Command & Control (C2)
The goal for today is to begin to understand what command and control (C2) is, why it is important, what are some common tools used fro C2, and what is the Mythic framework and how does it work. 

Although each malware is different, once executed, many will take similar initial discovery actions by running commands like `ipconfog`, `whoami`, `nslookup`, and `net user` or try to establish persistence by service creation or task scheduling. An attackers dream is to establish a command and control session over the target machine.

Command and Control (C2) is a tactic used by attackers to establish communication with compromised systems to remotely issue commands, manage operations, and exfiltrate data. C2 is a critical part of many cyber attacks, enabling persistent access and control over the victim's environment. 

According to the Mitre Att&ck framework, there are no less than [18 different techniques](https://attack.mitre.org/tactics/TA0011/), and even more subtechniques, that attackers use to communicate with systems under their control. Each allowing an attacker to perform actions on it and working towards their objective.

#### Some Common C2 Tools & Frameworks
__[Metasploit](https://www.metasploit.com/):__ According to it's website, is the world's most used penetration testing framework. It can function as a tool to establish and maintain a communication channel between an attacker (or penetration tester) and a compromised system, primarily achieved through payloads and post-exploitation tools like Meterpreter.

__[Cobalt Strike](https://www.cobaltstrike.com/):__ A widely used C2 framework designed for advanced penetration testing and red teaming. It allows attackers to simulate post-exploitation scenarios, establish robust C2 channels, and mimic adversary tactics in a controlled manner. It offers a suite of tools and features that enable stealthy, flexible, and resilient C2 operations.

__[Sliver](https://bishopfox.com/tools/sliver):__ An open-source C2 framework developed for red teaming, penetration testing, and adversary emulation. It is a cost-effective alternative to proprietary C2 tools with regular updates from a reputable security company and supports advanced C2 techniques for stealthy operations. Its modular design, malleable profiles, and peer-to-peer capabilities make it highly flexible for simulating advanced adversary techniques.

__[Mythic](https://docs.mythic-c2.net/):__ Designed with a focus on flexibility and extensibility, it allows operators to manage compromised systems efficiently while mimicking advanced adversary behaviors. Strong community support, flexibility, and user-friendly interface make it suitable for both experienced operators and those new to red teaming.

_______
Command and Control (C2) is crucial in cybersecurity for both offensive and defensive purposes. Understanding C2 is vital for defenders to detect, disrupt, and mitigate adversary operations while for attackers, it is key to emulating real-world threats in penetration testing and red teaming scenarios.

Finally, __[The DFIR Report](https://thedfirreport.com/)__ is a well regarded resource which publishes detailed, real-world case studies and technical analyses of cyberattacks, focusing on the tactics, techniques, and procedures (TTPs) used by adversaries. The reports aim to help security professionals understand threat actor behaviors, improve detection capabilities, and strengthen incident response strategies. Free and open to the community, it serves as a learning tool for analysts, incident responders, and security researchers.

## DAY 19: Attack Diagrams
In the coming days, I will actively attack a Windows server to establish command and control (C2) over it. To help facilitate that upcoming attack, it is important to take the time and logically think through the attack step by step. One way to accomplish this is with an attack diagram. Attack diagrams are a visual representation used in cybersecurity to illustrate the stages, methods, tools, and paths an attacker might take to compromise a system or network. They are valuable for understanding, analyzing, and communicating the progression and potential impact of cyberattacks.
[
Draw.io](https://app.diagrams.net/) is a great tool for creating these types of diagrams. However, rather than going over how to create a diagram, it may be more useful to take the attack diagram which I have already created and break it down step by step to gain a better understanding of how the attack is going to work.

#### Phase 1: Initial Access
A Kali Linux machine will gain unauthorized access to the target Windows Server through brute-forcing Remote Desktop Protocol (RDP) credentials. After brute-forcing, a successful authentication will provide the initial access to the target environment.
![1MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/0c4c023e-fdfc-443a-b72b-4028b0984072)

To help mitigate this risk organizations can enforce strong password policies, enable MFA, restrict/disable RDP access, enforce account lockout policies, and monitor login activity. 

#### Phase 2: Discovery
The discovery phase helps an attacker understand the system's structure and potential for lateral movement or privilege escalation. This is accomplished via commands like `whoami`, `ipconfig`, `net user`, and `net group` to gather information aboutthe current user identity, network configurations as well as users and groups on the system.
![2MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/8f60d046-e7aa-4ace-9cc2-f3bf54937757)

To help mitigate this risk organizations can adhere to the principle of least privilege, monitor system commands, utilize an endpoint detection and response (EDR) solution, and segment their network.

#### Phase 3: Defense Evasion
In order to evade endpoint protection and maintain stealth, its critical to avoid triggering any alerts or other security measures the target has configured. Here, the attack executes commands via RDP to disable Windows Defender helping to avoid detection of subsequent malicious activities.
![3MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/102299f6-2b38-4381-a069-05189d050f3f)

To help mitigate this risk organizations can use tamper protection to prevent unauthorized changes to Windows Defender or other security tools, enable logging for changes to Windows Defender settings and set up alerts for such activity, block or restrict the execution of unauthorized PowerShell commands, and enforce privileged access management by ensuring only authorized personnel can disable security tools.

#### Phase 4: Execution
To deliver the payload, the attack will utilize PowerShell IEX which refers to the Invoke-Expression cmdlet in PowerShell. It is a built-in command used to evaluate or execute a string as a PowerShell expression or script. Using it allows the attack to utilize Powershell to download and execute a malicious agent from the internet. In this instance, the payload is connected to a Mythic Command-and-Control (C2) server, which provides the attacker with remote control over the compromised server.
![4MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/e8947143-2d54-4c28-9e40-b659725569a6)

To help mitigate this riks organizations can use PowerShell Constrained Language Mode to limit the execution of malicious scripts, block known malicious URLs or IPs to prevent payload download, enforce code signing by requiring all PowerShell scripts to be digitally signed, and ensure antivirus software actively scans for suspicious script execution or payload delivery.

#### Phase 5: Command and Control
The malicious Mythic agent establishes a C2 channel with the Mythic server allowing the attacker to execute commands, exfiltrate data, and maintain control over the compromised system.
![5MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/f7523a88-f01d-42b2-b088-4cbd49077d64)

To help mitigate this risk organizations can use intrusion detection/prevention systems (IDS/IPS) to detect and block suspicious C2 traffic, restrict outbound traffic to known malicious IPs or domains using a firewall or web filtering, detect anomalies like unusual traffic patterns or behavior indicative of C2 communication, and isolate compromised systems to disrupt communication with the attacker.

#### Phase 6: Exfiltration
The attacker downloads a file (passwords.txt) from the target Windows Server. This simulates actual data exfiltration, and access sensitive information.
![6MyDFIR Attack Diagram drawio](https://github.com/user-attachments/assets/150a9eef-389a-4fc8-87fb-d240db88318c)

To help mitigate this risk organizations can implement tools to monitor and prevent unauthorized data transfers, ensure critical files are encrypted to prevent unauthorized access, set up alerts for unusual file creation, modification, or access activities, and bock unauthorized data transfer attempts at the network boundary.
__________

This attack uses common tools like Kali Linux, PowerShell, and Mythic C2 and tactics that leverage weak RDP credentials, built-in Windows utilities, and the disabling security features to progress through the network. It also aligns with stages in frameworks like the MITRE ATT&CK, including Initial Access, Discovery, Defense Evasion, Execution, Command and Control, and Exfiltration.

There are different types of attack diagrams but they all serve to illustrate the stages, methods, tools, and paths an attacker might take to compromise a system or network. The benefits of using attack diagrams include:
* __Improves Understanding:__ Simplifies complex attack paths for stakeholders.
* __Aids in Threat Analysis:__ Helps SOC teams anticipate and mitigate similar attacks.
* __Supports Incident Response:__ Guides analysts in tracing the attack and identifying containment strategies.
* __Training Tool:__ Enhances the skills of SOC analysts by providing clear examples of attack scenarios.
* __Communicates Risks:__ Effectively conveys technical details to non-technical audiences.

## DAY 20: Mythic Server Setup
Today's objective is to setup a Mythic C2 server and begin to understand how [Mythic](https://docs.mythic-c2.net/) works.

The first step is to deploy a new server within my [VPC](https://en.wikipedia.org/wiki/Virtual_private_cloud) network. This server won't experience much demand so it is configured as cloud compute with a shared CPU running an Ubuntu 22.04 OS. [Mythic recommends](https://docs.mythic-c2.net/installation) that it is run on a virtual machine with at least 2 CPUS and 4GB of RAM.

Next, I downloaded installed Kali Linux for a VMWare virtual machine. Once this VM was up and running, I utilized Powershell to SSH into the Mythic Server. Once there, I ran `apt-get update && apt-get upgrade -y` because it's always best to update and upgrade the repositories before getting started on any tasks, particularly with a newly established machine.

Mythic does require some [prerequisites](https://docs.mythic-c2.net/installation) to run. [Docker Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container applications.
<img width="967" alt="day20-2" src="https://github.com/user-attachments/assets/2ef29eb5-2b32-4345-bac6-5588c5a64a28" />

[Make](https://docs.mythic-c2.net/customizing/payload-type-development) compiles payloads, generates docker containers, and builds plugins for Mythic. In this instance, it was already installed.
<img width="959" alt="day20-3" src="https://github.com/user-attachments/assets/d2d13557-6c35-4225-a3a5-8ef11c0bc72a" />

Next, clone the repository. An essential step because the repository contains all the code, configurations, and resources needed to run the platform. The repository link is [https://github.com/its-a-feature/Mythic](https://github.com/its-a-feature/Mythic).
<img width="947" alt="day20-4aa" src="https://github.com/user-attachments/assets/3e8f9ac4-b5ef-45cc-87d1-d21d807787bf" />

Now, listing out what's available with the `ls` command, Mythic appears. Navigating to that directory, I see the [shell script](https://en.wikipedia.org/wiki/Shell_script), `install_docker_ubuntu.sh that I need to invoke.
<img width="947" alt="day20-4" src="https://github.com/user-attachments/assets/58005bc6-8c2d-41d4-b942-8b28f05c3b21" />

Once that finishes, I ran the command `make` which automates repetitive setup tasks like compiling code, creating Docker containers, or managing dependencies. However, I encountered an error that it could not connect to the Docker daemon so, I ran `systemctl status docker` to check the status. Sure enough, it was in a failed state. 
<img width="960" alt="day20-5" src="https://github.com/user-attachments/assets/ab4e6697-d3ce-4495-8826-d833b1abb311" />

This was easily resolved by restarticg Docker by running `systemctl restart docker`. 
<img width="957" alt="day20-5a" src="https://github.com/user-attachments/assets/6a8c872c-2abd-4634-8910-3388f074d2b6" />

Running `make` again, successfully this time.
<img width="958" alt="day20-6" src="https://github.com/user-attachments/assets/f376ab2c-f4d6-454d-8659-646457d9412f" />

Next, running `./mythic-cli start` starts the Mythic command line interface. With that, the Mythic C2 setup is complete and I should be able to log in. Before doing that, I want to configure a firewall rule that will only allow my computer and other agents within my controlto communicate with this Mythic. 

I created a new firewall group within my VPC, configured rules to only allow the necessary machines to communicate, and ensured my Mythic server was assigned to this firewall group.
<img width="956" alt="day20-7b" src="https://github.com/user-attachments/assets/3d07805f-34e9-417e-bacb-225d493409d9" />
<img width="965" alt="day20-7c" src="https://github.com/user-attachments/assets/e49739c2-83d3-45ec-b861-354db274f409" />
<img width="946" alt="day20-7d" src="https://github.com/user-attachments/assets/56ad7229-c855-4ce2-a010-c369b5adcc33" />
<img width="949" alt="day20-7e" src="https://github.com/user-attachments/assets/604d9fbf-d885-43d8-ac9f-c25c16d6f85e" />

To log into my newly established Mythic web GUI, I navigated to the server's IP address using port 7443, the default port for its HTTPS-based web interface, which brought me to the login screen.
<img width="954" alt="day20-8png" src="https://github.com/user-attachments/assets/8991dacf-50ba-4543-b0aa-0c06d0fd8c05" />

The default username is mythic_admin. The environment variable, which is hidden, specifies the password for this mythic_admin account. Entering `ls -la`, to include listing out the hidden files, the `.env` is seen.
<img width="947" alt="day20-8a" src="https://github.com/user-attachments/assets/36612b4e-dbdd-4a95-9dda-0e6d8cb441b2" />

Run `cat .env` to read what is inside the file and find the default password.
<img width="962" alt="day20-8b" src="https://github.com/user-attachments/assets/2340bc3d-9fd6-4eec-9cc7-bda51cb73aea" />

After entering the password, I am now inside my Mythic C2 server web GUI.
<img width="954" alt="day20-8c" src="https://github.com/user-attachments/assets/a90b945c-20cd-4471-8ee1-fc4ad1da9299" />

There isn't any activity yet but, that will come in the next few days. Some of the key features of the Mythic web GUI include:
*__Dashboard:__
  + Displays a summary of active agents, pending tasks, and system health.
  + Provides quick access to essential tools and features.
*__Agent Management:__
  + Monitor connected agents (payloads running on compromised systems).
  + View detailed information about each agent, including callback data and system details.
*__Tasking and Responses:__
  + Create, assign, and manage tasks for connected agents.
  + View responses from agents, including command output or retrieved files.
*__Payload Management:__
  + Configure and generate payloads for various operating systems and communication methods.
  + Customize C2 profiles for different operational needs.
*__Logging and Reporting:__
  + View detailed logs of all activities for tracking and auditing.
  + Export task and agent data for reporting purposes.

