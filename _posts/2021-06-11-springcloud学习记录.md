---
layout: post
title:  "springcloud学习笔记"
date:   2021-03-01 21:03:36 +0530
categories: 微服务
---
好记性不如烂笔头

@[TOC](springcloud学习 笔记)
### 01_什么是微服务

#### （1）微服务概念

> In short, the microservice architectural style is an approach to developing a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API. These services are built around business capabilities and independently deployable by fully automated deployment machinery. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies.——James Lewis and Martin Fowler (2014)

 - 微服务是一种架构风格
 - 一个应用拆分为一组小型服务
 - 每个服务运行在自己的进程内，也就是可独立部署和升级
 - 服务之间使用轻量级HTTP交互
 - 服务围绕业务功能拆分
 - 可以由全自动部署机制独立部署
 - 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术
#### （2）微服务技术架构
 - 服务调用
 - 服务降级与服务熔断
 - 服务注册与发现
 - 负载均衡
 - 服务消息队列
 - 服务网关
 - 配置中心管理
 - 自动化构建部署
 - 服务监控
 - 全链路跟踪
 - 服务定时任务
 - 调度操作
#### （3）springcloud

> SpringCloud=分布式微服务架构的站式解决方案，是多种微服务架构落地技术的集合体，俗称微服务全家桶

