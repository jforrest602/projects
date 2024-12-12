*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge (Part 3)

<img width="896" alt="Part 3 header" src="https://github.com/user-attachments/assets/17f0ae1b-4141-4628-8309-1db3531a52c5">

## Day 11: Understanding Brute Force Attacks
The goals for today are twofold:
* Understand what a brute force attack is.
* Learn how to protect yourself from a brute force attack.

#### What is a brute force attack?
A brute force attack is a method used to gain unauthorized access to a system, account, or encrypted data by systematically trying all possible combinations of passwords, encryption keys, or credentials until the correct one is found. This attack method relies on computing power and persistence rather than exploiting vulnerabilities.

Unfortunately, brute force methods are accessible and easy to use, making it a common attack method. Servers all around the world have services running that are exposed to the internet, like remote desktop (RDP), allowing anyone from the internet to connect and attempt to login using credentials found in password dumps, malware, or social engineering techniques. 

#### 3 Common Brute Force Attacks
__1) Simple Brute Force__: The attacker, relying on automation, tries every possible combination, utilizing trial and error in attempt to gain unauthorized access.

__2) Dictionary Attack__: The attacker uses a word list containing common words, phrases, and or passwords found in credential dumps. Credential dumps (or password dumps) are passwrods that have been leaked from previously successful data breaches. These attacks have a higher success rate due to the fact the people often reuse the same password across multiple different sites and platforms.

__3) Credential Stuffing__: An attacker will use the stolen username-password pairs from credential dumps and attempt to access  multiple accounts across different platforms. Again, this relies on users reusing passwords across sites.

#### How to Defend Yourself Against Brute Force Attacks
__Use longer passwords or pass phrases.__ The longer the password, the longer it will take for an attacker to crack. In general, 15+ characters utilizing uppercase and lowercase letters, numbers, and special characters is best practice. To save yourself from having to remember all of the unique passwords across sites and platforms, a password manager can help.

A password manager is a tool that securely stores and manages passwords for various online accounts. It helps users generate, store, and retrieve strong, unique passwords for each account, reducing the risk of password reuse or weak passwords. Password managers typically encrypt stored passwords and require a single master password to access them, streamlining security and convenience.

A passphrase is a longer and more complex form of a password, consisting of a sequence of words or characters, often forming a meaningful or memorable phrase. 

__Enable multifactor authenication (MFA)__ when available. MFA requires users to provide two or more verification factors to confirm their identity when accessing a system, account, or application. This can be accomplished through text message, email, or authenticator apps. The factors fall into three categories:

  **1)** Something you know (e.g., a password or PIN).

  **2)** Something you have (e.g., a smartphone, hardware token, or security key).

  **3)** Something you are (e.g., a fingerprint, facial recognition, or voice).

By requiring multiple factors, MFA significantly enhances security, making it harder for attackers to gain access, even if one factor (like a password) is compromised.

__Stay vigilant.__ Shift your mindset to question everything. Don't trust emails or text messages asking you to login or track a package that you did not order. You can check [have i been pwned](https://haveibeenpwned.com/) to see if your email has been compromised in any data breaches and even sign up for alerts that will notify you if it is involved in any future breaches.

Brute force attacks are a persistent threat, but understanding methods and implementing strong defenses can significantly reduce risk. Companies should always have a strong understanding of their attack surface and be aware of what assets or services are exposed to the internet. By using long, unique passwords or passphrases, utilizing password managers, enabling multifactor authentication, and staying vigilant, you can protect yourself from unauthorized access. Your security is a great responsibility, and adopting proactive measures helps safeguard not only your data but also the broader digital ecosystem. Stay informed, stay secure, and keep your defenses strong.

## Day 12: Ubuntu Server Install
Today's objective is to setup a cloud-based SSH server and learn how to review authentication logs in real time.

To begin, I deployed a server within my existing Vultr VPC network. I have walked through that process step by step in day(s) X/Y and will not rehash them here. The only thing to note is that an Ubuntu 24.04 LTS x64 image was used.

