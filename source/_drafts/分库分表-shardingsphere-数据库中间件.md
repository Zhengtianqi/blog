title: 分库分表-shardingsphere 数据库中间件
author: ztq
tags:
  - 分库分表
categories:
  - 数据库
date: 2023-04-17 16:21:00
---
# 一、shardingsphere 数据库中间件介绍
ShardingSphere是一套开源的分布式数据库中间件解决方案组成的生态圈，它由Sharding-JDBC、Sharding-Proxy和Sharding-Sidecar这3款相互独立的产品组成。 他们均提供标准化的数据分片、读写分离、多数据副本、数据加密、影子库压测、分布式事务和数据库治理等功能，可适用于如Java同构、异构语言、容器、云原生等各种多样化的应用场景。 Sharding-JDBC最早是当当网内部使用的一款分库分表框架，到2017年的时候才开始对外开源，这几年在大量社区贡献者的不断迭代下，功能也逐渐完善，现已更名为ShardingSphere， 2020年4月16日正式成为Apache软件基金会的顶级项目，同时兼容多种数据库，通过可插拔架构，理想情况下，可以做到对业务代码无感知。
shardingsphere 特点
适用于任何基于Java的ORM框架，如：JPA, Hibernate, Mybatis, Spring JDBC Template或直接使用JDBC
基于任何第三方的数据库连接池，如：DBCP, C3P0, BoneCP, Druid, HikariCP等
支持任意实现JDBC规范的数据库。目前支持MySQL，Oracle，SQLServer和PostgreSQL
官网：https://shardingsphere.apache.org/index_zh.html
# 二、shardingsphere 数据库中间件使用
## 1、引入Maven依赖
```xml
    <!-- for spring boot -->
    <dependency>
        <groupId>io.shardingsphere</groupId>
        <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
        <version>${sharding-sphere.version}</version>
    </dependency>
    
    <!-- for spring namespace -->
    <dependency>
        <groupId>io.shardingsphere</groupId>
        <artifactId>sharding-jdbc-spring-namespace</artifactId>
        <version>${sharding-sphere.version}</version>
    </dependency>
```
## 2、数据分配
### （1）、基于Java编码的规则配置
Sharding-JDBC的分库分表通过规则配置描述，以下例子是根据user_id取模分库, 且根据order_id取模分表的两库两表的配置。
```java
@Configuration
public class DataSourceTableRuleConfig {
    // 配置真实数据源
    Map<String, DataSource> dataSourceMap = new HashMap<>();

    // 配置第一个数据源
    BasicDataSource dataSource1 = new BasicDataSource();
    dataSource1.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource1.setUrl("jdbc:mysql://localhost:3306/ds0");
    dataSource1.setUsername("root");
    dataSource1.setPassword("");
    dataSourceMap.put("ds0", dataSource1);

    // 配置第二个数据源
    BasicDataSource dataSource2 = new BasicDataSource();
    dataSource2.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource2.setUrl("jdbc:mysql://localhost:3306/ds1");
    dataSource2.setUsername("root");
    dataSource2.setPassword("");
    dataSourceMap.put("ds1", dataSource2);

    // 配置Order表规则
    TableRuleConfiguration orderTableRuleConfig = new TableRuleConfiguration();
    orderTableRuleConfig.setLogicTable("t_order");
    orderTableRuleConfig.setActualDataNodes("ds${0..1}.t_order${0..1}");
    
    // 配置分库 + 分表策略
    orderTableRuleConfig.setDatabaseShardingStrategyConfig(new InlineShardingStrategyConfiguration("user_id", "ds${user_id % 2}"));
    orderTableRuleConfig.setTableShardingStrategyConfig(new InlineShardingStrategyConfiguration("order_id", "t_order${order_id % 2}"));

    // 配置分片规则
    ShardingRuleConfiguration shardingRuleConfig = new ShardingRuleConfiguration();
    shardingRuleConfig.getTableRuleConfigs().add(orderTableRuleConfig);

    // 省略配置order_item表规则...
    // ...

    // 获取数据源对象
    DataSource dataSource = ShardingDataSourceFactory.createDataSource(dataSourceMap, shardingRuleConfig, new ConcurrentHashMap(), new Properties());
}
```
### （2）、基于Yaml的规则配置
```yaml
dataSources:
  ds0: !!org.apache.commons.dbcp.BasicDataSource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ds0
    username: root
    password: 
  ds1: !!org.apache.commons.dbcp.BasicDataSource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ds1
    username: root
    password: 

tables:
  t_order: 
    actualDataNodes: ds${0..1}.t_order${0..1}
    databaseStrategy: 
      inline:
        shardingColumn: user_id
        algorithmInlineExpression: ds${user_id % 2}
    tableStrategy: 
      inline:
        shardingColumn: order_id
        algorithmInlineExpression: t_order${order_id % 2}
  t_order_item: 
    actualDataNodes: ds${0..1}.t_order_item${0..1}
    databaseStrategy: 
      inline:
        shardingColumn: user_id
        algorithmInlineExpression: ds${user_id % 2}
    tableStrategy: 
      inline:
        shardingColumn: order_id
        algorithmInlineExpression: t_order_item${order_id % 2}
```
## 3、读写分离
### （1）、基于Java编码的规则配置
```java
@Configuration
public class DataSourceRuleConfig {
    // 配置真实数据源
    Map<String, DataSource> dataSourceMap = new HashMap<>();

    // 配置主库
    BasicDataSource masterDataSource = new BasicDataSource();
    masterDataSource.setDriverClassName("com.mysql.jdbc.Driver");
    masterDataSource.setUrl("jdbc:mysql://localhost:3306/ds_master");
    masterDataSource.setUsername("root");
    masterDataSource.setPassword("");
    dataSourceMap.put("ds_master", masterDataSource);

    // 配置第一个从库
    BasicDataSource slaveDataSource1 = new BasicDataSource();
    slaveDataSource1.setDriverClassName("com.mysql.jdbc.Driver");
    slaveDataSource1.setUrl("jdbc:mysql://localhost:3306/ds_slave0");
    slaveDataSource1.setUsername("root");
    slaveDataSource1.setPassword("");
    dataSourceMap.put("ds_slave0", slaveDataSource1);

    // 配置第二个从库
    BasicDataSource slaveDataSource2 = new BasicDataSource();
    slaveDataSource2.setDriverClassName("com.mysql.jdbc.Driver");
    slaveDataSource2.setUrl("jdbc:mysql://localhost:3306/ds_slave1");
    slaveDataSource2.setUsername("root");
    slaveDataSource2.setPassword("");
    dataSourceMap.put("ds_slave1", slaveDataSource2);

    // 配置读写分离规则
    MasterSlaveRuleConfiguration masterSlaveRuleConfig = new MasterSlaveRuleConfiguration("ds_master_slave", "ds_master", Arrays.asList("ds_slave0", "ds_slave1"));

    // 获取数据源对象
    DataSource dataSource = MasterSlaveDataSourceFactory.createDataSource(createDataSourceMap(), masterSlaveRuleConfig, new HashMap<String, Object>(), new Properties());
}
```
### （2）、基于Yaml的规则配置
```yaml
dataSources:
  ds_master: !!org.apache.commons.dbcp.BasicDataSource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ds_master
    username: root
    password:
  ds_slave0: !!org.apache.commons.dbcp.BasicDataSource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ds_slave0
    username: root
    password:
  ds_slave1: !!org.apache.commons.dbcp.BasicDataSource
    driverClassName: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ds_slave1
    username: root
    password:

masterSlaveRule:
  name: ds_ms
  masterDataSourceName: ds_master
  slaveDataSourceNames: [ds_slave0, ds_slave1]

  props:
    sql.show: true
  configMap:
    key1: value1
```
## 4、强制路由
ShardingSphere使用ThreadLocal管理分片键值进行Hint强制路由。可以通过编程的方式向HintManager中添加分片条件，该分片条件仅在当前线程内生效。
参考： https://blog.csdn.net/weixin_45142496/article/details/119895812
1.分片字段不存在SQL中、数据库表结构中，而存在于外部业务逻辑。因此，通过Hint实现外部指定分片结果进行数据操作。
2.强制在主库进行某些数据操作。
配置
使用hint进行强制数据分片，需要使用HintManager搭配分片策略配置共同使用。若DatabaseShardingStrategy配置了Hint分片算法，则可使用HintManager进行分库路由结果的注入。同理，若TableShardingStrategy配置了Hint分片算法，则同样可 使用HintManager进行分表路由结果的注入。所以使用Hint之前，需要配置Hint分片算法。
参考配置如下：
```yaml
shardingRule:
  tables:
   t_order:
        actualDataNodes: demo_ds_${0..1}.t_order_${0..1}
        databaseStrategy:
          hint:
            algorithmClassName: io.shardingsphere.userAlgo.HintAlgorithm
        tableStrategy:
          hint:
            algorithmClassName: io.shardingsphere.userAlgo.HintAlgorithm
  defaultTableStrategy:
    none:
  defaultKeyGeneratorClassName: io.shardingsphere.core.keygen.DefaultKeyGenerator
  props:
      sql.show: true
```
实例化
```java
HintManager hintManager = HintManager.getInstance();
```
添加分片键值
- 使用hintManager.addDatabaseShardingValue来添加数据源分片键值。
- 使用hintManager.addTableShardingValue来添加表分片键值。
分库不分表情况下，强制路由至某一个分库时，可使用hintManager.setDatabaseShardingValue方式添加分片。通过此方式添加分片键值后，将跳过SQL解析和改写阶段，从而提高整体执行效率。
清除分片键值
分片键值保存在ThreadLocal中，所以需要在操作结束时调用hintManager.close()来清除ThreadLocal中的内容。
hintManager实现了AutoCloseable接口，可推荐使用try with resource自动关闭。
```java
// Sharding database and table with using hintManager.
String sql = "SELECT * FROM t_order";
try (HintManager hintManager = HintManager.getInstance();
	 Connection conn = dataSource.getConnection();
	 PreparedStatement preparedStatement = conn.prepareStatement(sql)) {
	hintManager.addDatabaseShardingValue("t_order", 1);
	hintManager.addTableShardingValue("t_order", 2);
	try (ResultSet rs = preparedStatement.executeQuery()) {
		while (rs.next()) {
			// ...
		}
	}
}

// Sharding database without sharding table and routing to only one database with using hintManger.
String sql = "SELECT * FROM t_order";
try (HintManager hintManager = HintManager.getInstance();
	 Connection conn = dataSource.getConnection();
	 PreparedStatement preparedStatement = conn.prepareStatement(sql)) {
	hintManager.setDatabaseShardingValue(3);
	try (ResultSet rs = preparedStatement.executeQuery()) {
		while (rs.next()) {
			// ...
		}
	}
}
```
基于暗示(Hint)的强制主库路由
实例化
与基于暗示(Hint)的数据分片相同。
设置主库路由
使用hintManager.setMasterRouteOnly设置主库路由。
清除分片键值
与基于暗示(Hint)的数据分片相同。
完整代码示例
```java
String sql = "SELECT * FROM t_order";
try (
        HintManager hintManager = HintManager.getInstance();
        Connection conn = dataSource.getConnection();
        PreparedStatement preparedStatement = conn.prepareStatement(sql)) {
        hintManager.setMasterRouteOnly();
        try (ResultSet rs = preparedStatement.executeQuery()) {
            while (rs.next()) {
                // ...
            }
        }
}
```

