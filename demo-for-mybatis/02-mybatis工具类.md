# mybatis工具类 

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MyBatisUtil {
    private static SqlSessionFactory sqlSessionFactory;

    /**
     * 创建ThreadLocal绑定当前线程中的SqlSession对象
     */
    private static final ThreadLocal<SqlSession> tl = new ThreadLocal<>();

    static {
        // 1.加载配置文件
        try (InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml")) {
            // 2.获取SqlSessionFactory
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取sqlSession
     * sqlSession属于线程唯一，全局不唯一的
     *
     * @return
     */
    public static SqlSession openSession() {
        SqlSession sqlSession = tl.get();
        if (sqlSession == null) {
            sqlSession = sqlSessionFactory.openSession();
            tl.set(sqlSession);
        }
        return sqlSession;
    }

    /**
     * 事务提交
     */
    public static void commit() {
        SqlSession sqlSession = openSession();
        sqlSession.commit();
        closeSession();
    }

    /**
     * 事务回滚
     */
    public static void rollback() {
        SqlSession sqlSession = openSession();
        sqlSession.rollback();
        closeSession();
    }

    /**
     * 资源释放
     */
    public static void closeSession() {
        SqlSession sqlSession = openSession();
        sqlSession.close();
    }

    /**
     * 获得mapper对象
     *
     * @param mapper
     * @param <T>
     * @return
     */
    public static <T> T getMapper(Class<T> mapper) {
        SqlSession sqlSession = openSession();
        return sqlSession.getMapper(mapper);
    }
}
```

