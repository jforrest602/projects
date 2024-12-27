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

First, I need to set up an agent within my Mythic instance to use as a payload. Working from my Mythic server, I downloaded and installed the [Apollo Mythic agent](https://github.com/MythicAgents/Apollo.git) from GitHub by running `./mythic-cli install github https://github.com/MythicAgents/Apollo.git`. 
<img width="961" alt="day21-7a" src="https://github.com/user-attachments/assets/033358e1-99e0-4f21-9d84-55fc656e88da" />

This [Agent Capabilities Matrix](https://mythicmeta.github.io/overview/agent_matrix.html) is a useful resource for determining which agent is good dependent upon operational conditions.

After a few moments, I can see from PowerShell and my Mythic web GUI that Apollo has been successfully installed.
<img width="948" alt="day21-7b" src="https://github.com/user-attachments/assets/704efa94-868b-4b59-9e85-873381aeee1a" />
<img width="959" alt="day21-7c" src="https://github.com/user-attachments/assets/b29f3454-5bca-4604-8069-dd039d81f3ca" />


