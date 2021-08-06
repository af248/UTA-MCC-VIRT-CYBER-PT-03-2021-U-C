## Networking Fundamentals Homework: Rocking your Network!

You have just been hired by RockStar Corporation as a network security analyst.

- RockStar Corp has recently built a new office in Hollywood, California. You are tasked with completing a **network vulnerability assessment** of the office.

- You will complete several steps in order to analyze the Hollywood network and then provide RockStar Corp a summary of your findings.

- RockStar Corp is also concerned that a hacker may have infiltrated their Hollywood office. You will need to determine if there is anything suspicious in your findings.

### Files Required

RockStar Corp has provided you with:

- A list of their network assets: [Rock Star Server List](resources/Rockstarserverlist.xlsx)
- Instructions to scan their network.

### Your Goal

You will follow instructions to work through four phases of the network assessment. For each phase, include the following:

- The steps and commands used to complete the tasks.

- A summary of your findings for each testing phase.

- Any network vulnerabilities discovered.

- Findings associated with a hacker.

- Recommended mitigation strategy.

- Document the OSI layer where the findings were found.


### Topics Covered in Your Assignment

- Subnetting
- CIDR
- IP Addresses
- `fping`
- OSI Model and OSI Layers
- Protocols
- Ports
- Wireshark
- PCAP Analysis
- `DNS`
- `HTTP`
- `ARP`
- `Syn` Scan
- `TCP`
- `nslookup`
- Network Vulnerability Assessments
- Network Vulnerability Mitigation

---

### Network Vulnerability Assessment Instructions

Please note that you will be using your Vagrant virtual machine for this homework.

### **Phase 1**: _"I'd like to Teach the World to `Ping`"_

* - Determine the IPs for the Hollywood office and run `fping` against the IP ranges in order to determine which IP is accepting connections.
* `12.205.151.91/24`
* `15.199.151.91/24`
* `15.199.158.91/28`
* `15.199.141.91/28`
* `15.199.131.91/28`
* `15.199.121.91/28`
* `15.199.111.91/28`
* `15.199.100.91/28`
* `15.199.99.91/28`
* `15.199.98.91/28`
* `15.199.97.91/28`
* `15.199.96.91/28`
* `15.199.95.91/28`
* `15.199.94.91/28`
* `11.199.158.91/28`
* `167.172.144.11/32`
* `11.199.141.91/28`
* `11.199.131.91/28`
* `11.199.121.91/29`
* `11.199.111.91/28`
* `11.199.100.91/32`
* `11.199.99.91/24`
* `11.199.98.91/28`

 * - RockStar Corp doesn't want any of their servers, even if they are up, indicating that they are accepting connections.
     - Use `fping <IP Address>` and ignore any results that say "Request timed out".
     - If any of the IP addresses send back a Reply, enter Ctrl+C to stop sending requests.

* `fping -g 12.205.151.91/24`- Unreachable
* `fping -g 15.199.151.91/24`- Unreachable
* `fping -g 15.199.158.91/28`- Unreachable
* `fping -g 15.199.141.91/28`- Unreachable
* `fping -g 15.199.131.91/28`- Unreachable
* `fping -g 15.199.121.91/28`- Unreachable
* `fping -g 15.199.111.91/28`- Unreachable
* `fping -g 15.199.100.91/28`- Unreachable
* `fping -g 15.199.99.91/28`- Unreachable
* `fping -g 15.199.98.91/28`- Unreachable
* `fping -g 15.199.97.91/28`- Unreachable
* `fping -g 15.199.96.91/28`- Unreachable
* `fping -g 15.199.95.91/28`- Unreachable
* `fping -g 15.199.94.91/28`- Unreachable
* `fping -g 11.199.158.91/28`- Unreachable
* `fping -g 167.172.144.11/32`- Is alive
* `fping -g 11.199.141.91/28`- Unreachable
* `fping -g 11.199.131.91/28`- Unreachable
* `fping -g 11.199.121.91/29`- Unreachable
* `fping -g 11.199.111.91/28`- Unreachable
* `fping -g 11.199.100.91/32`- Unreachable
* `fping -g 11.199.99.91/24`- Unreachable
* `fping -g 11.199.98.91/28`- Unreachable
Results of the `fping` commands:
![fping-results](./fping-results.png)
### **Phase 2**:  _"Some `Syn` for Nothin`"_

* With the IP(s) found from Phase 1, determine which ports are open:

sudo nmap -sS 167.172.144.11
Port 22/tcp - Open
OSI Layer - Transport Layer

### Phase 3: _"I Feel a `DNS` Change Comin' On"_


nslookup rollingstone.com - 151.101.128.69
ssh jimi@167.172.144.11 yes hendrix
cat /etc/hosts
exit
nslookup 98.137.246.8
OSI Layer - Application Layer


 ### Phase 4:  _"Sh`ARP` Dressed Man"_

ssh jimi:167.172.144.11 hendrix
ls /etc
cat /etc/packetcaptureinfo.txt
Hacker on POST /formservice/
Hacker has MAC address of 00:0c:29:1d:b3:b1


---

© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
