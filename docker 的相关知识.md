### docker 的部署 

这里建议安装在CentOS7.x以上的版本

（1）yum 包更新到最新

```
sudo yum update
```

（2）安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

（3）设置yum源为阿里云，如果是公司的就配置公司的yum源的地址即可

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

（4）安装docker

```
sudo yum install docker-ce
```

（5）安装后查看docker版本

```
docker -v
```



### docker的镜像仓库地址配置

daemon.json 是对 Docker Engine 进行配置和调整

daemon.json的配置文件说明：https://www.jianshu.com/p/c7c7dc24b9e3

```shell
[root@mirrors docker]# vi daemon.json
{
 "data-root":"/data/docker_data",
 "log-driver":"json-file",
 "log-opts": {"max-size":"500m", "max-file":"5"},
 "insecure-registries": ["harbor.core.powercloud.com"],
 "bip": "10.10.10.1/24",
 "default-address-pools":
  [
    {"base":"10.10.0.0/16","size":25}
  ]
}

再在hosts里面添加私有仓库host记录
172.18.40.23 harbor.core.powercloud.com
```

