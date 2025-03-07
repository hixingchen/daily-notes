# 根据时间分组，获取各时间段内最新的值

```sql
SELECT
	DATE_FORMAT( recordHours.collect_time, '%H' ) AS DATE,
	ROUND( SUBSTRING_INDEX( MAX( CONCAT( recordHours.collect_time, ',', recordHours.collect_value )), ',', -1 ), 2 ) AS VALUE	
FROM
	ibms_channel_archives a
	LEFT JOIN ibms_record_hour recordHours ON a.id = recordHours.channel_id 
WHERE
	a.del_flag = 0 
	AND DATE_FORMAT( recordHours.collect_time, '%Y-%m-%d %H' ) BETWEEN DATE_FORMAT( DATE_SUB( now(), INTERVAL 6 HOUR ), '%Y-%m-%d %H' ) 
		AND DATE_FORMAT( now(), '%Y-%m-%d %H' ) 
GROUP BY
	DATE_FORMAT( recordHours.collect_time, '%H' )
```

# 获取本季度时间区间内的数据

```sql
where DATE_FORMAT(DATE_SUB(DATE_FORMAT('数据库时间字段','%Y-%m-%d'),interval 2 month ),'%Y') =
	DATE_FORMAT(DATE_SUB(now(),interval 2 month ),'%Y')
    and QUARTER(DATE_SUB(DATE_FORMAT('数据库时间字段','%Y-%m-%d'),interval 2 month )) =
    QUARTER(DATE_SUB(now(),interval 2 month ))
```

# 关联表批量更新数据

```sql
UPDATE ibms_space_structure a
RIGHT JOIN ( SELECT SUBSTRING( NAME, 1, 2 ) AS NAME, space_center AS space_center FROM `ibms_space_structure` WHERE `name` LIKE '%社区%' ) b ON a.`name` LIKE CONCAT( '%', b.`name`, '%' ) 
SET a.space_center = b.space_center
```

# 获取表的所有字段

```sql
SELECT
	COLUMN_NAME
FROM
	INFORMATION_SCHEMA.COLUMNS
WHERE
	TABLE_SCHEMA = 'zhhq_xiangjiang'
	AND TABLE_NAME = 'ibms_kpi_realtime'
	AND COLUMN_NAME like 'kpi_%'
	AND COLUMN_NAME not like '%_time'
```

# 同步临时表与正式表数据

```sql
INSERT INTO carbon_record_month_all SELECT * FROM carbon_record_month_all_temp
		ON DUPLICATE KEY
		UPDATE carbon_specific_type = VALUES(carbon_specific_type)
```

