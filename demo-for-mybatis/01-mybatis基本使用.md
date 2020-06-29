# mybatis基本使用 

### 一、导入依赖

pom.xml：

```xml
<!-- mysql依赖 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.20</version>
    <scope>runtime</scope>
</dependency>
<!-- mybatis依赖 -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
```

当使用Spring Boot时，可以使用mybatis-spring-boot-starter依赖：

```xml
<!-- mybatis spring boot starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

### 二、mybatis-config.xml

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 file:// URL 构造的输入流。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。

mybatis-config.xml极简配置：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 导入外部参数 -->
    <properties resource="jdbc.properties"/>

    <!-- 设置别名 -->
    <typeAliases>
        <!-- 单个设置
        <typeAlias type="top.songfang.entity.User" alias="User"/>
        -->
        <!-- 定义实体类所在的package，每个实体类都会自动注册一个别名，别名=类名 -->
        <package name="top.songfang.entity"/>
    </typeAliases>

    <!-- 核心配置信息 -->
    <environments default="development">
        <!-- 数据库相关配置 -->
        <environment id="development">
            <!-- 事务控制类型 -->
            <transactionManager type="JDBC"/>
            <!-- 数据库连接参数 -->
            <dataSource type="org.apache.ibatis.datasource.pooled.PooledDataSourceFactory">
                <property name="driver" value="${jdbc.driver}"/>
                <!-- 由于为xml文件，&需要使用转义字符&amp; -->
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- mapper注册 -->
        <mapper resource="top/songfang/dao/UserDaoMapper.xml"/>
    </mappers>
</configuration>
```

### 三、mapper

当mapper.xml放在src/main/java目录下时，需要配置资源拷贝：

在pom.xml的 build 节点中，增加如下节点：

```xml
<!-- 资源拷贝 -->
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <!-- 默认（新添加则自定义失效） -->
            <include>*.xml</include>
            <!-- 新添加的 */ 代表一级目录，**/代表多级目录 -->
            <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
    </resource>
</resources>
```



```java
// entity
@Data
public class User {
    private Integer id;
    private String username;
    private String password;
    private Boolean gender;
    private Date registryTime;
}
// dao
public interface UserDao {
}
```

UserDaoMapper.xml：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="top.songfang.dao.UserDao">
</mapper>
```

### 四、数据库连接参数的配置方法

1. 直接在 dataSource 的 property 节点中写死，注意，其中 & 符号由于是在 xml 文件中，需要进行转义，即写为 `&amp;` 

2. 使用配置文件 jdbc.properties，配置文件可以放在 resources/ 目录下，然后在 mybatis-config.xml 中使用 properties 节点进行引入，然后在 dataSource 节点中使用 `${jdbc.driver}` 进行对应属性的引入
   
   jdbc.properties：
   
   ```properties
   # mysql8 的数据库驱动
   jdbc.driver=com.mysql.cj.jdbc.Driver
   # mysql8 必须配置 serverTimezone，配置为 Asia/Shanghai ，时区配置不正确导致时间出问题
   jdbc.url=jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
   jdbc.username=root
   jdbc.password=root
   ```
   
   mybatis-config.xml：
   
   ```xml
   <properties resource="jdbc.properties"/>
   
   <!-- 数据库连接参数 -->
   <dataSource type="org.apache.ibatis.datasource.pooled.PooledDataSourceFactory">
       <property name="driver" value="${jdbc.driver}"/>
       <!-- 由于为xml文件，&需要使用转义字符&amp; -->
       <property name="url" value="${jdbc.url}"/>
       <property name="username" value="${jdbc.username}"/>
       <property name="password" value="${jdbc.password}"/>
   </dataSource>
   ```

### 五、参数绑定的四种方法

1. 使用默认的参数描述：
   - arg0，arg1，...
   - param1，param2，...
   
2. 在接口中的方法参数前使用@Param进行命名，使可读性更好

3. 当接口中的参数名比较多时，可以使用Map进行参数传入，传入时使用Map的key

4. 直接传入一个实体作为参数，通过 entity 的属性名进行传参

   UserDao.java：

   ```java
   public interface UserDao {
       /**
        * 通过id查询用户
        *
        * @param id
        * @return
        */
       User queryUserById(@Param("id") Integer id);
   
   
       /**
        * 通过id和username查询用户
        *
        * @param map
        * @return
        */
       User queryUserByIdAndName(Map map);
   
       /**
        * 查询
        * @param user
        * @return
        */
       User queryUserByUser(User user);
   
       /**
        * 模糊查询
        * @param name
        * @return
        */
       List<User> queryUserByName(@Param("name") String name);
   }
   ```

   UserDaoMapper.xml：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="top.songfang.dao.UserDao">
       <!-- 通过id查询数据 -->
       <!-- 
   		mybatis的结果集匹配是根据数据库列名与entity的属性字段来一一映射的，当列名与entity的属性字段名不一致时，会导致无法映射，这时，可以使用 as 对数据查询的结果重命名，然后达到一一匹配的效果
   	-->
       <select id="queryUserById" resultType="User">
           select id,username,password,gender,registry_time as registryTime
           from t_user
           where id = #{id 或者 arg0 或者 param1}
       </select>
   
       <!-- 传入的是Map，可以通过 #{key} 获得相应的 value -->
       <select id="queryUserByIdAndName" resultType="User">
           select id,username,password,gender,registry_time as registryTime
           from t_user
           where id=#{id} and username=#{username}
       </select>
       
       <!-- 传入的是User实体，使用 #{属性名} 即可以获得相应的值 -->
       <select id="queryUserByUser" resultType="top.songfang.entity.User">
           select id,username,password,gender,registry_time as registryTime
           from t_user
           where id=#{id} and username=#{username}
       </select>
   
       <!-- 模糊查询，需要用到数据库的字符串拼接函数concat -->
       <select id="queryUserByName" resultType="top.songfang.entity.User">
           select id,username,password,gender,registry_time as registryTime
           from t_user
           where username like concat('%',#{name},'%')
       </select>
   </mapper>
   ```

