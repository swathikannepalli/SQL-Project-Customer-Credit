1) Avg  age of the customers using the credit :
select customer_id,avg(age),card_type
from customer 
group by customer_id,card_type;

2) The min,max And avg age groups :
select customer_id,
       card_type,
       Max(age) over (Partition by card_type) max_customer_age,
       MIN(age) over (partition by card_type) as min_customer_age
from customer;

3) maximum which segements of customers are there:
SELECT
    job_segment,
    SUM(CASE WHEN card_type = 'Gold' THEN 1 ELSE 0 END) AS Gold_Count,
    SUM(CASE WHEN card_type = 'Silver' THEN 1 ELSE 0 END) AS Silver_Count,
    SUM(CASE WHEN card_type = 'Platinum' THEN 1 ELSE 0 END) AS Platinum_Count
FROM customer
GROUP BY job_segment;

4) The frequeny of the products are they are using :
select product_type,
       count(product_type) as freq
from spend
group by product_type
order by freq DESC;
-- Most of the customers are using their credit cards  in petrol

5) The total sum their using products :
select customer_id,
       product_type,
     round(sum(amount),2) as total_amount
from spend
group by customer_id,product_type
order by total_amount DESC;

--  The total count card_type and city:
6) The  Total card_types  in different types cities 
select city,
count(card_type) as cnt_card_type,
card_type
from customer 
group by city,card_type
order by cnt_card_type DESC;

7) Avg credit card limit :
select 
     city,
     Round(Avg(credit_limit),2) as avg_credit_limit
from customer
group by city;

8) SELECT
    card_type,
    product_type,
    SUM(amount) AS total_amount
FROM customer c
JOIN spend s
    ON c.customer_id = s.customer_id
GROUP BY
    card_type,
    product_type
order by 
      total_amount Desc;
-- Note : We can try to analysis which credits cards are usings on which products ;
-- The Gold credit card holder are using more in petrol :

9) The total Amount spent by each customer and which products :

select c.customer_id,
	   s.product_type,
       sum(s.amount) as total_amount,
       c.card_type
from spend s
join customer c on s.customer_id = c.customer_id 
group by
        c.customer_id,
        s.product_type,
       c.card_type
order by total_amount desc;
-- so here what we found is the customer_id A45 has used the highest amount by using the gold credit card .

10) Now i want to check with each customer have how many credit cards:
SELECT
    card_type,
    COUNT(*) AS customer_count
FROM customer
GROUP BY card_type;

11) -- Card repayment:
-- Here we are checking with the total_amount for the individual cards Vs total_amount spend for all three cards 
-- for each customer  this analysis with make us understand that the total expe a customer have
-- for each products and so that w
WITH cte AS (
    SELECT
        c.customer_id,
        c.card_type,
        SUM(r.amount) AS total_amount
    FROM repayment r
    JOIN customer c
        ON r.customer_id = c.customer_id
    GROUP BY
        c.customer_id,
        c.card_type
)
SELECT
    customer_id,
    card_type,
    total_amount,
    SUM(total_amount) OVER (
        PARTITION BY card_type
    ) AS total_card_type_amount
FROM cte;

12)-- Month wise spent on the cateogry :
select 
     round (sum(amount),2) as total_spend_month_wise,
      monthname(date) as Month_wise
from spend
group by  monthname(date) 
order by total_spend_month_wise desc;

13)  What is the average no. of days a customer pays off their credit card bill?
SELECT
    spend.customer_id,
    MAX(spend.date) AS spend_date,
    MAX(STR_TO_DATE(repayment.date,'%m/%d/%Y')) AS repayment_date,
    ABS(
        DATEDIFF(
            MAX(STR_TO_DATE(repayment.date,'%m/%d/%Y')),
            MAX(spend.date)
        )
    ) AS days
FROM spend
JOIN customer
    ON customer.customer_id = spend.customer_id
JOIN repayment
    ON repayment.customer_id = customer.customer_id
GROUP BY spend.customer_id;