Once the server was up and running, I established an SSH connection and updated the respositiories by running `apt-get update && apt-get upgrade -y`.

Since I am interested in authentication logs, I navigated to the `/var/log` directory and used `ls` to see what was available with that diretory. There are sereral log files within the directory. The one to focus on is the `auth.log` file, which contains all of the authentication related activity.

<img width="946" alt="day12-2" src="https://github.com/user-attachments/assets/359a832d-e2a2-4be7-8098-913e9b766212">

To see what the file contains, I ran `cat auth.log`. 

<img width="947" alt="day12-3" src="https://github.com/user-attachments/assets/b17d46ba-166c-4656-9936-26f72098a080">

The above image doesn't show much interesting activity yet because the server was just deployed. After waiting for only 15 minutes, I started to see a different picture. 

<img width="943" alt="day12-4" src="https://github.com/user-attachments/assets/42230ed7-8ee4-449f-bd37-aa965c1f0810">

In this image, we can see some unauthorized attempts to login taking place. Key words like *failed password, invalid user, authentication failure, and connection closed* should stand out.

Next, I ran `grep -i failed auth.log` to filter the auth.log file for any entries that contain the word "failed".

<img width="944" alt="day12-5" src="https://github.com/user-attachments/assets/ee7a50b8-00ba-42d1-8827-0f5a12e24bd6">

I narrowed down my search further with `grep -i failed auth.log | grep -i root` to only include entries where someone tried to login as root. 

<img width="949" alt="day12-6" src="https://github.com/user-attachments/assets/0ffa77b8-2f9f-4e56-920d-4ad1835b9d2b">

To filter the results to see only the IP address from which these failed authentication attempts by root came from, I ran `grep -i failed auth.log | grep -i root | cut -d ‘ ‘ -f 9`.

<img width="947" alt="day12-7" src="https://github.com/user-attachments/assets/6af6d47f-d87d-476c-bcc6-48716340fdcd">

Let's break down this command.
1) `grep -i failed auth.log`
   * __Purpose:__ Searches the file `auth.log` for lines containing the word "failed".
   * `-i`: Makes the search case-insensitive (matches "Failed", "FAILED", etc.).
   * __Output:__ Displays only the lines in auth.log that include "failed" in any case.
2) `|` (Pipe)
   * Connects the output of the first `grep` command to the next command.
   * The output of `grep -i failed auth.log` becomes the input for the next `grep`.
3) `grep -i root`
   * __Purpose:__ Filters the lines passed from the first `grep` to find only those containing the word "root".
   * `-i`: Again, makes the search case-insensitive.
   * __Output:__ Displays lines containing both "failed" (from the previous step) and "root".
 4) `|` (Pipe)
    * Passes the filtered output (lines containing both "failed" and "root") to the next command.
 5) `cut -d ' ' -f 9`
    * __Purpose:__ Extracts the 9th field from each line, where fields are separated by spaces.
    * `-d ' '`: Specifies the delimiter<sup>*</sup> as a space character.
    * `-f 9`: Specifies the field to extract (the 9th one).
   
<sup>* </sup>A __delimiter__ is a character or sequence of characters used to separate or define boundaries between distinct pieces of data in a file, string, or dataset. It helps identify where one part of the data ends and the next begins.* 

Monitoring authentication attempts in real time is crucial for maintaining the security and stability of a server. Real-time monitoring of authentication attempts is essential for detecting unauthorized access, unusual activity, and brute force attacks, enabling quick responses to mitigate potential threats. It helps protect sensitive data, ensures compliance with security regulations, and prevents system compromise by identifying and blocking malicious activity. Additionally, it provides valuable insights into both malicious behavior and legitimate user issues, enhancing overall server security and reliability. By monitoring authentication attempts in real time, organizations can reduce the attack surface, improve response times, and maintain a secure environment for their servers and data.

## Day 13: Installing Elastic Agent
The goal for today is to install Elastic Agent onto the Linux Ubuntu server that was deployed yesterday. This will provide the ability to query the server's logs in my Elasticsearch instance. 

