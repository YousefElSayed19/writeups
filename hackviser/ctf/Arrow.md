### Verify Target Reachability

The first step was to confirm that the target machine was reachable over the network. I sent ICMP Echo Requests using the `ping` command to verify connectivity. The successful replies confirmed that the host was online and accessible, allowing me to proceed with further enumeration

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*T05b91o9_8uCJfgc.png)

### Service Enumeration with Nmap

###   

After confirming that the target machine was reachable, the next step was to enumerate the exposed services using **Nmap (Network Mapper)**.

Nmap is one of the most widely used network scanning tools in penetration testing. It is designed to discover active hosts, identify open ports, detect running services, and gather valuable information about a target system. This information helps an attacker or penetration tester understand the attack surface before attempting exploitation.

I executed the following command to scan the target:

nmap <target>

**_This is the simplest use of the tool, and we don’t need to use any parameters with the command at the moment because it’s not required_**_._

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*IDi8s30LH1lt-xg2.png)

The scan revealed that **port 23/TCP** was open and running the **Telnet** service.

### Telnet Service

###   

**Telnet** is a remote access protocol that allows users to connect to another machine and interact with its command-line interface over a network. Once authenticated, the user can execute commands on the remote system as if they were sitting in front of it.

One of Telnet’s biggest security weaknesses is that **all communication, including usernames and passwords, is transmitted in plain text without encryption**. This means anyone capable of intercepting the network traffic can read the credentials and the transmitted data, making Telnet an insecure protocol for modern environments.

Because of these security issues, Telnet has largely been replaced by **SSH (Secure Shell)**, which encrypts all communication between the client and the server.

### Connecting to the Telnet Service

###   

Since the Nmap scan confirmed that the Telnet service was accessible, the next step was to establish a connection to the target.

A Telnet connection can be initiated using the following command:

telnet <Target>

If the service is configured to allow authentication, it will prompt for a username and password. Once valid credentials are provided, the user gains an interactive shell on the remote system with the privileges assigned to that account.

In penetration testing, discovering an exposed Telnet service is significant because:

- It provides a direct remote login interface.
- Credentials are transmitted without encryption.
- Weak or default credentials can often lead to unauthorized access.
- Successful authentication may result in immediate shell access to the target machine.

The next step was to authenticate using the provided credentials and begin interacting with the remote system.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*Pnoy61D-6CEgXBU7.png)

### Authenticating to the Target

###   

Upon connecting to the Telnet service, the server prompted for authentication by requesting a username and password.

For this lab, the default credentials were:

Username: root  
Password: root

After entering the credentials, the authentication was successful, and I was granted access to the target system through an interactive shell.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*EJKhcz35qpp_AbsK.png)

**_Successful Authentication_**

### Gaining Shell Access

###   

Successful authentication provided direct command-line access to the remote machine with the privileges assigned to the **root** account. Since **root** is the administrative user on Linux systems, logging in with this account grants full control over the operating system.

From this point, I was able to execute Linux commands, navigate the file system, inspect directories, and gather information from the target machine.

This demonstrates one of the major risks of exposing Telnet with weak or default credentials. An attacker who discovers the service and successfully authenticates can gain immediate administrative access, potentially leading to complete system compromise.

### Verifying the Obtained Access

###   

After successfully logging into the system, I executed a few basic Linux commands to verify the level of access and gather initial information about the environment.

The first command was:

whoami

The `whoami` command displays the username of the currently logged-in user. The output confirmed that I was authenticated as **root**, indicating that I had administrative privileges on the target machine.

Next, I executed:

pwd

The `pwd` (Print Working Directory) command displays the current working directory. The output showed that my session started in the **root user's home directory**, confirming the initial location within the file system after authentication.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*LQJOot1qwjDlAqPt.png)

### Conclusion

This lab demonstrated the importance of performing systematic enumeration before attempting exploitation. After verifying that the target was reachable, I used **Nmap** to identify exposed services and discovered that **Telnet** was running on the target system.

Since Telnet provides remote command-line access and transmits credentials without encryption, it presents a significant security risk when exposed to untrusted networks. By authenticating with the default credentials (`root:root`), I successfully obtained a remote shell with **root privileges** and verified my access using standard Linux commands.

This exercise highlights two critical security lessons:

- Avoid exposing the **Telnet** service on production systems.
- Never use **default or weak credentials**, especially for privileged accounts.