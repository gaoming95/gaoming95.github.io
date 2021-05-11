---
title: RabbitMq
subtitle: RabbitMQ是一个开源的消息代理和队列服务器，用来通过普通协议在不同的应用之间共享数据(跨平台跨语言)。RabbitMQ是使用Erlang语言编写，并且基于AMQP协议实现
date: 2020-06-20
author: 高明
tags:
	- 消息队列
	- 应用框架
---

# RabbitMQ

`RabbitMQ`是一个开源的消息代理和队列服务器，用来通过普通协议在不同的应用之间共享数据(跨平台跨语言)。`RabbitMQ`是使用`Erlang`语言编写，并且基于`AMQP`协议实现

## RabbitMq

`RabbitMq`的优势

**`可靠性(Reliablity)：`**使用了一些机制来保证可靠性，比如持久化、传输确认、发布确认。

**`灵活的路由(Flexible Routing)：`**在消息进入队列之前，通过`Exchange`来路由消息。对于典型的路由功能，`Rabbit`已经提供了一些内置的`Exchange`来实现。针对更复杂的路由功能，可以将多个`Exchange`绑定在一起，也通过插件机制实现自己的`Exchange`。

**`消息集群(Clustering)：`**多个`RabbitMQ`服务器可以组成一个集群，形成一个逻辑`Broker`。

**`高可用(Highly Avaliable Queues)：`**队列可以在集群中的机器上进行镜像，使得在部分节点出问题的情况下队列仍然可用。

**`多种协议(Multi-protocol)：`**支持多种消息队列协议，如`STOMP`、`MQTT`等。

**`多种语言客户端(Many Clients)：`**几乎支持所有常用语言，比如`Java`、`.NET`、`Ruby`等。

**`管理界面(Management UI)：`**提供了易用的用户界面，使得用户可以监控和管理消息`Broker`的许多方面。

**`跟踪机制(Tracing)：`**如果消息异常，`RabbitMQ`提供了消息的跟踪机制，使用者可以找出发生了什么。

**`插件机制(Plugin System)：`**提供了许多插件，来从多方面进行扩展，也可以编辑自己的插件。

### 为什么使用消息队列

1. 异步
2. 解耦
3. 削峰

## 安装

### 安装 Erlang

环境变量

`ERLANG_HOME`

`Path：%ERLANG_HOME%\bin`

### 安装 RabbitMQ

环境变量

`RABBITMQ_SERVER`

`Path：%RABBITMQ_SERVER%\sbin`

### 安装 rabbitmq_management

```
rabbitmq-plugins list
rabbitmq-plugins enable rabbitmq_management
```

输入`http://localhost:15672`

`username:guest`
`password:guest`

## SpringBoot 快速集成 RabbitMq

### rabbitmq配置

```java
@Configuration
public class RabbitMQConfig {
    @Value("${rabbitmq.queue.sendMessage}")
    private String sendMessageQueueName;

    @Bean
    public Queue queue() {
        return new Queue(sendMessageQueueName);
    }
}
```

### sender 发送消息

```java
@Component
public class Sender {
    @Value("${rabbitmq.queue.sendMessage}")
    private String sendMessageQueueName;

    @Autowired
    private AmqpAdmin amqpAdmin;
    @Autowired
    private AmqpTemplate amqpTemplate;

    public void send() {
        amqpTemplate.convertAndSend(sendMessageQueueName, "foo");
    }
}
```

### listener 监听消息

```java
@Component
public class Listener {
    @RabbitListener(queues = "sendMessage1")
    public void process(@Payload String content) {
        System.out.println("this is listener:" + content);
    }
}
```

### application.properties

```properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.listener.direct.acknowledge-mode=none
rabbitmq.queue.sendMessage=sendMessage1
```

## SpringBoot 集成 RabbitMq

