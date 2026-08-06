![](https://cdn-images-1.medium.com/max/1000/0*d4iyxrpltYApOpvM.png)

**Attacktive Directory** is a beginner-friendly Active Directory room on TryHackMe that focuses on common enumeration and exploitation techniques used against Windows Domain Controllers.

In this room, I learned how to identify Active Directory services, enumerate valid domain users through Kerberos, perform AS-REP Roasting, crack Kerberos hashes, access SMB shares, dump NTDS credentials, and finally use Pass-the-Hash to gain administrative access and capture all flags.

This write-up documents the complete attack path, including the tools, commands, and techniques used during the assessment.

### Task 1 — Deploy The Machine

 

Before starting the challenge, I connected my Kali Linux machine to the TryHackMe VPN using the provided OpenVPN configuration file. After verifying the VPN connection, I deployed the target machine and confirmed that it was online and reachable.

At this point, both my attacking machine and the target machine were ready, allowing me to begin the enumeration phase.

![](https://cdn-images-1.medium.com/max/1000/0*ejHyBuiQ-IQvpp6n.png)

![](https://cdn-images-1.medium.com/max/1000/0*anvcX--CkWiFeXbe.png)

### Task 2 — Setup

 

Before starting the enumeration process, I verified that my Kali Linux machine had all the required tools installed. Since I was using my own virtual machine instead of the TryHackMe AttackBox, I needed to ensure that Python and the required penetration testing tools were available.

### Checking Python Installation

 

First, I checked whether Python 3 was already installed on my system.

python3 --version


I also verified that `pip` was available for installing Python packages.

pip3 --version
  

If Python was not installed, it could be installed using:

sudo apt update  
sudo apt install python3 python3-pip python3-venv -y

  

After confirming that Python was working correctly, I proceeded with installing the required tools.

### Installing Impacket

 

Impacket is a collection of Python scripts used to interact with Windows protocols and Active Directory. Throughout this room, it is used for tasks such as AS-REP Roasting, SMB enumeration, and dumping NTDS credentials.

First, I cloned the Impacket repository.

git clone https://github.com/fortra/impacket.git /opt/impacket
  

Then I moved into the project directory.

cd /opt/impacket


Next, I installed all required Python dependencies.

pip3 install -r requirements.txt


Since Kali Linux follows **PEP 668**, installing packages globally using `pip` may return the following error:

error: externally-managed-environment
  

To continue the installation, I used:

pip3 install -r requirements.txt --break-system-packages

  
Finally, I installed Impacket.

pip3 install . --break-system-packages

  

To verify that the installation was successful, I checked one of the Impacket tools.

find / -name GetNPUsers.py 2>/dev/null

  

The output confirmed that the script was successfully installed.

### Installing BloodHound and Neo4j

 

BloodHound is an Active Directory enumeration tool that uses Neo4j as its graph database.

Both packages can be installed with the following command:

sudo apt update  
sudo apt install bloodhound neo4j -y

  

After the installation was complete, all the required tools were ready, and I could move on to the enumeration phase.

### Task 3 — Welcome to Attacktive Directory

 

After preparing my environment, I started the reconnaissance phase by performing an Nmap scan against the target machine. The goal of this step was to identify open ports, running services, and gather as much information as possible about the target before attempting any exploitation.

I used the following command:

nmap -A -Pn <targe_ip>

  

### Command Explanation

 

- `-A` enables aggressive detection, including service version detection, OS detection, default scripts, and traceroute.
- `-Pn` skips host discovery and assumes the target is online.

### Nmap Results

 

The scan revealed several interesting services commonly found in an Active Directory Domain Controller.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*e9XTzhOMXt-iPfvN.png)

### Enumerating SMB

 

Since SMB (ports **139** and **445**) was available, the next step was to enumerate the SMB service.

The common tool used for this purpose is:

ANSWER 1 :

enum4linux

  

`enum4linux` is a Linux enumeration tool that gathers information from Windows systems through SMB. It can retrieve information such as:

- NetBIOS names
- Domain names
- Users
- Groups
- Shares
- Password policies

A basic enumeration can be performed with:

enum4linux -a <targe_ip>

  

### Discovering the Domain Information

 

The Nmap service detection also revealed useful information about the Active Directory domain.

From the LDAP service:

Domain: spookysec.local

  

From the RDP information:

NetBIOS_Domain_Name: THM-AD  
DNS_Domain_Name: spookysec.local

  

This immediately answered two important questions.

### NetBIOS Domain Name

 

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*V4DoI8Vcu7y0nFGX.png)

ANSWER 2:

THM-AD

  

### Active Directory Domain

 

![](https://cdn-images-1.medium.com/max/1000/0*FBy2FRUyx0FSvoiX.png)

ANSWER 3 :

spookysec.local

  

It is very common for Active Directory environments to use the `**.local**` top-level domain internally, even though it is not a valid public Internet TLD.

### Task 4 — Enumerating Users via Kerberos

 

After identifying that Kerberos (TCP Port **88**) was running on the Domain Controller, the next step was to enumerate valid domain users.

Kerberos is the default authentication protocol used in Active Directory environments. One of its interesting features is that it responds differently when a valid username is supplied compared to an invalid one. This behavior allows us to enumerate existing users without knowing their passwords.

To perform this enumeration, I used **Kerbrute**, a tool developed by Ronnie Flathers that can identify valid usernames, perform password spraying, and brute-force attacks against Kerberos.

### Enumerating Valid Usernames

 

The room provides a custom user list to speed up the enumeration process. Using that list, I executed the following command:

kerbrute userenum --dc <target_ip> -d spookysec.local userlist.txt

  

You can download userlist.txt from T[**here**](https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt)

### Command Explanation

 

- `userenum` → Enumerates valid usernames.
- `--dc` → Specifies the Domain Controller IP address.
- `-d` → Specifies the Active Directory domain.
- `userlist.txt` → The list of usernames to test.

Kerbrute sent authentication requests to the Domain Controller and identified which usernames were valid based on the Kerberos responses.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*ZHLFwNy8vohoDpkX.png)

### Results

 

The enumeration successfully identified several valid domain accounts. Among them, two accounts immediately stood out because of their names and potential privileges.

### ANSWER 1

 

**What command within Kerbrute will allow us to enumerate valid usernames?**

userenum

  

### ANSWER 2

 

**What notable account is discovered?**

svc-admin

  

This account appears to be a service account (**Service Administrator**), making it a high-value target for further attacks.

### ANSWER 3

 

**What is the other notable account discovered?**

backup

  

The **backup** account is also interesting because backup operators often have elevated privileges in Active Directory environments, making it another valuable target during the exploitation phase.

### Task 5 — Exploitation Abusing Kerberos

 

After successfully identifying valid domain users, the next step was to determine whether any of these accounts were vulnerable to **AS-REP Roasting**.

### What is AS-REP Roasting?

 

Normally, Kerberos requires users to perform **Pre-Authentication** before the Key Distribution Center (KDC) issues a Ticket Granting Ticket (TGT). However, if an account has the **“Do not require Kerberos preauthentication”** option enabled, anyone can request an authentication ticket for that user without knowing their password.

The returned ticket is encrypted using the user’s password hash, making it possible to perform an offline password cracking attack.

### Requesting AS-REP Tickets

 

To check whether any of the discovered users were vulnerable, I used the **GetNPUsers.py** script from the Impacket toolkit.

python3 /opt/impacket/examples/GetNPUsers.py spookysec.local/ -dc-ip 10.112.161.212 -usersfile userlist.txt -no-pass

  

### Command Explanation

 

- `spookysec.local/` → Specifies the target domain.
- `-dc-ip` → Specifies the Domain Controller IP address.
- `-usersfile` → Uses the list of valid usernames obtained from Kerbrute.
- `-no-pass` → Requests Kerberos tickets without providing a password.

The script queried the Domain Controller for all users in the list and checked whether any account had **Kerberos Pre-Authentication disabled**.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*le4kCoyEoyzOfpfK.png)