Elastic Agent is a unified data collection agent developed by Elastic. It simplifies the process of ingesting and managing data across different sources in the Elastic Stack. In the SOC context, Elastic Agent plays a critical role in:
* Collecting endpoint telemetry for threat detection and investigation.
* Enabling rapid deployment of threat detection rules and monitoring policies.
* Providing insights via Kibana dashboards for centralized visibility.

By adopting Elastic Agent, organizations can achieve a more cohesive and efficient approach to managing their data ingestion and endpoint security.

Elasticsearch is the core component of the Elastic Stack (ELK Stack) and powers the system's ability to store, search, and analyze data efficiently. In the context of cybersecurity and SOCs:
* Incident Investigation: Enables fast querying of logs and events to uncover potential threats.
* Threat Detection: Powers rule-based and machine learning-based threat detection.
* Centralized Visibility: Facilitates data integration from various security tools for a unified view.

Elasticsearch’s ability to handle massive amounts of data efficiently makes it a cornerstone of modern monitoring and analytics workflows.

#### The Install
To begin, I logged into the Elastic web GUI, clicked the hamburger icon, and navigated to `management > fleet > agent policies > create new policy`.

<img width="961" alt="day13-1" src="https://github.com/user-attachments/assets/8b3ae81b-e144-49ba-b474-db38c4360785">

After naming and creating the agent policy, I navigated into that policy and selected `system-3` to see the default log collection settings and what kind of logs this policy is instructing the Ubuntu server to push to my Elasticsearch instance. It points to the path `/var/log/auth.log` and `/var/log/secure`.

<img width="945" alt="day13-3" src="https://github.com/user-attachments/assets/b88f99a2-a8aa-445c-92a3-5a17e830e88f">

Hopping over to an SSH session on the Ubuntu server and listing out the contents of the `var/log` directory, the `auth.log` exists but, there isn't anything named `secure`. 

<img width="944" alt="day13-4" src="https://github.com/user-attachments/assets/35fe04ec-5fb0-4736-87eb-4db1d3732abd">

A bit of sleuthing shows that both `/var/log/auth.log` or `/var/log/secure` keep authentication logs for both successful or failed logins, and authentication processes and that storage depends on system type. If you're running Debian or Ubuntu, logs are stored in `/var/log/auth.log`. If you're using Redhat or CentOS, you'll find the logs stored in `/var/log/secure`.

Next, I navigated to `View All Agent Policies > Agents > Add Agent`, selected the proper policy to monitor, selected `Enroll in Fleet`, and copied the command provided for the Linux Tar OS.

<img width="953" alt="day13-5" src="https://github.com/user-attachments/assets/645d5252-2496-4ae0-a5e9-df0368df7ff3">

<img width="953" alt="day13-5a" src="https://github.com/user-attachments/assets/191a7171-b8e9-47bd-9f0c-f1c24543f886">

<img width="952" alt="day13-5b" src="https://github.com/user-attachments/assets/d0a11d80-8132-47b0-9175-cb2df17847c2">

I pasted that command into my SSH session with the server, making sure to change into the home directory first, and the Elastic Agent installed. However, an `Error: fail to enroll: fail to execute request to fleet server: x509: certificate signed by unknown authority` message arose.

In this case, I am using a self-signed certificate. A self-signed certificate is a type of digital certificate that is signed by the entity that it certifies, rather than by a trusted Certificate Authority (CA). These certificates are typically used for internal or development purposes where the cost or need for a certificate issued by a trusted CA is unnecessary.

<img width="947" alt="day13-6" src="https://github.com/user-attachments/assets/bd5164f3-28e2-462d-ab5e-aa2bdf074341">

To remedy this, I appended a `--insecure` to the command, ran it again, and the agent was successfully installed.

<img width="946" alt="day13-7" src="https://github.com/user-attachments/assets/c089b810-9e98-40e1-833f-1a813992625b">

