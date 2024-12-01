#         Pizza Sales Analysis with MySQL
![Pizza logo](https://github.com/VDhakad-Datamind/Pizza_Sales_Sql_Project/blob/main/Pizza-Image1.png)

##  Objectives
- Analyze Sales Performance by Order Volume and Revenue
- Identify and Rank Pizza Types by Popularity and Revenue
- Determine the Impact of Pizza Size and Category on Sales
- Track Ordering Patterns by Time and Date
- Calculate Revenue Contribution by Pizza Type and Category

-**Dataset Link**: [Pizza Dataset](https://github.com/VDhakad-Datamind/Pizza_Sales_Sql_Project/tree/main/pizza_sales_datasets)

## Business Problems and solutions


### 1. Retrieve the total number of orders placed.

```SQL
SELECT 
    COUNT(order_id) AS Total_Orders
FROM
    orders;
```

### 2. Calculate the total revenue generated from pizza sales.  

```SQL
SELECT 
    ROUND(SUM(o.quantity * p.price), 0) AS Total_sales
FROM
    order_details o
        JOIN
    pizzas p ON o.pizza_id = p.pizza_id;
  ``` 
    
### 3. Identify the highest-priced pizza.

```SQL
select p.name , o.price
from pizza_types p
join pizzas o
on p.pizza_type_id=o.pizza_type_id
order by price desc																																																																																			limit 1 ;
```   
    
### 4. Identify the most common pizza size ordered.

```SQL
SELECT 
    p.size, COUNT(o.Order_details_id) AS Total_orders
FROM
    order_details o
        JOIN
    pizzas p ON o.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY Total_orders DESC
LIMIT 1;
```

### 5. List the top 5 most ordered pizza types along with their quantities.

```SQL
SELECT 
    p.name, SUM(o.quantity) AS quantity
FROM
    pizza_types p
        JOIN
    pizzas po ON p.pizza_type_id = po.pizza_type_id
        JOIN
    order_details o ON po.pizza_id = o.pizza_id
GROUP BY p.name
ORDER BY quantity DESC
LIMIT 5;
```

### 6. Join the necessary tables to find the total quantity of each pizza category ordered.

```SQL
SELECT 
    p.category, SUM(o.quantity) AS quantity
FROM
    pizza_types p
        JOIN
    pizzas po ON p.pizza_type_id = po.pizza_type_id
        JOIN
    order_details o ON po.pizza_id = o.pizza_id
GROUP BY p.category
ORDER BY quantity DESC;
```

### 7. Determine the distribution of orders by hour of the day.

```SQL
SELECT 
    HOUR(order_time) AS hour, COUNT(order_id)
FROM
    orders
GROUP BY HOUR(order_time);
```

### 8. Join relevant tables to find the category-wise distribution of pizzas.

```SQL
select category , count(name) as Pizza_count from pizza_types
group by category;
```

### 9. Group the orders by date and calculate the average number of pizzas ordered per day.

```SQL
SELECT 
    ROUND(AVG(quantity)) as Average_Pizza_order
FROM
    (SELECT 
        o.order_date, SUM(p.quantity) AS quantity
    FROM
        order_details p
    JOIN orders o ON p.order_id = o.order_id
    GROUP BY order_date) AS order_Quantity;
```
   
   
### 10. Determine the top 3 most ordered pizza types based on revenue.
   
```SQL
SELECT 
    s.name, ROUND(SUM(o.quantity * p.price), 0) AS Revenue
FROM
    order_details o
        JOIN
    pizzas p ON o.pizza_id = p.pizza_id
join pizza_types s 
on p.pizza_type_id=s.pizza_type_id
group by s.name
order by Revenue desc
limit 3;
```

### 11. Calculate the percentage contribution of each pizza type to total revenue.

```SQL
SELECT 
    s.category,
    ROUND((SUM(o.quantity * p.price) / (SELECT 
                    ROUND(SUM(o.quantity * p.price), 2) AS total_sales
                FROM
                    order_details o
                        JOIN
                    pizzas p ON o.pizza_id = p.pizza_id)) * 100,
            0) AS revenue
FROM
    order_details o
        JOIN
    pizzas p ON o.pizza_id = p.pizza_id
        JOIN
    pizza_types s ON p.pizza_type_id = s.pizza_type_id
GROUP BY s.category
ORDER BY revenue DESC;
```


### 12. Analyze the cumulative revenue generated over time.

```SQL 
select 
	order_date, sum(revenue) over(order by order_date) as Cumulative_Sum 
from
	(select o.order_date, round(sum(a.quantity*p.price),2) as Revenue 
from 
	orders o
join order_details a
	on o.order_id= a.order_id
join pizzas p 
	on a.pizza_id=p.pizza_id
group by o.order_date)
as data;
```





### 13. Determine the top 3 most ordered pizza types based on revenue for each pizza category.
```SQL 
select category, name, revenue from 
(select category , name ,revenue, rank() over (partition by category order by revenue desc) as rn from
(select p.category,p.name , round(sum(o.quantity*a.price),2) as Revenue
from order_details o
join pizzas a 
on o.pizza_id = a.pizza_id
join pizza_types p 
on p.pizza_type_id=a.pizza_type_id
group by p.category, p.name) as data) as Temp
where rn<=3;
```

   







   
