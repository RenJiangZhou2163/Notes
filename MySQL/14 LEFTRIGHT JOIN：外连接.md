# MySQL LEFT/RIGHT JOIN：外连接



在《[MySQL内连接](http://c.biancheng.net/view/7407.html)》一节我们了解了 MySQL 的内连接。内连接的查询结果都是符合连接条件的记录，而外连接会先将连接的表分为基表和参考表，再以基表为依据返回满足和不满足条件的记录。

外连接可以分为左外连接和右外连接，下面根据实例分别介绍左外连接和右外连接。



## 左连接

左外连接又称为左连接，使用 **LEFT OUTER JOIN** 关键字连接两个表，并使用 ON 子句来设置连接条件。



左连接的语法格式如下：

`SELECT <字段名> FROM <表1> LEFT OUTER JOIN <表2> <ON子句>`

语法说明如下。

- 字段名：需要查询的字段名称。
- <表1><表2>：需要左连接的表名。
- LEFT OUTER JOIN：左连接中可以省略 OUTER 关键字，只使用关键字 LEFT JOIN。
- ON 子句：用来设置左连接的连接条件，不能省略。


上述语法中，“表1”为基表，“表2”为参考表。左连接查询时，可以查询出“表1”中的所有记录和“表2”中匹配连接条件的记录。如果“表1”的某行在“表2”中没有匹配行，那么在返回结果中，“表2”的字段值均为空值（NULL）。



#### 例 1

在进行左连接查询之前，我们先查看 tb_course 和 tb_students_info 两张表中的数据。SQL 语句和运行结果如下。

```
mysql> SELECT * FROM tb_course;
+----+-------------+
| id | course_name |
+----+-------------+
|  1 | Java        |
|  2 | MySQL       |
|  3 | Python      |
|  4 | Go          |
|  5 | C++         |
|  6 | HTML        |
+----+-------------+
6 rows in set (0.00 sec)

mysql> SELECT * FROM tb_students_info;
+----+--------+------+------+--------+-----------+
| id | name   | age  | sex  | height | course_id |
+----+--------+------+------+--------+-----------+
|  1 | Dany   |   25 | 男   |    160 |         1 |
|  2 | Green  |   23 | 男   |    158 |         2 |
|  3 | Henry  |   23 | 女   |    185 |         1 |
|  4 | Jane   |   22 | 男   |    162 |         3 |
|  5 | Jim    |   24 | 女   |    175 |         2 |
|  6 | John   |   21 | 女   |    172 |         4 |
|  7 | Lily   |   22 | 男   |    165 |         4 |
|  8 | Susan  |   23 | 男   |    170 |         5 |
|  9 | Thomas |   22 | 女   |    178 |         5 |
| 10 | Tom    |   23 | 女   |    165 |         5 |
| 11 | LiMing |   22 | 男   |    180 |         7 |
+----+--------+------+------+--------+-----------+
11 rows in set (0.00 sec)
```

在 tb_students_info 表和 tb_course 表中查询所有学生姓名和相对应的课程名称，包括没有课程的学生，SQL 语句和运行结果如下。

```
mysql> SELECT s.name,c.course_name FROM tb_students_info s LEFT OUTER JOIN tb_course c 
    -> ON s.`course_id`=c.`id`;
+--------+-------------+
| name   | course_name |
+--------+-------------+
| Dany   | Java        |
| Henry  | Java        |
| NULL   | Java        |
| Green  | MySQL       |
| Jim    | MySQL       |
| Jane   | Python      |
| John   | Go          |
| Lily   | Go          |
| Susan  | C++         |
| Thomas | C++         |
| Tom    | C++         |
| LiMing | NULL        |
+--------+-------------+
12 rows in set (0.00 sec)
```

可以看到，运行结果显示了 12 条记录，name 为 LiMing 的学生目前没有课程，因为对应的 tb_course 表中没有该学生的课程信息，所以该条记录只取出了 tb_students_info 表中相应的值，而从 tb_course 表中取出的值为 NULL。



## 右连接

右外连接又称为右连接，右连接是左连接的反向连接。使用 **RIGHT OUTER JOIN** 关键字连接两个表，并使用 ON 子句来设置连接条件。

右连接的语法格式如下：

SELECT <字段名> FROM <表1> RIGHT OUTER JOIN <表2> <ON子句>

语法说明如下。

- 字段名：需要查询的字段名称。
- <表1><表2>：需要右连接的表名。
- RIGHT OUTER JOIN：右连接中可以省略 OUTER 关键字，只使用关键字 RIGHT JOIN。
- ON 子句：用来设置右连接的连接条件，不能省略。


与左连接相反，右连接以“表2”为基表，“表1”为参考表。右连接查询时，可以查询出“表2”中的所有记录和“表1”中匹配连接条件的记录。如果“表2”的某行在“表1”中没有匹配行，那么在返回结果中，“表1”的字段值均为空值（NULL）。

#### 例 2

在 tb_students_info 表和 tb_course 表中查询所有课程，包括没有学生的课程，SQL 语句和运行结果如下。

```
mysql> SELECT s.name,c.course_name FROM tb_students_info s RIGHT OUTER JOIN tb_course c 
    -> ON s.`course_id`=c.`id`;
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
| NULL   | HTML        |
+--------+-------------+
11 rows in set (0.00 sec)
```

可以看到，结果显示了 11 条记录，名称为 HTML 的课程目前没有学生，因为对应的 tb_students_info 表中并没有该学生的信息，所以该条记录只取出了 tb_course 表中相应的值，而从 tb_students_info 表中取出的值为 NULL。

> 多个表左/右连接时，在 ON 子句后连续使用 LEFT/RIGHT OUTER JOIN 或 LEFT/RIGHT JOIN 即可。

使用外连接查询时，一定要分清需要查询的结果，是需要显示左表的全部记录还是右表的全部记录，然后选择相应的左连接和右连接。

