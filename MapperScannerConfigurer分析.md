## Mybatis阶段

1、resource文件中 创建mybatis-config.xml ，在其中配置数据源，注册对应的mapper文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://192.168.51.171:3306/mybatis?usSLL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    
    <!-- 每一个XXXMapper.xml文件都需要在此注册 -->
    <mappers>
        <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
    </mappers>
</configuration>
```

2、创建MybatisUtil工具类，用于创建SqlSession

```java
package edu.usc.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

//sqlSessionFactory
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory; // 提升sqlSessionFactory作用域

    /*
    * 获取sqlSessionFactory对象
    * */
    static {
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource); //加载配置文件 并生成流
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream); // 因为之前已经进行过类型声明 所以这里不需要再声明了
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /*
    * 获取到sqlSessionFactory之后，获取sqlSession实例，其包含了面向数据库执行SQL命令的所有方法
    * */
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }


}
```

3、创建实体类

4、创建Dao接口

5、mapper.xml实现接口，通过namespace与对应的dao绑定，此时回到mybatis-config.xml注册mapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace命名空间 绑定对应的Dao/Mapper 接口 -->
<mapper namespace="edu.usc.dao.UserDao">
    <!-- id是方法名 可以理解成对这个方法进行实现 -->
    <!-- resultType是返回值类型-->
    <select id="getUserList" resultType="edu.usc.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

6、测试

```java
package edu.usc.dao;

