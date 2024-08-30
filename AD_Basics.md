# Active Directory Basics 

According to [Microsoft](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/active-directory-domain-services-overview?source=recommendations), Active Directory Domain Services (AD DS) "provides the methods for storing directory data and making this data available to network users and administrators." They define a directory as "a hierarchical structure that stores information about objects on the network." Objects are single elements like users, groups,shared folders, computers, printers and so on. AD stores object details and enables authorized users to access that information.   

*Why is knowing how to use AD important?* 

![AD ninja](https://github.com/user-attachments/assets/5f0f9086-44a4-438f-a4ad-15fe0d29c0d4)

This introductory guided lab was completed as part of the HTB [Introduction to Active Directory](https://academy.hackthebox.com/module/details/74) module which covers AD basics. The goal of this lab was to gain confidence performing basic AD administrative tasks through the Windows graphical user interface (GUI).  

#### So what's the scenario?
![image](https://github.com/user-attachments/assets/ca863dbf-3300-4b95-8e7e-526763d71629)

To get started, I connected to the lab's host using the Remote Desktop Protocol (RDP).
![image](https://github.com/user-attachments/assets/c0350f3d-735a-4fed-ba5f-15c748acd1fa)

Once connected to the host, I accessed the Windows Administrative Tools by utilizing the search function on the taskbar.
![image](https://github.com/user-attachments/assets/e500e6a1-0e28-4e4e-941f-1ebd5206537b)


#### TASK 1: User Management
The first task of the lab was to create three new users, something any AD admin will have to do when your company hires and onboards new employees. To do this, I accessed the AD Users and Computers via Windows Administrative Tools.
![image](https://github.com/user-attachments/assets/40526871-21f5-468d-9ee7-5d94b7a1a975)

Navigating to the proper folder, in this case the INLANEFREIGHT.LOCAL domain and drilling down to the Corp > Employees > HQ-NYC > IT folder, I right clicked and selected New > User.
![image](https://github.com/user-attachments/assets/55fdb3f0-d758-47da-9742-503716dd362b)
