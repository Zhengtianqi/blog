title: maven梳理
tags:

  - maven
categories:
  - 软件管理
author: 郑天祺
date: 2019-08-28 17:01:00
---
# Maven使用

## 1.两个操作：

###    (1) 参数设置：

​     Linux：在~/.bash_profile文件中添加

```java
export MAVEN_OPTS="-Xms512m -Xmx1024m"
```

​    （此设置是为了maven执行java时分配给大点的内存，解决容易引起maven导包或插件时卡顿）
​     Windows：如下图
​		<img src="/img/maven配置.png">
​        

### 	(2) 用户配置：

把MAVEN_HOME/conf/seettings.xml  cp 到 ~/.m2/下，在.m2下的settings.xml中所作的配置就是用户级别的配置，而直接编辑MAVEN_HOME/conf/seettings.xml所作的配置是全局的配置

```java
上传到私服的流程：
  a.  加入打包插件
  b. mvn clean package // 加上clean 会清空target，然后再生成新的包。。。
  c.mvn source:jar  // 生成源码包
  d.mvn deploy // 上传私服，别忘升级版本哦~~~
2.idea和eclipse导入时不同： 
idea是project下的module  eclipse是workspace下的project
  idea导入maven项目  https://blog.csdn.net/weixin_37909363/article/details/80915509  
```
## 2. maven的命令：

```java
maven常用命令

创建maven项目：mvn archetype:create
指定 group： -DgroupId=packageName
指定 artifact：-DartifactId=projectName
创建web项目：-DarchetypeArtifactId=maven-archetype-webapp 
创建maven项目：mvn archetype:generate
验证项目是否正确：mvn validate
maven 打包：mvn package
只打jar包：mvn jar:jar
生成源码jar包：mvn source:jar
产生应用需要的任何额外的源代码：mvn generate-sources
编译源代码： mvn compile
编译测试代码：mvn test-compile
运行测试：mvn test
运行检查：mvn verify
清理maven项目：mvn clean  该操作会清空当前目录的target文件夹
生成eclipse项目：mvn eclipse:eclipse
清理eclipse配置：mvn eclipse:clean
生成idea项目：mvn idea:idea
安装项目到本地仓库：mvn install
发布项目到远程仓库：mvn:deploy
在集成测试可以运行的环境中处理和发布包：mvn integration-test
显示maven依赖树：mvn dependency:tree
显示maven依赖列表：mvn dependency:list
下载依赖包的源码：mvn dependency:sources
安装本地jar到本地仓库：mvn install:install-file -DgroupId=packageName -DartifactId=projectName -Dversion=version -Dpackaging=jar -Dfile=path
    WEB
启动tomcat：mvn tomcat:run
启动jetty：mvn jetty:run
运行打包部署：mvn tomcat:deploy
撤销部署：mvn tomcat:undeploy
启动web应用：mvn tomcat:start
停止web应用：mvn tomcat:stop
重新部署：mvn tomcat:redeploy
部署展开的war文件：mvn war:exploded tomcat:exploded
    maven 命令的格式为 mvn [plugin-name]:[goal-name]，可以接受的参数如下。
-D 指定参数，如 -Dmaven.test.skip=true 跳过单元测试；
-P 指定 Profile 配置，可以用于区分环境；
-e 显示maven运行出错的信息；
-o 离线执行命令,即不去远程仓库更新包；
-X 显示maven允许的debug信息；
-U 强制去远程更新snapshot的插件或依赖，默认每天只更新一次。
```

