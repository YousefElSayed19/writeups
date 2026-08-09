This room focuses on **network forensics** and demonstrates how attackers can use legitimate HTTP traffic as a covert communication channel to exfiltrate data. Instead of transferring files directly, the attacker hides encoded information inside HTTP request headers, making the traffic appear harmless at first glance. By analyzing the provided PCAP file, identifying the suspicious beaconing pattern, extracting the hidden data, and decoding it, the concealed message can be reconstructed to reveal the flag. The challenge highlights how seemingly normal network traffic may conceal malicious activity and emphasizes the importance of packet analysis during incident response.

![](https://cdn-images-1.medium.com/max/1000/1*DIn-C4nuPPJt3KJ4x2YyHg.png)

The challenge provides a **PCAP** file containing captured network traffic. I opened the capture in **Wireshark** and began inspecting the available protocols.

  

![](https://cdn-images-1.medium.com/max/1000/1*Ug0ISw4KW03D5wmmcSjDlw.png)

After reviewing the traffic, I noticed repeated **HTTP GET** requests being sent every second to the same destination on **port 8080**, which matched the suspicious behavior described in the challenge.

![](https://cdn-images-1.medium.com/max/1000/1*hCaq5wRz4wYvfm6N-UJR8Q.png)

Next, I inspected the HTTP request headers and discovered that each request contained a **Cookie** named:

Unlike a normal session cookie, its value changed with every request.

![](https://cdn-images-1.medium.com/max/1000/1*yich179FLaKQZ8aen4qe9w.png)

> _The cookie values were extracted from each HTTP request and decoded individually from Base64 using_ **_Burp Suite Decoder_**_. This produced a sequence of raw bytes rather than readable text, indicating that an additional layer of encoding/obfuscation was present._

> _I then imported the reconstructed byte stream into_ **_CyberChef_** _and applied the required decoding operations to recover the original message, which revealed the challenge flag._

#### Flag: THM{V3r4_1s_w4tch1ng_0veR_y0u}