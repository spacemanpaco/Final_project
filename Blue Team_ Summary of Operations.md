**Blue Team: Summary of Operations**


**Table of Contents**
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further


**Network Topology**


The following machines were identified on the network:
- Azure Virtual Machine
  - **Operating System** - Windows
  - **Purpose**: Houses Hyper-V Manager with closed network of Virtual Machines
  - **IP Address**: 192.168.1.1
- ELK VM
  - **Operating System**: Linux
  - **Purpose**: Houses ELK stack to monitor Target 1 VM
  - **IP Address**: 192.168.1.100
- Kali VM
  - **Operating System**: Linux
  - **Purpose**: Penetration machine used to hack Target 1
  - **IP Address**: 192.168.1.90
- Target 1
  - **Operating System**: Linux
  - **Purpose**: Hosts vulnerable Wordpress server to be hacked
  - **IP Address**: 192.168.1.110

Network Diagram: (/main/TargetEnvironment.pdf)
  

**Description of Targets**


The target of this attack was: `Target 1` (192.168.1.110).


Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:


**Monitoring the Targets**
A scan reveals the ports and services running on the machine to show areas that could be used as potential points of entry for hackers.
  
Scan results: (/Images/network_targets.jpg)

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:


**Name of Alert 1**
CPU Usage Monitor is implemented as follows:
WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes
  - **Metric**: When max() of system.process.cpu.total.pct over all documents 
  - **Threshold**: is above 0.5 for last 5 minutes
  - **Vulnerability Mitigated**: Designed to mitigate software that intentionally or unintentionally uses up CPU resources
  - **Reliability**: This alert is of high reliability because regular processes that take up CPU can also be monitored and configured.
  
CPU Usage Results: (Images/CPU-alert.jpg)


**Name of Alert 2**
Excessive HTTP Errors is implemented as follows:
WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes
  - **Metric**: when count() grouped over top 5 http.response.status_code
  - **Threshold**: is above 400 for last 5 minutes
  - **Vulnerability Mitigated**: Brute force/ Excessive enumeration on server
  - **Reliability**: This alert has high reliability because most 400 code errors are associated with users’ interaction with server/client and investigating them will help determine if they are malicious or not i.e. 401 and 403 are worth investigating compared to 404 or 400. Higher rates of these errors would also be suspicious.
  
HTTP Errors Results: (Images/HTTP-errors.jpg)


**Name of Alert 3**
HTTP Request Size Monitor is implemented as follows:
WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute
  - **Metric**: when sum() of http.request.bytes over all documents
  - **Threshold**: is above 3500 for last 1 minute
  - **Vulnerability Mitigated**: Mitigate Denial of Service or XSS attack
  - **Reliability**: Low reliability because there are a lot of false positives on this alert and bad actors can split up their requests into multiple deployments or unusually large HTTP requests which are not malicious
  
HTTP Request Results: (Images/HTTP-request.jpg)


**Suggestions for Going Further**


The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:


- Vulnerability 1
  - **Patch**: Virus/ Malware Hardening with Host Based Intrusion Detection System
  - **Why It Works**: Monitoring of network packets before being accepted to the server will allow for filtering of malicious network traffic. HIDS must be configured with a network packet sniffing tool such as Wireshark or Snort to be able to organize the packets.


- Vulnerability 2
  - **Patch**: Code Injection Hardening
  - **Why It Works**: By making sure the URL path cannot be manipulated through manual enumeration, the server will not accept code manipulation to divulge sensitive information or be compromised further.


- Vulnerability 3
  - **Patch**: Implement a Web Application Firewall that can mitigate malicious requests
  - **Why It Works**: Abnormal requests can be blocked with a WAF as some HTTP requests would require bigger data to be used in the requests such as POST requests.
