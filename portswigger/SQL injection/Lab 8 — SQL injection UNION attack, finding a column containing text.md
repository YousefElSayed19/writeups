This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application’s response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a [previous lab](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns). The next step is to identify a column that is compatible with string data.

The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data.

in this lab we just want to Make the database retrieve the string: ‘8M1KWm’

`'+UNION+SELECT+'`8M1KWm`',NULL,NULL--`

If an error occurs, move on to the next null and try that instead.

and here we go this work :

%27+UNION+SELECT+null,%278M1KWm%27,NULL -

![](https://cdn-images-1.medium.com/max/1000/1*g8nNpPW5B7U2IQirTrPICw.png)