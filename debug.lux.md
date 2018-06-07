# Debug

## Ref
- http://8d12518c.wiz03.com/share/s/2d4B6c1cw4t92c7int2AVzYq2QA9cs1iOAyE2I58x31Ysqh2
- https://dev.mysql.com/doc/refman/8.0/en/making-trace-files.html
- https://fedoraproject.org/wiki/How_to_debug_MySQL_and_MariaDB

## Pre Setup
- 首先要有一个编译时设置了debug的mysql。也就是要编译一个mysql，并开启了debug
    - 编译，安装 https://dev.mysql.com/doc/internals/en/cmake.html
        -下载源码，目录srcPath
        - 新建一个build目录 buildPath
        - cd buildPath
        - cmake srcPath -DCMAKE_CXX_FLAGS="-fpermissive" -DWITH_DEBUG=1
            - \-fpermissive是因为编译5.6.24时候有warning，禁用掉。
        - make install DESTDIR=dstPath
    - 初始化
        - `scripts/mysql_install_db --datadir=dataDir`
    - 启动
        - `./bin/mysqld_safe --port=3330`
    - 验证是不是debug版本的
```
$  mysql ./bin/mysqld -V
./bin/mysqld  Ver 5.6.24-debug for osx10.13 on x86_64 (Source distribution)
```

- 启动的时候，指定debug就可以了
    - `./bin/mysqld_safe --port=3330 --debug`
    - 默认的日志文件是/tmp/mysqld.trace
   
### A debug Output Example
- start mysql server with debug `./bin/mysqld_safe --port=3330 --debug`
- connect and do stuff
```
$  mysql mysql -uroot -h127.0.0.1 -P3330 -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.24-debug-log Source distribution

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| a              |
+----------------+
1 row in set (0.00 sec)

mysql> select * from a;
+----+------+
| id | name |
+----+------+
|  1 | a    |
+----+------+
1 row in set (0.01 sec)

mysql> exit;
Bye

```

- kill mysql server
- Generated Trace File at `/tmp/mysql.trace`, see [here](docLux/mysqld.trace)

