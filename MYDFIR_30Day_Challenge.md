*This project is under construction.*

# MyDFIR 30 Day SOC Analyst Challenge


This 30-day Security Operations Center (SOC) Analyst challenge aims to provide free, practical experience for aspiring SOC analysts who may lack hands-on experience. It is designed to bridge the gap between theoretical knowledge and practical application of skills for entry level applicants. 

The challenge consists of daily lessons released on YouTube and will cover topics  such as creating logical diagrams, the ELK stack, attacking machines, detecting and investigating those attacks. Practical skills include creating alerts and dashboards, as well as integrating a ticketing system to track work flow.

For more information about the project creator, visit https://www.mydfir.com/. 

To find out more about the challenge and try it for yourself, check out https://www.youtube.com/@MyDFIR, where you will find the entire video series for the challenge and many other helpful and informative videos for anyone looking to build their cybersecurity knowledge and skillset.

# Day One: Logical Diagram

![MYDFIR 30 Day Challenge Network Diagram drawio](https://github.com/user-attachments/assets/8571246f-1dd1-42db-b6da-3c5a82e15e4a)

The first task of the challenge was to create a logical diagram of the challenge infrastructure. The resource used to create the diagram was [draw.io](http://draw.io/), a user-friendly platform with many tools and options for customizing diagrams. 

From the diagram, we see that the challenge involves six servers, five of which reside on a virtual private cloud (VPC). One advantage of using a VPC provider is that it allows you to place your virtual servers on the same network. Another is that there will be much less consumption of your own machine's resources compared to if you were running these virtual servers on your local machine. In the top left corner of the VPC inner rectangle, our IP range and subnet mask are noted. 

We see that the VPC is connected to the internet via an internet gateway represented by the orange cloud. This can be thought of as the internet service provider (ISP). Finally, we have our own SOC analyst machine in blue and the attackers machine and command and control server in red. Kali Linux is a Linux distribution designed for digital forensics and penetration testing and is a preferred operating system of both ethical and unethical hackers. 

Day one taught me how to create a diagram and I now have a logical representation of the challenge setup. [Draw.io](http://draw.io/) is an excellent platform for getting comfortable with building logical diagrams.

# Day Two: The ELK Stack

# Day Three
