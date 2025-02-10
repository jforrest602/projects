*This project is under construction.*
# MyDFIR 30 Day SOC Analyst Challenge (Part 6)
<img width="894" alt="part6header" src="https://github.com/user-attachments/assets/928e52a6-b869-4f58-86ea-864eb87c4635" />

## Day 26: Investigating an SSH Brute Force Attack
To begin investigating the SSH alerts that I previously created, I navigated to the hamburger icon `Secuirty > Alerts` in my Elastic web GUI. Nothing displayed on this page.
<img width="959" alt="Day26-2" src="https://github.com/user-attachments/assets/13eab798-7bfe-4f29-8634-eccab89fb280" />

However, I know there had been 176 events over the last 7 days, as shown in `Observability > Alerts` below that should be triggering the security alert.
<img width="959" alt="Day26-1" src="https://github.com/user-attachments/assets/a623ccab-f8da-4b94-b050-c52f199e8c75" />

More on this later.

----
There are several ways to investigate a brute force attack. Many failed login attempts over a short period of time from the same IP address or against the same account can be a key indicator. Alert rules can be defined to monitor this. Login attempts from uncommon geo-locations can be another indicator. 

#### Timelines in Elastic
Elastic offers a [timeline function](https://www.elastic.co/guide/en/security/current/timelines-ui.html) that can provide relevant information at a glance to help with investigations. Users have the ability to use predefined timeline templates or create custom timelines. Timelines utilize a drag-and-drop interface, query filters, and can even integrate MITRE ATT&CK tactics and techniques. Timelines can be a powerful tool for SOC analysts to investigate security threats, correlate events, and respond to incidents efficiently.

----
#### Investigating an Alert
<img width="947" alt="Day26-3" src="https://github.com/user-attachments/assets/aabc3628-10bf-4c66-9ec6-781e3a854e35" />

There are a few questions that you can ask yourself that can help with the investigation.
__1) Is this IP address known to perform brute force activities?__
This helps build context around the alert. If the IP address is known to be associated with brute force activity, it should be cause for concern.
The above screenshot of some of the alert details gives me the IP address of `218.92.0.182`. To find out whether or not this IP address has been associated with any malicious behavior, I can search for it on [AbuseIPDB](https://www.abuseipdb.com/). This IP address has been reported 1094 times with a 100% confidence of abuse.
<img width="959" alt="Day26-4" src="https://github.com/user-attachments/assets/aad49208-0b6e-4271-b987-79aa9ca30311" />

Scrolling down to view more details, recent reports all involved failed password for root and are tagged as ssh brute force, over and over.
<img width="958" alt="Day26-4a" src="https://github.com/user-attachments/assets/5c706d74-3cac-4741-a230-c12f4198db85" />

This leaves no doubt that this IP address is well known to perform brute force attacks.

Another good resource to use is [GreyNoise](https://www.greynoise.io/), which similarly allows users to search IP addresses. After entering the `218.92.0.182` IP address, it is very lear that this IP is associated with malicious activity.
<img width="959" alt="Day26-4b" src="https://github.com/user-attachments/assets/b8bae858-f4db-49a5-9767-387edde54a8c" />

It even goes as far as suggesting a course of action.
<img width="960" alt="Day26-4c" src="https://github.com/user-attachments/assets/8a0ffb1c-08ed-44ef-892c-2ce5947c390e" />

Other details like associated organization and tags are also provided.
<img width="958" alt="Day26-4d" src="https://github.com/user-attachments/assets/60806dd2-61f0-4981-9d52-f9e4fb444836" />

__2) Were any other users affected by this IP address?__
Answering this question can help discern who or what the attacker is targeting. To answer it, I navigated to the search function within Elastic and searched for the `218.92.0.182` IP address. The results provide nearly 8,200 events in the psat 30 days. To find out what users were targeted, I selected the `user.name` field from the list of field names in the left hand column. It showed me that the user root was targeted 100% of the time.
<img width="960" alt="Day26-5" src="https://github.com/user-attachments/assets/e8adb8c5-b2fb-4c8b-bea2-f724c0b4b33e" />

__3) Were any of the login attempts succesful?__
This is critical to know because if a successful logon occurred, I want to know what type of activity occurred next. Did they download a shellscript? Did they perform discovery commands? Did they execute something malicious? Did they run [linpeas](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS) for privilege escalation?

