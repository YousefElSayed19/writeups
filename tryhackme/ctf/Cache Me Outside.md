![](https://cdn-images-1.medium.com/max/1000/0*F5Pel3xlKAIS3QBL.png)

> **_The investigation began with a single artifact: the conversation screenshot shown below. No additional information was provided, so every finding in this write-up was obtained by pivoting from the data contained within this image using open-source intelligence (OSINT) techniques._**

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*GbedeUwRyjOPSI6T.png)

One seemingly insignificant detail in the conversation was the victim’s Komoot profile: `[https://www.komoot.com/user/5667624959835](https://www.komoot.com/user/5667624959835)`. Visiting the profile revealed the victim's full name, as well as a linked GitHub account. These findings became the starting point for the next phase of the OSINT investigation.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*d-wrNye1s5FOxvAo.png)

> _What is the retired hacker’s full name?_

Answer: Jim Lee

### Finding the Email Address

The next objective was to identify the victim’s email address.

My first attempt was to inspect the GitHub profile directly, including the repositories and publicly available information. However, no email address was exposed on the profile itself.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*MbWm6mViOwytktgd.png)

No email addresses here, but if we were to add .patch to the link we would get a meta data for it.

Since GitHub stores a complete history of repository changes, I decided to inspect the repository’s commit history. One of the commits contained a link to its corresponding **patch** file.

> **_Note:_** _A_ **_patch_** _is a plain-text representation of the changes introduced by a specific commit. Besides showing the added and removed lines, it often includes metadata such as the author’s name and email address, making it a valuable source during OSINT investigations._

After opening the patch file, I found the author’s email address embedded within the commit metadata, which satisfied the challenge requirement.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*y-tT9zEApVdDpbbW.png)

> [_https://github.com/jiml33t/jiml33t/commit/7b2c8e0a540c36f2e09da5945066020621d6a059.patch_](https://github.com/jiml33t/jiml33t/commit/7b2c8e0a540c36f2e09da5945066020621d6a059.patch)


Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*ZbqYWsUMW46Lbjjg.png)

> _What email address did he accidentally expose?_


Answer: [jimleepro1@gmail.com](mailto:jimleepro1@gmail.com)

### About her phone number

Spent a few minutes trying to find where could his number be but I wasn’t able to do so. Checking some blogs and videos regarding this room, looks like you can just send an email to him and ask for his phone number.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*QBhCHyY4j1n7Rnnb.png)

> _What is his phone number?_


Answer : +40 743 321 239

### Determining the Country

The next step was to determine the victim’s country of residence. Fortunately, the recovered phone number provided an excellent starting point:

+40 743 321 239

The phone number begins with the international dialing code **+40**, which is officially assigned to **Romania**. By identifying the country code, I was able to conclude that the phone number is registered in Romania, giving us another valuable piece of information for the investigation.

However, I couldn’t identify the city using the phone number, so I tried to find any social media account with the same username I obtained from his GitHub account, jiml33t. I actually found a post of his on X with the address of a shop or place he had recently visited.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*Tf-c_dH0WgB__EfB.png)

![](https://cdn-images-1.medium.com/max/1000/0*fjqDtPNcXX_Yy1HJ.png)

The place was called **irigatii.ro**, and after some searching on my Google Maps,

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*FtRncnwvLN0zpbZJ.png)

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*LMHHEaCn8CiMq-ks.png)

I found the location and learned that it was in the city of **Timișoara.**

> _In which city is he located?_


Answer : Timișoara

in the end, all that remains is to reach the nearest tram station, which will likely be Piața Gheorghe Domășneanu, and from there take the tram home on May 7, 2026.

After numerous tests, it was determined to be Piața Gheorghe Domășneanu.

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*K4VATec71NgID3Qb.png)

> _Submit the name of the tram station where he got off on the 7th of May, 202 6._


Answer : Piața Gheorghe Domășneanu

**And now the osint process, which began with a Discord chat, has ended, reaching the tram stop he uses to get home :DD**

Press enter or click to view image in full size

![](https://cdn-images-1.medium.com/max/1000/0*uhgeatWUBiifTQdv.png)