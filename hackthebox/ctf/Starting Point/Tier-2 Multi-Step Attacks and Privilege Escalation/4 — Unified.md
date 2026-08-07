![](https://cdn-images-1.medium.com/max/1000/1*GVoyUG3ky6si_8AeEHeMrw.png)

    

![](https://cdn-images-1.medium.com/max/1000/0*naH2jMiH_EPw-dNl.png)

View the webpage source, we can see the version

    

![](https://cdn-images-1.medium.com/max/1000/0*O-sZ0fpV6U0DQ4t9.png)

Searching and we will find its log4j CVE-2021–44228

    

![](https://cdn-images-1.medium.com/max/1000/0*BIqoC7DlmWdnuSXZ.png)

    

![](https://cdn-images-1.medium.com/max/1000/0*OLi3xBNbNTlHJzDS.png)

tcpdump is used on local machine to listen for incoming connection from target.

- Standard LDAP: Port 389 (unencrypted)
- Secure LDAP (LDAPS): Port 636 (encrypted)

Generate a base64 payload reverse shell:

    

![](https://cdn-images-1.medium.com/max/1000/0*31n_Gpz6FadyxfVj.png)

Start LDAP Server:

    

![](https://cdn-images-1.medium.com/max/1000/0*OEbvIFOXLbIM4lT-.png)

Trigger the payload:

    

![](https://cdn-images-1.medium.com/max/1000/0*Qun2U0VPYDyh3F5Y.png)

We got a shell:

    

![](https://cdn-images-1.medium.com/max/1000/0*oFCiyrx7BjvxGtcB.png)

MongoDB run on port 27117:

    

![](https://cdn-images-1.medium.com/max/1000/0*XhDRg9JFVpjBoT2l.png)

    

![](https://cdn-images-1.medium.com/max/1000/0*SOgJJVFVaRzcyQU_.png)

    

![](https://cdn-images-1.medium.com/max/1000/0*7G9hCFHtTdUEVqj2.png)

First generate a SHA512 hash for admin

    

![](https://cdn-images-1.medium.com/max/1000/0*_vq5Md8lH9ljIzHS.png)

Update admin hash password:

    

![](https://cdn-images-1.medium.com/max/1000/0*hO0vlnS17FeBZDMP.png)

Now login to admin with the password we updated:

    

![](https://cdn-images-1.medium.com/max/1000/0*dcP_UqKPS743Zp72.png)

We can see the root password here:

    

![](https://cdn-images-1.medium.com/max/1000/0*nXvjewkkghwjgS9j.png)

SSH to root and we got root and user flag:

![](https://cdn-images-1.medium.com/max/1000/0*mQikH8sArIFrSczU.png)

![](https://cdn-images-1.medium.com/max/1000/0*MSY4pqIdEV6Tpc7c.png)

![](https://cdn-images-1.medium.com/max/1000/0*iy4X0frXzJS3eEm-.png)