**Red Team: Summary of Operations**


**Table of Contents**
- Exposed Services
- Critical Vulnerabilities
- Exploitation


**Exposed Services**

Nmap scan results for each machine reveal the below services and OS details:

`’’bash

$ nmap -sV 192.168.1.100/24


This scan identifies the services below as potential points of entry:
- Target 1
  - List of Exposed Ports/Services

- 22/tcp           open        SSH                OpenSSH
- 80/tcp           open        http               Apache
- 111/tcp          open        rpcbind            2-4
- 139/tcp          open        netbios-ssn        Samba
- 445/tcp          open        netbios-ssn        Samba

 
**List of Critical Vulnerabilities**

The following vulnerabilities were identified:

CVE-2014-0226 - allows remote attackers to cause a denial of service (heap-based buffer 
overflow), or possibly obtain sensitive credential information or execute arbitrary code. The first vulnerability allows hackers to potentially access sensitive information. With the scan that was used to find vulnerabilities it will also give the bad actor directories marked as interesting highlighting that there may be something of use in those directories.


CVE-2014-3503 - pertains to weak random values to generate passwords, which makes it easier for remote attackers to guess the password via a brute force attack. Password policies must be adhered to because many brute force attacks rely on simple password combinations to crack the account. Sometimes a brute force is not required as this user used their name as the password which is also unadvisable.


CVE-2000-0525 - OpenSSH does not properly drop privileges when the UseLogin option is enabled, which allows local users to execute arbitrary commands by providing the command to the ssh daemon. With an SSH connection that comes from an unknown IP address, the system can expose information that the hacker can further leverage to manipulate the system even more.

(Images/target1_vulnscan.jpg)


**Exploitation**


The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  `flag1`: flag1{b9bbcb33e11b80be759c4e844862482d}
     **Exploit Used**
     
      - viewed HTML source code
      - used Page inspect
      (Images/flag_1.jpg)

  
  `flag2`: flag2{fc3fd58dcdad9ab23faca6e9a36e581c}
      **Exploit Used**
      
      - Used wpscan to enumerate users from the wordpress site, giving us target to exploit
      - by guessing a user password, an SSH connection was made to the web server
      - flag was found in the /var/www directory

    Commands        

    - wpscan --url http://192.168.1.110/wordpress --enumerate u
    - ssh michael@192.168.1.110
    - cat flag2.txt
    - (Images/flag_2.jpg)

  ‘flag3’: flag3{afc01ab56b50591e7dccf93122770cd2}
       **Exploit Used**
 
        - mySQL database config file was leveraged to find root user access to mySQL database
        - through investigation, SQL queries were made to find password hashes for Wordpress users
        - flag found in the table

    Commands        
    - mysql -u root -p ‘R@v3nSecurity’
    - show databases; use wordpress; show tables; select * from wp_posts;
    - (Images/flag_3.jpg)


  ‘flag4’: flag4{715dea6c055b9fe3337544932f2941ce}
          **Exploit Used**
          
          - through mySQL infiltration, found another user to use as opportunity to get to root user
          - used john the ripper on the password hashes collected from mySQL database
          - found password for other user and connected via SSH
          - this user can run python commands with sudo
          - root system OS must be leveraged to gain root access
          - flag found in home directory for root user
    Commands        
    - john wp_hashes.txt -wordlist=rockyou.txt
    - ssh steven@192.168.1.110
    - sudo python, import os, os.system(“/bin/bash”)
    - cat flag4.txt
    - (Images/flag_4.jpg)