[项目](https://github.com/gaoming95/rtz-learn/tree/master/rtz-rabbitmq)

### 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.70</version>
</dependency>
```

### RabbitMqConfig

```java
@Configuration
public class RabbitMQConfig {
    @Value("${rabbitmq.corePoolSize}")
    private Integer corePoolSize;
    @Value("${rabbitmq.maxPoolSize}")
    private Integer maxPoolSize;
    @Value("${rabbitmq.concurrency}")
    private String concurrency;
    @Value("${rabbitmq.queueCapacity}")
    private Integer queueCapacity;
    @Value("${rabbitmq.keepAliveSeconds}")
    private Integer keepAliveSeconds;

    @Bean(name = "mqExecutor")
    public ThreadPoolTaskExecutor mqExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(corePoolSize);
        executor.setMaxPoolSize(maxPoolSize);
        executor.setQueueCapacity(queueCapacity);
        executor.setKeepAliveSeconds(keepAliveSeconds);
        return executor;
    }

    @Bean
    public SimpleMessageListenerContainer ServiceNameListenerContainer(
            ConnectionFactory connectionFactory,
            MessageConverter messageConverter,
            @Qualifier("serviceNameQueue") Queue queue,
            @Qualifier("mqExecutor") Executor executor,
            @Qualifier("serviceNameListener") ChannelAwareMessageListener messageListener
    ){
        return defaultMessageListenerContainer(connectionFactory, messageConverter, queue, executor, messageListener);
    }

    private SimpleMessageListenerContainer defaultMessageListenerContainer(
            ConnectionFactory connectionFactory,
            MessageConverter messageConverter,
            Queue queue,
            Executor executor,
            ChannelAwareMessageListener messageListener) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.setAcknowledgeMode(AcknowledgeMode.MANUAL);
        container.setTaskExecutor(executor);
        container.setConcurrency(concurrency);
//        container.setMessageConverter(messageConverter);
        container.setQueues(queue);
        container.setMessageListener(messageListener);
        return container;
    }
}
```

### 配置监听

```java
@Component
@Slf4j
public class ServiceNameListener implements ChannelAwareMessageListener {

    @Autowired
    private FastJsonMessageConverter messageConverter;

    @Override
    public void onMessage(Message message, Channel channel) throws Exception {
        log.info("do some service, message is {}", message);
        long deliveryTag = message.getMessageProperties().getDeliveryTag();
        String key = message.getMessageProperties().getReceivedRoutingKey();
        System.out.println(key);
        ServiceNameMessage msg;

        try {
            msg = messageConverter.fromMessage(message, ServiceNameMessage.class);
        } catch (Exception e) {
            log.error("ServiceName: message params error ", e);
            channel.basicReject(deliveryTag, false);
            return;
        }

        try {
            this.processServiceName(msg);
            channel.basicAck(deliveryTag, false);
        } catch (Exception e) {
            log.error("Service Name failed with msg={}, error is {}", msg, e);
            channel.basicReject(deliveryTag, false);
        }
    }

    private void processServiceName(ServiceNameMessage msg) {
        // 处理消息
        System.out.println("消息处理完成");
    }
}
```

### 配置处理器

```java
@Component
@Slf4j
public class ServiceNameHandler extends AbstractMqMessageHandler<ServiceNameMessage> {

    @Autowired
    @Qualifier("serviceNameExchange")
    private FanoutExchange exchange;

    @Override
    public void convertAndSend(ServiceNameMessage msg) {
        Assert.isTrue(messageValidate(msg), "非法消息不可发送");
        this.sendMsgWithRetry(msg, exchange.getName(), "s");
    }

    @Override
    public Boolean messageValidate(ServiceNameMessage msg) {
        log.info("messageValid with message={}", msg);
        return msg != null && msg.getAccountId() != null && msg.getGoodId() != null;
    }
}
```

### application.properties

```properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
#spring.rabbitmq.virtual-host=test

rabbitmq.concurrency=5
rabbitmq.corePoolSize=50
rabbitmq.maxPoolSize=100
rabbitmq.queueCapacity=100
rabbitmq.keepAliveSeconds=60

rabbitmq.queue.service_name=queue_service_name
rabbitmq.exchange.service_name=exchange_service_name
```

