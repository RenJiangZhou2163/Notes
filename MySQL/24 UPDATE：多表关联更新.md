# MySQL UPDATE多表关联更新



MySQL 可以基于多表查询更新数据。对于多表的 UPDATE 操作需要慎重，建议在更新前，先使用 SELECT 语句查询验证更新的数据与自己期望的是否一致。

下面我们建两张表，一张表为 product 表，用来存放产品信息，其中有产品价格字段 price；另外一张表是 product_price 表。现要将 product_price 表中的价格字段 price 更新为 product 表中价格字段 price 的 80%。

操作前先分别查看两张表的数据，SQL 语句和运行结果如下：

```mysql
mysql> SELECT * FROM product;
+----+-----------+-----------------------+-------+----------+
| id | productid | productname           | price | isdelete |
+----+-----------+-----------------------+-------+----------+
|  1 |      1001 | C语言中文网Java教程   |   100 |        0 |
|  2 |      1002 | C语言中文网MySQL教程  |   110 |        0 |
|  3 |      1003 | C语言中文网Python教程 |   120 |        0 |
|  4 |      1004 | C语言中文网C语言教程  |   150 |        0 |
|  5 |      1005 | C语言中文网GoLang教程 |   160 |        0 |
+----+-----------+-----------------------+-------+----------+
5 rows in set (0.02 sec)

mysql> SELECT * FROM product_price;
+----+-----------+-------+
| id | productid | price |
+----+-----------+-------+
|  1 |      1001 |  NULL |
|  2 |      1002 |  NULL |
|  3 |      1003 |  NULL |
|  4 |      1004 |  NULL |
|  5 |      1005 |  NULL |
+----+-----------+-------+
5 rows in set (0.01 sec)
```

下面是 MySQL 多表更新在实践中的几种不同写法。执行不同的 SQL 语句，仔细观察 SQL 语句执行后表中数据的变化，很容易就能理解多表联合更新的用法。

#### 1. 使用UPDATE

在 MySQL 中，可以使用“UPDATE table1 t1,table2,...,table n”的方式来多表更新，SQL 语句和运行结果如下：

```mysql
mysql> UPDATE product p, product_price pp SET pp.price = p.price * 0.8 WHERE p.productid= pp.productId;
Query OK, 5 rows affected (0.02 sec)
Rows matched: 5  Changed: 5  Warnings: 0

mysql> SELECT * FROM product_price;
+----+-----------+-------+
| id | productid | price |
+----+-----------+-------+
|  1 |      1001 |    80 |
|  2 |      1002 |    88 |
|  3 |      1003 |    96 |
|  4 |      1004 |   120 |
|  5 |      1005 |   128 |
+----+-----------+-------+
5 rows in set (0.00 sec)
```

#### 2. 通过INNER JOIN

另外一种方法是使用 INNER JOIN 来多表更新。SQL 语句如下：

```mysql
mysql> UPDATE product p INNER JOIN product_price pp ON p.productid= pp.productid SET pp.price = p.price * 0.8;
Query OK, 5 rows affected (0.09 sec)
Rows matched: 5  Changed: 5  Warnings: 0

mysql> SELECT * FROM product_price;
+----+-----------+-------+
| id | productid | price |
+----+-----------+-------+
|  1 |      1001 |    80 |
|  2 |      1002 |    88 |
|  3 |      1003 |    96 |
|  4 |      1004 |   120 |
|  5 |      1005 |   128 |
+----+-----------+-------+
5 rows in set (0.00 sec)
```

#### 3. 通过LEFT JOIN

也可以使用 LEFT JOIN 来做多表更新，如果 product_price 表中没有产品价格记录的话，将 product 表的 isdelete 字段设置为 1。在 product 表添加 1006 商品，且不在 product_price 表中添加对应信息，SQL 语句如下。

```mysql
mysql> UPDATE product p LEFT JOIN product_price pp ON p.productid= pp.productid SET p.isdelete = 1 WHERE pp.productid IS NULL;
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM product;
+----+-----------+-----------------------+-------+----------+
| id | productid | productname           | price | isdelete |
+----+-----------+-----------------------+-------+----------+
|  1 |      1001 | C语言中文网Java教程   |   100 |        0 |
|  2 |      1002 | C语言中文网MySQL教程  |   110 |        0 |
|  3 |      1003 | C语言中文网Python教程 |   120 |        0 |
|  4 |      1004 | C语言中文网C语言教程  |   150 |        0 |
|  5 |      1005 | C语言中文网GoLang教程 |   160 |        0 |
|  6 |      1006 | C语言中文网Spring教程 |  NULL |        1 |
+----+-----------+-----------------------+-------+----------+
6 rows in set (0.00 sec)
```

#### 4. 通过子查询

也可以通过子查询进行多表更新，SQL 语句和执行过程如下：

```
mysql> UPDATE product_price pp SET price=(SELECT price*0.8 FROM product WHERE productid = pp.productid);
Query OK, 5 rows affected (0.00 sec)
Rows matched: 5  Changed: 5  Warnings: 0

mysql> SELECT * FROM product_price;
+----+-----------+-------+
| id | productid | price |
+----+-----------+-------+
|  1 |      1001 |    80 |
|  2 |      1002 |    88 |
|  3 |      1003 |    96 |
|  4 |      1004 |   120 |
|  5 |      1005 |   128 |
+----+-----------+-------+
5 rows in set (0.00 sec)
```

另外，上面的几个例子都是在两张表之间做关联，只更新一张表中的记录。MySQL 也可以同时更新两张表，如下语句就同时修改了两个表。

UPDATE product p INNER JOIN product_price pp ON p.productid= pp.productid SET pp.price = p.price * 0.8, p.dateUpdate = CURDATE()

两张表做关联，同时更新了 product_price 表的 price 字段和 product 表的 dateUpdate 两个字段。

> 日常开发中，一般都是用单表 UPDATE 语句，很少写多表关联的 UPDATE。

