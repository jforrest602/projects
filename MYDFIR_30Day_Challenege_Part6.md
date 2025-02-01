*This project is under construction.*
# MyDFIR 30 Day SOC Analyst Challenge (Part 6)
<img width="894" alt="part6header" src="https://github.com/user-attachments/assets/928e52a6-b869-4f58-86ea-864eb87c4635" />

## Day 26: Investigating a Brute Force Attack
To begin investigating the SSH alerts that I previously created, I navigated to the hamburger icon `Secuirty > Alerts` in my Elastic web GUI. Nothing displayed on this page.
<img width="959" alt="Day26-2" src="https://github.com/user-attachments/assets/13eab798-7bfe-4f29-8634-eccab89fb280" />

However, I know there had been 176 events over the last 7 days, as shown in `Observability > Alerts` below that should be triggering the security alert.
<img width="959" alt="Day26-1" src="https://github.com/user-attachments/assets/a623ccab-f8da-4b94-b050-c52f199e8c75" />

More on this later.

----
There are several ways to investigate a brute force attack. Many failed login attempts over a short period of time from the same IP address or against the same account can be a key indicator. Alert rules can be defined to monitor this. Login attempts from uncommon geo-locations can be another indicator. 

#### Timelines in Elastic
Elastic offers a [timeline function](https://www.elastic.co/guide/en/security/current/timelines-ui.html) that can provide relevant information at a glance to help with investigations. Users have the ability to use predefined timeline templates or create custom timelines. Timelines utilize a drag-and-drop interface, query filters, and can even integrate MITRE ATT&CK tactics and techniques. Timelines can be a powerful tool for SOC analysts to investigate security threats, correlate events, and respond to incidents efficiently.

----
#### Investigating an Alert
<img width="947" alt="Day26-3" src="https://github.com/user-attachments/assets/7271e518-819a-4f77-abe5-50c43e2fd179" />

There are a few questions that you can ask yourself that can help with the investigation.
1) Is this IP address known to perform brute force activities?
2) Were any other users affected by this IP address?
3) Were any of the login attempts succesful?
4) If yes, what activities occurred after the login?