To find the answer, I modified my Elsaticsearch parameters from `218.92.0.182` to `218.92.0.182 AND Accepted` to show me any successfull login events from that IP address. 
Note here that capitalization *DOES* matter when crafting searches in Elastic. 
<img width="959" alt="Day26-6" src="https://github.com/user-attachments/assets/7ebef4e5-93ec-4940-be05-49902a9457bf" />

No successful logins from this IP address occurred. If this were taking place in an organizational setting and not just a demonstration, I would be following a defined process, like entering any notes into the ticketing system before closing this alert.

__4) If yes, what activities occurred after the login?__
If I did find any successful logins, this investigation could go much further. I would want to know what actions the attacker took next and where on the network did they go.

#### Modifying Rules to Push Alerts to osTicket
The osTicket instance that I set up will be useless if it isn't able to receive any alerts so, I must configure the alert rules to push the alerts to the ticketing system.

To do this, I went to `Rules > Detection rules (SIEM) > *rule name* > Edit rule settings > Actions > Webhook`. Once there, my osTicket was selected by default. I changed the action frequency to each alert.
<img width="957" alt="Day26-7" src="https://github.com/user-attachments/assets/adddd92e-0b6d-43b0-b7c6-8f49538ee2d9" />

Next, I updated the body of the xml payload [example provided by osTicket](https://docs.osticket.com/en/latest/Developer%20Documentation/API/Tickets.html?highlight=body). 
Within this xml payload example, I want to change the `subject` field from `Testing API` to the actual rule name. To do this,
I clicked on the small `Add variable` icon highlighted in the screenshot below and chose `rule.name` from the list of available variables.
<img width="958" alt="Day26-7a" src="https://github.com/user-attachments/assets/f4a97772-156c-49b9-8cc9-057bc63571f8" />

Next, I updated the included message to let the receiver know to investigate the rule.
<img width="959" alt="Day26-7b" src="https://github.com/user-attachments/assets/1eca159f-1738-43f4-b631-95c6e397f775" />

Finally, I repeated these steps for the alert rule monitoring for any RDP connections. After a few minutes, I refreshed my osTicket web-interface and the was a new ticket.
<img width="960" alt="Day26-8" src="https://github.com/user-attachments/assets/84699ed4-acdd-47fe-b615-732629934eb4" />

By clicking into the alert I can see some details. This can be configured to include many other details but that is beyond the scope of what I am trying to do today. Ideally, I think that including the alert name, source IP address, user name, and computer could go a long way in providing the analyst with good information to begin their work. For now, I'm satisfied that the alerts are being pushed to osTicket.
<img width="960" alt="Day26-8a" src="https://github.com/user-attachments/assets/23683bf3-3c31-46f2-af61-0d9294b82be5" />

#### Troubleshooting
I ran into a problem right out of the gate with this day's task. The alerts were not populating in the Security alerts section in my Elastic web GUI. It took me more than a days work to figure out what the issue was. The filebeat, which collects and forwards logs from various sources, was not mapped correctly. Improper mapping can cause inconsistencies across field types resulting in query failures or dashboard issues leading to delays in investigations. This is all new to me but I will say that if things aren't showing up where you expect them to be, mapping could be an issue.

The troubleshooting process is a learning process. It was frustrating at the time but I have emerged a bit more knowlegable and much more aware about the importance of ensuring proper mapping in a SOC environment. SOCs rely on accurate, searchable data which enables strong threat detection and effecient incident response.

## Day 27: Investigating an RDP Brute Force Attack
Today, I am going to investigate an RDP brute force alert and identify some of the key indicators to be aware of. Here, I am going to apply the same methodology that I used to investigate the SSH brute force alert on Day 26. 

I began in the Elastic web GUI by navigating to `Security > Alerts` under the hamburger menu and filtered for RDP brute force alerts occuring in the past 30 days. More than 1,000 events have been generated. 
<img width="960" alt="Day27-1" src="https://github.com/user-attachments/assets/ee98f45f-c0b8-431a-8860-fb66230b0e42" />

Next, I chose the first event listed and brought up its details and see that the IP address `179.60.147.198` attempted to login as Administrator 22 times.
<img width="959" alt="Day27-2" src="https://github.com/user-attachments/assets/69d543e3-02b6-4f70-ad1e-c9874c25c89a" />

#### Pushing the alerts to osTicket
Now, like yesterday, I want to modify this rule to automatically create a ticket in osTicket each time it triggers. To do this, I'm following the same steps. I will even copy and paste the body of the xml payload.

After copying the body of the xml payload, I navigated to `Rules > Detection rules (SIEM) > *rule name* > Edit rule settings > Actions > Webhook` and confirmed that osTicket was auto selected, pasted in the body and saved the changes.

Before moving on, I went to `Edit rule settings > Schedule` to ensure that the rule would run every 1 minute.

#### Investigation Methodology
Just like when investigating the SSH brute force attempts, there are four questions that I want to answer as I go through this investigation. They are:

__1) Is this IP known to perform brute force activity?__ Yes

