# <p align="center" style="margin-top: 0px;">🛒 Case Study #5 - Data Mart 🛒
## <p align="center"> A. Data Cleansing Steps

## Solution

View the complete syntax [*here*](https://github.com/hydaai/8-Week-SQL-Challenge/tree/main/Case%20Study%20%235%20-%20Data%20Mart/Scripts).

***

### *In a single query, perform the following operations and generate a new table named clean_weekly_sales:*
- Convert the `week_date` to a **DATE** format
- Add a `week_number` as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 
	8th to 14th will be 2 etc
- Add a `month_number` with the calendar month for each week_date value as the 3rd column
- Add a `calendar_year` column as the 4th column containing either 2018, 2019 or 2020 values
- Add a new column called `age_band` after the original segment column using the following mapping on the number inside the segment value:
	| segment| age_band |
	| -- | -- |
	| 1 | Young Adults |
	| 2 | Middle Aged |
	| 3 or 4 | Retirees |
- Add a new `demographic` column using the following mapping for the first letter in the segment values:
	| segment | demographic |
	| -- | -- |
	| C	| Couples |
	| F	| Families |
- Ensure all *null* string values with an "*unknown*" string value in the original segment column as well as the new `age_band` and `demographic` columns
- Generate a new `avg_transaction` column as the sales value divided by transactions **round**ed to 2 decimal places for each record

### Create New Table `clean_weekly_sales`
| Column | Actions |
| -- | -- |
| week_date | change datatype to **date** with **to_date**  |
| week_number | return to number of week with **extract** |
| month_number | return to number of month with **extract** |
| calendar_year | return to number of year with **extract** |
| region | no changes |
| platform | no changes |
| segment | no changes |
| age_band | Use **CASE** with conditional statement, 1 = Young Adults, 2 = Middle Aged , 3 or 4 = Retirees , null = unknown |
| demographic | Use **CASE** with conditional statement, C = Couples, F = Families, null = unknown |
| customer_type | no changes |
| transactions | change datatype to **float** |
| sales | change datatype to **float** |
| avg_transaction | Divide sales by transactions and round to 2 decimals |

#### Answer:
````sql
drop table if exists data_mart.clean_weekly_sales;
create table data_mart.clean_weekly_sales as
select 
      to_date(week_date, 'DD/MM/YY') as week_date
    , extract(week from to_date(week_date, 'DD/MM/YY')) as num_week
    , extract(month from to_date(week_date, 'DD/MM/YY')) as month 
    , extract(year from to_date(week_date, 'DD/MM/YY')) as year
    , region
    , platform
    , segment
    , (case when right(segment, 1) = '1' then 'Young Adults'
           when right(segment, 1) = '2' then 'Middle Aged'  
           when right(segment, 1) in ('3', '4') then 'Retirees' else 'unknown' end) as age_band
    , (case when left(segment, 1) = 'C' then 'Couples'
            when left(segment, 1) = 'F' then 'Families' else 'unknown' end) as demographic
    , customer_type        
    , transactions::float
    , sales::float
    , round(sales::numeric/transactions, 2) as avg_transaction
from data_mart.weekly_sales;

select *
from data_mart.clean_weekly_sales;
````

We got a new table.

***NOTE*** : *Not all output is displayed, considering the number of results and will take up space*	
week_date | num_week | month | year | region | platform | segment | age_band | demographic | customer_type | transactions | sales | avg_transaction
-- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --
2020-08-31 | 36 | 8 | 2020 | ASIA | Retail | C3 | Retirees | Couples | New | 120631 | 3656163 | 30.31
2020-08-31 | 36 | 8 | 2020 | ASIA | Retail | F1 | Young Adults | Families | New | 31574 | 996575 | 31.56
2020-08-31 | 36 | 8 | 2020 | USA | Retail | null | unknown | unknown | Guest | 529151 | 16509610 | 31.2
2020-08-31 | 36 | 8 | 2020 | EUROPE | Retail | C1 | Young Adults | Couples | New | 4517 | 141942 | 31.42
2020-08-31 | 36 | 8 | 2020 | AFRICA | Retail | C2 | Middle Aged | Couples | New | 58046 | 1758388 | 30.29
2020-08-31 | 36 | 8 | 2020 | CANADA | Shopify | F2 | Middle Aged | Families | Existing | 1336 | 243878 | 182.54
2020-08-31 | 36 | 8 | 2020 | AFRICA | Shopify | F3 | Retirees | Families | Existing | 2514 | 519502 | 206.64
2020-08-31 | 36 | 8 | 2020 | ASIA | Shopify | F1 | Young Adults | Families | Existing | 2158 | 371417 | 172.11
2020-08-31 | 36 | 8 | 2020 | AFRICA | Shopify | F2 | Middle Aged | Families | New | 318 | 49557 | 155.84
2020-08-31 | 36 | 8 | 2020 | AFRICA | Retail | C3 | Retirees | Couples | New | 111032 | 3888162 | 35.02

***

# <p align="center" style="margin-top: 0px;">👩‍💻👩‍💻👩‍💻
