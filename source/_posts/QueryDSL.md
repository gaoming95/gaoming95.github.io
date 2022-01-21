---
title: QueryDSL
subtitle: QueryDSL
date: 2020-07-01
author: 高明
tags:
	- 数据库
---

# QueryDSL

## 特性对比

| 方面     | 分类       | 特性                                                         | Mybatis                                                      | JPA（Hibernate）                                             | Querydsl-sql                                               |
| -------- | ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| 开发效率 | 自动化     | 逆向生成                                                     | 支持                                                         | 支持                                                         | 支持                                                       |
|          | 参数处理   | 绑定参数                                                     | 弱绑定，通过字符串配置关联                                   | 强绑定，直接传参                                             | 强绑定，直接传参                                           |
|          |            | 参数类型检查                                                 | 无检查，运行时报错                                           | 启动时检查                                                   | 编译检查                                                   |
|          |            | 特殊字符转义                                                 | 不支持                                                       | 支持                                                         | 支持                                                       |
|          |            | 动态条件                                                     | 保证可读性的前提下，每个动态条件至少三行代码（使用Criteria） | 保证可读性的前提下，每个动态条件至少三行代码（使用Predicate） | 保证可读性的前提下，每个动态条件可用一行代码，支持链式调用 |
|          |            | 复杂条件，如：( a > 1 and b < 2 ) or ( c > 3 and (d>4 or e > 5) ) | API不支持、需要手写方法或重写条件组合                        | API支持，可读性差                                            | API支持，可读性好                                          |
|          | 查询结果集 | 返回实体                                                     | 支持                                                         | 支持                                                         | 支持                                                       |
|          |            | 返回指定Class                                                | API不支持，需要手写方法                                      | API不支持，需要手写方法                                      | API支持，通过参数指定                                      |
|          |            | 返回指定字段                                                 | API不支持，需要手写方法                                      | API不支持，需要手写方法                                      | API支持，通过参数指定                                      |
|          |            | 分组与分组条件                                               | API不支持，需要手写方法                                      | API不支持，需要手写方法                                      | API支持，通过参数指定                                      |
|          |            | 排序                                                         | API支持                                                      | API支持                                                      | API支持                                                    |
|          |            | 分页                                                         | API支持                                                      | API支持                                                      | API支持                                                    |
|          | 保存与更新 | 保存自选字段                                                 | API支持                                                      | API不支持指定                                                | API支持，通过参数                                          |
|          |            | 事务性表达式，如：set a=a+1                                  | API不支持，需要手写方法                                      | API不支持，需要手写方法                                      | API支持，通过参数指定                                      |
|          |            | 条件更新                                                     | API支持                                                      | API支持                                                      | API支持                                                    |
|          | 面向对象   | SQL复用性                                                    | 好（xml片段）                                                | 差                                                           | 好（表达式都是常量对象）                                   |
|          |            | 抽象与封装性                                                 | 差（Example、Criteria无抽象父类）                            | 差（类型限制太严格）                                         | 好（抽象性高、灵活性好）                                   |
|          | 故障排除   | 可调试性                                                     | 运行时生成（无源码）、调试性差                               | 运行时生成（无源码）、调试性差                               | 源码所见即所得，调试性好                                   |
|          |            | SQL与参数日志                                                | 支持                                                         | 支持                                                         | 支持                                                       |
|          | 开发工作量 | 自动提示                                                     | 需要插件支持，但上下文关联差                                 | 支持，上下文关联性差                                         | 支持，上下文提示性好                                       |
|          |            | 手动代码量                                                   | 高（不友好的API和xml）                                       | 中（不友好的API）                                            | 低（友好的API）                                            |
|          |            | 自动代码量                                                   | 高（Example API 及 xml）                                     | 低（延迟到运行时生成）                                       | 中（对应表的映射类）                                       |
| 执行效率 | 运行速度   | 启动速度                                                     | 慢（读取xml生成Mapper实现）                                  | 慢（读取注解生成实现）                                       | 快（不需要运行时生成）                                     |
|          |            | 运行时速度                                                   | 中                                                           | 慢                                                           | 快                                                         |
|          | 本地缓存   | 一级缓存                                                     | 支持                                                         | 支持                                                         | 不支持                                                     |
|          |            | 二级缓存                                                     | 支持                                                         | 支持                                                         | 不支持                                                     |
| 兼容性   | 数据库     | 标准SQL语法                                                  | 支持                                                         | 支持                                                         | 支持                                                       |
|          |            | 数据库特定SQL语法                                            | 支持（xml）                                                  | 支持（Native Sql）                                           | 支持（Sql Template）                                       |
|          |            | 数据库函数调用                                               | 支持                                                         | 支持                                                         | 支持                                                       |
|          |            | 跨数据库/可移植性                                            | 不支持                                                       | 支持                                                         | 支持                                                       |
|          | SQL支持    | 单表复杂查询                                                 | API不支持、需要手写方法                                      | API支持、代码较长、可读性差                                  | API支持，代码简洁                                          |
|          |            | 多表关联查询                                                 | API不支持、需要手写方法                                      | API支持，建议新建方法                                        | API支持，建议新建方法                                      |
|          |            | 嵌套子查询                                                   | API不支持、需要手写方法                                      | API支持，建议新建方法                                        | API支持，建议新建方法                                      |
|          |            | UNION查询                                                    | API不支持、需要手写方法                                      | API支持，建议新建方法                                        | API支持，建议新建方法                                      |
|          |            | Jar包依赖量                                                  | 中                                                           | 多                                                           | 少                                                         |
| 可维护性 | 重构性     | 新增字段                                                     | 重新生成                                                     | 无须处理                                                     | 重新生成                                                   |
|          |            | 修改字段                                                     | 扩展的mapper和xml要手动检查                                  | Predicate常量字段名需要手动检查                              | 开发工具联动修改                                           |
|          |            | 删除字段                                                     | 扩展的mapper和xml要手动检查                                  | Predicate常量字段名需要手动检查                              | 编译器自动检查                                             |
|          | 维护成本   | 学习使用成本                                                 | 中                                                           | 高                                                           | 中                                                         |
|          |            | 疑难杂症成本                                                 | 高                                                           | 高                                                           | 低（简洁且全程带源码调试）                                 |
|          | 数据库迁移 | 迁移改动量                                                   | 多                                                           | 中（部分Native Sql）                                         | 少（少量Sql Template）                                     |
|          | 功能扩展   | 日志等功能切入                                               | 拦截器                                                       | 拦截器、监听器                                               | 监听器、异常转换器                                         |

