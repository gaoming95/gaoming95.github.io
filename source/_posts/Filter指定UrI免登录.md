---
title: Filter指定UrI免登录
subtitle: 基于注解实现拉取所有需要免登录的接口，可以匹配到HttpMethod层面的路由地址
date: 2020-09-16
author: 高明
tags:
	- 工具
	- Java基础
---



# Filter指定UrI免登录

## 背景

部分`web_api`请求接口由初始的登录权限校验变更为免登录权限校验

一种方式是变成`web_api`为`open_api`，`open_api`无权限校验

这种方式有不足的地方

1. 增加了额外接口，前端需要修改接口进行适配
2. `open_api`彻底放开了该接口的权限，后续如果需要增加权限校验，还需要再改
3. `open_api`和`web_api`适用范围，`open_api`是那些对方可以直接进行调用的接口，很显然，由web_api免登录转变的接口在逻辑上不是`open_api`

## Filter过滤URI

一般做法是在`Filter`层配置那些需要免校验的路由，如

```java
private static final List<String> IGNORE_URL = new ArrayList<>();
static {
    IGNORE_URL.add("/login");
    IGNORE_URL.add("api-docs");
}
```

这样，在`doFilter`方法中，当请求地址中包含这些需要被忽略的地址，则直接返回，不需要进行校验

```java
for (String ignore : IGNORE_URL) {
    if (httpRequest.getRequestURI().indexOf(ignore) > 0) {
        chain.doFilter(request, response);
        return; // 直接返回
    }
}
Response<String> error = securityCheck(httpRequest, httpResponse);
```

注意，此时`ignoreUrl`只要包含在请求地址中，就会直接跳过

很显然，这只适合部分地址，比如`login`，`api-docs`等，以及那些整体前缀都需要忽略的

## URI和HttpMethod

但是，针对以下场景是存在问题的

比如，某一个接口类

```java
@RequestMapping("/web_api/v1")
class A {
	@RequestMapping("/a", HttpMethod.GET)
    public void a1(){
    }
    
    @RequestMapping("/a", HttpMethod.POST)
    public void a2(){
    }
    
    @RequestMapping("/a", HttpMethod.PUT)
    public void a3(){
    }
}
```

很显然，这是对某个数据源的查询，新建和编辑，用不同的`HttpMethod`来区分（参考`Restful`）

因此，如果仅仅想开放查询接口，也就是路由是`/web_api/v1/a`并且`HttpMethod`是`GET`

那么在`doFilter`中可以写

```java
for (String ignore : IGNORE_URL) {
    if (httpRequest.getRequestURI().indexOf(ignore) > 0 && httpRequest.getRequestMethod().equals(HttpMethod.GET)) {
        chain.doFilter(request, response);
        return; // 直接返回
    }
}
Response<String> error = securityCheck(httpRequest, httpResponse);
```

`index of`还是有问题

1. `web_api/v1/a` `GET`
2. `web_api/v1/a/b` `GET`
3. `web_api/v1/a/{id}` `GET`

如果仅仅想开放`1`，而不开放`2,3`，这肯定不行

对于`URI`的判断需要制定规则

```java
private boolean match(String[] filterUrl, String[] httpUrl) {
    if (filterUrl.length != httpUrl.length) {
        return false;
    }
    for (int i = 0; i < filterUrl.length; i++) {
        if (filterUrl[i].startsWith("{") && filterUrl[i].endsWith("}")) {
            continue;
        }
        if (!filterUrl[i].equals(httpUrl[i])) {
            return false;
        }
    }
    return true;
}
```

这边对两个`url`都对`/`进行分割，分别比对两个`url`的每一部分

`web_api/v1/a`

`web_api/v1/a/1`

`web_api/v1/a/{id}`

对于占位符`{}`不做比较，也不进行类型比较，直接由`SpringWeb`进行处理

## 注解拉取接口

这样除了需要配置`URI`，还需要配置`HttpMethod`，这是一个叉乘的过程，如果每一次枚举太麻烦

增加注解`LoginFree`，`Filter`层拉取一下所有标注了`LoginFree`的注解，获取`RequestMapping`中的`UrI`和`Method`

## 完整代码

```java
@Documented
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface FreeLogin {
}
```

```java
@FreeLogin
@RequestMapping("/web_api/v1")
class A {
    @FreeLogins
	@RequestMapping("/a", HttpMethod.GET)
    public void a1(){
    }
    
    @RequestMapping("/a", HttpMethod.POST)
    public void a2(){
    }
    
    @RequestMapping("/a", HttpMethod.PUT)
    public void a3(){
    }
}
```

