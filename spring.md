# spring

## spring环境搭建

创建项目

倒入依赖	

```
spring-context依赖

​	<由于这个依赖同时依赖其他依赖，所以target中实际导入了5个依赖包>
```

编写配置文件spring-context.xml文件

建立各种包目录结构，以及需要被代理的实体类

```
这里由于实体类set注入需要set方法，构造器注入需要对应的构造方法，所以导入一个lombok依赖
```

在spring-context.xml文件加入需要的<bean>标签，由spring生成对象

测试

## IOC

IOC是 inverse of control的缩写，表示控制反转，没有这种思想，A类依赖 B类，只能在A类中通过new 创建B对象，这样就造成A类和B类的高度耦合，B类改变就会导致A类发生一些变动

经过IOC转换后，A类虽然还是依赖B类，但是A类中不创建B类的对象，对象有Spring创建好后给A，A和B解耦

如果没有这种思想的情况举例：mybatis中用Userservice层调用UserDao操作数据库，需要在service中为了获取Usermapper做一堆准备工作，而且，如果修改的话，也不容易

```java

public class UserServiceImpl implements UserService
{
    @SneakyThrows
    @Override
    public PageInfo<Userseven> queryAllUser(int pageNumber, int pageSize)
    {
//获取Usermapper
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        //startPage分页助手
       PageHelper.startPage(pageNumber, pageSize);
       //执行查询方法
        List<Userseven> usersevens = userMapper.queryAll();
        //获取pageInfo
        PageInfo pageInfo=new PageInfo(usersevens);
        return pageInfo;
    }
}

```

## DI注入

DI注入有三种方式：

```
set注入
构造注入
自动注入
```

### set注入

set注入注意点：

1.需要有对应的set方法

2.可以将DATA数据类型匹配String：

```xml
<property name="bornDate" value="1990/1/1" /><!--注意格式"/"-->
```

3.特殊的类型注入时，如map ，list，properties，array，set，需要在<property name="属性名">标签中再嵌套标签如<array><value>

4.引入自建类型作为属性，需要要用ref标签赋值，

 

```xml-dtd
 <property name="ud" ref="userDao" /><!--ud属性引用userDao对象-->
```

set注入过程

​	创建实体类，在实体类中，加入set方法，

​	在spring配置文件增加bean标签

### 构造注入

构造注入注意点

2. ```
   1，构造注入一般在set注入不能使用的情况下用，它太依赖构造方法了。如果只给两个属性传值，必须要有对应的构造方法。
   
   2. 用的标签和set注入不同：<constructor-args name="id" value="1234" />
   3. 当给特殊类型注入时如map ，list，properties，array，set，和set注入一样，用小标签给值如<array><value>
   4. 也可以将自己输入的字符串转换成Date
   5. 引入自建类型作为属性，需要要用ref标签赋值，
   ```

   

### 自动注入

在<bean>标签中追加一个属性autowire

autowire有两种取值，

```
byName：根据类中属性名，找同名<bean>标签创造的对象，在创造对象是，将后者作为属性赋值给对象
byType：根据类中属性声明的类型，赵同类型的bean创建的对象，这种方法必须每种属性只有一个对象
```

## 生成对象的单例模式、多例模式

由<bean>中的一个属性scope控制，

```xml
< bean scope=singleton >单例模式（默认）
< bean scope=prototype >多例模式
```

如何选择单例模式还是多例模式

```

```

## factoryBean







## bean的生命周期





## spring整合mybatis