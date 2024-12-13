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

Now that the search parameters are defined, a new alert can be configured. Navigating to `Alerts`, choosing `Create search threshold rule`, it is straightforward to name the rule, customize the configurations, test the rule, and save.
<img width="960" alt="day16-5a" src="https://github.com/user-attachments/assets/736d52d3-14d2-4ff0-b6de-66092a5499a7" />
<img width="958" alt="day16-5b" src="https://github.com/user-attachments/assets/034688eb-f380-4cca-9880-b147e06b3e13" />
<img width="960" alt="day16-5c" src="https://github.com/user-attachments/assets/f92fe2f0-a932-47de-b77a-19c3d04d2f19" />

#### Viewing Alerts
To view the alerts that these rules are now generating, under the hamburger icon, navigate to `Management` > `Stack Management` > `Alerts`. To view the alert details, click the ellipsis icon next to the rule name and select `View alert details`.
<img width="961" alt="day16-6b" src="https://github.com/user-attachments/assets/547a959a-e15e-49fe-a81b-6975cf86bbfb" />

The problem here is that it doesn't provide many useful details as to what has taken place. You may need to know what user was affected or where the attack came from.
<img width="959" alt="day16-6c" src="https://github.com/user-attachments/assets/0fe53363-d596-47d5-92dd-a7def0c6f2f6" />

#### SIEM Detection Rules
Let's create a rule that will provide us with more useful details. Under the hamburger icon, navigate to `Security` > `Rules` > `Detection rules (SIEM)`.
<img width="958" alt="day16-7" src="https://github.com/user-attachments/assets/3d0752f5-89c2-4ada-8192-9649395e1079" />

Here, you can load Elastic prebuilt detection rules and create custom rules. 
<img width="962" alt="day16-7a" src="https://github.com/user-attachments/assets/5b1bb91c-cbd2-4fb1-95ef-1517b989e5dd" />

Choosing `Create new rule` provides several options. In this case, I am using `Threshold`. 
<img width="957" alt="day16-7b" src="https://github.com/user-attachments/assets/e7522f3f-87d9-42e4-9e7f-c717ee927a19" />

The `Index Patterns` are left as default and the `Custom query` is defined by copying the previously saved search with an additional parameter of `user.name: root`. Next, decide how to organize the results in the `Group by` field. In this case, by username and source IP address. `User.name` and `source.ip` are also included as `Required fields`.
<img width="948" alt="day16-7c" src="https://github.com/user-attachments/assets/ffa63482-b8e4-4f69-b632-6021cc21a2d7" />

Next, the name will need a rule and brief description followed by severity and risk score
<img width="949" alt="day16-7d" src="https://github.com/user-attachments/assets/f3710d60-d163-4dc0-b9c7-1cab16eeb7aa" />

There are other optional settings to consider here under `Advanced settings` where items like URLs or the MITRE ATT&CK tactic can be included. You can also choose a custom highlighted field, include a setup guide, or even provide steps for investigating the alert. This is great for a SOC environment because it helps analysts understand exactly what they should and should not do when the alert has been triggered.

Next, shcedule the time interval on which the rule should run and decide how far back you want it to look.
<img width="949" alt="day16-7f" src="https://github.com/user-attachments/assets/c6573230-3390-447b-923a-38daea9d78f5" />

Now, you have the ability to choose what kind of action you want to take when the rule is triggered. Finish by clicking `Create & enable rule`.
<img width="947" alt="day16-7h" src="https://github.com/user-attachments/assets/b94f1f27-4269-4105-9614-5d76105176b5" />
