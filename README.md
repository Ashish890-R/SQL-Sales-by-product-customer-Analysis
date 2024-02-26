# SQL-Sales-by-product-customer-Analysis-----

        For many of us in the tech world, SQL (Structured Query Language) is the backbone of data management and analysis. It's a critical skill, whether you're a data analyst, software developer, business analyst, or a professional in virtually any field that relies on data.

Now, let's understand AtliQ's business model. In the industry, domain understanding is actually more important than technical skills.
 1. Gross monthly total sales report for Croma

Discription:-

As a product owner, I need an aggregate monthly gross sales report for Croma India customers so that I can track how much sales this particular customer is generating for AtliQ and manage our relationships accordingly.

The report should have the following fields,

Month
Total gross sales amount to Croma India in this month
SQL Query :-

SELECT
    S.date,
    ROUND (SUM(g.gross_price * s.sold_quantity),2) AS gross_total_price
FROM fact_sales_monthly s
JOIN fact_gross_price g
    ON g.product_code = s.product_code
    AND g.fiscal_year = get_fiscal_year(s.date)
WHERE customer_code = 90002002
GROUP BY s.date
ORDER BY s.date DESC

Result :-

date	gross_total_price
2021-12-01	$ 19537146.56
2021-10-01	$ 13908229.29
2021-09-01	$ 11192823.08
2021-08-01	$ 2349478.82
2021-06-01	$ 2288587.45
2021-05-01	$ 2181587.78
2021-04-01	$ 2253574.91
2021-02-01	$ 2355170.45
2021-01-01	$ 2303086.37
2020-12-01	$ 4078789.92

**2. Generate a yearly report for Croma India where there are two columns.**

Discription:-

Fiscal Year

Total Gross Sales amount in that Year from Croma

SQL Query :-

SELECT
    get_fiscal_year(s.date) AS Fiscal_Year,
    ROUND (SUM(g.gross_price * s.sold_quantity),2) AS gross_total_price
FROM
	fact_sales_monthly s
JOIN
	fact_gross_price g
ON
	g.product_code = s.product_code
AND
	g.fiscal_year = get_fiscal_year(s.date)
WHERE customer_code = 90002002
GROUP BY get_fiscal_year(date)
ORDER BY fiscal_year DESC

Result :-

Fiscal Year	gross_total_price
2022	        $ 44638198.92
2021	        $ 23216512.22
2020	        $ 6502181.91
2019	        $ 3555079.02
2018	        $ 1324097.44

**3. Created stored proc for monthly gross sales report.**

Discription:-

As a Business intelligence developer, I want to create a stored proc for monthly gross sales reports so that I don't have to modify the query every time manually. Stored proc can be run by other users (who have limited access to the database) and they can generate this report without having to involve the data analytics team.

The report should have the following columns,

Month

Total gross sales in that month from a given customer

SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_monthly_gross_sales_for_customer`(
	in_customer_code TEXT
)
BEGIN
	SELECT
		s.date,
		ROUND(SUM(g.gross_price * s.sold_quantity),2) AS gross_monthly_sales
	FROM
		fact_sales_monthly s
	JOIN
		fact_gross_price g
	ON
		g.product_code = s.product_code
	AND
		g.fiscal_year = get_fiscal_year(s.date)
	WHERE
		FIND_IN_SET(s.customer_code, in_costomer_code)>0
	GROUP BY s.date
	ORDER BY s.date DESC;
END


Result :-

date	gross_monthly_sales
2021-12-01	$ 19537146.56
2021-10-01	$ 13908229.29
2021-09-01	$ 11192823.08
2021-08-01	$ 2349478.82
2021-06-01	$ 2288587.45
2021-05-01	$ 2181587.78
2021-04-01	$ 2253574.91
2021-02-01	$ 2355170.45
2021-01-01	$ 2303086.37
2020-12-01	$ 4078789.92
2020-10-01	$ 3109316.88
2020-09-01	$ 2296919.63
2020-08-01	$ 799327.63
2020-06-01	$ 362545.14
2020-05-01	$ 145049.05
2020-04-01	$ 130520.92


**4. Stored proc for the market badge.**

Discription:-

Create a stored proc that can determine the market badge based on the following logic, If the total sold quantity > 5 million that market is considered Gold else it is Silver.

My input will be,

Market
Fiscal Year
Output

Market badge
SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_market_badge`(
	IN in_market VARCHAR(45),
    IN in_fiscal_year YEAR,
    OUT out_badge VARCHAR(45)
)
BEGIN
	DECLARE qty INT DEFAULT 0;
    #If value not given for market assume like default market is India
    IF in_market = "" THEN
		SET in_market = "India";
	END IF;
    #Retrive total qty for given market and fiscal year
	SELECT
		SUM(s.sold_quantity) AS total_qty
	FROM fact_sales_monthly s
	JOIN dim_customer c
	ON s.customer_code = c.customer_code
	WHERE
		get_fiscal_year(s.date) = in_fiscal_year AND
        c.market = in_market
	GROUP BY c.market;

    #Determine market badge
    IF qty > 5000000 THEN
		SET out_badge = "Gold";
	ELSE
		SET out_badge = "Silver";
	END IF;
