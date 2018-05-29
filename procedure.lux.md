# Procedure Analyze 流程分析

## Slave startup
![start_slave_thread](docLux/start_slave_thread.png)

`main.cc:main` -> `mysqld.cc:mysqld_main` -> `rpl_slave.cc:init_slave` -> `rpl_slave.cc:start_slave_threads`


## Execute a command like `select * from a`
![command execute](docLux/mysql_execute_command.png)
