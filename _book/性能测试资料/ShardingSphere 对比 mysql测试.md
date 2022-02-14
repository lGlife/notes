# 测试说明

Apache ShardingSphere是一套开源的分布式数据库解决方案组成的生态圈，它由JDBC、Proxy和Sidecar （规划中）这 3 款既能够独立部署，又支持混合部署配合使用的产品组成。它们均提供标准化的数据水平扩展、分布式事务和分布式治理等功能，可适用于如 Java 同构、异构语言、云原生等各种多样化的应用 场景。 Apache ShardingSphere 旨在充分合理地在分布式的场景下利用关系型数据库的计算和存储能力，而并非实现一个全新的关系型数据库。

而ShardingSphere-proxy定位为透明化的数据库代理端，提供封装了数据库二进制协议的服务端版本，用于完成对异构语言的支 持。目前提供MySQL和PostgreSQL版本，它可以使用任何兼容MySQL/PostgreSQL协议的访问客户端 (如：MySQL Command Client, MySQLWorkbench, Navicat 等) 操作数据，对DBA更加友好。 

- 向应用程序完全透明，可直接当做MySQL/PostgreSQL使用。
- 适用于任何兼容MySQL/PostgreSQL协议的的客户端。

本测试读者只需熟悉mysql和分布式数据库概念即可

# 测试准备

**测试工具(涉及中间件)**

| 工具           | 生产厂商/自产 | 用途           | 版本        |
| -------------- | ------------- | -------------- | ----------- |
| jmeter         | Apache        | 压测           | 5.4.1       |
| 和宇大数据平台 | 自产          | 业务测试       | 演示版      |
| keepalived     | 开源          | 高可用         | 2.1.5-6.el7 |
| lvs            | 开源          | 负载均衡       | 1.27-8.el7  |
| zookeeper      | Apache        | 配置、注册中心 | 3.7.0       |

**测试环境**

- 单计算节点

  | 主机          | 用途              | 资源配置               | 操作系统 |
  | ------------- | ----------------- | ---------------------- | -------- |
  | 172.18.40.194 | 计算节点/存储节点 | 8核CPU 8G内存 265G磁盘 | Linux    |
  | 172.18.40.195 | 存储节点          | 8核CPU 8G内存 215G磁盘 | Linux    |
  | 172.18.40.196 | 存储节点          | 8核CPU 8G内存 215G磁盘 | Linux    |
  | 172.18.40.198 | 存储节点          | 8核CPU 8G内存 215G磁盘 | Linux    |

- MySQL节点

| 主机          | 用途        | 资源配置               | 操作系统 |
| ------------- | ----------- | ---------------------- | -------- |
| 172.18.40.145 | 单节点MySQL | 8核CPU 8G内存 365G磁盘 | Linux    |

# 测试执行与结果

## 单机sharding-proxy性能测试

### proxy的读取性能

**测试场景**

分别测试**2千万**数据的50字段水平表100、500、1000并发执行1分钟、5分钟、10分钟的**查询**操作(有索引)，记录运行期间的CPU、内存、TPS、IO

**测试sql**

```
select * from acct_retn_evt_c where EVTSN=${tid}; 
```

其中${tid}为1到1亿随机数

**测试方法**

使用jmeter，持续随机读取水平表(有索引)某条数据

**测试执行过程**

##### 10并发10分钟

![image-20210924091528038](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091528038.png)

吞吐量变化

![image-20210924091548702](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091548702.png)

##### 100并发下1分钟

![image-20210916163035707](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916163035707.png)

吞吐量变化

![image-20210917100419950](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100419950.png)

##### 100并发下5分钟

![image-20210917092135522](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092135522.png)

吞吐量变化

![image-20210917100518800](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100518800.png)

##### 100并发下10分钟

![image-20210917092156832](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092156832.png)

吞吐量变化

![image-20210917100612526](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100612526.png)

##### 500并发下1分钟

![image-20210916163044841](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916163044841.png)

吞吐量变化

![image-20210917100636089](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100636089.png)

##### 500并发下5分钟

![image-20210917092213992](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092213992.png)

吞吐量变化

![image-20210917100653652](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100653652.png)

##### 500并发下10分钟

![image-20210917092230054](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092230054.png)

吞吐量变化

![image-20210917100713850](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917100713850.png)

##### 1000并发下1分钟

![image-20210916163057527](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916163057527.png)

吞吐量变化

![image-20210917101045501](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917101045501.png)

##### 1000并发下5分钟

![image-20210917092313231](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092313231.png)

吞吐量变化

![image-20210917101104478](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917101104478.png)

##### 1000并发下10分钟

![image-20210917092342366](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917092342366.png)

吞吐量变化

![image-20210917101126333](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917101126333.png)

#### sharding测试结论

| 并发数 | 执行时间 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 4.50     | 4.00    | 5.00    | 1324001 | 2205.20 |
| 100    | 1分钟    | 17.67    | 24      | 28      | 334937  | 5603.41 |
| 100    | 5分钟    | 17.16    | 24.00   | 28.00   | 3488151 | 5813.88 |
| 100    | 10分钟   | 17.57    | 25      | 28      | 3406844 | 5678.04 |
| 500    | 1分钟    | 85.06    | 163     | 219     | 337537  | 5824.92 |
| 500    | 5分钟    | 92.55    | 177.00  | 242.00  | 3238314 | 5395.89 |
| 500    | 10分钟   | 90.83    | 187.00  | 246.00  | 1648923 | 5493.88 |
| 1000   | 1分钟    | 173.21   | 362     | 480     | 326738  | 5719.95 |
| 1000   | 5分钟    | 174.13   | 338.00  | 480.00  | 1721075 | 5730.59 |
| 1000   | 10分钟   | 185.35   | 378.00  | 527.00  | 3235114 | 5389.30 |

sharding在2000万数据量时，随着并发量增加，平均时延和p90、p95时延增加，吞吐量随之降低，在100并发下吞吐量最大

### proxy的写入性能

**测试场景**

1. 分别测试一个**50字段**的水平表100、500、1000并发执行1分钟、5分钟、10分钟的**插入**操作，记录运行期间的CPU、内存、TPS、IO

2. 分别测试一个**2千万数据50字段**的水平表100、500、1000并发执行1分钟的**插入**操作，记录运行期间的CPU、内存、TPS、IO


**测试方法**

使用jmeter，持续插入数据

**测试sql**

插入数据，将EVTSN、SERV_MATT_INST_ID、、SERV_MATT_NODE_INST_ID、EVT_INST_ID四个bigint字段设置为随机数
姓名PSN_NAME字段设置为随机名字，时间字段统一设置为函数now(6)

```sql
insert into acct_retn_evt_c (EVTSN, SERV_MATT_INST_ID, SERV_MATT_NODE_INST_ID, EVT_INST_ID,
                             ACCT_RETN_BCHNO, EVT_TYPE, YEAR, PSN_NO, PSN_INSU_RLTS_ID, PSN_CERT_TYPE, CERTNO,
                             PSN_NAME, GEND, BRDY, CONER_NAME, TEL, ADDR, INSU_ADMDVS, EMP_NO, EMP_NAME, TRT_ISSU_WAY,
                             DFR_OBJ, RETN_TYPE, RETN_REA, INT_FLAG, OLD_BALC, ACCT_RETN_AMT, BALC, BANKCODE,
                             BANK_TYPE_CODE, BANKACCT, BANK_SAMECITY_OUT_FLAG, RETN_OBJ_NAME, ACCTNAME, AGNTER_NAME,
                             AGNTER_CERT_TYPE, AGNTER_CERTNO, AGNTER_TEL, AGNTER_ADDR, AGNTER_RLTS, VALI_FLAG,
                             RCHK_FLAG, MEMO, RID, UPDT_TIME, CRTER_ID, CRTER_NAME, CRTE_TIME, CRTE_OPTINS_NO, OPTER_ID,
                             OPTER_NAME, OPT_TIME, OPTINS_NO, POOLAREA_NO)
values (${tid}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, 11, 01, 2021,
        43000011100006806991, 43111000000001026471, 01, 432901194410052045,
        '${__RandomString(1,赵钱孙李周,)}${__RandomString(1,强喜勇凤英,)}${__RandomString(1,泽雨佳浩欣,)}', 2, now(6), '', '', '',
        431102, 430000111000001392, '永州市零陵区公路建设养护中心', 11, 11, 2, '', 0, 10000.00, 10000.00,
        ${fnum}.${__Random(0,9,)}${__Random(0,9,)}, '', 0, 2990010100100573282, '', '李满春', '李满春', '', '', '', '', '',
        '', 1, 1, '', 430000202105061618241400079764, now(6), 1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132',
        1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132', 431102);
```

**测试执行过程**

##### 10并发10分钟

![image-20210924092909634](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092909634.png)

吞吐量变化

![image-20210924092931774](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092931774.png)

##### 100并发1分钟

![image-20210916095759660](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916095759660.png)

吞吐量变化

![image-20210917103016974](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103016974.png)

##### 100并发5分钟

