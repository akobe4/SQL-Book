# Chapter 3: Time Series Analysis 

## Trending the Data

**Simple Trends**
```SQL
SELECT sales_month
	,sales
FROM retail_sales
WHERE kind_of_business = 'Retail and food services sales, total'
;
```
Results

![alt text](image.png)


![alt text](image-1.png).


```SQL
SELECT date_part('year', sales_month) AS sales_year
	,SUM(sales) AS sales
FROM retail_sales
WHERE kind_of_business = 'Retail and food services sales, total'
GROUP BY 1
;
```

Results

![alt text](image-2.png)

![](image-4.png)



**Comparing Components**
- compare the yearly sales trends for a few categories that are associated with leisure activities: book stores, sporting goods stores, and hobby stores

```SQL
SELECT date_part('year',sales_month) AS sales_year
	,kind_of_business
	,SUM(sales) AS sales
FROM retail_sales
WHERE kind_of_business IN ('Book stores','Sporting goods stores','Hobby, toy, and game stores')
GROUP BY 1, 2
ORDER BY 1,2
;
```

Results 

![alt text](image-5.png)



- sales at women's clothing stores and at men's clothing stores

```SQL 
SELECT sales_month
	,kind_of_business
	,sales
FROM retail_sales
WHERE kind_of_business IN ('Men''s clothing stores', 'Women''s clothing stores')
;
```

![alt text](image-6.png)



- for more precision you can calculate the gap between the two categories, the ratio and the percent difference between them

```SQL 
SELECT date_part('year', sales_month) AS sales_year
	,SUM(CASE WHEN kind_of_business = 'Women''s clothing stores'
			  THEN sales
			  END) AS womens_sales
	,SUM(CASE WHEN kind_of_business = 'Men''s clothing stores'
			  THEN sales
			  END) AS mens_sales
FROM retail_sales
WHERE kind_of_business IN ('Men''s clothing stores', 'Women''s clothing stores')
GROUP BY 1
ORDER BY 1
;
```

Results
![alt text](image-7.png)


- we can find the diffference, ratio and percent difference between time series in the data set

```SQL 
SELECT sales_year
	,womens_sales - mens_sales AS womens_minus_mens
	,mens_sales - womens_sales AS mens_minus_womens
FROM 
(	
	SELECT date_part('year', sales_month) AS sales_year
		,SUM(CASE WHEN kind_of_business = 'Women''s clothing stores'
				  THEN sales
				  END) AS womens_sales
	   ,SUM(CASE WHEN  kind_of_business = 'Men''s clothing stores'
		   		 THEN sales
		   		 END) AS mens_sales
	FROM retail_sales
	WHERE kind_of_business IN ('Men''s clothing stores', 'Women''s clothing stores')
	AND sales_month <= '2019-12-01'
	GROUP BY 1
)a
;
```

Results

![alt text](image-8.png)


- the subquery is not required from a query execution standpoint, since aggregations can be added to or subtracted from each other. 
- a subquery is often more legible but does add more lines to the code 
- this is an example without the subquery

```SQL
SELECT date_part('year', sales_month) AS sales_year
	,SUM(CASE WHEN kind_of_business = 'Women''s clothing stores'
			  THEN sales END)
   -
   SUM(CASE WHEN kind_of_business = 'Men''s clothing stores'
	  		THEN sales END)
   AS womens_minus_mens
FROM retail_sales
WHERE kind_of_business IN ('Men''s clothing stores', 'Women''s clothing stores')
AND sales_month <= '2019-12-01'
GROUP BY 1
;
```


Results

![alt text](image-9.png)

![alt text](image-10.png)


- ratio of these categories with men's sales as the denominator 

```SQL 
SELECT sales_year
	,womens_sales / mens_sales AS womens_times_mens
FROM 
(
	SELECT date_part('year', sales_month) AS sales_year
		  ,SUM(CASE WHEN kind_of_business = 'Women''s clothing stores'
			  		THEN sales
			  		END) AS womens_sales
		 ,SUM(CASE WHEN kind_of_business = 'Men''s clothing stores'
				   THEN sales
				   END) AS mens_sales
	FROM retail_sales
	WHERE kind_of_business IN ('Men''s clothing stores', 'Women''s clothing stores')
	AND sales_month <= '2019-12-01'
	GROUP BY 1
) a
;
```

Results 

![alt text](image-11.png)

![alt text](image-12.png)



- calculating the percent difference between sales at women's and men's clothing stores 

```SQL 
SELECT sales_year 
	,(womens_sales / mens_sales - 1) * 100 AS womens_pctg_of_mens
FROM 
(	SELECT date_part('year', sales_month) AS sales_year
 		   ,SUM(CASE WHEN kind_of_business = 'Women''s clothing stores'
			   THEN sales
			   END) AS womens_sales
 		   ,SUM(CASE WHEN kind_of_business = 'Men''s clothing stores'
			    THEN sales
			    END) AS mens_sales
 			FROM retail_sales
 			WHERE kind_of_business in ('Men''s clothing stores', 'Women''s clothing stores')
 			AND sales_month <= '2019-12-01'
  			GROUP BY 1
) a 
;
```


Results 


![alt text](image-13.png)



**Percent of Total Calculations**

