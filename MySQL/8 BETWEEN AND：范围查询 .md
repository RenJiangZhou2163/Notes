# MySQL BETWEEN AND：范围查询



MySQL 提供了 **BETWEEN AND** 关键字，用来判断字段的数值是否在指定范围内。

BETWEEN AND 需要两个参数，即范围的起始值和终止值。如果字段值在指定的范围内，则这些记录被返回。如果不在指定范围内，则不会被返回。

使用 BETWEEN AND 的基本语法格式如下：

```
[NOT] BETWEEN 取值1 AND 取值2
```

其中：

- NOT：可选参数，表示指定范围之外的值。如果字段值不满足指定范围内的值，则这些记录被返回。
- 取值1：表示范围的起始值。
- 取值2：表示范围的终止值。


BETWEEN AND 和 NOT BETWEEN AND 关键字在查询指定范围内的记录时很有用。例如，查询学生的年龄段、出生日期，员工的工资水平等。

#### 例 1

在表 tb_students_info 中查询年龄在 20 到 23 之间的学生姓名和年龄，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age FROM tb_students_info 
    -> WHERE age BETWEEN 20 AND 23;
+--------+------+
| name   | age  |
+--------+------+
| Green  |   23 |
| Henry  |   23 |
| Jane   |   22 |
| John   |   21 |
| Lily   |   22 |
| Susan  |   23 |
| Thomas |   22 |
| Tom    |   23 |
+--------+------+
8 rows in set (0.00 sec)
```

查询结果中包含学生年龄为 20 和 23 的记录，这就说明，在 MySQL 中，BETWEEN AND 能匹配指定范围内的所有值，包括起始值和终止值。



#### 例 2

在表 tb_students_info 中查询年龄不在 20 到 23 之间的学生姓名和年龄，SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,age FROM tb_students_info 
    -> WHERE age NOT BETWEEN 20 AND 23;
+------+------+
| name | age  |
+------+------+
| Dany |   25 |
| Jim  |   24 |
+------+------+
2 rows in set (0.00 sec)
```



#### 例 3

在表 tb_students_info 中查询注册日期在 2015-10-01 和 2016-05-01 之间的学生信息。SQL 语句和运行结果如下。

```mysql
mysql> SELECT name,login_date FROM tb_students_info
    -> WHERE login_date BETWEEN '2015-10-01' AND '2016-05-01';
+-------+------------+
| name  | login_date |
+-------+------------+
| Jim   | 2016-01-15 |
| John  | 2015-11-11 |
| Lily  | 2016-02-26 |
| Susan | 2015-10-01 |
+-------+------------+
4 rows in set (0.00 sec)
```

