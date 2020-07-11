# ssm整合创建项目

## 创建web项目

## 导入相关依赖

```xml
<dependencies>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>
        <!--mybatis和spring整合依赖，可以生成sqlSessionFactory-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.4</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.22</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <!--导入shiro依赖，为了使用加密-->
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-core</artifactId>
            <version>1.4.2</version>
        </dependency>
        <!--将数据转换成json字符串-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.8</version>
        </dependency>

        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.1.10</version>
        </dependency>

    </dependencies>
```

## 创建数据库，准备表和测试数据

## idea连接数据库，自动创建实体类

## 创建mybatis的配置文件

### 导入db.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql:///trivial
jdbc.username=root
jdbc.password=root
```



### 创建mybatis-config.xml文件

由于连接数据源，导入mapper.xml文件操作都在spring的核心配置文件中作为SQLsessionFactory的属性加载，所以mybatis-config.xml文件中还需要配置：

​	1.别名

​	2.插件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--properties-->

    <!--数据源-->

    <!--实体类别名-->
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <typeAliases>
        <package name="com.qf.entity"/>
    </typeAliases>

    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>
    <!--映射文件导入-->

</configuration>
```

## 创建spring的配置文件

### 	创建spring管理SQLsessionFactory的配置文件

在这个文件中要做5件事

​	1.加载db.properties

​	2.加载注解扫描器component-scan

​	3.创建数据源

​	4.创建sqlSessionFactoryBean

​	5.创建mapper接口扫描器MapperScannerConfigurer

```
这是一个生成指定文件夹下所有Dao类对象的组件，如果包下有UserDao.java，在getBean生成对象时穿参需要传入userDao字符串
```

### 创建spring的其他配置文件

在这个配置文件中有以下四件事需要做

```
1.开启service层注解扫描

2.开启AOP注解

3.声明事务管理tx

4.事务管理注解扫描
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/context/spring-aop.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--扫描service注解-->
    <context:component-scan base-package="com.qf.service"/>
    <!--开启AOP注解-->

    <!--这个bean用来创造spring提供的管理事务的类，他需要的参数是DataSource-->
    <bean id="txname" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--这里的DataSource就算飘红也不用改，它在另外一个配置文件中生成了-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务管理器的增强tx-->

    <tx:annotation-driven transaction-manager="txname"/>
</beans>
```

## 配置SpringMVC.xml文件

在这个配置文件中有三件事需要做：

1.开启注解扫描，controller以及其他包都可以放在这里

2.指定映射器和适配器 

3.配置视图解析器 ，controller返回一个字符串，这个字符串会被拼接上前缀和后缀“.jsp”

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--    开启注解扫描-->
    <context:component-scan base-package="com.qf.controller,com.qf.exception" />

    <!--    指定映射器和适配器-->
    <mvc:annotation-driven />

    <!--    视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/" />
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

## 配置web.xml文件

在这个文件中

1.配置前端控制器dispatchServlet

​		a.让Tomcat能扫描到springMVC配置文件

​		b.配置前端控制器的扫描范围，

```
/    除了.jsp的请求，都有前端控制器接受
/*   所有请求都由前端控制器接受
```

2.为了让之前配置的xml文件能加载，监听器加载applicationContext-*.xml

3.处理post请求乱码

```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--处理post请求乱码-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <!--为了让xml文件能被加载，监听器加载applicationContext-*.xml-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext-*.xml</param-value>
    </context-param>

    <!--配置前端控制器，dispatchServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--让Tomcat能扫描到springMVC配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>
    <!--设置前端控制器的控制范围是：除了.jsp的请求，全部由前端控制器接受-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

 <!--   &lt;!&ndash;放行js&ndash;&gt;
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.js</url-pattern>
    </servlet-mapping>-->


</web-app>
```

## 配置tomcat

加载tomcat

将项目的war包添加到tomcat上

