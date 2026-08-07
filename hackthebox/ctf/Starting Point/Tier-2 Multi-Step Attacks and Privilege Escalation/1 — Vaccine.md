![](https://cdn-images-1.medium.com/max/1000/1*o8zPoh_S65INOXjJ1PfiGA.png)

As the previous boxes, I use simple Nmap command to start port scanning and reconnaisance. Maybe most of the boxes in “Starting Point” don’t need deep enumeration but Nmap. For explanation about nmap syntax and its parameter, I usually visit this site: 

`nmap -sV -n -vv -Pn -T4 -p- -A` 10.129.28.66

The result has only 3 services for us to discover. Nmap scan reveals 3 open ports running, for FTP, SSH and Apache respectively.

PORT   STATE SERVICE REASON         VERSION  
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3  
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.0p1 Ubuntu 6build1 (Ubuntu Linux; protocol 2.0)  
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))

First of all, I considered the ftp credential i had grab from the box “Oopsie”, haha! It sounds great and I can easily login to this FTP server, use command below and enter user+password when it prompts:

`ftp` 10.129.28.66

Then, let’s use command `dir` or `ls -la` to display all things contained. You will find the sole file called `backup.zip` in the folder and use `get` command to download it to your Attacker machine.

![](https://cdn-images-1.medium.com/max/1000/1*yy0qCJRJs1ElFfc96no21w.png)

I was actually stuck at this step and don’t know how to move forward, and I decided to read the walkthrough 😌. Using “**John the Ripper**” tool to crack the archive password make me a little confused. So I thought HackTheBox never uses brute-force tools, it’s my fault. To extract this archive, we need a utility from John “`zip2john`" to accomplish. for `zip2john` usage, and also other articles for more interesting things. **Raj Chandel** is my favorite author and thank him a lot.

Now John cannot directly crack this key, first, we will have to change its format, which can be done using “`zip2john`".

`zip2john backup.zip > crack.txt`

![](https://cdn-images-1.medium.com/max/1000/1*JS2PjZ_mrQK6_gNKSfv8Eg.png)

![](https://cdn-images-1.medium.com/max/1000/1*3o6dO-wKjxP9pC6QzZR6DA.png)

and now password for unzip : 741852963

![](https://cdn-images-1.medium.com/max/1000/1*DVzt6NT459Gu0-xQy2-axA.png)

after unzip i found index.php and style.css

let’s open and see them

and when i open index.php i found :

![](https://cdn-images-1.medium.com/max/1000/1*lz3PCJKmXo0D5ypcafU7RA.png)

Looking at the PHP source code, we can find a login check. The input password is hashed and compared to the MD5 hash: `2cb42f8734ea607eefed3b70af13bbd3`. Another way, you can use this online to identify hash type

![](https://cdn-images-1.medium.com/max/1000/1*3qGK9OmCbHnoucNLWGQCBg.png)

password is : qwert789

![](https://cdn-images-1.medium.com/max/1000/1*rcQm2RtCD0WEctE_UQNLOw.png)

![](https://cdn-images-1.medium.com/max/1000/1*39C1Rtl0NT8n_RD9lR3UuA.png)

Using sqlmap to exploit:

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*NlfEJ3hgN0_fYNpF.png)

Now we got a initial shell:

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*0JHL1ka_cwTTDVRj.png)

I tried cd and read dashboard.php but failed

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*usT008lMc_xKHuif.png)

So i base64 encode it :)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*mvI1gn8t1LuYeTzu.png)

Decode it we will have the password

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*QGi-0nUXfynN3C3X.png)

SSH into postgres we have first flag

![](https://cdn-images-1.medium.com/max/1000/0*rINcqfjBzaa4aUZ4.png)

Checking sudo permission, i saw postgres can run vi to edit /etc/postgresql/11/main/pg_hba.conf as root without root password, so we can use this to gain root shell.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*qeMP1rAuHKhaqRAb.png)

sudo vi /etc/postgresql/11/main/pg_hba.conf  
and then `:!/bin/sh`

We got it now:

![](https://cdn-images-1.medium.com/max/1000/0*dUl7bpMgEW7cwXds.png)