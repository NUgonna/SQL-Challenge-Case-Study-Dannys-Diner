## Case Study Questions and Answers - Danny's Dinner

### Q1. What is the total amount each customer spent at the restaurant? 

 **Solution steps**:
 * The ``` price ``` field from the ``` menu ``` table and the ``` customer_id ``` field from the ``` sales ``` table are the important fields for this.
 * Retrieve the ``` customer_id ``` and use an aggregate function ``` SUM() ``` on the ``` price ``` field, and then join the ``` sales ``` table and the ``` menu ``` table. If you notice, the sales table has a foreign key called ``` product_id ```, which is a primary key in the ``` menu ``` table. The relationship between the two tables lies with this unique identifier, so the join is done on ``` product_id ```.
 * To get the total spend for each customer, use the GROUP BY clause to group the result set from step 1 and 2 above based on the names of the customers, which is ``` customer_id ``` and order the total amount in descending order.
 
  ``` sql
   SELECT sales.customer_id, SUM(menu.price) AS total_spend  
   FROM dannys_diner. sales 
   JOIN dannys_diner.menu ON sales.product_id = menu.product_id
   GROUP BY sales.customer_id
   ORDER BY total_spend DESC;
   ```
**Final Result**
| customer_id | total_spend |
|--------------|-------------|
| A | 110|
| B | 74|
| C | 46 |

Customer A has spent more money in the restaurant more than any other customer with a total of $110, followed by customer B with a total of $74 and C with the least total spend of $46.

### Q2. How many days has each customer visited the restaurant? 

 **Solution steps**:
 * Interpretation of the question: Danny wants to know the number of days each customer popped in, and not how many times they bought an item. For example, if a customer buys a curry dish 5 times a day, it means that the customer had only come in a day and not 5 days.
 * To get the unique number of days a customer visited the restaurant,  use the ``` COUNT (DISTINCT sales.order_date)```. The DISTINCT keyword removes the duplicates on order_date field, I used that because I need all orders made same day by each customer to be counted as 1. 
 * Finally, aggregate the output of step 2 above by utilising ``` GROUP BY sales. customer_id ```
 
 ``` sql
SELECT sales.customer_id, COUNT (DISTINCT sales.order_date) AS Number_of_days FROM dannys_diner.sales
GROUP BY sales. customer_id;
   ```

**Final Result**
| customer_id | number_of_days |
|--------------|-------------|
| A | 6 |
| B | 6 |
| C | 3 |

A and B have visited the store 6 days, and C has visited 3 days. 

### Q3. What was the first item from the menu purchased by each customer? 

``` sql
WITH first_order AS
(
SELECT RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date) AS order_rank, sales.customer_id, menu.product_name, sales.order_date
 FROM dannys_diner.sales
 JOIN dannys_diner.menu ON sales.product_id = menu.product_id
 )
 SELECT customer_id, product_name, order_date from first_order
 WHERE order_rank = 1;
```

**Final Result**

| customer_id | product_name | order_date |
|--------------|-------------|----------|
|	A | curry	| 2021-01-01T00:00:00.000Z |
| A |	sushi	| 2021-01-01T00:00:00.000Z |
|	B	| curry	| 2021-01-01T00:00:00.000Z |
| C	| ramen |	2021-01-01T00:00:00.000Z |
|	C	| ramen |	2021-01-01T00:00:00.000Z |


