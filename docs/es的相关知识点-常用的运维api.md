### 常用api整理



curl -u elastic:elastic http://192.168.0.108:9200/_cat/nodes?v



curl -u elastic:elastic http://192.168.0.108:9200/_cluster/health?pretty



设置最大分片数

curl -u elastic:elastic -XPUT -H "Content-Type:application/json" -d '{"persistent":{"cluster":{"max_shards_per_node":100000}}}' 'http://192.168.0.108:9200/_cluster/settings'

查询集群的配置信息

curl -u elastic:elastic http://192.168.0.108:9200/_cluster/settings?pretty

查询索引的配置信息

curl -u elastic:elastic http://192.168.0.108:9200/es_index_phc_item_info_1412681997799718913_12/_settings?pretty



常用的命令

https://blog.csdn.net/bo_wei/article/details/120564016



集群es的监控，indices可以过滤属性，比如：jvm

curl -u elastic:elastic http://192.168.0.108:9200/_nodes/stats/indices?pretty

curl -u elastic:elastic http://192.168.0.108:9200/_nodes/stats/jvm?pretty

curl -u elastic:elastic http://192.168.0.108:9200/_nodes/stats?pretty

curl -u elastic:elastic http://192.168.0.108:9200/_cluster/stats?pretty

参考;https://blog.csdn.net/u010824591/article/details/78614505

监控指标解释：

https://blog.csdn.net/prestigeding/article/details/89815143

https://www.cnblogs.com/zsql/p/15344600.html





查询日志数据

func_id 为字段一样可以查询2304

curl -XGET -u'elastic:HX@es$log&2021'  http://127.0.0.1:9200/power-b-powercloud_power-csb_bizmoniter_info-20211223/_search?q=func_id:2304



