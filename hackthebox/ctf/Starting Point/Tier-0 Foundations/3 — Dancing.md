
![](https://cdn-images-1.medium.com/max/1000/0*3Q_IAKQ5CgPZqcFm.png)

I started by running an Nmap scan to identify the exposed services on the target. The scan revealed that both **NetBIOS Session Service (139)** and **SMB (445)** were open. These ports commonly work together to provide Windows file sharing services, making SMB enumeration the logical next step. Since accessible network shares can sometimes allow anonymous or misconfigured access, I decided to investigate the SMB service further.


![](https://cdn-images-1.medium.com/max/1000/0*ypbz0uVc8L6dAudP.png)

After identifying the available SMB shares, I connected to the **WorkShares** share without providing credentials. The connection was successful, allowing me to enumerate the shared directories and search for interesting files that could lead to the flag.


![](https://cdn-images-1.medium.com/max/1000/0*6vXyZ3nA5H2mSoTL.png)


![](https://cdn-images-1.medium.com/max/1000/0*TajOb55nOQ4bJsYP.png)

![](https://cdn-images-1.medium.com/max/1000/0*cqI5SFhsd7JW4UTd.png)

![](https://cdn-images-1.medium.com/max/1000/0*kaJnM3q6n4TSAFoj.png)

flag : 5f61c10dffbc77a704d76016a22f1664