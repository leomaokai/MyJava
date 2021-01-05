# Spring

## 简介

[Spring官方文档](https://docs.spring.io/spring-framework/docs/current/reference/html/)

[官方下载地址](http://repo.spring.io/release/org/springframework/spring)

[GitHub地址](https://github.com/spring-projects/spring-framework)

Spring是一个轻量级的控制反转(IOC)和面向切面编程(AOP)的容器框架

理念:使现有的技术更加容易使用,本身是一个大杂烩,整合了现有的技术框架

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc	</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

优点:

* Spring是开源的免费的框架(容器)
* Spring是轻量级的非入侵式的框架
* 控制反转（IOC）、面向切面编程（AOP）
* 支持事务的处理、对框架整合的支持

## 组成

![image-20210105171227600](Spring.assets/image-20210105171227600.png)

**Spring Core**:核心容器，主要组件BeanFactory，使用依赖注入(DI)的方式来管理Bean。BeanFactory是工厂模式的实现，使用控制反转(Ioc)将应用配置和依赖说明从代码中分离出来。 

**Spring Context**:应用上下文，为Spring框架提供上下文信息。 

**Spring AOP**：面向切面编程 

**Spring ORM**：为目前集中流程ORM提供集成方案 

**Spring DAO**：Spring对JDBC大量重复代码：取得连接、创建语句、处理结果集，关闭连接做了提取；支持事务管理。 

**Spring Web**：web上下文建立在context上下文之上，为web应用提供上下文。提供了其他web框架的集成 

**Spring Web MVC**:Spring为web提供了功能全面的MVC框架，使逻辑和业务对象完全分离。

## 扩展

Spring Boot

* 一个快速开发的脚手架
* 基于Boot可以快速开发单个微服务
* 约定大于配置

Spring Cloud

* 基于Spring Boot实现的

学习Spring Boot的前提，需要学习Spring及SpringMVC

# IOC理论推导

在之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码，如果程序代码量十分大，修改代价十分昂贵

```java
private UserDao userDao = new UserDaoImpl();
```

## 控制反转

* 之前，程序是主动创建对象，控制权在程序员手上

* 通过set注入，程序不再具有主动性，而是被动的接受对象，控制权在用户手上

```java
private UserDao userDao;
//利用set进行动态实现
public void setUserDao(UserDao userDao){
    this.userDao=userDao;
}
```

控制反转IOC(Inversion of Control)是一种设计思想，DI(依赖注入)是实现IOC的一种方法，没有IOC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，所谓的控制反转就是：获得依赖对象的方式反转了

IOC是Spring框架的核心内容，通过多种方式完美实现了IOC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IOC

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从IOC容器中取出需要的对象

![image-20210105192524274](Spring.assets/image-20210105192524274.png)

控制反转是一种通过描述(XML或注解)并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入(Dependency Injection,DI)