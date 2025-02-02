# Part 1:
## Security Control Types
The concept of defense in depth can be broken down into three different security control types. Identify the security control type of each set  of defense tactics.
1) Walls, bollards, fences, guard dogs, cameras, and lighting are what type of security control?
* Answer: Physical control
2) Security awareness programs, BYOD policies, and ethical hiring practices are what type of security control?
* Answer: Administrative control
3) Encryption, biometric fingerprint readers, firewalls, endpoint security, and intrusion detection systems are what type of security control?
* Answer: Technical control

## Intrusion Detection and Attack indicators
1) What's the difference between an IDS and an IPS?
* Answer: `Intrution Detection Systems` analyze and monitor network traffic for signatures that match known cyberattacks, these tools do not take action on there own while `Intrution Prevention system` analyze, monitor. accepts or rejects network traffic based on ruleset. These tools can take action on their own.
2) What's the difference between an Indicator of Attack and an Indicator of Compromise?
* Answer: `Indicator of Attack` shows that an attack is ongoing on a system. it detects the intent of what an attacker is trying to accomplish  while `Indicator of Compromise` indicates that the security of the network has been breached. It shows the details after an attack has been done. The information acquired can be used to mitigate future possible threats

## The Cyber Kill Chain
Name each of the seven stages for the Cyber Kill chain and provide a brief example of each.
* Stage 1-RECONNAISSANCE: This is the step an attacker selects a target, depending on the motive(s) of the attacker and also gathering as much infomation as possible on an individual or organization in preparation for the attack
* Stage 2-WEAPONIZATION: attackers will re-engineer some core malware to suit their purposes using sophisticated techniques. This process often involves embedding specially crafted malware into an otherwise benign or legitimate document, such as a press release or contract document, or hosting the malware on a compromised domain
* Stage 3-DELIVERY: Attackers will then determine which methods to use in order to deliver malicious payloads. Some of the methods they might utilize are automated tools, such as exploit kits, spear phishing attacks with malicious links, or attachments and malvertizing
* Stage 4-EXPLOITATION: At this stage, attackers deploy an exploit against a vulnerable application or system so a to gaining remote access and compromise the user's machine
* Stage 5-INSTALLATION: Once attackers have established an initial foothold, they will install malware in order to conduct further operations, such as maintaining access, persistence and escalating privileges
* Stage 6-COMMAND AND CONTROL: Attackers will establish a command channel in order to communicate and pass data back and forth between the infected devices and their own infrastructure. They can now actively control the system, instructing the next stages of attack.
* Stage 7-Actions on the Objective: Now that the adversaries have control, persistence and ongoing communication, they will act upon their motivations in order to achieve their goal. This could be data exfiltration, destruction of critical infrastructure, to deface web property, or to create fear or the means for extortion.

## Snort Rule Analysis
Use the Snort rule to answer the following questions:
#### Snort Rule #1
```
alert tcp $EXTERNAL_NET any -> $HOME_NET 5800:5820 (msg:"ET SCAN Potential VNC Scan 5800-5820"; flags:S,12; threshold: type both, track by_src, count 5, seconds 60; reference:url,doc.emergingthreats.net/2002910; classtype:attempted-recon; sid:2002910; rev:5; metadata:created_at 2010_07_30, updated_at 2010_07_30;)
```
1) Break down the Sort Rule header and explain what is happening.
* Answer: This type of response is an alert, the protocol is tcp. traffic coming from external network from any port going to home network on port 5800-5820
2) What stage of the Cyber Kill Chain does this alert violate?
* Answer: Reconnaissance
3) What kind of attack is indicated?
* Answer: Potential VNC Scan

#### Snort Rule #2
```
alert tcp $EXTERNAL_NET $HTTP_PORTS -> $HOME_NET any (msg:"ET POLICY PE EXE or DLL Windows file download HTTP"; flow:established,to_client; flowbits:isnotset,ET.http.binary; flowbits:isnotset,ET.INFO.WindowsUpdate; file_data; content:"MZ"; within:2; byte_jump:4,58,relative,little; content:"PE|00 00|"; distance:-64; within:4; flowbits:set,ET.http.binary; metadata: former_category POLICY; reference:url,doc.emergingthreats.net/bin/view/Main/2018959; classtype:policy-violation; sid:2018959; rev:4; metadata:created_at 2014_08_19, updated_at 2017_02_01;)
```
1) Break down the Sort Rule header and explain what is happening.
* Answer: This type of response is an alert, the protocol is tcp. traffic coming from external network from http port or port 80 going to home network on any port
2) What layer of the Defense in Depth model does this alert violate?
* Answer: Administrative: policy-violation
3) What kind of attack is indicated?
* Answer: PE EXE or DLL Windows file download HTTP

