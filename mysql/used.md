### 查询某表的所有字段
```mysql
select column_name from information_schema.columns where table_schema='库名' and table_name='表名' order by column_name;
```
