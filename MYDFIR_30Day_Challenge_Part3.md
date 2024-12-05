*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge (Part 3)

<img width="896" alt="Part 3 header" src="https://github.com/user-attachments/assets/17f0ae1b-4141-4628-8309-1db3531a52c5">

## Day 11: Understanding Brute Force Attacks
The goals for today are threefold:
* Understand what a brute force attack is.
* Learn about common tools used to perform the attack.
* Learn how to protect yourself from a brute force attack.

#### What is a brute force attack?
A brute force attack is a method used to gain unauthorized access to a system, account, or encrypted data by systematically trying all possible combinations of passwords, encryption keys, or credentials until the correct one is found. This attack method relies on computing power and persistence rather than exploiting vulnerabilities.

Unfortunately, brute force methods are accessible and easy to use, making it a common attack method. Servers all around the world have services running that are exposed to the internet, like remote desktop (RDP), allowing anyone from the internet to connect and attempt to login using credentials found in password dumps, malware, or social engineering techniques. 

#### 3 Common Brute Force Attacks
* Simple Brute Force: The attacker, relying on automation, tries every possible combination, utilizing trial and error in attempt to gain unauthorized access.
* Dictionary Attack: The attacker uses a word list containing common words, phrases, and or passwords found in credential dumps. Credential dumps (or password dumps) are passwrods that have been leaked from previously successful data breaches. These attacks have a higher success rate due to the fact the people often reuse the same password across multiple different sites and platforms.
* Credential Stuffing: An attacker will use the stolen username-password pairs from credential dumps and attempt to access  multiple accounts across different platforms. Again, this relies on users reusing passwords across sites.
