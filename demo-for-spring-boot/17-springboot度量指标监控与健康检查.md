# springboot度量指标监控与健康检查

### 使用 Actuator 检查与监控

1. 添加依赖 pom.xml

   ```xml
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

2. 配置application.yaml

   ```yaml
   management:
     endpoints:
       web:
         base-path: /actuator # 配置访问端点的根路径
         exposure:
           # 配置开启其他端点的URI
           include: '*' # 开启所有的端点 * ，或指定端点访问：如beans
           exclude: env # 在所有的开启端点中排除 env，beans，info
   ```

### 可视化监控应用 Spring Boot Admin

- Spring Boot Admin的使用是需要建立服务端与客户端
- 服务端：独立的项目，会将搜索到的数据在自己的图形界面显示
- 客户端：需要监控的项目
- 对应关系：一个服务器监控多个客户端

#### 搭建服务端

- 添加项目

- 添加依赖 pom.xml

  ```xml
  <!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-server -->
  <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-server</artifactId>
      <version>2.2.3</version>
  </dependency>
  ```

- 修改配置文件application.properties

  ```properties
  server.port=9000
  ```

- 修改启动类

  ```java
  @SpringBootApplication
  @EnableAdminServer // 开启Spring Boot Admin 服务端
  public class DemoForSpringBootAdminApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(DemoForSpringBootAdminApplication.class, args);
      }
  
  }
  ```

### 客户端

- 在pom.xml中添加依赖：

  ```xml
  <!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-client -->
  <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-client</artifactId>
      <version>2.2.3</version>
  </dependency>
  ```

- 修改配置文件

  ```yaml
  spring:
    boot:
      admin:
        client:
          url: http://localhost:9000 #指定服务端的url
  ```

  