![springcloud全家桶](https://img-blog.csdnimg.cn/20210714110213839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDM0MTI1,size_16,color_FFFFFF,t_70#pic_center)
springcloud的技术栈
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210714110358372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDM0MTI1,size_16,color_FFFFFF,t_70#pic_center)

springcloud官网：[springcloud官网](https://spring.io/projects/spring-cloud)
springcloud中文文档: [springcloud中文文档](https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md)
#### （4）springcloud组件说明
cloud组件很多都已经停更

 - 停更引发的“升级惨案
  	
	 - 停更不停用
	 - 被动修复bugs
	 - 不再接受合并请求
	 - 不再发布新版本
 - Cloud升级
	- 服务注册中心
		- × Eureka（很多老项目还在使用）
		- √ Zookeeper（用过）
		- √ Consul（没用过）
		- √ Nacos（阿里出品，推荐使用）  
	
	 - 服务调用
	 	- √ Ribbon
	 	- √ LoadBalancer（实现负载均衡）
	 
	  - 服务调用2
	 	- × Feign
	 	- √ OpenFeign（最多使用）
		
	 - 服务降级
	 	- × Hystrix（俗称豪猪哥，老项目有使用的）
	 	- √ resilience4j
	 	- √ sentienl（阿里出品，感觉最好用）	
	 - 服务网关
	 	- × Zuul
	 	- ! Zuul2
	 	- √ gateway
	 - 服务配置
	 	- × Config（老项目有使用的）
	 	- √ Nacos	（是真的好用）
### 02_工程Project空间新建
#### （1）项目结构
![项目结构](https://img-blog.csdnimg.cn/20210714133752345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDM0MTI1,size_16,color_FFFFFF,t_70)
约定 > 配置 > 编码

创建微服务cloud整体聚合父工程Project，有8个关键步骤：
	

 1. New Project - maven工程 - create from archetype: maven-archetype-site
 2. 聚合总父工程名字
 3. Maven选版本
 4. 工程名字
 5. 字符编码 - Settings - File encoding
 6. 注解生效激活 - Settings - Annotation Processors
 7. Java编译版本选8
 8. File Type过滤 - Settings - File Type
#### （1）父工程pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>  

    <groupId>com.lun</groupId>
    <artifactId>LearnCloud</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging><!-- 这里添加，注意不是jar或war -->
    
    <!-- 统一管理jar包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>5.1.47</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>
    
    <!-- 子模块继承之后，提供作用：
		锁定版本+子modlue不用写groupId和version -->
    <dependencyManagement>
        <dependencies>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

```
#### （2）复习DependencyManagement和Dependencies
Maven使用`dependencyManagement`元素来提供了一种管理依赖版本号的方式。

通常会在一个组织或者项目的最顶层的父POM中看到`dependencyManagement`元素。

使用pom.xml中的`dependencyManagement`元素能让所有在子项目中引用个依赖而不用显式的列出版本量。

Maven会沿着父子层次向上走，直到找到一个拥有`dependencyManagement`元素的项目，然后它就会使用这个`dependencyManagement`元素中指定的版本号。

```html
<dependencyManagement>
    <dependencies>
        <dependency>
        <groupId>mysq1</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.2</version>
        </dependency>
    <dependencies>
</dependencyManagement>

```
然后在子项目里就可以添加`mysql-connector`时可以不指定版本号，例如：

```html
<dependencies>
    <dependency>
    <groupId>mysq1</groupId>
    <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>

```
这样做的好处就是：如果有多个子项目都引用同一样依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或切换到另一个版本时，只需要在顶层父容器里更新，而不需要一个一个子项目的修改；另外如果某个子项目需要另外的一个版本，只需要声明version就可。

 1. `dependencyManagement`里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。
 2. 如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项,并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom。
 3. 如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。
#### （3）支付模块构建
创建微服务模块套路：
 1. 建Module
 2. 改POM
 3. 写YML
 4. 主启动
 5. 业务类

![项目关系](https://img-blog.csdnimg.cn/20210714135732397.png)

创建cloud-provider-payment8001微服务提供者支付Module模块：
详细过程参考本人github中的项目：[cloud-provider-payment8001](https://github.com/Jizehan/springcloud/tree/main/cloud-provider-payment)
SQL:

```html
CREATE TABLE `payment`(
	`id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'ID',
    `serial` varchar(200) DEFAULT '',
	PRIMARY KEY (id)
)ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4

```
数据随便填写两条即可。
测试：启动项目，浏览器 - http://localhost:8001/payment/get/1

ps：
**实现项目的热部署**

 1. Adding devtools to your project
 	

```html
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>

```

 2. Adding plugin to your pom.xml
 	下段配置复制到聚合父类总工程的pom.xml
 	

```html
<build>
    <!--
	<finalName>你的工程名</finalName>（单一工程时添加）
    -->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>

```

 3. Enabling automatic build
 	

> File -> Settings(New Project Settings->Settings for New Projects) ->Complier

 4. Update the value of
 	键入Ctrl + Shift + Alt + / ，打开Registry，勾选：
 	
	 - compiler.automake.allow.when.app.running
 	- actionSystem.assertFocusAccessFromEdt
 5. 重启idea

#### （4）消费者订单模块
创建名为cloud-consumer-order80的maven工程。
参考：[消费者订单模块](https://github.com/Jizehan/springcloud/tree/main/cloud-consumer-order80)
测试:

 - 运行cloud-consumer-order80与cloud-provider-payment8001两工程
 - 浏览器 - http://localhost/consumer/payment/get/1

**RestTemplate**

RestTemplate提供了多种便捷访问远程Http服务的方法，是一种简单便捷的访问restful服务模板类，是Spring提供的用于访问Rest服务的客户端模板工具集。
使用：
	

 - 使用restTemplate访问restful接口非常的简单粗暴无脑。
 - `(url, requestMap, ResponseBean.class)`这三个参数分别代表。
 - REST请求地址、请求参数、HTTP响应转换被转换成的对象类型。
 ps：
 	其实http请求的工具非常的多，我最近使用了hutool中的http请求工具也非常的好用，相比于restTemplate，它不用写配置类，更加人性化。


### 03_Eureka基础知识
#### （1）**什么是服务治理**

Spring Cloud封装了`Netflix` 公司开发的`Eureka`模块来实现服务治理

在传统的RPC远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

#### （2）**什么是服务注册与发现**

`Eureka`采用了CS的设计架构，Eureka Sever作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的客户端连接到 Eureka Server并维持心跳连接。这样系统的维护人员就可以通过Eureka Server来监控系统中各个微服务是否正常运行。

在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息比如服务地址通讯地址等以别名方式注册到注册中心上。另一方(消费者服务提供者)，以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用RPC远程调用框架核心设计思想:在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)。在任何RPC远程框架中，都会有一个注册中心存放服务地址相关信息(接口地址)。
![eureka和dubbo的架构对比](https://img-blog.csdnimg.cn/20210714142911767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDM0MTI1,size_16,color_FFFFFF,t_70)
#### （3）**Eureka包含两个组件:Eureka Server和Eureka Client**

**Eureka Server提供服务注册服务**

各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。

**EurekaClient通过注册中心进行访问**

它是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒)

#### （4）EurekaServer服务端安装

IDEA生成eurekaServer端服务注册中心，类似物业公司
创建名为cloud-eureka-server7001的Maven工程，参考：[cloud-eureka-server7001](https://github.com/Jizehan/springcloud/tree/main/cloud-eureka-server7001)
**测试：**
测试运行`EurekaMain7001`，浏览器输入`http://localhost:7001`/回车，会查看到Spring Eureka服务主页。
