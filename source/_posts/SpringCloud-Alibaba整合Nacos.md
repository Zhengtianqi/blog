title: SpringCloud-Alibaba整合Nacos配置中心
author: 郑天祺
tags:
  - nacos-config
  - SpringCloud
categories:
  - SpringCloud
date: 2019-11-27 17:53:00
---

在 Nacos Spring Cloud 中，dataId 的完整格式如下：${prefix}-${spring.profile.active}.${file-extension}

​	${prefix} 为dataId的前缀，对应于Client端配置 spring.cloud.nacos.config.prefix 的值，如未配置，则默认对应Client端 spring.application.name 配置项的值。

​	${file-extension} 为配置内容的数据格式，可以通过配置项 spring.cloud.nacos.config.file-extension 来配置。目前只支持 properties 和 yaml 类型。

​	${spring.profile.active} 为为当前环境对应的 profile，如为空，则变为${prefix}-${spring.profile.active}.${file-extension}形式。

建议${prefix}采用类似 package.class的命名规则保证全局唯一性，class 部分建议是配置的业务含义。

全部字符小写。只允许英文字符和 4 种特殊字符（"."、":"、"-"、"_"），不超过 256 字节。

一、nacos配置

![image-20191127175944755](/img/nacos-springCloud1.png)

![image-20191127180038072](/img/nacos-springCloud2.png)

二、配置文件

创建配置文件  bootstrap.yml，并添加基础配置信息：

```java
spring:
  application:
    name: nacos-config-example
  cloud:
    nacos:
      config:
        server-addr: http://192.168.101.147:8848/
        # config external configuration
        ext-config:
          # 1、Data Id (nacos-config-example.mysql-config.properties) 在默认的组 DEFAULT_GROUP,不支持配置的动态刷新
          - data-id: mysql-config.properties
          # 2、Data Id (nacos-config-example.es-config.properties) 不在默认的组，支持动态刷新
          - data-id: es-config.properties
            group: GLOBALE_GROUP
            refresh: true
        encode: utf-8

```

三、使用变量

```java
    @Value("jdbc-name")
    private String jdbcName;
    @Value("jdbc-password")
    private String jdbcPassword;

    @Value("es-name")
    private String esName;
    @Value("es-password")
    private String esPassword;
```