## 快速使用

### 引入依赖

```xml
<properties>
    <lombok.version>1.18.20</lombok.version>
    <mysql.connector.java.version>8.0.23</mysql.connector.java.version>
    <hikaricp.version>4.0.3</hikaricp.version>
    <querydsl.version>4.4.0</querydsl.version>
    <spring.jdbc.version>5.2.8.RELEASE</spring.jdbc.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
</properties>
```

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>com.querydsl</groupId>
        <artifactId>querydsl-sql</artifactId>
        <version>${querydsl.version}</version>
    </dependency>
    <dependency>
        <groupId>com.querydsl</groupId>
        <artifactId>querydsl-sql-spring</artifactId>
        <version>${querydsl.version}</version>
    </dependency>
    <dependency>
        <groupId>com.querydsl</groupId>
        <artifactId>querydsl-sql-codegen</artifactId>
        <version>${querydsl.version}</version>
        <scope>provided</scope>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.connector.java.version}</version>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
    </dependency>

    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>${hikaricp.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>${spring.jdbc.version}</version>
    </dependency>
</dependencies>
```

### 配置插件

```xml
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>

    <plugin>
        <groupId>com.querydsl</groupId>
        <artifactId>querydsl-maven-plugin</artifactId>
        <version>${querydsl.version}</version>
        <configuration>
            <jdbcDriver>com.mysql.cj.jdbc.Driver</jdbcDriver>
            <jdbcUrl>jdbc:mysql://localhost:3306/boke?useUnicode=true&amp;characterEncoding=UTF-8</jdbcUrl>
            <jdbcUser>root</jdbcUser>
            <jdbcPassword>root</jdbcPassword>
            <exportForeignKeys>false</exportForeignKeys>
            <sourceFolder>${project.basedir}/src/main/resources</sourceFolder>
            <targetFolder>${project.basedir}/src/main/java</targetFolder>
            <exportBeans>true</exportBeans>
            <packageName>com.gaoming.querydsl.dao.query</packageName>
            <beanPackageName>com.gaoming.querydsl.dao.entity</beanPackageName>
            <beanSuffix>Do</beanSuffix>
            <beanAddToString>true</beanAddToString>
            <tableNamePattern>sys_role</tableNamePattern>
            <beanInterfaces>
                <beanInterface>java.io.Serializable</beanInterface>
            </beanInterfaces>
            <!-- 使用更习惯的类型 -->
            <numericMappings>
                <numericMapping>
                    <total>1</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
                <numericMapping>
                    <total>2</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
                <numericMapping>
                    <total>3</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
                <numericMapping>
                    <total>4</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
                <numericMapping>
                    <total>5</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
                <numericMapping>
                    <total>6</total>
                    <decimal>0</decimal>
                    <javaType>java.lang.Integer</javaType>
                </numericMapping>
            </numericMappings>
        </configuration>
        <dependencies>
            <!--数据库对应的驱动包-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.connector.java.version}</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>
