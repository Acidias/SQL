# SQL
# You can check the project @ https://docs.google.com/presentation/d/1JscXZb7PGmGv3OlxXinunORKLV75FB_MNKYCRUPg_fs/edit?usp=sharing

WITH months AS
(SELECT
  '2017-01-01' as first_day,
  '2017-01-31' as last_day
UNION
SELECT
  '2017-02-01' as first_day,
  '2017-02-28' as last_day
UNION
SELECT
  '2017-03-01' as first_day,
  '2017-03-31' as last_day
),
cross_join AS
(SELECT * FROM subscriptions
  CROSS JOIN months
),
status AS 
(SELECT 
id,
first_day AS month,
CASE
  WHEN (subscription_start < first_day) AND
(subscription_end > first_day OR subscription_end IS NULL) AND (segment = 87) THEN 1
  ELSE 0
END AS is_active_87,
CASE
  WHEN (subscription_start < first_day) AND
(subscription_end > first_day OR subscription_end IS NULL) AND (segment = 30) THEN 1
  ELSE 0
END AS is_active_30,
CASE
  WHEN (subscription_end BETWEEN first_day AND last_day) AND (segment = 87) THEN 1
  ELSE 0
  END AS is_canceled_87,
CASE
  WHEN (subscription_end BETWEEN first_day AND last_day) AND (segment = 30) THEN 1
  ELSE 0
  END AS is_canceled_30 
FROM cross_join
), sumed AS 
(SELECT
SUM(is_active_87) AS active_87,
SUM(is_active_30) AS active_30,
SUM(is_canceled_87) AS canceled_87,
SUM(is_canceled_30) AS canceled_30
FROM status
) SELECT * FROM sumed;
