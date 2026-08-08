This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.

####  Hint

You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

I started by visiting the shop's front page and intercepting the request containing the `TrackingId` cookie using **Burp Suite**.

I then modified the cookie to inject a PostgreSQL time-delay payload:

```text
TrackingId=x'||pg_sleep(10)--
```

After sending the request, the application took approximately **10 seconds** to respond. This confirmed that the SQL injection was being executed and that a time delay could be used as a side channel to infer information from the database.