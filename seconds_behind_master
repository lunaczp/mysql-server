# `seconds_behind_master

`seconds_behind_master` is caculated and showed when `show_slave_status` is executed

```
//sql/rpl_slave.cc:2754
bool show_slave_status(THD* thd, Master_info* mi)
{
...
//2979
    /*
      The pseudo code to compute Seconds_Behind_Master:
        if (SQL thread is running)
        {
          if (SQL thread processed all the available relay log)
          {
            if (IO thread is running)
              print 0;
            else
              print NULL;
          }
          else
            compute Seconds_Behind_Master;
        }
        else
          print NULL;
    */
    if (mi->rli->slave_running)
    {
      /* Check if SQL thread is at the end of relay log
           Checking should be done using two conditions
           condition1: compare the log positions and
           condition2: compare the file names (to handle rotation case)
      */
      if ((mi->get_master_log_pos() == mi->rli->get_group_master_log_pos()) &&
           (!strcmp(mi->get_master_log_name(), mi->rli->get_group_master_log_name())))
      {
        if (mi->slave_running == MYSQL_SLAVE_RUN_CONNECT)
          protocol->store(0LL);
        else
          protocol->store_null();
      }
      else
      {
        long time_diff= ((long)(time(0) - mi->rli->last_master_timestamp)
                                - mi->clock_diff_with_master);
```

From the code, we can see that it relies on `last_master_timestamp` and `clock_diff_with_master`.

## `clock_diff_with_master`

```
//sql/rpl_mi.h:255
  /*
     The difference in seconds between the clock of the master and the clock of
     the slave (second - first). It must be signed as it may be <0 or >0.
     clock_diff_with_master is computed when the I/O thread starts; for this the
     I/O thread does a SELECT UNIX_TIMESTAMP() on the master.
     "how late the slave is compared to the master" is computed like this:
     clock_of_slave - last_timestamp_executed_by_SQL_thread - clock_diff_with_master

  */
  long clock_diff_with_master;
```

It's the diff of os clock where master and slave lives in.

## `last_master_timestamp`

`last_master_timestamp` is calculated when `exec_relay_log_event`

```
//sql/rpl_slave.cc:4090
static int exec_relay_log_event(THD* thd, Relay_log_info* rli)
{
...
//4132
    /*
      Even if we don't execute this event, we keep the master timestamp,
      so that seconds behind master shows correct delta (there are events
      that are not replayed, so we keep falling behind).

      If it is an artificial event, or a relay log event (IO thread generated
      event) or ev->when is set to 0, or a FD from master, or a heartbeat
      event with server_id '0' then  we don't update the last_master_timestamp.
    */
    if (!(rli->is_parallel_exec() ||
          ev->is_artificial_event() || ev->is_relay_log_event() ||
          ev->when.tv_sec == 0 || ev->get_type_code() == FORMAT_DESCRIPTION_EVENT ||
          ev->server_id == 0))
    {
      rli->last_master_timestamp= ev->when.tv_sec + (time_t) ev->exec_time;/*lux: 事件发生时刻+持续时间*/
      DBUG_ASSERT(rli->last_master_timestamp >= 0);
    }
```
```
//sql/log_event.h:1098
  /*
    Timestamp on the master(for debugging and replication of
    NOW()/TIMESTAMP).  It is important for queries and LOAD DATA
    INFILE. This is set at the event's creation time, except for Query
    and Load (et al.) events where this is set at the query's
    execution time, which guarantees good replication (otherwise, we
    could have a query and its event with different timestamps).
  */
  struct timeval when;
  /* The number of seconds the query took to run on the master. */
  ulong exec_time;
```
We see that `last_master_timestamp` is updated whenever exectuting a relay log event(except some event), and it equals to _the timestamp this event created on master__ plus its execution time

![exec_relay_log_event](./exec_relay_log_event.png)
