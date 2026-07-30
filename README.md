# Sales-Data-ETL-Pipeline-PostgreSQL


## Table of Contents
- [Overview](#overview)
- [Tech stack](#tech-stack)
- [Key techniques](#key-techniques) 
- [Problem](#problem)
- [Pipeline Steps](#pipeline-steps)
- [Final Result](#final-result)


## Overview
SQL ETL pipeline in PostgreSQL: regex-based extraction of dates and IDs from messy text data, type conversion, and a production-ready master_sales view joining revenue, product, and customer tables with window function-based revenue aggregation.


## Tech stack
SQL · PostgreSQL · DBeaver


## Key techniques
- **Data cleaning:** regex pattern matching, string manipulation, COALESCE
- **Schema management:** ALTER TABLE, column type conversion (DDL)
- **Data transformation:** UPDATE/DML operations, CASE WHEN logic
- **Data modeling:** views, JOINs across multiple tables
- **Analytics:** window functions (SUM OVER PARTITION BY)

## Problem
(a nyers, strukturálatlan text_field bemutatása + screenshot)

## Pipeline Steps
'''
select * from data_revenue dr
where  dr.text_field ~ '\d{4}[-_./]\d{2}[-_./]\d{2}';  -- "~" regex (regular expression) match-> 4 digit year then separator [-_./]



-- DDL = Data Definition Language --> add column by alter table, use only once 
alter table data_revenue 
add  column date_from_text text;

--extract date
update data_revenue
set date_from_text = 
        substring(text_field from '\d{4}[-_./]\d{2}[-_./]\d{2}') --substring(<szöveg> from <minta>)
where text_field ~ '\d{4}[-_./]\d{2}[-_./]\d{2}'; 




--DDL add product_ID_from_text
alter table data_revenue 
add column product_ID_from_text text;

--extract product_ID
update data_revenue dr 
set product_id_from_text=
	substring(dr.text_field from 'P\d{3}')
where text_field ~ 'P\d{3}';




--DDL add costumer_ID_from_text
alter table data_revenue 
add column costumer_ID_from_text text;

--extract costumer_ID
update data_revenue dr 
set costumer_ID_from_text=
	substring(text_field from 'C\d{4}')
where text_field ~ 'C\d{4}';






--DDL finalize extracted data, IDs and date
alter table data_revenue 
add column final_date text,
add column final_customer_ID text,
add column final_product_ID text;

--DML final date --> Data Manipulation Language 
update data_revenue
set final_date =
    case
        when date is null then date_from_text
        else date
    end;

--DML final_product_id -->better solution than "case when end"
update data_revenue dr 
set final_product_id =
	coalesce(dr."product_ID" ,dr.product_id_from_text );

--DML final_customer_id
update data_revenue dr 
set final_customer_id =
	coalesce(dr."customer_ID" ,dr.costumer_id_from_text );

select date,dr.date_from_text,dr.final_date,dr."product_ID",dr.product_id_from_text,dr.final_product_id,dr."customer_ID" ,dr.costumer_id_from_text,dr.final_customer_id from data_revenue dr;

--final_date--
update data_revenue dr 
set final_date =
	replace(replace(replace(dr.final_date ,'.','-'),'/','-'),'_','-');

--DDL final_date--> string to date
alter table data_revenue
alter column final_date type date
using to_date(final_date,'YYYY-MM-DD');

--DDL quantity text to integer
alter table data_revenue
alter column quantity type integer
using quantity::integer;

--Check the string to date conversion
select column_name, data_type
from information_schema.columns
where table_name = 'data_revenue';


select * from data_revenue dr ;

--Drop MASTER SALES if we have
drop view if exists master_sales;

--Create the MASTER SALES table--> production ready
create view master_sales as
select
    dr.final_date,
    dr.final_product_id,
    dp.product_name,
    dr.final_customer_id,
    dc.customer_name,
    dr.country,
    dr.city,
    dr.quantity,
    dp.price_huf,
    dr.quantity * dp.price_huf as revenue_huf,
    sum(dr.quantity * dp.price_huf) over (partition by dr.final_product_id) as product_total_revenue
from data_revenue dr
left join data_product dp
    on dr.final_product_id = dp."product_ID"
left join data_costumer dc
    on dr.final_customer_id = dc."customer_ID";
'''


## Final Result
(a master_sales view bemutatása + screenshot)
