# Spring Boot整合jdbc

### 添加依赖pom.xml

```xml
        <!-- mysql驱动器 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!-- jdbc -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```

### 配置数据源

#### 通过自定义配置文件方式配置数据源信息

##### 通过@PropertySource 以及 @Value 注解读取配置文件

1. 添加Druid数据源依赖

   ```xml
           <!-- Druid数据源依赖-->
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>druid</artifactId>
               <version>1.1.20</version>
           </dependency>
   ```

2. 添加properties文件：config/jdbc.properties

   ```prop
   jdbc.driverClassName = com.mysql.cj.jdbc.Driver
   jdbc.url = jdbc:mysql://localhost:3306/demo_for_java?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=false
   jdbc.username = root
   jdbc.password = 1234 
   ```

3. 创建配置类

   ```java
   // 数据源的配置
   @Configuration
   // 加载指定的配置文件，spring框架提供的注解
   @PropertySource(name = "dataSource", value = "classpath:/config/jdbc.properties", ignoreResourceNotFound = false)
   public class JdbcConfig {
       @Value("${jdbc.username}")
       private String username;
       @Value("${jdbc.password}")
       private String password;
       @Value("${jdbc.url}")
       private String url;
       @Value("${jdbc.driverClassName}")
       private String driverClassName;
   
       /**
        * 实例化Druid
        */
       @Bean
       public DataSource getDataSource() {
           DruidDataSource source = new DruidDataSource();
           source.setPassword(password);
           source.setUsername(username);
           source.setDriverClassName(driverClassName);
           source.setUrl(url);
           return source;
       }
   }
   ```

##### 通过@ConfigurationProperties（spring boot提供的注解）注解读取配置文件

1. 在application.properties中配置jdbc连接

   ```properties
   jdbc.driverClassName = com.mysql.cj.jdbc.Driver
   jdbc.url = jdbc:mysql://localhost:3306/demo_for_java?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=false
   jdbc.username = root
   jdbc.password = 1234 
   ```

2. 添加配置信息实体entity.JdbcProperties

   ```java
   // 是Spring Boot的注解，不能读取其他配置文件，只能读取Spring Boot的application配置文件
   // ConfigurationProperties是利用setter注入的，所以需要添加setter方法
   @ConfigurationProperties(prefix = "jdbc")
   @Setter
   @Getter
   public class JdbcProperties {
       private String username;
       private String password;
       private String url;
       private String driverClassName;
   }
   ```

3. 修改配置类

   ```java
   @Configuration
   @EnableConfigurationProperties(JdbcProperties.class) // 指定加载哪个配置信息属性类
   public class JdbcConfig {
       // 1. 自动注入
       // @Autowired
       // private JdbcProperties jdbcProperties;
       // 2. 通过构造方法注入
       private JdbcProperties jdbcProperties;
   
       public JdbcConfig(JdbcProperties jdbcProperties){
           this.jdbcProperties = jdbcProperties;
       }
       /**
        * 实例化Druid
        */
       @Bean
       public DataSource getDataSource() {
           DruidDataSource source = new DruidDataSource();
           source.setPassword(this.jdbcProperties.getPassword());
           source.setUsername(this.jdbcProperties.getUsername());
           source.setDriverClassName(this.jdbcProperties.getDriverClassName());
           source.setUrl(this.jdbcProperties.getUrl());
           return source;
       }
   }
   ```

##### 优雅的使用@ConfigurationProperties

无需使用配置类实体，直接使用@ConfigurationProperties注解，将该注解注解在方法上，完成自动配置。

1. 在application.properties中配置jdbc连接

      ```properties
      jdbc.driverClassName = com.mysql.cj.jdbc.Driver
      jdbc.url = jdbc:mysql://localhost:3306/demo_for_java?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=false
      jdbc.username = root
      jdbc.password = 1234 
      ```

2. 创建配置类JdbcConfig

   ```java
   @Configuration
   public class JdbcConfig {
       @Bean
       @ConfigurationProperties(prefix = "jdbc")
       public DataSource getDataSource() {
           DruidDataSource source = new DruidDataSource();
           return source;
       }
   }
   ```

#### 通过Spring Boot配置文件配置数据源

- 在Spring Boot 1.x版本中的Spring-boot-starter-jdbc启动器中默认使用的是org.apache.tomcat.pool.DataSource作为数据源
- 在Spring Boot 1.x版本中的Spring-boot-starter-jdbc启动器中默认使用的是org.zaxxer.hikariDataSource作为数据源

1. 使用Spring Boot默认的HikariDataSource数据源

   ```properties
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   spring.datasource.url=jdbc:mysql://localhost:3306/demo_for_java?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC&useSSL=false
   spring.datasource.username=root
   spring.datasource.password=1234
   ```

2. 使用第三方的Druid数据源

   ```properties
   spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
   ```

### 使用JDBC操作数据库

