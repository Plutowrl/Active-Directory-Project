# Active Directory HomeLab

## Objective

The goal of this project was to design, deploy, and optimize a fully functional active directory domain environment on prem, leveraging various tools and virtualization technologies which in turn enabled me to simulate real-world scenarios, generate some realistic telemetry data and monitoring for potential security threats. This lab as I have learnt is beneficial both for Red/Blue teamers as it allowed me initiate attacks and understand what to look out for in identifying these attacks. 

### Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.
- Familiarity with Network/Endpoint Administration

### Tools Used

- Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Network analysis tools (such as Wireshark) for capturing and examining network traffic.
- Telemetry generation tools (such as Atomic Red Team) to create realistic network traffic and attack scenarios.

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

And for Kali, I clicked the monitor logo at the top right hand corner and then edit connections. You can see the ipv4 option. I hit that and simply put in the static ip: 192.168.10.250 as per my network diagram and I used used google's DNS:


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

Following the restart, I was now able to login with the newly created users. I will use the user: Jose Cuntab as a point of reference. As you can see here, I was able to put in the logon name: jcuntab@Pluto.wrld and my super secure password. 


![Screenshot (62)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/3cb14441-ecfc-47c0-a99d-3f49c0f1182e)
Ref #: Using newly created users to login to the domain

## Part 5 
This is the final part of this project where things get a little spicy. In this part, the goal was to use the attacking machine running Kali linux and then perform a brute force attack to one of the newly created users from the previous part. To begin, I headed over to the Kali machine and I used a brute force tool: **crowbar**

I will not go into specifics on how to install **crowbar** as there is online documentation. Just remember to update your repositories first with sudo-get update and sudo-get upgrade as that was one error i ran into. So now with **crowbar** installed, I used the very popular **rockyou.txt** wordlist for this attack but I made an important change. The rockyou.txt wordlist is a big file and will take some time going through each word against a target. For the sake of this lab, I only used the first couple lines in the document and saved it in a new document: **passwords.txt**. I then modified the new document by adding an additional line which contains my super secure password **IdK123()!?** for one of the targets. Again, I will use Jose Cuntab as a reference point. See below:

![Screenshot 2024-04-18 174722](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/d523ea80-8bbe-4f8f-acd0-eb9d935a8040)
Ref #: Modified passwords.txt file that contains login password as the very bottom for **Jose Cuntab**

It was now time to head back to the target machine. First thing I did was to enable remote desktop by navigating to This PC > Properties > Advanced System Settings > Remote > Allow Remote Connections > Users > Add and then here i filled in the user I wanted to enable rdp for. In this case, Jose Cuntab as you can see at the bottom of the list

![Screenshot 2024-04-18 180801](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/0e6371e5-7d9f-4338-8955-8c6adb19d539)
Ref #: Enabling RDP for Jose Cuntab

Now it was finally time to launch the attack. I needed four key things for this: the service type (RDP), the user (jcuntab), the worlist (passwords.txt) and the target ip which from our diagram was 192.168.10.100. On my Kali machine, I used the keywords: **crowbar -b rdp -u jcuntab -C passwords.txt -s 192.168.10.100/32**. The "/32" was just to indicate it was a single host. As you can see from the below snip, the **RDP SUCCESS** indicates a successful brute force attack. 

![Screenshot 2024-04-18 184448](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/579ddf98-5540-40af-8579-19dbac2b6a2b)
Ref #: Successful brute force attack against jcuntab!

Now, I made my way back to splunk and ran a search to see what type of events that will generate. If you can recall, I created an index named **endpoint** from part 3. Now i ran a search with endpoint as the index and then specified the account which in this case was jcuntab. I was able to see some events. The main event I was looking for, I matched to an event code **4625**. This event code signifies a failed logon attempt. We can see here from the snip below, the amount of events that were generated.

![Screenshot (66)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/3e8473ae-6d8e-4e95-a057-92baf6920a51)
Ref #: Searching in splunk to identify our brute force attack

To enhance my search, I was mainly looking at the time of occurence as any events that occured simultaneously will be a clear indicator of brute force. As you can see here from the below snips, I clicked on the "show all 61 lines" and then you can see here from the network information, it shows my kali machine and the ip address as the entity that was trying to gain access. The handwork of crowbar!


![Screenshot 2024-04-18 192412](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/ee4a8d52-f4fc-4166-a64a-c63cbe6c170e)

Ref #: Identical timestamps to indicate a clear brute force attack


![Screenshot (65)](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/ed2b2359-8b19-4b55-84c4-fe6b1a33b2b1)


Ref #: Successful identification of brute force

Part 6
This is the final part of this project. In this part, I used Atomic Red Team to............... Upon installing ART, there's a file within it titled **atomics**. In this file, there's a bunch of technique ID's which are consistent with the technique id's from the MITRE ATT&CK FRAMEWORK. In my case, I tried to test out a technique of persistence, say creating an account for example (T1136). See below:




![Screenshot 2024-04-23 183510](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/2e705bf9-0cc3-4f0f-b041-c11dc5397457)
Ref #: Using the "Create an account Technique ID T1136.001)

There's three variants to that technique ID but for this project, I made use of the first one which is for creating a local user account (T1136.001), I used the command "Invoke-AtomicTest T1163.001". This should automatically generate some telemetry based on creating a local user account. You can see from the snip below that this created an account with the name: NewLocalUser.

![Screenshot 2024-04-23 185844](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/3249bc2b-7ea3-44e4-bd87-01a6ef262dd0)
Ref #: Using Atomic Red Team to create a new local user.

Now I went back to splunk to see what kind of telemtry was generated for the new user: NewLocalUser. We can see here from the below snip that there was some telemetry generated for NewLocalUser:

![Screenshot 2024-04-23 191133](https://github.com/Plutowrl/Active-Directory-HomeLab/assets/166238383/f6f9b7de-10d5-41be-85d3-7671cc7b02b6)

Ref #: Splunk search highlighting events for NewLocalUser.

Now what does this mean? The beauty of Atomic Red Team is, it can show you what your system is visible to and what your system can't detect with your current settings. This previous example tells me that my current settings are in fact able to detect any compromise of a local account creation. Now if for example my splunk search returned no events, that will simply tell me that my current settings are not set to be able to detect/alert when a potential adversary is able to create a local account on the system. In summary, ART is a great tool to use and see what type of compromise your system may/may not be blind to!

## The End