<img width="947" alt="day13-8" src="https://github.com/user-attachments/assets/00e65519-6106-4ecb-a167-36e3f075afc9">

This is confirmed with the Elastic web GUI.

<img width="953" alt="day13-8a" src="https://github.com/user-attachments/assets/fbeca93c-86fb-411a-a090-573dcc0924c6">

Next, clicking on the hamburger icon and navigating to `Discover` the agent also appears now.
<img width="961" alt="day13-8b" src="https://github.com/user-attachments/assets/1c5ad9b6-3ccb-4cd6-8632-483b0329f999">

By clicking the plus (+) button there, it applies a filter to show logs tht contain only this agent name.

<img width="961" alt="day13-8c" src="https://github.com/user-attachments/assets/13929ad8-b0c6-4199-b680-5d4bb7482aad">

Yesterday, I ran `grep -i failed auth.log | grep -i root | cut -d ‘ ‘ -f 9` identified one particular IP address that had failed to login to the root account.

 <img width="948" alt="day13-9" src="https://github.com/user-attachments/assets/610b2ab8-b42b-4dd9-91ff-a94f919e8837">

 Searching for that in Elastic, I found some results.
 
<img width="959" alt="day13-9a" src="https://github.com/user-attachments/assets/07c1afdb-806d-4927-b9d6-c903892dd1cc">

Adding the keyword "authentication failure" I narrow the results down even further.

<img width="957" alt="day13-9b" src="https://github.com/user-attachments/assets/5f03bfe1-c24b-4723-9837-89f2a042b9a7">

Viewing the details of one of these resutls, the message reads "pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=148.153.158.114  user=root". To add this information into the results table, I clicked the second icon above the highlighted message field, "toggle column in table", making the information easier to digest.

<img width="960" alt="day13-9c" src="https://github.com/user-attachments/assets/6338dd10-fda4-4ae2-924f-98d6a99d4722">

<img width="962" alt="day13-9d" src="https://github.com/user-attachments/assets/c194535f-63e0-4bab-a093-d02c7d181544">

Now that the Elastic Agent is installed on the SSH server, I can begin looking for interesting events with the Elasticsearch instance.

## Day 14: Creating Alerts and Dashboards in Kibana (1/4)
Dashboards are crucial in a Security Operations Center (SOC) because they provide real-time visibility into security events, enabling analysts to quickly identify threats, monitor trends, and assess system health. They centralize data from various sources into an intuitive visual format, facilitating faster decision-making and efficient incident response.

I will create an SSH brute force alert and associated dashboard to visualize where the attacks are coming from.

#### Identifying Brute Force Attempts
To begin, I filtered my Elasticsearch instance for the `agent.name` `MYDFIR-Linux-jf`, my SSH server, which revealed 5000+ events for that day.
<img width="963" alt="day14-1a" src="https://github.com/user-attachments/assets/1f7ab5d9-d5a8-4dd1-9a87-c60d6d8dcf0d">

One of the first things to look for in a brute force attack is failed authentications because every possible combination is being tried to gain unauthorized access. Searching for the keyword "failed" my search results narrow to 37. However, looking at the message, it doesn't seem to be relevant to failed authentication attempts.
<img width="960" alt="day14-2" src="https://github.com/user-attachments/assets/d620accb-6cd0-4cf9-8d12-fd9dd6352ff5">

Searching the list of available fields on the left hand side of the page, `system.auth` is an option under `event.dataset`. Applying that as the filter leaves me 786 events to work with. 
<img width="959" alt="day14-2a" src="https://github.com/user-attachments/assets/1fc99415-b2e6-41d1-b700-d7deb25fccf4">

Looking at the details of one of the events, I can see that it is coming from the `/var/log/auth.log` filepath that we learned about previously.
<img width="961" alt="day14-2d" src="https://github.com/user-attachments/assets/e44ae5a7-d80a-47eb-bd98-90ffbb999dcd">

