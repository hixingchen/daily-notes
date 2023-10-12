## 关联表批量更新数据

```sql
UPDATE ibms_space_structure a
RIGHT JOIN ( SELECT SUBSTRING( NAME, 1, 2 ) AS NAME, space_center AS space_center FROM `ibms_space_structure` WHERE `name` LIKE '%社区%' ) b ON a.`name` LIKE CONCAT( '%', b.`name`, '%' ) 
SET a.space_center = b.space_center
```

