This lab contains a SQL injection vulnerability in its stock check feature. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables.

The database contains a `users` table, which contains the usernames and passwords of registered users. To solve the lab, perform a SQL injection attack to retrieve the admin user's credentials, then log in to their account.

I started by inspecting the stock check functionality and noticed that the application sends the `productId` and `storeId` parameters in **XML** format. I sent the `POST /product/stock` request to **Burp Repeater** to investigate the `storeId` parameter.

I first tested whether the `storeId` input was being evaluated by replacing it with a mathematical expression:

```
<storeId>1+1</storeId>
```

The application returned stock information for a different store, confirming that my input was being evaluated by the backend.

I then tested for **SQL injection** by appending a `UNION SELECT` statement:

```
<storeId>1 UNION SELECT NULL</storeId>
```

The request was blocked, indicating that a **WAF** was detecting the payload.

Since the injection was being performed inside XML, I attempted to bypass the WAF by encoding the SQL payload using **XML entities**. I used the **Hackvertor** extension in Burp Suite to encode the payload with `hex_entities`.

After resending the encoded request, the application returned a normal response, confirming that the WAF had been successfully bypassed.

I then tested the number of columns returned by the original query and determined that it returned **a single column**, as attempts to return multiple columns resulted in `0 units`.

Because only one column could be returned, I concatenated the usernames and passwords into a single value using the `~` character as a separator:

```
<storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>
```

The query successfully returned the usernames and passwords from the database,