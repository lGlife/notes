# spring boot 应用调优



##  内置tomcat参数优化

```yml
server:
  tomcat:
    uri-encoding: UTF-8
    #最小空闲线程数,默认10, 适当增大一些，以便应对突然增长的访问量
    min-spare-threads: 100
    #最大线程数,maxThreads默认200,线程数的经验值为：1核2g内存为200，线程数经验值200；4核8g内存，线程数经验值800,以此类推
    max-threads: 1000
    #最大链接数
    max-connections: 20000
    #当tomcat启动线程达到最大时，允许的最大排队请求个数,默认值为100
    accept-count: 1000
    #请求头最大长度kb
    max-http-header-size: 1048576
    #请请求体最大长度kb
    #max-http-post-size: 2097152
  #链接建立超时时间
  connection-timeout: 12000
```

注：根据应用系统的实际运行环境设置

参考案例链接：https://www.cnblogs.com/crazymakercircle/p/11748214.html

##  运行jvm参数优化
```
-XX:MetaspaceSize=128m （元空间默认大小,建议为物理内存的1/32）
-XX:MaxMetaspaceSize=128m （元空间最大大小,建议为物理内存的1/32）
-Xms1024m （初始堆大小,建议为物理内存的1/4）
-Xmx1024m （最大堆大小,建议为物理内存的1/4）
-Xmn256m （新生代大小,一般设置为Xmx的3、4分之一）
-Xss256k （棧最大深度大小,每个线程的堆栈大小）
-XX:SurvivorRatio=8 （新生代分区比例 8:2,则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10）
-XX:+UseConcMarkSweepGC （指定使用的垃圾收集器，这里使用CMS收集器,设置并发收集器）

内存溢出只能是给java命令增加参数
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=${目录}
```

案例：
```
java -jar -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=128m -Xms1024m -Xmx1024m -Xmn256m -Xss256k -XX:SurvivorRatio=8 -XX:+UseConcMarkSweepGC -XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/app/log/
```

注：根据应用服务器的资源进行配置
一、服务器资源：4C 8G
```
java -jar -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m -Xms2048m -Xmx2048m -Xmn512m -Xss256k -XX:SurvivorRatio=8 -XX:+UseConcMarkSweepGC -XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/app/log/
```

二、服务器资源：8C 16G
```
java -jar -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=512m -Xms4096m -Xmx4096m -Xmn1024m -Xss256k -XX:SurvivorRatio=8 -XX:+UseConcMarkSweepGC -XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/app/log/
```

## 链接mysql配置参数优化

国家医保hsaf框架使用的是 druid数据源

durid 常用配置解释
```
  1.name:配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。如果没有配置，将会生成一个名字，
  格式是："DataSource-" + System.identityHashCode(this). 另外配置此属性至少在1.0.5版本中是不起作用的，强行设置name会出错.
  2.initialSize 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时 
  3.maxActive 最大连接池数量
  4.minIdle 最小连接池数量
  5.maxWait 获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，
  并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。
  6.poolPreparedStatements 是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。
  7.maxPoolPreparedStatementPerConnectionSize:要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。
  在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
  8.validationQuery 用来检测连接是否有效的sql，要求是一个查询语句，常用select 'x'。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会起作用。
  9.validationQueryTimeout 单位：秒，检测连接是否有效的超时时间。底层调用jdbc Statement对象的void setQueryTimeout(int seconds)方法
  10.testOnBorrow 申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
  11.testOnReturn 归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。
  12 testWhileIdle 建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，
  如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
  13 keepAlive 连接池中的minIdle数量以内的连接，空闲时间超过minEvictableIdleTimeMillis，则会执行keepAlive操作。(1.0.28version)
  14 timeBetweenEvictionRunsMillis 有两个含义(1.0.14 version)：
     1) Destroy  线程会检测连接的间隔时间，如果连接空闲时间大于等于minEvictableIdleTimeMillis则关闭物理连接。
     2) testWhileIdle的判断依据，详细看testWhileIdle属性的说明
  15 numTestsPerEvictionRun 不再使用，一个DruidDataSource只支持一个EvictionRun (1.0.14 version)
  16 minEvictableIdleTimeMillis 连接保持空闲而不被驱逐的最小时间
  17 connectionInitSqls 物理连接初始化的时候执行的sql
  18 exceptionSorter 当数据库抛出一些不可恢复的异常时，抛弃连接
  19 filters  属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：
             监控统计用的filter:stat
             日志用的filter:log4j
             防御sql注入的filter:wallfilters 
			 
  20 proxyFilters 类型是List<com.alibaba.druid.filter.Filter>，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系

```

