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
To initiate the attack, I will utilize a Kali Linux machine. Kali Linux comes preloaded with a vast array of tools for various cybersecurity tasks, including tools for network analysis, web application testing, password cracking, wireless testing, reverse engineering, and more. 

There are several preexisting wordlists within Kali Linux found in `/usr/share/wordlists/`. This attack will utilize the rockyou.txt file. To unzip the file, run `sudo gunzip rockyou.txt.gz` and enter the default password `kali`.
<img width="954" alt="day21-3a" src="https://github.com/user-attachments/assets/87413db2-acee-4cda-bff1-94716d60a623" />

Running `cat rockyou.txt` will show all the list of commonly used passwords contained in the file. I only want to use the first 50 passwords listed in the file. To do this, I need to create a separate list, which will be in a file titled mydfir-wordlist.txt, by running `head -50 rockyou.txt > /home/kali/mydfir-wordlist.txt`. This command works by listing out the first 50 passwords in the `rockyou.txt` file and outputting those into a new file called `mydfir-wordlist.txt`.
<img width="959" alt="day21-3b" src="https://github.com/user-attachments/assets/6b57f7bb-4b06-4838-b48a-feb039798b93" />

Now, it's necessary to include honeypot2024! in the `mydfir-wordlist.txt` file. To do so, run `nano mydfir-wordlist.txt` to open the file in the Nano text editor. [Here](https://youtu.be/g2PU--TctAM) is a short video on the basics of using Nano.
<img width="959" alt="day21-3c" src="https://github.com/user-attachments/assets/9a128a53-159b-4cdd-b1b6-763144712c72" />

A tool called [Crowbar](https://github.com/galkan/crowbar) is going to do the brute forcing. After updating and upgrading the system repositories, to download the tool, run `sudo apt-get install -y crowbar`. 
