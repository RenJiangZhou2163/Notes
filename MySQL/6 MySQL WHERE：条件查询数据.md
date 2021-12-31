# MySQL WHERE：条件查询数据



在 MySQL 中，如果需要有条件的从数据表中查询数据，可以使用 WHERE 关键字来指定查询条件。



使用 WHERE 关键字的语法格式如下：

`WHERE 查询条件`



查询条件可以是：

- 带比较运算符和逻辑运算符的查询条件

- 带 BETWEEN AND 关键字的查询条件

- 带 IS NULL 关键字的查询条件

- 带 IN 关键字的查询条件

- 带 LIKE 关键字的查询条件

  

## 单一条件的查询语句

单一条件指的是在 WHERE 关键字后只有一个查询条件。

#### 例 1

在 tb_students_info 数据表中查询身高为 170cm 的学生姓名，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,height FROM tb_students_info
    -> WHERE height=170;
+-------+--------+
| name  | height |
+-------+--------+
| Susan |    170 |
+-------+--------+
1 row in set (0.17 sec)
```

可以看到，查询结果中记录的 height 字段的值等于 170。如果根据指定的条件进行查询时，数据表中没有符合查询条件的记录，系统会提示“Empty set(0.00sec)”。

#### 例 2

在 tb_students_info 数据表中查询年龄小于 22 的学生姓名，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age FROM tb_students_info
    -> WHERE age<22;
+------+------+
| name | age  |
+------+------+
| John |   21 |
+------+------+
1 row in set (0.05 sec)
```

可以看到，查询结果中所有记录的 age 字段的值均小于 22 岁，而大于或等于 22 岁的记录没有被返回。



## 多条件的查询语句

在 WHERE 关键词后可以有多个查询条件，这样能够使查询结果更加精确。多个查询条件时用逻辑运算符 AND（&&）、OR（||）或 XOR 隔开。

- AND：记录满足所有查询条件时，才会被查询出来。
- OR：记录满足任意一个查询条件时，才会被查询出来。
- XOR：记录满足其中一个条件，并且不满足另一个条件时，才会被查询出来。

#### 例 3

在 tb_students_info 表中查询 age 大于 21，并且 height 大于等于 175 的学生信息，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age,height FROM tb_students_info 
    -> WHERE age>21 AND height>=175;
+--------+------+--------+
| name   | age  | height |
+--------+------+--------+
| Henry  |   23 |    185 |
| Jim    |   24 |    175 |
| Thomas |   22 |    178 |
+--------+------+--------+
3 rows in set (0.00 sec)
```

可以看到，查询结果中所有记录的 age 字段都大于 21 且 height 字段都大于等于 175。

#### 例 4

在 tb_students_info 表中查询 age 大于 21，或者 height 大于等于 175 的学生信息，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age,height FROM tb_students_info 
    -> WHERE age>21 OR height>=175;
+--------+------+--------+
| name   | age  | height |
+--------+------+--------+
| Dany   |   25 |    160 |
| Green  |   23 |    158 |
| Henry  |   23 |    185 |
| Jane   |   22 |    162 |
| Jim    |   24 |    175 |
| Lily   |   22 |    165 |
| Susan  |   23 |    170 |
| Thomas |   22 |    178 |
| Tom    |   23 |    165 |
+--------+------+--------+
9 rows in set (0.00 sec)
```

可以看到，查询结果中所有记录的 age 字段都大于 21 或者 height 字段都大于等于 175。

#### 例 5

在 tb_students_info 表中查询 age 大于 21，并且 height 小于 175 的学生信息和 age 小于 21，并且 height 大于等于 175 的学生信息，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age,height FROM tb_students_info 
    -> WHERE age>21 XOR height>=175;
+-------+------+--------+
| name  | age  | height |
+-------+------+--------+
| Dany  |   25 |    160 |
| Green |   23 |    158 |
| Jane  |   22 |    162 |
| Lily  |   22 |    165 |
| Susan |   23 |    170 |
| Tom   |   23 |    165 |
+-------+------+--------+
7 rows in set (0.00 sec)
```

可以看到，查询结果中所有记录的 age 字段都大于 21 且 height 字段都小于 175。tb_students_info 数据表中没有 age 字段小于 21 且 height 字段大于等于 175 的记录。

> **OR、AND 和 XOR 可以一起使用，但是在使用时要注意运算符的优先级。关于 MySQL 中运算符的优先级可阅读学习《[MySQL运算符的优先级](http://c.biancheng.net/view/7399.html)》一节。**

查询条件越多，查询出来的记录就会越少。因为，设置的条件越多，查询语句的限制就更多，能够满足所有条件的记录就更少。为了使查询出来的记录正是自己想要的，可以在 WHERE 语句中将查询条件设置的更加具体。