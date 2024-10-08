# 微服务和Spring Cloud Alibaba介绍

## 1、微服务介绍

### 1.1 系统架构演变

随着互联网的发展，网站应用的规模也在不断的扩大，进而导致系统架构也在不断的进行变化。

从互联网早起到现在，系统架构大体经历了下面几个过程: 单体应用架构—>垂直应用架构—>分布 式架构—>SOA架构—>微服务架构，当然还有悄然兴起的Service Mesh(服务网格化)。

接下来我们就来了解一下每种系统架构是什么样子的，  以及各有什么优缺点。

#### 1.1.1 单体应用架构

互联网早期, 一般的网站应用流量较小，只需一个应用，将所有功能代码都部署在一起就可以，这 样可以减少开发、部署和维护的成本。

比如说一个电商系统，里面会包含很多用户管理，商品管理，订单管理，物流管理等等很多模块， 我们会把它们做成一个web项目，然后部署到一台tomcat服务器上。

![img](.\images\wps2.png) 

**优点：**

* 项目架构简单，小型项目的话，  开发成本低

* 项目部署在一个节点上，  维护方便

**缺点：**

* 全部功能集成在一个工程中，对于大型项目来讲不易开发和维护

* 项目模块之间紧密耦合，单点容错率低

* 无法针对不同模块进行针对性优化和水平扩展

#### 1.1.2 垂直应用架构

随着访问量的逐渐增大，单一应用只能依靠增加节点来应对，但是这时候会发现并不是所有的模块都会有比较大的访问量.

还是以上面的电商为例子，  用户访问量的增加可能影响的只是用户和订单模块，  但是对消息模块

的影响就比较小. 那么此时我们希望只多增加几个订单模块，  而不增加消息模块. 此时单体应用就做不到了， 垂直应用就应运而生了.

所谓的垂直应用架构，就是将原来的一个应用拆成互不相干的几个应用，以提升效率。比如我们可以将上面电商的单体 应用拆分成:

* 电商系统(用户管理 商品管理 订单管理)

* 后台系统(用户管理 订单管理 客户管理)

* CMS系统(广告管理 营销管理)

这样拆分完毕之后，  一旦用户访问量变大，只需要增加电商系统的节点就可以了，而无需增加后台 和CMS的节点。

![img](.\images\wps11.png) 

**优点：**

* 系统拆分实现了流量分担，解决了并发问题，而且可以针对不同模块进行优化和水扩展一个系统的问题不会影响到其他系统，提高容错率

**缺点：**

* 系统之间相互独立，  无法进行相互调用

* 系统之间相互独立，  会有重复的开发任务

#### 1.1.3 分布式架构

当垂直应用越来越多，重复的业务代码就会越来越多。这时候，我们就思考可不可以将重复的代码抽取出来，做成统一 的业务层作为独立的服务，然后由前端控制层调用不同的业务层服务呢？

这就产生了新的分布式系统架构。它将把工程拆分成表现层和服务层两个部分，服务层中包含业务 逻辑。表现层只需要处理和页面的交互，业务逻辑都是调用服务层的服务来实现。

![img](.\images\wps16.jpg) 

**优点：**

* 抽取公共的功能为服务层，提高代码复用性

**缺点：**

* 系统间耦合度变高，调用关系错综复杂，难以维护

#### 1.1.4  SOA架构

在分布式架构下，当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加

一个调度中心对集群进行实时管理。此时，用于资源调度和治理中心(SOA Service Oriented Architecture)是关键。

![img](.\images\wps17.jpg) 

**优点：**

* 使用治理中心（ESB\dubbo）解决了服务间调用关系的自动调节

**缺点：**

* 服务间会有依赖关系，  一旦某个环节出错会影响较大( 服务雪崩 )

* 服务关系复杂，运维、测试部署困难

#### 1.1.5 微服务架构

微服务架构在某种程度上是面向服务的架构SOA继续发展的下一步，它更加强调服务的"彻底拆分"。

![img](.\images\wps21.png) 

微服务架构与SOA架构的不同

