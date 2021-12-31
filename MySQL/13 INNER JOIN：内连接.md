# MySQL INNER JOIN：内连接



在《[MySQL交叉连接](http://c.biancheng.net/view/7456.html)》一节中我们了解了 MySQL 的交叉连接，本节主要介绍多表查询的另一种方式——内连接。

内连接（INNER JOIN）主要通过设置连接条件的方式，来移除查询结果中某些数据行的交叉连接。简单来说，就是利用条件表达式来消除交叉连接的某些数据行。

内连接使用 **INNER JOIN** 关键字连接两张表，并使用 ON 子句来设置连接条件。如果没有连接条件，INNER JOIN 和 CROSS JOIN 在语法上是等同的，两者可以互换。



内连接的语法格式如下：

`SELECT <字段名> FROM <表1> INNER JOIN <表2> [ON子句]`

语法说明如下。

- 字段名：需要查询的字段名称。
- <表1><表2>：需要内连接的表名。
- INNER JOIN ：内连接中可以省略 INNER 关键字，只用关键字 JOIN。
- **ON 子句：用来设置内连接的连接条件。**

> INNER JOIN 也可以使用 WHERE 子句指定连接条件，但是 INNER JOIN ... ON 语法是官方的标准写法，而且 WHERE 子句在某些时候会影响查询的性能。



多个表内连接时，在 FROM 后连续使用 INNER JOIN 或 JOIN 即可。

内连接可以查询两个或两个以上的表。为了让大家更好的理解，暂时只讲解两个表的连接查询。



#### 例 1

在 tb_students_info 表和 tb_course 表之间，使用内连接查询学生姓名和相对应的课程名称，SQL 语句和运行结果如下。

```mysql
mysql> SELECT s.name,c.course_name FROM tb_students_info s INNER JOIN tb_course c 
    -> ON s.course_id = c.id;
+--------+-------------+
| name   | course_name |
+--------+-------------+
| Dany   | Java        |
| Green  | MySQL       |
| Henry  | Java        |
| Jane   | Python      |
| Jim    | MySQL       |
| John   | Go          |
| Lily   | Go          |
| Susan  | C++         |
| Thomas | C++         |
| Tom    | C++         |
+--------+-------------+
10 rows in set (0.00 sec)
```

在这里的查询语句中，两个表之间的关系通过 INNER JOIN 指定，连接的条件使用 ON 子句给出。

 

注意：当对多个表进行查询时，要在 SELECT 语句后面指定字段是来源于哪一张表。因此，在多表查询时，SELECT 语句后面的写法是`表名.列名`。另外，如果表名非常长的话，也可以给表设置别名，这样就可以直接在 SELECT 语句后面写上`表的别名.列名`。