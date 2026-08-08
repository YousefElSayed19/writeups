

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

#### Hint

You can assume that the password only contains lowercase, alphanumeric characters.

I first tested a simple boolean condition:

TrackingId=xyz' AND '1'='1

The response contained the `**Welcome back**` message, confirming that the condition was true.

I then changed the condition to:

TrackingId=xyz' AND '1'='2

This time, the `**Welcome back**` message disappeared. This confirmed that I could use the application's response to determine whether a SQL condition was true or false.

Next, I tested whether a `users` table existed:

TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a

The condition returned true, confirming the existence of the `users` table.

I then checked whether the `administrator` user existed:

TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a

The response confirmed that the `administrator` account existed.

I used the `LENGTH()` function to determine the administrator's password length:

TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a

I repeated the test with increasing values until the condition became false. This allowed me to determine that the password was **20 characters long**.

Since testing every possible character manually would require many requests, I moved the request to **Burp Intruder**.

I used the following payload:

TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a

I placed the Intruder payload markers around the final character:

TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§

I configured a **Simple list** containing:

a-z  
0-9

I also configured **Grep — Match** with:

Welcome back

The character that produced the `Welcome back` response was the correct character for that position.

I repeated the same attack while changing the `SUBSTRING()` offset from `1` to `2`, `3`, `4`, and so on until all **20 characters** of the password were recovered.