![image-20210917103053731](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103053731.png)

吞吐量变化

![image-20210917103113932](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103113932.png)

##### 100并发10分钟

![image-20210917103151156](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103151156.png)

吞吐量变化

![image-20210917103210770](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103210770.png)

##### 500并发1分钟

![image-20210916100911938](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916100911938.png)

吞吐量变化

![image-20210917104037599](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917104037599.png)

##### 500并发5分钟

![image-20210917103249707](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103249707.png)

吞吐量变化

![image-20210917103927649](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103927649.png)

##### 500并发10分钟

![](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103437096.png)

吞吐量变化

![image-20210917103900312](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103900312.png)

##### 1000并发1分钟

![image-20210916100935654](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916100935654.png)

吞吐量变化

![image-20210917103821900](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103821900.png)

##### 1000万并发5分钟

![image-20210917103507070](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103507070.png)

吞吐量变化

![image-20210917103736312](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103736312.png)

##### 1000万并发10分钟

![image-20210917103659336](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103659336.png)

吞吐量变化

![image-20210917103718826](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917103718826.png)

##### 100并发20万次

![image-20210916111625551](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916111625551.png)

吞吐量变化

![image-20210917104237579](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917104237579.png)

#### **2千万数据下的写入测试**

##### 10并发10分钟

![image-20210924091758323](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091758323.png)

吞吐量变化

![image-20210924091827592](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091827592.png)

##### 100并发1分钟

![image-20210916143530459](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916143530459.png)

吞吐量变化

![image-20210917171932359](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917171932359.png)

##### 100并发5分钟

![image-20210917140618832](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917140618832.png)

吞吐量变化

![](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917171932359.png)

##### 100并发10分钟

![image-20210917140548696](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917140548696.png)

吞吐量变化

![image-20210917173609770](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917173609770.png)

##### 500并发1分钟

![image-20210916143538610](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916143538610.png)

吞吐量变化

![image-20210917173926231](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917173926231.png)

##### 500并发5分钟

![image-20210917140658169](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917140658169.png)

吞吐量变化

![image-20210917174125877](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917174125877.png)

##### 500并发10分钟

![image-20210917140731637](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917140731637.png)

![image-20210917174147765](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917174147765.png)

##### 1000并发1分钟

![image-20210916143546265](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916143546265.png)

吞吐量变化

![image-20210917174358340](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917174358340.png)

##### 1000并发10分钟

![image-20210918154440858](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918154440858.png)

吞吐量变化

![image-20210918154455258](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918154455258.png)

#### sharding测试结论

零数据下的写入测试

| 并发数 | 持续时间 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 5.49     | 5.00    | 6.00    | 1086208 | 1810.40 |
| 100    | 1分钟    | 15.5     | 22      | 28      | 384971  | 6376    |
| 100    | 5分钟    | 12.70    | 21.00   | 31.00   | 2350768 | 7835.71 |
| 100    | 10分钟   | 12.74    | 20.00   | 26.00   | 4690274 | 7816.90 |
| 500    | 1分钟    | 67       | 146     | 200     | 445034  | 7398    |
| 500    | 5分钟    | 65.52    | 127.00  | 164.00  | 2284848 | 7612.96 |
| 500    | 10分钟   | 66.26    | 142.00  | 179.00  | 4521867 | 7534.66 |
| 1000   | 1分钟    | 140.39   | 257     | 328     | 425039  | 7061    |
| 1000   | 5分钟    | 141.49   | 326.00  | 462.00  | 2117388 | 7052.37 |
| 1000   | 10分钟   | 134.68   | 332.00  | 433.00  | 4451103 | 7415.58 |

2千万数据下的写入测试

| 并发数 | 持续时间 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 4.19     | 5.00    | 6.00    | 1418170 | 2363.70 |
| 100    | 1分钟    | 15.25    | 21      | 29      | 389181  | 6485    |
| 100    | 5分钟    | 17.38    | 21.00   | 27.00   | 1718875 | 5730.08 |
| 100    | 10分钟   | 14.81    | 20.00   | 25.00   | 4037436 | 6727.14 |
| 500    | 1分钟    | 77.11    | 187     | 580     | 388110  | 6428    |
| 500    | 5分钟    | 69.91    | 159.00  | 235.00  | 2146966 | 7126.55 |
| 500    | 10分钟   | 75.25    | 134.00  | 161.00  | 3981468 | 6634.50 |
| 1000   | 1分钟    | 170      | 275     | 362     | 351169  | 5826    |
| 1000   | 10分钟   | 166.92   | 822.80  | 1740.00 | 3594015 | 5983.75 |



### proxy的读写性能

**测试场景**

分别测试**2千万**数据的50字段水平表100、500、1000并发执行1分钟、5分钟、10分钟的**查询插入混合**操作，记录运行期间的CPU、内存、TPS、IO

**测试方法**

使用jmeter，持续随机读取水平表(有索引)某条数据和插入数据

**执行sql**

每次执行两条sql语句：一句为读取的sql，一句为写入的sql

读取sql

```sql
select * from acct_retn_evt_c where  EVTSN=${tid};
```

写入sql

```sql
insert into acct_retn_evt_c (EVTSN, SERV_MATT_INST_ID, SERV_MATT_NODE_INST_ID, EVT_INST_ID,
                             ACCT_RETN_BCHNO, EVT_TYPE, YEAR, PSN_NO, PSN_INSU_RLTS_ID, PSN_CERT_TYPE, CERTNO,
                             PSN_NAME, GEND, BRDY, CONER_NAME, TEL, ADDR, INSU_ADMDVS, EMP_NO, EMP_NAME, TRT_ISSU_WAY,
                             DFR_OBJ, RETN_TYPE, RETN_REA, INT_FLAG, OLD_BALC, ACCT_RETN_AMT, BALC, BANKCODE,
                             BANK_TYPE_CODE, BANKACCT, BANK_SAMECITY_OUT_FLAG, RETN_OBJ_NAME, ACCTNAME, AGNTER_NAME,
                             AGNTER_CERT_TYPE, AGNTER_CERTNO, AGNTER_TEL, AGNTER_ADDR, AGNTER_RLTS, VALI_FLAG,
                             RCHK_FLAG, MEMO, RID, UPDT_TIME, CRTER_ID, CRTER_NAME, CRTE_TIME, CRTE_OPTINS_NO, OPTER_ID,
                             OPTER_NAME, OPT_TIME, OPTINS_NO, POOLAREA_NO)
values (${tid}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, 11, 01, 2021,
        43000011100006806991, 43111000000001026471, 01, 432901194410052045,
        '${__RandomString(1,赵钱孙李周,)}${__RandomString(1,强喜勇凤英,)}${__RandomString(1,泽雨佳浩欣,)}', 2, now(6), '', '', '',
        431102, 430000111000001392, '永州市零陵区公路建设养护中心', 11, 11, 2, '', 0, 10000.00, 10000.00,
        ${fnum}.${__Random(0,9,)}${__Random(0,9,)}, '', 0, 2990010100100573282, '', '李满春', '李满春', '', '', '', '', '',
        '', 1, 1, '', 430000202105061618241400079764, now(6), 1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132',
        1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132', 431102);
```

**测试执行过程**

##### 10并发10分钟

![image-20210924092057422](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092057422.png)

吞吐量变化

![image-20210924092130573](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092130573.png)

##### 100并发1分钟

![image-20210916165049413](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916165049413.png)

吞吐量变化

![image-20210917155039444](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917155039444.png)

##### 100并发10分钟

![image-20210918153812645](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918153812645.png)

吞吐量变化

![image-20210918153829393](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918153829393.png)

##### 500并发1分钟

![image-20210916165117558](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916165117558.png)

吞吐量变化

![image-20210917155108074](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917155108074.png)

##### 1000并发1分钟

![image-20210916165154784](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916165154784.png)

吞吐量变化

![image-20210917155132976](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917155132976.png)



#### **sharding测试结论**

| 并发数 | 持续时间 | 操作 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | ---- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 插入 | 7.65     | 6.00    | 12.00   | 269990  | 449.89  |
| 10     | 10分钟   | 读取 | 14.50    | 12.00   | 30.00   | 269988  | 453.34  |
| 100    | 1分钟    | 插入 | 17.34    | 29.00   | 36.00   | 194501  | 3240.82 |
| 100    | 1分钟    | 读取 | 13.19    | 23.00   | 31.00   | 194449  | 3269.75 |
| 100    | 10分钟   | 插入 | 19.94    | 33.00   | 44.00   | 1718355 | 2863.94 |
| 100    | 10分钟   | 读取 | 14.87    | 29.00   | 39.00   | 1718308 | 2872.07 |
| 500    | 1分钟    | 插入 | 94.92    | 189.00  | 250.00  | 177830  | 2955.12 |
| 500    | 1分钟    | 读取 | 72.81    | 158.00  | 205.00  | 177548  | 2984.30 |
| 1000   | 1分钟    | 插入 | 171.82   | 386.00  | 447.00  | 196138  | 3254.92 |
| 1000   | 1分钟    | 读取 | 132.97   | 299.00  | 413.00  | 195610  | 3282.60 |

