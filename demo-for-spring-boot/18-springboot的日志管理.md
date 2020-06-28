# Spring Boot日志管理

Spring Boot默认使用Logback组件作为日志管理。Logback是由log4j的创始人设计的一个开源日志组件。

在Spring Boot项目中不需要添加额外的添加Logback的依赖，因为在spring-boot-starter或者spring-boot-starter-web中已经包含了Logback的组件。

1. Logback读取配置文件的步骤

   1. 在classpath下查找文件 logback-rest.xml
   2. 如果文件不存在，则查找 logback.xml
   3. 如果两个文件都不存在，Logback使用BasicConfiguration自动对自己进行最小化配置，实现了不需要添加任何配置就能够输出到控制台日志信息

2. 添加Logback配置文件 /resources/logback.xml：

   ```xml
   
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration debug="false">
       <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
       <property name="LOG_HOME" value="${catalina.base}/logs/" />
       
       <!-- 控制台输出 -->
       <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </encoder>
       </appender>
       
       <!-- 按照每天生成日志文件 -->
       <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!--日志文件输出的文件名-->
               <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
               <!--日志文件保留天数-->
               <MaxHistory>30</MaxHistory>
           </rollingPolicy>
           <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
               <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </encoder>
           
           <!--日志文件最大的大小-->
           <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
               <MaxFileSize>10MB</MaxFileSize>
           </triggeringPolicy>
       </appender>
   
       <!-- 日志输出级别 -->
       <root level="INFO">
           <appender-ref ref="STDOUT" />
       </root>
   </configuration>
   ```

3. 使用日志记录：

   ```java
   // Logger和LoggerFactory均为 org.slf4j 包下
   // XX.class：要做日志记录的类
   private final static Logger logger=LoggerFactory.getLogger(XX.class);
   
   logger.info(); // 生成日志
   ```

4. 配置application.yaml：

   ```yaml
   logging:
     level: 
       com: off # 排除com包
   ```

   