```

### 生成 ORM 文件

执行命令或者点击`Maven`插件中`querydsl`中的`querydsl:test-export`

```
mvn querydsl:test-export
```

该插件会生成对应的`entity`和`query`文件

```java
package com.gaoming.querydsl.dao.entity;
import javax.annotation.Generated;
import java.io.Serializable;
/**
 * SysRoleDo is a Querydsl bean type
 */
@Generated("com.querydsl.codegen.BeanSerializer")
public class SysRoleDo implements Serializable {
    private Integer eee;
    private Integer etxt;
    private Integer roleId;
    private String roleName;
    public Integer getEee() {
        return eee;
    }
    public void setEee(Integer eee) {
        this.eee = eee;
    }
    public Integer getEtxt() {
        return etxt;
    }
    public void setEtxt(Integer etxt) {
        this.etxt = etxt;
    }
    public Integer getRoleId() {
        return roleId;
    }
    public void setRoleId(Integer roleId) {
        this.roleId = roleId;
    }
    public String getRoleName() {
        return roleName;
    }
    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }
    @Override
    public String toString() {
         return "eee = " + eee + ", etxt = " + etxt + ", roleId = " + roleId + ", roleName = " + roleName;
    }
}
```

```java
package com.gaoming.querydsl.dao.query;
import static com.querydsl.core.types.PathMetadataFactory.*;
import com.gaoming.querydsl.dao.entity.SysRoleDo;
import com.querydsl.core.types.dsl.*;
import com.querydsl.core.types.PathMetadata;
import javax.annotation.Generated;
import com.querydsl.core.types.Path;
import com.querydsl.sql.ColumnMetadata;
import java.sql.Types;
/**
 * QSysRole is a Querydsl query type for SysRoleDo
 */