随着并发数的增加，平均时延和p90、p95时延随之增高，最高吞吐量为3254/3282此时并发数为1000

### proxy的数据更新性能

**测试场景**

分别测试**2千万**数据的50字段水平表在无索引和有索引执行**更新**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录排序(asc、desc)

使用jmeter，执行语句

```sql
update table_name set field1=new_value where 索引字段=${tid};
```

**测试执行过程**

#### 带索引情况下的更新

##### 10并发10分钟

![image-20210924092521314](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092521314.png)

吞吐量变化

![image-20210924092548759](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924092548759.png)

##### 100并发1分钟

![image-20210917143553925](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143553925.png)

吞吐量变化

![image-20210917143614800](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143614800.png)

##### 100并发10分钟

![image-20210918145054752](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918145054752.png)

吞吐量变化

![image-20210918145126726](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918145126726.png)



##### 300并发1分钟

![image-20210917143631592](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143631592.png)

吞吐量变化

![image-20210917143649676](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143649676.png)

##### 500并发1分钟

![image-20210917143711424](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143711424.png)

吞吐量变化

![image-20210917143732026](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143732026.png)

##### 700并发1分钟

![image-20210917143747700](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143747700.png)

吞吐量变化

![image-20210917143807089](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143807089.png)

##### 1000并发1分钟

![image-20210917143824500](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143824500.png)

吞吐量变化

![image-20210917143840064](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917143840064.png)

#### 不带索引情况下的更新

##### 10并发1分钟

![image-20210917163336147](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163336147.png)

吞吐量变化

![image-20210917163557002](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163557002.png)

##### 30并发1分钟

![image-20210917163350113](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163350113.png)

吞吐量变化

![image-20210917163713454](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163713454.png)

##### 50并发1分钟

![image-20210917163401928](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163401928.png)

吞吐量变化

![image-20210917163650229](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163650229.png)

##### 70并发1分钟

![image-20210917163423812](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163423812.png)

吞吐量变化

![image-20210917163759655](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163759655.png)

##### 100并发1分钟

![image-20210917152809032](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917152809032.png)

![image-20210917152926069](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917152926069.png)

#### sharding测试结论

带索引的测试结果

| 并发数 | 执行时间 | 平均时延 | p95时延 | p90时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 4.66     | 3.00    | 4.00    | 1279816 | 2133.04 |
| 100    | 1分钟    | 48.59    | 71.00   | 132.00  | 123226  | 2031.02 |
| 100    | 10分钟   | 17.82    | 18.00   | 23.00   | 3358691 | 5597.40 |
| 300    | 1分钟    | 110.56   | 320.90  | 539.95  | 161787  | 2665.49 |
| 500    | 1分钟    | 152.25   | 336.00  | 567.00  | 196094  | 3254.24 |
| 700    | 1分钟    | 218.05   | 339.00  | 482.00  | 191957  | 3188.71 |
| 1000   | 1分钟    | 294.33   | 1151.00 | 1539.00 | 205198  | 3322.45 |

不带索引的测试结果

| 并发数 | 执行时间 | 平均时延  | p95时延   | p90时延   | 总量 | 吞吐量 | 异常率 |
| ------ | -------- | --------- | --------- | --------- | ---- | ------ | ------ |
| 10     | 2分钟    | 47905.12  | 64858.20  | 70087.00  | 17   | 0.18   | 0%     |
| 30     | 3分钟    | 179580.27 | 182254.80 | 182878.30 | 30   | 0.16   | 0%     |
| 50     | 6分钟    | 332501.36 | 357108.40 | 361555.35 | 50   | 0.14   | 0%     |
| 70     | 6分钟    | 199274.72 | 359992.10 | 363875.35 | 90   | 0.25   | 44.44% |
| 100    | 7分钟    | 84702.01  | 332518.50 | 349000.15 | 230  | 0.61   | 78.26% |

### proxy的数据排序性能

**测试场景**

分别测试**2千万**数据的50字段水平表在无索引和有索引执行**排序**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录排序(asc、desc)

使用jmeter，执行语句

```sql
select * from acct_retn_evt_c order by EVTSN desc limit 1;
```

**测试执行过程**

10并发10分钟

![image-20210922145747163](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922145747163.png)

吞吐量变化

![image-20210922150242768](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922150242768.png)

无索引时10并发10分钟

![image-20210923114627865](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923114627865.png)

吞吐量变化

![image-20210923115005726](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923115005726.png)

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 有索引   | 2.69     | 4.00    | 4.00    | 2211909 | 3686.62 |
| 10     | 10分钟   | 无索引   | 6121.13  | 6847.80 | 7025.80 | 985     | 1.63    |



### proxy的扫描性能

**测试场景**

分别测试**2千万**数据的50字段水平表并发执行无索引和有索引的**扫描**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录全表扫描，求指定的记录

使用jmeter，执行语句

```sql
select count(*) from acct_retn_evt_c where EVTSN=${tid};
```

**测试执行过程**

10并发10分钟

不带索引的全表扫描

![image-20210923112612153](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923112612153.png)

吞吐量变化

![image-20210923113157199](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923113157199.png)

带索引的对比

![image-20210923160252445](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923160252445.png)

吞吐量变化

![image-20210923160609573](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923160609573.png)

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 无索引   | 5342.50  | 6015.40 | 6184.60 | 1127    | 1.86    |
| 10     | 10分钟   | 有索引   | 1.44     | 2.00    | 2.00    | 4098038 | 6830.27 |

### proxy的join性能

**测试场景**

分别测试**2千万**数据的50字段水平表并发执行无索引和有索引的**查询**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录a join b 2千万记录，按a表的输入条件过滤，按b表聚合

使用jmeter，执行语句

```sql
select count(b.EVTSN) from acct_retn_evt_c_b a join acct_retn_evt_c b on (a.EVTSN=b.EVTSN and a.EVTSN=${tid});
```

**测试执行过程**

10并发10分钟

![image-20210922150835370](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922150835370.png)

吞吐量变化

![image-20210922151034589](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922151034589.png)

无索引的1次join

![image-20210923135549924](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923135549924.png)

**测试结论**

| 并发量 | 执行时间  | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | --------- | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟    | 有索引   | 1.89     | 2.00    | 3.00    | 3139211 | 5232.15 |
| 10     | 74秒(1次) | 无索引   | 74038    | 74038   | 74038   | 1       | 0.01    |

### proxy的分组聚合性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**分组聚合**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录，分组聚合

使用jmeter，执行语句

```sql
select max(SERV_MATT_INST_ID)
from acct_retn_evt_c
where EVTSN > 9900000
group by EVTSN;
```

**测试执行过程**

10并发10分钟

![image-20210923083551064](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923083551064.png)

吞吐量变化

![image-20210923083650452](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923083650452.png)

无索引1次测试结果

![image-20210923141426732](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923141426732.png)

吞吐量为

![image-20210923141513554](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923141513554.png)

**测试结论**

| 并发量 | 执行时间     | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量 | 吞吐量 |
| ------ | ------------ | -------- | -------- | ------- | ------- | ---- | ------ |
| 10     | 10分钟       | 有索引   | 893.92   | 1031.00 | 1076.00 | 6711 | 11.17  |
| 1      | 95798(9.5秒) | 无索引   | 95798    | 95798   | 95798   | 1    | 0.01   |

### proxy的过滤性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**过滤**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录字符串、浮点、时间、整型过滤

使用jmeter，先进行联合索引创建

```sql
create index test_fuhe1 on acct_retn_evt_c(EVTSN,PSN_NAME,BALC,BRDY);
```

然后再执行语句

```sql
select count(*)
from acct_retn_evt_c
where PSN_NAME = '周勇泽'
   or BALC < '8000.00'
   or EVTSN > 90000000
   or BRDY < str_to_date('2021-09-22 20:46:18.0', 'yyyy-mm-dd hh:MM:ss');
```

**测试执行过程**

![image-20210923083714855](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923083714855.png)

吞吐量变化

![image-20210923083809639](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923083809639.png)

无索引1次测试结果

![image-20210923141612158](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923141612158.png)

吞吐量为

![image-20210923141737017](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923141737017.png)

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延  | p95时延  | 总量 | 吞吐量 |
| ------ | -------- | -------- | -------- | -------- | -------- | ---- | ------ |
| 10     | 10分钟   | 有索引   | 11962.75 | 12843.20 | 13106.70 | 505  | 0.83   |
| 1      | 117秒    | 无索引   | 117849   | 117849   | 117849   | 1    | 0.01   |

### proxy的范围查询性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**范围查询**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录范围查询统计(统计区间)、(max，min)

使用jmeter，首先创建时间索引

```
create index(time_index) on acct_retn_evt_c(BRDY);
```

之后执行语句

统计区间：

```
select count(EVTSN) from acct_retn_evt_c where BRDY between str_to_date('2021-09-22 20:46:18.0','yyyy-mm-dd hh:MM:ss') and str_to_date('2021-09-22 20:56:18.0','yyyy-mm-dd hh:MM:ss');
```

