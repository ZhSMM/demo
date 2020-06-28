# Spring Boot热部署

### 通过DevTools工具实现热部署

1. 修改pom.xml文件

   ```xml
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-devtools</artifactId>
               <scope>runtime</scope>
               <optional>true</optional>
           </dependency>
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
                   <configuration>
                       <fork>true</fork>
                   </configuration>
               </plugin>
   		</plugins>
       </build>
   ```

2. 配置Idea

   1. 开启自动编译：

       File > settings > Build,Execution,..  > Compiler > Build project automatically

   2. 设置Idea的Registry：

      - 快捷键：Ctrl + Shift + Alt + "/" 
      - 选择第一项  "Registry"
      - 勾选 compiler.automake.allow.when.app.running
      - 重启 idea