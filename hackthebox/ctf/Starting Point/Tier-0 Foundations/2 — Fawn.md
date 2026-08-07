   

![](https://cdn-images-1.medium.com/max/1000/0*Th2DaVQVQYDIsJ0X.png)

I started by scanning the target with Nmap to identify the exposed services. The scan revealed that **FTP** was running on port **21**, making it the primary service to investigate.


![](https://cdn-images-1.medium.com/max/1000/0*pQx3Lj67zHamJsG_.png)

The FTP server allowed anonymous authentication. I logged in using `anonymous` as both the username and password, which granted access to the server. After gaining access, I listed the available files and searched for the flag.

![](https://cdn-images-1.medium.com/max/1000/0*66nkHDRlyRh8BNeG.png)


![](https://cdn-images-1.medium.com/max/1000/0*uVt2YQ0mf8v3eI8R.png)

![](https://cdn-images-1.medium.com/max/1000/0*YdajCSHRVEHc0ImA.png)

flag : 035db21c881520061c53e0536e44f815