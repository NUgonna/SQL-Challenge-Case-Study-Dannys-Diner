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
 **Solution steps**:
 
 * First thing is to create a Common Table Expression (CTE) called 'first_order', inside the CTE, create a new column by using the **RANK()** function. This function assigns a rank over the partition that has been created on ``` sales.customer_id ```. The idea is to ensure that the ranking starts where the ORDER BY sales.order_date is in ascending order (i.e. minimum date will be ranked as 1), this is why the ```ORDER BY sales.order_date ``` is important.
 * Use the **JOIN** keyword to join tables ```sales``` and ```menu``` **ON**  their common unique identifier, which is the ```product.id``` . The reason for this join is  to get the name of the item bought by each customer. The ```product_name ``` field is found on the ```menu``` table and not on the ```sales``` table.
 * In the outer query, retrieve all orders from the **CTE** where the ranking equals 1. This will give you the first item ordered by the customer from the store.

``` sql
WITH first_order AS
(
SELECT RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date) AS order_rank, sales.customer_id, menu.product_name, sales.order_date
 FROM dannys_diner.sales
 JOIN dannys_diner.menu ON sales.product_id = menu.product_id
 )
 SELECT customer_id, product_name, order_date from first_order
 WHERE order_rank = 1
 GROUP BY customer_id, product_name, order_date;
```

**Final Result**

| customer_id | product_name | order_date |
|--------------|-------------|----------|
|	A | curry	| 2021-01-01T00:00:00.000Z |
| A |	sushi	| 2021-01-01T00:00:00.000Z |
|	B	| curry	| 2021-01-01T00:00:00.000Z |
| C	| ramen |	2021-01-01T00:00:00.000Z |

Customer A ordered curry and sushi as their first item, while B ordered a curry dish as their first item and lastly, C's first item is ramen. Notice that two dishes happen to be customer A's first ordered items, this is because the ```sales.order_date ``` does not have a timestamp, it has a **DATE** datatype, if there was a timestamp for orders made on the same day, the **RANK()** function would have ranked both differently.

### 4.	What is the most purchased item on the menu and how many times was it purchased by all customers?

**Solution steps**:
* Use the ```COUNT () ``` function on the ``` sales.product_id ``` to get the number of times an item has been purchased. Please note that the ``` sales.product_id ``` does not give the name of the item, it provides only the unique number of the item, hence, the ``` menu``` table needs to be combined with the ``` sales``` table.
* Use the ``` JOIN ``` clause to combine the ``` sales ``` and the ``` menu ``` tables.
* **GROUP** the result set by ``` menu.product_name ``` and order the collapsed result using the newly created column ``` number_of_orders ``` in descending order, this ensures that most ordered item is on top of the returned rows.
* Use the ``` LIMIT``` clause to limit the number of rows to be displayed. In this case, only one row is needed, which is the top most row. Hence, ``` LIMIT 1```

``` sql
SELECT menu.product_name, COUNT(sales.product_id) AS number_of_orders 
FROM dannys_diner.sales JOIN dannys_diner.menu ON sales.product_id = menu.product_id
GROUP BY menu.product_name
Order BY number_of_orders DESC
LIMIT 1;
```
**Final Result**

|product_name |	number_of_orders |
|--------------|-------------|
| ramen	| 10 |

The most purchased item is Ramen.

### 5. Which item was the most popular item for each customer ?

**Solution steps**:

 * Create a CTE called '**popular_orders**' and within the CTE, create a new column called **SN** using the **RANK()** function. This function assigns a rank over the partition that has been created on ``` sales.customer_id ```. The idea is to ensure that the ranking starts where the **ORDER BY COUNT(sales.product_id)** is in descending order - ``` ORDER BY COUNT(sales.product_id) DESC ``` (i.e. the highest number of orders for each customer and for each product will be ranked as 1,). The **RANK** function is important in this case because there is a likelihood that a customer may have two or more most popular dishes (same maximum count).
 * Use the **JOIN** keyword to join tables ```sales``` and ```menu``` **ON**  their common unique identifier, which is the ```product.id``` . The reason for this join is to get the name of the item bought by each customer. The ```product_name ``` field is found on the ```menu``` table and not on the ```sales``` table.
 * In the outer query, retrieve the most popular item for each customer from the **CTE** where the ranking equals 1.

``` sql
WITH popular_orders AS
(
SELECT 
RANK() OVER (PARTITION BY sales.customer_id ORDER BY COUNT(sales.product_id) DESC) AS SN, COUNT(sales.product_id) AS number_of_orders, sales.customer_id,menu.product_name
 
FROM dannys_diner.sales
JOIN dannys_diner.menu ON sales.product_id = menu.product_id
GROUP BY sales.customer_id, menu.product_name
)
SELECT customer_id, product_name, number_of_orders FROM popular_orders 
WHERE SN = 1;

```
**Final Result**
|customer_id |	product_name |	number_of_orders |
|--------------|-------------|------------------|
| A	| ramen |	5 |
| B |	ramen |	2 |
| B	| curry |	2 |
| B	| sushi |	2 |
| C	| ramen |	3 |

Customers A and C have **Ramen** has their most popular dish. Customer B doesn't seem to have a most popular dish  because all three dishes have been purchased by the customer the same number of times.

### 6. Which item was purchased first by the customer after they became a member ?

* **Get all orders purchased after becoming a member**: Create a CTE called ```orders_after_membership ```. Select all relevant columns needed for your results,  **JOIN** tables ``` sales ``` and ``` members``` **ON** ``` customer_id``` and **JOIN** tables ``` menu``` and ``` sales```  **ON**  ``` product_id```. Then filter the table using the ``` WHERE``` clause to display results only when order dates are greater than the sign-up dates.
*  **Rank orders after membership starting with the minimum order date**:  Create a CTE called **first_order** from ```orders_after_membership ``` temp table. Within the second CTE, create a new column called **first_orderr** using the ``` RANK()``` window function to rank ``` sales.customer_id ``` partition based on ``` sales.order_date in ascending order ```  . Get all the necessary columns from the ```orders_after_membership ``` table. _ ** Note: The RANK () function because a customer can order two different items on the same date (Note: the ```sales.orders_date ``` does not have a timestamp).**_
  
*  **Filter the output of the second CTE using the ```WHERE ``` clause**: In the outer query, retrieve results from the ``` first_order ``` CTE  where the rank equals 1. first_order

``` sql
WITH orders_after_membership AS (
SELECT s.customer_id, m.product_name, s.order_date FROM dannys_diner.sales s
JOIN dannys_diner.menu m ON s.product_id = m.product_id
JOIN dannys_diner.members e ON s.customer_id = e.customer_id
WHERE s.order_date > DATE(e.join_date)
),

first_order AS (
SELECT RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS first_orderr, customer_id, product_name, order_date FROM orders_after_membership
 )
SELECT customer_id, product_name, order_date FROM first_order
WHERE first_orderr = 1;
``` 
**Final Result**

| customer_id |	product_name	| order_date|
|--------------|-------------|------------------|
| A	| ramen |	2021-01-10T00:00:00.000Z |
| B	| sushi |	2021-01-11T00:00:00.000Z |

Customer A purchased ramen after signing up for the membership, while B went for sushi. In conclusion, customers are likely to order either **ramen** or **sushi** after being members.


