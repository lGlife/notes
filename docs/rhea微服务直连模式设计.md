### rhea微服务直连模式

为了满足开发环境以及微小型项目在不使用注册中心zeus，rhea微服务模式下应用正常能相互访问\相互调式的需求，rhea框架里面需要设计直连的模式来满足

#### 微服务定义规范

为了适应直连的调式设计，我们定义了微服务rhea.xml中服务生产者的编写规范

定义服务生产者时，serviceName统一按照应用或者模块名称为前缀开头，同时已横杆拼接服务名称，比如：

```xml
<!--service name为：vesta_server-vestaTaskService-->
<service name="vesta_server-vestaTaskService" serviceinterface="com.powercloud.vesta.task.service.VestaTaskService"></service>

<!--service name为：power-monitor-alarmGatherService-->
<service name="power-monitor-alarmGatherService" serviceinterface="com.powercloud.moniter.alarm.service.AlarmGatherService" ></service>
<!--service name为：power-alarm-server-alarmPlatformService ，power-alarm-server为monitor里面的子模块-->
<service name="power-alarm-server-alarmPlatformService"				 serviceinterface="com.powercloud.alarm.api.service.AlarmPlatformService"></service>
```

#### 配置说明

```properties
# 注册中心连接通道是否关闭,默认为on，一旦设置为off，就会provider.mock.项目-应用 配置的节点清单来获取服务的访问地址
rhea.zeus.link.channel=off

# 配置服务的节点清单 
# key值为 ： provider.mock.项目-应用,也可以provider.mock.项目
# values值为：ip:port:deployName，deployName与应用名称一直，则可以不配置deployName；多个值则由逗号分隔, 
provider.mock.powercloud=172.18.40.40:20001
provider.mock.powercloud-apollo=172.18.100.1:8080:/
```

**服务节点清单配置详细说明**

1. 一个项目配置多个访问节点

```properties
provider.mock.powercloud=172.18.40.40:20001,172.18.40.42:20002
```

2. 按照应用配置访问节点

```properties
provider.mock.powercloud-apollo=172.18.40.40:20001
```

3. 应用的上下文与应用的标识不一致的情况配置

```properties
#apollo应用的部署上下文是/，那么最后就加上:/
provider.mock.powercloud-apollo=172.18.40.40:20001:/
```