微服务架构比 SOA架构粒度会更加精细，让专业的人去做专业的事情（专注），目的提高效率，每个服务于服务之间互不影响，微服务架 构中，每个服务必须独立部署，微服务架构更加轻巧，轻量级。

SOA 架构中可能数据库存储会发生共享，微服务强调独每个服务都是单独数据库，保证每个服务于服务之间互不影响。 项目体现特征微服务架构比 SOA 架构更加适合与互联网公司敏捷开发、快速迭代版本，因为粒度非常精细。

**优点：**

* 服务原子化拆分，独立打包、部署和升级，保证每个微服务清晰的任务划分，利于扩展 微服务之间采用Restful等轻量级http协议相互调用

**缺点：**

* 分布式系统开发的技术成本高（容错、分布式事务等）

* 复杂性更高。各个微服务进行分布式独立部署，当进行模块调用的时候，分布式将会变得更加麻烦。

### 1.2 微服务架构介绍

作者

![img](.\images\wps26.png) 

​	Martin Fowler 

①：英文:https://martinfowler.com/articles/microservices.html

②: 中文:http://blog.cuicc.com/blog/2015/07/22/microservices

**他说微服务其实是一种架构风格**，我们在开发一个应用的时候这个应用应该是由一组小型服务组成，每个小型服务都运行在自己的进 程内；小服务之间通过HTTP的方式进行互联互通。

![img](.\images\wps27.jpg) 

![img](.\images\wps28.png) 

#### 1.2.1 微服务架构的常见问题

一旦采用微服务系统架构，就势必会遇到这样几个问题：

* **这么多小服务，如何管理他们？(服务治理 注册中心[服务注册 发现 剔除])     nacos**
* **这么多小服务，他们之间如何通讯？(restful rpc dubbo feign)    httpclient("url",参数)，  springBoot,restTemplate("url",参数) , feign**
* **这么多小服务，客户端怎么访问他们？(网关)     gateway**
* **这么多小服务，一旦出现问题了，应该如何自处理？(容错)     sentinel**
* **这么多小服务，一旦出现问题了，应该如何排错? (链路追踪)   skywalking**
* **4个9    52.6分钟    5个9   5分钟**

对于上面的**问题**，是任何一个微服务设计者都不能绕过去的，因此大部分的***微服务***产品都针对每一 个***问题***提供了相应的组件来***解决***它们。

![img](.\images\wps34.jpg) 

#### 1.2.2 常见微服务架构

1. **dubbo: zookeeper+dubbo+ SpringMVC/SpringBoot**

* 配套 通信方式：rpc

* 注册中心：zookeeper / redis

* 配置中心：diamond

2. **SpringCloud：全家桶+轻松嵌入第三方组件(Netflix)**

* 配套 通信方式：http restful

* 注册中心：eruka / consul

* 配置中心：config

* 断 路 器：hystrix

* 网关：zuul

* 分布式追踪系统：sleuth + zipkin

3. **SpringCloudAlibaba**

> Spring Cloud 以微服务为核心的分布式系统构建标准

“分布式系统中的常见模式”给了 Spring Cloud 一个清晰的定位，即“模式”。也就是说 Spring Cloud 是针对分布式系统开发所做的通用抽象，

是标准模式的实现。这个定义非常抽象，看完之后并不能知道 Spring Cloud 具体包含什么内容。再来看一下 Spring 官方给出的一个 High Light 的 架构图，就可以对这套模式有更清晰的认识：

![img](.\images\wps45.png) 

可以看到这个图中间就是各个 Microservice，也就是我们的这个微服务的实现，周边周围的话就是去围绕这个微服务来去做各种辅助的信息事情。

例如分布式追踪、服务注册、配置服务等，都绕微服务运行时所依赖的必不可少的的支持性功能。我们可以得出这样一个结论：Spring Cloud 是以 微服务为核心的分布式系统的一个构建标准。

**2、Spring Cloud Alibaba 介绍**

Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发微服务架构的必需组件，方便开发者 通过 Spring Cloud 编程模型轻松使用这些组件来开发微服务架构。

依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里分布式应用解 决方案，通过阿里中间件来迅速搭建分布式应用系统。

