# SQL-Sales-by-product-customer-Analysis-----

For many of us in the tech world, SQL (Structured Query Language) is the backbone of data management and analysis. It's a critical skill, whether you're a data analyst, software developer, business analyst, or a professional in virtually any field that relies on data.

Now, let's understand AtliQ's business model. In the industry, domain understanding is actually more important than technical skills.

 1. **Gross monthly total sales report for Croma**--

Discription:-
As a product owner, I need an aggregate monthly gross sales report for Croma India customers so that I can track how much sales this particular customer is generating for AtliQ and manage our relationships accordingly.

The report should have the following fields,

Month
Total gross sales amount to Croma India in this month
SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/ac286bc1-bd29-46af-b36e-d75feb975096)


Result :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/15cf4147-c510-4839-98f2-804560a0f2df)


**2. Generate a yearly report for Croma India where there are two columns.**

Discription:-

Fiscal Year

Total Gross Sales amount in that Year from Croma

SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/d3048af6-ce94-4af7-943d-f980e88f1773)


Result :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/8b21621b-ae4c-4188-b4eb-5c8ce944d56d)


**3. Created stored proc for monthly gross sales report.**

Discription:-

As a Business intelligence developer, I want to create a stored proc for monthly gross sales reports so that I don't have to modify the query every time manually. Stored proc can be run by other users (who have limited access to the database) and they can generate this report without having to involve the data analytics team.

The report should have the following columns,

Month

Total gross sales in that month from a given customer

SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/f602334d-df40-491d-aa77-4025b3264131)
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/7d4ac303-77c2-45cc-9299-792fcd702040)


Result :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/e20abae8-0756-4757-b8cb-ea92307e9775)



**4. Stored proc for the market badge.**

Discription:-

Create a stored proc that can determine the market badge based on the following logic, If the total sold quantity > 5 million that market is considered Gold else it is Silver.

My input will be,

Market
Fiscal Year
Output

Market badge
SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/d4acae45-1da7-4e93-a532-270f228f3730)
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/5da14108-6a43-4de8-a167-375da0f4b459)


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

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/f80463c9-74fb-4941-a5c2-93a893a47a57)
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/fce1cee5-7308-4f0e-879b-2d6a03e11432)


SELECT * FROM gdb0041.gross_sales;

Result :-

![gross-sales](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/8a571577-c8b3-45db-8c5f-b7897839f6a9)

**6. Top markets, products, and customers for a given financial year.**

Discription:-

As a product owner, I want a report for top markets, products, and customers by net sales for a given financial year so that I can have a holistic view of our financial performance and can take appropriate actions to address any potential issues.

We will probably write stored proc for this as we will also need this report going forward.

Report for top markets
SQL Query :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/1bf4b048-7870-4269-934f-d7c6fbdbe99b)


call gdb0041.get_top_n_market_by_net_sales(2021, 5);

Result :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/b6c71fad-19f5-4379-b04f-c95492951cc2)


**Report for top products**-
   
SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/5671523d-9dd7-4bb6-b5e4-22f7e02bbb65)


call gdb0041.get_top_n_product_by_net_sales('India', 2021, 5);

Result :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/5835d3c3-08fe-48c8-a9ec-c96cc1179bcd)


 **Report for top customers-**

SQL Query :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/74d2e06b-c52a-415e-97b2-b7c8e468c85e)
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/c370d640-5dc5-4fe3-ab89-660346b613fc)


call gdb0041.get_top_n_customer_by_net_sales('India', 2021, 5);

Result :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/7ac24baf-e4aa-4a99-95fb-f89fa742edff)


**7. Net sales % share Global.**--

Discription:-

AS product owner, I want to see a bar Chart report for FY—2021 for top IO markets by % net sales.

SQL Query :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/ec74625e-9d25-4d68-acbc-548ba6989463)


Result :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/a412a481-4d58-4f19-81b8-7651820d0d96)


Bar Chart :-

![net-sales-bar-chart](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/479163bf-5355-44ef-b2be-4b47e39a4ca5)

8. **Net sales % share by region**--

Discription:-

As a product owner, I want to see region-wise (APAC, EU, L TAM, etc) % net sales breakdown by customers in a respective region so that I can perform my regional analysis on the financial performance of the company.

The end result should be bar charts in the following format for FY = 2021. Build a reusable asset that we can use to conduct this analysis for any financial year.

SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/9be92d75-9542-4aef-b8dd-2cfd17de622b)

Result :-

APAC Region-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/20dc548b-166d-471d-80f2-f3b636237560)


EU Region-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/6e5fa408-e1c9-41e5-81fc-1ece695c8b28)


NA Region-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/3af72b19-2c4e-40be-a442-0e41a097fb75)


9. **Get top n products in each division by their quantity sold.**--

Discription:-

Write a stored proc for getting TOP n products in each division by their quantity sold in a given financial year. For example below would be the result for FY=2021.

SQL Query :-

![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/2a4180b4-b3f4-4e69-9040-b4ebdf425480)


call gdb0041.get_top_n_product_by_net_sales('india', 2021, 5);

Result :-
![image](https://github.com/Ashish890-R/SQL-Sales-by-product-customer-Analysis/assets/154528363/2ec6a1ca-8258-4c2b-be56-2831b1164fb0)


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























