# 动态sql 

### sql

用于抽取重复的sql片段，实现复用。

```xml
<mapper namespace="top.songfang.dao.UserDao">
    <sql id="selectUser">
        select id,username,password,gender,registry_time as registryTime
        from t_user
    </sql>
    
    <select id="queryUserByName" resultType="User">
    	<!-- 引入sql -->
        <include refid="selectUser"/>
        where username=#{username}
    </select>
</mapper>
```

### if 和 where

if标签作用：用于判断某个查询是否为空

where标签作用：

1. 补充where关键字
2. 识别where子句中如果以 or 或 and 开头，会将 or或and 去除

示例：

```xml
<select id="queryUserByUser" resultType="User">
    <include refid="selectUser"/>
    <where>
        <if test="id!=null">
            id=#{id}
        </if>
        <if test="username!=null">
            or username=#{username}
        </if>
    </where>
    limit 1;
</select>
```

### set

set作用：

1. 补充set关键字
2. 自动将set子句的最后的逗号去除

```xml
<update id="updateById" parameterType="User">
    update t_user
    <set>
        <if test="username!=null">
            username=#{username},
        </if>
        <if test="password!=null">
            password=#{password},
        </if>
        <if test="gender!=null">
            gender=#{gender},
        </if>
        <if test="registryTime!=null">
            registry_time=#{registryTime}
        </if>
    </set>
    where id=#{id}
</update>
```

### trim

trim：

1. prefix：补充一个前缀的关键字
2. prefixOverrides="and|or"：当子句中以 and或者or开头时将其去除
3. suffixOverrides=","：当子句中以 ，结尾时将其去除

```xml
    <select id="queryUserByUser" resultType="User">
        <include refid="selectUser"/>
<!--        <where>-->
<!--            <if test="id!=null">-->
<!--                id=#{id}-->
<!--            </if>-->
<!--            <if test="username!=null">-->
<!--                or username=#{username}-->
<!--            </if>-->
<!--        </where>-->
        <trim prefix="where" prefixOverrides="and|or">
            <if test="id!=null">
                id=#{id}
            </if>
            <if test="username!=null">
                or username=#{username}
            </if>
        </trim>
        limit 1;
    </select>


    <update id="updateById" parameterType="User">
        update t_user
<!--        <set>-->
<!--            <if test="username!=null">-->
<!--                username=#{username},-->
<!--            </if>-->
<!--            <if test="password!=null">-->
<!--                password=#{password},-->
<!--            </if>-->
<!--            <if test="gender!=null">-->
<!--                gender=#{gender},-->
<!--            </if>-->
<!--            <if test="registryTime!=null">-->
<!--                registry_time=#{registryTime}-->
<!--            </if>-->
<!--        </set>-->
        <trim prefix="set" suffixOverrides=",">
            <if test="username!=null">
                username=#{username},
            </if>
            <if test="password!=null">
                password=#{password},
            </if>
            <if test="gender!=null">
                gender=#{gender},
            </if>
            <if test="registryTime!=null">
                registry_time=#{registryTime}
            </if>
        </trim>
        where id=#{id}
    </update>
```

### foreach

```xml
<!-- 批量增加 -->
<insert id="insertMany" parameterType="java.util.List">
    <!-- insert into t_user values (user1),(user2),(user3) -->
    insert into t_user values
    <foreach collection="list" open="" close="" separator="," item="user9">
        (null,#{user9.username},#{user9.password},#{user9.gender},#{user9.registryTime})
    </foreach>
</insert>

<!-- 批量删除 -->
<delete id="deleteMany" parameterType="java.util.List">
    <!-- delete from t_user where id in (x , x , x, x, x) -->
    delete from t_user where id in
    <foreach collection="list" open="(" close=")" item="ids" separator=",">
        #{ids}
    </foreach>
</delete>
```

| 参数       | 描述     | 取值                                  |
| ---------- | -------- | ------------------------------------- |
| collection | 容器类型 | list、array、map                      |
| open       | 起始符   | (                                     |
| close      | 结束符   | )                                     |
| separator  | 分隔符   | ,                                     |
| index      | 下标号   | 从0开始，依次递增                     |
| item       | 当前项   | 任意名称，循环中通过#{item}表达式访问 |

