## Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.

```SQL
WITH table1 as (
SELECT
sport,
country,
COUNT(medal) as cnt_medal,
FROM `dsmbootcamp.erkan_ekser.summer_medals` 
WHERE year>1980 
GROUP BY sport,country),
table_2 as (
SELECT 
sport,
country,
cnt_medal,
dense_rank() over (PARTITION BY sport ORDER BY cnt_medal DESC) as rank
FROM 
table1)
Select * from table_2 WHERE rank IN (1,3,5)
```
