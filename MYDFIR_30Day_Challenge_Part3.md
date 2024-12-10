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

