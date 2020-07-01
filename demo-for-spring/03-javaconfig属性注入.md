# Java注解开发

### 声明Bean

> 用于替换自建类型组件的`<bean...>`标签；可以更快速的声明bean
>
> 声明Bean的标签：
>
> - @Service：业务类专用
> - @Repository：dao层专用
> - @Controller：web层专用
> - @Component：通用
>
> 控制bean的创建模式：@Scope

示例：

```java
@Service // 等效于<bean id="userServiceImpl" class="xxx.UserServiceImpl"/>
@Service("userService") //等效于<bean id="userService" class="xxx.UserServiceImpl"/>
public class UserServiceImpl implements UserService{ 
    //.....
}
```

### 注入

> 用于完成bean中属性值的注入
>
> - @Autowired：Spring框架提供，基于类型自动注入
> - @Resource：java提供，基于名称自动注入
> - @Qualifier("userDao")：限定要自动注入的bean的id，一般和@Autowired连用
> - @Value：注入简单数据类型（jdk8种+String）

使用@Autowired自动注入时，如果该类型存在多个bean，可以使用

```java
// 基于类型自动注入，并且挑选 id="userDao" 的bean
@Autowired
@Qualified("userDao")
private UserDao userDao;

// 基于名称自动注入
@Resource(name="userDao")
private UserDao userDao;
```

### 基于事务的注解

> 用于控制事务切入：@Transactional
>
> - 使用该注解可以省略xml配置中的`<tx:advice...` 和 `<aop:config...`
> - 该注解可以注解在class上表示class中的每个方法都切入事务，有自己事务控制的除外；注解在方法上表示该事务控制仅对该方法生效

### 注解发现

注解发现可以使用xml配置和JavaConfig类：

- spring-context.xml配置：

  ```xml
  <!-- 
  	开启包扫描，扫描有注解的类生成bean，base-package指定基础包，可以指定多个，用逗号隔开
  -->
  <context:component-scan base-package="xx.xx,xx.xxx"/>
  
  <!-- 1.引入一个事务管理器，其中依赖dataSource，借以获得连接，进而控制事务逻辑 -->
  <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  <!--
  	告知Spring，@Transactional在定制事务时，基于txManager=DataSourceTransactionManager
  -->
  <tx:annotation-driven transaction-manager="txManager"/>
  ```

- 使用JavaConfig：使用@Configuration注解

