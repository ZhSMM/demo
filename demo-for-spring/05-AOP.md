# AOP

### 代理模式

1. 静态代理

2. 动态代理：

   - JDK动态代理实现（基于接口）

     ```java
     public class JdkProxy implements InvocationHandler {
         /**
          * 目标对象
          */
         private Object target;
     
         public JdkProxy(Object target) {
             this.target = target;
         }
     
         @Override
         public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
             before();
             Object result=method.invoke(target,args);
             after();
             return result;
         }
     
         /**
          * 前置业务操作
          */
         private void after() {
             System.out.println("前置业务操作！");
         }
     
         /**
          * 后置业务操作
          */
         private void before() {
             System.out.println("后置业务操作！");
         }
     
         @SuppressWarnings("unchecked")
         public <T> T getProxy(){
             return (T)Proxy.newProxyInstance(target.getClass().getClassLoader(),
                     target.getClass().getInterfaces(),this);
         }
     }
     
     // 测试
         @Test
         public void test(){
             JdkProxy proxy=new JdkProxy(new OrderServiceImpl());
             OrderService orderService=proxy.getProxy();
             orderService.order();
         }
     ```

   - CGlib动态代理实现（基于继承）

     ```java
     public class CglibProxy implements MethodInterceptor {
         private static CglibProxy instance=new CglibProxy();
     
         // 单例模式
         private CglibProxy() {
         }
     
         public static CglibProxy getInstance(){
             return instance;
         }
     
         @Override
         public Object intercept(Object o, Method method, Object[] objects, 
                                 MethodProxy methodProxy) throws Throwable {
             before();
             Object result= methodProxy.invokeSuper(o,objects);
             after();
             return null;
         }
     
         private void after() {
             System.out.println("后置方法!");
         }
     
         private void before() {
             System.out.println("前置方法！");
         }
     
         @SuppressWarnings("unchecked")
         public <T> T getProxy(Class<T> clazz){
             return (T) Enhancer.create(clazz,this);
         }
     }
     
     // 测试
         @Test
         public void testCglib(){
             OrderService orderService=CglibProxy.getInstance().getProxy(OrderServiceImpl.class);
             orderService.order();
         }
     ```

### 面向切面编程

> Spring AOP：通过动态代理为原始类的方法添加辅助功能

##### 使用

1. pom.xml引入依赖：

    ```xml
    <!-- 引入spring-context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.1.6.RELEASE</version>
</dependency>
    
    <!-- 引入spring-aspects -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.1.6.RELEASE</version>
    </dependency>
    ```
    
2. 定义通知类（添加额外功能），通过继承相应的接口使之成为相应的通知类：

    - MethodBeforeAdvice：前置通知
    - AfterAdvice：后置通知
    - AfterReturningAdvice：有异常不执行，方法会因异常而结束，无返回值
    - ThrowsAdvice：异常通知
    - MethodInterceptor：环绕通知

3. spring-context.xml文件进行配置：

    1. 引入aop命名空间：

       ```xml
       <beans xmlns:aop="http://www.springframework.org/schema/aop"
              xsi:schemaLocation="http://www.springframework.org/schema/aop                           https://www.springframework.org/schema/aop/spring-aop.xsd">
       </beans>
       ```

    2. 进行配置

       > 切点表达式：根据表达式通配匹配切入点

       1. 匹配参数：`execution(* *(top.songfang.entity.User))`
       2. 匹配方法名：
          - 无参：`execution(* save())`
          - 任意参数：`execution(* save(..))`
       3. 匹配返回值类型：`execution(top.songfang.entity.User *(..))`
       4. 匹配类名：`execution(* top.songfang.UserService.*(..))`
       5. 匹配包名：`execution(* top.songfang.*.*(..))`
       6. 匹配包名、以及子包名：`execution(* top.songfang..*.*(..))`

       ```xml
       <!-- 定义目标类 -->
       <bean id="orderService" class="top.songfang.service.impl.OrderServiceImpl"/>
       <!-- 定义通知 -->
       <bean id="before" class="top.songfang.proxy.Before"/>
       <!-- 编制 -->
       <aop:config>
           <!-- 切点定义 -->
           <!-- execution(修饰符 返回值 包.类.方法名(参数列表)) -->
           <aop:pointcut id="myPointerCut" expression="execution(* order(..))"/>
           <!-- 组装切面 -->
           <aop:advisor advice-ref="before" pointcut-ref="myPointerCut"/>
       </aop:config>
       ```

    3. 测试

       ```java
       @Test
       public void testAop(){
           ApplicationContext context=new ClassPathXmlApplicationContext("/spring-context.xml");
         
           OrderService orderService= (OrderService) context.getBean("orderService");
           // 输出：class com.sun.proxy.$Proxy6，说明使用了动态代理
           System.out.println(orderService.getClass());
           orderService.order();
       }
       ```

4. JDK和CGLIB的选择：

    > Spring底层，包含了jdk代理和cglib代理两种代理生成机制
    >
    > 基本规则是：
    >
    > - 目标业务类如果有接口则用JDK代理，没有接口则用CGLIB代理
    > - 可以通过设置`<aop:config proxy-target-class="true">` 来明确规定使用CGLIB代理

    ```java
    class DefaultAopProxyFactory{
        // 该方法中明确定义了 JDK代理和CGlib代理的选取规则
        public AopProxy createAopProxy(){
            ...
        }
    }
    ```

5. 后处理器：

    >Spring中定义了许多后处理器：每个bean在创建完成之前，都会有一个后处理过程，即再加工，对bean做出相关改变和调整。
    >
    >在Spring AOP中，就有一个专门的后处理器，负责通过原始业务组件（Service），再加工得到一个代理组件。

    Bean的生成过程：构造 -> set -> 后处理 -> init -> 后处理 -> destory

    后处理器可以实现 BeanPostProcessor 接口实现，示例：

    ```java
    public class MyBeanPostProcessor implements BeanPostProcessor {
        @Override
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("前置处理");
            System.out.println("bean name:"+beanName);
            return null;
        }
    
        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("后置处理");
            System.out.println("bean name:"+beanName);
            return null;
        }
    }
    // 在spring-context.xml中配置这个bean，就能对所有的bean都进行后置处理
    ```

    

