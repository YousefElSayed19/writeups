---

**Do Not Disturb** is a realistic Boot2Root challenge that combines web exploitation, post-exploitation, and privilege escalation. Rather than presenting a single vulnerability, the room encourages following an attack chain where each discovery naturally leads to the next. Success depends on careful enumeration, understanding application behavior, and recognizing how small misconfigurations can eventually result in full system compromise.

---

After identifying the web application, one detail immediately caught my attention. The server was running **Express.js**, and my first thought was that there was a good chance the backend relied on MongoDB. Whenever I see that combination, one of the first things I like to check is whether the authentication logic is vulnerable to **NoSQL Injection**.

![](https://cdn-images-1.medium.com/max/1000/1*50SEwa2SJMUD_GbfyH9tiQ.png)

  

I intercepted the login request with **Burp Suite** and replaced both the username and password parameters with the classic `$ne` payload. Instead of rejecting the request, the application accepted it and redirected me to the **/staff** dashboard. At that point it was clear that the login mechanism trusted my input more than it should have, and I now had access to the staff area.

![](https://cdn-images-1.medium.com/max/1000/1*O245Zh9txdfkjsyU4c2CxA.png)

![](https://cdn-images-1.medium.com/max/1000/1*D1ia6xdpYLdPfK6y0KlOig.png)

---

Once inside the dashboard, I shifted my attention to the application’s functionality. Since Express applications often use **EJS** as their template engine, I wanted to verify whether user input was rendered safely. I injected a simple expression:

![](https://cdn-images-1.medium.com/max/1000/1*-D-h39gicABIw8OpJrdPiA.png)

<%= 7*7 %>

Instead of displaying the payload as text, the page evaluated it and returned **49**. That single response confirmed the presence of a **Server-Side Template Injection (SSTI)** vulnerability and completely changed the direction of the attack. Rather than looking for another vulnerability, I already had a direct path toward code execution.

![](https://cdn-images-1.medium.com/max/1000/1*nMMQ6CK4pXxxGTQoFcDCLA.png)

---

Using the SSTI vulnerability, I generated a reverse shell payload and started a Netcat listener on my machine. As soon as the payload was rendered by the server, I received an interactive shell as the **poolside** user. After stabilizing the shell, I retrieved the user flag before beginning a much more interesting phase of the challenge: local enumeration.

![](https://cdn-images-1.medium.com/max/1000/1*93crjSghwkkDL1NZiTbi3A.png)

![](https://cdn-images-1.medium.com/max/1000/1*VbRjZFmfdwPmFPhQ4vpvxw.png)

user flag : THM{w4rm_s3ss10n_h1j4ck3d}

---

  

The local system didn’t reveal anything obvious at first, so I continued checking users, groups, and running services. One account immediately stood out: **pipelinesvc**. Unlike the others, this user belonged to the **disk** group, which effectively grants direct access to the underlying storage device. That alone was interesting, but I still needed a way to execute commands as that user.

While inspecting the running processes, I noticed something even more valuable: a Node.js application running with the **Inspector Debugger** enabled on port **9229**. Although it was only listening on localhost, I forwarded the port over SSH and connected using Node’s built-in debugger. Executing a simple `id` command confirmed that I was now running code as **pipelinesvc**.

![](https://cdn-images-1.medium.com/max/1000/1*muztxBpZRng5pMVvW3qtTQ.png)

---

Now that I had code execution as a user belonging to the **disk** group, the privilege escalation became much simpler. Instead of searching for kernel exploits or weak sudo permissions, I took advantage of the group’s ability to access the raw disk directly.

![](https://cdn-images-1.medium.com/max/1000/1*1ZVdsBq8nt18Zd1FnD-kOg.png)

![](https://cdn-images-1.medium.com/max/1000/1*GM5gkZRszD3AZ9eB1TSrNA.png)

Using **debugfs**, I opened the filesystem from the raw partition itself, bypassing the normal Linux permission model. Navigating to the root user’s home directory, I finally reached **root.txt** and recovered the final flag.

![](https://cdn-images-1.medium.com/max/1000/1*RbpRGjIvAZ_-FVk3B39V6Q.png)

**root flag : THM{r4w_d1sk_4cc3ss_w4s_t00_much}**