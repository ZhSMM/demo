# JUnit

1. 导入依赖：

   ```xml
   <dependency>
   	<groupId>org.springframework</groupId>
       <artifactId>spring-test</artifactId>
   </dependency>
   <dependency>
   	<groupId>junit</groupId>
       <artifactId>junit</artifactId>
   </dependency>
   ```

2. 编码

   >可以免去工厂的创建过程；
   >
   >可以直接将要测试的组件注入到测试类

   ```java
   //由SpringJUnit4ClassRunner启动测试
   @RunWith(SpringJUnit4ClassRunner.class)
   //spring的配置文件位置
   @ContextConfiguration(locations="classpath:applicationContext.xml")
   public class SpringTest{
       // 当前测试类会被纳入工厂，所以其中属性可注入
       
       
   }
   ```

   