根据 Jakarta 2019 年的调研报告，Spring Boot 拥有非常高的占比。熟悉 Java 语言的同学，应该对 Spring 框架都不会陌生。其倡导的依赖倒置、 面向切面编程等特性已经形成了 Java 语言的事实标准，几乎所有三方框架都会提供对 Spring 框架的支持。

![img](.\images\wps46.jpg) 

### 3. Spring Cloud Alibaba的定位

既然说 Spring Cloud 是标准，那么自然少不了针对标准的实现。这里，为大家介绍下 Spring Cloud Alibaba 这套实现。先给出下面这张图帮助大 家理解 Spring Cloud Alibaba 的定位：



![img](.\images\wps47.jpg) 

这里给大家这么一个公式，这个叫做：“3 加 2”。

3  指的就是图中深色的部分，其实它就是 Spring Cloud 标准，  一共有 3 层。中间颜色最深的部分就是及整个微服务最核心的内容，包括了“ RPC 调用”以及“服务注册与发现”。第二层，也就是围绕着核心的这一圈，是一些辅助微服务更好的工作功能，包括了负载均衡、路由、网关、断路 器，还有就是追踪等等这些内容。再外层的话，主要是一些分布式云环境里通用能力。

“3 加 2”中的“2”，指的就是上图中最外面这一圈。这一部分就是这个我们 Spring Cloud Alibaba 的一个定义，它其实包含两个部分的内容：

**右上部分**是对于 Spring Cloud 标准的实现。例如，我们通过 Dubbo 实现了 RPC 调用功能，通过 Nacos 实现了“服务注册与发现”、“分布式配 置”，通过 Sentinel 实现了断路器等等，这里就不一一列举了。

***左下部分***是我们 Spring Cloud Alibaba 对阿里云各种服务的集成。可能很多同学会有这样的一个问题：为什么要加上这一部分呢？此时回头审视一 下 Spring Cloud ，它仅仅是一个微服务的一个框架。但是在实际生产过程中，单独使用微服务框架其实并不足以支撑我们去构建一个完整的系统。 所以这部分是用阿里帮助开发者完成微服务以外的云产品集成的功能。

这里可能会很多同学会有这么一个担心：是不是使用了 Spring Cloud Alibaba，就会被阿里云平台绑定呢？在此，我们明确的告诉大家，这是不会  的。为什么这么说呢？如上面说的，“3 加 2”中的 2 是被分为两个部分的。其中对 Spring Cloud 的实现是完全独立的，开发者可以只是用这部分 实现运行在任何云平台中。当然，另一部分，由于天然是对阿里云服务的集成，这部分是和平台相关的。这里给开发者充分的自由，选择只是用其中 的部分还是全部产品。当然，我们也非常欢迎开发者选择使用阿里云的全套服务，我们也会尽量保证使用整套产品时的连贯性与开发的便利性。

### 4. SpringCloud各套实现对比

Spring Cloud 作为一套标准，它的实现肯定不止一套，那么各套实现都有什么区别呢？我们来一起看一下下面这张图：

![img](.\images\wps48.jpg) 

可以发现 Spring Cloud Alibaba 是所有的实现方案中功能最齐全的。尤其是在 Netflix 停止更新了以后，  Spring Cloud Alibaba 依然在持续更新和 迭代。



![img](.\images\wps49.png) 



![img](.\images\wps50.jpg) 

从 18 年 7 月份 Spring Cloud Alibaba 正式提交代码开始，就得到了大家广泛的关注。截止今天，Spring Cloud Alibaba 一共获得了超过了 1.5 万 的 star 数，已经的领先于所有其他实现的总和。

根据今年 X-lab 开放实验室刚刚发布的《2020 年微服务领域开源数字化报告》，Spring Cloud Alibaba 已经成为最活跃的 Spring Cloud 实现。

![img](.\images\wps51.png) 

数据来源《2020 年微服务领域开源数字化报告》，公众号后台回复关键词“微服务报告”获取报告全文。

### 5. Spring Cloud Alibaba生态