This is closer to what I'm looking for but, I think there is better information out there. Further review of the available fields revealed a `system.auth.ssh.event` field, with "failed", "invalid", and "accepted" as the top values. I added this as a column in my search results table.
<img width="959" alt="day14-3" src="https://github.com/user-attachments/assets/37e9e6fb-2f10-4a06-9369-4f7e54b78589">

However, the column populated with only a `-` so I searched for `system.auth.ssh.event: *` to ensure to return only results with data in the `system.auth.ssh.event` field.
<img width="959" alt="day14-3a" src="https://github.com/user-attachments/assets/833f8af4-1319-442b-b752-559428ad6d02">
<img width="959" alt="day14-3b" src="https://github.com/user-attachments/assets/cb2b9229-35b4-4691-97e7-28c8201e3dac">

Moving forward, I added `user.name` as a column along with `source.ip` and `source.geo.country_name`.
<img width="961" alt="day14-4" src="https://github.com/user-attachments/assets/3090424e-f9d6-4655-8719-d63cc40d1d0a">

Finally, I filtered for "failed' to show only failed attempts narrowing the search down to just 169 events.
<img width="959" alt="day14-6" src="https://github.com/user-attachments/assets/137d3502-004c-4fb7-9082-c720d2f1fbce">

After saving these search parameters search, I am ready to create a new alert.

#### Creating a Custom Alert
Located under `Alerts` in the top right hand corner of the screen, I chose `Create search threshold rule`.
<img width="961" alt="day14-6a" src="https://github.com/user-attachments/assets/79f01acf-34d3-4eca-9432-b96be613878d">

After naming the rule, I can see that my search parameters have automatically populated in the `Define your query` section.
<img width="957" alt="day14-6c" src="https://github.com/user-attachments/assets/4bc81a9f-38ec-430b-adf3-35e6d87e57c1">

I set the thresholds for the alert.
<img width="963" alt="day14-6d" src="https://github.com/user-attachments/assets/3a4688cf-7658-4e32-befd-f83ccf496d8e">

Tested the query and saved.
<img width="958" alt="day14-6e" src="https://github.com/user-attachments/assets/338308fa-900a-4648-905d-3df5855f498e">

This is not the best rule for an alert but rather just the beginning of learning how to build and tune custom rules within a SOC environment. Later, I will further tune this rule to take action when triggered.

#### Creating a Dashboard
Under the hamburger menu, I navigated to `Analytics > Maps`. Once there, I enetered my previously defines search parameters into the search bar and clicked `Add layer`.
<img width="960" alt="day14-8" src="https://github.com/user-attachments/assets/0b756757-f35b-4c98-8810-90a9ac7cbbad">

One of my goals here is to pinpoint activites based on geolocation derived from the source IP address. The Chloropleth option will shade areas to compare statisitcs across boundaries.
<img width="961" alt="day14-8b" src="https://github.com/user-attachments/assets/b0e829b0-53f3-461d-aca6-4574f68afc1d">

For the `Boudaries source`, `Administrative Boundaries > World Countries` was applied. The same `Data view` used for my custom search parameters was applied as the `Statistics source`. `source.geo.country_iso_code` was applied as the `Join field`. 
<img width="952" alt="day14-8c" src="https://github.com/user-attachments/assets/25a5b0e3-6504-4526-91d4-026598f5fa3b">

Other settings were left as default. After saving and naming the map, it needs to be added to a dashboard. No dashboards exist yet, so `New` was chosen.
<img width="959" alt="day14-8d" src="https://github.com/user-attachments/assets/bd7f9dc3-e7ed-44dc-a085-63f7fc4dfce2">
<img width="963" alt="day14-8e" src="https://github.com/user-attachments/assets/85c7df2b-356d-49dd-8c43-6ae4dfe77635">

To create an SSH Successful Authentications visulaization, I duplicated my first visulaization, renamed it, and adjusted the query parameters to `system.auth.ssh.event: * and agent.name: MYDFIR-Linux-jf and system.auth.ssh.event: Accepted`. As we see, only my successful authentications from Japan are shown.
<img width="965" alt="day14-9" src="https://github.com/user-attachments/assets/fa26c0a8-773a-4e34-9d59-8c304d9d3ff0">

