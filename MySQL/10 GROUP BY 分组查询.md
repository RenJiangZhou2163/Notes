# MySQL使用GROUP BY分组查询



在 MySQL 中，GROUP BY 关键字可以根据一个或多个字段对查询结果进行分组。



使用 GROUP BY 关键字的语法格式如下：

`GROUP BY <字段名>`

其中，“字段名”表示需要分组的字段名称，多个字段时用逗号隔开。



## GROUP BY单独使用

单独使用 GROUP BY 关键字时，查询结果会只显示每个分组的第一条记录。

#### 例 1

下面根据 tb_students_info 表中的 sex 字段进行分组查询，SQL 语句和运行结果如下：

```
mysql> SELECT `name`,`sex` FROM tb_students_info 
    -> GROUP BY sex;
+-------+------+
| name  | sex  |
+-------+------+
| Henry | 女   |
| Dany  | 男   |
+-------+------+
2 rows in set (0.01 sec)
```

结果中只显示了两条记录，这两条记录的 sex 字段的值分别为“女”和“男”。



## GROUP BY 与 GROUP_CONCAT() 

GROUP BY 关键字可以和 GROUP_CONCAT() 函数一起使用。GROUP_CONCAT() 函数会把每个分组的字段值都显示出来。



#### 例 2

下面根据 tb_students_info 表中的 sex 字段进行分组查询，使用 GROUP_CONCAT() 函数将每个分组的 name 字段的值都显示出来。SQL 语句和运行结果如下：

```
mysql> SELECT `sex`, GROUP_CONCAT(name) FROM tb_students_info 
    -> GROUP BY sex;
+------+----------------------------+
| sex  | GROUP_CONCAT(name)         |
+------+----------------------------+
| 女   | Henry,Jim,John,Thomas,Tom  |
| 男   | Dany,Green,Jane,Lily,Susan |
+------+----------------------------+
2 rows in set (0.00 sec)
```

由结果可以看到，查询结果分为两组，sex 字段值为“女”的是一组，值为“男”的是一组，且每组的学生姓名都显示出来了。



#### 例 3

下面根据 tb_students_info 表中的 age 和 sex 字段进行分组查询。SQL 语句和运行结果如下：

```
mysql> SELECT age,sex,GROUP_CONCAT(name) FROM tb_students_info 
    -> GROUP BY age,sex;
+------+------+--------------------+
| age  | sex  | GROUP_CONCAT(name) |
+------+------+--------------------+
|   21 | 女   | John               |
|   22 | 女   | Thomas             |
|   22 | 男   | Jane,Lily          |
|   23 | 女   | Henry,Tom          |
|   23 | 男   | Green,Susan        |
|   24 | 女   | Jim                |
|   25 | 男   | Dany               |
+------+------+--------------------+
7 rows in set (0.00 sec)
```

上面实例在分组过程中，先按照 age 字段进行分组，当 age 字段值相等时，再把 age 字段值相等的记录按照 sex 字段进行分组。

 

多个字段分组查询时，会先按照第一个字段进行分组。如果第一个字段中有相同的值，MySQL 才会按照第二个字段进行分组。如果第一个字段中的数据都是唯一的，那么 MySQL 将不再对第二个字段进行分组。



## GROUP BY 与聚合函数

在数据统计时，GROUP BY 关键字经常和聚合函数一起使用。

聚合函数包括 COUNT()，SUM()，AVG()，MAX() 和 MIN()。其中，COUNT() 用来统计记录的条数；SUM() 用来计算字段值的总和；AVG() 用来计算字段值的平均值；MAX() 用来查询字段的最大值；MIN() 用来查询字段的最小值。

> 关于聚合函数的详细内容可以阅读学习《[MySQL函数大全](http://c.biancheng.net/mysql/function/)》一节。



#### 例 4

下面根据 tb_students_info 表的 sex 字段进行分组查询，使用 COUNT() 函数计算每一组的记录数。SQL 语句和运行结果如下：

```
mysql> SELECT sex,COUNT(sex) FROM tb_students_info 
    -> GROUP BY sex;
+------+------------+
| sex  | COUNT(sex) |
+------+------------+
| 女   |          5 |
| 男   |          5 |
+------+------------+
2 rows in set (0.00 sec)
```

结果显示，sex 字段值为“女”的记录是一组，有 5 条记录；sex 字段值为“男”的记录是一组，有 5 条记录。



## GROUP BY 与 WITH ROLLUP

**WITH POLLUP** 关键字用来在所有记录的最后加上一条记录，这条记录是上面所有记录的总和，即统计记录数量。



#### 例 5

下面根据 tb_students_info 表中的 sex 字段进行分组查询，并使用 WITH ROLLUP 显示记录的总和。

```
mysql> SELECT sex,GROUP_CONCAT(name) FROM tb_students_info 
    ->GROUP BY sex WITH ROLLUP;
+------+------------------------------------------------------+
| sex  | GROUP_CONCAT(name)                                   |
+------+------------------------------------------------------+
| 女   | Henry,Jim,John,Thomas,Tom                            |
| 男   | Dany,Green,Jane,Lily,Susan                           |
| NULL | Henry,Jim,John,Thomas,Tom,Dany,Green,Jane,Lily,Susan |
+------+------------------------------------------------------+
3 rows in set (0.00 sec)
```

查询结果显示，GROUP_CONCAT(name) 显示了每个分组的 name 字段值。同时，最后一条记录的 GROUP_CONCAT(name) 字段的值刚好是上面分组 name 字段值的总和。

