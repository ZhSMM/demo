# aop的注解开发

注解使用：

```java
@Aspect //声明该类是一个切面类，会包含切入点（pointcut）和通知（advice）
@Component //声明组件，进入工厂
public class MyAspect{
    // 定义切点
    @Pointcut("execution(* com..*.*(..))")
    public void pc(){}
    
    // 前置通知
    @Before("pc()")
    public void before(JointPoint a){
       	// ....
    }
    
    // 后置通知
    @AfterReturning(value="pc()",returning="ret")
    public void myAfterReturning(JoinPoint a,Object ret){
        // ret：返回值
    }
    
    // 环绕通知
    @Around("pc()")
    public Object myInterceptor(ProceedingJoinPoint p) throws Throwable{
        // 执行目标方法
        Object ret=p.proceed();
        return ret;
    }
    
    // 异常通知
    @AfterThrowing(value="pc()",throwing="ex")
    public void myThrows(JoinPoint jp,Exception ex){
        
    }
}
```

在spring-context.xml中开启xml注解：

```xml
<aop:aspectj-autoproxy/>
```

