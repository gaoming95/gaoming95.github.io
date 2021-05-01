---
title: CountDownLatch
subtitle: CountDownLatch
date: 2020-06-06
author: 高明
tags:
	- 多线程
---

# CountDownLatch

1. countDownLatch是在java1.5被引入，跟它一起被引入的工具类还有CyclicBarrier、Semaphore、concurrentHashMap和BlockingQueue
2. 存在于java.util.cucurrent包下

## 概念

countDownLatch这个类使一个线程等待其他线程各自执行完毕后再执行

是通过一个计数器来实现的，计数器的初始值是线程的数量。每当一个线程执行完毕后，计数器的值就-1，当计数器的值为0时，表示所有线程都执行完毕，然后在闭锁上等待的线程就可以恢复工作了

## Demo

### 线程池配置

```java
@Configuration
public class ThreadPoolConfig {
    @Bean
    public ThreadPoolTaskExecutor taskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(8);
        taskExecutor.setMaxPoolSize(32);
        taskExecutor.setKeepAliveSeconds(300);
        taskExecutor.setQueueCapacity(128);
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy());
        return taskExecutor;
    }
}
```

### 多线程

```java
@Autowired
private ThreadPoolTaskExecutor taskExecutor;

final CountDownLatch latch = new CountDownLatch(3);

// 第一个任务
 Future<List<ResultDto>> result1= taskExecutor.submit(() -> {
     try {
		return doSomething(queryDto);
     } finally {
         latch.countDown();
     }
 });

// 第二个任务
 Future<List<ResultDto>> result2= taskExecutor.submit(() -> {
     try {
		return doSomething(queryDto);
     } finally {
         latch.countDown();
     }
 });

// 第三个任务
 Future<List<ResultDto>> result3= taskExecutor.submit(() -> {
     try {
		return doSomething(queryDto);
     } finally {
         latch.countDown();
     }
 });

try {
    // 等待三个线程执行完毕
    latch.await();
} catch (InterruptedException e) {
    e.printStackTrace();
}

// 获取结果
List<ResultDto> r1 = result1.get();
List<ResultDto> r2 = result2.get();
List<ResultDto> r3 = result3.get();

// 处理结果返回
handleResult(r1, r2, r3)
```

****

**CountDownLatch和CyclicBarrier区别：**

```
1.countDownLatch是一个计数器，线程完成一个记录一个，计数器递减，只能只用一次
2.CyclicBarrier的计数器更像一个阀门，需要所有线程都到达，然后继续执行，计数器递增，提供reset功能，可以多次使用
```

### ExecutorCompletionService

```java
@Autowired
private ThreadPoolTaskExecutor taskExecutor;

CompletionService<Tuple2<Integer, String>> cService = new ExecutorCompletionService<>(taskExecutor);
IntStream.range(0, pageCount).forEach(i -> cService.submit(() -> {
    return doSomething(i);
}));

for (int i = 0; i < pageCount; i++) {
    Future<Tuple2<Integer, String>> future = cService.take();
    String r = future.get();
}
```