Now, next time brute force activity occurs, I can be alerted and easily view where the activity is coming from. However, like custom alerts, this is just the beginning of be able to create custom dashboards and visualizations that help analysts and decision makers easily ingest and comprehend the relevant data key to keeping an organization safe from unauthorized access. 

Custom alerts for unauthorized SSH connections and associated dashboard visualizations are vital for detecting potential breaches and mitigating risks promptly. They enable real-time monitoring, quick identification of suspicious activity, and actionable insights, helping protect sensitive systems and data from unauthorized access.

## Day 15: Remote Desktop Protocol (RDP)
Remote Desktop Protocol (RDP) is one of the most commonly abused protocols. According to this [source](https://www.sophos.com/en-us/press/press-releases/2024/04/cybercriminals-abuse-remote-desktop-protocol-rdp-90-attacks-handled), it was present in 90% of ransomware attacks in the year 2023.

#### What is RDP? 
RDP is a protocol, or a set of agreed upon rules on which to operate, that is used for communication between a terminal server and a terminal server client. It operates within TCP and defaults to port 3389. It allows authorized users to connect remotely to another machine.

Read more about it from Microsoft [here](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol).

#### Why is it used? 
RDP is accessible, convenient, and saves time. It allows users to remotely connect to endpoints to work or troubleshoot problems without have to be in the same physical location. However, these upsides of RDP are accompanied by risk.

#### How do malicious actors abuse it? 
RDP is one way an attacker can gain initial access to a target network. Servers will often have the RDP service open and exposed to the internet if the organization has not tightened up its security configurations. This means that it's possible for anyone from the internet to try and connect to that server. This can be done through brute force or with stolen credentials. Once this initial access is gained, depending on the level of privileges, the attacker can now do things like steal more data or move to other areas of the network.

#### How can you identify endpoints with RDP exposed to the internet? 
[Shodan](https://www.shodan.io/) (*account required*)identifies servers with RDP open to the internet by scanning IP addresses across the internet and checking for specific ports associated with RDP. When Shodan scans an IP, it probes common ports for services and identifies the service banners or response patterns that indicate RDP is running. It then indexes these findings, which allows users to search for devices with RDP exposed publicly. Shodan essentially maps the global internet to highlight systems with potentially risky configurations like open RDP ports.

[Censys](https://censys.com/) is a search engine that scans the internet to collect and index data about devices and services exposed to the web. Similar to Shodan, Censys continuously scans the global internet to discover servers, services, and their vulnerabilities. Censys provides tools to search and analyze exposed systems by filtering based on factors like service types (e.g., HTTP, HTTPS, RDP), geographical location, or security issues. It’s widely used by security researchers, network administrators, and organizations for monitoring, threat intelligence, and vulnerability assessments.

#### How can you protect yourself?
+ __Turn off RDP__ - if it's not needed, don't expose yourself to unecessary risk.
+ __Use MFA__ - this provides an initial layer of security.
+ __Restrict access__ - implement firewall rules or put servers behind a VPN.
+ __Have strong password policies__ - requiring passwords with 15+ characters containing upper and lowercase letter, numbers, and special characters is best.
+ __Utilize a Privileged Access Management Tool (PAM)__ - these generally work by centralizing access control, enforcing the principle of least privilege, provide session recording and monitoring, offer password vaulting and rotation, and assist with audits and reporting responsibilities. 
+ __Don't use default accounts__ - change the local default acounts that get created and setup a different administrator account with a unique name, if needed.

While RDP offers convenience and flexibility for remote work, its exposure to the internet creates significant security risks. Attackers often exploit weak or default credentials to gain unauthorized access, leading to data breaches, ransomware attacks, and network compromise. To mitigate these risks, it’s crucial to disable RDP when not needed, implement strong access controls, use multi-factor authentication, and adopt tools like PAM for better credential management. By taking these proactive measures, you can significantly reduce the chances of RDP becoming a vector for malicious activity.
