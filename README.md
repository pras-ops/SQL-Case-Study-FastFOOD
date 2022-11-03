# SQL-Case-Study-FastFOOD

#### The company has started a new business of selling burgers due to the growing popularity of fast food in the current economy, so the company needs to analyze the sales and performance of the company.
#### 1,How many burgers were ordered?
SELECT COUNT(*) as 'no of orders' FROM runner_orders;\

## 2,How many unique customer orders were made?
SELECT COUNT(DISTINCT order_id) AS unique_order_count\
FROM customer_orders;\

## 3,How many successful orders were delivered by each runner?
SELECT\
  runner_id,\
  COUNT(DISTINCT order_id) AS successful_orders\
FROM runner_orders\
WHERE cancellation IS NULL\
GROUP BY runner_id\
ORDER BY successful_orders DESC;\

## 4,How many of each type of burger was delivered?
SELECT p.burger_name, COUNT(c.burger_id) AS delivered_burger_count\
FROM customer_orders AS c\
JOIN runner_orders AS r\
 ON c.order_id = r.order_id\
JOIN burger_names AS p\
 ON c.burger_id = p.burger_id\
WHERE r.distance != 0\
GROUP BY p.burger_name;\

## 5,How many Vegetarian and Meatlovers were ordered by each customer?
SELECT c.customer_id, p.burger_name, COUNT(p.burger_name) AS order_count\
FROM customer_orders AS c\
JOIN burger_names AS p\
 ON c.burger_id= p.burger_id\
GROUP BY c.customer_id, p.burger_name\
ORDER BY c.customer_id;\

## 6,What was the maximum number of burgers delivered in a single order?
WITH burger_count_cte AS\
(\
 SELECT c.order_id, COUNT(c.burger_id) AS burger_per_order\
 FROM customer_orders AS c\
 JOIN runner_orders AS r\
  ON c.order_id = r.order_id\
 WHERE r.distance != 0\
 GROUP BY c.order_id\
)\
SELECT MAX(burger_per_order) AS burger_count\
FROM burger_count_cte;\

## 7,For each customer, how many delivered burgers had at least 1 change and how many had no changes?
SELECT c.customer_id,\
 SUM(CASE \
  WHEN c.exclusions <> ' ' OR c.extras <> ' ' THEN 1\
  ELSE 0\
  END) AS at_least_1_change,\
 SUM(CASE \
  WHEN c.exclusions = ' ' AND c.extras = ' ' THEN 1 \
  ELSE 0\
  END) AS no_change\
FROM customer_orders AS c\
JOIN runner_orders AS r\
 ON c.order_id = r.order_id\
WHERE r.distance != 0\
GROUP BY c.customer_id\
ORDER BY c.customer_id;\

## Q8,What was the total volume of burgers ordered for each hour of the day?
SELECT EXTRACT(HOUR from order_time) AS hour_of_day, \
 COUNT(order_id) AS burger_count\
FROM customer_orders\
GROUP BY EXTRACT(HOUR from order_time);\

## Q9,How many runners signed up for each 1 week period? 
SELECT EXTRACT(WEEK from registration_date) AS registration_week,\
 COUNT(runner_id) AS runner_signup\
FROM burger_runner\
GROUP BY EXTRACT(WEEK from registration_date);\

## Q10,What was the average distance travelled for each customer?
SELECT c.customer_id, AVG(r.distance) AS avg_distance\
FROM customer_orders AS c\
JOIN runner_orders AS r\
 ON c.order_id = r.order_id\
WHERE r.duration != 0\
GROUP BY c.customer_id;\
