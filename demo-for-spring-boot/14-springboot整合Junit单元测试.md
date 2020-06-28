# Spring Boot整合Junit

Spring Boot 2.x 使用Junit5 作为测试平台

### 修改pom.xml添加test依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <!-- junit-vintage-engine 提供了Junit3 与Junit4的运行平台-->
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

### Spring Boot使用Junit4

1. pom.xml依赖中将排除vintage-engine的依赖注释掉

2. 使用：

   ```java
   @RunWith(SpringJUnit4ClassRunner.class)
   @SpringBootTest(classes = DemoForSpringBootMybatisApplication.class)
   public class UserTest {
       @Test
       void test(){
           
       }
   }
   ```

### Spring Boot使用Junit5

```java
@SpringBootTest
class UserTest {

    @Test
    void contextLoads() {
    }

}
```