import edu.usc.pojo.User;
import edu.usc.util.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {

    @Test
    public void test() {

        SqlSession sqlSession = MybatisUtils.getSqlSession(); // 获取sqlSession
        
        try {
            UserDao userDao = sqlSession.getMapper(UserDao.class); // 获取UserDao接口
            List<User> userList = userDao.getUserList(); // 调用方法

            for (User user : userList) {
                System.out.println(user);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            sqlSession.close();
        }
    }

}
```



## Spring阶段

1、创建实体类

2、创建Dao接口

3、mapper.xml实现接口

4.、创建mybatis-config.xml，给包起别名

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <typeAliases>
        <package name="edu.usc.pojo"></package>
    </typeAliases>

</configuration>
```

5、创建spring-dao.xml 配置数据源，配置sqlFactory（绑定mybatis-config.xml并注册mapper），配置sqlSession

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?usSLL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:edu/usc/mapper/*.xml"/>
    </bean>

    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>

</beans>
```

6、编写dao接口的实现类，该实现类**通过sqlSession调用dao接口**，dao接口对应xml实现

```java
package edu.usc.mapper;

import edu.usc.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserMapperImpl implements UserMapper {

    SqlSessionTemplate sqlSession;

    public void setSqlSession(SqlSessionTemplate sqlSession) {
        this.sqlSession = sqlSession;
    }

    @Override
    public List<User> selectUser() {
        User user = new User(4, "dyc", "123");
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        mapper.insertUser(user);
        mapper.deleteUser(4);
        List<User> userList = mapper.selectUser();
        return userList;
    }

    @Override
    public int insertUser(User user) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.insertUser(user);
    }

    @Override
    public int deleteUser(int id) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        return mapper.deleteUser(id);

    }
}
```

7、创建ApplicationConext.xml，注册dao的实现类并引入mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="edu.usc.mapper.UserMapperImpl">
        <property name="sqlSession" ref="sqlSession"/>
    </bean>

</beans>
```

8、测试

```java
public class MyTest {

    @Test
    public void test02() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
        userMapper.selectUser();
    }
}
```

## 比较

Mybatis阶段调用需要先生成SqlSession，然后通过SqlSession找到对应的Mapper接口，然后完成方法调用（spring为我们完成了mapper接口和xml的对应）

Spring阶段调用首先需要先获取上下文，然后再上下文中获取dao接口的实现类，然后完成方法调用（spring为我们完成了mapper接口和xml的对应，我们在实现类中完成SqlSession的操作，由Spring为我们完成SqlSession的注入）



## SpringMVC

1、数据库配置文件 database.properties

2、编写MyBatis的核心配置文件mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
   
   <typeAliases>
       <package name="edu.usc.pojo"/>
   </typeAliases>
    
   <!-- 也可以在spring配置文件中配置 --> 
   <mappers>
       <mapper resource="edu/usc/dao/BookMapper.xml"/>
   </mappers>

</configuration>
```

3、编写实体类

4、编写dao接口

5、mapper.xml实现dao接口 并去mybatis-config.xml注册

6、编写Service层的接口

7、编写services实现类 实现类**直接调用dao接口**

8、编写spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

   <!-- 配置整合mybatis -->
   <!-- 1.关联数据库文件 -->
   <context:property-placeholder location="classpath:database.properties"/>

   <!-- 2.数据库连接池 -->
   <!--数据库连接池
       dbcp 半自动化操作 不能自动连接
       c3p0 自动化操作（自动的加载配置文件 并且设置到对象里面）
   -->
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
       <!-- 配置连接池属性 -->
       <property name="driverClass" value="${jdbc.driver}"/>
       <property name="jdbcUrl" value="${jdbc.url}"/>
       <property name="user" value="${jdbc.username}"/>
       <property name="password" value="${jdbc.password}"/>

       <!-- c3p0连接池的私有属性 -->
       <property name="maxPoolSize" value="30"/>
       <property name="minPoolSize" value="10"/>
       <!-- 关闭连接后不自动commit -->
       <property name="autoCommitOnClose" value="false"/>
       <!-- 获取连接超时时间 -->
       <property name="checkoutTimeout" value="10000"/>
       <!-- 当获取连接失败重试次数 -->
       <property name="acquireRetryAttempts" value="2"/>
   </bean>

   <!-- 3.配置SqlSessionFactory对象 -->
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <!-- 注入数据库连接池 -->
       <property name="dataSource" ref="dataSource"/>
       <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
       <property name="configLocation" value="classpath:mybatis-config.xml"/>
   </bean>

   <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
   <!-- 我们就不再需要去配置sqlSessionTemplate了 
		不再需要去注册UserMapperImpl bean了
		不再需要从UserMapperImpl中配置SqlSessionTemplate了
														 -->	
   <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->
   <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <!-- 注入sqlSessionFactory -->
       <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
       <!-- 给出需要扫描Dao接口包 -->
       <property name="basePackage" value="com.kuang.dao"/>
   </bean>

</beans>
```



## 该篇核心

### 第一阶段

手动从工具类获取SqlSession

调用Dao

### 第二阶段

spring-dao.xml文件中配置SqlSession

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>
```
实现类中留出set方法，让xml注入SqlSession

xml文件中注册实现类，并让SqlSession注入进去

```xml
<bean id="userMapper" class="edu.usc.mapper.UserMapperImpl">
    <property name="sqlSession" ref="sqlSession"/>
</bean>
```

获取上下文

从上下文中获取的实现类

完成方法调用

### 第三阶段（未使用过）

```xml
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
  <property name="mapperInterface" value="org.mybatis.spring.sample.mapper.UserMapper" />
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

无需再去配置sqlSession

创建一个MapperFactoryBean实例，然后注入这个接口和sqlSessionFactory（mybatis中提供的SqlSessionFactory接口，MapperFactoryBean会使用SqlSessionFactory创建SqlSession）这两个属性。
之后想使用这个UserMapper接口的话，直接通过spring注入这个bean，然后就可以直接使用了

### 第四阶段

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 注入sqlSessionFactory -->
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    <!-- 给出需要扫描Dao接口包 -->
    <property name="basePackage" value="com.kuang.dao"/>
</bean>
```

无需再去配置sqlSession

也无需再去创建dao的实现类 无需在实现类中调用sqlSession

 

以上的配置已经为basePackage包下的所有接口自动装配sqlSession

但是还是需要将自动装配好sqlSession的实现类（spring装配的，我们并没有写） 装配到service的实现类中

```xml
<bean id="BookServiceImpl" class="com.kuang.service.BookServiceImpl">
    <property name="bookMapper" ref="bookMapper"/>
</bean>
```



https://www.cnblogs.com/daxin/p/3545040.html

https://blog.csdn.net/xchyi/article/details/74094189