推荐配置
```
# 配置初始化大小、最小、最大
initialSize = "10" 
minIdle = "10" 
maxActive = "100" 
# 配置从连接池获取连接等待超时的时间
maxWait = "10000"     
# 配置间隔多久启动一次DestroyThread，对连接池内的连接才进行一次检测，单位是毫秒。检测时:1.如果连接空闲并且超过minIdle以外的连接，如果空闲时间超过minEvictableIdleTimeMillis设置的值则直接物理关闭。2.在minIdle以内的不处理。
timeBetweenEvictionRunsMillis = "600000" 
# 配置一个连接在池中最大空闲时间，单位是毫秒
minEvictableIdleTimeMillis = "300000" 
# 设置从连接池获取连接时是否检查连接有效性，true时，每次都检查;false时，不检查
testOnBorrow = "false" 
# 设置往连接池归还连接时是否检查连接有效性，true时，每次都检查;false时，不检查
testOnReturn = "false" 
# 设置从连接池获取连接时是否检查连接有效性，true时，如果连接空闲时间超过minEvictableIdleTimeMillis进行检查，否则不检查;false时，不检查
testWhileIdle = "true" 
# 检验连接是否有效的查询语句。如果数据库Driver支持ping()方法，则优先使用ping()方法进行检查，否则使用validationQuery查询进行检查。(Oracle jdbc Driver目前不支持ping方法)
validationQuery = "select 1 from dual" 
# 单位：秒，检测连接是否有效的超时时间。底层调用jdbc Statement对象的void setQueryTimeout(int seconds)方法
validationQueryTimeout = "1"      
# 打开后，增强timeBetweenEvictionRunsMillis的周期性连接检查，minIdle内的空闲连接，每次检查强制验证连接有效性。
keepAlive = "true"
# 连接泄露检查，打开removeAbandoned功能 , 连接从连接池借出后，长时间不归还，将触发强制回连接。回收周期随timeBetweenEvictionRunsMillis进行，如果连接为从连接池借出状态，并且未执行任何sql，并且从借出时间起已超过removeAbandonedTimeout时间，则强制归还连接到连接池中。
removeAbandoned = "true"  
# 超时时间，秒
removeAbandonedTimeout = "80"
# 打开PSCache，并且指定每个连接上PSCache的大小，Oracle等支持游标的数据库，打开此开关，会以数量级提升性能，具体查阅PSCache相关资料
poolPreparedStatements = "true" 
maxPoolPreparedStatementPerConnectionSize"    = "20"    

```

## 链接kafka配置参数优化

producer
```
#################producer的配置参数#################
#procedure要求leader在考虑完成请求之前收到的确认数，用于控制发送记录在服务端的持久化，0:表示producer无需等待leader的确认;1:代表需要leader确认写入它的本地log并立即确认;-1(all):代表所有的备份都完成后确认
acks=all
#如果该值大于零时，表示启用重试失败的发送次数
retries=1
#批量发送的最大容量,每当多个记录被发送到同一分区时，生产者将尝试将记录一起批量处理为更少的请求，(batch 越小，producer的吞吐量越低，越大，吞吐量越大)
batch.size=1
#指定了生产者在每次发送消息的时间间隔(为了减少了网络IO，提升了整体的TPS。假设设置linger.ms=5，表示producer请求可能会延时5ms才会被发送)
linger.ms=1
#生产者可用于缓冲等待发送到服务器的记录的内存总字节数，默认值为33554432
buffer.memory=33554432
#producer压缩器，目前支持none（不压缩），gzip，snappy和lz4
compression.type=lz4
```