#### Snort Rule #3
Your turn! Write a Snort rule that alerts when traffic is detected inbound on port 4444 to the local network on any port. Be sure to include the msg in the Rule Option.
* Answer: `alert tcp $EXTERNAL_NET 4444 -> $HOME_NET any (msg:"ET Possible Trojan, Prosiak, Oracle or Crackdown")`

# Part 2: "Drop Zone" Lab
## Uninstall ufw
Before getting started, you should verify that you do not have any instances of ufw running. This will avoid conflicts with your firewalld service. This also ensures that firewalld will be your default firewall.
* Run the command that removes any running instance of ufw.
  * `$ sudo apt -y remove ufw`
## Enable and start firewalld
By default, these service should be running. If not, then run the following commands:
- Run the commands that enable and start firewalld upon boots and reboots.
  * `$ sudo systemctl enable firewalld`
  * `$ sudo systemctl start firewalld`
 - ![alt text](homework_11/134321.png)
- Note: This will ensure that firewalld remains active after each reboot.
## Confirm that the service is running.
- Run the command that checks whether or not the firewalld service is up and running.
  * `$ sudo firewall-cmd --state`
## List all firewall rules currently configured.
Next, lists all currently configured firewall rules. This will give you a good idea of what's currently configured and save you time in the long run by not doing double work.
* Run the command that lists all currently configured firewall rules:
  * `$ sudo firewall-cmd --list-all`
- Take note of what Zones and settings are configured. You many need to remove unneeded services and settings.
## List all supported service types that can be enabled.
- Run the command that lists all currently supported services to see if the service you need is available
  * `$ sudo firewalld-cmd --get-services`
  * ![alt text](homework_11/134325.png)
- We can see that the Home and Drop Zones are created by default.
## Zone Views
- Run the command that lists all currently configured zones.
  * `$ sudo firewall-cmd --list-all-zones`
- We can see that the Public and Drop Zones are created by default. Therefore, we will need to create Zones for Web, Sales, and Mail.
## Create Zones for Web, Sales and Mail.
- Run the commands that creates Web, Sales and Mail zones.
  * `$ sudo firewall-cmd --permanent --new-zone=web`
  * `$ sudo firewall-cmd --permanent --new-zone=mail`
  * `$ sudo firewall-cmd --permanent --new-zone=sales`
  * ![alt text](homework_11/134326.png)
## Set the zones to their designated interfaces:
- Run the commands that sets your eth interfaces to your zones.
  * `$ sudo firewall-cmd --zone=public --change-interface=eth0`
  * `$ sudo firewall-cmd --zone=web --change-interface=eth1`
  * `$ sudo firewall-cmd --zone=sales --change-interface=eth2`
  * `$ sudo firewall-cmd --zone=mail --change-interface=eth3`
  * ![alt text](homework_11/134321-(8).png)
## Add services to the active zones:
-Run the commands that add services to the public zone, the web zone, the sales zone, and the mail zone.
- Public:
  * `$ sudo firewall-cmd --permanent --zone=public --add-service=http`
  * `$ sudo firewall-cmd --permanent --zone=public --add-service=https`
  * `$ sudo firewall-cmd --permanent --zone=public --add-service=pop3`
  * `$ sudo firewall-cmd --zone=public --add-service=smtp`
- Web:
  * `$ sudo firewall-cmd --permanent --zone=web --add-service=http`
- Sales
  * `$ sudo firewall-cmd --permanent --zone=sales --add-service=https`
- Mail
  * `$ sudo firewall-cmd --permanent --zone=mail --add-service=pop3`
  * `$ sudo firewall-cmd --permanent --zone=mail --add-service=smtp`
  * ![alt text](homework_11/134321-(13).png)
