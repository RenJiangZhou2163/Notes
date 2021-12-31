# MySQL MIN函数：查询指定列的最小值



MIN() 函数是用来返回查询列中的最小值。



为了方便理解，首先创建一个学生成绩表 tb_students_score，学生成绩表的数据内容如下所示。

```mysql
mysql> use test_db;
Database changed
mysql> SELECT * FROM tb_students_score;
+--------------+---------------+
| student_name | student_score |
+--------------+---------------+
| Dany         |            90 |
| Green        |            99 |
| Henry        |            95 |
| Jane         |            98 |
| Jim          |            88 |
| John         |            94 |
| Lily         |           100 |
| Susan        |            96 |
| Thomas       |            93 |
| Tom          |            89 |
+--------------+---------------+
10 rows in set (0.13 sec)
```

【实例】在 tb_students_score 表中查找最低的成绩，输入的 SQL 语句和执行结果如下所示。

```
mysql> SELECT MIN(student_score)
    -> AS min_score
    -> FROM tb_students_score;
+-----------+
| min_score |
+-----------+
|        88 |
+-----------+
1 row in set (0.00 sec)
```

由结果可以看到，MIN() 函数查询出 student_score 字段的最小值为 88。

> 提示：MIN() 函数与 MAX() 函数类似，不仅适用于查找数值类型，也可应用于字符类型。

