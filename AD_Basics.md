# Active Directory Basics 

[Microsoft Active Directory Domain Services](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (AD DS) "provides the methods for storing directory data and making this data available to network users and administrators." It is "a hierarchical structure that stores information about objects on the network." Objects are single elements like users, groups,shared folders, computers, or printers. AD stores object details and enables authorized users to access that information. Active Directory also provides authentication and authorization services within a Windows domain environment.  

According to [HTB](https://academy.hackthebox.com/module/74/section/699), "around 95% of Fortune 500 companies run Active Directory." If you're planning to join the IT or InfoSec fields, familiarizing yourself with AD is a must. Not only because it is so widespread but it is inherently insecure (primarily due to misconfigurations) making it a high priority target for any intruder looking to access and or move around a network.  

![AD ninja](https://github.com/user-attachments/assets/5f0f9086-44a4-438f-a4ad-15fe0d29c0d4)
     
This introductory guided lab was completed as part of the HTB [Introduction to Active Directory](https://academy.hackthebox.com/module/details/74) module which introduces AD basics. The goal of this lab was to demonstrate the skills needed to perform basic AD administrative tasks. In this case, through the Windows graphical user interface (GUI).   

Most, if not all, of the tasks performed in this demonstration can also be performed via the command line/PowerShell.

## What's the scenario?
![image](https://github.com/user-attachments/assets/ca863dbf-3300-4b95-8e7e-526763d71629)
    
To get started, connect to the lab's host using the Remote Desktop Protocol (RDP).  
  
![image](https://github.com/user-attachments/assets/c0350f3d-735a-4fed-ba5f-15c748acd1fa)
  
Once connected to the host, access Windows Administrative Tools by utilizing the search function on the taskbar.       
     
![image](https://github.com/user-attachments/assets/e500e6a1-0e28-4e4e-941f-1ebd5206537b)
  

### TASK 1: User Management - Add New Users
The first task of the lab is to create three new users, something that an AD admin will need to do each time the organization onboards new employees. To do this via the GUI, access AD Users and Computers via Windows Administrative Tools.  
  
![image](https://github.com/user-attachments/assets/40526871-21f5-468d-9ee7-5d94b7a1a975)

Navigate to the proper location, in this case `INLANEFREIGHT.LOCAL` domain and drill down to the `Corp > Employees > HQ-NYC > IT` folder. Right click and select `New > User`.    
    
![image](https://github.com/user-attachments/assets/55fdb3f0-d758-47da-9742-503716dd362b)
  
Once the dialog box opens up, enter the new employee information. In this case, onl first and last name, email address, display name are required, as well as a temporary password. Enable the `User must change password at next logon` feature. 

In AD, object [attributes](https://learn.microsoft.com/en-us/windows/win32/adschema/attributes) are data that define the properties of objects. There are many different attributes an object can have but, here they include name, email address, and display name. Attributes can fall into several different types of class but, that is beyond the scope of this project.  

Repeat this procedure for the additional two new users.  
  
![image](https://github.com/user-attachments/assets/c62c991d-fe50-4ae6-bd59-0111eeca38df)
  
![image](https://github.com/user-attachments/assets/ad314262-fcc7-4ae8-9a51-39fb804b6ac1)
  
![image](https://github.com/user-attachments/assets/4ab803a4-96da-42a5-90ae-edd854f7381b)
  

### TASK 2: User Managment - Remove Users  
  
Of course, employees (users) leave organizations too. When they do leave, AD admins will need to remove the users or reconfigure the user's rights and permissions.  

In this instance, we only have first and last names to work with so, utilize the search function to locate the user profiles.  
  
![image](https://github.com/user-attachments/assets/f6ce2483-13cb-448a-a847-c799382417ba)
  
Once located, right click and select `Delete` and click `Yes`.  
  
![image](https://github.com/user-attachments/assets/586d915f-8efa-46d9-a2be-f89983d054dd)  
  
![image](https://github.com/user-attachments/assets/b9f794b2-8c7c-4cad-b9fb-47cee6dcb993)
  
Repeat the process to remove the second user.  
  
### TASK 3: User Management - Unlock Account and Forcie Password Change  

Within [Identity and Access Management](https://www.microsoft.com/en-us/security/business/security-101/what-is-identity-access-management-iam) (IAM), best practice includes setting a limit to the maximum number of login attempts before an account is locked. It helps [protect against brute force attacks](https://owasp.org/www-community/controls/Blocking_Brute_Force_Attacks).   
  
You may have experienced this before if you have ever forgotten a password.   
  
This task requires unlocking a user account and forcing the user to change their password upon their next logon. This helps protect both the user and the organization in case the user credential were in fact compromised rather than just forgotten. 

Locate the user via the search function. Right click and select `Reset Password` on the pop-up menu.  
  
![image](https://github.com/user-attachments/assets/0a9bc2f3-02e2-4e7f-8ca5-1214c79aef6e)
  
Once there, make sure that the `Unlock Account` and `User must change password at next logon` options are selected and create a temporary password for the user.  
  
![image](https://github.com/user-attachments/assets/51b6ee6d-36ef-427e-be7a-f1980850a569)
    
![image](https://github.com/user-attachments/assets/0eab7e60-80be-47e9-b3df-a75bc13dffa2)
  
It is useful to note here, that this is just one of several ways that this task can be accomplished through the GUI.  

### TASK 4: Manage Groups and Organizational Units - Create a New OU & Security Group  

The first thing to do within this task is to create a new Analysts organizational unit (OU) under IT. Withing AD,  [organizational units](https://learn.microsoft.com/en-us/entra/identity/domain-services/create-ou) allow you to logically group objects together and apply group policies to enforce configuration settings, ultimately making your job as administrator a bit easier.   

Staying within Active Directory Users and Computers navigate again to  `Corp > Employees > HQ-NYC > IT` folder under the `INLANEFREIGHT.LOCAL` domain. Right click and select `New > Organizational Unit`.
  
![image](https://github.com/user-attachments/assets/69e13475-40dc-4e8b-809e-c9b9bac1328f)
  
![image](https://github.com/user-attachments/assets/4bb728f4-57b6-4324-b1a7-98ff306e182f)   
    
Next, create a new Security Group within the new Analysts OU following similar steps. Right click on `Analysts`. Select `New > Group`.     
    
![image](https://github.com/user-attachments/assets/a7fd2d88-2339-413a-bee6-bc47c9baa1cd)
  
![image](https://github.com/user-attachments/assets/992dbdf5-05b4-4260-83b1-a33d4d15a401)
    
Confirm that the group was sucessfully created.  
    
![image](https://github.com/user-attachments/assets/3c1ac9b1-c2c1-4dd9-b08e-bdd5f2aebd12)
    
To add the new employees to this security group, select the employess to move and utilized the `Adds selected objects to group you specify` button located on the toolbar.     
  
![image](https://github.com/user-attachments/assets/03b67bbb-1264-4f5f-8f91-35ecb6e9e1bb)
  
![image](https://github.com/user-attachments/assets/bce9dc51-57d0-4057-bde7-818ae713860e)
  
![image](https://github.com/user-attachments/assets/d6dffd12-746d-4509-82b4-8fa350f4f338)  

Confirm that the users were added to the group by checking the group properties.  
  
![image](https://github.com/user-attachments/assets/f8ce50fc-6bed-40b3-8c04-255ee31c3f0c)     
  
### TASK 5: Manage Group Policy Objects  
  
Within AD, [group policy](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-policy/group-policy-overview) is used to define configurations for groups of users, client computers, and to help manage servers. Group policy settings are stored in a Group Policy Object (GPO). GPOs are essentially a collection of configurations (policy settings, security permissions, etc.) that are applied to a group (user, computers, servers, etc.).   
  
####  Deny access to removable storage devices  
  
Navigate to the `Group Policy Management` function within the `Administrative Tools`.    
  
![image](https://github.com/user-attachments/assets/fdf1f3b9-4352-42d3-9cd6-2c7bfd5a4c76)  

To modify removable media policy settings, navigate to: `User Configuration > Policies > Administrative Templates > System > Removable Storage Access`.  
  
![image](https://github.com/user-attachments/assets/8e0a0976-b205-4820-b733-5ab98f6770b7)
  
Right click the policy object and select `Edit` from the pop-up menu.  

Enable the `Deny all access` option and `Apply`.  
  
![image](https://github.com/user-attachments/assets/1d7f3743-df27-4ec1-aca3-5ca2e86beb6a)
  
![image](https://github.com/user-attachments/assets/69e4f8ef-8d02-4aa0-9e2b-407645b5cbb6)   
  
#### Allow Access to CMD.exe  
  
Navigate to: `User Configuration > Policies > Administrative Templates > System`. Select `Prevent access to the command prompt` and click `Edit policy settings`.  
  
![image](https://github.com/user-attachments/assets/39f4d531-3f19-4d37-a1ed-d548de7c2775)  
  
Select `Disabled` and `Apply`.  
  
![image](https://github.com/user-attachments/assets/475bd3bc-54ca-48d7-a4a0-4659d6311e18)
  
![image](https://github.com/user-attachments/assets/ef174f49-33bd-4dd3-9f43-1493795ecc9e)     
  
#### Duplicate Logon Banner Policy  
  
Edit the Logon Banner policy by right clicking the policy object and selecting `Edit` from the pop-up menu.    
  
![image](https://github.com/user-attachments/assets/417e7f43-0881-493c-8d59-86831653c9e6)   
  
Navigate to: `Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options`.   
Right click on Interactive logon: Message text for users attemptoing to logon and select Properties from the pop-up menu. Setting is enabled.
![image](https://github.com/user-attachments/assets/dff49c54-e3fb-4f73-a995-50772a9e7fcc)  
![image](https://github.com/user-attachments/assets/9dff404e-6b39-4ac6-ae4d-d367abc2ab5b)

Right click on Interactive logon: Message totle for users attempting to log on, select Properties, and confirm policy is enabled.
![image](https://github.com/user-attachments/assets/d9ce17cd-da95-4c07-b8f9-5d62bd4997a2)  

![image](https://github.com/user-attachments/assets/90a5481e-3516-4748-bfcf-94b7272d9d43)
  


##### Ensure password policy settings for Security Analyst group are strong  
Within the Group Policy Management editor, navigate to: Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy.

![image](https://github.com/user-attachments/assets/139d3256-2010-485e-b7b9-966dcccd8d56)  
Right click each policy setting and click Properties.
![image](https://github.com/user-attachments/assets/38bde3f3-7a9f-43fc-a1a7-a8c555b25b42)
Define policy settings and apply.
![image](https://github.com/user-attachments/assets/c0dc460a-86b9-479c-883b-f1272bf7008c)  

![image](https://github.com/user-attachments/assets/c3a6b75a-ff78-4d4c-b98f-fc88bae2e4a8)  

![image](https://github.com/user-attachments/assets/68c03c59-f60d-4a2d-8852-f460b5c95593)  

#### TASK 6: Add & Remove Computers to the Domain
In order to add a remote computer to the localhost domain, you first have to RDP to that computer. Do this by opening the Remote Desktop Connection application.
![image](https://github.com/user-attachments/assets/38be997e-a5ca-468d-9633-0379bf5cffdb)  
Enter the name of the computer you wih to connect to and enter your credentials.
![image](https://github.com/user-attachments/assets/f52b69a3-b72b-4964-8607-c02b1a7fcfcf)
![image](https://github.com/user-attachments/assets/cb63fc41-5a9c-4e46-8768-c7370870158a)  
Once connected, open up the computers Control Panel and navigate to: System and Security > System > Change settings.
![image](https://github.com/user-attachments/assets/5699323a-a4d3-4d4c-9d5e-93a869df4317)  
![image](https://github.com/user-attachments/assets/3f154f40-2ee3-4387-90d2-90fbd9b216de)  
![image](https://github.com/user-attachments/assets/1a6c7fcb-1eff-416a-aa37-b665b5746f12) 
![image](https://github.com/user-attachments/assets/363573b7-b73d-45ea-9c28-aee9aeda9425)  
After the dialog box opens click Change next to To rename this computer or change its domain or workgroup, click change.
 ![image](https://github.com/user-attachments/assets/ef2b90dc-e9a0-461c-857a-83f7fad65e43)  
 
Select Domain and enter domain name.
![image](https://github.com/user-attachments/assets/60af2cfb-e473-4880-9233-87ee66287bec) 
Enter authorized user credentials and click OK three times.
![image](https://github.com/user-attachments/assets/c46cfd0b-40bb-4b03-a1c9-a6b1dc9bc3d6) 
![image](https://github.com/user-attachments/assets/6898272f-cd9c-40d8-948d-c7cb4d50d280)
![image](https://github.com/user-attachments/assets/87035151-ece2-4658-b048-23f5d19bce1f) 
![image](https://github.com/user-attachments/assets/adfa936b-c633-4fcf-90ea-c85204964ea4)


#### TASK 7: Move the Computer Object to a New OU
Open the Windows Administrative Management tool. Navigate to Active Directory Users and COmputers > INLANEFREIGHT.LOCAL > Computers, right click on the computer you want to move and select Move... from the pop-up menu.

![image](https://github.com/user-attachments/assets/3457c83f-7950-4eb5-bad4-b53f60f60dfb)
![image](https://github.com/user-attachments/assets/cea43b40-2197-4c02-8141-c6f548641a8e)
![image](https://github.com/user-attachments/assets/4d9876f3-b137-46e7-b239-c697893bfb81)
Select the container into which you want to move the object.
![image](https://github.com/user-attachments/assets/d6bac30b-a923-4eb3-8065-1e0c4edff33a)
Confirm object was successfully moved.
![image](https://github.com/user-attachments/assets/e95e2775-b20b-4a19-a1d0-20ab79949c09)

### Summary 
reinforce the basic concepts surrounding AD management. great to get hands-on
experience with topics and technologies like Active Directory. This experience provides a better
understanding of how it functions and how it can possibly be taken advantage of. New
vulnerabilities and attacks are being released every day that affect the Windows operating
system, and by extension, Active Directory. A fundamental understanding of AD, the attacks that
plague it, and defensive measures will take us a long way as security Professionals.
### Conclusions
exposure exposure exposure





















