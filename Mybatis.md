# Mybatis

## 动态sql

### <sql>

将重复使用的sql语句包起来，用id标志，当sql语句中使用到这个片段时，可以用<include refid="">

将这个<sql>标签中的内容引用过来

```xml
<sql id="UserSeven_Colums" >
    SELECT id,name,gender,regist_time
</sql>
```

```xml
<select id="queryAllUser" resultType="Userseven">
        <include refid="UserSeven_Colums"/>
        FROM userseven
    </select>
```

<if>

可以帮助拼接多个条件，一般要和<where>标签一起用，因为<if>标签只能判断这个标签能不能符合拼接的要求，不能将and 或者or自动去除。

```xml
<!--  List<Userseven>  queryByIdAndNameAndGender(@Param("userid")Integer id,@Param("username")String name,@Param("usergender")Integer gender);-->
    <select id="queryByIdAndNameAndGender" resultType="Userseven">
        <include refid="UserSeven_Colums"/>
        from userseven
      <where>
        <if test="userid!=null and userid>0">
            id=#{userid}
        </if>
        <if test="username!=null">
            and  name=#{username}
        </if>
        <if test="usergender!=null">
            and gender=#{usergender}
        </if>
      </where>
    </select>
```

<where>

这个标签主要有两个作用，

1.代替sql语句中的where关键字

2.在多条件拼接时，将第一个能拼接到sql语句中的条件**前面**多余的and或者or去掉 ，见上一段代码

```java
//把这个and或者or放到一个条件后面，<where>就去不掉了，会报错，见下面的代码，赋值时usergender不给值
//日志中的sql语句是：SELECT id,name,gender,regist_time from userseven WHERE id=? and name=? and 
```



```xml

<!--  List<Userseven>  queryByIdAndNameAndGender(@Param("userid")Integer id,@Param("username")String name,@Param("usergender")Integer gender);-->
    <select id="queryByIdAndNameAndGender" resultType="Userseven">
        <include refid="UserSeven_Colums"/>
        from userseven
      <where>
        <if test="userid!=null and userid>0">
            id=#{userid} and
        </if>
        <if test="username!=null">
              name=#{username} and
        </if>
        <if test="usergender!=null">
             gender=#{usergender}
        </if>
      </where>
    </select>
```

<foreach>

用于批量操作的标签

6个属性值：	

```
	1. collection标签选择被<foreach>遍历集合类型
	2. item循环变量,遍历的一条是#{item}
	3. open：循环输出到SQL语句中的整个内容以什么开头
	4. close：循环输出到SQL语句中的整个内容的内容以什么结尾
	5. separate：循环输出到SQL语句中的整个内容每个值用什么分隔
	6.index：遍历次数
```

批量添加

```xml
 <!-- int insertUserBatch(List<Userseven>users);
    INSERT INTO testimport (name, message)
VALUES
('testname', 'jfksdfkdsfjksadljfkdsfjsdlafjdaslkfjasfd'),
('testname', 'jfksdfkdsfjksadljfkdsfjsdlafjdaslkfjasfd'),
('testname', 'jfksdfkdsfjksadljfkdsfjsdlafjdaslkfjasfd');-->
    <insert id="insertUserBatch">
        insert into userseven
        (name, gender, regist_time)
        values
        <foreach collection="list" item="user" separator="," close="" open="" >
            (#{user.name},#{user.gender},#{user.registTime})
        </foreach>

    </insert>

```

测试类

```Java 

    @Test
    public void insertUserBatchTest() throws IOException
    {
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        SevenMapper sevenMapper = sqlSession.getMapper(SevenMapper.class);
        List<Userseven> users = new ArrayList<>();
        for (int i = 0; i < 10; i++)
        {
            Date da = new Date();
            Userseven userseven = new Userseven(null, "某某" + i, 0, null, da);
            users.add(userseven);
        }
        sevenMapper.insertUserBatch(users);
        sqlSession.commit();
    }
```

批量查询

```xml

    <!--   //批量查询
    List<Userseven> queryByForeach(List nums);
    WHERE column IN (value1,value2,...)
WHERE column NOT IN (value1,value2,...)-->
    <select id="queryByForeach" resultType="Userseven">
        <include refid="UserSeven_Colums"/>
        from
        userseven
        where id in
        <foreach collection="list" open="(" close=")" separator="," item="id">
            #{id}
        </foreach>
    </select>


```

测试类

```java

    @Test
    public void queryByForeach() throws IOException
    {
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        SevenMapper sevenMapper = sqlSession.getMapper(SevenMapper.class);
        List a = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
        List<Userseven> usersevens = sevenMapper.queryByForeach(a);
        System.out.println(usersevens);
        //sqlSession.commit();

    }
```

