## 1 安装MySQL

Mac安装MySQL的地址：

[Download MySQL Community Server](<https://dev.mysql.com/downloads/mysql/>)

MySQL安装完成后会自动在后台运行，可以使用`mysql -u root -p`来验证MySQL安装是否成功。

> ```
> ~ mysql -u root -p
> Enter password: 
> Welcome to the MySQL monitor.  Commands end with ; or \g.
> Your MySQL connection id is 9
> Server version: 8.0.16 MySQL Community Server - GPL
> 
> Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
> 
> ...
> ```

也可以使用`mysql —version`查看mysql的版本：

```
~ mysql --version
mysql  Ver 8.0.16 for macos10.14 on x86_64 (MySQL Community Server - GPL)
```

## 2 连接和断开连接

使用`mysql -u root -p`来连接服务：

```
~ mysql -u root -p             
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.16 MySQL Community Server - GPL
...
```

> 注意：输入`mysql -u root -p`命令后需要输入密码，这里的密码是安装MySQL后设置的root权限的密码。

连接成功之后可以输入`QUIT`或者`\q`来断开连接：

```
mysql> QUIT
Bye
```

或者：

```
mysql> \q
Bye
```

## 3 输入查询 Entering Queries

在mysql>提示符后输入下面的指令，来查询服务器的版本号和当前日期：

```
mysql> SELECT VERSION(), CURRENT_DATE;
+-----------+--------------+
| VERSION() | CURRENT_DATE |
+-----------+--------------+
| 8.0.16    | 2019-06-24   |
+-----------+--------------+
1 row in set (0.01 sec)
```

查询通常包含一个SQL语句，后面跟一个分号。

查询关键字的输入可以是任何字母，比如，下面的查询是等价的，会输出相同的结果：

```
mysql> SELECT VERSION(), CURRENT_DATE;
mysql> select version(), current_date;
mysql> seLeCt vErSiOn(), current_DATE;
```

也可以将mysql作为简单的计算器来使用：

```
mysql> SELECT SIN(PI()/4), (4+1)*5;
+--------------------+---------+
| SIN(PI()/4)        | (4+1)*5 |
+--------------------+---------+
| 0.7071067811865475 |      25 |
+--------------------+---------+
1 row in set (0.00 sec)
```

也可以在一行中输入多个语句，每个语句以分号结束：

```
mysql> SELECT VERSION(); SELECT NOW();
+-----------+
| VERSION() |
+-----------+
| 8.0.16    |
+-----------+
1 row in set (0.00 sec)

+---------------------+
| NOW()               |
+---------------------+
| 2019-06-24 16:25:39 |
+---------------------+
1 row in set (0.00 sec)
```

mysql接受自由格式的输入，它是通过查找终止的分号来确定语句的结束位置，因此，我们也可以输入多行查询语句：

```
mysql> SELECT
    -> USER()
    -> ,
    -> CURRENT_DATE;
+----------------+--------------+
| USER()         | CURRENT_DATE |
+----------------+--------------+
| root@localhost | 2019-06-24   |
+----------------+--------------+
1 row in set (0.00 sec)
```

> 需要注意的是：使用多行查询语句时，别忘了结束时的分号。

如果决定不想执行正在输入的查询，可以输入`\c`来取消：

```
mysql> SELECT
    -> USER()
    -> \c
mysql>
```

> 注意：`mysql>`提示表明mysql已准备好进行新的查询。

下面表格总结了可能看到的提示，以及它们对mysql所处状态的含义：

| 提示     | 含义                                            |
| -------- | ----------------------------------------------- |
| `mysql>` | 准备好进行新的查询                              |
| `->`     | 多行查询时，等待下一行查询内容                  |
| `'>`     | 等待下一行，等待以单引号`'`开头的字符串的完成   |
| `">`     | 等待下一行，等待以双引号`"`开头的字符串的完成   |
| ``>`     | 等待下一行，等待以反引号（`）开头的标识符的完成 |
| `/*>`    | 等待下一行，等待以`/*`开头的注释的完成          |

例如，输入了下面的查询语句：

```
mysql> SELECT * FROM my_table WHERE name = 'Smith AND age < 30;
    '> 
```

这是因为语句中以`'`开头的字符并没有以`'`结束。

此时可以取消查询，但是不能简单的键入`\c`，mysql会把它作为收集的字符的一部分：

```
mysql> SELECT * FROM my_table WHERE name = 'Smith AND age < 30;
    '> \c
    '> 
```

正确的取消是，先输入结束的单引号，告诉mysql字符串已经结束了，然后再使用`\c`：

```
mysql> SELECT * FROM my_table WHERE name = 'Smith AND age < 30;
    '> '\c
mysql>
```

## 4 创建和使用数据库 Creating and Using a Database

使用`SHOW`语句来查找当前服务器上存在的数据库：

```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
```

> mysql数据库描述了用户访问的权限。
>
> 如果没有`SHOW DATABASES`的权限，则`SHOW DATABASES`不会显示你没有权限的数据库。

访问数据库是使用`USE`，例如：

```
mysql> USE test
Database changed
```

需要注意的是，`USE`跟`QUIT`类似，结尾不需要添加分号。另外，`USE`的声明还有一点特殊之处就是，它必须在一行中给出。

#### 4.1 创建和选择数据库

使用`CREATE DATABASE xxx;`创建数据库。

例如，创建一个名为`menagerie`的数据库：

```
mysql> CREATE DATABASE menagerie;
Query OK, 1 row affected (0.01 sec)
```

在Unix系统下，数据库的名称是大小写敏感的，表名称也是。然而在Windows下，并没有这样限制。不过建议的最佳做法是始终使用创建数据库时使用的相同字母。

如果需要将`menagerie`数据库作为当前的数据库，使用下面的语句：

```
mysql> USE menagerie
Database changed
```

数据库只需要被创建一次，但每次开始mysql会话时都必须选择它才能使用。可以通过`USE`语句来执行此操作，也可以在调用mysql时在命令行上选择数据库。

可以随时使用`SELECT DATABASE();`查看当前选择的数据库：

```
mysql> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| menagerie  |
+------------+
1 row in set (0.00 sec)
```

#### 4.2 创建表

前面创建的数据库是空的，可以使用`SHOW TABLES;`来查看：

```
mysql> SHOW TABLES;
Empty set (0.00 sec)
```

使用`CREATE TABLE`语句来指定表的结构布局：

```
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20), species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
Query OK, 0 rows affected (0.00 sec)
```

> 上面的`VARCHAR`适用于name、owner、species，因为这些列的值的长度不同，通常可以选择1到65535之间的任何长度。如果选择不好，后面需要更长的字段，MySQL提供了一个`ALTER TABLE`语句。

创建表之后，使用`SHOW TABLES;`可以查看到一些输出：

```
mysql> SHOW TABLES;
+---------------------+
| Tables_in_menagerie |
+---------------------+
| pet                 |
+---------------------+
1 row in set (0.01 sec)
```

如果要验证表是否按预期的方式创建，可以使用`DESCRIBE`语句：

```
mysql> DESCRIBE pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
6 rows in set (0.01 sec)
```

可以随时使用`DESCRIBE`查看，比如忘记了表中列的名称和类型。

#### 4.3 将数据加载到表中

###### 使用`LOAD DATA`加载数据

- 创建一个`pet.txt`文件，每一行包含一条记录，使用tab键分隔值，并按`CREATE TABLE`语句中的顺序添加内容。

- 对于缺失的值或者空值，可以使用`NULL`值。在text field中，使用`\N`来表示。

- 使用下面的语句将pet.txt文件内容加载到pet表中：

  ```
  LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet;
  ```

  这里将pet.txt文件保存在桌面上，因此执行的语句如下：

  ```
  mysql> LOAD DATA LOCAL INFILE '/Users/darkgm/Desktop/pet.txt' INTO TABLE pet;
  ERROR 1148 (42000): The used command is not allowed with this MySQL version
  ```

  上面的语句报错，原因是local_infile未开启，详情可参考[Security Issues with LOAD DATA LOCAL](<https://dev.mysql.com/doc/refman/5.7/en/load-data-local.html>)。

  解决办法：

  - 使用`show global variables like 'local%';`查看local_infile 状态；
  - 使用`set global local_infile = "on";`开启local_inifile。
  - 重新加载数据。

  ```
  mysql> show global variables like 'local%';
  +---------------+-------+
  | Variable_name | Value |
  +---------------+-------+
  | local_infile  | OFF   |
  +---------------+-------+
  1 row in set (0.01 sec)
  
  mysql> set global local_infile = "on";
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> show global variables like 'local%'
      -> ;
  +---------------+-------+
  | Variable_name | Value |
  +---------------+-------+
  | local_infile  | ON    |
  +---------------+-------+
  1 row in set (0.00 sec)
  
  mysql> LOAD DATA LOCAL INFILE '/Users/darkgm/Desktop/pet.txt' INTO TABLE pet;
  Query OK, 4 rows affected (0.01 sec)
  Records: 4  Deleted: 0  Skipped: 0  Warnings: 0
  ```

  > 参考自[[MySQL中LOAD DATA INFILE出现“ERROR 1148 (42000): The used command is not allowed with this MySQL version”](https://www.cnblogs.com/dandychen/p/9520678.html)]

  查看表中的内容：

  ```
  mysql> select * from pet;
  +--------+--------+---------+------+------------+-------+
  | name   | owner  | species | sex  | birth      | death |
  +--------+--------+---------+------+------------+-------+
  | Fluffy | Harold | cat     | f    | 1993-02-04 | NULL  |
  | Claws  | Gwen   | cat     | m    | 1994-03-17 | NULL  |
  | Buffy  | Harold | dog     | f    | 1989-05-13 | NULL  |
  | Fang   | Benny  | dog     | m    | 1990-08-27 | NULL  |
  +--------+--------+---------+------+------------+-------+
  4 rows in set (0.00 sec)
  ```

###### 使用`INSERT`加载数据

如果需要一次添加一条记录，可以使用`INSERT`语句。只需要按照`CREATE TABLE`语句中列出的列的顺序为每列提供值即可：

```
mysql> INSERT INTO pet VALUES ('Puffball', 'Diane', 'hamster', 'f', '1999-03-30', NULL);
Query OK, 1 row affected (0.01 sec)
```

再次查看表中的内容：

```
select * from pet;
+----------+--------+---------+------+------------+-------+
| name     | owner  | species | sex  | birth      | death |
+----------+--------+---------+------+------------+-------+
| Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL  |
| Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy    | Harold | dog     | f    | 1989-05-13 | NULL  |
| Fang     | Benny  | dog     | m    | 1990-08-27 | NULL  |
| Puffball | Diane  | hamster | f    | 1999-03-30 | NULL  |
+----------+--------+---------+------+------------+-------+
5 rows in set (0.00 sec)
```

> 需要注意的是：字符串和日期值需要指定为带引号的字符串。另外，使用`IINSERT`时，可以直接插入`NULL`表示缺少的值。

还可以继续添加数据：

```
mysql> INSERT INTO pet VALUES ('Bowser', 'Diane', 'dog', 'm', '1979-08-31', '1995-07-29');
Query OK, 1 row affected (0.01 sec)
```

#### 4.4 从表中检索信息

`SELECT`语句用于从表中查找数据，常见的语句形式为：

```
SELECT what_to_select
FROM which_table
WhERE conditions_to_satisfy;
```

*what_to_select*：表示想查找的内容，可以是某几列，也可以使用`*`来表明所有的列。

*which_table*：表示要从中检索数据的表。

*conditions_to_satisfy*：这一项是可选的，如果出现了，则表示指定行必须满足的一个或多个条件才有资格进行检索。

##### 4.4.1 Selecting All Data

检索表中的所有数据，可以使用`SELECT * FROM tablename;`，例如：

```
mysql> SELECT * FROM pet;
+----------+--------+---------+------+------------+------------+
| name     | owner  | species | sex  | birth      | death      |
+----------+--------+---------+------+------------+------------+
| Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
| Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
| Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
| Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
| Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
| Bowser   | Diane  | dog     | m    | 1979-08-31 | 1995-07-29 |
+----------+--------+---------+------+------------+------------+
6 rows in set (0.00 sec)
```

对于表中刚刚加载的数据，使用上面的形式来查看整个表格内容很有用。例如，你可能会发现Bowser的出生日期似乎不太合适或者输入有误，需要更改为1989年，而不是1979年。可以使用下面两种方式来修复：

- 编辑`pet.txt`文件来修改，然后使用`DELETE`清空表，再使用`LOAD DATA`重新加载。

  - 修改`pet.txt`文件的内容。

  - 使用`DELETE`清空表：

    ```
    mysql> DELETE FROM pet;
    Query OK, 6 rows affected (0.00 sec)
    
    mysql> select * from pet;
    Empty set (0.00 sec)
    ```

  - 使用`LOAD DATA`重新加载表中的数据：

    ```
    mysql> LOAD DATA LOCAL INFILE '/Users/darkgm/Desktop/pet.txt' INTO TABLE pet;
    ERROR 1148 (42000): The used command is not allowed with this MySQL version
    ```

    这次又出现了同样的问题。

    使用`show global variables like 'local%';`查看local_infile为`ON`，但是依然报错。

    ```
    mysql> show global variables like 'local%';
    +---------------+-------+
    | Variable_name | Value |
    +---------------+-------+
    | local_infile  | ON    |
    +---------------+-------+
    1 row in set (0.00 sec)
    ```

    尝试新的解决方法：

    - 退出mysql：

      ```
      mysql> \q
      Bye
      ```

    - 使用下面的命令在连接服务时开启local_infile（需要输入密码）：

      ```
      ~ mysql -u root -p menagerie --local-infile=1
      Enter password: 
      Reading table information for completion of table and column names
      You can turn off this feature to get a quicker startup with -A
      
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 20
      Server version: 8.0.16 MySQL Community Server - GPL
      ...
      ```

      > menagerie为数据库的名称。
      >
      > 参考自：[LOAD DATA LOCAL INFILE not working in mysql](<https://bugs.mysql.com/bug.php?id=72220>)

    - 重新加载数据：

      ```
      mysql> LOAD DATA LOCAL INFILE '/Users/darkgm/Desktop/pet.txt' INTO TABLE pet;
      Query OK, 6 rows affected (0.00 sec)
      Records: 6  Deleted: 0  Skipped: 0  Warnings: 0
      ```

    - 查看表中的内容：

      ```
      mysql> SELECT * from pet;
      +----------+--------+---------+------+------------+------------+
      | name     | owner  | species | sex  | birth      | death      |
      +----------+--------+---------+------+------------+------------+
      | Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
      | Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
      | Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
      | Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
      | Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
      | Bowser   | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
      +----------+--------+---------+------+------------+------------+
      6 rows in set (0.00 sec)
      ```

    > 总结：对于*The used command is not allowed with this MySQL version* 问题推荐使用在连接服务时开启local_infile的方法来解决。`show global variables like 'local%';`不太了解，不确定为什么有时不可行。

- 使用`UPDATE`语句只修复错误的内容。

  ```
  UPDATE pet SET birth = '1999-08-31' WHERE name = 'Bowser';
  Query OK, 1 row affected (0.00 sec)
  Rows matched: 1  Changed: 1  Warnings: 0
  
  mysql> SELECT * FROM pet;
  +----------+--------+---------+------+------------+------------+
  | name     | owner  | species | sex  | birth      | death      |
  +----------+--------+---------+------+------------+------------+
  | Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
  | Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
  | Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
  | Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
  | Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
  | Bowser   | Diane  | dog     | m    | 1999-08-31 | 1995-07-29 |
  +----------+--------+---------+------+------------+------------+
  6 rows in set (0.00 sec)
  ```

  再次更新：

  ```
  mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Bowser';
  Query OK, 1 row affected (0.00 sec)
  Rows matched: 1  Changed: 1  Warnings: 0
  
  mysql> SELECT * FROM pet;
  +----------+--------+---------+------+------------+------------+
  | name     | owner  | species | sex  | birth      | death      |
  +----------+--------+---------+------+------------+------------+
  | Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
  | Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
  | Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
  | Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
  | Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
  | Bowser   | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
  +----------+--------+---------+------+------------+------------+
  6 rows in set (0.00 sec)
  ```

##### 4.4.2 Selecting Particular Rows

如果表中的内容很多，查看指定的内容很有必要。

例如上面的操作中，我们只更改了Bowser的内容，可以只查看这一行内容：

```
mysql> SELECT * FROM pet WHERE name = 'Bowser';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
1 row in set (0.00 sec)
```

> 字符串比较通常不区分大小写，因此可以将名称指定为“bowser”，“BOWSER”等。查询结果是一样的。

可以在任意列上指定条件，例如，如果想知道哪些动物是在1998年之后出生的，可以测试birth列：

```
mysql> SELECT * FROM pet WHERE birth >= '1998-1-1';
+----------+-------+---------+------+------------+-------+
| name     | owner | species | sex  | birth      | death |
+----------+-------+---------+------+------------+-------+
| Puffball | Diane | hamster | f    | 1999-03-30 | NULL  |
+----------+-------+---------+------+------------+-------+
1 row in set (0.01 sec)
```

也可以组合条件，例如查询母狗：

```
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f';
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
1 row in set (0.00 sec)
```

上面的查询使用了`AND`运算符，还可以使用`OR`运算符：

```
mysql> SELECT * FROM pet WHERE species = 'hamster' OR species = 'cat';
+----------+--------+---------+------+------------+-------+
| name     | owner  | species | sex  | birth      | death |
+----------+--------+---------+------+------------+-------+
| Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL  |
| Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Puffball | Diane  | hamster | f    | 1999-03-30 | NULL  |
+----------+--------+---------+------+------------+-------+
3 rows in set (0.00 sec)
```

AND和OR可以混合使用，但AND的优先级高于OR。如果同时使用了这两个运算符，最好使用括号表明语义：

```
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm') OR (species = 'dog' AND sex = 'f');
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
2 rows in set (0.01 sec)
```

> AND和OR也不区分大小写，下面的语句，输出的结果与上面一致：
>
> ```
> mysql> select * from pet where (species = "cat" and sex = "m") or (species = "dog" and sex = "f");
> ```

##### 4.4.3 Selecting Particular Columns

可以使用下面的语句查询指定的列：

```
mysql> SELECT name, birth FROM pet;
+----------+------------+
| name     | birth      |
+----------+------------+
| Fluffy   | 1993-02-04 |
| Claws    | 1994-03-17 |
| Buffy    | 1989-05-13 |
| Fang     | 1990-08-27 |
| Puffball | 1999-03-30 |
| Bowser   | 1989-08-31 |
+----------+------------+
6 rows in set (0.00 sec)
```

如果要查找宠物的主人，可以使用下面的语句：

```
mysql> SELECT owner FROM pet;
+--------+
| owner  |
+--------+
| Harold |
| Gwen   |
| Harold |
| Benny  |
| Diane  |
| Diane  |
+--------+
6 rows in set (0.01 sec)
```

上面的查询中只是简单的检索`owner`列，有些值重复出现了，为了最小化输出，可以通过添加`DISTINCT`关键字只检索每个唯一的输出记录一次：

```
mysql> SELECT DISTINCT owner FROM pet;
+--------+
| owner  |
+--------+
| Harold |
| Gwen   |
| Benny  |
| Diane  |
+--------+
4 rows in set (0.01 sec)
```

也可以使用WHERE语句组合行和列的查询。例如，只获取狗和猫的出生日期可以使用下面的语句：

```
mysql> SELECT name, species, birth FROM pet WHERE species = 'dog' OR species = 'cat';
+--------+---------+------------+
| name   | species | birth      |
+--------+---------+------------+
| Fluffy | cat     | 1993-02-04 |
| Claws  | cat     | 1994-03-17 |
| Buffy  | dog     | 1989-05-13 |
| Fang   | dog     | 1990-08-27 |
| Bowser | dog     | 1989-08-31 |
+--------+---------+------------+
5 rows in set (0.00 sec)
```

##### 4.4.4 Sorting Rows

可以使用`ORDER BY`对查询结果进行排序：

```
mysql> SELECT name, birth FROM pet ORDER BY birth;
+----------+------------+
| name     | birth      |
+----------+------------+
| Buffy    | 1989-05-13 |
| Bowser   | 1989-08-31 |
| Fang     | 1990-08-27 |
| Fluffy   | 1993-02-04 |
| Claws    | 1994-03-17 |
| Puffball | 1999-03-30 |
+----------+------------+
6 rows in set (0.00 sec)
```

在字符类型的列上，排序与所有其他比较操作一样，通常以不区分大小写的方式执行。 可以使用`BINARY`强制对列进行区分大小写的排序。

默认的排序顺序是升序，先是最小值。如果要按反向排序（降序），需要将`DESC`关键字添加到要排序的列的名称后面。

```
mysql> SELECT name, birth FROM pet ORDER BY birth DESC;
+----------+------------+
| name     | birth      |
+----------+------------+
| Puffball | 1999-03-30 |
| Claws    | 1994-03-17 |
| Fluffy   | 1993-02-04 |
| Fang     | 1990-08-27 |
| Bowser   | 1989-08-31 |
| Buffy    | 1989-05-13 |
+----------+------------+
6 rows in set (0.00 sec)
```

也可以对多个列进行排序，并且不同的列可以按照不同的方向来排序。例如，动物的类型按照升序来排列，动物类型中的出生日期按照降序来排序，如下：

```
mysql> SELECT name, species, birth FROM pet ORDER BY species, birth DESC;
+----------+---------+------------+
| name     | species | birth      |
+----------+---------+------------+
| Claws    | cat     | 1994-03-17 |
| Fluffy   | cat     | 1993-02-04 |
| Fang     | dog     | 1990-08-27 |
| Bowser   | dog     | 1989-08-31 |
| Buffy    | dog     | 1989-05-13 |
| Puffball | hamster | 1999-03-30 |
+----------+---------+------------+
6 rows in set (0.00 sec)
```

> `DESC`关键字仅适用于它前面的列名（birth），不会影响到species列的排序顺序。

##### 4.4.5 Date Calculations

使用`TIMESTAMPDIFF()`函数可以计算两个日期差，它的参数包括：

- 你希望返回的结果单元。
- 需要比较的两个日期。

例如，计算宠物的年龄可以编写下面的语句：

```
mysql> SELECT name, birth, CURDATE(), TIMESTAMPDIFF(YEAR, birth, CURDATE()) AS age FROM pet;
+----------+------------+------------+------+
| name     | birth      | CURDATE()  | age  |
+----------+------------+------------+------+
| Fluffy   | 1993-02-04 | 2019-06-25 |   26 |
| Claws    | 1994-03-17 | 2019-06-25 |   25 |
| Buffy    | 1989-05-13 | 2019-06-25 |   30 |
| Fang     | 1990-08-27 | 2019-06-25 |   28 |
| Puffball | 1999-03-30 | 2019-06-25 |   20 |
| Bowser   | 1989-08-31 | 2019-06-25 |   29 |
+----------+------------+------------+------+
6 rows in set (0.00 sec)
```

按照名称排序：

```
mysql> SELECT name, birth, CURDATE(), TIMESTAMPDIFF(YEAR, birth, CURDATE()) AS age FROM pet ORDER BY name;
+----------+------------+------------+------+
| name     | birth      | CURDATE()  | age  |
+----------+------------+------------+------+
| Bowser   | 1989-08-31 | 2019-06-25 |   29 |
| Buffy    | 1989-05-13 | 2019-06-25 |   30 |
| Claws    | 1994-03-17 | 2019-06-25 |   25 |
| Fang     | 1990-08-27 | 2019-06-25 |   28 |
| Fluffy   | 1993-02-04 | 2019-06-25 |   26 |
| Puffball | 1999-03-30 | 2019-06-25 |   20 |
+----------+------------+------------+------+
6 rows in set (0.00 sec)
```

按照年龄排序：

```
mysql> SELECT name, birth, CURDATE(), TIMESTAMPDIFF(YEAR, birth, CURDATE()) AS age FROM pet ORDER BY age;
+----------+------------+------------+------+
| name     | birth      | CURDATE()  | age  |
+----------+------------+------------+------+
| Puffball | 1999-03-30 | 2019-06-25 |   20 |
| Claws    | 1994-03-17 | 2019-06-25 |   25 |
| Fluffy   | 1993-02-04 | 2019-06-25 |   26 |
| Fang     | 1990-08-27 | 2019-06-25 |   28 |
| Bowser   | 1989-08-31 | 2019-06-25 |   29 |
| Buffy    | 1989-05-13 | 2019-06-25 |   30 |
+----------+------------+------------+------+
6 rows in set (0.00 sec)
```

下面用于查询死亡动物的死亡年龄。可以通过检查death值是否为NULL来确定，对于具有非NULL值的动物，计算死亡和出生的差值：

```
mysql> SELECT name, birth, death, TIMESTAMPDIFF(YEAR, birth, death) AS age FROM pet WHERE death IS NOT NULL ORDER BY age;
+--------+------------+------------+------+
| name   | birth      | death      | age  |
+--------+------------+------------+------+
| Bowser | 1989-08-31 | 1995-07-29 |    5 |
+--------+------------+------------+------+
1 row in set (0.00 sec)
```

MySQL还提供了一些函数用于提取日期的一部分，例如`YEAR()`、`MONTH()`、`DAYOFMONTH`。

例如，想知道下个月哪些动物过生日，可以使用`MONTH()`：

```
mysql> SELECT name, birth, MONTH(birth) FROM pet;
+----------+------------+--------------+
| name     | birth      | MONTH(birth) |
+----------+------------+--------------+
| Fluffy   | 1993-02-04 |            2 |
| Claws    | 1994-03-17 |            3 |
| Buffy    | 1989-05-13 |            5 |
| Fang     | 1990-08-27 |            8 |
| Puffball | 1999-03-30 |            3 |
| Bowser   | 1989-08-31 |            8 |
+----------+------------+--------------+
6 rows in set (0.01 sec)
```

比如现在是4月份，month value就是4，也可以按照下面的方式查找在五月份出生的动物：

```
mysql> SELECT name, birth FROM pet WHERE MONTH(birth) = 5;
+-------+------------+
| name  | birth      |
+-------+------------+
| Buffy | 1989-05-13 |
+-------+------------+
1 row in set (0.00 sec)
```

> 需要注意的是，如果当前月份是12，你不能查找在13月出生的动物，该月份并不存在，你应该查找在一月出生的动物。

`DATE_ADD()`允许将时间间隔添加到给定日期：

```
mysql> SELECT name, birth FROM pet Where MONTH(birth) = MONTH(DATE_ADD(CURDATE(), INTERVAL 1 MONTH));
Empty set (0.01 sec)
```

也可以在当前月份之后加1来获取下个月份，使用`MOD`函数，如果当前月份是12，则可以将它包装为0。

```
mysql> SELECT name, birth FROM pet WHERE MONTH(birth) = MOD(MONTH(CURDATE()), 12) + 1;
Empty set (0.00 sec)
```

> `MONTH()`返回1到12的数字，`MOD(something, 12)`返回0到11的数字。

如果计算使用了无效的日期，则会计算失败：

```
mysql> SELECT '2018-10-31' + INTERVAL 1 DAY;
+-------------------------------+
| '2018-10-31' + INTERVAL 1 DAY |
+-------------------------------+
| 2018-11-01                    |
+-------------------------------+
1 row in set (0.00 sec)

mysql> SELECT '2018-10-32' + INTERVAL 1 DAY;
+-------------------------------+
| '2018-10-32' + INTERVAL 1 DAY |
+-------------------------------+
| NULL                          |
+-------------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SHOW WARNINGS;
+---------+------+----------------------------------------+
| Level   | Code | Message                                |
+---------+------+----------------------------------------+
| Warning | 1292 | Incorrect datetime value: '2018-10-32' |
+---------+------+----------------------------------------+
1 row in set (0.00 sec)
```

##### 4.4.6 Working with NULL Values

> NULL means "a missing unknown value"

测试NULL时，可以使用`IS NULL`和`IS NOT NULL`操作符：

```
mysql> SELECT 1 IS NULL, 1 IS NOT NULL;
+-----------+---------------+
| 1 IS NULL | 1 IS NOT NULL |
+-----------+---------------+
|         0 |             1 |
+-----------+---------------+
1 row in set (0.00 sec)
```

不能对NULL使用算术比较运算符，例如`=`， `<`或`<>`，例如：

```
mysql> SELECT 1 = NULL, 1 <> NULL, 1 < NULL, 1 > NULL;
+----------+-----------+----------+----------+
| 1 = NULL | 1 <> NULL | 1 < NULL | 1 > NULL |
+----------+-----------+----------+----------+
|     NULL |      NULL |     NULL |     NULL |
+----------+-----------+----------+----------+
1 row in set (0.00 sec)
```

由于对NULL进行的任何算术比较都是NULL，因此在上面的比较中不会得到有意义的结果。

在MySQL中，`0`或`NULL`意味着false，除此之外的意味着true。布尔操作的默认truth值是`1`。

在GROUP BY中，两个NULL值被视为相等。

当执行`ORDER BY`时，如果是`ORDER BY … ASC`（升序），则`NULL`值会出现在最前面；如果是`ORDER BY … DESC`（降序），则`NULL`值出现在最后面。

使用NULL时，可以将0或空字符串插入定义为NOT NULL的列中，这些实际上是值，NULL表示没有值，因此可以使用`IS [NOT] NULL`来测试：

```
mysql> SELECT 0 IS NULL, 0 IS NOT NULL, '' IS NULL, '' IS NOT NULL;
+-----------+---------------+------------+----------------+
| 0 IS NULL | 0 IS NOT NULL | '' IS NULL | '' IS NOT NULL |
+-----------+---------------+------------+----------------+
|         0 |             1 |          0 |              1 |
+-----------+---------------+------------+----------------+
1 row in set (0.00 sec)
```

##### 4.4.7 Pattern Matching

MySQL提供标准的SQL模式匹配以及基于扩展正则表达式的模式匹配形式。

SQL模式匹配可以使用`_`来匹配任何单一的字符，使用`%`来匹配任意数量的字符（包括0字符）。在MySQL中，SQL模式默认不区分大小写的。当使用SQL模式时，不要使用`=`或`<>`，使用`LIKE`或`NOT LIKE`比较操作符来替代。

例如，查找以b开头的宠物名称：

```
mysql> SELECT * FROM pet WHERE name LIKE 'b%';
+--------+--------+---------+------+------------+------------+
| name   | owner  | species | sex  | birth      | death      |
+--------+--------+---------+------+------------+------------+
| Buffy  | Harold | dog     | f    | 1989-05-13 | NULL       |
| Bowser | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+--------+---------+------+------------+------------+
2 rows in set (0.00 sec)
```

查找以`fy`结尾的名称：

```
mysql> SELECT * FROM pet WHERE name LIKE '%fy';
+--------+--------+---------+------+------------+-------+
| name   | owner  | species | sex  | birth      | death |
+--------+--------+---------+------+------------+-------+
| Fluffy | Harold | cat     | f    | 1993-02-04 | NULL  |
| Buffy  | Harold | dog     | f    | 1989-05-13 | NULL  |
+--------+--------+---------+------+------------+-------+
2 rows in set (0.00 sec)
```

查找包含`w`的名称：

```
mysql> SELECT * FROM pet WHERE name LIKE '%w%';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Claws  | Gwen  | cat     | m    | 1994-03-17 | NULL       |
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
2 rows in set (0.00 sec)
```

查找包含五个字符的名称，使用五个`_`模式字符：

```
mysql> SELECT * FROM pet WHERE name like '_____';
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
2 rows in set (0.00 sec)
```

MySQL提供的另一种模式匹配使用扩展的正则表达式。 当测试这类模式的匹配项时，需要使用`REGEXP_LIKE()`函数（或`REGEXP`或`RLIKE`运算符，它们是`REGEXP_LIKE()`的同义词）。

下面列出了一些扩展的正则表达式：

- `.`匹配任何的单一字符。
- 字符类`[...]`匹配方括号内的任意字符，例如`[abc]`匹配a, b或c。要定义一个字符的范围，可以使用短横线。例如，`[a-z]`匹配任意的字母，`[0-9]`匹配任意的数字。
- `*`匹配它前面的0个或多个字符。例如，`x*`匹配任意数量的`x`字符，`[0-9]`匹配任意数量的数字，`.*`匹配任意数量的任何数字或字符。
- 如果模式匹配正在测试的值中的任何位置，则正则表达式模式匹配成功。（这与LIKE模式匹配不同，仅当模式匹配整个值时才成功。）
- 要锚定（anchor）模式以使其必须与要测试的值的开头或结尾匹配，需要在模式的开头使用`^`，在结尾使用`$`。

使用正则表达式来查找以b开头的名称：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^b');
+--------+--------+---------+------+------------+------------+
| name   | owner  | species | sex  | birth      | death      |
+--------+--------+---------+------+------------+------------+
| Buffy  | Harold | dog     | f    | 1989-05-13 | NULL       |
| Bowser | Diane  | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+--------+---------+------+------------+------------+
2 rows in set (0.02 sec)
```

要强制在正则表达式中区分大小写，可以使用区分大小写的排序规则，或使用`BINARY`关键字将其中的一个字符串作为二进制字符串，或执行`c`匹配控制字符。

下面这些查询仅匹配以小写字母`b`开头的名称：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^b' COLLATE utf8mb4_0900_as_cs);
Empty set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, BINARY '^b');
Empty set (0.00 sec)

mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^b', 'c');
Empty set (0.00 sec)
```

要查找以`fy`结尾的名称，可以使用`$`来匹配名称的结尾：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, 'fy$');
+--------+--------+---------+------+------------+-------+
| name   | owner  | species | sex  | birth      | death |
+--------+--------+---------+------+------------+-------+
| Fluffy | Harold | cat     | f    | 1993-02-04 | NULL  |
| Buffy  | Harold | dog     | f    | 1989-05-13 | NULL  |
+--------+--------+---------+------+------------+-------+
2 rows in set (0.00 sec)
```

查找包含`w`的名称：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, 'w');
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Claws  | Gwen  | cat     | m    | 1994-03-17 | NULL       |
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
2 rows in set (0.00 sec)
```

查找包含五个字符的名称：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^.....$');
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
2 rows in set (0.00 sec)
```

也可以使用`{n}`查找包含五个字符的名称：

```
mysql> SELECT * FROM pet WHERE REGEXP_LIKE(name, '^.{5}$');
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
2 rows in set (0.01 sec)
```

##### 4.4.8 Counting Rows

`COUNT(*)`用于计算行数，例如，查询表中动物的数量，可以使用下面的语句：

```
mysql> SELECT COUNT(*) FROM pet;
+----------+
| COUNT(*) |
+----------+
|        6 |
+----------+
1 row in set (0.01 sec)
```

也可以使用`COUNT()`来查看每位主人有几个宠物：

```
mysql> SELECT owner, COUNT(*) FROM pet GROUP BY owner;
+--------+----------+
| owner  | COUNT(*) |
+--------+----------+
| Harold |        2 |
| Gwen   |        1 |
| Benny  |        1 |
| Diane  |        2 |
+--------+----------+
4 rows in set (0.01 sec)
```

上面使用`GROUP BY`对每个主人的所有记录进行分组。将`COUNT()`与`GROUP BY`结合使用对于在各种分组下表征数据非常有用。

查询每个物种的动物数量：

```
mysql> SELECT species, COUNT(*) FROM pet GROUP BY species;
+---------+----------+
| species | COUNT(*) |
+---------+----------+
| cat     |        2 |
| dog     |        3 |
| hamster |        1 |
+---------+----------+
3 rows in set (0.00 sec)
```

查询每种性别的动物数量：

```
mysql> SELECT sex, COUNT(*) FROM pet GROUP BY sex;
+------+----------+
| sex  | COUNT(*) |
+------+----------+
| f    |        3 |
| m    |        3 |
+------+----------+
2 rows in set (0.00 sec)
```

查询每个性别和种类组合的动物数量：

```
mysql> SELECT species, sex, COUNT(*) FROM pet GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
| hamster | f    |        1 |
+---------+------+----------+
5 rows in set (0.01 sec)
```

使用`COUNT()`时，不必查询整个表，例如，只查看猫和狗的性别和种类数量：

```
mysql> SELECT species, sex, COUNT(*) FROM pet WHERE species = 'dog' OR species = 'cat' GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
+---------+------+----------+
4 rows in set (0.00 sec)
```

只查看已知性别的每种性别的动物数量：

```
mysql> SELECT species, sex, COUNT(*) FROM pet WHERE sex IS NOT NULL GROUP BY species, sex;
+---------+------+----------+
| species | sex  | COUNT(*) |
+---------+------+----------+
| cat     | f    |        1 |
| cat     | m    |        1 |
| dog     | f    |        1 |
| dog     | m    |        2 |
| hamster | f    |        1 |
+---------+------+----------+
5 rows in set (0.00 sec)
```

如果除了`COUNT()`值之外还要指定要选择的列，则应该存在一个GROUP BY子句，该子句将这些列命名为相同的列。 否则，会发生以下情况：

- 如果`ONLY_FULL_GROUP_BY` SQL模式开启，会发生下面的错误

  ```
  mysql> SET sql_mode = 'ONLY_FULL_GROUP_BY';
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> SELECT owner, COUNT(*) FROM pet;
  ERROR 1140 (42000): In aggregated query without GROUP BY, expression #1 of SELECT list contains nonaggregated column 'menagerie.pet.owner'; this is incompatible with sql_mode=only_full_group_by
  ```

- 如果`ONLY_FULL_GROUP_BY`未开启，则通过将所有行视为单个组来处理查询，但为每个命名列选择的值是不确定的。 服务器可以自由选择任何行中的值：

  ```
  mysql> SELECT owner, COUNT(*) FROM pet;
  +--------+----------+
  | owner  | COUNT(*) |
  +--------+----------+
  | Harold |        6 |
  +--------+----------+
  1 row in set (0.00 sec)
  ```

##### 4.4.9 Using More Than one Table

之前创建了创建了pet表，现在再创建一个表用于记录宠物生活中的事件，该表大致需要以下信息：

- 宠物名称，用来表明事件属于哪个宠物的。
- 日期，用来表明事件发生的时间。
- 描述事件的字段。
- 事件类型的字段，以便于对事件进行分类。

下面就创建一个event表：

```
mysql> CREATE TABLE event (name VARCHAR(20), date DATE, type VARCHAR(15), remark VARCHAR(255));
Query OK, 0 rows affected (0.01 sec)
```

添加数据：

```
LOAD DATA LOCAL INFILE '/Users/darkgm/Desktop/event.txt' INTO TABLE event;
Query OK, 10 rows affected (0.01 sec)
Records: 10  Deleted: 0  Skipped: 0  Warnings: 0
```

查看内容：

```
mysql> select * from event;
+----------+------------+----------+-----------------------------+
| name     | date       | type     | remark                      |
+----------+------------+----------+-----------------------------+
| Fluffy   | 1995-05-15 | litter   | 4 kittens, 3 female, 1 male |
| Buffy    | 1993-06-23 | litter   | 5 puppies, 2 female, 3 male |
| Buffy    | 1994-06-19 | litter   | 3 puppies, 3 female         |
| Chirpy   | 1999-03-21 | vet      | needed beak straightened    |
| Slim     | 1997-08-03 | vet      | broken rib                  |
| Bowser   | 1991-10-12 | kennel   | NULL                        |
| Fang     | 1991-10-12 | kennel   | NULL                        |
| Fang     | 1998-08-28 | birthday | Gave him a new chew toy     |
| Claws    | 1998-03-17 | birthday | Gave him a new flea collar  |
| Whistler | 1998-12-09 | birthday | First birthday              |
+----------+------------+----------+-----------------------------+
10 rows in set (0.00 sec)
```

假设需要查找每个宠物在什么时间开始有自己的窝，这时需要用到pet表中宠物的出生日期，也就是说需要两个表，可以使用下面的语句：

```
mysql> SELECT pet.name, TIMESTAMPDIFF(YEAR, birth, date) AS age, remark FROM pet INNER JOIN event ON pet.name = event.name WHERE event.type = 'litter';
+--------+------+-----------------------------+
| name   | age  | remark                      |
+--------+------+-----------------------------+
| Fluffy |    2 | 4 kittens, 3 female, 1 male |
| Buffy  |    4 | 5 puppies, 2 female, 3 male |
| Buffy  |    5 | 3 puppies, 3 female         |
+--------+------+-----------------------------+
3 rows in set (0.00 sec)
```

需要注意的是：

- `FROM`子句连接了两个表。

- 当从多个表中连接信息时，需要指定一个表中的记录如何与另一个表中的记录相匹配。在上面的语句中使用了name列，并且使用`ON`子句来匹配两个表中基于name的记录。

  上面的查询中使用`INNER JOIN`来组合两个表。当且仅当两个表都满足ON子句中指定的条件时，INNER JOIN才允许来自任一表的行出现在结果中。 在此示例中，ON子句指定pet表中的name列必须与事件表中的name列匹配。 如果名称出现在一个表中但不出现在另一个表中，则该行不会出现在结果中，因为ON子句中的条件失败。

- 由于name列出现在两个表中，因此在引用表时，必须具体说明所指的表。可以通过将表名添加到列名称来完成的。

执行连接时也可以使用同一个表，如果要将表中的记录与同一表中的其他记录进行比较，有时将表连接到自身会很有用。 例如，要在宠物中找到繁殖对，可以将pet表加入它自身，来生成候选对的活体雄性和类似物种的雌性：

```
mysql> SELECT p1.name, p1.sex, p2.name, p2.sex, p1.species FROM pet AS p1 INNER JOIN pet AS p2 ON p1.species = p2.species AND p1.sex = 'f' AND p1.death IS NULL AND p2.sex = 'm' AND p2.death IS NULL;
+--------+------+-------+------+---------+
| name   | sex  | name  | sex  | species |
+--------+------+-------+------+---------+
| Fluffy | f    | Claws | m    | cat     |
| Buffy  | f    | Fang  | m    | dog     |
+--------+------+-------+------+---------+
2 rows in set (0.00 sec)
```

## 5 从数据库和表中获取信息

如果想查找当前选择的是哪个数据库，可以使用`DATEBASE()`函数：

```
mysql> SELECT DATABASE();
+------------+
| DATABASE() |
+------------+
| menagerie  |
+------------+
1 row in set (0.00 sec)
```

如果还没有选择任何数据库，则上面结果为NULL。

查找默认的数据库包含哪些表：

```
mysql> SHOW TABLES;
+---------------------+
| Tables_in_menagerie |
+---------------------+
| event               |
| pet                 |
+---------------------+
2 rows in set (0.00 sec)

```

如果需要查找表的结构，可以使用`DESCRIBE`语句，它会展示每个表的列：

```
mysql> DESCRIBE pet;
+---------+-------------+------+-----+---------+-------+
| Field   | Type        | Null | Key | Default | Extra |
+---------+-------------+------+-----+---------+-------+
| name    | varchar(20) | YES  |     | NULL    |       |
| owner   | varchar(20) | YES  |     | NULL    |       |
| species | varchar(20) | YES  |     | NULL    |       |
| sex     | char(1)     | YES  |     | NULL    |       |
| birth   | date        | YES  |     | NULL    |       |
| death   | date        | YES  |     | NULL    |       |
+---------+-------------+------+-----+---------+-------+
6 rows in set (0.01 sec)

mysql> DESCRIBE event;
+--------+--------------+------+-----+---------+-------+
| Field  | Type         | Null | Key | Default | Extra |
+--------+--------------+------+-----+---------+-------+
| name   | varchar(20)  | YES  |     | NULL    |       |
| date   | date         | YES  |     | NULL    |       |
| type   | varchar(15)  | YES  |     | NULL    |       |
| remark | varchar(255) | YES  |     | NULL    |       |
+--------+--------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

在上面的结果中：

`Field`表示列名；`Type`表示列的数据类型；`NULL`表明该列是否包含`NULL`值；`Key`表明该列是否编制了索引；`Default`指定了列的默认值；`Extra`显示了列的特殊信息。如果一个列使用`AUTO_INCREMENT`选项创建，则值将是`auto_increment`，而非空的。

另外，`DESC`是`DESCRIBE`的简写。

如果表中包含索引，可以使用`SHOW INDEX FROM tbl_name`查看信息。

## 6 在批处理模式下使用mysql（Using mysql in Batch Mode）

如果要在batch mode下运行mysql，需要将要运行的语句放到一个文件中，然后告诉mysql从文件中读取输入：

```
shell> mysql < batch-file
```

## 7 常见查询示例

使用下面的语句创建并填充一个示例表：

```
mysql> CREATE TABLE shop (article INT UNSIGNED DEFAULT '0000' NOT NULL, dealer CHAR(20) DEFAULT '' NOT NULL, price DECIMAL(16, 2) DEFAULt '0.00' NOT NULL, PRIMARY KEY(article, dealer));
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO shop VALUES (1, 'A', 3.45), (1, 'B', 3.99), (2, 'A', 10.99), (3, 'B', 1.45), (3, 'C', 1.69), (3, 'D', 1.25), (4, 'D', 19.95);
Query OK, 7 rows affected (0.01 sec)
Records: 7  Duplicates: 0  Warnings: 0
```

查看表中的内容：

```
mysql> SELECT * FROM shop ORDER BY article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | A      |  3.45 |
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | B      |  1.45 |
|       3 | C      |  1.69 |
|       3 | D      |  1.25 |
|       4 | D      | 19.95 |
+---------+--------+-------+
7 rows in set (0.00 sec)
```

#### 7.1 The Maximum Value for a Column

查找article列的最大值：

```
mysql> SELECT MAX(article) AS article FROM shop;
+---------+
| article |
+---------+
|       4 |
+---------+
1 row in set (0.01 sec)
```

#### 7.2 The Row Holding the Maximum of a Certain Column

查找最贵的文章的number、dealer、price：

```
mysql> SELECT article, dealer, price FROM shop WHERE price=(SELECT MAX(price) FROM shop);
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.01 sec)
```

还有一种解决方法是使用`LEFT JOIN`或按照价格降序排列所有行，并使用特定于MySQL的`LIMIT`子句获取第一行：

```
mysql> SELECT s1.article, s1.dealer, s1.price FROM shop s1 LEFT JOIN shop s2 ON s1.price < s2.price WHERE s2.article IS NULL;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.01 sec)

mysql> SELECT article, dealer, price FROM shop ORDER BY price DESC LIMIT 1;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       4 | D      | 19.95 |
+---------+--------+-------+
1 row in set (0.00 sec)
```

> 需要注意的是，如果最贵的文章有多个，每个的价格都是19.95，则`LIMIT`解决方案只会显示其中的一条数据。

#### 7.3 Maximum of Column per Group

查找每篇文章的最高价格：

```
mysql> SELECT article, MAX(price) AS price FROM shop GROUP BY article ORDER BY article;
+---------+-------+
| article | price |
+---------+-------+
|       1 |  3.99 |
|       2 | 10.99 |
|       3 |  1.69 |
|       4 | 19.95 |
+---------+-------+
4 rows in set (0.00 sec)
```

#### 7.4 The Rows Holding the Group-wise Maximum of a Certain Column

对于每篇文章，查找价格最高的零售商：

```
mysql> SELECT article, dealer, price FROM shop s1 WHERE price = (SELECT MAX(s2.price) FROM shop s2 WHERE s1.article = s2.article) ORDER BY article;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       1 | B      |  3.99 |
|       2 | A      | 10.99 |
|       3 | C      |  1.69 |
|       4 | D      | 19.95 |
+---------+--------+-------+
4 rows in set (0.00 sec)
```

上面例子使用了一个相关子查询（correlated subquery），比较低效。可以在FROM子句中使用不相关子查询（uncorrelated subquery），或使用`LEFT JOIN`，或者使用具有窗口函数的公用表表达式。

- Uncorrelated subquery:

  ```
  mysql> SELECT s1.article, dealer, s1.price FROM shop s1 JOIN (SELECT article, MAX(price) AS price FROM shop GROUP BY article) AS s2 ON s1.article = s2.article AND s1.price = s2.price ORDER BY article;
  +---------+--------+-------+
  | article | dealer | price |
  +---------+--------+-------+
  |       1 | B      |  3.99 |
  |       2 | A      | 10.99 |
  |       3 | C      |  1.69 |
  |       4 | D      | 19.95 |
  +---------+--------+-------+
  4 rows in set (0.00 sec)
  ```

- LEFT JOIN:

  ```
  mysql> SELECT s1.article, s1.dealer, s1.price FROM shop s1 LEFT JOIN shop s2 ON s1.article = s2.article AND s1.price < s2.price WHERE s2.article IS NULL ORDER BY s1.article;
  +---------+--------+-------+
  | article | dealer | price |
  +---------+--------+-------+
  |       1 | B      |  3.99 |
  |       2 | A      | 10.99 |
  |       3 | C      |  1.69 |
  |       4 | D      | 19.95 |
  +---------+--------+-------+
  4 rows in set (0.01 sec)
  ```

  > `LEFT JOIN`的工作原理是，当`s1.price`处于其最大值时，`s2.price`没有比它更大的值，因此相应的`s2.article`值为`NULL`。

- Common table expression with window function:

  ```
  mysql> WITH s1 AS (
      ->    SELECT article, dealer, price,
      ->           RANK() OVER (PARTITION BY article
      ->                            ORDER BY price DESC
      ->                       ) AS `Rank`
      ->      FROM shop
      -> )
      -> SELECT article, dealer, price
      ->   FROM s1
      ->   WHERE `Rank` = 1
      -> ORDER BY article;
  +---------+--------+-------+
  | article | dealer | price |
  +---------+--------+-------+
  |       1 | B      |  3.99 |
  |       2 | A      | 10.99 |
  |       3 | C      |  1.69 |
  |       4 | D      | 19.95 |
  +---------+--------+-------+
  4 rows in set (0.01 sec)
  ```

#### 7.5 Using User-Defined Variables

可以采用MySQL用户变量（user variables）来记录结果，而无需在服务器中以临时变量来存储它们。

例如，要查找最高和最低价格的文章，可以使用下面的语句：

```
mysql> SELECT @min_price:=MIN(price), @max_price:=MAX(price) FROM shop;
+------------------------+------------------------+
| @min_price:=MIN(price) | @max_price:=MAX(price) |
+------------------------+------------------------+
|                   1.25 |                  19.95 |
+------------------------+------------------------+
1 row in set, 2 warnings (0.00 sec)

mysql> SELECT * FROM shop WHERE price=@min_price OR price=@max_price;
+---------+--------+-------+
| article | dealer | price |
+---------+--------+-------+
|       3 | D      |  1.25 |
|       4 | D      | 19.95 |
+---------+--------+-------+
2 rows in set (0.00 sec)
```

也可以在用户变量中存储数据库对象（如表或列）的名称，然后在SQL语句中使用此变量，但是这需要准备好的声明。

#### 7.6 Using Foreign Keys

#### 7.7 Searching on Two Keys

在单个键上使用`OR`是很好的优化，跟`AND`的处理类似。

比较棘手的是，使用`OR`在两个键上搜索：

```
mysql> SELECT field1_index, field2_index FROM test_table 
    -> WHERE field1_index = '1' OR field2_index = '1';
```

也可以使用`UNION`有效的解决这个问题，即组合两个分离的`SELECT`语句的输出。每个`SELECT`只搜索一个键并可以进行优化：

```
mysql> SELECT field1_index, field2_index
    ->     FROM test_table WHERE field1_index = '1'
    -> UNION
    -> SELECT field1_index, field2_index
    ->     FROM text_table WHERE field2_index = '1';
```

#### 7.8 Calculating Visits Per Day

下面的示例显示了如何使用位组（bit group）函数来计算每个月用户访问网页的天数。

```
mysql> CREATE TABLE t1 (year YEAR, month INT UNSIGNED, day INT UNSIGNED);
Query OK, 0 rows affected (0.02 sec)

mysql> INSERT INTO t1 VALUES(2000,1,1),(2000,1,20),(2000,1,30),(2000,2,2),(2000,2,23),(2000,2,23);
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> select * from t1;
+------+-------+------+
| year | month | day  |
+------+-------+------+
| 2000 |     1 |    1 |
| 2000 |     1 |   20 |
| 2000 |     1 |   30 |
| 2000 |     2 |    2 |
| 2000 |     2 |   23 |
| 2000 |     2 |   23 |
+------+-------+------+
6 rows in set (0.00 sec)
```

上面语句创建了一个t1名称的表，包含了表示用户对该页面的访问的年月日值。如果想要知道这些访问在每个月中发生了多少天，可以使用下面的语句查询：

```
mysql> SELECT year,month,BIT_COUNT(BIT_OR(1<<day)) AS days FROM t1 GROUP BY year, month;
+------+-------+------+
| year | month | days |
+------+-------+------+
| 2000 |     1 |    3 |
| 2000 |     2 |    2 |
+------+-------+------+
2 rows in set (0.00 sec)
```

> 上面的查询会计算表中每个年/月组合出现了多少天，并会自动删除重复的条目。

#### 7.9 Using AUTO_INCREMENT

`AUTO_INCREMENT`属性可以为新行生成一个唯一标识符。

```
mysql> CREATE TABLE animals (
    ->     id MEDIUMINT NOT NULL AUTO_INCREMENT,
    ->     name CHAR(30) NOT NULL,
    ->     PRIMARY KEY (id)
    -> );
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO animals (name) VALUES ('dog'),('cat'),('penguin'),('lax'),('whale'),('ostrich');
Query OK, 6 rows affected (0.01 sec)
Records: 6  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM animals;
+----+---------+
| id | name    |
+----+---------+
|  1 | dog     |
|  2 | cat     |
|  3 | penguin |
|  4 | lax     |
|  5 | whale   |
|  6 | ostrich |
+----+---------+
6 rows in set (0.00 sec)
```

如果没有为`AUTO_INCREMENT`列指定任何值，MySQL会自动分配序号。如果想要生成序列号，可以为该列显式分配0，也可以启用`NO_AUTO_VALUE_ON_ZERO`模式。

```
mysql> INSERT INTO animals (id,name) VALUES(0, 'groundhog');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM animals;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | dog       |
|  2 | cat       |
|  3 | penguin   |
|  4 | lax       |
|  5 | whale     |
|  6 | ostrich   |
|  7 | groundhog |
+----+-----------+
7 rows in set (0.00 sec)
```

如果该列被声明为`NOT NULL`，也可以将`NULL`分配给该列来生成序列号：

```
mysql> INSERT INTO animals (id,name) VALUES(NULL,'squirrel');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM animals;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | dog       |
|  2 | cat       |
|  3 | penguin   |
|  4 | lax       |
|  5 | whale     |
|  6 | ostrich   |
|  7 | groundhog |
|  8 | squirrel  |
+----+-----------+
8 rows in set (0.01 sec)
```

如果想在`AUTO_INCREMENT`列插入其他的值，可以将该列设置为该值并重置序列，以便下一个自动生成的值从最大列的值开始依次出现：

```
mysql> INSERT INTO animals (id,name) VALUES(100, 'rabbit');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO animals (id,name) VALUES(NULL, 'mouse');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM animals;
+-----+-----------+
| id  | name      |
+-----+-----------+
|   1 | dog       |
|   2 | cat       |
|   3 | penguin   |
|   4 | lax       |
|   5 | whale     |
|   6 | ostrich   |
|   7 | groundhog |
|   8 | squirrel  |
| 100 | rabbit    |
| 101 | mouse     |
+-----+-----------+
10 rows in set (0.00 sec)
```

> 更新现有的`AUTO_INCREMENT`列值也会重置自增序列。

可以使用`LAST_INSERT_ID()`SQL函数或者`mysql_insert_id()`C API函数检索最新自动生成的`AUTO_INCREMENT`值。这些函数是特定于连接的，因此它们的返回值不受其他正在执行插入操作的连接的影响。

对于自增列使用最小整数数据类型，该数据类型应足够大以容纳你所需的最大序列值。当列到达数据类型的上限时，继续生成序列号将会失败。如果可能，请尽量使用`UNSIGNED`属性的数据类型，以允许更大范围的值。例如，如果使用`TINYINT`，则允许的最大序列号为127，对于`TINYINT UNSIGNED`，允许的最大值为255。

> 对于多行插入，`LAST_INSERT_ID()`和`mysql_insert_id()`实际上会从插入的第一行中返回`AUTO_INCREMENT`键。这样可以在复制设置中的其他服务器上正确地复制多行插入。（？？？）

如果需要使用自定义的自增值而非1开头，可以使用`CREATE TABLE`或者`ALTER TABLE`来设置自增值：

```
ALTER TABLE tbl AUTO_INCREMENT = 100;
```

> 如何使用？？

## 8 Using MySQL with Apache

有些程序可让你从MySQL数据库验证用户身份，还可以将日志文件写入MySQL表。

可以通过将以下内容放入Apache配置文件中来更改Apache日志记录格式，使MySQL更易于读取：

```
LogFormat \
        "\"%h\",%{%Y%m%d%H%M%S}t,%>s,\"%b\",\"%{Content-Type}o\",  \
        \"%U\",\"%{Referer}i\",\"%{User-Agent}i\""
```

要将这种格式的日志文件加载到MySQL中，可以使用类似以下的语句：

```
LOAD DATA INFILE '/local/access_log' INTO TABLE tbl_name
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' ESCAPED BY '\\'
```

对于上面的语句，应该创建一个语句中对应的表名，并使其具有与LogFormat行写入日志文件的列相对应的列。

## 参考资料

[Installing MySQL on macOS](<https://dev.mysql.com/doc/refman/8.0/en/osx-installation.html>)

[MySQL安装（Mac版）](<https://juejin.im/post/5cc2a52ce51d456e7079f27f>)

[安装MySQL](<https://www.liaoxuefeng.com/wiki/1177760294764384/1179611020917408>)

[MySQL-DOCUMENTATION-Chapter 3 Tutorial](<https://dev.mysql.com/doc/refman/8.0/en/tutorial.html>)



