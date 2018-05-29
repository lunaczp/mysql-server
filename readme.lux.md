# learning mysql

- [file list](./file_list.lux.md）
- [procedure analyze](./procedure.lux.md)
- [replication protocal](./replication.lux.md)


## toBeSolved

- log_pos overflow, see:/sql/log_event.cc:1151
当mysql写入的offset被截断的时候，slave从binlog拿到的offset就问题的。假如slave挂掉，重启，用这个offset向mysql发起请求，那么mysql给出的数据是对的？

- 应该是不对的。`mysys/mf_iocache2.c:132`，mysql会根据这个offset从缓存或者文件去读取，而读到的这个偏移就是个错误的位置。//todo 编译mysql，模拟从库验证