最值查询：

```
select max(EVTSN),min(EVTSN) from acct_retn_evt_c;
```

**测试执行过程**

10并发10分钟

带索引的范围查询

![image-20210923101419682](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923101419682.png)

吞吐量变化

![image-20210923101604398](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923101604398.png)

不带索引的范围查询

![image-20210923141939606](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923141939606.png)

吞吐量

![image-20210923142032907](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923142032907.png)

**测试结论**

| 并发量 | 执行时间 | 操作     | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量  |
| ------ | -------- | -------- | -------- | -------- | ------- | ------- | ------- | ------- |
| 10     | 10分钟   | 最值查询 | 有索引   | 1.42     | 2.00    | 2.00    | 2056257 | 3432.48 |
| 10     | 10分钟   | 统计区间 | 有索引   | 1.46     | 2.00    | 2.00    | 2056262 | 3427.20 |
| 1      | 90秒     | 最值查询 | 无索引   | 90791    | 90791   | 90791   | 1       | 0.01    |
| 1      | 111秒    | 统计区间 | 无索引   | 111351   | 111351  | 111351  | 1       | 0.01    |

### proxy的创建索引性能

**测试场景**

测试2千万数据的50字段水平表执行**创建索引**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

使用jmeter，执行create index test_t1 on tableName(field3);语句

**测试执行过程**

创建简单二级索引

![image-20210923084423601](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923084423601.png)

创建联合索引

![image-20210923084710640](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923084710640.png)

**测试结论**

| 并发量 | 执行时间 | 索引     | 平均时延 | p90时延 | p95时延 | 总量 | 吞吐量 |
| ------ | -------- | -------- | -------- | ------- | ------- | ---- | ------ |
| 1      | 27527    | 简单索引 | 27527    | 27527   | 27527   | 1    | 0.04   |
| 1      | 40916    | 联合索引 | 40916    | 40916   | 40916   | 1    | 0.02   |





## 单机mysql性能测试

### mysql的读取性能

**测试场景**

分别测试**2千万**数据的50字段水平表100、500、1000并发执行1分钟、5分钟、10分钟的**查询**操作(有索引)，记录运行期间的CPU、内存、TPS、IO

**测试方法**

使用jmeter，持续随机读取水平表(有索引)某条数据

```
select * from acct_retn_evt_c where EVTSN=${tid}; 
```

其中${tid}为1到1亿随机数

**测试执行过程**

##### 10并发10分钟

![image-20210924085322667](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924085322667.png)

吞吐量变化

![image-20210924085402812](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924085402812.png)

##### 100并发1分钟

![image-20210916172120476](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916172120476.png)

吞吐量变化

![image-20210917175148650](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175148650.png)

##### 100并发5分钟

![image-20210917091036777](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917091036777.png)

吞吐量变化

![image-20210917175205596](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175205596.png)

##### 100并发10分钟

![image-20210918134005249](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918134005249.png)

吞吐量变化

![image-20210918134028737](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918134028737.png)

##### 500并发1分钟

![image-20210916172123998](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916172123998.png)

吞吐量变化

![image-20210917175239720](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175239720.png)

##### 500并发5分钟

![image-20210917091102808](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917091102808.png)

吞吐量变化

![image-20210917175427983](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175427983.png)

##### 500并发10分钟

![image-20210917091118944](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917091118944.png)

吞吐量变化

![image-20210917175459395](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175459395.png)

##### 1000并发1分钟

![image-20210916172127340](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916172127340.png)

吞吐量变化

![image-20210917175528932](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917175528932.png)

##### 1000并发5分钟

![image-20210917091150075](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917091150075.png)

吞吐量变化

![image-20210917180026871](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917180026871.png)

##### 1000并发10分钟

![image-20210917091211882](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917091211882.png)

吞吐量变化

![image-20210917180048258](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917180048258.png)

#### **mysql测试结论**

| 并发数 | 执行时间 | 平均时延 | p90时延 | p95时延 | 总量   | 吞吐量 |
| ------ | -------- | -------- | ------- | ------- | ------ | ------ |
| 10     | 10分钟   | 33.02    | 60.00   | 115.00  | 181497 | 302.04 |
| 100    | 1分钟    | 192.89   | 471.00  | 697.00  | 30898  | 509.01 |
| 100    | 5分钟    | 103.49   | 152.00  | 299.95  | 289358 | 963.97 |
| 100    | 10分钟   | 174.16   | 350.00  | 668.00  | 344210 | 573.48 |
| 500    | 1分钟    | 753.80   | 1613.00 | 2021.00 | 39952  | 640.46 |
| 500    | 5分钟    | 548.87   | 1140.90 | 1445.95 | 273569 | 907.72 |
| 500    | 10分钟   | 560.42   | 1151.00 | 1446.00 | 535301 | 890.10 |
| 1000   | 1分钟    | 2100.83  | 4354.00 | 5297.00 | 29000  | 465.02 |
| 1000   | 5分钟    | 1068.49  | 2244.00 | 2720.00 | 280833 | 932.48 |
| 1000   | 10分钟   | 1186.17  | 2216.00 | 2726.90 | 505958 | 841.04 |

mysql的单机读取随着并发数增加时延增加，p95、p90时延也随之增加。吞吐量最高为640，此时并发数为500

### mysql的写入性能

**测试场景**

1. 分别测试一个**50字段**的水平表100、500、1000并发执行1分钟、5分钟、10分钟的**插入**操作，记录运行期间的CPU、内存、TPS、IO

2. 分别测试一个**2千万数据50字段**的水平表100、500、1000并发执行1分钟的**插入**操作，记录运行期间的CPU、内存、TPS、IO

**测试方法**

使用jmeter，持续插入数据

插入数据，将EVTSN、SERV_MATT_INST_ID、、SERV_MATT_NODE_INST_ID、EVT_INST_ID四个bigint字段设置为随机数 姓名PSN_NAME字段设置为随机名字，时间字段统一设置为函数now(6)

```
insert into acct_retn_evt_c (EVTSN, SERV_MATT_INST_ID, SERV_MATT_NODE_INST_ID, EVT_INST_ID,
                             ACCT_RETN_BCHNO, EVT_TYPE, YEAR, PSN_NO, PSN_INSU_RLTS_ID, PSN_CERT_TYPE, CERTNO,
                             PSN_NAME, GEND, BRDY, CONER_NAME, TEL, ADDR, INSU_ADMDVS, EMP_NO, EMP_NAME, TRT_ISSU_WAY,
                             DFR_OBJ, RETN_TYPE, RETN_REA, INT_FLAG, OLD_BALC, ACCT_RETN_AMT, BALC, BANKCODE,
                             BANK_TYPE_CODE, BANKACCT, BANK_SAMECITY_OUT_FLAG, RETN_OBJ_NAME, ACCTNAME, AGNTER_NAME,
                             AGNTER_CERT_TYPE, AGNTER_CERTNO, AGNTER_TEL, AGNTER_ADDR, AGNTER_RLTS, VALI_FLAG,
                             RCHK_FLAG, MEMO, RID, UPDT_TIME, CRTER_ID, CRTER_NAME, CRTE_TIME, CRTE_OPTINS_NO, OPTER_ID,
                             OPTER_NAME, OPT_TIME, OPTINS_NO, POOLAREA_NO)
values (${tid}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, 11, 01, 2021,
        43000011100006806991, 43111000000001026471, 01, 432901194410052045,
        '${__RandomString(1,赵钱孙李周,)}${__RandomString(1,强喜勇凤英,)}${__RandomString(1,泽雨佳浩欣,)}', 2, now(6), '', '', '',
        431102, 430000111000001392, '永州市零陵区公路建设养护中心', 11, 11, 2, '', 0, 10000.00, 10000.00,
        ${fnum}.${__Random(0,9,)}${__Random(0,9,)}, '', 0, 2990010100100573282, '', '李满春', '李满春', '', '', '', '', '',
        '', 1, 1, '', 430000202105061618241400079764, now(6), 1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132',
        1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132', 431102);
```

**测试执行过程**

- 零数据时写入结果

##### 10并发10分钟

![image-20210924090422381](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924090422381.png)

吞吐量变化

![image-20210924090500021](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924090500021.png)

##### 100并发1分钟

![image-20210916183122735](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916183122735.png)

吞吐量变化

![image-20210918095533711](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095533711.png)

##### 100并发10分钟

![image-20210918102854535](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918102854535.png)

吞吐量变化

![image-20210918102910490](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918102910490.png)

##### 500并发1分钟

![image-20210916182556640](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916182556640.png)

吞吐量变化

![image-20210918095630139](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095630139.png)

##### 500并发10分钟

![image-20210918104930422](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918104930422.png)

吞吐量变化

![image-20210918104957309](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918104957309.png)

##### 1000并发1分钟

![image-20210916183514519](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916183514519.png)

吞吐量变化

![image-20210918095656165](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095656165.png)

##### 1000并发10分钟

![image-20210918115042825](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918115042825.png)