1. 添加User表

   ```sql
   DROP TABLE IF EXISTS `user`;
   CREATE TABLE `user`  (
     `userid` int(0) NOT NULL AUTO_INCREMENT,
     `username` varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
     `usersex` varchar(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
     PRIMARY KEY (`userid`) USING BTREE
   ) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;
   ```

2. 添加POJO

   ```java
   @Setter
   @Getter
   @NoArgsConstructor
   public class User {
       private Integer userId;
       private String userName;
       private String userSex;
   }
   ```

3. 页面

   ```html
       <form action="@{/user/addUser}" method="post">
           <label for="name">用户姓名:</label>
           <input id="name" name="name" value="name"><br/>
           <label for="sex">用户性别:</label>
           <input id="sex" name="sex" value="sex"><br/>
           <input type="submit" value="提交">
       </form>
   <!-- 再创建一个成功、一个失败页面 -->
   ```

4. Controller

   ```java
   // Page跳转
   @Controller
   public class PageController {
       @RequestMapping("/{page}")
       public String showPage(@PathVariable String page){
           return page;
       }
   }
   // User功能Controller
   @Controller
   @RequestMapping("/user")
   public class UserController {
       @Autowired
       private UserService userService;
   
       @PostMapping("/addUser")
       public String addUser(@RequestParam String name,
                             @RequestParam String sex) {
           User user=new User();
           user.setUserName(name);
           user.setUserSex(sex);
           try {
               userService.addUser(user);
           } catch (Exception e) {
               e.printStackTrace();
               return "error";
           }
           return "redirect:/success";
       }
   }
   ```

5. UserService

   ```java
   // interface
   public interface UserService {
       void addUser(User user);
   }
   // impl
   @Service
   public class UserServiceImpl implements UserService {
       @Autowired
       private UserDao userDao;
   
       @Override
       @Transactional
       public void addUser(User user) {
           userDao.addUser(user);
       }
   }
   ```

6. UserDao

   ```java
   // interface
   public interface UserDao {
       void addUser(User user);
   }
   // impl
   @Repository // dao层
   public class UserDaoImpl implements UserDao {
       @Autowired
      private JdbcTemplate jdbcTemplate;
       
       @Override
       public void addUser(User user) {
           String sql="insert into user (username,usersex) values (?,?)";
           this.jdbcTemplate.update(sql,user.getUserName(),user.getUserSex());
       }
   }
   ```

### 查询用户

#### 查询所有用户

1. 修改Controller

   ```java
       @RequestMapping("/findAll")
       public String findAll(Model model){
           List<User> users=null;
           try {
               users = userService.findAll();
               model.addAttribute("users",users);
           }catch (Exception e){
               e.printStackTrace();
               return "error";
           }
           return "showUsers";
       }
   ```

2. 修改Service层

   ```java
   public interface UserService {
       void addUser(User user);
       List<User> findAll();
   }
   
   @Service
   public class UserServiceImpl implements UserService {
       @Autowired
       private UserDao userDao;
   
       /**
        * 添加用户
        * @param user
        */
       @Override
       @Transactional
       public void addUser(User user) {
           userDao.addUser(user);
       }
   	/**
   	* 查询所有用户
   	*/
       @Override
       @Transactional
       public List<User> findAll() {
           return userDao.findAll();
       }
   }
   ```

3. 修改Dao（持久）层

   ```java
   public interface UserDao {
       void addUser(User user);
       List<User> findAll();
   }
   
   
   @Repository // dao层
   public class UserDaoImpl implements UserDao {
       @Autowired
      private JdbcTemplate jdbcTemplate;
   
       /**
        * 添加用户
        * @param user
        */
       @Override
       public void addUser(User user) {
           String sql="insert into user (username,usersex) values (?,?)";
           this.jdbcTemplate.update(sql,user.getUserName(),user.getUserSex());
       }
   
       @Override
       public List<User> findAll() {
           String sql = "select * from user";
           // RowMapper：提供结果集的映射
           return this.jdbcTemplate.query(sql, new RowMapper<User>() {
               @Override
               public User mapRow(ResultSet resultSet, int i) throws SQLException {
                   User user=new User();
                   user.setUserId(resultSet.getInt("userid"));
                   user.setUserName(resultSet.getString("username"));
                   user.setUserSex(resultSet.getString("usersex"));
                   return user;
               }
           });
       }
   }
   ```

4. 创建页面查询效果

   ```html
   <table border="1" align="center">
       <tr>
           <th>用户ID</th>
           <th>用户姓名</th>
           <th>用户性别</th>
           <th>操作</th>
       </tr>
       <tr th:each="u : ${users}">
           <th th:text="${u.userId}"></th>
           <th th:text="${u.userName}"></th>
           <th th:text="${u.userSex}"></th>
           <th>
               <a th:href="@{/user/preupdate(id = ${u.userId})}">修改</a>
               <a th:href="@{/user/delete(id = ${u.userId})}">删除</a>
           </th>
       </tr>
   </table>
   ```