```java
private static final Map<String, String> LOGIN_FREE_URL_PATTERN = new HashMap<>();
static {
    initFreeLogin();
}
private static void initFreeLogin() {
    // 获取所有的注解
    Set<Class<?>> classSet = ClassUtil.getClasses("com.test.a.b.portal.webapi");
    for (Class<?> clazz : classSet) {
        FreeLogin classFreeLogin = clazz.getDeclaredAnnotation(FreeLogin.class);
        if (null == classFreeLogin) {
            continue;
        }
        RequestMapping classRequestMapping = clazz.getDeclaredAnnotation(RequestMapping.class);
        if (null == classRequestMapping) {
            continue;
        }
        String[] classMappingUrls = classRequestMapping.value();
        Method[] declaredMethods = clazz.getDeclaredMethods();
        for (Method method : declaredMethods) {

            FreeLogin methodFreeLogin = method.getDeclaredAnnotation(FreeLogin.class);
            if (null == methodFreeLogin) {
                continue;
            }

            RequestMapping methodRequestMapping = method.getDeclaredAnnotation(RequestMapping.class);
            if (null == methodRequestMapping) {
                continue;
            }
            String[] methodMappingUrls = methodRequestMapping.value();
            RequestMethod[] methodMappingMethods = methodRequestMapping.method();
            compose(classMappingUrls, methodMappingUrls, methodMappingMethods);
        }
    }
}

private static void compose(String[] classMappingUrls, String[] methodMappingUrls, RequestMethod[] methodMappingMethods) {
    for (String classMappingUrl :
         classMappingUrls) {
        for (String methodMappingUrl :
             methodMappingUrls) {
            for (RequestMethod requestMethod :
                 methodMappingMethods) {
                LOGIN_FREE_URL_PATTERN.put(classMappingUrl + methodMappingUrl, requestMethod.name());
            }
        }
    }
}
```



```java
import org.springframework.stereotype.Component;
import java.io.File;
import java.io.FileFilter;
import java.io.IOException;
import java.net.JarURLConnection;
import java.net.URL;
import java.net.URLDecoder;
import java.util.Enumeration;
import java.util.Iterator;
import java.util.LinkedHashSet;
import java.util.Set;
import java.util.jar.JarEntry;
import java.util.jar.JarFile;

@Component
public class ClassUtil {
    public static Set<Class<?>> getClasses(String pack) {

        Set<Class<?>> classes = new LinkedHashSet<>();
        boolean recursive = true;
        String packageName = pack;
        String packageDirName = packageName.replace('.', '/');
        Enumeration<URL> dirs;
        try {
            dirs = Thread.currentThread().getContextClassLoader().getResources(packageDirName);
            while (dirs.hasMoreElements()) {
                URL url = dirs.nextElement();
                String protocol = url.getProtocol();
                if ("file".equals(protocol)) {
                    System.err.println("file类型的扫描");
                    String filePath = URLDecoder.decode(url.getFile(), "UTF-8");
                    findAndAddClassesInPackageByFile(packageName, filePath, recursive, classes);
                } else if ("jar".equals(protocol)) {
                    JarFile jar;
                    try {
                        jar = ((JarURLConnection) url.openConnection()).getJarFile();
                        Enumeration<JarEntry> entries = jar.entries();
                        while (entries.hasMoreElements()) {
                            JarEntry entry = entries.nextElement();
                            String name = entry.getName();
                            if (name.charAt(0) == '/') {
                                name = name.substring(1);
                            }
                            // 如果前半部分和定义的包名相同
                            if (name.startsWith(packageDirName)) {
                                int idx = name.lastIndexOf('/');
                                if (idx != -1) {
                                    packageName = name.substring(0, idx).replace('/', '.');
                                }
                                if ((idx != -1) || recursive) {
                                    if (name.endsWith(".class") && !entry.isDirectory()) {
                                        String className = name.substring(packageName.length() + 1, name.length() - 6);
                                        try {
                                            classes.add(Class.forName(packageName + '.' + className));
                                        } catch (ClassNotFoundException e) {
                                            e.printStackTrace();
                                        }
                                    }
                                }
                            }
                        }
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        return classes;
    }

    public static void findAndAddClassesInPackageByFile(String packageName, String packagePath, final boolean recursive,
                                                        Set<Class<?>> classes) {
        File dir = new File(packagePath);
        if (!dir.exists() || !dir.isDirectory()) {
            return;
        }
        File[] dirfiles = dir.listFiles(file -> (recursive && file.isDirectory()) || (file.getName().endsWith(".class")));
        for (File file : dirfiles) {
            if (file.isDirectory()) {
                findAndAddClassesInPackageByFile(packageName + "." + file.getName(), file.getAbsolutePath(), recursive,
                                                 classes);
            } else {
                String className = file.getName().substring(0, file.getName().length() - 6);
                try {
                    classes.add(
                        Thread.currentThread().getContextClassLoader().loadClass(packageName + '.' + className));
                } catch (ClassNotFoundException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @SuppressWarnings({"rawtypes", "unchecked"})
    public static Set<Class<?>> getByInterface(Class clazz, Set<Class<?>> classesAll) {
        Set<Class<?>> classes = new LinkedHashSet<Class<?>>();
        if (!clazz.isInterface()) {
            try {
                Iterator<Class<?>> iterator = classesAll.iterator();
                while (iterator.hasNext()) {
                    Class<?> cls = iterator.next();
                    if (clazz.isAssignableFrom(cls)) {
                        if (!clazz.equals(cls)) {
                            classes.add(cls);
                        } else {

                        }
                    }
                }
            } catch (Exception e) {
                System.out.println("出现异常");
            }
        }
        return classes;
    }
}
```

