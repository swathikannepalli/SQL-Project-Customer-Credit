-- Avg  age of the customers using the credit :
select customer_id,avg(age),card_type
from customer 
group by customer_id,card_type;

-- The min,max And avg age groups :
select customer_id,
       card_type,
       Max(age) over (Partition by card_type) max_customer_age,
       MIN(age) over (partition by card_type) as min_customer_age
from customer;

-- maximum which segements of customers are there:
SELECT
    job_segment,
    SUM(CASE WHEN card_type = 'Gold' THEN 1 ELSE 0 END) AS Gold_Count,
    SUM(CASE WHEN card_type = 'Silver' THEN 1 ELSE 0 END) AS Silver_Count,
    SUM(CASE WHEN card_type = 'Platinum' THEN 1 ELSE 0 END) AS Platinum_Count
FROM customer
GROUP BY job_segment;

-- The frequeny of the products are they are using :
select product_type,
       count(product_type) as freq
from spend
group by product_type
order by freq DESC;
-- Most of the customers are using their credit cards  in petrol
-- the total sum their using products :
select customer_id,
       product_type,
     round(sum(amount),2) as total_amount
from spend
group by customer_id,product_type
order by total_amount DESC;

--  The total count card_type and city:
-- the  Total card_types  in different types cities 
select city,
count(card_type) as cnt_card_type,
card_type
from customer 
group by city,card_type
order by cnt_card_type DESC;

-- Avg credit card limit :
select 
     city,
     Round(Avg(credit_limit),2) as avg_credit_limit
from customer
group by city;
