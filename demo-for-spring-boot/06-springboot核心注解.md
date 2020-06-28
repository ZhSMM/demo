# Spring Boot核心注解

- @SpringBootApplication：Spring Boot 启动类，等同于 @Configuration + @EnableAutoConfiguration +@ComponentScan 注解的组合；
- @SpringBootConfiguration：@Configuration的派生注解，标注此类是Spring Boot配置类，@Configuration标注此类是Spring 配置类；
- @Configuration：通过对bean对象的操作替代 Spring 中的 xml文件；
- @EnableAutoConfiguration：自动配置的注解，会根据添加的jar依赖自动配置Spring应用，是@AutoConfigurationPackage + @Import({AutoConfigurationImportSelector.class}) 注解的组合；
- @AutoConfigurationPackage：自动注入主类下所有的加了注解的类（@Service、@Controller等），以及配置类@Configuration；
-  @Import({AutoConfigurationImportSelector.class}) ：
  - 直接导入普通的类
  - 导入实现了ImportSelector接口的类
  - 导入实现了ImportBeanDefinitionRegistrar接口的类
- @ComponentScan：组件扫描，可以自动发现和装配一些Bean；
- @ConfigurationPropertiesScan：扫描配置属性
- @EnableConfigurationProperties：使@ConfigurationProperties注解的类生效