END

set @out_badge = '0';
call gdb0041.get_market_badge('india', 2021, @out_badge);
select @out_badge;

Result :-

@out_badge
Silver

**5. Create a view for gross sales. It should have the following columns.**

Discription:-

date, fiscal_year, customer_code, customer, market, product_code, product, variant, sold_quanity, gross_price_per_item, gross_price_total

SQL Query :-

CREATE
    ALGORITHM = UNDEFINED
    DEFINER = `root`@`localhost`
    SQL SECURITY DEFINER
VIEW `gross_sales` AS
    SELECT
		S.date,
		S.fiscal_year,
		S.customer_code,
		C.customer,
		C.market,
		S.product_code,
		P.product,
		P.variant,
		S.sold_quantity,
		g.gross_price AS gross_price_per_item,
		ROUND((s.sold_quantity * g.gross_price),2) AS gross_price_total
	FROM fact_sales_monthly s
	JOIN dim_customer c
		ON s.customer_code = c.customer_code
	JOIN dim_product p
		ON s.product_code = p.product_code
	JOIN fact_gross_price g
		ON s.fiscal_year = g.fiscal_year
		AND s.product_code = g.product_code

SELECT * FROM gdb0041.gross_sales;

Result :-

![gross-sales](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/8a571577-c8b3-45db-8c5f-b7897839f6a9)

**6. Top markets, products, and customers for a given financial year.**

Discription:-

As a product owner, I want a report for top markets, products, and customers by net sales for a given financial year so that I can have a holistic view of our financial performance and can take appropriate actions to address any potential issues.

We will probably write stored proc for this as we will also need this report going forward.

Report for top markets
SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_top_n_market_by_net_sales`(
	in_fiscal_year INT,
    in_top_n INT
)
BEGIN
	SELECT
		market,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales
	WHERE fiscal_year = in_fiscal_year
	GROUP BY market
	ORDER BY net_sales_mln DESC
	LIMIT in_top_n;
END

call gdb0041.get_top_n_market_by_net_sales(2021, 5);

Result :-

market	net sales mln
India	  $ 210.67
USA	  $ 132.05
South Korea	$ 64.01
Canada	  $ 45.89
United Kingdom	$ 44.73

**Report for top products**-
   
SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_top_n_product_by_net_sales`(
	in_market VARCHAR(45),
    in_fiscal_year INT,
    in_top_n INT
)
BEGIN
	SELECT
		product,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales
	WHERE fiscal_year = in_fiscal_year
		AND market = in_market
	GROUP BY product
	ORDER BY net_sales_mln DESC
	LIMIT in_top_n;
END

call gdb0041.get_top_n_product_by_net_sales('India', 2021, 5);

Result :-

product	net sales mln
AQ BZ Allinl	$ 8.54
AQ Qwerty	$ 7.22
AQ Trigger	$ 6.78
AQ Gen Y	$ 6.02
AQ Trigger Ms	$ 5.74

 **Report for top customers-**

SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_top_n_customer_by_net_sales`(
	in_market VARCHAR(45),
    in_fiscal_year INT,
    in_top_n INT
)
BEGIN
	SELECT
		c.customer,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales n
	JOIN dim_customer c
		ON n.customer_code = c.customer_code
	WHERE fiscal_year = in_fiscal_year
		AND n.market = in_market
	GROUP BY c.customer
	ORDER BY net_sales_mln DESC
	LIMIT in_top_n;
END

call gdb0041.get_top_n_customer_by_net_sales('India', 2021, 5);

Result :-

customer	net sales mln
Amazon	        $30.00
Atliq Exclusive	$23.98
Flipkart	$12.96
Electricalsocity $12.31
Propel	       $11.86


**7. Net sales % share Global.**--

Discription:-

AS product owner, I want to see a bar Chart report for FY—2021 for top IO markets by % net sales.

SQL Query :-

WITH cte AS (
	SELECT
		C.customer,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales n
	JOIN dim_customer c
		ON n.customer_code = c.customer_code
	WHERE fiscal_year = 2021
	GROUP BY c.customer
)
SELECT
	*,
	net_sales_mln*100/SUM(net_sales_mln) over() AS pct
FROM cte
ORDER BY net_sales_mln DESC

Result :-

customer	net sales mln	pct
Amazon	$109.03	13.23%
Atliq Exclusive	$79.92	9.7%
Atliq e Store	$70.31	8.53%
Sage	$27.07	3.29%
Flipkart	$25.25	3.06%
Leader	$24.52	2.98%
Neptune	$21.01	2.55%
Ebay	$19.88	2.41%
Electricalsocity	$16.25	1.97%
Synthetic	$16.10	1.95%

Bar Chart :-

![net-sales-bar-chart](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/479163bf-5355-44ef-b2be-4b47e39a4ca5)

8. **Net sales % share by region**--

Discription:-

As a product owner, I want to see region-wise (APAC, EU, L TAM, etc) % net sales breakdown by customers in a respective region so that I can perform my regional analysis on the financial performance of the company.

The end result should be bar charts in the following format for FY = 2021. Build a reusable asset that we can use to conduct this analysis for any financial year.

SQL Query :-

WITH cte AS (
	SELECT
		c.customer,
		c.region,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales n
	JOIN dim_customer c
		ON n.customer_code = c.customer_code
	WHERE fiscal_year = 2021
	GROUP BY c.customer, c.region
)
SELECT
	*,
	net_sales_mln*100/SUM(net_sales_mln) over(partition by region) AS pct
FROM cte
ORDER BY region, net_sales_mln DESC

Result :-

APAC Region-

customer	region	net_sales_mln	pct
Amazon	        APAC	$57.41	12.99%
Atliq Exclusive	APAC	$51.58	11.67%
Atliq e Store	APAC	$36.97	8.36%
Leader	        APAC	$24.52	5.55%
Sage	        APAC	$22.85	5.17%
Neptune	        APAC	$21.01	4.75%
Electricalsocity APAC	$16.25	3.68%
Propel	        APAC	$14.14	3.2%
Synthetic	APAC	$14.14	3.2%
Flipkart	APAC	$12.96	2.93%


EU Region-

customer	region	net_sales_mln	pct
Atliq e Store	EU	$19.83	       9.87%
Amazon	        EU	$19.77	       9.84%
Atliq Exclusive	EU	$13.39	       6.67%
UniEuro	        EU	$9.63	       4.8%
Expert	        EU	$8.38	       4.17%
Chip 7	        EU	$7.23	       3.6%
Radio Popular	EU	$6.95	       3.46%
Media Markt	EU	$6.88	       3.43%
ElkjÃ¸p	        EU	$6.76	       3.37%
Sorefoz	        EU	$6.13	       3.05%

NA Region-

customer	region	net_sales_mln	pct
Amazon	        NA	$30.31	17.03%
Atliq Exclusive	NA	$14.95	8.4%
walmart	        NA	$12.63	7.1%
Atliq e Store	NA	$12.42	6.98%
Costco	        NA	$12.19	6.85%
Staples	        NA	$11.49	6.46%
Flipkart	NA	$10.35	5.82%
Path	        NA	$9.10	5.11%
Ebay	        NA	$8.74	4.91%
Acclaimed StoresNA	$8.53	4.79%

9. **Get top n products in each division by their quantity sold.**--

Discription:-

Write a stored proc for getting TOP n products in each division by their quantity sold in a given financial year. For example below would be the result for FY=2021.

SQL Query :-

CREATE DEFINER=`root`@`localhost` PROCEDURE `get_top_n_product_by_net_sales`(
	in_market VARCHAR(45),
	in_fiscal_year INT,
	in_top_n INT
)
BEGIN
	SELECT
		product,
		ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
	FROM net_sales
	WHERE fiscal_year = in_fiscal_year
		AND market = in_market
	GROUP BY product
	ORDER BY net_sales_mln DESC
	LIMIT in_top_n;
END

call gdb0041.get_top_n_product_by_net_sales('india', 2021, 5);

Result :-

product	net_sales_mln
AQ BZ Allinl	$8.54
AQ Qwerty	$7.22
AQ Trigger	$6.78
AQ Gen Y	$6.02
AQ Trigger Ms	$5.74


**10. Creating a fact_act_est.**--

Discription:-

using the fact_sales_mothly and fact_forecast_monthly create a fact_act_est.

SQL Query :-

create table fact_act_est(
	select
		s.date as date,
		s.fiscal_year as fiscal_year,
		s.product_code as product_code,
		s.customer_code as customer_code,
		s.sold_quantity as sold_quantity,
		f.forecast_quantity as forecast_quantity
	from fact_sales_monthly s
	left join fact_forecast_monthly f
	using (date, customer_code, product_code)
)
union
(
	select
		f.date as date,
		f.fiscal_year as fiscal_year,
		f.product_code as product_code,
		f.customer_code as customer_code,
		s.sold_quantity as sold_quantity,
		f.forecast_quantity as forecast_quantity
	from fact_forecast_monthly  f
	left join fact_sales_monthly s
	using (date, customer_code, product_code)
);

update fact_act_est
set sold_quantity = 0
where sold_quantity is null;

update fact_act_est
set forecast_quantity = 0
where forecast_quantity is null;

SELECT * FROM gdb0041.fact_act_est;

Result :-

date	fiscal_year	product_code	customer_code	sold_quantity	forecast_quantity
9/1/2017	2018	A0118150101	70002017	   51	           18
9/1/2017	2018	A0118150101	70002018	   77	           11
9/1/2017	2018	A0118150101	70003181	   17	            9
9/1/2017	2018	A0118150101	70003182	    6	            6
9/1/2017	2018	A0118150101	70006157	    5	            5
9/1/2017	2018	A0118150101	70006158	    7	            6  
9/1/2017	2018	A0118150101	70007198	   29	            4
9/1/2017	2018	A0118150101	70007199	   34	            7 
9/1/2017	2018	A0118150101	70008169	   22	            7
9/1/2017	2018	A0118150101	70008170	    5	            8


11. **Write a query for the below scenario**--.

Discription:-

The supply chain business manager wants to see which customers’ forecast accuracy has dropped from 2020 to 2021. Provide a complete report with these columns: customer_code, customer_name, market, forecast_accuracy_2020, forecast_accuracy_2021

SQL Query :-
**
**forecast_accuracy_2021****

create temporary table forecast_accuracy_2021
with forecast_err_table as (
	select
		s.customer_code as customer_code,
		c.customer as customer_name,
		c.market as market,
		sum(s.sold_quantity) as total_sold_qty,
		sum(s.forecast_quantity) as total_forecast_qty,
		sum(s.forecast_quantity-s.sold_quantity) as net_error,
		round( sum( s.forecast_quantity - s.sold_quantity ) * 100 / sum( s.forecast_quantity ) , 1 ) as net_error_pct,
		sum(abs(s.forecast_quantity-s.sold_quantity)) as abs_error,
		round ( sum( abs( s.forecast_quantity - sold_quantity ) ) * 100 / sum( s.forecast_quantity ) , 2 ) as abs_error_pct
	from fact_act_est s
	join dim_customer c
	on s.customer_code = c.customer_code
	where s.fiscal_year=2021
	group by customer_code
)
Select
	*,
	if (abs_error_pct > 100, 0, 100.0 - abs_error_pct) as forecast_accuracy
from forecast_err_table
order by forecast_accuracy desc;


**forecast_accuracy_2020--**

create temporary table forecast_accuracy_2020
with forecast_err_table as (
	select
		s.customer_code as customer_code,
		c.customer as customer_name,
		c.market as market,
		sum(s.sold_quantity) as total_sold_qty,
		sum(s.forecast_quantity) as total_forecast_qty,
		sum(s.forecast_quantity-s.sold_quantity) as net_error,
		round( sum( s.forecast_quantity - s.sold_quantity ) * 100 / sum( s.forecast_quantity ) , 1 ) as net_error_pct,
		sum(abs(s.forecast_quantity-s.sold_quantity)) as abs_error,
		round ( sum( abs( s.forecast_quantity - sold_quantity ) ) * 100 / sum( s.forecast_quantity ) , 2 ) as abs_error_pct
	from fact_act_est s
	join dim_customer c
	on s.customer_code = c.customer_code
	where s.fiscal_year=2020
	group by customer_code
)
Select
	*,
	if (abs_error_pct > 100, 0, 100.0 - abs_error_pct) as forecast_accuracy
from forecast_err_table
order by forecast_accuracy desc;

select
	f_2020.customer_code,
	f_2020.customer_name,
	f_2020.market,
	f_2020.forecast_accuracy as forecast_acc_2020,
	f_2021.forecast_accuracy as forecast_acc_2021
from forecast_accuracy_2020 f_2020
join forecast_accuracy_2021 f_2021
on f_2020.customer_code = f_2021.customer_code
where f_2021.forecast_accuracy < f_2020.forecast_accuracy
order by forecast_acc_2020 desc;

Result :-

customer_code	customer_name	market	forecast_acc_2020	forecast_acc_2021
70006158	Atliq e Store	Philiphines	42.65	            24.49
70008170	Atliq e Store	Australia	40.96	            38.74
90005161	Zone	        Pakistan	40.08	            37.1
90014140	Radio Popular	Netherlands	38.53	              0
90008166	Sound	        Australia	38.51	            36.79
70014143	Atliq e Store	Netherlands	38.32	              0
90004062	Flawless Stores	   Japan	38.22	            32.56
90014137	Media Markt	Netherlands	37.85	              0
90014138	Mbit	        Netherlands	37.83	              0
70004069	Atliq Exclusive	   Japan	37.62	            32.09























