In this room, the objective was to abuse a flaw in the application’s reward system. The application allowed users to claim daily reward points, but due to a race condition in the backend, it was possible to send multiple requests simultaneously and receive the reward more than once. By exploiting this logic flaw, I was able to accumulate enough points to unlock the Whale Vault and retrieve the flag.

open site ip_targe:3000

![](https://cdn-images-1.medium.com/max/1000/1*Do6Tapgdht4RRRmK3KkGdA.png)

### Exploitation

First, I created a new user account and logged into the application.

After exploring the available functionality, I found the request responsible for claiming the daily reward. Under normal circumstances, this request could only be used once every 24 hours.

I intercepted the request using **Burp Suite** and sent it to **Turbo Intruder**. Then, I used a Python script to generate and send a large number of concurrent requests to the reward endpoint.

![](https://cdn-images-1.medium.com/max/1000/1*HspSJczzNC2nWxn8v6l4kg.png)

Because the server processed multiple requests at the same time before updating the user’s claim status, many of those requests were accepted successfully. As a result, my account received a huge number of reward points instead of just one daily reward.

![](https://cdn-images-1.medium.com/max/1000/1*7jfr4Cxq59g71XnDC7m8fw.png)

Once I had accumulated enough points, the **Whale Vault** became available. I opened it and successfully retrieved the flag.

![](https://cdn-images-1.medium.com/max/1000/1*FF8ajrYCSuu2TN8nSRPonA.png)

**Flag:** `THM{t0w3l_0n_th3_sunb3d_d0ubl3_sp3nt}`