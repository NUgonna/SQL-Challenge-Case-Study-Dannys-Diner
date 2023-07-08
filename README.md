# SQL-Challenge-Case-Study-Dannys-Diner
The Danny's Dinner SQL challenge is one of the most interesting challenges  I have participated in; I wonder why it took me this long to jump on it, which is why the solutions of these exercise made it to my repository! I will be translating business queries into insights using SQL. How interesting is that? Let's go! 

## Table of Contents
* Background
* Problem Statement
* Database Schema and Tables
* [Case Study Questions and SQL Solutions](https://github.com/NUgonna/SQL-Challenge-Case-Study-Dannys-Diner/blob/main/Case%20Study%20Questions%20and%20Solutions.md)

## Background 

Danny's Diner is a fictitious little restaurant that sells 3 amazing dishes: Sushi, Curry and Ramen. The restaurant has been receiving customers and have stored their purchase data, but the thing is that the restaurant does not know how to use this data.

## Problem Statement

Delivering excellent and a personalised customer experience for loyal customers has been a top priority for Danny's Diner, and it has been difficult for the owner to create this experience without understanding his customers' visiting patterns, how much money they have spent, and what dish on the restaurant's menu is their favourite. 

The restaurant's owner, Danny, wants to utilize these insights to make decisions around the expansion of the restaurant's loyalty program. He has provided some datasets and needs answers to some questions to enable him to make an effective decision.

## Database Design Schema and Tables
In the quest to help Danny's Diner stay afloat, below is the database schema and the tables for this task. 

_**NOTE: The data in the "Tables" sub-section are sample data provided by Danny.**__

 * **The Schema**
   
The schema has three tables: ``` sales ```, ``` members ```,and ``` menu ```. These tables are found in the ``` dannys_diner ``` database. From the diagram, the following are the relationships:
* The ``` sales ``` table has a many-to-one relationship with the ``` members ``` table . What does this mean? It means that the primary key in the ``` members ``` table will appear numerous times in the ``` sales ``` table. Customers who are members are likely to make one or more purchases.
* The ``` sales ```  table has a many-to-one relationship with the  ``` menu ```. What does this mean ? This simply means that many customers can buy a certain dish. Technically, the primary key (unique identifier) in the ``` menu ``` is likely to appear many times on the ``` sales ``` table

![image](https://github.com/NUgonna/SQL-Challenge-Case-Study-Dannys-Diner/assets/51289316/afa9e36b-3226-40a1-ae87-fcadf2db9bc4)

 * **Tables**
   
   **Sales Table**:
   
   | **customer_id** | **order_date** | **product_id** |
    |---------------|------------------|---------------|
   | A | 2021-01-01 | 1 |
   | A | 2021-01-01 | 2 |
   | A | 2021-01-07 | 2 |
   | A | 2021-01-10 | 3 |
   | A | 2021-01-11 | 3 |
   | A | 2021-01-11 | 3 |
   | A | 2021-01-14 | 3 |
   | A | 2021-01-15 | 3 |
   | B | 2021-01-01 | 2 |
   | B | 2021-01-02 | 2 |
   | B | 2021-01-04 | 1 |
   | B | 2021-01-11 | 1 |
   | B | 2021-01-16 | 3 |
   | B | 2021-02-01 | 3 |
   | C | 2021-01-01 | 3 |
   | C | 2021-01-01 | 3 |
   | C | 2021-01-07 | 3 |
   | C | 2021-01-08 | 1 |
    
     **Menu Table**:
    | **product_id** | **product_name** | **price** |
    |---------------|------------------|---------------|
    | 1 | sushi | 10 |
    | 2 | curry | 15 |
    | 3 | ramen | 12 |
   
    **Members Table**:
 
    | **customer_id** | **join_date** |
   |------------------|---------------|
   | A | 2021-01-07 |
   | B | 2021-01-09 |
    
## Case Study Questions and SQL Solutions
Using the tables above and knowing their relationships, now let's provide some insights to Danny's business questions:
* ### [Case Study Questions and Solutions](https://github.com/NUgonna/SQL-Challenge-Case-Study-Dannys-Diner/blob/main/Case%20Study%20Questions%20and%20Solutions.md)

    



