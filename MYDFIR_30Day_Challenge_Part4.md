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
__Metasploit:__ According to it's [website](https://www.metasploit.com/), is the world's most used penetration testing framework. It can function as a tool to establish and maintain a communication channel between an attacker (or penetration tester) and a compromised system, primarily achieved through payloads and post-exploitation tools like Meterpreter.
__[Cobalt Strike](https://www.cobaltstrike.com/):__ A widely used C2 framework designed for advanced penetration testing and red teaming. It allows attackers to simulate post-exploitation scenarios, establish robust C2 channels, and mimic adversary tactics in a controlled manner. It offers a suite of tools and features that enable stealthy, flexible, and resilient C2 operations.
__[Sliver](https://bishopfox.com/tools/sliver):__ An open-source C2 framework developed for red teaming, penetration testing, and adversary emulation. It is a cost-effective alternative to proprietary C2 tools with regular updates from a reputable security company and supports advanced C2 techniques for stealthy operations. Its modular design, malleable profiles, and peer-to-peer capabilities make it highly flexible for simulating advanced adversary techniques.
__[Mythic](https://docs.mythic-c2.net/):__ Designed with a focus on flexibility and extensibility, it allows operators to manage compromised systems efficiently while mimicking advanced adversary behaviors. Strong community support, flexibility, and user-friendly interface make it suitable for both experienced operators and those new to red teaming.