### 六、增、删、改事务

在 mybatis 进行事务操作时，需要该事务进行提交或者回滚，才能真正执行。

##### id参数回填

1. id参数为 Autoincrement 主键：

   ```xml
   <insert id="insert" parameterType="User">
       <!-- 
   		主键回填，将新数据的ID，存入java对象
   	-->
       <selectKey order="AFTER" resultType="int" keyProperty="id" keyColumn="id">
           select last_insert_id()
       </selectKey>
       insert into t_user (id,username,password,gender,registry_time)
       value (#{id},#{username},#{password},#{gender},#{registryTime})
   </insert>
   ```

2. id参数为字符串形式，比如 uuid ：

   ```xml
   <insert id="insert" parameterType="Student">
       <!-- 先使用uuid()函数生成uuid，然后使用数据库函数replace替换掉 - ，然后在执行数据库的insert操作 -->
       <selectKey order="BEFORE" keyProperty="id" resultType="string">
           select replace(uuid(),'-','')
       </selectKey>
       insert into t_student
       values (#{id},#{name},#{age})
   </insert>
   ```

### 七、结果集映射

##### 配置别名

在mybatis-config.xml中配置 typeAliases，可以在结果集映射时不需要写类全路径

```xml
<!-- 设置别名 -->
<typeAliases>
    <!-- 单个设置
        <typeAlias type="top.songfang.entity.User" alias="User"/>
        -->
    <!-- 定义实体类所在的package，每个实体类都会自动注册一个别名，别名=类名 -->
    <package name="top.songfang.entity"/>
</typeAliases>
```

##### 结果集映射

1. 使用 as 对查询结果起别名使之和实体属性名一致；

2. 映射结果集：

   ```xml
   <resultMap id="UserMap" type="User">
       <id column="id" property="id"/>
       <id column="username" property="username"/>
       <id column="password" property="password"/>
       <id column="gender" property="gender"/>
       <id column="registry_time" property="registryTime"/>
   </resultMap>
   
   <!-- 模糊查询 -->
   <select id="queryUserByName" resultMap="UserMap">
       select id,username,password,gender,registry_time as registryTime
       from t_user
       where username like concat('%',#{name},'%')
   </select>
   ```

3. 联表查询结果集映射：首先需要在实体中添加一个关联属性，然后再使用结果集进行映射

   ```xml
   <resultMap id="" type="">
   	<!-- 基础属性映射 -->
       <id column="列名" property="属性名"/>
       <result column="列名" property="属性名"/>
       
       <!-- 一对一关联属性映射，可以使用结果集映射或者自建属性 -->
       <association property="关联属性名" javaType="类型" resultMap="UserMap">
           <id column="列名" property="属性名"/>
           <result column="列名" property="属性名"/>
       </association>
       
       <!-- 一对多关联属性映射 -->
       <collection property="关联属性名" ofType="类型">
           <id column="列名" property="属性名"/>
           <result column="列名" property="属性名"/>
       </collection>
   </resultMap>
   ```


### 取值表达式${}和#{}

- `${}`：在mybatis中是直接拼接字符串，因此，当属性名为字符串时，需要用`where username = '${username}'`，使用sql拼接时，有sql注入风险，优点：随意拼接
- `#{}`：在mybatis中是直接使用占位符，规避sql注入风险，使用原则，填充数据，要和列相关，与列数据无关的不能使用占位符，比如`select * from t_user order by ?` ，order = desc，会失败

### 嵌套查询

在查询过程中嵌套另一个查询：

优点：

- 可以复用一些基本查询语句

- 可以设置延迟查询，在子查询的结果被访问到时才进行查询，设置方法：

  在mybatis-config.xml中配置：

  ```xml
  <settings>
      <!-- 设置子查询延迟查询 -->
      <setting name="lazyLoadingEnabled" value="true"/>
  </settings>
  ```

缺点：

- 有可能造成N+1现象：即在进行一堆多的查询时，子查询语句有可能会被执行N次，会给数据库造成压力。

```xml
<!-- 嵌套查询 -->
<resultMap id="sub" type="Sub">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="grade" property="grade"/>
	
    <!-- 
		在collection中，select指定了子查询的语句，column指定的是将本次查询的那一列作为参数传给下一个查询，
		property指定了子查询返回的属性名，ofType指定的是返回子查询返回的类型
	-->
    <collection property="students" ofType="Stu"
                select="queryStuById" column="id">
    </collection>
</resultMap>
<select id="querySubjectById" resultMap="sub">
    select id,name,grade from t_sub
    where id=#{id}
</select>

<select id="queryStuById" resultType="Stu">
    select id,name,sex from t_stu
    where id in
    (select student_id from t_stu_sub
    where subject_id = #{id})
</select>
```



