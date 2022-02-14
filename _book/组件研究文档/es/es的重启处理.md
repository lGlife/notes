**ES 节点离线并重启之后，发现本级上的分片已经再开始在平衡了，就会把本级上的所有分片删除，从新从其他集群上拉取，这样就会出现大量的数据迁移。占用超级多的网络资源。**

**之前已经讲过破解和安装x-pack的教程。本文涉及到的是安装成功之后重启服务的问题。ES集群在其中任何一个节点离线之后都会进行数据再平衡，就是把现有的数据进行重新分片,以达到高可用的目的。但是这样也会有很多问题，比如是因为网络抖动引起节点之间通信阻断，本来马上就能好的，可是就出现大量的数据迁移。**

**生产环境下建议关闭自动平衡。因为集群中集群的节点数是定的，所以分片也是固定的，如果是添加了新节点，就需要打开自动平衡功能，先把分片平衡了再关闭这个功能。**

## 停机维护时操作步骤

### 1、关闭分片分配

```
# 此操作会停止所有的分片动作，以及分片的数据移动。
PUT _cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "none"
  }
}
curl -X PUT -u elastic:elastic  "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "transient": {
    "cluster.routing.allocation.enable": "none"
  }
}
'
```

### 2、执行同步刷新

```
POST _flush/synced
curl -X POST -u elastic:elastic  "localhost:9200/_flush/synced?pretty"
```

### 3、关闭自动平衡(重要)

```
# 这一项很重要，如果配置了关闭分片分配，没有关闭自平衡，也会在打开分片分配之后出现集群自平衡
PUT /_cluster/settings?pretty
{
  "transient" : {
    "cluster.routing.rebalance.enable" : "none"
  }
}
curl -X PUT -u elastic:elastic "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "transient": {
    "cluster.routing.rebalance.enable": "none"
  }
}
'

# 设置以后需要手动验证一下
curl http://{host}:9200/_cluster/settings?pretty
curl -u elastic:elastic http://localhost:9200/_cluster/settings?pretty
```

### 4、延迟副本分片的分配

```
PUT /_all/_settings
{
  "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
  }
}
```

**上面的步骤都做完了，集群自平衡关闭了，分片功能关闭了，就可以停机维护服务**

### 5、重启之后

```
# 打开分片分配，就会把分片上数据不一致的进行同步，不会出现再平衡
PUT _cluster/settings
{
  "transient": {
    "cluster.routing.allocation.enable": "all"
  }
}

curl -X PUT -u elastic:elastic  "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "transient": {
    "cluster.routing.allocation.enable": "all"
  }
}
'
```

## 参考文档

- https://www.elastic.co/guide/en/elasticsearch/reference/5.4/shards-allocation.html#shards-allocation
- https://juejin.im/post/5ab5f53f518825556b6cbdea
- https://www.elastic.co/guide/en/elasticsearch/reference/5.4/rolling-upgrades.html



## 第一种 - 如何正确的关闭ES集群

- 第一步，禁止分片自动分布

```shell
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
```

- 第二步，执行同步刷新

```
POST _flush/synced
```

- 第三步，各节点逐个关闭

```
# 通过服务关闭
# sudo systemctl stop elasticsearch.service
# 发送TERM信号关闭进程
kill $(cat pid.txt)
```

## 如何启动ES集群

- 第一步，执行完操作后逐个启动节点

```
cd $ES_HOME/bin
./elasticsearch -d -p $ES_HOME/pid.txt
```

- 第二步，等待所有节点加入集群 查看集群状态是否为"green"

```
GET _cat/health

GET _cat/nodes
```

- 第三步，启用分片自动分布

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

- 第四步，等待集群可用 通过集群的状态和恢复进程监控集群是否可用

```
GET _cat/health

GET _cat/recovery

curl -X GET "localhost:9200/_cat/health?pretty"
curl -X GET "localhost:9200/_cat/recovery?pretty"
```



## 第二种 - 如何正确的关闭ES集群

**问题缘由：**javascript

在elasticsearch集群中，当集群发现某个节点关闭时，将延迟一分钟后（默认）再开始将该节点上的分片复制到集群中的其余节点，这可能涉及不少I / O。因为该节点不久将要从新启动，所以该I / O是没必要要的。您能够经过在关闭节点以前禁用副本分配来避免。html

**正确关闭方式：**java

**第一步：**node

**禁止分片自动分布**json

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
'
```

**第二部：**安全

**执行同步刷新**bash

```
curl -X POST "localhost:9200/_flush/synced?pretty"
```

注意：服务器

执行同步刷新时，请检查响应以确保没有失败。尽管请求自己仍返回200 OK状态，但在响应正文中列出了因为挂起索引操做而失败的同步刷新操做。若是失败，请从新发出请求。app

**第三部：**curl

**关闭全部节点**

```
若是您使用如下命令运行Elasticsearch systemd：
sudo systemctl stop elasticsearch.service

若是您正在使用SysV运行Elasticsearch init：
sudo -i service elasticsearch stop

若是您将Elasticsearch做为守护程序运行：
kill $(cat pid.txt)
```

关闭结束后能够执行你任何的更改

**正确重启方式：**

**第一步：**

**执行完操做后逐个启动节点**

```
cd $ES_HOME
./bin/elasticsearch -d -p $ES_HOME/pid.txt
```

**第二步：**

等全部节点启动完成后，能够经过执行以下请求查看集群状态：

```
curl -X GET "localhost:9200/_cat/health?pretty"
curl -X GET "localhost:9200/_cat/nodes?pretty"
```

状态分别有：`red`，`yellow`，`green`。

当节点加入集群时，它开始恢复本地存储的全部主分片。该[`_cat/health`](http://www.javashuo.com/link?url=https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-health.html)API最初将报告`status`中`red`，代表并不是全部的初级碎片已被分配。一旦节点恢复了其本地分片，集群`status`就会切换到 `yellow`，表示全部主分片都已恢复，但并不是全部副本分片都已分配。这是能够预期的，由于您还没有从新启用分配。将副本的分配延迟到全部节点都`yellow`可用以后，主服务器即可以将副本分配给已经具备本地分片副本的节点。

**第三步：**

**启用分片自动分布**

当全部节点都已加入集群并恢复了其主要分片后，可经过恢复`cluster.routing.allocation.enable`为其默认值来从新启用分配：

```
curl -X PUT "localhost:9200/_cluster/settings?pretty" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
'
```

从新启用分配后，集群便开始将副本分片分配给数据节点。此时，恢复索引和搜索是安全的，可是若是您能够等待直到成功分配了全部主分片和副本分片而且全部节点的状态为，集群就会恢复得更快`green`。

您可使用[`_cat/health`](http://www.javashuo.com/link?url=https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-health.html)和 [`_cat/recovery`](http://www.javashuo.com/link?url=https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-recovery.html)API 监视进度

```
curl -X GET "localhost:9200/_cat/health?pretty"
curl -X GET "localhost:9200/_cat/recovery?pretty"
```