- What is the status of http, https, smtp and pop3?

## Add your adversaries to the Drop Zone.
- Run the command that will add all current and any future blacklisted IPs to the Drop Zone.
  - `$ sudo firewall-cmd --permanent --zone=drop --add-source=10.208.56.23`
  - `$ sudo firewall-cmd --permanent --zone=drop --add-source=135.95.103.76`
  - `$ sudo firewall-cmd --permanent --zone=drop --add-source=76.34.169.118`
  - ![alt text](homework_11/134321-(11).png)
## Make rules permanent then reload them:
- It's good practice to ensure that your firewalld installation remains nailed up and retains its services across reboots. This ensure that the network remains secured after unplanned outages such as power failures.
- Run the command that reloads the firewalld configurations and writes it to memory
  - `$ sudo firewall-cmd --reload`
## View active Zones
Now, we'll want to provide truncated listings of all currently active zones. This a good time to verify your zone settings.
- Run the command that displays all zone services.
  - `$ sudo firewall-cmd --get-active-zones` 
## Block an IP address
- Use a rich-rule that blocks the IP address 138.138.0.3.
  - `$ sudo firewall-cmd --permanent --zone=public --add-rich-rule='rule family="ipv4" source address="138.138.0.3" reject'`
  - ![alt text](homework_11/134321-(18).png)
## Block Ping/ICMP Requests
Harden your network against ping scans by blocking icmp ehco replies.
- Run the command that blocks pings and icmp requests in your public zone.
  - `$ sudo firewall-cmd --zone=public --add-icmp-block=echo-reply --add-icmp-block=echo-request`
  - ![alt text](homework_11/134321-(19).png)
## Rule Check
Now that you've set up your brand new firewalld installation, it's time to verify that all of the settings have taken effect.
- Run the command that lists all  of the rule settings. Do one command at a time for each zone.
  - `$ sudo firewall-cmd --list-all --zone=public`
  - ![alt text](homework_11/134321-(20).png)
  - `$ sudo firewall-cmd --list-all --zone=web`
  - ![alt text](homework_11/134321-(21).png)
  - `$ sudo firewall-cmd --list-all --zone=sales`
  - ![alt text](homework_11/134321-(22).png)
  - `$ sudo firewall-cmd --list-all --zone=mail`
  - ![alt text](homework_11/134321-(23).png)
  - `$ sudo firewall-cmd --list-all --zone=drop`
  - ![alt text](homework_11/134321-(24).png)
  - Are all of our rules in place? If not, then go back and make the necessary modifications before checking again.
  - Congratulations! You have successfully configured and deployed a fully comprehensive firewalld installation.

# Part 3: IDS, IPS, DiD and Firewalls
Now, we will work on another lab. Before you start, complete the following review questions.
## IDS vs. IPS Systems
1) Name and define two ways an IDS connects to a network.
- Answer 1: Network based intrusion detection system; is deployed at a strategic point or points within the network, where it can monitor inbound and outbound traffic to and from all the devices on the network
- Answer 2: Host intrusion detection system (HIDS) runs on all computers or devices in the network with direct access to both the internet and the enterprise's internal network.
2) Describe how an IPS connects to a network.
- Answer: An IPS is mostly located between a company's firewall and the rest of its network and may have the ability to stop any suspected traffic from getting to the rest of the network. Intrusion prevention systems execute responses to active attacks in real time and can actively catch intruders that firewalls or antivirus software may miss
3) What type of IDS compares patterns of traffic to predefined signatures and is unable to detect Zero-Day attacks?
- Answer: Signature based IDS
4) Which type of IDS is beneficial for detecting all suspicious traffic that deviates from the well-known baseline and is excellent at detecting when an attacker probes or sweeps a network?
- Answer: Anomaly based
## Defense in Depth
1) For each of the following scenarios, provide the layer of Defense in Depth that applies:
   1) A criminal hacker tailgates an employee through an exterior door into a secured facility, explaining that they forgot their badge at home.
   - Answer: Physical 
   2) A zero-day goes undetected by antivirus software.
   - Answer: Application 
   3) A criminal successfully gains access to HR’s database.
   - Answer: Data
   4) A criminal hacker exploits a vulnerability within an operating system.
   - Answer: Host
   5) A hacktivist organization successfully performs a DDoS attack, taking down a government website.
   - Answer: Network
   6) Data is classified at the wrong classification level.
   - Answer: Policy, procedures, & awareness
   7) A state sponsored hacker group successfully firewalked an organization to produce a list of active services on an email server.
   - Answer: Perimeter