### Results

 

The scan revealed that only one account was vulnerable to AS-REP Roasting.

### ANSWER 1

 

**Which user account can you query a ticket from with no password?**

svc-admin

  

The tool returned an **AS-REP hash** for this account, which could then be cracked offline.

### Identifying the Hash Type


The returned hash started with:

$krb5asrep$23$

  

Looking at the **Hashcat Example Hashes Wiki**, this format corresponds to:

### ANSWER 2

**Kerberos 5 AS-REP etype 23**

### ANSWER 3

**Hashcat Mode**

18200

  

This mode tells Hashcat how to correctly process and crack the retrieved Kerberos hash.

### Cracking the Hash

 

I saved the retrieved hash into a file named **hash.txt** and used Hashcat with the modified password list provided by the room.

john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

  

### Command Explanation

 

- `john` → Starts the John the Ripper password cracking tool.
- `--wordlist=/usr/share/wordlists/rockyou.txt` → Specifies the **RockYou** wordlist, which contains millions of common passwords to perform a dictionary attack.
- `hash.txt` → Contains the captured **Kerberos 5 AS-REP** hash retrieved using `GetNPUsers.py`.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*Hr--uVQ2KkCjPF2F.png)

### ANSWER 4

 

**Recovered Password**

management2005

  

The password belonged to the **svc-admin** account and would later be used during the next stages of the attack.

