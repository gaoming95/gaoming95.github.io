---
title: Pdf转图片
subtitle: Java Pdf文件转图片
date: 2020-06-06
author: 高明
tags:
	- 工具
---

# PdfBox

pdf文件转图片，并上传服务器

考虑到单线程情况下，16页的pdf转成图片需要6-8s

使用多线程，16页的pdf转成图片需要2s，提升了部分速度

## 引入依赖

```xml
<dependency>
    <groupId>org.apache.pdfbox</groupId>
    <artifactId>pdfbox</artifactId>
    <version>2.0.22</version>
</dependency>
```

## Demo

**单线程**

```java
public static List<byte[]> pdfToImage(byte[] fileContent) throws IOException {
    List<byte[]> result = new ArrayList<>();
    try (PDDocument document = PDDocument.load(fileContent)) {
        PDFRenderer renderer = new PDFRenderer(document);
        for (int i = 0; i < document.getNumberOfPages(); ++i) {
            BufferedImage bufferedImage = renderer.renderImageWithDPI(i, DPI);
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            ImageIO.write(bufferedImage, IMG_TYPE, out);
            result.add(out.toByteArray());
        }
    }
    return result;
}
```

****

**多线程**

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

```
    @Autowired
    private ThreadPoolTaskExecutor taskExecutor;
```

```java
public List<String> uploadAndParsePDF(File file) throws IOException {
    List<Tuple2<Integer, String>> imgUrls = new ArrayList<>();
    PDDocument doc = null;
    try {
        doc = PDDocument.load(file);
        PDFRenderer renderer = new PDFRenderer(doc);
        int pageCount = doc.getNumberOfPages();
        CompletionService<Tuple2<Integer, String>> cService = new ExecutorCompletionService<>(taskExecutor);
        IntStream.range(0, pageCount).forEach(i -> cService.submit(() -> {
            BufferedImage image = renderer.renderImage(i, 2.5f);
            byte[] bytes = imageToBytes(image, "PNG");
            return new Tuple2(i, pictureService.upload(fileName + ".png", bytes));
        }));

        for (int i = 0; i < pageCount; i++) {
            Future<Tuple2<Integer, String>> future = cService.take();
            imgUrls.add(future.get());
        }
    } catch (Exception e) {
        Assert.isTrue(true, "文件解析错误");
        e.printStackTrace();
    } finally {
        if (doc != null) {
            doc.close();
        }
    }
    return imgUrls;
}
```

