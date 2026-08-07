

![](https://cdn-images-1.medium.com/max/1000/0*YXL0apbPWmDP2cBW.png)



![](https://cdn-images-1.medium.com/max/1000/0*kQHYhTfqcCVnQhPQ.png)

I started by running an Nmap scan against the target to identify the available services. The scan revealed that the **Telnet** service was open, making it the next point of investigation.


![](https://cdn-images-1.medium.com/max/1000/0*EGZw6Gg4VADURSGx.png)

Since the Telnet service was exposed, I connected to it using the Telnet client. After checking the available login options, I successfully authenticated and gained access to the target system.

![](https://cdn-images-1.medium.com/max/1000/0*Vur-C0fERT1kbWnF.png)

I connected to the exposed Telnet service and logged in as the **root** user without requiring a password. After gaining access, I confirmed my privileges using `whoami` and located the `flag.txt` file in the root user's home directory. Finally, I read the flag to complete the machine.

![](https://cdn-images-1.medium.com/max/1000/0*S_N1vKfV_C1GkUDT.png)

Flag : b40abdfe23665f766f9c61ecba8a4c19