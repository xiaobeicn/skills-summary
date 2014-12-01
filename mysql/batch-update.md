### 一条sql语句实现批量更新

```mysql
UPDATE categories 
    SET display_order = CASE id 
        WHEN 1 THEN 3 
        WHEN 2 THEN 4 
        WHEN 3 THEN 5 
    END
WHERE id IN (1,2,3)
```
这句sql的意思是，更新display_order 字段，如果id=1 则display_order 的值为3，如果id=2 则 display_order 的值为4，如果id=3 则 display_order 的值为5。
如果更新多个值的话，只需要稍加修改：

```mysql
UPDATE categories 
    SET display_order = CASE id 
        WHEN 1 THEN 3 
        WHEN 2 THEN 4 
        WHEN 3 THEN 5 
    END, 
    title = CASE id 
        WHEN 1 THEN 'New Title 1'
        WHEN 2 THEN 'New Title 2'
        WHEN 3 THEN 'New Title 3'
    END
WHERE id IN (1,2,3)
```
但是要在业务中运用，需要结合服务端语言，这里以php为例，构造这条mysql语句：

```mysql
$display_order = array( 
    1 => 4, 
    2 => 1, 
    3 => 2, 
    4 => 3, 
    5 => 9, 
    6 => 5, 
    7 => 8, 
    8 => 9 
); 
$ids = implode(',', array_keys($display_order)); 
$sql = "UPDATE categories SET display_order = CASE id "; 
foreach ($display_order as $id => $ordinal) { 
    $sql .= sprintf("WHEN %d THEN %d ", $id, $ordinal); 
} 
$sql .= "END WHERE id IN ($ids)"; 
echo $sql;
```
这个例子，有8条记录进行更新

### 性能分析
当我使用上万条记录利用mysql批量更新，发现使用最原始的批量update发现性能很差，将网上看到的总结一下一共有以下三种办法：

* 批量update，一条记录update一次，性能很差

```mysql
update test_tbl set dr='2' where id=1;
```
* replace into 或者insert into ...on duplicate key update

```mysql
replace into test_tbl (id,dr) values (1,'2'),(2,'3'),...(x,'y');
或者使用
insert into test_tbl (id,dr) values  (1,'2'),(2,'3'),...(x,'y') on duplicate key update dr=values(dr);
```
* 创建临时表，先更新临时表，然后从临时表中update

```mysql
create temporary table tmp(id int(4) primary key,dr varchar(50));
insert into tmp values  (0,'gone'), (1,'xx'),...(m,'yy');
update test_tbl, tmp set test_tbl.dr=tmp.dr where test_tbl.id=tmp.id;
```
注意：这种方法需要用户有temporary 表的create 权限。

----

