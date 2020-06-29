# druid连接池 

在mybatis中使用 druid 连接池。

1. 引入 druid 依赖：

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
       <version>1.1.23</version>
   </dependency>
   ```

2. 创建DruidDataSourceFactory

   > MyDruidDataSourceFactory 并继承 PooledDataSourceFactory ，并替换数据源

   ```java
   import com.alibaba.druid.pool.DruidDataSource;
   import org.apache.ibatis.datasource.pooled.PooledDataSourceFactory;
   
   public class MyDruidDataSourceFactory extends PooledDataSourceFactory {
       public MyDruidDataSourceFactory() {
           /**
            * 替换数据源
            */
           this.dataSource = new DruidDataSource();
       }
   }
   ```

   修改mybatis-config.xml，使用 MyDruidDataSourceFactory 连接池，以及相应的 driver 改为 driverClass ，url 改为 jdbcUrl：

   ```xml
   <dataSource type="top.songfang.dataSource.MyDruidDataSourceFactory">
       <property name="driverClass" value="${jdbc.driver}"/>
       <property name="jdbcUrl" value="${jdbc.url}"/>
       <property name="username" value="${jdbc.username}"/>
       <property name="password" value="${jdbc.password}"/>
   </dataSource>
   ```

   