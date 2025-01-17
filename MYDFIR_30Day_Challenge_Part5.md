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

To get started, I deployed a new, dedicated server running on a Windows Standoard 2022 operating system within my VPC network. Once the server was set up, I integrated it into my firewall group.

Next, I connected directly to this new virtual machine via remote desktop protocol (RDP) to set up a web server. To do this, I used `xampp`. Google `xampp` to find the [dowload link](https://www.apachefriends.org/download.html). Once the installer downloaded, I opened the file to run the installer leaving the default settings in place. 
<img width="948" alt="Day24-1" src="https://github.com/user-attachments/assets/1c288a37-b5b7-4c27-919d-1bd99fc2043b" />

Once installation is complete, you will see the `xampp` control panel. 
<img width="958" alt="Day24-1a" src="https://github.com/user-attachments/assets/c0574c87-8ff8-4867-a26b-2f1a65c12689" />

Before going any further, I need to edit the properties configuration file located in `C:\xampp\properties.txt`. I need to change `apache_domainname` from 127.0.0.1 (localhost) to my server’s public IP, which is 45.76.213.92
<img width="959" alt="Day24-2" src="https://github.com/user-attachments/assets/8a91bc4c-3df5-4b80-872f-9518b4dd1a6e" />
Save and exit.

Next, change the phpMyAdmin configuration file located at `C:\xampp\phpMyAdmin\config.inc.php`. Before making any changes to this file I created a backup file by copying the original and renaming it `config.inc.backup.php`.

I opened the file with Notepad and changed `/* Bind to the localhost ipv4 address and tcp */
$cfg['Servers'][$i]['host'] = '127.0.0.1';` to use my server's public IP address, `/* Bind to the localhost ipv4 address and tcp */
$cfg['Servers'][$i]['host'] = '45.76.213.92'. 
<img width="959" alt="Day24-2a" src="https://github.com/user-attachments/assets/c1c080d5-0d3b-482e-8d72-1079d6170446" />
Save and exit.