consumer

```
#################consumer的配置参数#################
#是否后台自动提交偏移量
enable.auto.commit=true
#消费者偏移自动提交给Kafka的频率（以毫秒为单位)
auto.commit.interval.ms=1000
#等待来自followers的确认的最大时间，默认30000
session.timeout.ms=25000
#当没有初始偏移量或当前偏移量不存在时设置消费的起始点(earliest、latest、none三个值可选择)earliest：指定从最早的位移开始消费 。 注意这里最早的位移不一定就是 0 。latest：指定从最新处位移开始消费 。none ：指定如果未发现位移信息或位移越界，则抛出异常。笔者在实际使用过程中几
auto.offset.reset=earliest
```

## 链接redis配置参数优化

参数详解
```
参数名：maxIdle
含义：资源池允许最大的空闲连接数 【默认值：8】
使用建议：建议跟maxTotal设置的值一样，这样可以减少创建新连接的开销

参数名：minIdle
含义：资源池确保最少空闲连接数 【默认值：0】
使用建议：建议第一次开启的时候预热（初始化一个值），减少第一次启动后的新连接开销

参数名：maxWaitMillis
含义：当资源池连接用尽后，调用者最大等待时间（单位为毫秒） 【默认-1，表示永不超时】
使用建议：不建议使用默认值，再高并发环境下，获取资源不能hand在一个没有超时时间的地方，具体设置根据实际场景 如设置1000即为等待1秒。

参数名：testOnBorrow、testOnReturn
含义：这两个参数是说，客户端向连接池借用或归还时，是否会在内部进行有效性检测（ping），无效的资源将被移除 【默认值：false】
使用建议：建议false，在高并发场景下，因为这样无形给每次增加了两次ping操作，对QPS有影响，如果不是高并发环境，可以考虑开启，或者自己来检测

参数名：testWhileIdle
含义：如果为true，表示有一个idle object evitor线程对idle object进行扫描，如果validate失败，此object会被从pool中drop掉；这一项只有在timeBetweenEvictionRunsMillis大于0时才有意义
使用建议：建议false，失效连接主要通过testWhileIdle保证，如果获取到了不可用的数据库连接，一般由应用处理异常

参数名：timeBetweenEvictionRunsMillis
含义：表示idle object evitor两次扫描之间要sleep的毫秒数；

参数名：numTestsPerEvictionRun
含义：表示idle object evitor每次扫描的最多的对象数；

参数名：minEvictableIdleTimeMillis
含义：表示一个对象至少停留在idle状态的最短时间，然后才能被idle object evitor扫描并驱逐；这一项只有在timeBetweenEvictionRunsMillis大于0时才有意义；
```

参数配置案例
```
#最大活动对象数     
redis.pool.maxTotal=1000    
#最大能够保持idel状态的对象数      
redis.pool.maxIdle=100  
#最小能够保持idel状态的对象数   
redis.pool.minIdle=50    
#当池内没有返回对象时，最大等待时间    
redis.pool.maxWaitMillis=10000    
#当调用borrow Object方法时，是否进行有效性检查    
redis.pool.testOnBorrow=true    
#当调用return Object方法时，是否进行有效性检查    
redis.pool.testOnReturn=true  
#向调用者输出“链接”对象时，是否检测它的空闲超时；  
redis.pool.testWhileIdle=true  
#“空闲链接”检测线程，检测的周期，毫秒数。如果为负值，表示不运行“检测线程”。默认为-1. 
redis.pool.timeBetweenEvictionRunsMillis=30000  
redis.pool.minEvictableIdleTimeMills=60000
# 对于“空闲链接”检测线程而言，每次检测的链接资源的个数。默认为3.  
redis.pool.numTestsPerEvictionRun=-1 


```