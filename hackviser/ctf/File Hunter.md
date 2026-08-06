### Introduction

In this warm-up lab, the objective was to enumerate an exposed **FTP (File Transfer Protocol)** service, establish a connection to the server, explore its contents, and retrieve a file containing user information. This exercise demonstrates the basics of FTP enumeration and highlights the risks of exposing sensitive files through misconfigured FTP services.

### Verifying Target Reachability

Before interacting with the target, I verified that the machine was reachable using the `ping` command. Receiving successful ICMP replies confirmed that the host was online and ready for further enumeration.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*GKlrQJ-6vSeJJmmO.png)

### Service Enumeration with Nmap

The next step was to enumerate the services running on the target using **Nmap (Network Mapper)**.

Nmap is a powerful network scanning tool used to identify active hosts, discover open ports, detect running services, and gather information about the target. This information helps determine potential attack vectors during a penetration test.

nmap -sV <trget-ip>

The scan revealed that **port 21/TCP** was open and running the **FTP (File Transfer Protocol)** service.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*sDTDtOhrwhabR0lH.png)

### Understanding FTP

**FTP (File Transfer Protocol)** is a standard protocol used to transfer files between a client and a server over a network. It allows users to upload, download, rename, and manage files stored on a remote server.

FTP servers usually require authentication using a username and password, although some servers allow **anonymous access** with limited permissions.

One major security limitation of traditional FTP is that it **does not encrypt credentials or transferred files**, making intercepted traffic readable by attackers.

### Connecting to the FTP Server

After identifying the FTP service, I connected to the server using the FTP client.

ftp <target-ip>

Upon connecting, the server prompted for a username. In this lab,  
 I authenticated using the anonymous account by entering:

Username: anonymous

The server accepted the anonymous login without requiring   
privileged credentials,granting access to the FTP server and   
allowing me to begin enumerating its contents.

Anonymous FTP access is a feature that allows users to connect   
to an FTP server without having a dedicated account.  
While it can be useful for sharing public files,  
it poses a security risk if sensitive data is exposed or   
if excessive permissions are granted.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*sgD99zAVjkLMFubX.png)

### Enumerating the FTP Server

###   

Once authenticated, I began exploring the FTP server.

To display all supported FTP commands, I used:

help

This command lists all available FTP commands supported by the server, making it easier to understand the available functionality.

### Discovering Available Files

Next, I listed the files stored on the FTP server.

ls

The output revealed the presence of a file that appeared to contain useful information.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*lHmW_Q0if2NUXCuS.png)

### Downloading the File

To retrieve the discovered file from the FTP server, I used the **get** command.

get <filename>

The file was successfully downloaded to my local machine for further analysis.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*NFJo8cxoliYb_QK0.png)

### Analyzing the Downloaded File

After downloading the file, I examined its contents and identified the user information requested by the lab.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*ukzbO20HkwlNUTWA.png)

### Conclusion

This lab introduced the fundamentals of **FTP enumeration** and demonstrated how exposed FTP services can unintentionally disclose sensitive information.

During the assessment, I:

- Verified the target’s availability.
- Enumerated services using **Nmap**.
- Identified an exposed **FTP** service.
- Authenticated to the FTP server.
- Explored the available commands and files.
- Downloaded a file using the **get** command.
- Retrieved the required user information.

This exercise highlights the importance of securing FTP services, disabling unnecessary anonymous access, enforcing strong authentication, and avoiding the storage of sensitive information in publicly accessible locations.