### 更新用户

#### 预更新查询

1. 修改Controller

   ```java
       // 预更新用户的查询
       @RequestMapping("/preupdate")
       public String preUpdate(Integer id,Model model){
           try {
               User user = this.userService.getUserById(id);
               model.addAttribute("user",user);
           }catch (Exception e){
               e.printStackTrace();
               return "error";
           }
           return "updateUser";
       }
   ```

2. 修改Service

   ```java
   public interface UserService {
       void addUser(User user);
       List<User> findAll();
       User getUserById(Integer id);
   }
   
   // impl
   @Service
   public class UserServiceImpl implements UserService {
       @Autowired
       private UserDao userDao;
   
       /**
        * 添加用户
        * @param user
        */
       @Override
       @Transactional
       public void addUser(User user) {
           userDao.addUser(user);
       }
   
       @Override
       @Transactional
       public List<User> findAll() {
           return userDao.findAll();
       }
   
       @Override
       @Transactional
       public User getUserById(Integer id) {
           return userDao.selectById(id);
       }
   }
   ```

3. 修改Dao

   ```java
   public interface UserDao {
       void addUser(User user);
       List<User> findAll();
       User selectById(Integer id);
   }
   // impl
   @Repository // dao层
   public class UserDaoImpl implements UserDao {
       @Autowired
      private JdbcTemplate jdbcTemplate;
   
       /**
        * 添加用户
        * @param user
        */
       @Override
       public void addUser(User user) {
           String sql="insert into user (username,usersex) values (?,?)";
           this.jdbcTemplate.update(sql,user.getUserName(),user.getUserSex());
       }
   
       @Override
       public List<User> findAll() {
           String sql = "select * from user";
           // RowMapper：提供结果集的映射
           return this.jdbcTemplate.query(sql, new RowMapper<User>() {
               @Override
               public User mapRow(ResultSet resultSet, int i) throws SQLException {
                   User user=new User();
                   user.setUserId(resultSet.getInt("userid"));
                   user.setUserName(resultSet.getString("username"));
                   user.setUserSex(resultSet.getString("usersex"));
                   return user;
               }
           });
       }
   	// 预更新用户查询
       @Override
       public User selectById(Integer id) {
           User user =new User();
           String sql = "select * from user where userid = ?";
           Object[] arr = new Object[]{id};
           this.jdbcTemplate.query(sql,arr,new RowCallbackHandler() {
               @Override
               public void processRow(ResultSet resultSet) throws SQLException {
                   user.setUserId(resultSet.getInt("userid"));
                   user.setUserName(resultSet.getString("username"));
                   user.setUserSex(resultSet.getString("usersex"));
               }
           });
           return user;
       }
   }
   ```

4. 更新页面 updateUser.html

   ```html
   <form th:action="@{/user/updateUser}">
       <input type="hidden" name="userid" th:value="${user.userId}"> <br>
       姓名：<input type="text" name="userName" th:value="${user.userName}"> <br>
       性别：<input type="text" name="userSex" th:value="${user.userSex}"> <br>
       <input type="submit">
   </form>
   ```

#### 更新用户

1. 修改Controller

   ```java
       @PostMapping("/updateUser")
       public String updateUser(User user){
           try {
               userService.updateUser(user);
           } catch (Exception e) {
               e.printStackTrace();
               return "error";
           }
           return "redirect:/success";
       }
   ```

2. 修改Service

   ```java
       void updateUser(User user);  // 在UserService接口添加
   
       // impl
       @Override
       @Transactional
       public void updateUser(User user) {
           userDao.updateById(user);
       }
   ```

3. 修改Dao层

   ```java
       // UserDao接口
       void updateById(User user);
   
       // impl
       // 更新用户
       @Override
       public void updateById(User user) {
           String sql = "update user set username = ?,usersex=? where userid=?";
           this.jdbcTemplate.update(sql,user.getUserName(),user.getUserSex(),user.getUserId());
       }
   ```

### 删除用户

1. 修改Controller

   ```java
       @GetMapping("/delete")
       public String deleteUser(Integer id){
           try {
               userService.deleteById(id);
           } catch (Exception e) {
               e.printStackTrace();
               return "error";
           }
           return "redirect:/success";
       }
   ```

2. 修改Service

   ```java
   // interface
       void deleteById(Integer id);
   // impl
       @Override
       public void deleteById(Integer id) {
           userDao.deleteById(id);
       }
   ```

3. 修改Dao

   ```java
   // interface
       void deleteById(Integer id);
   // impl
       @Override
       @Transactional 
       public void deleteById(Integer id) {
           String sql = "delete from user where userid = ?";
           this.jdbcTemplate.update(sql,id);
       }
   ```

   