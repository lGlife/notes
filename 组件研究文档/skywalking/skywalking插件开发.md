https://www.itmuch.com/skywalking/write-plugin/

官方的插件开发指南：https://skyapm.github.io/document-cn-translation-of-skywalking/zh/6.2.0/guides/Java-Plugin-Development-Guide.html

插件开发案例：https://blog.csdn.net/a17816876003/article/details/113703991



https://asclouds.com/ch12/guides/Java-Plugin-Development-Guide.html

### skywalking 的插件开发

在skywalking的插件包里面新增一个所需的插件，新增一个maven工程

目录：apm-sniffer  -->  apm-sdk-plugin 里面

工程的结构

![image-20220113165457036](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\skywalking插件开发.assets\image-20220113165457036.png)



### 插件开发的三个基本要素

+ 编写 *Instrumentation类，定义拦截要素，继承```ClassInstanceMethodsEnhancePluginDefine```

1. 匹配类名 enhanceClass()
2. 匹配构造方法 getConstructorsInterceptPoints()
3. 匹配方法 getInstanceMethodsInterceptPoints()

+ 编写* Interceptor 类，定义拦截的实际处理方法，一般实现 ```InstanceMethodsAroundInterceptor```

1. 方法执行前 beforeMethod()

2. 方法执行后 afterMethod()

3. 方法发生异常 handleMethodException()

   经典案例：![image-20220113172604770](D:\A-资料文档\E-文档编写笔记文件夹\typora笔记\skywalking插件开发.assets\image-20220113172604770.png)

   SpanLayer.asHttp(span)

+ resources 文件夹下面定义skywalking-plugin.def插件说明文件，告知探针,插件类型\插件定义的类的全类名





### 插件开发之后的打包

apm-sdk-plugin.pom加入自己的maven工程结构中

```mvn clean install```

构建完成后，到target目录中，找到JAR包(非 `origin-xxx.jar` 、非`xxx-source.jar` )，扔到 `agent/plugins` 目录里面去，即可启动