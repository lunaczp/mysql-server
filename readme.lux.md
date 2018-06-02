# learning mysql

- [protocol basic](./protocol_basic.lux.md)
- [file list](./file_list.lux.md)
- [procedure analyze](./procedure.lux.md)
- [replication protocal](./replication.lux.md)


## Other

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


## toBeSolved

- log_pos overflow, see:/sql/log_event.cc:1151
当mysql写入的offset被截断的时候，slave从binlog拿到的offset就问题的。假如slave挂掉，重启，用这个offset向mysql发起请求，那么mysql给出的数据是对的？

- 应该是不对的。`mysys/mf_iocache2.c:132`，mysql会根据这个offset从缓存或者文件去读取，而读到的这个偏移就是个错误的位置。//todo 编译mysql，模拟从库验证

## Debug
- [Debug](./debug.lux.md)

## Ref
[Internal Mannual](https://dev.mysql.com/doc/internals/en/)