### Task 6 — SMB Share Enumeration

 

After recovering the credentials for the **svc-admin** account, I gained authenticated access to the Active Directory environment. The next step was to enumerate the available SMB shares to identify any files or sensitive information that could assist in further privilege escalation.

### Listing Available SMB Shares

 

To enumerate the SMB shares, I used the `smbclient` utility.

smbclient -L //10.112.161.212 -U svc-admin

  

After entering the password (`management2005`), the server returned a list of all available shares.

### Command Explanation

 

- `smbclient` → A command-line SMB client used to interact with Windows file shares.
- `-L` → Lists all available SMB shares on the target server.
- `//10.113.184.117` → The target Domain Controller.
- `-U svc-admin` → Authenticates using the **svc-admin** account.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*SiE2EZpbTDBnseQE.png)

### Enumeration Results

 

The SMB server exposed six available shares.

### ANSWER 1

 

**What utility can we use to map remote SMB shares?**

smbclient

  

### ANSWER 2

 

**Which option will list shares?**

-L

  

### ANSWER 3

 

**How many remote shares is the server listing?**

6

  

### Accessing the Backup Share

 

Among the listed shares, the **backup** share was accessible using the recovered credentials.

I connected to it using:

smbclient //10.112.161.212/backup -U svc-admin

  

After successfully connecting, I listed the files inside the share.

ls

  

A text file was found containing what appeared to be an encoded string.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*fUsuvCddE3TAqSbw.png)

Now file in the desktop .

### ANSWER 4

 

**Which share contains the text file?**

backup

  

### ANSWER 5

 

**What is the content of the file?**

YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw

  

### Decoding the File

 

The string appeared to be **Base64-encoded**, so I decoded it using the following command:

echo "YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw" | base64 -d

  

The decoded output revealed another set of credentials.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*GkJMG3R-XgmZxlkD.png)

### ANSWER 6

 

**Decoded Content**

backup@spookysec.local:backup2517860

  

These credentials belonged to the **backup** account and would be used in the next phase to dump the Active Directory database and continue the privilege escalation process.

### Task 7: Domain Privilege Escalation

 

After obtaining the credentials of the **backup** user, we started looking for additional privileges that this account might have inside the Active Directory domain.

