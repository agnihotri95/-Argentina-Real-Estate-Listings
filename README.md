# Argentina Real Estate Listings
SQL Queries exploring Argentina Real Estate Listings Dataset on Google BigQuery Dataset


### finding the maximum house price in the data set
```sql
SELECT max(price) FROM `properati-data-public.properties_ar.properties_rent_201501` LIMIT 1000
```
query result
	
585000.0


