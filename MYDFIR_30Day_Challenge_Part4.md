*This project is under construction.* 
# MyDFIR 30 Day SOC Analyst Challenge (Part 4)

<img width="896" alt="Part4banner" src="https://github.com/user-attachments/assets/42b2ae0a-0cc6-4fc6-9b95-d7c80b7e5b9e" />

## Day 16: Creating Alerts and Dashboards in Kibana (2/4)
Let's examine the authentication logs from my Windows server and create another brute force alert.

To get started, I opened up the `Discover` page within the Elastic web GUI. With the timeframe set to `Today`, I began narrowing down the events filtering by `agent.name` selecting only events logged by my `MYDFIR-WIN-jf` machine.

<img width="960" alt="day16-1" src="https://github.com/user-attachments/assets/46117632-7ae6-4483-abe9-653622246021" />

This machine has logged over 34,000 events today. Focusing on brute force activity, I can narrow these events down further by looking at event IDs that are associated with failed authentications. If you're not familiar with Windows event IDs, you can always Google it. In this case, [event ID 4625](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4625) documents every failed logon attempt. 

<img width="955" alt="day16-2" src="https://github.com/user-attachments/assets/3d59c82c-a019-47a5-baa2-f3d82ca64fa5" />

Searching for "event.code: 4625" narrows the number of events down further to 6,600. To start making sense of these results, I updated the search resutls table to include columns for `source.ip` and `user.name`.

<img width="962" alt="day16-5" src="https://github.com/user-attachments/assets/b0991f4b-2785-4139-8875-43f45da0a87f" />

Now that the search parameters are defined, a new alert can be configured.
