

First of all I am going to do an nmap scan:

```
nmap -Pn -sV -sC -p- 10.114.134.172                
Starting Nmap 7.95 ( https://nmap.org ) at 2026-08-06 21:25 EDT  
Nmap scan report for 10.114.134.172  
Host is up (0.064s latency).  
Not shown: 65533 filtered tcp ports (no-response)  
PORT     STATE SERVICE       VERSION  
80/tcp   open  http          Microsoft IIS httpd 10.0  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: IIS Windows Server  
| http-methods:   
|_  Potentially risky methods: TRACE  
3389/tcp open  ms-wbt-server Microsoft Terminal Services  
| rdp-ntlm-info:   
|   Target_Name: RETROWEB  
|   NetBIOS_Domain_Name: RETROWEB  
|   NetBIOS_Computer_Name: RETROWEB  
|   DNS_Domain_Name: RetroWeb  
|   DNS_Computer_Name: RetroWeb  
|   Product_Version: 10.0.14393  
|_  System_Time: 2026-08-07T01:28:32+00:00  
|_ssl-date: 2026-08-07T01:28:36+00:00; -1s from scanner time.  
| ssl-cert: Subject: commonName=RetroWeb  
| Not valid before: 2026-08-06T01:24:21  
|_Not valid after:  2027-02-05T01:24:21  
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 197.83 seconds
```

As you can see there are 2 ports open :

Port 80 — `http` - `Microsoft IIS httpd 10.0`

Port 3389 — `ms-wbt-server` - `Microsoft Terminal Services`

Web Enumeration

Visitinng the webpage at port 80, we get a `default IIS page`

![](https://cdn-images-1.medium.com/max/1000/1*cvRTKDveTyl93Fg4H-j6kg.png)

To enumerate the hidden directories , I used ffuf

```
┌──(shebu㉿kali)-[~/Desktop/thm/retro]  
└─$ ffuf -c -w /usr/share/wordlists/dirbuster/medium.txt -u http://retro.thm/FUZZ -fc 403,404 -fs 703

        /'___\  /'___\           /'___\         
       /\ \__/ /\ \__/  __  __  /\ \__/         
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\        
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/        
         \ \_\   \ \_\  \ \____/  \ \_\         
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3  
________________________________________________

 :: Method           : GET  
 :: URL              : [http://retro.thm/FUZZ](http://retro.thm/FUZZ)  
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/medium.txt  
 :: Follow redirects : false  
 :: Calibration      : false  
 :: Timeout          : 10  
 :: Threads          : 40  
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405  
 :: Filter           : Response status: 403,404  
 :: Filter           : Response size: 703  
________________________________________________

retro                   [Status: 301, Size: 146, Words: 9, Lines: 2]

```
here we go we found **_/retro_** dir 

Qn 1 - What is the hidden directory which the website lives on? — `/retro`

On visiting the `/retro` directory we get this webpage 

![](https://cdn-images-1.medium.com/max/1000/1*PF8D7iwPsFZdJnvfs687kg.png)

Seems like a fancy looking blog page .Taking a close look at the page , you can see that the author of the blog posts is `Wade`

Wade might possibly be a username which may come handy later .Lets note it down and enumerate further !

While looking at all the posts ,one particular post is quite interesting

  

![](https://cdn-images-1.medium.com/max/1000/0*gZloOLFKPw5xfVYx.png)

This posts has some comments on it 

![](https://cdn-images-1.medium.com/max/1000/0*fN42Wq-uqg_8waaU.png)

Could that be Wade’s password Let’s try logging in using xfreerdp 

xfreerdp /u:wade /p:parzival /cert:ignore /v:10.114.134.172

![](https://cdn-images-1.medium.com/max/1000/1*5-W3vWxhR57wS6H3qBAUPw.png)

And we’re in

![](https://cdn-images-1.medium.com/max/1000/1*caccfO_VD4rSWIEBfvIZ0g.png)

and we get the first flag ( user ) 

now let’s search for root (admin) flag 


The next thing I noticed is there was Google Chrome shortcut in desktop which was weird !  
Opening Chrome , we could see that the author of the box has a bookmark called NVD-CVE-2019–1388

![](https://cdn-images-1.medium.com/max/1000/1*bi6VMhJQqO-p6sYdh_bUQg.png)

  

But how do we get the privilege escalation?!

Pretty easy actually. Check out this site

### [swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md?source=post_page-----fe4599480267---------------------------------------)


### [Extract patchs and updates Architecture List all env variables List all drives Get current username List user privilege…](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md?source=post_page-----fe4599480267---------------------------------------)



[github.com](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md?source=post_page-----fe4599480267---------------------------------------)

You’ll find a link to an exploit for [https://github.com/SecWiki/windows-kernel-exploits/tree/master/CVE-2017-0213](https://github.com/SecWiki/windows-kernel-exploits/tree/master/CVE-2017-0213)

On your Kali machine, go and download the zip file for the x64 architecture.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*acdhUnU3k6ZdWLEV.png)

Unzip the executable and start a simple web server using Python

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/1*19D_g_aiU_CrLMwtpb-DIQ.png)

![](https://cdn-images-1.medium.com/max/1000/1*MzgbtSEVOCr6qulTuf2DsA.png)

![](https://cdn-images-1.medium.com/max/1000/1*wSCSJCbICuyKxXgAQanSSw.png)

The machine may complain about the file and asking if you want to discard it, of course you want to keep it.

Now simply run the executable as the user wade and you’ll be presented with an elevated administrator command prompt. Go and get your root.txt file..

![](https://cdn-images-1.medium.com/max/1000/1*weBNwpWqTO_8ObVtep1wQg.png)

Mission complete.