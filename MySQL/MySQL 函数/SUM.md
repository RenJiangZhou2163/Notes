# MySQL SUM函数：求和



MySQL SUM() 是一个求总和的函数，返回指定列值的总和。



SUM() 函数是如何工作的？

- 如果在没有返回匹配行 SELECT 语句中使用 SUM 函数，则 SUM 函数返回 NULL，而不是 0。
- DISTINCT 运算符允许计算集合中的不同值。
- SUM 函数忽略计算中的 NULL 值。



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

【实例】在 tb_students_score 表中计算学生成绩的总分，输入的 SQL 语句和执行结果如下所示。

```
mysql> SELECT SUM(student_score)
    -> AS score_sum
    -> FROM tb_students_score;
+-----------+
| score_sum |
+-----------+
|       942 |
+-----------+
1 row in set (0.00 sec)
```

由查询结果可以看到，SUM() 函数返回学生的所有成绩之和为 942。

> 提示：SUM() 函数在计算时，忽略列值为 NULL 的行。

