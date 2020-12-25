## Soru 2) 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.
```SQL
WITH years as (
SELECT 
  year,
  --Burada yıllara küçükten büyüğe olacak şekilde sıra numarası verildi. Oyunlar geçmişte 4 yıl ara ile gerçekleşti ama örneğin bu seneki olimpiyatlar 
  --2021'de gerçekleşecek. Eğer yılları 4 yıl aralıklarla olacak şekilde karşılaştırırsak sonrasında veri güncellendiğinde kodu da değiştirmemiz gerekecek.
  --Ya da elimizde daha büyük bir data olduğunda bazı yıllarda benzer sorunlar oluşmuş ve gözümüzden kaçmış olabilir.
  row_number() OVER(ORDER BY year) AS year_order 
FROM `dsmbootcamp.sample.summer_medals` 
  WHERE year>=1980 
  GROUP BY year 
  ORDER BY year
  ),
medals AS (
SELECT 
  athlete,
  sport,
  first_value(year) over(partition by athlete,sport order by year) as first_year,
  last_value(year) over(partition by athlete,sport order by year rows between current row and 1 following) second_year,
  last_value(year) over(partition by athlete,sport order by year rows between current row and 2 following) third_year,
  first_value(year_order) over(partition by athlete,sport order by year) as first_year_order,
  last_value(year_order) over(partition by athlete,sport order by year rows between current row and 1 following) second_year_order,
  last_value(year_order) over(partition by athlete,sport order by year rows between current row and 2 following) third_year_order,
  year_order
FROM (
  SELECT
    athlete,
    sport,
    sm.year AS year,
    year_order
  FROM 
    `dsmbootcamp.erkan_ekser.summer_medals` sm 
  INNER JOIN 
    years ON years.year=sm.year
  WHERE 
    sm.year>=1980
  ORDER BY 
    athlete,year,sport
)
ORDER BY
  athlete,year,sport)
SELECT 
  athlete,sport, first_year,second_year,third_year 
FROM 
  medals 
WHERE 
  first_year_order=second_year_order-1 AND first_year_order=third_year_order-2
```