2) Name one method of protecting data-at-rest from being readable on hard drive.
- Answer: Data encription
3) Name one method to protect data-in-transit.
- Answer: Using SSL to encript connection
4) What technology could provide law enforcement with the ability to track and recover a stolen laptop.
- Answer:  GPS or Wi-Fi geolocation
5) How could you prevent an attacker from booting a stolen laptop using an external hard drive?
- Answer: Set a BIOS password, Fireware encrypted password
## Firewall Architectures and Methodologies
1) Which type of firewall verifies the three-way TCP handshake? TCP handshake checks are designed to ensure that session packets are from legitimate sources.
- Answer: Circuit-Level Gateway Firewall
2) Which type of firewall considers the connection as a whole? Meaning, instead of looking at only individual packets, these firewalls look at whole streams of packets at one time.
- Answer: Stateful Firewalls
3) Which type of firewall intercepts all traffic prior to being forwarded to its final destination. In a sense, these firewalls act on behalf of the recipient by ensuring the traffic is safe prior to forwarding it?
- Answer: Application gateways
4) Which type of firewall examines data within a packet as it progresses through a network interface by examining source and destination IP address, port number, and packet type- all without opening the packet to inspect its contents?
- Answer: Packet-filtering firewall
5) Which type of firewall filters based solely on source and destination MAC address?
- Answer: MAC layer firewall

# Bonus Lab: "Green Eggs & SPAM"
## Threat Intelligence Card
1) What was the indicator of an attack? Hint: What do the details of the reveal?
- Answer: TROJAN JS/Nemucod.M.gen downloading EXE payload, Trojan activity
2) What was the adversarial motivation (purpose of attack)?
- Answer: To be able to gain remote access of the computer and steal information 
3) Describe observations and indicators that may be related to the perpetrators of the intrusion. Categorize your insights according to the appropriate stage of the cyber kill chain, as structured in the following table
- TTP- Reconnaissance
  - Example- How did they attacker locate the victim?
  - Findings- the campaigns are attached directly to emails inside a zip file
-
- TTP- Weaponization
  - Example- What was it that was downloaded?
  - Findings- A Trojan JavaScript, An italian spam campaigns
-
- TTP-Delivery
  - Example- How was it downloaded?
  - Findings- Using JavaScript downloader called JS/Nemucod
- 
- TTP-Exploitation
  - Example- What does the exploit do?
  - Findings- the default file type associations in Windows caused internet Explorer to open and execute the JavaScript
-
- TTP-Installation
  - Example- How is the exploit installed?
  - Findings- When the user opens the zip file and double clicks the JavaScript, the default file type associations in Windows will cause internet Explorer to open and execute the JavaScript. This version of javaScript file downloads a simple EXE file which is then invoked directly in the bacground through the WScript.Shell ActiveX control
-
- TTP-Command & Control (C2)
  - Example- How does the attacker gain control of the remote machine?
  - Findings- Once all files have been executed, the computer is rebooted after few instants and Gozi starts phoning home after the reboot. 
-
- TTP-Actions on Objectives
  - Example- What does the software that the attacker sent do to complete it's tasks?
  - Findings- The executable files downloaded by Nemucod are used to retrieve a Tronjan Downloader called Fareit or Pony Downloader, which in turn downloads another set of executables files containing the Gozi infostealer.

4) What are your recommended mitigation strategies?
- Answer: Provide ongoing education to users on spear phishing links, unknown emails, risky websites, etc.
  - Perform continuous inspection of network traffic flows to detect and prevent port scans and host sweeps
  - Redirect malicious outbound communication to internal sinkholes to identify and block compromised hosts
  - Block known and unknown vulnerability exploits on the endpoint
5) List your third-party references.
- Answer: Certago, paloaltonetworks Cyberpedia, Protech cyber security