可以看到除了围绕着 Spring Cloud 的标准实现以外，还有包括的数据、资源、消息、缓存等各种类型的服务。在不同类型的服务下，也有很多具体 的产品可供用户选择。



![img](.\images\wps52.jpg) 

这里罗列典型而非全部产品。更多的内容，可以参考阿里云官网

### 6. Spring Cloud Alibaba用户数

截止到今天，Spring Cloud Alibaba 获得了数超过 1.5w 的 star 数。同时在 Github 上的项目依赖，就是对 Spring Cloud Alibaba 产生依赖关系的 产品，也超过了 6000。最重要的，使用 Spring Cloud Alibaba 的公司超过 1000 家。当然不只是外部的公司在使用，阿里内部也在使用。经过了   双十一的洗礼，其实整个这套框架它的这个稳定性可靠性都得到了印证。

![img](.\images\wps53.png) 

### 7. Spring Cloud Alibaba环境搭建

SpringCloud Alibaba 依赖 [Java](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 环境来运行。还需要为此配置 [Maven](https://maven.apache.org/index.html)环境，请确保是在以下版本环境中安装使用:

1. 64 bit JDK 1.8+； [下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) & [配置](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)。1.8.0_131

2. Maven 3.2.x+； [下载](https://maven.apache.org/download.cgi) & [配置](https://maven.apache.org/settings.html)。3.6.1



1. 基于SpringBoot的父maven项目

2. 创建2个服务（订单服务和库存服务）

 

版本说明： https://github.com/alibaba/spring-cloud-

[alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E](https://github.com/alibaba/spring-cloud-alibaba/wiki/版本说明)

 

Spring Cloud Alibaba:2.2.5.RELEASE

Spring Boot :2.3.2.RELEASE

Spring Cloud:Hoxton.SR8

 

最新的版本选择：  Spring Cloud Alibaba 2.2.5.RELEASE

![img](.\images\wps54.jpg) 

```pom
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐starter‐parent</artifactId>
    <version>2.3.2.RELEASE</version>
    <relativePath/>
    <!‐‐ lookup parent from repository ‐‐>
</parent>
<groupId>com.chen.mall</groupId>
<artifactId>vip‐spring‐cloud‐alibaba</artifactId>
<version>0.0.1‐SNAPSHOT</version>
<name>spring‐cloud‐alibaba</name>
<packaging>pom</packaging>
<description>Demo project for Spring Cloud Alibaba</description>

<properties>
    <java.version>1.8</java.version>
    <spring‐cloud.version>Hoxton.SR8</spring‐cloud.version>
    <spring‐cloud‐alibaba.version>2.2.5.RELEASE</spring‐cloud‐alibaba.version>
</properties>

<dependencyManagement>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring‐cloud‐dependencies</artifactId>
        <version>${spring‐cloud.version}</version>
        <type>pom</type>
        <scope>import</scope>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring‐cloud‐alibaba‐dependencies</artifactId>
        <version>${spring‐cloud‐alibaba.version}</version>
        <type>pom</type>
        <scope>import</scope>
   </dependency>
   </dependencies>
   </dependencyManagement>
 </project>
```

### 8. Java工程脚手架：更适合亚太区Java开发者的脚手架

![img](.\images\wps61.jpg) 

很多开发者应该跟我一样，都有过这样的经历：创建新应用时，先找一个我们最熟悉的一个老应用，把它里边的业务代码全部清理干净。然后相关的 各种配置名称全部改掉，最终做出一个空的一个应用模板。再把这个应用模板拿过来改个名子，就变成了一个新的应用。

当然可能有的同学会做的更多一些，例如长期维护这么一个空白模板在那里。下次拿过出来之后再改改个名字，就是一个新的应用。 这样做可能是一个相对保险的方案，但是缺点也非常明显：

* 版本老旧，新特性无法享受

* 团队知识无法沉淀

* 重复劳动

通过提供 Java 工程脚手架来解决这个问题。下面就是 Java 工程脚手架的页面：  https://start.aliyun.com/bootstrap.html

![img](.\images\wps65.jpg) 

在这里，开发者设置项目的基本信息，例如：开发语言、Java 版本、Spring Boot 版本等内容。