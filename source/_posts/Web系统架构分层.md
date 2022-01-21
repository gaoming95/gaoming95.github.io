---
title: Web系统架构分层
subtitle: 微服务架构 api biz common job portal
date: 2020-06-22
author: 高明
tags:
	- 应用框架
---

# Web系统架构分层

## 九层架构

### api

外部系统调用本系统服务的入口

打成`jar`包提交到`maven`仓库供其他项目调用

满足调用需要的类名、方法名、请求和返回参数

****

#### 接口定义

接口（`interface`）定义

```java
/**
 * @file: IArchiveService
 * @author: gaoming
 * @date: 2021/01/22
 * @version: 1.0
 * @description: 
 **/
public interface IExampleService {
    PageResult<ExampleListDto> queryExamplesByPage(QueryExampleListDto queryDto);
    List<ExampleTopDto> queryExampleTop(QueryExampleTopDto queryDto);
}
```

#### 模型对象定义

模型对象（`DTO`）定义

```java
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import java.io.Serializable;

/**
 * @file: ExampleQueryInfoDto
 * @author: gaoming
 * @date: 2021/01/22
 * @version: 1.0
 * @description:
 **/

@AllArgsConstructor
@NoArgsConstructor
@Builder
@Data
public class QueryExampleListDto implements Serializable {
    private static final long serialVersionUID = -3861945445570798616L;

    /**
     * 内容
     */
    private String content;

    /**
     * 页码
     */
    private Page page;
}
```

****

【注意事项】

1. `api`层尽量精简
2. 注意对象是`DTO`，而不是`Dto`
3. 接口或类注释，方法注释
4. 请求参数最好不要使用基本类型（不利于后续扩展）
5. 返回参数不要直接返回数据，要使用包装类包装

### biz

业务逻辑、消息队列的处理

对上接`service`、`task`层，下接`manager`、`integration`层

【注意事项】

1. `biz`层统一使用`BO`对象做逻辑处理
   1. 将外部系统与内部系统命名不统一的字段进行转换处理
   2. 有一些字段需要逻辑处理，但是又不要存储的放在`BO`对象里
   3. 方法可复用
   4. 除了消息队列中的对象外，biz层的对象不需要序列化
2. `biz`层检查到的异常统一封装成`ServiceException`
3. 类名统一以biz结尾（为了和`service`区分）
4. `biz`层的类没有必要使用接口的形式，直接在类里面写逻辑处理就可以了
5. 如非特殊情况，事务统一在`manager`层去处理

### common

1. 系统常量
2. 异常处理
3. 日期、加解密等工具类
4. 系统内部枚举

### dal

数据访问层，与底层`Mysql`、`Oracle`、`Hbase`进行数据交互

如：`Mybatis` `ORM`文件，`mapper`、`po`、`dao`文件

### integration

外部接口访问

### manager

1. 对`service`层通用能力的下沉，如缓存方案、数据库操作通用处理
2. 与`dao`层交互，对dao的业务通用能力的封装
3. 事务处理

【注意事项】

1. `manager`层的每个方法都是一系列的数据库操作，`dao`层整合、事务处理
2. `BO`转`PO`对象
3. `manager`层针对缓存的规范（权衡是否需要缓存，请求量不大的勿用）

### service

1. `api`层的服务实现
2. 外部请求的参数校验（`aop`）
3. 外部请求的入参、回参和异常的日志打印
4. 调用`biz`层进行逻辑处理

【注意事项】

1. 使用`AOP+Oval`框架进行请求参数校验

### task

定时任务

### web

`web`层

## 通用五层架构

### api

外部系统调用本系统服务的入口

打成jar包提交到`maven`仓库供其他项目调用

满足调用需要的类名、方法名、请求和返回参数

### biz

业务逻辑逻辑处理（`Service`层 调用`Delegate`层）

### common

系统常量、枚举等

### job

定时任务

### portal

`web`接口层

## 其他层级

### bom

`BOM` (`Bill Of Materials`) 是由`maven`提供的功能，它通过定义一整套相互兼容的`jar`包版本集合，使用时只需要依赖该`BOM`文件，即可放心的使用需要的`jar`包，且无需在指定版本号。`BOM`的维护方负责版本升级，并保证`BOM`定义的`jar`包之间的兼容性。

使用`BOM`除了可以方便使用者在声明依赖的客户端时不需要指定版本号外，最主要的原因是可以解决依赖冲突。

`BOM` 本质上是一个普通的`POM`文件，区别是对于使用方而言，生效的只有`dependencyManament`这一个部分，也只是需要在`dependencyManament`定义对于对外发布的客户端版本即可

#### 使用

在项目主`pom.xml`文件中 节点下首位处加入如下

```xml
<dependencyManagement>
    <dependencies>
         <dependency>
            <groupId>com.test.xx</groupId>
            <artifactId>cloud-bom</artifactId>
            <version>${version}</version>
            <type>pom</type>
            <scope>import</scope>
          </dependency>
        
          <dependency>
            ...
          </dependency>
    </dependencies>
</dependencyManagement>
```

在需要使用相关`JAR`包的`pom.xml`文件中节点下引入如下

```xml
<dependencies>
 ...
	 <dependency>
	  <groupId>com.google.guava</groupId>
	  <artifactId>guava</artifactId>
	 </dependency>

	 <dependency>
	  <groupId>commons-collections</groupId>
	  <artifactId>commons-collections</artifactId>
	 </dependency>
 ....
</dependencies>
```





## 参考文献

[微服务之BOM定义](https://blog.csdn.net/Yue_zuozuo/article/details/108975078)

