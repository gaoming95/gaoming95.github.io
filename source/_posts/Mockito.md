---
title: Mockito 测试框架
subtitle: Mockito 是的 Java 单元测试 Mock 框架，开源
date: 2020-06-06
author: 高明
tags:
	- 测试框架
---

# Mockito 测试框架

## Mockito

Mockito是mocking框架

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>1.9.5</version>
    <scope>test</scope>
</dependency>
```

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>
```

```
@Test
public void verify_behaviour(){
    //模拟创建一个List对象
    List mock = mock(List.class);
    //使用mock的对象
    mock.add(1);
    mock.clear();
    //验证add(1)和clear()行为是否发生
    verify(mock).add(1);
    verify(mock).clear();
}
```

## mock redis

```java
@Mock
private RedisTemplate<String, String> stringRedisTemplate;
@Mock
private ValueOperations valueOperations;
```

```Java
when(stringRedisTemplate.opsForValue()).thenReturn(valueOperations);
```

## mock redisson lock

```java
@Mock
private RedissonClient redissonClient;
@Mock
private RedissonLock redissonLock;
```

```Java
when(mgkBaseService.getLock(any(),any())).thenReturn(redissonLock);
```

```java
when(redissonClient.getLock(any())).thenReturn(redissonLock);
```



## Mock @Value

```java
@Test
public void testGetWeChatSign() {
    when(stringRedisTemplate.opsForValue()).thenReturn(valueOperations);
    ReflectionTestUtils.setField(landingPageServiceDelegate, "appId", "");
    ReflectionTestUtils.setField(landingPageServiceDelegate, "appSecret", "");
    ReflectionTestUtils.setField(landingPageServiceDelegate, "accessTokenUrl", "");
    ReflectionTestUtils.setField(landingPageServiceDelegate, "jsapiTicketUrl", "");
    String weChatSign = landingPageServiceDelegate.getWeChatSign();
    assertNotNull(weChatSign);
}
```

实际项目开发中，我们经常会使用@Value注解从配置文件中注入属性值，写单侧时，在不启动容器的条件下，如何对这种属性进行mock呢？对这种情况，Spring提供了一个很好的工具类ReflectionTestUtils来实现。
 注入属性：

```java
@Value("${prepaid.partner.id}")
private String merchantPartnerId;
```

对这个属性，在单测中可以这样mock：

```java
ReflectionTestUtils.setField(targetObject, "merchantPartnerId", "123");
```

## Mock Void

```java
doNothing().when(collageLogService).insertLog(any(NewCollageOperationLogDto.class));
```

## Mock 属性赋值

比如数据库插入数据时，po带Id

```java
when(mgkCollageMediaDao.insertSelective(any())).thenAnswer(answer -> {
    MgkCollageMediaPo po = answer.getArgumentAt(0, MgkCollageMediaPo.class);
    po.setId(2);
    return 1;
});
collageMediaServiceDelegate.insert(operator, collageMediaDto);
```

## 预期的异常

```
@Test(expected = IllegalArgumentException.class)
```

## 初始化参数

```java
BeanTestUtils.initSimpleFields(QueryArchiveListDto.builder().build());
```

