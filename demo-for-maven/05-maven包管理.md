# Maven包管理

### 父项目版本管理

1. 建立一个maven工程 pom类型

2. 统一管理依赖和版本号，子工程不会使用所有定义的依赖，子工程使用依赖时无需指定版本号，类似Spring Boot的父项目。

3. 父项目pom.xml

   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
     <modelVersion>4.0.0</modelVersion>
     <groupId>com.spiritmark.cyf</groupId>
     <artifactId>environment</artifactId>
     <version>0.0.1-SNAPSHOT</version>
     <packaging>pom</packaging>
     <!-- 集中定义依赖版本号 -->
       <properties>
           <junit.version>4.10</junit.version>
           <spring.version>4.2.2.RELEASE</spring.version>
           <mybatis.version>3.2.8</mybatis.version>
           <mybatis.spring.version>1.2.2</mybatis.spring.version>
           <mybatis.paginator.version>1.2.15</mybatis.paginator.version>
           <mysql.version>5.1.47</mysql.version>
           <slf4j.version>1.6.4</slf4j.version>
           <jackson.version>2.4.2</jackson.version>
           <druid.version>1.0.9</druid.version>
           <httpclient.version>4.3.5</httpclient.version>
           <jstl.version>1.2</jstl.version>
           <servlet-api.version>2.5</servlet-api.version>
           <jsp-api.version>2.0</jsp-api.version>
           <joda-time.version>2.5</joda-time.version>
           <commons-lang3.version>3.3.2</commons-lang3.version>
           <commons-io.version>1.3.2</commons-io.version>
       </properties>
   
       <!-- 管理jar包，不会引入 ，如果子工程需要哪些jar包，则具体地在子工程中引入，不过不需要写版本号-->
       <dependencyManagement>
           <dependencies>
               <!-- 单元测试 -->
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
                   <version>${junit.version}</version>
                   <scope>test</scope>
               </dependency>
   
   
               <!-- Spring -->
               <dependency>
                   <groupId>org.springframework</groupId>
                   <artifactId>spring-context</artifactId>
                   <version>${spring.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.springframework</groupId>
                   <artifactId>spring-beans</artifactId>
                   <version>${spring.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.springframework</groupId>
                   <artifactId>spring-webmvc</artifactId>
                   <version>${spring.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.springframework</groupId>
                   <artifactId>spring-jdbc</artifactId>
                   <version>${spring.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.springframework</groupId>
                   <artifactId>spring-aspects</artifactId>
                   <version>${spring.version}</version>
               </dependency>
   
   
               <!-- Mybatis -->
               <dependency>
                   <groupId>org.mybatis</groupId>
                   <artifactId>mybatis</artifactId>
                   <version>${mybatis.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.mybatis</groupId>
                   <artifactId>mybatis-spring</artifactId>
                   <version>${mybatis.spring.version}</version>
               </dependency>
   
   
               <!-- MySql -->
               <dependency>
                   <groupId>mysql</groupId>
                   <artifactId>mysql-connector-java</artifactId>
                   <version>${mysql.version}</version>
               </dependency>
   
   		   <!-- sl4j日志管理 -->
               <dependency>
                   <groupId>org.slf4j</groupId>
                   <artifactId>slf4j-log4j12</artifactId>
                   <version>${slf4j.version}</version>
               </dependency>
   
   
               <!-- Jackson Json处理工具包 -->
               <dependency>
                   <groupId>com.fasterxml.jackson.core</groupId>
                   <artifactId>jackson-databind</artifactId>
                   <version>${jackson.version}</version>
               </dependency>
   
   
               <!-- 连接池 -->
               <dependency>
                   <groupId>com.jolbox</groupId>
                   <artifactId>bonecp-spring</artifactId>
                   <version>0.8.0.RELEASE</version>
               </dependency>
   
   
               <!-- httpclient -->
               <dependency>
                   <groupId>org.apache.httpcomponents</groupId>
                   <artifactId>httpclient</artifactId>
                   <version>${httpclient.version}</version>
               </dependency>
   
   
               <!-- JSP相关 -->
               <dependency>
                   <groupId>jstl</groupId>
                   <artifactId>jstl</artifactId>
                   <version>${jstl.version}</version>
               </dependency>
               <dependency>
                   <groupId>javax.servlet</groupId>
                   <artifactId>servlet-api</artifactId>
                   <version>${servlet-api.version}</version>
                   <scope>provided</scope>
               </dependency>
               <dependency>
                   <groupId>javax.servlet</groupId>
                   <artifactId>jsp-api</artifactId>
                   <version>${jsp-api.version}</version>
                   <scope>provided</scope>
               </dependency>
   
   
               <!-- 时间操作组件 -->
               <dependency>
                   <groupId>joda-time</groupId>
                   <artifactId>joda-time</artifactId>
                   <version>${joda-time.version}</version>
               </dependency>
   
   
               <!-- Apache工具组件 -->
               <dependency>
                   <groupId>org.apache.commons</groupId>
                   <artifactId>commons-lang3</artifactId>
                   <version>${commons-lang3.version}</version>
               </dependency>
               <dependency>
                   <groupId>org.apache.commons</groupId>
                   <artifactId>commons-io</artifactId>
                   <version>${commons-io.version}</version>
               </dependency>
   
   
           </dependencies>
       </dependencyManagement>
   
   
       <build>
           <finalName>${project.artifactId}</finalName>
           <plugins>
               <!-- 资源文件拷贝插件 -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-resources-plugin</artifactId>
                   <version>2.7</version>
                   <configuration>
                       <encoding>UTF-8</encoding>
                   </configuration>
               </plugin>
               <!-- java编译插件 -->
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-compiler-plugin</artifactId>
                   <version>3.2</version>
                   <configuration>
                       <source>1.8</source>
                       <target>1.8</target>
                       <encoding>UTF-8</encoding>
                   </configuration>
               </plugin>
           </plugins>
           <pluginManagement>
               <plugins>
                   <!-- 配置Tomcat插件 -->
                   <plugin>
                       <groupId>org.apache.tomcat.maven</groupId>
                       <artifactId>tomcat7-maven-plugin</artifactId>
                       <version>2.2</version>
                   </plugin>
               </plugins>
           </pluginManagement>
       </build>
   </project>
   ```

4. 新建一个maven工程

   在pom.xml中，继承父工程，依赖去掉版本号。

   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
     <modelVersion>4.0.0</modelVersion>
     <!-- 使用继承 -->
     <parent>
          <groupId>com.spiritmark.cyf</groupId>
         <artifactId>environment</artifactId>
         <version>0.0.1-SNAPSHOT</version>
     </parent>
     <groupId>com.spiritmark.cyf</groupId>
     <artifactId>usermanage</artifactId>
     <version>0.0.1-SNAPSHOT</version>
     <packaging>war</packaging>
     
     <dependencies>
         <dependency>
               <groupId>org.apache.poi</groupId>
               <artifactId>poi</artifactId>
               <version>3.10.1</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
           <dependency>
               <groupId>com.fasterxml.jackson.core</groupId>
               <artifactId>jackson-databind</artifactId>
           </dependency>
           <!-- 时间操作组件 -->
           <dependency>
               <groupId>joda-time</groupId>
               <artifactId>joda-time</artifactId>
           </dependency>
   
           <!-- spring-webmvc -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-webmvc</artifactId>
           </dependency>
           <!-- 切面 -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-aspects</artifactId>
           </dependency>
           <!-- spring的jdbc -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-jdbc</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-test</artifactId>
               <version>4.3.7.RELEASE</version>
           </dependency>
           <!-- mysql的驱动 -->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency>
   
           <!-- mybatis -->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
           </dependency>
   
           <!-- mybatis于spring整合的jar -->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis-spring</artifactId>
           </dependency>
   
           <!-- generator -->
           <dependency>
               <groupId>org.mybatis.generator</groupId>
               <artifactId>mybatis-generator-core</artifactId>
               <version>1.3.5</version>
           </dependency>
   
           <!-- jstl -->
           <dependency>
               <groupId>javax.servlet</groupId>
               <artifactId>jstl</artifactId>
               <version>1.2</version>
           </dependency>
           <!-- pagehelper -->
           <dependency>
               <groupId>com.github.pagehelper</groupId>
               <artifactId>pagehelper</artifactId>
               <version>5.1.2</version>
           </dependency>
   
           <!-- c3p0数据源 -->
           <dependency>
               <groupId>com.mchange</groupId>
               <artifactId>c3p0</artifactId>
               <version>0.9.5.2</version>
           </dependency>
   
           <!-- -->
           <dependency>
               <groupId>javax.servlet</groupId>
               <artifactId>javax.servlet-api</artifactId>
               <version>3.1.0</version>
               <scope>provided</scope>
           </dependency>
   
           <!-- -->
           <dependency>
               <groupId>org.slf4j</groupId>
               <artifactId>slf4j-log4j12</artifactId>
           </dependency>
           <!-- https://mvnrepository.com/artifact/junit/junit -->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <scope>test</scope>
           </dependency>
   
           <!-- 文件上传 -->
           <dependency>
               <groupId>commons-fileupload</groupId>
               <artifactId>commons-fileupload</artifactId>
               <version>1.3.1</version>
           </dependency>
   
   
           <dependency>
               <groupId>org.apache.shiro</groupId>
               <artifactId>shiro-core</artifactId>
               <version>1.4.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.shiro</groupId>
               <artifactId>shiro-web</artifactId>
               <version>1.4.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.shiro</groupId>
               <artifactId>shiro-ehcache</artifactId>
               <version>1.4.0</version>
           </dependency>
           <dependency>
               <groupId>org.apache.shiro</groupId>
               <artifactId>shiro-spring</artifactId>
               <version>1.4.0</version>
           </dependency>
     </dependencies>
   </project>
   ```

5. 插件配置

   ```xml
   <build>
         <plugins>
             <!-- 配置tomcat插件 -->
             <plugin>
                 <groupId>org.apache.tomcat.maven</groupId>
               <artifactId>tomcat7-maven-plugin</artifactId>
               <!-- tomcat配置 -->
               <configuration>
                   <!-- 端口号 -->
                   <port>8001</port>
                   <!-- 项目的路径 -->
                   <path>/</path>
               </configuration>
             </plugin>
         </plugins>
     </build>
   ```

### 原文链接

- https://blog.csdn.net/qq_42897427/article/details/104826533