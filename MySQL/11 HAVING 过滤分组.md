# MySQL HAVING：过滤分组



在 MySQL 中，可以使用 HAVING 关键字对分组后的数据进行过滤。



使用 HAVING 关键字的语法格式如下：

`HAVING <查询条件>`



HAVING 关键字和 WHERE 关键字都可以用来过滤数据，且 HAVING 支持 WHERE 关键字中所有的操作符和语法。



但是 WHERE 和 HAVING 关键字也存在以下几点差异：

- 一般情况下，**WHERE 用于过滤数据行，而 HAVING 用于过滤分组**。
- WHERE 查询条件中不可以使用聚合函数，而 HAVING 查询条件中可以使用聚合函数。
- WHERE 在数据分组前进行过滤，而 HAVING 在数据分组后进行过滤 。
- WHERE 针对数据库文件进行过滤，而 HAVING 针对查询结果进行过滤。也就是说，**WHERE 根据数据表中的字段直接进行过滤，而 HAVING 是根据前面已经查询出的字段进行过滤**。
- WHERE 查询条件中不可以使用字段别名，而 HAVING 查询条件中可以使用字段别名。


下面通过实例让大家更直观的了解 WHERE 和 HAVING 关键字的相同点和不同点。

#### 例 1

分别使用 HAVING 和 WHERE 关键字查询出 tb_students_info 表中身高大于 150 的学生姓名，性别和身高。SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,sex,height FROM tb_students_info 
    -> HAVING height>150;
+--------+------+--------+
| name   | sex  | height |
+--------+------+--------+
| Dany   | 男   |    160 |
| Green  | 男   |    158 |
| Henry  | 女   |    185 |
| Jane   | 男   |    162 |
| Jim    | 女   |    175 |
| John   | 女   |    172 |
| Lily   | 男   |    165 |
| Susan  | 男   |    170 |
| Thomas | 女   |    178 |
| Tom    | 女   |    165 |
+--------+------+--------+
10 rows in set (0.00 sec)

mysql> SELECT name,sex,height FROM tb_students_info 
    -> WHERE height>150;
+--------+------+--------+
| name   | sex  | height |
+--------+------+--------+
| Dany   | 男   |    160 |
| Green  | 男   |    158 |
| Henry  | 女   |    185 |
| Jane   | 男   |    162 |
| Jim    | 女   |    175 |
| John   | 女   |    172 |
| Lily   | 男   |    165 |
| Susan  | 男   |    170 |
| Thomas | 女   |    178 |
| Tom    | 女   |    165 |
+--------+------+--------+
10 rows in set (0.00 sec)
```

上述实例中，因为在 SELECT 关键字后已经查询出了 height 字段，所以 HAVING 和 WHERE 都可以使用。但是如果 SELECT 关键字后没有查询出 height 字段，MySQL 就会报错。



#### 例 2

使用 HAVING 和 WHERE 关键字分别查询出 tb_students_info 表中身高大于 150 的学生姓名和性别（与例 1 相比，这次没有查询 height 字段）。SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,sex FROM tb_students_info 
    -> WHERE height>150;
+--------+------+
| name   | sex  |
+--------+------+
| Dany   | 男   |
| Green  | 男   |
| Henry  | 女   |
| Jane   | 男   |
| Jim    | 女   |
| John   | 女   |
| Lily   | 男   |
| Susan  | 男   |
| Thomas | 女   |
| Tom    | 女   |
+--------+------+
10 rows in set (0.00 sec)

mysql> SELECT name,sex FROM tb_students_info HAVING height>150;
ERROR 1054 (42S22): Unknown column 'height' in 'having clause'
```

由结果可以看出，如果 SELECT 关键字后没有查询出 HAVING 查询条件中使用的 height 字段，MySQL 会提示错误信息：“having子句”中的列“height”未知”。



#### 例 3

根据 height 字段对 tb_students_info 表中的数据进行分组，并使用 HAVING 和 WHERE 关键字分别查询出分组后平均身高大于 170 的学生姓名、性别和身高。SQL 语句和运行结果如下。

```mysql
mysql> SELECT GROUP_CONCAT(name),sex,height FROM tb_students_info 
    -> GROUP BY height 
    -> HAVING AVG(height)>170;
+--------------------+------+--------+
| GROUP_CONCAT(name) | sex  | height |
+--------------------+------+--------+
| John               | 女   |    172 |
| Jim                | 女   |    175 |
| Thomas             | 女   |    178 |
| Henry              | 女   |    185 |
+--------------------+------+--------+
4 rows in set (0.00 sec)

mysql> SELECT GROUP_CONCAT(name),sex,height FROM tb_students_info WHERE AVG(height)>170 GROUP BY height;
ERROR 1111 (HY000): Invalid use of group function
```

由结果可以看出，如果在 WHERE 查询条件中使用聚合函数，MySQL 会提示错误信息：无效使用组函数。

