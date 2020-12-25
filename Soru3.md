## Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var. Bu çalışmada çıkarmak istediğimiz bilgi, günün her bir dakikası için aktif kullanıcı sayısının hesaplanması.

```SQL
-- Burada hll_count fonksiyonu ile tablodan dakika bazlı device_id sayıldı. 
CREATE OR REPLACE TABLE
  erkan_ekser.active_users_per_minute AS
SELECT
  TIMESTAMP_TRUNC(view_ts,minute) AS view_minute,
  hll_count.init(deviceid,
    15) AS cnt_device,
FROM
  erkan_ekser.pageview
GROUP BY
  1
ORDER BY
  1
  
--Burada ise sorguyla dakika başı aktif kullanıcı sayısı hesaplandı.
SELECT
  view_minute,
  SUM(approx_dist_user) OVER (ROWS BETWEEN 4 PRECEDING AND CURRENT ROW) AS total_active_users
FROM (
  SELECT
    view_minute,
    hll_count.EXTRACT(cnt_device) approx_dist_user
  FROM
    erkan_ekser.active_users_per_minute )
ORDER BY
  view_minute
  ```
