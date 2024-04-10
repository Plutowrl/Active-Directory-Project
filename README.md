# Active Directory HomeLab

## Objective
[Brief Objective - Remove this afterwards]

The goal of this project was to design, deploy, and optimize a fully functional active directory domain environment on prem, leveraging various tools and virtualization technologies which in turn enabled me to simulate real-world scenarios, generate some realistic telemetry data and monitoring for potential security threats. This lab as I have learnt is beneficial both for Red/Blue teamers as it allowed me initiate attacks and understand what to look out for in identifying these attacks. 

### Skills Learned
[Bullet Points - Remove this afterwards]

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
- Familiarity with Network/Endpoint Administration

### Tools Used
[Bullet Points - Remove this afterwards]

- Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Network analysis tools (such as Wireshark) for capturing and examining network traffic.
- Telemetry generation tools to create realistic network traffic and attack scenarios.

## Steps
drag & drop screenshots here or use imgur and reference them using imgsrc

## Part 1
For the first part of this project. I drew a logical network diagram as a reference point that will help me map out how I would design the lab. For this project, I used a total of two servers: One for splunk which will be running on ubuntu and the other for Active Directory which will be running on our windows server. Two computers: One target which will be running windows and the other will be my kali linux maching for launching attacks. All servers and computers were configured using VirtualBox.


![1st Homelab](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/d219429a-8091-4937-a9dc-73ea335fc7d0)

Ref 1: Network Diagram

## Part 2
For part two of this project, this was where I got to installing all machines and servers as shown in the network diagram in part one. The most important thing here is to make sure all servers/computers are part of the same network so they can talk to each other. I will show you how I did this on VirtualBox. For the sake of being concise, I'll not go through how to configure each server/computer on Vbox as you can find online documentation for that, I will just provide screenshots showing the end results after installing them all

So to begin, I'll head over to virtualbox and set all computers/servers to talk to each other and have internet. I did this by setting the network settings to NAT network. Network settings can be located by navigating to Tools > Bullet points > NAT Network > Create. Now I filled in the name: AD Project as you can see below and then put in the ip address of our network, which if you recall was in the network diagram and then saved it. Below is a screenshot of the config, and if you look to the left, you can see all my servers and computers that were installed. 

![Screenshot (55)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/56969dc0-29fe-43b6-bcf4-3f15bbdbafd8)
                         Ref 2: All machines/servers installed on Vbox, all on NAT Network

Next, I had to go into each machine and assign them their respective static ip's as seen in the network diagram in part one. I will show you how I did this for my Windows and Kali machine just to keep things concise. For windows, I went into my network settings and then changed my adapter options to use the static ip: 192.168.10.100. You may also notice my DNS is set to the static ip of my windows server where I installed active directory. This was so my target PC was able to resolve my domain server. You will see more of this explanation in part four. 

And for Kali, I clicked the monitor logo at the top right hand corner and then edit connections. You can see the ipv4 option. I hit that and simply put in the static ip: 192.168.10.250 and used google's DNS:


## Part 3
For this part of the project, I got to install and configure SYSMON, splunk and splunk universal forwarder on to my windows target machine and my windows server. Doing this will ensure they can start collecting telemetry and sending logs over to our splunk server. Again, the purpose of this project is not to show you how to install these tools as I myself used online videos and documentation in doing so. After successfully installing the three tools listed earlier, I was now able to login to splunk. If you recall from the diagram, my spluk server was assigned an ip of 192.168.10.10 and splunk by default listens on port 8000. 


![Screenshot (58)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/bc3a552d-4262-4b20-b1a6-dd8063444f85)
Ref #: Successful login to splunk using 192.168.10.10:8000

Next thing I did here was to configure splunk and create my index with an inputs.conf file I found online. All of the events on the inputs.conf file are sent over to an index called endpoints so I was able to create a new index with the name: endpoints. I did thing by navigating to Settings > Indexes > New Index > and then filled in the name field as endpoints. Once that was done, I had to make sure my splunk server could receive the data. I did this by navigating to Settings > Forwarding and Receiving > Configute receiving > New Receiving Port > and filled in the receiving port number. I kept this as the defaul port: 9997

Lastly, to test this out and make sure we can see data coming in from our windows machine which I have named as "TARGET", I was able to perform a search by clicking Apps > Search and Reporting > and then using the query: index=endpoints. And as you can see from the below snippet, the query was able to generate some events!

![Screenshot (59)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/4a184fc6-de72-4190-b12f-cf1559c4c39a)
Ref #: Simple search on splunk to see events received from my target machine

## Part 4
For part four of the project, the goal here was to configure and install Active Directory on to our windows server, promoting it to a domain controller and then finally using our target machine to join the domain. After successfully installing Active Directory Domain Services (ADDS) and promoting my server to a domain controller, I was now able to login to my AD domain which I named PLUTO!

Ref #: Login screen after installing AD and promoting my server to a domain controller

The next step was to begin creating some users. To do this, I navigated to Tools > AD Users and Computers. In a real world scenario, users/computers will likely be members of different organisational units for example; IT, Finance, HR, etc. To go in line with that, I created three OU's: HR, IT and Marketing. I did this by right clicking my domain controller > New > Organisational Unit. Then i was able to assign whatever name of my choice. Below is a snip of the three organisational units I created:

![Screenshot 2024-04-08 194021](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/59a302aa-227e-490b-adca-43d5dbbab896) 

Ref #: IT, Marketing and HR organisation unit I created

Next step was to begin creating some users. For this project, all I needed was just a single user but I went ahead to create two users underneath the IT organisational unit. I did this by rightclicking the IT OU > New > User and then filling in their respective names and logon names. Below is a screenshot of the process and what it looks like after successfully creating them:


![Screenshot 2024-04-08 195950](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/d9d0d541-14d7-4d40-b7d2-813d05461821)

Ref #: Creating new users

![Screenshot 2024-04-08 195749](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/e587a3ed-8c48-47ab-b938-86e27853d379)

Ref #: After successfully creating new users

The last thing to do now was to head over to my windows target machine and then use it to join the newly created domain. So on the target Machine, I was able to join the domain by navigating to the Start menu > Searching "This PC" > Properties > Advanced System Settings > Computer Name > Change > Domanin and then filling in name of the domain which as you can see from the snippet below, my domain name is Pluto.wrld. When I did this the first time, I actually got an error that indicated my domain could not be contacted. I did some research and found out the reason was because my Target PC did not know how to resolve Pluto.wrld based on the way DNS works. The way I fixed this was to update my network adapter settings and change my DNS server to point at my domain hosted on 192.168.10.7 as you can recall from the diagram. After that, I hit OK, logged in with Adminstrator credentials and was able to successfully join the domain. I was prompted to do a restart to finalize everything.

![Screenshot (61)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/2b22276f-eee8-495f-bc83-0f0e3531dd73)
Ref #: Using target PC to join newly created domain

Following the restart, I was now able to login with the newly created users. I will use the user: Jose Cuntab as a point of reference. As you can see here, I was able to put in the logon name: jcuntab@Pluto.wrld and my super secure password.  my DNS  We will do this by using the newly created users above. I will use user: Jose Cuntab for reference. You can see here 


![Screenshot (62)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/3cb14441-ecfc-47c0-a99d-3f49c0f1182e)
Ref #: Using newly created users to login to the domain




Example below.

*Ref 1: Network Diagram*
