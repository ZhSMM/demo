# Spring Boot项目中访问静态资源

在Spring Boot项目中没有常规web开发的WebContent（WebApp），只有src目录。src/main/resources目录下存在两个文件夹：

- static：存放静态资源，可以直接访问
- template：存放动态页面，默认使用Thymeleaf作为视图层技术

### 访问其他位置的静态资源

- classpath:/META-INF/resources
- classpath:/resources/
- classpath:/static/
- classpath:/public/

### 自定义静态资源位置

注意：一旦自定义了静态资源位置，默认配置会失效；

```yaml
spring:
  resources:
    static-locations: /static/,/other/
```

