# Active Directory Basics 

According to [Microsoft](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/active-directory-domain-services-overview?source=recommendations), Active Directory Domain Services (AD DS) "provides the methods for storing directory data and making this data available to network users and administrators." They define a directory as "a hierarchical structure that stores information about objects on the network." Objects are single elements like users, groups,shared folders, computers, printers and so on. AD stores object details and enables authorized users to access that information.   

Active Directory also provides authentication and authorization services within a Windows domain environment. 

*Why is knowing how to use AD important?* 

According to [HTB](https://academy.hackthebox.com/module/74/section/699), "around 95% of Fortune 500 companies run Active Directory." Therefore, if you're planning on joining the IT or InfoSec fields, familiarizing yourself with AD is a good idea.

![AD ninja](https://github.com/user-attachments/assets/5f0f9086-44a4-438f-a4ad-15fe0d29c0d4)

This introductory guided lab was completed as part of the HTB [Introduction to Active Directory](https://academy.hackthebox.com/module/details/74) module which covers AD basics. The goal of this lab was to gain confidence performing basic AD administrative tasks through the Windows graphical user interface (GUI).  

#### So what's the scenario?
![image](https://github.com/user-attachments/assets/ca863dbf-3300-4b95-8e7e-526763d71629)

To get started, I connected to the lab's host using the Remote Desktop Protocol (RDP).
![image](https://github.com/user-attachments/assets/c0350f3d-735a-4fed-ba5f-15c748acd1fa)

Once connected to the host, I accessed the Windows Administrative Tools by utilizing the search function on the taskbar.
![image](https://github.com/user-attachments/assets/e500e6a1-0e28-4e4e-941f-1ebd5206537b)


#### TASK 1: User Management - Adding New Users
The first task of the lab was to create three new users, something any AD admin will have to do when your company hires and onboards new employees. To do this, I accessed the AD Users and Computers via Windows Administrative Tools.
![image](https://github.com/user-attachments/assets/40526871-21f5-468d-9ee7-5d94b7a1a975)

Navigating to the proper folder, in this case the INLANEFREIGHT.LOCAL domain and drilling down to the Corp > Employees > HQ-NYC > IT folder, I right clicked and selected New > User.
![image](https://github.com/user-attachments/assets/55fdb3f0-d758-47da-9742-503716dd362b)

Once the dialog box opened up, I entered the employee information (first and last name, email address, display name), created a temporary password, and enabled the *User must change password at next logon* feature. 

In AD, object [attributes](https://learn.microsoft.com/en-us/windows/win32/adschema/attributes) is data that define the properties of objects. There are many different attributes an object can have but, here they include name, email address, and display name. Attributes can fall into several different types of class but, that is beyond the scope of this project. 

I repeteaded this procedure for an additional two new users.
![image](https://github.com/user-attachments/assets/c62c991d-fe50-4ae6-bd59-0111eeca38df)
![image](https://github.com/user-attachments/assets/ad314262-fcc7-4ae8-9a51-39fb804b6ac1)
![image](https://github.com/user-attachments/assets/4ab803a4-96da-42a5-90ae-edd854f7381b)


#### TASK 2: User Managment - Removing Users
Of course, employees (users) leave companies too. Next up, I was tasked with removing two users. I only had first and last names to work with so, I utilized the search function to locate the users.

![image](https://github.com/user-attachments/assets/f6ce2483-13cb-448a-a847-c799382417ba)

Once located, it is as simple as right clicking and selecting the Delete option and clicking OK.

![image](https://github.com/user-attachments/assets/586d915f-8efa-46d9-a2be-f89983d054dd)
![image](https://github.com/user-attachments/assets/b9f794b2-8c7c-4cad-b9fb-47cee6dcb993)

This process was repeated to remove the second user.

#### TASK 3: User Management - Unlocking Accounts and Forcing Password Changes
Within Identity and Access Management (IAM), best practice includes setting a limit to the maximum number of login attempts before an account is locked. It helps protect against brute force attacks. You may have experienced this before if you have ever forgotten a password.   
This task required me to unlock a user account and force the user to change their password upon their next logon. This helps protect the user and organization in case their password was compromised rather than just forgotten.
After locating the user via the search function, I right clicked and selected Reset Password on the pop-up menu.
![image](https://github.com/user-attachments/assets/0a9bc2f3-02e2-4e7f-8ca5-1214c79aef6e)

Once there, I made sure that the Unlock Account and User must change password at next logon options were selected and created a temporary password for the user.
![image](https://github.com/user-attachments/assets/51b6ee6d-36ef-427e-be7a-f1980850a569)
![image](https://github.com/user-attachments/assets/0eab7e60-80be-47e9-b3df-a75bc13dffa2)
I want to note here that this is just one of several ways that this task can be accomplished through the GUI.




