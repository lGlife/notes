### 广州问题分析推测

大量的LWLock的锁，然后WALWriteLock是写wal日志的锁，推测是wal日志的配置参数，已经checkpoint机制导致：

![image-20210325221811149](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\其他杂事\postgresql 性能调用（广州监控新增）.assets\image-20210325221811149.png)



#### 参考链接

这次分析最重要的参考网站

**如何遏制PostgreSQL WAL的疯狂增长**

http://www.postgres.cn/news/viewone/1/273

**Postgresql之WAL log机制**

http://www.voidcn.com/article/p-fkhhtufz-bsd.html

**postgresql 并发访问_PostgreSQL中的LWLock浅谈**

https://blog.csdn.net/weixin_29343657/article/details/112365421

**Postgresql之CheckPoint机制**

https://blog.csdn.net/zhq651/article/details/89304285

**PostgreSQL 10.0 的三种日志**

https://developer.aliyun.com/article/694169

**PostgreSQL checkpoint 相关参数优化设置与解释**

https://blog.csdn.net/weixin_34283445/article/details/89719266?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs&dist_request_id=1328707.841.16166824014892891&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs

**认识PostgreSQL的Checkpoint（这文章还不错）**

https://www.dazhuanlan.com/2020/02/09/5e3f8e28a52e7/



#### 优化参数说明

shared_buffers = 16GB    # 1/4 内存    
checkpoint_completion_target = 0.9  # checkpoint target duration, 0.0 - 1.0   
checkpoint_timeout = 30min # range 30s-1d  
min_wal_size = 8GB # shared_buffers * 1/2   
max_wal_size = 32GB  # 2*shared_buffers  
#wal_log_hints = on
#wal_level = replica
#wal_keep_segments = 1000
wal_compression = on
wal_buffers = 16MB
synchronous_commit = off

建议设置 log_checkpoints=on ， 可以评估checkpoint的统计信息，用于帮助修正以上参数



**其他查询相关优化（之前查询很慢）**

max_parallel_workers=16  #cpu核数
max_worker_processes=16  #cpu核数
关闭两个索引类型
enable_bitmapscan=off
enable_indexonlyscan=off

**默认值**

![image-20210326141805659](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\postgresql 性能调用（广州监控新增）.assets\image-20210326141805659.png)



- wal_buffers参数

  WAL共享缓存区的大小可以通过设置postgresql.conf文件参数wal_buffers来设置，官方解释如下：

  表示WAL共享缓存区的大小，和oracle中的log buffer类似，单位可以是kB, MB

  默认值为-1，表示大小占shared_buffers大小的1/32，但是大于64kB，小于16MB

  - 用户可以自己设置，小于32kB的值会被转化为32kB

  - 只能在服务启动之前设置

    可以看出，当多个client同时进行事务提交时，如果这个缓存区比较大，相应地会更大程度地合并I/O，提高性能，但是如果过大，同时也存在断电后，这部分数据丢失的风险。所以，这里比较推荐使用默认值，即shared_buffers的1/32

- checkpoint_timeout
  
  - 系统自动执行checkpoint之间的最大时间间隔。合理的范围在 30 秒到 1 天之间。默认是 5 分钟(5min)。增加这个参数的值会增加崩溃恢复所需的时间。
- checkpoint_completion_target
  - 该参数表示checkpoint的完成时间占两次checkpoint时间间隔的比例，系统默认值是0.5,也就是说每个checkpoint需要在checkpoints间隔时间的50%内完成。
  - 什么意思呢，假如我的checkpoint_timeout设置是30分钟，而wal生成了10G（自己推测：10G非常大，如果完成checkpoint时间非常小，那么性能比较大），那么设置成0.5就允许可以在15分钟内完成checkpoint，调大这个值就可以降低checkpoint对性能的影响，但是万一数据库出现故障，那么这个值设置越大数据就越危险
- checkpoint_warning
  
- 系统默认值是30秒，如果checkpoints的实际发生间隔小于该参数，将会在server log中写入写入一条相关信息。可以通过设置为0禁用。
  
- max_wal_size (integer)
  