__2) Were any other users affected by this IP address?__ No

__3) Were any of the login attempts successful?__ No

__4) If so, what activity occurred after the successful login?__ N/A

I will use [AbuseIPDB](https://www.abuseipdb.com/) and [Greynoise](https://www.greynoise.io/) again to check the `179.60.147.198` IP address.

This IP address, originating from the Netherlands, has been report to AbuseIPDB 635 times with a 100% confidence of abuse associated with RDP brute force activity.
<img width="961" alt="Day27-3" src="https://github.com/user-attachments/assets/2fe60868-2061-42ac-8381-efff0dca6ec4" />
<img width="959" alt="Day27-3a" src="https://github.com/user-attachments/assets/f55b4811-ea33-4976-bcb0-b5bdf75d4b41" />

Greynoise doesn't provide as much information for this IP, but does tag it as an RDP crawler - a tool or program that scans or "crawls" through RDP servers to discover and collect information about available systems or potential vulnerabilities.
<img width="958" alt="Day27-3b" src="https://github.com/user-attachments/assets/bc6f992b-b9b2-4525-9944-b5c1054d30c7" />

To find out if any other users have been affected by this IP, I go to `Discover` in the Elastic web GUI to search for the IP address. I find that there have been more than 48,000 events in the past 30 days. All of them targeting Administrator.
<img width="960" alt="Day27-4" src="https://github.com/user-attachments/assets/6c96fb79-f9b4-450b-98bb-3078c1467338" />

To determine if any of the 48,000+ authentication attempts were successful, I combine the IP address and the event code 4624 to make my query - `179.60.147.198 and event.code: 4624`. Remember that event code 4624 in Windows is a log entry that records a successful account logon.
<img width="959" alt="Day27-4a" src="https://github.com/user-attachments/assets/817645ff-6d94-4e98-b529-043c1dcac64b" />

Thankfully, there hasn't been any successful authentications. However, just to be sure my query worked as expected, I query just `event.code: 4624` to see if there are any results. And there are 557 events returned, meaning my query performed as I intended.
<img width="959" alt="Day27-4b" src="https://github.com/user-attachments/assets/e39f4382-9d53-45f0-882f-b244ebdb71e9" />

#### Conclusions
By applying the same investigative methodology as with the SSH brute force attempts, I was able to identify and analyze the RDP brute force activity. The IP `179.60.147.198` is clearly linked to malicious behavior, with numerous failed attempts targeting Administrator accounts. Fortunately, no successful logins were observed. Using tools like AbuseIPDB and Greynoise provided context to the IP’s activity, reinforcing its association with brute force attacks. Finally, by pushing alerts to osTicket, I am ensuring that a hypothetical security team is immediately notified of any future attempts, allowing for quicker responses to potential threats.

## Day 28: Investigating Mythic Agent
Today's task will involve investigating the Mythic coomand-and-control (C2) framework by identifying some common things to look for. To get started, I do have a bit of an unfair advantage because, based upon prevous activity in this challenge, I know that the Mythic C2 Agent in this case is called `srvhost-jf.exe`.

With that in mind, I search for `srvhost-jf.exe` in my Elastic web GUI, sorting the results from old to new to begin following the chain of events. The search returns 40 results that have taken place within the last 30 days.
<img width="957" alt="Day28-1" src="https://github.com/user-attachments/assets/86b26fa4-b8f7-49d7-9d22-07f63801f237" />

#### How to Identify a C2 (When the Agent Name is not Known)
- Examine network telemetry. Typically, an ongoing C2 session will have a lot of back and forth traffic. In other words, a lot of data bytes are being transferred back and forth and the IP addresses will likely be among the top ten IP address pairs communicating with each other.
- Use a tool like [RITA](https://www.blackhillsinfosec.com/projects/rita/), offered by [BHIS](https://www.blackhillsinfosec.com/), to examine the heartbeat which can help detect potential C2 traffic.
- Use Sysmon to look at process creations (event ID 1) and network creations (event ID 3).
---- 

I created a dashboard to display suspicious activity, like process creations and process initiated network connections. Process initiated network connections are network connections that started as a process on the local machine and called out to somewhere to establish a connection.
<img width="958" alt="Day28-2" src="https://github.com/user-attachments/assets/4ef4ebaa-1fb2-4029-89cd-422a6747a9e4" />

Scrolling a bit further down that list, I see the `C:\Users\Public\Downloads\srvhost-jf.exe`.
<img width="946" alt="Day28-3a" src="https://github.com/user-attachments/assets/a27da619-284c-4506-8779-5c0f70900696" />

I can identify this as suspicious, even if it weren't titled `svchost-jf.exe`, because this directory is an odd place for an executable to be originating a connection to an external IP address. 

Just below that entry, I see Powershell calling out to the same IP address. That is certainly suspicious. I want to investigate this more.

#### Digging in to the PowerShell Call Out
I begin by searching `event.code: 3 and winlog.event_data.DestinationIp: 108.160.131.2` to start building a timeline of events. Remember, in Sysmon, event code 3 represents a network connection. The search returns 5 results.
<img width="960" alt="Day28-2-1" src="https://github.com/user-attachments/assets/6bb23839-1e5c-4d16-9cdc-cfda198ff0da" />

__TIMELINE__
- Nov 15, 2024 @ 16:04:30.169 (local time): a file named `svchost-jf.exe` is created under `C:\Users\Public\Downloads\`.
<img width="959" alt="Day28-2-4" src="https://github.com/user-attachments/assets/6caee549-4dd0-4716-a67f-c761d5162e33" />

- Nov 15, 2024 @ 16:04:31.202 (local time): an outbound network connection to `108.160.131.2` is made.
<img width="958" alt="Day28-2-2" src="https://github.com/user-attachments/assets/33d26508-d683-4c27-a2c2-85c9a4eee375" />

It is good practice to take a look at the Process GUID if it's available. This process GUID can help identify all of the events generated from the PowerShell session.

- Nov 15, 2024 @ 16:04:32.723 (local time): a file executable is detected, event code 29. In Sysmon, event code 29 is generated when Sysmon detects the creation of a new executable file. It is also worth noting that the message here contains the file hashes which could prove helpful.
<img width="958" alt="Day28-2-5a" src="https://github.com/user-attachments/assets/8b17af76-019e-4467-aada-28ac391f763d" />

- Nov 15, 2024 @ 16:05:52.785 (local time): a process create of `svchost-jf.exe` takes place.
<img width="959" alt="Day28-2-6a" src="https://github.com/user-attachments/assets/4e4f7d11-acb6-4ce7-9bf4-b9e92b10ab24" />

It is important to take note of the process GUID here, if it is available, because it can be used to track down related activities. A quick search returns promising results like original file name, command line input, parent user, parent image, and process ID.
<img width="962" alt="Day28-2-6" src="https://github.com/user-attachments/assets/753ff83f-e656-43d9-9a8c-648be5e869fe" />

Another good practice is to utilize the process ID and parent process ID to look for any processes spawned from the parent process.

As you can see, this sequence of events strongly suggests the possibility of a malicious process being executed, potentially indicating a system compromise or malware infection. Of course, further investigation is needed to confirm the intent and scope of the activity.

#### Next Steps
__1)Examine the Process GUID:__ If available, track the events associated with the Process GUID to get a complete view of the process lifecycle, its parent process, and any other actions it triggered.

__2)Investigate the Network Connection:__ Investigate the IP address `108.160.131.2` to check if it is known for malicious activity. Tools like threat intelligence feeds or external blacklists can help determine whether it is associated with C2 or malware.

__3)Check the File Hash:__ Use the file hash provided by Sysmon to look up the file in antivirus/malware databases or check for any previous detections of the same file. This can help verify whether this is part of a known malware campaign.

__4)Parent Process Review:__ Investigate the parent process that spawned `svchost-jf.exe` (if this information is available) to understand how this process was initiated. Checking for any PowerShell or other script activity may provide additional clues.

__5)Analyze System Behavior:__ Monitor the system for any additional signs of unusual activity, such as further outbound network connections, creation of additional files, or system performance anomalies.

## Day 29: Elastic Defend Setup