吞吐量变化

![image-20210918115114414](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918115114414.png)

#### 2千万数据时写入结果

##### 10并发10分钟

![image-20210924085754692](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924085754692.png)

吞吐量变化

![image-20210924085824991](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924085824991.png)

##### 100并发1分钟

![image-20210917185003415](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917185003415.png)

吞吐量变化

![](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095419264.png)

##### 100并发10分钟

![image-20210918093543380](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918093543380.png)

吞吐量变化

![image-20210918095824203](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095824203.png)

##### 500并发1分钟

![image-20210916181145275](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916181145275.png)

吞吐量变化

![image-20210918095311946](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095311946.png)

##### 500并发10分钟

![image-20210918134857533](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918134857533.png)

吞吐量变化

![image-20210918134920684](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918134920684.png)

##### 1000并发1分钟

![image-20210916181153946](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916181153946.png)

吞吐量变化

![image-20210918095721153](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918095721153.png)

##### 1000并发10分钟

![image-20210918155942394](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918155942394.png)

吞吐量变化

![image-20210918155955427](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918155955427.png)

#### mysql测试结论

零数据时的测试结果

| 并发数 | 执行时间 | 平均时延 | p90时延 | p95时延 | 总量     | 吞吐量   |
| ------ | -------- | -------- | ------- | ------- | -------- | -------- |
| 10     | 10分钟   | 0.70     | 1.00    | 1.00    | 8236598  | 13723.68 |
| 100    | 1分钟    | 4.11     | 8.00    | 10.00   | 1430299  | 23845.47 |
| 100    | 10分钟   | 4.92     | 11.00   | 16.00   | 12088426 | 20138.15 |
| 500    | 1分钟    | 18.39    | 54.00   | 78.00   | 1484831  | 26889.9  |
| 500    | 10分钟   | 26.04    | 71.00   | 116.00  | 11489740 | 19145.42 |
| 1000   | 1分钟    | 45.09    | 153.0   | 228.00  | 1369189  | 21958.48 |
| 1000   | 10分钟   | 48.81    | 165.00  | 250.00  | 12275877 | 20448.65 |

2千万数据时的测试结果

| 并发数 | 执行时间 | 平均时延 | p90时延 | p95时延 | 总量     | 吞吐量   |
| ------ | -------- | -------- | ------- | ------- | -------- | -------- |
| 10     | 10分钟   | 0.71     | 1.00    | 1.00    | 8144015  | 13573.95 |
| 100    | 1分钟    | 4.04     | 7.00    | 10.00   | 1453835  | 24224.93 |
| 100    | 10分钟   | 4.44     | 8.00    | 11.00   | 13381488 | 22301.81 |
| 500    | 1分钟    | 20.02    | 57.00   | 86.00   | 1484831  | 24698.20 |
| 500    | 10分钟   | 30.21    | 97.00   | 824.95  | 9908357  | 16510.13 |
| 1000   | 1分钟    | 43.58    | 146.90  | 223.95  | 1369189  | 22730.41 |
| 1000   | 10分钟   | 71.99    | 366.00  | 566.00  | 8324239  | 13867.61 |

### mysql的读写性能

**测试场景**

分别测试**2千万**数据的50字段水平表100、500、1000并发执行1分钟、5分钟、10分钟的**查询插入混合**操作，记录运行期间的CPU、内存、TPS、IO

**测试方法**

使用jmeter，持续随机读取水平表(有索引)某条数据和插入数据

每次执行两条sql语句：一句为读取的sql，一句为写入的sql

读取sql

```sql
select * from acct_retn_evt_c where  EVTSN=${tid};
```

写入sql

```sql
insert into acct_retn_evt_c (EVTSN, SERV_MATT_INST_ID, SERV_MATT_NODE_INST_ID, EVT_INST_ID,
                             ACCT_RETN_BCHNO, EVT_TYPE, YEAR, PSN_NO, PSN_INSU_RLTS_ID, PSN_CERT_TYPE, CERTNO,
                             PSN_NAME, GEND, BRDY, CONER_NAME, TEL, ADDR, INSU_ADMDVS, EMP_NO, EMP_NAME, TRT_ISSU_WAY,
                             DFR_OBJ, RETN_TYPE, RETN_REA, INT_FLAG, OLD_BALC, ACCT_RETN_AMT, BALC, BANKCODE,
                             BANK_TYPE_CODE, BANKACCT, BANK_SAMECITY_OUT_FLAG, RETN_OBJ_NAME, ACCTNAME, AGNTER_NAME,
                             AGNTER_CERT_TYPE, AGNTER_CERTNO, AGNTER_TEL, AGNTER_ADDR, AGNTER_RLTS, VALI_FLAG,
                             RCHK_FLAG, MEMO, RID, UPDT_TIME, CRTER_ID, CRTER_NAME, CRTE_TIME, CRTE_OPTINS_NO, OPTER_ID,
                             OPTER_NAME, OPT_TIME, OPTINS_NO, POOLAREA_NO)
values (${tid}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, ${__Random(1,10000000,)}, 11, 01, 2021,
        43000011100006806991, 43111000000001026471, 01, 432901194410052045,
        '${__RandomString(1,赵钱孙李周,)}${__RandomString(1,强喜勇凤英,)}${__RandomString(1,泽雨佳浩欣,)}', 2, now(6), '', '', '',
        431102, 430000111000001392, '永州市零陵区公路建设养护中心', 11, 11, 2, '', 0, 10000.00, 10000.00,
        ${fnum}.${__Random(0,9,)}${__Random(0,9,)}, '', 0, 2990010100100573282, '', '李满春', '李满春', '', '', '', '', '',
        '', 1, 1, '', 430000202105061618241400079764, now(6), 1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132',
        1584543210558005744, '永州市零陵区测试用户', now(6), 'S43110270132', 431102);
```

**测试执行过程**

##### 10并发10分钟

![image-20210924090738722](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924090738722.png)

吞吐量变化

![image-20210924090759205](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924090759205.png)

##### 100并发1分钟

![image-20210916184416487](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916184416487.png)

吞吐量变化图

![image-20210918101624185](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918101624185.png)

##### 100并发10分钟

![image-20210918153008579](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918153008579.png)

吞吐量变化

![image-20210918153029435](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918153029435.png)

##### 500并发1分钟

![image-20210916184401025](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916184401025.png)

吞吐量变化图

![image-20210918101647585](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918101647585.png)

##### 1000并发1分钟

