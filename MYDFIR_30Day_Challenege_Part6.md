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
<img width="947" alt="Day26-3" src="https://github.com/user-attachments/assets/aabc3628-10bf-4c66-9ec6-781e3a854e35" />

There are a few questions that you can ask yourself that can help with the investigation.
__1) Is this IP address known to perform brute force activities?__
This helps build context around the alert. If the IP address is known to be associated with brute force activity, it should be cause for concern.
The above screenshot of some of the alert details gives me the IP address of `218.92.0.182`. To find out whether or not this IP address has been associated with any malicious behavior, I can search for it on [AbuseIPDB](https://www.abuseipdb.com/). This IP address has been reported 1094 times with a 100% confidence of abuse.
<img width="959" alt="Day26-4" src="https://github.com/user-attachments/assets/aad49208-0b6e-4271-b987-79aa9ca30311" />

Scrolling down to view more details, recent reports all involved failed password for root and are tagged as ssh brute force, over and over.
<img width="958" alt="Day26-4a" src="https://github.com/user-attachments/assets/5c706d74-3cac-4741-a230-c12f4198db85" />

This leaves no doubt that this IP address is well known to perform brute force attacks.

Another good resource to use is [GreyNoise](https://www.greynoise.io/), which similarly allows users to search IP addresses. After entering the `218.92.0.182` IP address, it is very lear that this IP is associated with malicious activity.
<img width="959" alt="Day26-4b" src="https://github.com/user-attachments/assets/b8bae858-f4db-49a5-9767-387edde54a8c" />

It even goes as far as suggesting a course of action.
<img width="960" alt="Day26-4c" src="https://github.com/user-attachments/assets/8a0ffb1c-08ed-44ef-892c-2ce5947c390e" />

Other details like associated organization and tags are also provided.
<img width="958" alt="Day26-4d" src="https://github.com/user-attachments/assets/60806dd2-61f0-4981-9d52-f9e4fb444836" />

__2) Were any other users affected by this IP address?__
This can help you discern who or what the attacker is targeting.

4) Were any of the login attempts succesful?

5) If yes, what activities occurred after the login?


