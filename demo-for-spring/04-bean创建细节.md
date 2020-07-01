# Bean创建细节

### 简单对象的单例、多例模式

> 配置`<bean scope="singleton|prototype" />`
>
> - singleton：默认，每次调用工厂，得到的都是同一个对象
> - prototype：每次调用工厂，都会创建新的对象

单例、多例的使用原则：

- 根据需求场景决定对象的单例、多例模式
- 可以共用：Service、DAO、SqlSessionFactory（或者说所有工厂）
- 不可共用：Connection、SqlSession、ShoppingCart

### FactoryBean创建复杂对象

> 作用：让Spring可以创建复杂对象，或者无法直接通过反射创建的对象
> 
> 比如：数据库连接的Connection对象无法直接通过new进行创建，而是通过 DriverManager.getConnection() 进行创建
> 
>因此，当面对这种使用工厂创建的复杂对象时，Spring提供了一个 `FactoryBean<E>`接口，用于创建复杂bean。

使用方式：
1. 自定义一个class继承FactoryBean接口
2. FactoryBean提供了三个方法：
  1. getObject()：需要通过Spring工厂创建的Bean
  2. getObjectType()：复杂bean的类对象
  3. isSingleton()：该工厂创建的类是否是单例
3. 在spring-context.xml中进行配置

示例，使用Spring创建数据库Connection：
1. 自定义ConnectionFactory实现FactoryBean接口：
    ```java
    public class ConnectionFactory implements FactoryBean<Connection> {
        @Override
        public Class<?> getObjectType() {
            return Connection.class;
        }

        @Override
        public Connection getObject() throws Exception {
            Class.forName("com.mysql.cj.jdbc.Driver");
            return DriverManager.getConnection("jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Shanghai","root","root");
        }

        @Override
        public boolean isSingleton() {
            return false;
        }
    }
    ```
2. 在spring-context.xml中进行配置：
    ```xml
        <!-- 
            1.当从Spring的工厂中，使用getBean以id即"conn"获取Bean时，
              如果是FactoryBean，则实际返回的是工厂Bean的getObject()方法的返回值
            2.如果以id前加"&"符获取，即"&conn"，则获取的是本身工厂类这个bean
        -->
        <bean id="conn" class="top.songfang.entity.ConnectionFactory"/>
    ```

### Spring的工厂特性

1. 饿汉式创建

    > 工厂创建之后，会将Spring配置文件中的所有对象都创建完成（饿汉式）。
    > 
    > 优点：提高程序运行效率，避免多次IO，减少对象创建时间。（概念接近连接
    > 池，一次性创建好，使用时直接获取）

2. 生命周期方法

    > - 自定义初始化方法：添加"init-method"属性，Spring则会在创建
    >   对象之后，调用此方法
    > - 自定义销毁方法：添加"destroy-method"属性，Spring则会在销毁对
    >   象之前调用此方法
    > - 销毁：工厂的close()方法被调用之后，Spring会销毁所有已经创建的单例对象
    > - 分类：singleton对象由Spring容器销毁，Prototype对象由JVM销毁

3. 生命周期注解

    > 初始化注解、销毁注解
    
    - @PostConstruct：初始化
    - @PreDestroy：销毁

4. 生命周期方法

    - 单例：
        > 随工厂启动 创建 -> 构造方法 -> set方法（注入值） -> init（初始化）-> 构建完成 -> 随工厂关闭 销毁
    - 多例：
        > 被使用时 创建 -> 构造方法 -> set方法（注入值） -> init（初始化）-> 构建完成 -> JVM垃圾回收 销毁
