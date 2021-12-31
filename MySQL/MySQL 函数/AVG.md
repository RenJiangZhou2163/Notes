# MySQL AVG函数：求平均值



MySQL AVG() 函数通过计算返回的行数和每一行数据的和，求得指定列数据的平均值。



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



【实例】在 tb_students_score 表中，查询所有学生成绩的平均值，输入的 SQL 语句和执行结果如下所示。

```
mysql> SELECT AVG(student_score)
    -> AS score_avg
    -> FROM tb_students_score;
+-----------+
| score_avg |
+-----------+
|   94.2000 |
+-----------+
1 row in set (0.03 sec)
```

> 提示：使用 AVG() 函数时，参数为要计算的列名称，若要得到多个列的平均值，则需要在每一列都使用 AVG() 函数。