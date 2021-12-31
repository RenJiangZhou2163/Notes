# MySQL TRUNCATE：清空表记录



MySQL 提供了 DELETE 和 TRUNCATE 关键字来删除表中的数据。本节主要讲解 TRUNCATE 关键字的使用。

**TRUNCATE** 关键字用于完全清空一个表。其语法格式如下：

```mysql
TRUNCATE [TABLE] 表名
```

其中，TABLE 关键字可省略。



#### 例 1

新建表 tb_student_course，插入数据并查询，SQL 语句和运行结果如下：

```mysql
mysql> CREATE TABLE `tb_student_course` (
    -> `id` int(4) NOT NULL AUTO_INCREMENT,
    -> `name` varchar(25) NOT NULL,
    -> PRIMARY KEY (`id`)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> INSERT INTO tb_student_course(name) VALUES ('Java'),('MySQL'),('Python');
Query OK, 3 rows affected (0.05 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM tb_student_course;
+----+--------+
| id | name   |
+----+--------+
|  1 | Java   |
|  2 | MySQL  |
|  3 | Python |
+----+--------+
3 rows in set (0.00 sec)
```

使用 TRUNCATE 语句清空 tb_student_course 表中的记录，SQL 语句和运行结果如下：

```mysql
mysql> TRUNCATE TABLE tb_student_course;
Query OK, 0 rows affected (0.04 sec)

mysql> SELECT * FROM tb_student_course;
Empty set (0.00 sec)
```



### TRUNCATE 和 DELETE 的区别

从逻辑上说，TRUNCATE 语句与 DELETE 语句作用相同，但是在某些情况下，两者在使用上有所区别。

- DELETE 是 DML 类型的语句；TRUNCATE 是 DDL 类型的语句。它们都用来清空表中的数据。
- DELETE 是逐行一条一条删除记录的；TRUNCATE 则是直接删除原来的表，再重新创建一个一模一样的新表，而不是逐行删除表中的数据，执行数据比 DELETE 快。因此需要删除表中全部的数据行时，尽量使用 TRUNCATE 语句， 可以缩短执行时间。
- DELETE 删除数据后，配合事件回滚可以找回数据；TRUNCATE 不支持事务的回滚，数据删除后无法找回。
- DELETE 删除数据后，系统不会重新设置自增字段的计数器；TRUNCATE 清空表记录后，系统会重新设置自增字段的计数器。
- DELETE 的使用范围更广，因为它可以通过 WHERE 子句指定条件来删除部分数据；而 TRUNCATE 不支持 WHERE 子句，只能删除整体。
- DELETE 会返回删除数据的行数，但是 TRUNCATE 只会返回 0，没有任何意义。



### 总结

- 当不需要该表时，用 DROP；
- 当仍要保留该表，但要删除所有记录时，用 TRUNCATE；
- 当要删除部分记录时，用 DELETE。