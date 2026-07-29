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
(a lépések: regex extrakció, coalesce, típuskonverzió — kód snippetek + screenshotok)




## Final Result
(a master_sales view bemutatása + screenshot)
