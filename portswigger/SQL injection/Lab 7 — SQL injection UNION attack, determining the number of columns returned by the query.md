

This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application’s response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.

To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values.

in this lab just need to determine number of columns 

Modify the `category` parameter to add an additional column containing a null value:

`'+UNION+SELECT+NULL,NULL--`

Continue adding null values until the error disappears and the response includes additional content containing the null values.

![](https://cdn-images-1.medium.com/max/1000/1*TbmS5oev1BYu5xqy38lX-Q.png)

and here we go there just 3 column