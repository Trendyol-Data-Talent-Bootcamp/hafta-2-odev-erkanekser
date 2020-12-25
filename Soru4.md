## SORU 4: sample.content_category tablosunu sample.content_category_20201222_00_59 tablosu ile güncelleme.
```SQL 

MERGE
  erkan_ekser.content_category t
USING
  erkan_ekser.content_category_20201222_00_59 s
ON
  t.id = s.id
  WHEN MATCHED THEN UPDATE SET t.cdc_date = s.cdc_date,t.category = s.category
  WHEN NOT MATCHED BY SOURCE
  THEN
UPDATE
SET
  t.is_deleted=true
  WHEN NOT MATCHED BY TARGET
  THEN
INSERT
  (cdc_date,
    is_deleted,
    id,
    category)
VALUES
  (s.cdc_date,s.is_deleted,s.id,s.category)

-- Çözümümüzün doğruluğunu test etmek için burada content_category_target tablosu ile oluşturduğumuz tabloyu kıyaslıyoruz. 

WITH table_1
AS (select farm_fingerprint(to_json_string(t1)) as hash1
from `dsmbootcamp.erkan_ekser.content_category_target` t1),

table_2 as (SELECT farm_fingerprint(to_json_string(t2)) as hash2
from `dsmbootcamp.erkan_ekser.content_category` t2  
)

select * from table_1 full outer join table_2 ON table_1.hash1=table_2.hash2 WHERE hash1 is null or hash2 is null
```
