# 关联表批量更新数据

```sql
UPDATE ibms_space_structure a
RIGHT JOIN ( SELECT SUBSTRING( NAME, 1, 2 ) AS NAME, space_center AS space_center FROM `ibms_space_structure` WHERE `name` LIKE '%社区%' ) b ON a.`name` LIKE CONCAT( '%', b.`name`, '%' ) 
SET a.space_center = b.space_center
```

# 获取本季度时间区间内的数据

```sql
where DATE_FORMAT(DATE_SUB(DATE_FORMAT('数据库时间字段','%Y-%m-%d'),interval 2 month ),'%Y') =
	DATE_FORMAT(DATE_SUB(now(),interval 2 month ),'%Y')
    and QUARTER(DATE_SUB(DATE_FORMAT('数据库时间字段','%Y-%m-%d'),interval 2 month )) =
    QUARTER(DATE_SUB(now(),interval 2 month ))
```

