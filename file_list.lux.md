# File List


## Thread

- `mysql_thread.h` 线程工具集
	- 互斥锁、读写锁
	- 是底层实际线程类（如pthread）的封装。
- `my_pthread` pthread线程实现类


## Replia

- `rpl_slave.cc` 从库工具集
	- 启动工作线程


## Binlog

- `log_event.cc` binlog事件实现集

## Sql

- `sql_db.cc` create,drop,use...db
- `sql_select.cc` select sql
- `sql_delete.cc` delete sql

## Vio

- `vioscocket.c` 虚拟io层
    - read,write

## Storage Engine

### Innodb

- `srv0start`
    - io thread handler
    
    
# Other

## Debug

- `dbug.c`  debug工具