---
title: Spring HTTP invoker(SOA)
subtitle: Spring HTTP invoker 是 spring框架中的一个远程调用模型，执行基于HTTP的远程调用（意味着可以通过防火墙），并使用Java的序列化机制在网络间传递对象
date: 2020-09-03
author: 高明
tags:
	- Spring
---



# Spring HTTP invoker(SOA)

`Spring HTTP invoker` 是 `spring`框架中的一个远程调用模型，执行基于`HTTP`的远程调用（意味着可以通过防火墙），并使用`Java`的序列化机制在网络间传递对象。这需要在远端和本地都使用`Spring`才行，客户端可以很轻松地像调用本地对象一样调用远程服务器上地对象，这有点类似于`webservice`，但又不同于`webservice`，区别如下：

| WebService                                      | Http Invoker                       |
| ----------------------------------------------- | ---------------------------------- |
| 跨平台                                          | 只支持`java`语言                   |
| 支持`SOAP`，提供`wsdl`                          | 不支持                             |
| 结构庞大，依赖特定的`webservice`实现，如`xfire` | 结构简单，只依赖于`spring`框架本身 |

说明：

1 服务器端：通过`HTTP Invoker`服务将服务接口的某个实现类提供远程服务

2 客户端：通过`HTTP invoker`代理向服务器端发送请求，远程调用服务接口的方法

3 服务器端与客户端通信的数据均需要序列化

## 1 exporter

【接口】

```java
public interface ITestService {
    String test();
}
```

【接口实现】

```Java
@Service
public class TestService implements ITestService {
    @Override
    public String test() {
        return "打印一段字符";
    }
}
```

【配置】

```java
@Configuration
public class ExportConfig {
    @Bean("/testService")
    public HttpInvokerServiceExporter testService(ITestService testService) {
        HttpInvokerServiceExporter httpInvokerServiceExporter = new HttpInvokerServiceExporter();
        httpInvokerServiceExporter.setService(testService);
        httpInvokerServiceExporter.setServiceInterface(ITestService.class);
        return httpInvokerServiceExporter;
    }
}
```

```
server.port=8080
```

## 2 Importer

需要接收到的接口

```java
public interface ITestService {
    String test();
}
```

配置

```java
@Configuration
public class ImporterConfiguration {
    @Bean
    public HttpInvokerProxyFactoryBean testService() {
        HttpInvokerProxyFactoryBean httpInvokerProxyFactoryBean = new HttpInvokerProxyFactoryBean();
        httpInvokerProxyFactoryBean.setServiceUrl("http://localhost:8080/testService");
        httpInvokerProxyFactoryBean.setServiceInterface(ITestService.class);
        return httpInvokerProxyFactoryBean;
    }
}
```

```
server.port=8081
```



## 3 Controller 调用

```java
@Controller
public class TestController {
    @Autowired
    private ITestService testService;

    @ResponseBody
    @RequestMapping("test")
    public String test(){
        return testService.test();
    }
}
```

