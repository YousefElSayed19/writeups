This room focuses on **web exploitation and Linux privilege escalation**, demonstrating how a seemingly harmless web application can become the starting point for a complete system compromise. The challenge begins with gathering information from the exposed web interface, leading to initial access through reused credentials. Further investigation reveals an insecure YAML deserialization vulnerability within the jukebox service, allowing arbitrary command execution. Finally, through local enumeration and credential reuse, it is possible to escalate privileges and obtain full administrative access. The room highlights the risks of exposing sensitive information, insecure deserialization, and poor credential management in real-world environments.

let’s open web and take a look

![](https://cdn-images-1.medium.com/max/1000/1*3_xbYJVxnxM_t91cMTzJVw.png)

hmm we need username and password to login :(

and after take a look about source page i found :

![](https://cdn-images-1.medium.com/max/1000/1*eiq63buzRqgYt1IyxdDCgA.png)

username= DJ / password DJ

![](https://cdn-images-1.medium.com/max/1000/1*3VadAwL6xwArvg3zF00rzg.png)

after login i found form lets start write command there

![](https://cdn-images-1.medium.com/max/1000/1*xTOLIJ00HYv6YJMWqkmVFg.png)

![](https://cdn-images-1.medium.com/max/1000/1*9rXfWpvUXOh5gJjml7TWQw.png)

![](https://cdn-images-1.medium.com/max/1000/1*Jb30mzhO1uqow_aAeOseYw.png)

![](https://cdn-images-1.medium.com/max/1000/1*1LSZuu-TAFL1kGPfkVJsXg.png)

![](https://cdn-images-1.medium.com/max/1000/1*u3CYaOcs-cMAQWJDF7t4_w.png)

![](https://cdn-images-1.medium.com/max/1000/1*OXt76XfUVQaeuBfVpI056A.png)

![](https://cdn-images-1.medium.com/max/1000/1*Yu6NQhO0G_gwcIZ4lWYOZg.png)

flag : THM{cr3d3nt14l_r3us3_4t_th3_bt34ch_b4r}