The username **backup** was interesting because it suggested that this account might have backup-related permissions on the Domain Controller.

During enumeration, we discovered that this account had the **DCSync privilege**. This permission allows a user to request password hashes from the Domain Controller and effectively perform an Active Directory synchronization attack.

To exploit this privilege, we used **Impacket’s secretsdump.py** tool to extract password hashes from Active Directory.

### Dumping Hashes

 

Command used:

impacket-secretsdump spookysec.local/backup:backup2517860@10.112.161.212

  

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*TqQa-Lo0Tk5sjmmg.png)

The tool successfully extracted NTLM hashes from the Domain Controller.

### Answers

 

### Answer 1:

 

**What method allowed us to dump NTDS.DIT?**

DRSUAPI

  

### Answer 2:

 

**What is the Administrator’s NTLM hash?**

0e0363213e37b94221497260b0bcb4fc

  

### Answer 3:

 

**What method of attack could allow us to authenticate as the user without the password?**

Pass The Hash

  

### Answer 4:

 

**Using Evil-WinRM what option will allow us to use a hash?**

-H

  

### Exploiting Administrator Access

 

After obtaining the Administrator NTLM hash, we could authenticate without knowing the actual password by using the **Pass The Hash** technique.

Using Evil-WinRM, we can provide the hash with the `-H` option:

evil-winrm -i 10.112.161.212 -u Administrator -H 0e0363213e37b94221497260b0bcb4fc

  

This gave us Administrator-level access to the Domain Controller.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*xc5Pc6goqvM4ZOZs.png)

### Task 8: Flag Submission

 

After successfully performing the **DCSync attack** and obtaining the Administrator NTLM hash, I used the **Pass The Hash** technique to authenticate as the Administrator user.

Using Evil-WinRM with the extracted hash gave me an administrative shell on the Domain Controller.

### Command Used:

 

evil-winrm -i 10.112.161.212 -u Administrator -H 0e0363213e37b94221497260b0bcb4fc

  

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*ISHOPaXnjiva8lBh.png)

This provided Administrator-level access without requiring the actual password.

Although I did not directly log in through the Administrator desktop, the obtained privileges allowed me to access the system with full administrative permissions and retrieve the required flags.

### Retrieving Flags

 

The room required collecting flags from the desktops of three different users:

### Answer 1: svc-admin

 

The first flag belonged to the **svc-admin** account.

**By using Pass-the-Hash, I obtained Administrator-level privileges, which allowed me to access files and data from all users on the Domain Controller.**

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*jRTPdKgDTCW6WUyt.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*atO0Ee3rU4ZAI-fB.png)

Flag:

TryHackMe{K3rb3r0s_Pr3_4uth}

  

This flag was obtained after compromising the svc-admin account through **AS-REP Roasting** and recovering its password.

### Answer 2: backup

 

The second flag belonged to the **backup** account.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*vytexDh2NWYl5_Pz.png)

Flag:

TryHackMe{B4ckM3UpSc0tty!}

  

The backup account credentials were discovered by accessing the SMB backup share and decoding the Base64 encoded file.

### Answer 3: Administrator

 

The final flag belonged to the Administrator account.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*TwBtCdw5C-sR4C-j.png)

Flag:

TryHackMe{4ctiveD1rectoryM4st3r}

  

After performing a **DCSync attack** against the Domain Controller and extracting the Administrator NTLM hash, I used **Pass The Hash** with Evil-WinRM to obtain administrative access and retrieve the final flag.

> **_Finally, the Attacktive Directory room has been completed successfully. I was able to compromise the domain step by step, escalate my privileges, and capture all three flags. This room was a great introduction to Active Directory attacks and common techniques used in real-world penetration testing :((_**


Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*ncJvGL75y5K5e_0p.png)

[LinkedIn](https://www.linkedin.com/in/yousefelsayed20/) And thanks for reading my write-up. I hope you found it useful and learned something new from this walkthrough. < 3