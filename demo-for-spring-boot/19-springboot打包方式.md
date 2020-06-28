# Spring Boot打包方式

打包插件：

```xml
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

打包：

maven > Lifecycle >  运行install

运行：

java -jar 打好的包