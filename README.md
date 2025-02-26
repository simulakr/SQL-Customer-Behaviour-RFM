# SQL Customer Behavior Analysis - RFM 


**--1. How many times each customers made a purchase? + Top customers with highest purchases.**

SELECT customer_id, COUNT(*) as total_purchase 
from customer_transactions 
GROUP by customer_id order by 2 desc

**--2. Calculate the total amount each customers spent.**

SELECT customer_id, sum( quantity * unitprice) as total_spent 
from customer_transactions
GROUP by customer_id ORDER by 2 desc

**--3. Calculate the total sales per month to understand purchasing trends over time.**

SELECT DISTINCT MONTH(invoice_date) as month_, sum( quantity * unitprice) as totalsale 
from customer_transactions GROUP by MONTH(invoice_date) 
order by month_ asc

**--Customer Segmentation: RFM Analysis** 

**--4. Calculate recency(R).** 

WITH Recency as(
SELECT customer_id, datediff(day, max(invoice_date), getdate()) as recency
from customer_transactions 
GROUP by customer_id
),

**--5. Calculate frequency(F).**

Frequency as( 
  SELECT customer_id, COUNT(*) as frequency 
  FROM customer_transactions 
  GROUP by customer_id
  ),

**--6. Calculate total sale(monetary)(M).**

Monetary AS(
  SELECT customer_id, sum( quantity*unitprice) as monetary
  FROM customer_transactions 
  GROUP by customer_id)

**--7. Combine the steps that R, F, M.**

SELECT r.customer_id, r.recency, f.frequency, m.monetary
FROM Recency as r 
JOIN Frequency as f on r.customer_id = f.customer_id 
JOIN Monetary as m on f.customer_id = m.customer_id
