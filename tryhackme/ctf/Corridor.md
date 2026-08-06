![](https://cdn-images-1.medium.com/max/1000/0*-AdiyVZnsA6lkUtL.png)

In this challenge, we are presented with a web application that simulates a corridor containing multiple doors. Our objective is to identify and exploit an **Insecure Direct Object Reference (IDOR)** vulnerability to gain access to a hidden location and retrieve the flag.

At the start of the challenge, the following information is provided:

- A target web application representing a corridor with multiple doors.
- Each door redirects to a URL containing a unique hexadecimal value.
- The challenge hints that these hexadecimal values resemble hashes.
- No credentials or additional information are provided, meaning the attack relies entirely on analyzing and manipulating the URL identifiers.

Our goal is to determine how these identifiers are generated and use that knowledge to access resources that are not directly exposed through the application’s interface.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*baOrS9GzEG8uf_t1.png)

After connecting to the TryHackMe VPN using my Kali Linux virtual machine and launching the challenge, I accessed the target web application. The website displayed a corridor containing **13 different doors**, with each door leading to a separate page. At this stage, no credentials or additional information were provided, so the only available approach was to carefully inspect the application’s behavior and structure.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*HCo5JUUF2jntLc2M.png)

After spending some time exploring the application and examining the available pages, I noticed that each door redirected to a unique URL containing a different hexadecimal string. By analyzing these values, I discovered that every identifier was an **MD5 hash** rather than a plain numeric value. This indicated that the application was using hashed identifiers for its resources, which became the key clue for understanding how the application handled object references and eventually identifying the underlying **IDOR** vulnerability.

![](https://cdn-images-1.medium.com/max/1000/0*d28cqH9lueSnScLm.png)

When I tried to make this text readable or correct, I found that it represents a correct number, which is

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*Qt0mbi9mOQReueYn.png)

With a little more research and the existence of 13 different doors, each numbered from 1 to 13, it seems there’s a hidden door containing the flag. This could be door 0 or door 14. Now we need to convert the number 0 to an MD5 hash to try and reach door 0.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*_EJzmDfPu_R1j5y2.png)

Wow, easier than I expected, and the flag was already on the door. :DD

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*KPTdoFoeo5m5OCQU.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*GQsb_8XeaWdEzksv.png)