@Generated("com.querydsl.sql.codegen.MetaDataSerializer")
public class QSysRole extends com.querydsl.sql.RelationalPathBase<SysRoleDo> {
    private static final long serialVersionUID = -1544683865;
    public static final QSysRole sysRole = new QSysRole("sys_role");
    public final NumberPath<Integer> eee = createNumber("eee", Integer.class);
    public final NumberPath<Integer> etxt = createNumber("etxt", Integer.class);
    public final NumberPath<Integer> roleId = createNumber("roleId", Integer.class);
    public final StringPath roleName = createString("roleName");
    public final com.querydsl.sql.PrimaryKey<SysRoleDo> primary = createPrimaryKey(roleId);
    public QSysRole(String variable) {
        super(SysRoleDo.class, forVariable(variable), "null", "sys_role");
        addMetadata();
    }
    public QSysRole(String variable, String schema, String table) {
        super(SysRoleDo.class, forVariable(variable), schema, table);
        addMetadata();
    }
    public QSysRole(String variable, String schema) {
        super(SysRoleDo.class, forVariable(variable), schema, "sys_role");
        addMetadata();
    }
    public QSysRole(Path<? extends SysRoleDo> path) {
        super(path.getType(), path.getMetadata(), "null", "sys_role");
        addMetadata();
    }
    public QSysRole(PathMetadata metadata) {
        super(SysRoleDo.class, metadata, "null", "sys_role");
        addMetadata();
    }
    public void addMetadata() {
        addMetadata(eee, ColumnMetadata.named("eee").withIndex(4).ofType(Types.INTEGER).withSize(10).notNull());
        addMetadata(etxt, ColumnMetadata.named("etxt").withIndex(3).ofType(Types.INTEGER).withSize(10).notNull());
        addMetadata(roleId, ColumnMetadata.named("role_id").withIndex(1).ofType(Types.INTEGER).withSize(10).notNull());
        addMetadata(roleName, ColumnMetadata.named("role_name").withIndex(2).ofType(Types.VARCHAR).withSize(50).notNull());
    }
}
```

### 配置

```java
package com.gaoming.querydsl.config;
import com.querydsl.sql.MySQLTemplates;
import com.querydsl.sql.SQLQueryFactory;
import com.querydsl.sql.SQLTemplates;
import com.querydsl.sql.spring.SpringConnectionProvider;
import com.querydsl.sql.spring.SpringExceptionTranslator;
import com.zaxxer.hikari.HikariDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import javax.inject.Provider;
import javax.sql.DataSource;
import java.sql.Connection;
/**
 * @file: QueryDslConfig
 * @author: gaoming
 * @date: 2021/04/25
 * @version: 1.0
 * @description:
 **/
@Configuration
public class QueryDslConfig {
    @Bean
    public DataSource dataSource() {
        // implementation omitted
        // 这里使用HikariCP链接池
        HikariDataSource hikariDataSource = new HikariDataSource();
        hikariDataSource.setJdbcUrl("jdbc:mysql://localhost:3306/boke?useUnicode=true&characterEncoding=UTF-8");
        hikariDataSource.setUsername("root");
        hikariDataSource.setPassword("root");
        return hikariDataSource;
    }
    
    // 配置事务
    @Bean
    public DataSourceTransactionManager transactionManager() {
        return new DataSourceTransactionManager(dataSource());
    }
    
    // 使用Mysql模板
    @Bean
    public com.querydsl.sql.Configuration querydslConfiguration() {
        SQLTemplates templates = MySQLTemplates.builder().quote().build();
        com.querydsl.sql.Configuration configuration = new com.querydsl.sql.Configuration(templates);
        configuration.setExceptionTranslator(new SpringExceptionTranslator());
        return configuration;
    }
    
    // 向Spring容器注入 sqlQueryFactory
    @Bean(name = "sqlQueryFactory")
    public SQLQueryFactory queryFactory() {
        Provider<Connection> provider = new SpringConnectionProvider(dataSource());
        return new SQLQueryFactory(querydslConfiguration(), provider);
    }
}
```

### Controller

```java
package com.gaoming.querydsl.controller;
import com.gaoming.querydsl.service.IndexService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
/**
 * @file: IndexController
 * @author: gaoming
 * @date: 2021/04/26
 * @version: 1.0
 * @description:
 **/
@RestController
@RequestMapping(value = "/index")
public class IndexController {
    @Autowired
    private IndexService indexService;
    @RequestMapping(value = "", method = RequestMethod.GET)
    public String index() {
        return indexService.query();
    }
}
```

### Service

```java
@Service
public class IndexService {
    @Resource(name = "sqlQueryFactory")
    private SQLQueryFactory sqlQueryFactory;
    @Transactional
    public String query() {
        List<SysRoleDo> fetch = sqlQueryFactory.selectFrom(sysRole).fetch();
        return fetch.stream().map(SysRoleDo::getRoleName).collect(Collectors.joining(","));
    }
}
```

```bash
curl post http://localhost:8080/index
```

