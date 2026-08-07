![](https://cdn-images-1.medium.com/max/1000/1*0S8HdHj1bd7tRgKFXB-Emw.png)

  

Using ZAP, go to the IP and capture the traffic

![](https://cdn-images-1.medium.com/max/1000/0*ExRoOw5JVW5DePg4.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*LFYnuqWvaiBSm2gL.png)

So we need admin. First login as guest

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*_C4eCxGWqfe8ATPP.png)

Change id in URL from 2 to 1, we get the admin id

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*INqVJ3pckRjb-eij.png)

Change the cookie to the admin id

![](https://cdn-images-1.medium.com/max/1000/0*f8yArf-BdsT7QoG1.png)

We can now access the upload page

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*q9i4n7qHIOCHxA7L.png)

http://10.129.95.191/uploads/shell.php

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*Z8FGCUr1b52ExASN.png)

Navigate to the backend dir and found the password.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*bLDGjsN-jPVjIUWl.png)

ssh to robert and run:

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*gceI9fW1mWM_F9mW.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*gm0lqAae--UZ2svJ.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*084Uny4OrTja4zW5.png)

SUID bit is set, run the program will run with the privileges of the file’s owner, rather than the privileges of the user who is actually typing the command

Using ltrace:

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*arPIQtafrmkccyCn.png)

We can see the binary is calling ‘cat’ without an absolute path, we can create a fake cat script to get shell.

![](https://cdn-images-1.medium.com/max/1000/0*5IJ83A-jKtSl9ehP.png)

![](https://cdn-images-1.medium.com/max/1000/0*QPeizk9LnIddzZHb.png)

![](https://cdn-images-1.medium.com/max/1000/0*PoJxnEDCQx-Txqvg.png)