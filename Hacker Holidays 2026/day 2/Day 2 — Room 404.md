This room focuses on identifying and exploiting an exposed Git repository on a web server. Rather than targeting a traditional web vulnerability, the challenge demonstrates how accidentally exposing development resources can disclose sensitive information. By recovering the repository and analyzing its contents, it is possible to identify files that were never intended to be publicly accessible, ultimately leading to the discovery of the flag. The room highlights the importance of securing development artifacts before deploying applications to production.

now lets open website

![](https://cdn-images-1.medium.com/max/1000/1*vTEPj79siqXcp5Hj7s8-qQ.png)

nothing there lets do fuzz to check **Directories :**

![](https://cdn-images-1.medium.com/max/1000/1*QzKgsUOGEF6Iz8kbmvL7dg.png)

and we found .get let’s download them in kali by git-dumber :

![](https://cdn-images-1.medium.com/max/1000/1*YNtpJ_PfYIsTdrvxutEqyQ.png)

![](https://cdn-images-1.medium.com/max/1000/1*Ro1-Im7zL9mLx2e_yt6Wvw.png)

and we found flag in readme.md , it’s was very easy :D

#### Flag : THM{byt3_l0tus_n3v3r_f0rg3ts}