# Spring Boot整合web层技术

### 整合Servlet

#### 通过注解扫描完成Servlet组件的注册：@WebServlet

1. 创建Servlet：

   ```java
   @WebServlet(name = "FirstServlet",urlPatterns = "/first")
   public class DemoServlet01 extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("第一个Servlet启动");
           resp.getWriter().print("Hello");
       }
   }
   ```

2. 修改启动类：

   ```java
   @SpringBootApplication
   @ServletComponentScan // 在Spring Boot启动时会扫描@WebServlet注解，并将该类实例化
   public class DemoForSpringBootBasicApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(DemoForSpringBootBasicApplication.class, args);
       }
   
   }
   ```

#### 通过方法完成Servlet组件的注册

1. 创建Servlet

   ```java
   public class DemoServlet02 extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("第二个Servlet启动");
           resp.getWriter().print("Hello ，I am second");
       }
   }
   ```

2. 创建Servlet配置类

   ```java
   @Configuration
   public class ServletConfig {
       @Bean
       public ServletRegistrationBean getServletRegistrationBean() {
           return new ServletRegistrationBean(new DemoServlet02(), "/second");
       }
   }
   ```

### 整合filter

#### 通过注解扫描完成Filter组件的注册：@WebFilter

1. 创建Filter：

   ```java
   @WebFilter(filterName = "first",urlPatterns = {"/first"})
   public class DemoFilter01 implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           System.out.println("First Filter初始化");
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("进入First Filter");
           filterChain.doFilter(servletRequest,servletResponse);
           System.out.println("离开First Filter");
       }
   
       @Override
       public void destroy() {
           System.out.println("First Filter结束");
       }
   }
   ```

2. 修改启动类：

   ```java
   @SpringBootApplication
   @ServletComponentScan // 在Spring Boot启动时会扫描@WebFilter注解，并将该类实例化
   public class DemoForSpringBootBasicApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(DemoForSpringBootBasicApplication.class, args);
       }
   
   }
   ```

#### 通过方法完成Filter组件的注册

1. 创建Filter

   ```java
   @WebFilter(filterName = "second",urlPatterns = {"/second"})
   public class DemoFilter02 implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
           System.out.println("Second Filter初始化");
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("进入Second Filter");
           filterChain.doFilter(servletRequest,servletResponse);
           System.out.println("离开Second Filter");
       }
   
       @Override
       public void destroy() {
           System.out.println("Second Filter结束");
       }
   }
   public class DemoServlet02 extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           System.out.println("第二个Servlet启动");
           resp.getWriter().print("Hello ，I am second");
       }
   }
   ```

2. 创建Filter配置类

   ```java
   @Configuration
   public class FilterConfig {
       @Bean
       public FilterRegistrationBean getFilterRegistrationBean(){
           FilterRegistrationBean bean = new FilterRegistrationBean(new DemoFilter02());
   //        bean.addUrlPatterns(new String[]{"/second","*.action"});
           bean.addUrlPatterns("/second");
           return bean;
       }
   }
   ```

### 整合listener

#### 通过注解扫描完成Listener组件注册

1. 创建Listener：

   ```java
   @WebListener
   public class DemoListener01 implements ServletContextListener {
       @Override
       public void contextDestroyed(ServletContextEvent sce) {
           
       }
   
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           System.out.println("Servlet Context初始化！");
       }
   }
   ```

2. 修改配置类

   ```java
   @SpringBootApplication
   @ServletComponentScan // 在Spring Boot启动时会扫描@WebListener注解，并将该类实例化
   public class DemoForSpringBootBasicApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(DemoForSpringBootBasicApplication.class, args);
       }
   
   }
   ```

#### 通过方法完成Listener组件注册

1. 编写Listener：

   ```java
   public class DemoListener02 implements ServletContextListener {
       @Override
       public void contextDestroyed(ServletContextEvent sce) {
   
       }
   
       @Override
       public void contextInitialized(ServletContextEvent sce) {
           System.out.println("Servlet Context初始化！");
       }
   }
   ```

2. 配置Listener启动类

   ```java
   @Configuration
   public class ListenerConfig {
       @Bean
       public ServletListenerRegistrationBean getListener(){
           return new ServletListenerRegistrationBean(new DemoListener02());
       }
   }
   ```

   