- 在自动 WAL 检查点之间允许 WAL 增长到的最大尺寸。这是一个软限制， 在特殊的情况下 WAL 尺寸可能会超过`max_wal_size`， 例如在重度负荷下、[archive_command](https://postgresqlco.nf/doc/zh/param/archive_command/)失败或者高的 [wal_keep_segments](https://postgresqlco.nf/doc/zh/param/wal_keep_segments/)设置。如果指定值时没有单位，则以兆字节为单位。默认为 1 GB。增加这个参数 可能导致崩溃恢复所需的时间。这个参数只能在postgresql.conf 或者服务器命令行中设置
  
- min_wal_size (integer)

  - 只要 WAL 磁盘用量保持在这个设置之下，在检查点时旧的 WAL 文件总是 被回收以便未来使用，而不是直接被删除。这可以被用来确保有足够的 WAL 空间被保留来应付 WAL 使用的高峰，例如运行大型的批处理任务。 如果指定值时没有单位，则以兆字节为单位。默认是 80 MB。这个参数只能在postgresql.conf 或者服务器命令行中设置

  

#### 参数详解官网

https://postgresqlco.nf/doc/zh/param/max_wal_size/



#### 什么情况会checkpoint

checkpoint，我们再来看看哪些情况会触发数据库的checkpoing：
1.手动执行CHECKPOINT命令；
2.执行需要检查点的命令（例如pg_start_backup 或pg_ctl stop|restart等等）；
3.达到检查点配置时间(checkpoint_timeout)；
4.max_wal_size已满。



### 分析过程中的sql分析

https://phyger.blog.csdn.net/article/details/113410108

#### 查询锁,删除锁

select w1.pid as 等待进程,
w1.mode as 等待锁模式,
w2.usename as 等待用户,
w2.query as 等待会话,
b1.pid as 锁的进程,
b1.mode 锁的锁模式,
b2.usename as 锁的用户,
b2.query as 锁的会话,
b2.application_name 锁的应用,
b2.client_addr 锁的IP地址,
b2.query_start 锁的语句执行时间
from pg_locks w1
join pg_stat_activity w2 on w1.pid=w2.pid
join pg_locks b1 on w1.transactionid=b1.transactionid and w1.pid!=b1.pid
join pg_stat_activity b2 on b1.pid=b2.pid
where not w1.granted;
————————————————
版权声明：本文为CSDN博主「Python测试和开发」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_40442753/article/details/113410108



删除锁：

```sql
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid='62560'
```



#### 查询进程、执行时间相关 、锁

SELECT 

C.relname 对象名称,
l.locktype 可锁对象的类型,
l.pid 进程id,
l.MODE 持有的锁模式,
l.GRANTED 是否已经对锁进行授权,
l.fastpath,
psa.datname 数据库名称,
psa.usesysid 用户id,
psa.usename 用户名称,
psa.application_name 应用程序名称,
psa.client_addr 连接的IP地址,
psa.client_port 连接使用的TCP端口号,
psa.backend_start 进程开始时间,
psa.xact_start 事务开始时间,
psa.query_start 事务执行此语句时间,
psa.state_change 事务状态改变时间,
psa.wait_event_type 等待事件类型,
psa.wait_event 等待事件,
psa.STATE 查询状态,

backend_xid 事务是否有写入操作,
backend_xmin 是否执事务快照,

psa.query 执行语句,
now( ) - query_start 持续时间

FROM

pg_locks l
INNER JOIN pg_stat_activity psa ON ( psa.pid = l.pid )
LEFT OUTER JOIN pg_class C ON ( l.relation = C.oid )
-- where l.relation = 'tb_base_apparatus'::regclass

where relkind ='r'
ORDER BY query_start asc



#### 是否到达自动清理的表

SELECT
    c.relname 表名,
    (current_setting('autovacuum_analyze_threshold')::NUMERIC(12,4))+(current_setting('autovacuum_analyze_scale_factor')::NUMERIC(12,4))*reltuples AS 自动分析阈值,
    (current_setting('autovacuum_vacuum_threshold')::NUMERIC(12,4))+(current_setting('autovacuum_vacuum_scale_factor')::NUMERIC(12,4))*reltuples AS 自动清理阈值,
    reltuples::DECIMAL(19,0) 活元组数,
    n_dead_tup::DECIMAL(19,0) 死元组数
FROM
    pg_class c 
LEFT JOIN pg_stat_all_tables d
    ON C.relname = d.relname
WHERE
    c.relname LIKE'tb%'  AND reltuples > 0
    AND n_dead_tup > (current_setting('autovacuum_analyze_threshold')::NUMERIC(12,4))+(current_setting('autovacuum_analyze_scale_factor')::NUMERIC(12,4))*reltuples;



#### 手动收缩表

```
VACUUMFULL VERBOSE 表名;VACUUM FULL VERBOSE ANALYZE 表名;
```