![image-20210916184609005](https://gitee.com/LuoBest/PicGo/raw/master/image-20210916184609005.png)

吞吐量变化图

![image-20210918101710485](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918101710485.png)

#### **mysql测试结论**

| 并发数 | 持续时间 | 操作 | 平均时延 | p90时延  | p95时延  | 总量  | 吞吐量 | 异常率 |
| ------ | -------- | ---- | -------- | -------- | -------- | ----- | ------ | ------ |
| 10     | 10分钟   | 插入 | 17.18    | 6.00     | 58.00    | 36531 | 61.02  | 0%     |
| 10     | 10分钟   | 读取 | 146.98   | 457.00   | 712.00   | 36541 | 60.82  | 0%     |
| 100    | 1分钟    | 插入 | 447.20   | 1157.00  | 1775.00  | 6172  | 101.97 | 0%     |
| 100    | 1分钟    | 读取 | 525.93   | 278.00   | 1282.00  | 6228  | 101.93 | 0%     |
| 100    | 10分钟   | 插入 | 248.57   | 348.00   | 832.00   | 79167 | 132.09 | 0%     |
| 100    | 10分钟   | 读取 | 508.54   | 1046.00  | 1652.00  | 79240 | 131.95 | 0%     |
| 500    | 1分钟    | 插入 | 2912.65  | 6706.00  | 8890.85  | 5666  | 89.04  | 2.77%  |
| 500    | 1分钟    | 读取 | 2438.32  | 5499.50  | 8184.50  | 5924  | 92.35  | 1.74%  |
| 1000   | 1分钟    | 插入 | 6418.77  | 10000.00 | 10001.00 | 5036  | 76.93  | 22.72% |
| 1000   | 1分钟    | 读取 | 5510.44  | 10000.00 | 10001.00 | 5480  | 82.92  | 19.84% |

异常的原因都是无法获取连接

500并发1分钟异常截图

![image-20210918084449991](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918084449991.png)

1000并发1分钟异常截图

![image-20210918084609966](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918084609966.png)

### mysql的更新性能

**测试场景**

分别测试**2千万**数据的50字段水平表在无索引和有索引执行**更新**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录排序(asc、desc)

使用jmeter，执行语句

```sql
update table_name set field1=new_value where 索引字段=${tid};
```

**测试执行过程**

#### 带索引情况下的更新

##### 10并发10分钟

![image-20210924091022418](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091022418.png)

吞吐量变化

![image-20210924091126601](https://gitee.com/LuoBest/PicGo/raw/master/image-20210924091126601.png)

##### 100并发1分钟

![image-20210917145846942](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917145846942.png)

吞吐量变化

![image-20210917145858770](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917145858770.png)

##### 100并发10分钟

![image-20210918145806929](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918145806929.png)

吞吐量变化

![image-20210918145832395](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918145832395.png)

##### 300并发1分钟

![image-20210917145911077](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917145911077.png)

吞吐量变化

![image-20210917145929701](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917145929701.png)

##### 500并发1分钟

![image-20210917150015801](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150015801.png)

吞吐量变化

![image-20210917150019272](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150019272.png)

##### 700并发1分钟

![image-20210917150042503](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150042503.png)

吞吐量变化

![image-20210917150045798](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150045798.png)

##### 1000并发1分钟

![image-20210917150112463](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150112463.png)

吞吐量变化

![image-20210917150115361](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150115361.png)

异常原因

![image-20210917150133540](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917150133540.png)

#### 不带索引情况下的更新

##### 10并发1分钟

![image-20210917180504317](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917180504317.png)

吞吐量变化

![image-20210917180519737](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917180519737.png)

##### 30并发1分钟

![image-20210917163926479](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163926479.png)

吞吐量变化

![image-20210917163946168](https://gitee.com/LuoBest/PicGo/raw/master/image-20210917163946168.png)



#### mysql测试结论

| 并发数 | 执行时间 | 平均时延 | p95时延  | p90时延  | 总量  | 吞吐量 | 异常率 |
| ------ | -------- | -------- | -------- | -------- | ----- | ------ | ------ |
| 10     | 10分钟   | 148.06   | 476.00   | 736.95   | 40483 | 67.47  | 0%     |
| 100    | 1分钟    | 1175.22  | 2705.60  | 4667.10  | 5116  | 83.84  | 0%     |
| 100    | 10分钟   | 906.60   | 1716.90  | 3315.75  | 66195 | 110.09 | 0%     |
| 300    | 1分钟    | 2256.35  | 4955.00  | 7064.40  | 8041  | 129.48 | 0.20%  |
| 500    | 1分钟    | 3597.33  | 7408.80  | 9660.30  | 8445  | 133.98 | 2.49%  |
| 700    | 1分钟    | 4193.30  | 8791.00  | 10000.00 | 10437 | 158.34 | 5.08%  |
| 1000   | 1分钟    | 5023.71  | 10000.00 | 10001.00 | 12633 | 177.65 | 9.42%  |

不带索引时测试结论

| 并发数 | 执行时间 | 平均时延  | p95时延   | p90时延   | 异常率 | 总量 | 吞吐量 |
| ------ | -------- | --------- | --------- | --------- | ------ | ---- | ------ |
| 10     | 7分钟    | 447395.40 | 469395.40 | 469424.00 | 20%    | 10   | 0.02   |
| 30     | 5分钟    | 893732.97 | 913631.40 | 913764.05 | 0%     | 30   | 0.03   |

有索引时的异常原因都是无法获取连接

300并发1分钟异常截图

![image-20210918090741503](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918090741503.png)

500并发1分钟异常截图

![image-20210918090821801](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918090821801.png)

700并发1分钟异常截图

![image-20210918084804307](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918084804307.png)

1000并发1分钟异常截图

![image-20210918090903414](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918090903414.png)

无索引时异常为执行时间过长事务回滚

10并发7分钟(执行一次)的异常截图

![image-20210918091102069](https://gitee.com/LuoBest/PicGo/raw/master/image-20210918091102069.png)

### mysql的数据排序性能

**测试场景**

分别测试**2千万**数据的50字段水平表在无索引和有索引执行**排序**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录排序(asc、desc)

使用jmeter，执行语句

```sql
select *
from acct_retn_evt_c
order by EVTSN asc
limit 1;
```

**测试执行过程**

10并发10分钟

![image-20210922161314044](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922161314044.png)

吞吐量变化

![image-20210922161632686](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922161632686.png)

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量     | 吞吐量   |
| ------ | -------- | -------- | -------- | ------- | ------- | -------- | -------- |
| 10     | 10分钟   | 有索引   | 0.53     | 1.00    | 1.00    | 11011393 | 18352.81 |
| 1      |          | 无索引   | 执行失败 |         |         |          |          |



### mysql的扫描性能

**测试场景**

分别测试**2千万**数据的50字段水平表并发执行无索引和有索引的**扫描**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录全表扫描，求指定的记录

使用jmeter语句执行

```sql
select count(*) from acct_retn_evt_c where EVTSN=${tid};
```

**测试执行过程**

无索引时的全表扫描

单独一次执行测试失败

有索引时的对比

![image-20210923144609012](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923144609012.png)



**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量     | 吞吐量   |
| ------ | -------- | -------- | -------- | ------- | ------- | -------- | -------- |
| 1      |          | 无索引   | 执行失败 |         |         |          |          |
| 10     | 10分钟   | 有索引   | 0.39     | 1.00    | 1.00    | 14484545 | 24141.47 |

### mysql的join性能

**测试场景**

分别测试**2千万**数据的50字段水平表并发执行无索引和有索引的**查询**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录a join b 2千万记录，按a表的输入条件过滤，按b表聚合

使用jmeter，执行语句

```
select count(b.EVTSN)
from acct_retn_evt_c_b a
         join acct_retn_evt_c b on (a.EVTSN = b.EVTSN and a.EVTSN = ${tid});
```

**测试执行过程**

10并发10分钟

![image-20210922165636293](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922165636293.png)

吞吐量变化

![image-20210922165821967](https://gitee.com/LuoBest/PicGo/raw/master/image-20210922165821967.png)

无索引时join测试失败

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延  | p95时延  | 总量     | 吞吐量   |
| ------ | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 10     | 10分钟   | 有索引   | 0.43     | 1.00     | 1.00     | 13182104 | 21971.16 |
| 1      | 执行失败 | 无索引   | 执行失败 | 执行失败 | 执行失败 | 执行失败 | 执行失败 |

### mysql的分组聚合性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**分组聚合**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录，分组聚合

使用jmeter，执行语句

```
select max(SERV_MATT_INST_ID)
from acct_retn_evt_c
where EVTSN > 9900000
group by EVTSN;
```

**测试执行过程**

无法正常进行1次执行

**测试结论**

测试失败

### mysql的过滤性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**过滤**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录字符串、浮点、时间、整型过滤

使用jmeter，执行语句

```
select count(*)
from acct_retn_evt_c
where PSN_NAME = '周勇泽'
   or BALC < '8000.00'
   or EVTSN > 90000000
   or BRDY < str_to_date('2021-09-22 20:46:18.0', 'yyyy-mm-dd hh:MM:ss');
```

**测试执行过程**

有索引10并发10分钟执行结果

![image-20210923110821427](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923110821427.png)

吞吐量变化

![image-20210923112459812](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923112459812.png)

无索引时测试执行失败

**测试结论**

| 并发量 | 执行时间 | 有无索引 | 平均时延 | p90时延  | p95时延  | 总量 | 吞吐量 |
| ------ | -------- | -------- | -------- | -------- | -------- | ---- | ------ |
| 10     | 10分钟   | 有索引   | 38945.53 | 39820.70 | 40008.35 | 160  | 0.26   |

### mysql的范围查询性能

**测试场景**

分别测试2千万数据的50字段水平表并发执行无索引和有索引的**范围查询**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

2千万记录范围查询统计(统计区间)、(max，min)

使用jmeter，执行语句

统计区间

```sql
select count(EVTSN) from acct_retn_evt_c where BRDY between str_to_date('2021-09-22 20:46:18.0','yyyy-mm-dd hh:MM:ss') and str_to_date('2021-09-22 20:56:18.0','yyyy-mm-dd hh:MM:ss');
```

最值查询

```sql
select max(EVTSN),min(EVTSN) from acct_retn_evt_c;
```

**测试执行过程**

10并发10分钟

![image-20210923100341823](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923100341823.png)

吞吐量变化

![image-20210923100627251](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923100627251.png)

无索引下测试

![image-20210923164822791](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923164822791.png)



**测试结论**

| 并发量 | 执行时间    | 操作     | 有无索引 | 平均时延 | p90时延 | p95时延 | 总量    | 吞吐量   |
| ------ | ----------- | -------- | -------- | -------- | ------- | ------- | ------- | -------- |
| 10     | 10分钟      | 最值查询 | 有索引   | 0.36     | 1.00    | 1.00    | 7748430 | 12923.38 |
| 10     | 10分钟      | 统计区间 | 有索引   | 0.37     | 1.00    | 1.00    | 7748433 | 12914.57 |
| 1      | 8分钟(1次)  | 最值查询 | 无索引   | 494421   | 494421  | 494421  | 1       | 0        |
| 1      | 10分钟(1次) | 统计区间 | 无索引   | 628646   | 628646  | 628646  | 628646  | 0        |

### mysql的创建索引性能

**测试场景**

测试2千万数据的50字段水平表执行**创建索引**操作，记录运行期间的CPU、内存、TPS、IO和**执行时间**

**测试方法**

使用jmeter，执行语句

创建简单二级索引

```sql
create index test_1 on acct_retn_evt_c (EVTSN);
```

创建联合索引

```sql
create index test_fuhe2 on acct_retn_evt_c (EVTSN, PSN_NAME, BALC, BRDY);
```

**测试执行过程**

创建简单二级索引

![image-20210923101710889](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923101710889.png)

创建联合索引

![image-20210923103418154](https://gitee.com/LuoBest/PicGo/raw/master/image-20210923103418154.png)

**测试结论**

| 并发量 | 执行时间 | 索引     | 平均时延 | p90时延 | p95时延 | 总量 | 吞吐量 |
| ------ | -------- | -------- | -------- | ------- | ------- | ---- | ------ |
| 1      | 27527    | 简单索引 | 914210   | 914210  | 914210  | 1    | 0      |
| 1      | 40916    | 联合索引 | 943717   | 943717  | 943717  | 1    | 0      |





## 综合测试结论

| 测试类型                  | 并发数 | 执行时间       | 数据库       | 平均时延 | 总量     | 吞吐量   | 异常率 |
| ------------------------- | ------ | -------------- | ------------ | -------- | -------- | -------- | ------ |
| 读取测试(2千万数据)       | 10     | 10分钟         | **sharding** | 4.50     | 1324001  | 2205.20  | 0%     |
| 读取测试(2千万数据)       | 10     | 10分钟         | mysql        | 33.02    | 181497   | 302.04   | 0%     |
| 读取测试(2千万数据)       | 100    | 1分钟          | sharding     | 17.67    | 334937   | 5603.41  | 0%     |
| 读取测试(2千万数据)       | 100    | 1分钟          | mysql        | 192.89   | 30898    | 509.01   | 0%     |
| 读取测试(2千万数据)       | 100    | 5分钟          | **sharding** | 17.16    | 3488151  | 5813.88  | 0%     |
| 读取测试(2千万数据)       | 100    | 5分钟          | mysql        | 103.49   | 289358   | 963.97   | 0%     |
| 读取测试(2千万数据)       | 100    | 10分钟         | **sharding** | 17.57    | 3406844  | 5678.04  | 0%     |
| 读取测试(2千万数据)       | 100    | 10分钟         | mysql        | 174.16   | 344210   | 573.48   | 0%     |
| 读取测试(2千万数据)       | 500    | 1分钟          | **sharding** | 85.06    | 337537   | 5824.92  | 0%     |
| 读取测试(2千万数据)       | 500    | 1分钟          | mysql        | 753.8    | 39952    | 640.46   | 0%     |
| 读取测试(2千万数据)       | 500    | 5分钟          | **sharding** | 92.55    | 3238314  | 5395.89  | 0%     |
| 读取测试(2千万数据)       | 500    | 5分钟          | mysql        | 548.87   | 273569   | 907.72   | 0%     |
| 读取测试(2千万数据)       | 500    | 10分钟         | **sharding** | 90.83    | 1648923  | 5493.88  | 0%     |
| 读取测试(2千万数据)       | 500    | 10分钟         | mysql        | 560.42   | 535301   | 890.1    | 0%     |
| 读取测试(2千万数据)       | 1000   | 1分钟          | mysql        | 2100.83  | 29000    | 465.02   | 0%     |
| 读取测试(2千万数据)       | 1000   | 1分钟          | **sharding** | 173.21   | 326738   | 5719.95  | 0%     |
| 读取测试(2千万数据)       | 1000   | 5分钟          | **sharding** | 174.13   | 1721075  | 5730.59  | 0%     |
| 读取测试(2千万数据)       | 1000   | 5分钟          | mysql        | 1068.49  | 280833   | 932.48   | 0%     |
| 读取测试(2千万数据)       | 1000   | 10分钟         | **sharding** | 185.35   | 3235114  | 5389.3   | 0%     |
| 读取测试(2千万数据)       | 1000   | 10分钟         | mysql        | 1186.17  | 505958   | 841.04   | 0%     |
|                           |        |                |              |          |          |          |        |
| 写入(0数据)               | 10     | 10分钟         | **sharding** | 5.49     | 1086208  | 1810.40  | 0%     |
| 写入(0数据)               | 10     | 10分钟         | mysql        | 0.70     | 8236598  | 13723.68 | 0%     |
| 写入(0数据)               | 100    | 1分钟          | **sharding** | 15.5     | 384971   | 6376     | 0%     |
| 写入(0数据)               | 100    | 1分钟          | mysql        | 4.11     | 1430299  | 23845.47 | 0%     |
| 写入(0数据)               | 100    | 10分钟         | **sharding** | 12.74    | 4690274  | 7816.9   | 0%     |
| 写入(0数据)               | 100    | 10分钟         | mysql        | 12.74    | 4690274  | 7816.9   | 0%     |
| 写入(0数据)               | 500    | 1分钟          | **sharding** | 67       | 445034   | 7398     | 0%     |
| 写入(0数据)               | 500    | 1分钟          | mysql        | 18.39    | 1484831  | 26889.9  | 0%     |
| 写入(0数据)               | 500    | 10分钟         | **sharding** | 66.26    | 4521867  | 7534.66  | 0%     |
| 写入(0数据)               | 500    | 10分钟         | mysql        | 26.04    | 11489740 | 19145.42 | 0%     |
| 写入(0数据)               | 1000   | 1分钟          | **sharding** | 140.39   | 425039   | 7061     | 0%     |
| 写入(0数据)               | 1000   | 1分钟          | mysql        | 45.09    | 1369189  | 21958.48 | 0%     |
| 写入(0数据)               | 1000   | 10分钟         | **sharding** | 134.68   | 4451103  | 7415.58  | 0%     |
| 写入(0数据)               | 1000   | 10分钟         | mysql        | 48.81    | 12275877 | 20448.65 | 0%     |
|                           |        |                |              |          |          |          |        |
| 写入(2千万数据)           | 10     | 10分钟         | **sharding** | 4.19     | 1418170  | 2363.70  | 0%     |
| 写入(2千万数据)           | 10     | 10分钟         | mysql        | 0.71     | 8144015  | 13573.95 | 0%     |
| 写入(2千万数据)           | 100    | 1分钟          | **sharding** | 15.25    | 389181   | 6485     | 0%     |
| 写入(2千万数据)           | 100    | 1分钟          | mysql        | 4.04     | 1453835  | 24224.93 | 0%     |
| 写入(2千万数据)           | 100    | 10分钟         | **sharding** | 14.81    | 4037436  | 6727.14  | 0%     |
| 写入(2千万数据)           | 100    | 10分钟         | mysql        | 4.44     | 13381488 | 22301.81 | 0%     |
| 写入(2千万数据)           | 500    | 1分钟          | **sharding** | 77.11    | 388110   | 6428     | 0%     |
| 写入(2千万数据)           | 500    | 1分钟          | mysql        | 20.02    | 1484831  | 24698.2  | 0%     |
| 写入(2千万数据)           | 500    | 10分钟         | **sharding** | 75.25    | 3981468  | 6634.5   | 0%     |
| 写入(2千万数据)           | 500    | 10分钟         | mysql        | 30.21    | 9908357  | 16510.13 | 0%     |
| 写入(2千万数据)           | 1000   | 1分钟          | **sharding** | 170      | 351169   | 5826     | 0%     |
| 写入(2千万数据)           | 1000   | 1分钟          | mysql        | 43.58    | 1369189  | 22730.41 | 0%     |
| 写入(2千万数据)           | 1000   | 10分钟         | **sharding** | 166.92   | 3594015  | 5983.75  | 0%     |
| 写入(2千万数据)           | 1000   | 10分钟         | mysql        | 71.99    | 8324239  | 13867.61 | 0%     |
|                           |        |                |              |          |          |          |        |
| 读写测试--写入(2千万数据) | 10     | 10分钟         | **sharding** | 7.65     | 269990   | 449.89   | 0%     |
| 读写测试--读取(2千万数据) | 10     | 10分钟         | **sharding** | 14.50    | 269988   | 453.34   | 0%     |
| 读写测试--写入(2千万数据) | 100    | 1分钟          | mysql        | 17.18    | 36531    | 61.02    | 0%     |
| 读写测试--读取(2千万数据) | 100    | 1分钟          | mysql        | 146.98   | 36541    | 60.82    | 0%     |
| 读写测试--写入(2千万数据) | 100    | 1分钟          | **sharding** | 17.34    | 194501   | 3240.82  | 0%     |
| 读写测试--读取(2千万数据) | 100    | 1分钟          | **sharding** | 13.19    | 194449   | 3269.75  | 0%     |
| 读写测试--写入(2千万数据) | 100    | 1分钟          | mysql        | 447.2    | 6172     | 101.97   | 0%     |
| 读写测试--读取(2千万数据) | 100    | 1分钟          | mysql        | 525.93   | 6228     | 101.93   | 0%     |
| 读写测试--写入(2千万数据) | 100    | 10分钟         | **sharding** | 19.94    | 1718355  | 2863.94  | 0%     |
| 读写测试--读取(2千万数据) | 100    | 10分钟         | **sharding** | 14.87    | 1718308  | 2872.07  | 0%     |
| 读写测试--写入(2千万数据) | 100    | 10分钟         | mysql        | 248.57   | 79167    | 132.09   | 0%     |
| 读写测试--读取(2千万数据) | 100    | 10分钟         | mysql        | 508.54   | 79240    | 131.95   | 0%     |
| 读写测试--写入(2千万数据) | 500    | 1分钟          | **sharding** | 94.92    | 177830   | 2955.12  | 0%     |
| 读写测试--读取(2千万数据) | 500    | 1分钟          | **sharding** | 72.81    | 177548   | 2984.3   | 0%     |
| 读写测试--写入(2千万数据) | 500    | 1分钟          | mysql        | 2912.65  | 5666     | 89.04    | 2.77%  |
| 读写测试--读取(2千万数据) | 500    | 1分钟          | mysql        | 2438.32  | 5924     | 92.35    | 1.74%  |
| 读写测试--写入(2千万数据) | 1000   | 1分钟          | **sharding** | 171.82   | 196138   | 3254.92  | 0%     |
| 读写测试--读取(2千万数据) | 1000   | 1分钟          | **sharding** | 132.97   | 195610   | 3282.6   | 0%     |
| 读写测试--写入(2千万数据) | 1000   | 1分钟          | mysql        | 6418.77  | 5036     | 76.93    | 22.72% |
| 读写测试--读取(2千万数据) | 1000   | 1分钟          | mysql        | 5510.44  | 5480     | 82.92    | 19.84% |
|                           |        |                |              |          |          |          |        |
| 有索引时更新(2千万数据)   | 10     | 10分钟         | **sharding** | 4.66     | 1279816  | 2133.04  | 0%     |
| 有索引时更新(2千万数据)   | 10     | 10分钟         | mysql        | 148.06   | 40483    | 67.47    | 0%     |
| 有索引时更新(2千万数据)   | 100    | 1分钟          | **sharding** | 48.59    | 123226   | 2031.02  | 0%     |
| 有索引时更新(2千万数据)   | 100    | 1分钟          | mysql        | 1175.22  | 5116     | 83.84    | 0%     |
| 有索引时更新(2千万数据)   | 100    | 10分钟         | **sharding** | 17.82    | 3358691  | 5597.4   | 0%     |
| 有索引时更新(2千万数据)   | 100    | 10分钟         | mysql        | 906.6    | 66195    | 110.09   | 0%     |
| 有索引时更新(2千万数据)   | 300    | 1分钟          | **sharding** | 110.56   | 161787   | 2665.49  | 0%     |
| 有索引时更新(2千万数据)   | 300    | 1分钟          | mysql        | 2256.35  | 8041     | 129.48   | 0.20%  |
| 有索引时更新(2千万数据)   | 500    | 1分钟          | **sharding** | 152.25   | 196094   | 3254.24  | 0%     |
| 有索引时更新(2千万数据)   | 500    | 1分钟          | mysql        | 3597.33  | 8445     | 133.98   | 2.49%  |
| 有索引时更新(2千万数据)   | 700    | 1分钟          | **sharding** | 218.05   | 191957   | 3188.71  | 0%     |
| 有索引时更新(2千万数据)   | 700    | 1分钟          | mysql        | 4193.3   | 10437    | 158.34   | 5.08%  |
| 有索引时更新(2千万数据)   | 1000   | 1分钟          | **sharding** | 294.33   | 205198   | 3322.45  | 0%     |
| 有索引时更新(2千万数据)   | 1000   | 1分钟          | mysql        | 5023.71  | 12633    | 177.65   | 9.42%  |
|                           |        |                |              |          |          |          |        |
| 无索引时更新(2千万数据)   | 10     | 跑完1次(2分钟) | **sharding** | 47905.12 | 17       | 0.18     | 0%     |
| 无索引时更新(2千万数据)   | 10     | 跑完1次(9分钟) | mysql        | 447395.4 | 10       | 0.02     | 20%    |
| 无索引时更新(2千万数据)   | 30     | 跑完1次(3分钟) | **sharding** | 179580.3 | 30       | 0.16     | 0%     |
| 无索引时更新(2千万数据)   | 30     | 跑完1次(5分钟) | mysql        | 893733   | 30       | 0.03     | 0%     |
| 无索引时更新(2千万数据)   | 50     | 跑完1次(6分钟) | **sharding** | 332501.4 | 50       | 0.14     | 0%     |
| 无索引时更新(2千万数据)   | 70     | 跑完1次(6分钟) | **sharding** | 199274.7 | 90       | 0.25     | 44.44% |
|                           |        |                |              |          |          |          |        |
| 有索引数据排序(2千万数据) | 10     | 10分钟         | **sharding** | 2.69     | 2211909  | 3686.62  | 0%     |
| 有索引数据排序(2千万数据) | 10     | 10分钟         | mysql        | 0.53     | 11011393 | 18352.81 | 0%     |
|                           |        |                |              |          |          |          |        |
| 无索引数据排序(2千万数据) | 10     | 10分钟         | **sharding** | 6121.13  | 985      | 1.63     | 0%     |
| 无索引数据排序(2千万数据) | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 扫描读取(2千万数据)       | 10     | 10分钟         | **sharding** | 5342.5   | 1127     | 1.86     | 0%     |
| 扫描读取(2千万数据)       | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 加索引扫描对比(2千万数据) | 10     | 10分钟         | **sharding** | 1.44     | 4098038  | 6830.27  | 0%     |
| 加索引扫描对比(2千万数据) | 10     | 10分钟         | mysql        | 0.39     | 14484545 | 24141.47 | 0%     |
|                           |        |                |              |          |          |          |        |
| 有索引时join(2千万数据)   | 10     | 10分钟         | **sharding** | 1.89     | 3139211  | 5232.15  | 0%     |
| 有索引时join(2千万数据)   | 10     | 10分钟         | mysql        | 0.43     | 13182104 | 21971.16 | 0%     |
|                           |        |                |              |          |          |          |        |
| 无索引时join(2千万数据)   | 1      | 74秒(1次)      | **sharding** | 74038    | 1        | 0.01     | 0%     |
| 无索引时join(2千万数据)   | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 有索引时分组(2千万数据)   | 10     | 10分钟         | **sharding** | 893.92   | 6711     | 11.17    | 0%     |
| 有索引时分组(2千万数据)   | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 无索引时分组(2千万数据)   | 1      | 9.5秒(1次)     | **sharding** | 95798    | 1        | 0.01     | 0%     |
| 无索引时分组(2千万数据)   | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 有索引时过滤(2千万数据)   | 10     | 10分钟         | **sharding** | 11962.75 | 505      | 0.83     | 0%     |
| 有索引时过滤(2千万数据)   | 10     | 10分钟         | mysql        | 38945.53 | 160      | 0.26     | 0%     |
|                           |        |                |              |          |          |          |        |
| 无索引时过滤(2千万数据)   | 1      | 117秒(1次)     | **sharding** | 117849   | 1        | 0.01     | 0%     |
| 无索引时过滤(2千万数据)   | 1      | 执行失败       | mysql        | 执行失败 | 执行失败 | 执行失败 |        |
|                           |        |                |              |          |          |          |        |
| 有索引最值查询(2千万数据) | 10     | 10分钟         | **sharding** | 1.42     | 2056257  | 3432.48  | 0%     |
| 有索引最值查询(2千万数据) | 10     | 10分钟         | mysql        | 0.36     | 7748430  | 12923.38 | 0%     |
| 有索引统计区间(2千万数据) | 10     | 10分钟         | **sharding** | 1.46     | 2056262  | 3427.2   | 0%     |
| 有索引统计区间(2千万数据) | 10     | 10分钟         | mysql        | 0.37     | 7748433  | 12914.57 | 0%     |
|                           |        |                |              |          |          |          |        |
| 无索引最值查询(2千万数据) | 1      | 90秒(1次)      | **sharding** | 90791    | 1        | 0.01     | 0%     |
| 无索引最值查询(2千万数据) | 1      | 8分钟(1次)     | mysql        | 494421   | 1        | 0        | 0%     |
| 无索引统计区间(2千万数据) | 1      | 111秒(1次)     | **sharding** | 111351   | 1        | 0.01     | 0%     |
| 无索引统计区间(2千万数据) | 1      | 10分钟(1次)    | mysql        | 628646   | 1        | 0        | 0%     |
|                           |        |                |              |          |          |          |        |
| 创建简单索引(2千万数据)   | 1      | 27秒(1次)      | **sharding** | 27527    | 1        | 0.04     | 0%     |
| 创建简单索引(2千万数据)   | 1      | 15分钟(1次)    | mysql        | 914210   | 1        | 0        | 0%     |
|                           |        |                |              |          |          |          |        |
| 创建联合索引(2千万数据)   | 1      | 40秒(1次)      | **sharding** | 40916    | 1        | 0.02     | 0%     |
| 创建联合索引(2千万数据)   | 1      | 15分钟(1次)    | mysql        | 943717   | 1        | 0        | 0%     |





