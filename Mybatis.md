[官方文档](https://mybatis.org/mybatis-3/) 

## 什么是Mybatis

MyBatis 是一款优秀的持久层框架 它支持定制化 SQL、存储过程以及高级映射。 

MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。

 MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO。

## 什么是持久化

持久化就是将程序的数据在持久状态和瞬时状态转化的过程 

内存断电即失 

数据库(JDBC),io文件持久化。

## Maven依赖

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
```



## 第一个Mybatis程序

### 创建数据库

```sql
CREATE DATABASE `mybatis`;
use `mybatis`;
CREATE TABLE `user`(
`id` INT(20) NOT NULL PRIMARY KEY,
`name` VARCHAR(30) DEFAULT NULL,
`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;


INSERT INTO `user`(`id`,`name`,`pwd`) VALUES
(1, 'wjj', '123456'),
(2, 'fxt', '123456'),
(3, 'cny', '123');
```

### 创建Maven项目

1、创建普通maven项目（什么都不选）

![image-20200605114957183](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605114957183.png)

2、项目名

![image-20200605115031376](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605115031376.png)

3、记得添加‘-’

![image-20200605115053434](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605115053434.png)

4、删除src文件夹



### 引入maven依赖

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```



### 新建子模块

![image-20200605120433345](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605120433345.png)

![image-20200605120459139](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605120459139.png)

![image-20200605120539260](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605120539260.png)

可以发现 子模块中也有父模块的包



### 编写xml配置文件

mybatis-01下的resource文件中 创建mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 可以配置多套环境 -->
    <environments default="development">
        <environment id="development">
            <!--使用jdbc事务 -->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
       
    </mappers>
</configuration>
```



连接数据库 查看url

![image-20200605122450037](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605122450037.png)

![image-20200605122519269](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605122519269.png)	

完整的配置文件

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



### 编写Mybatis工具类

创建包

![image-20200605123426792](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605123426792.png)

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



### 编写代码

![image-20200605131100786](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605131100786.png)

#### 实体类

```java
package edu.usc.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```



#### Dao接口

```java
package edu.usc.dao;

import edu.usc.pojo.User;

import java.util.List;

public interface UserDao {
    List<User> getUserList();
}
```

#### 接口实现类（由UserDaoImp转变为Mapper文件）

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



### 测试

![image-20200605133433717](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605133433717.png)

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

### 踩坑

1、 空指针异常

![image-20200605133550416](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200605133550416.png)

原因在`MybatisUtil`里对`sqlSessionFactory`进行了重复声明，以为在提升作用域的时候已经声明了，所以在下面的静态代码块中不应该再次声明

```java
package edu.usc.util;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory; 

    static {
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource); 
            //出问题的地方
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream); 
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }


}
```



2、找不到UserDao接口

```
org.apache.ibatis.binding.BindingException: Type interface edu.usc.dao.UserDao is not known to the MapperRegistry.

	at org.apache.ibatis.binding.MapperRegistry.getMapper(MapperRegistry.java:47)
	at org.apache.ibatis.session.Configuration.getMapper(Configuration.java:779)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.getMapper(DefaultSqlSession.java:291)
	at edu.usc.dao.UserDaoTest.test(UserDaoTest.java:16)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:68)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)
```



原因是`mybatis-config`文件中忘记配置`<mapper></mapper>`

```xml
<mappers>
    <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
</mappers>
```

3、初始化异常 失败 `UserMapper.xml`不存在

```
java.lang.ExceptionInInitializerError
	at edu.usc.dao.UserDaoTest.test(UserDaoTest.java:15)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:68)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)
Caused by: org.apache.ibatis.exceptions.PersistenceException: 
### Error building SqlSession.
### The error may exist in edu/usc/dao/UserMapper.xml
### Cause: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: java.io.IOException: Could not find resource edu/usc/dao/UserMapper.xml
	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:80)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:64)
	at edu.usc.util.MybatisUtils.<clinit>(MybatisUtils.java:23)
	... 23 more
Caused by: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: java.io.IOException: Could not find resource edu/usc/dao/UserMapper.xml
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parseConfiguration(XMLConfigBuilder.java:121)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parse(XMLConfigBuilder.java:98)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:78)
	... 25 more
Caused by: java.io.IOException: Could not find resource edu/usc/dao/UserMapper.xml
	at org.apache.ibatis.io.Resources.getResourceAsStream(Resources.java:114)
	at org.apache.ibatis.io.Resources.getResourceAsStream(Resources.java:100)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.mapperElement(XMLConfigBuilder.java:372)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parseConfiguration(XMLConfigBuilder.java:119)
	... 27 more
```

原因是**资源过滤问题**

maven由于他的约定大于配置，所以可能出现配置文件无法被导出或者生效的问题。

maven默认资源文件配置是在resource目录下，但是我们现在把 `UserMapper.xml`放在了java目录下，所以我们需要手动配置资源过滤

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
            <include>**/*.properties</include>
            <include>**/*.xml</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```



4、初始化 异常失败

```
java.lang.ExceptionInInitializerError
	at edu.usc.dao.UserDaoTest.test(UserDaoTest.java:15)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:68)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)
Caused by: org.apache.ibatis.exceptions.PersistenceException: 
### Error building SqlSession.
### The error may exist in edu/usc/dao/UserMapper.xml
### Cause: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: org.apache.ibatis.builder.BuilderException: Error creating document instance.  Cause: com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 2 字节的 UTF-8 序列的字节 2 无效。
	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:80)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:64)
	at edu.usc.util.MybatisUtils.<clinit>(MybatisUtils.java:23)
	... 23 more
```

原因是 这个`UerMapper.xml`文件中不能出现中文，中文注释也不可以



## CRUD

### 查询

`UserMapper`

```java
public interface UserMapper {

    // 根据Id查询用户
    User getUserById(int id);
j
}
```

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.UserMapper">
    <select id="getUserById" parameterType="int" resultType="edu.usc.pojo.User">
        select * from mybatis.user where id=#{id}
    </select>
</mapper>
```

`UserMapperTest`

```java
@Test
public void test2() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper usermapper = sqlSession.getMapper(UserMapper.class);
    User user = usermapper.getUserById(1);
    System.out.println(user);

    sqlSession.close();
}
```



### 插入

`UserMapper`

```java
package edu.usc.dao;

import edu.usc.pojo.User;

import java.util.List;

public interface UserMapper {

    // 插入新用户
    int addUser(User user);
}
```

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.UserMapper">
    
    <!-- 可以自动获取User对象的参数 也就是说values后面的参数会自动和 parameterType里User的属性进行对应 -->
    <insert id="addUser" parameterType="edu.usc.pojo.User">
        insert into mybatis.user(id, name, pwd) values (#{id}, #{name}, ${pwd});
    </insert>

</mapper>
```

`UserMapperTest`

**增删改需要提交事务**

```java
@Test
public void test3() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    int res = userMapper.addUser(new User(4, "syc", "123"));
    if (res >0) {
        System.out.println("插入成功");
    }
    sqlSession.commit();
    sqlSession.close();
}
```



### 更新

`UserMapper`

```java
package edu.usc.dao;

import edu.usc.pojo.User;

import java.util.List;

public interface UserMapper {

    // 更新用户
    int updateUser(User user);

}
```

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.UserMapper">

    <update id="updateUser" parameterType="edu.usc.pojo.User">
        update mybatis.user set  name=#{name}, pwd=#{pwd}  where id=#{id};
    </update>

</mapper>
```

`UserMapperTest`

**增删改需要提交事务**

```java
@Test
public void test4() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    int res = userMapper.updateUser(new User(4, "cny", "12345"));
    if (res > 0) {
        System.out.println("更新成功");
    }
    sqlSession.commit();
    sqlSession.close();
}

```

### 删除

`UserMapper`

```java
package edu.usc.dao;

import edu.usc.pojo.User;

import java.util.List;

public interface UserMapper {

    // 删除用户
    int deleteUser(int id);
}
```

`UserMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.UserMapper">

    <update id="updateUser" parameterType="edu.usc.pojo.User">
        update mybatis.user set  name=#{name}, pwd=#{pwd}  where id=#{id};
    </update>

</mapper>
```

`UserMapperTest`

**增删改需要提交事务**

```java
@Test
public void test5() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper usermapper = sqlSession.getMapper(UserMapper.class);
    int res = usermapper.deleteUser(4);
    if (res > 0) {
        System.out.println("删除成功");
    }

    sqlSession.commit();
    sqlSession.close();
}
```



## Map优化CRUD

如果实体类或者数据库中的表的字段或则参数过多，我们应该考虑使用Map

```java
package edu.usc.dao;

import edu.usc.pojo.User;

import java.util.List;
import java.util.Map;

public interface UserMapper {

    // 用Map更新用户
    int updateUser2(Map<String, Object> map);

}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.UserMapper">

    <update id="updateUser2" parameterType="map">
        update mybatis.user set pwd=#{password}  where id=#{id};
    </update>
    
</mapper>
```

```java
@Test
public void test6() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    Map<String, Object> map = new HashMap<String, Object>();
    map.put("password", "123123");
    map.put("id", 3);
    int res = userMapper.updateUser2(map);
    System.out.println("修改成功");
    sqlSession.commit();
    sqlSession.close();
}
```

可以看出，使用Map的时候有以下优点

1、取值是根据Map的key值，而不再需要与对象中的字段名一致（User类中为pwd Map中为password），在有多个key的情况下也可以根据key值自动匹配。

2、在Test中使用时，不再需要创建对象并对所有字段赋值，只需将需要修改的字段放入Map即可。

### 总结

1、使用对象传递参数，xml文件中sql语句会自动匹配对象中的属性(也就是根据`#{}`中的属性名自动匹配)

```xml
    <update id="updateUser" parameterType="edu.usc.pojo.User">
        update mybatis.user set  name=#{name}, pwd=#{pwd}  where id=#{id};
    </update>
```

2、使用Map传递参数，xml文件中的sql语句会自动匹配Map中的Key值(也就是根据`#{}`中的键名自动匹配)

    ```xml
    <update id="updateUser2" parameterType="map">
        update mybatis.user set pwd=#{password}  where id=#{id};
    </update>
    ```

3、只有一个基本参数的情况下，可以不设置`parameterType`，sql语句会自动匹配到

```xml
    <select id="getUserById" parameterType="int" resultType="edu.usc.pojo.User">
        select * from mybatis.user where id=#{id}
    </select>
```



## 模糊查询

`UserMapper`

```java
List<User> selectUserByLike(String value);
```

### 方法一

Java代码执行时传递通配符‘%’

```xml
<select id="selectUserByLike" parameterType="String" resultType="edu.usc.pojo.User">
    select * from mybatis.user where name like #{value}
</select>
```

```java
@Test
public void test7() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    List<User> users = userMapper.selectUserByLike("%c%");
    for (User user : users) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



### 方法二

在sql拼接是使用通配符‘%’

```xml
<select id="selectUserByLike" parameterType="String" resultType="edu.usc.pojo.User">
    select * from mybatis.user where name like "%" #{value} "%"
</select>
```

```java
@Test
public void test7() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    List<User> users = userMapper.selectUserByLike("c");
    for (User user : users) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



## 配置文件详解

### mybatis-config.xml-environment详解

[官方文档](https://mybatis.org/mybatis-3/configuration.html#environments)

#### transactionManager

默认为JDBC

There are two TransactionManager types (i.e. type="[JDBC|MANAGED]") that are included with MyBatis

- **JDBC** – This configuration simply makes use of the JDBC commit and rollback facilities directly. It relies on the connection retrieved from the dataSource to manage the scope of the transaction.
- **MANAGED** – This configuration simply does almost nothing. 

#### dataSource

默认为POOLED

There are three built-in dataSource types (i.e. type="[UNPOOLED|POOLED|JNDI]"):



### mybatis-config.xml-properties详解

These are externalizable, substitutable properties that can be configured in a typical Java Properties file instance, or passed in through sub-elements of the properties element. 

1、resource下创建db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.50.238:3306/mybatis?usSLL=true&useUnicode=true&characterEncoding=UTF-8
username=root
password=123456
```

2、在mybatis-config.xml文件中引入	

从外部引入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- properties要在最上面 -->
    <!-- 引入外部配置文件 -->
    <properties resource="db.properties"/>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
    </mappers>

</configuration>
```

从内部引入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://192.168.50.238:3306/mybatis?usSLL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
    </mappers>

</configuration>
```

**外部引用优先级高于内部引用**



### mybatis-config.xml-typeAliases详解

[官方文档](https://mybatis.org/mybatis-3/configuration.html#typeAliases)

A type alias is simply a shorter name for a Java type. It's only relevant to the XML configuration and simply exists to reduce redundant typing of fully qualified classnames. 

当包下类较多的时候 推荐使用第二种方法

1、将这个类命名为自定义的别名

```xml
<typeAliases>
    <typeAlias alias="User" type="edu.usc.pojo.User"></typeAlias>
</typeAliases>
```

2、扫描这个包，将这个包下的类名（小写）作为别名

```xml
<typeAliases>
    <package name="edu.usc.pojo"></package>
</typeAliases>
```

可以通过添加注解实现自定义别名



基本类型需要加下划线

| Alias      | Mapped Type |
| :--------- | :---------- |
| _byte      | byte        |
| _long      | long        |
| _short     | short       |
| _int       | int         |
| _integer   | int         |
| _double    | double      |
| _float     | float       |
| _boolean   | boolean     |
| string     | String      |
| byte       | Byte        |
| long       | Long        |
| short      | Short       |
| int        | Integer     |
| integer    | Integer     |
| double     | Double      |
| float      | Float       |
| boolean    | Boolean     |
| date       | Date        |
| decimal    | BigDecimal  |
| bigdecimal | BigDecimal  |
| object     | Object      |
| map        | Map         |
| hashmap    | HashMap     |
| list       | List        |
| arraylist  | ArrayList   |
| collection | Collection  |
| iterator   | Iterator    |



### mybatis-config.xml-setting详解

[官方文档](https://mybatis.org/mybatis-3/configuration.html#settings)



### mybatis-config.xml-typeHandler详解（无需了解）

[官方文档](https://mybatis.org/mybatis-3/configuration.html#typeHandlers)



### mybatis-config.xml-objectFactory详解（无需了解）

[官方文档](https://mybatis.org/mybatis-3/configuration.html#objectFactory)



### mybatis-config.xml-plugin详解（无需了解）

[官方文档](https://mybatis.org/mybatis-3/configuration.html#plugins)



### mybatis-config.xml-mapper（三种方式）详解

[官方文档](https://mybatis.org/mybatis-3/configuration.html#mappers)

Now that the behavior of MyBatis is configured with the above configuration elements, we’re ready to define our mapped SQL statements. But first, we need to tell MyBatis where to find them. Java doesn’t really provide any good means of auto-discovery in this regard, so the best way to do it is to simply tell MyBatis where to find the mapping files. 

1、

```xml
<mappers>
    <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
</mappers>
```

2、使用class文件绑定注册

```xml
<mappers>
    <mapper class="edu.usc.dao.UserMapper"></mapper>
</mappers>
```

**注意**：

+ 接口和xml配置文件名字必须相同
+ 接口和xml配置文件必须在同一包下

3、扫描包

```xml
<mappers>
    <package name="edu.usc.dao"></package>
</mappers>
```

**注意**：

+ 接口和xml配置文件名字必须相同
+ 接口和xml配置文件必须在同一包下



## Mybaties生命周期

[官方文档](https://mybatis.org/mybatis-3/getting-started.html)

![image-20200606111829520](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200606111829520.png)

生命周期和作用域是至关重要的，错误的使用会导致



**SqlSessionFactoryBuilder**

+ 一旦完成创建SqlSessionFactory，就不再需要它了

+ **局部变量**



**SqlSessionFactory**

+ 可以理解为一个数据库连接池，一旦创建就会在程序执行期间一直存在，不丢弃，不重新创建
+ 最佳作用域为**应用作用域**
+ 最简单的就是使用单例模式和静态单例模式



**SqlSession**

+ 连接到连接池的一个请求
+ SqlSession的实例不是线程安全的，因此它不能被共享，所以他的最佳作用于是**请求或者方法作用域**
+ 用完需要马上关闭，防止资源被占用



![image-20200606113156398](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200606113156398.png)

## Result Map 解决属性名和字段名不一致的问题

[官方文档](https://mybatis.org/mybatis-3/sqlmap-xml.html#Result_Maps)

### Mapper.xml的查询原理

```xml
<select id="getUserById" parameterType="int" resultType="edu.usc.pojo.User">
    select id,name,pwd from mybatis.user where id=#{id}
</select>
```

上述语句其实会自动创建一个ResultMap（id, id）（name, name）（pwd, pwd），这样JavaBean就可以据此与Colum进行匹配了

但是如果JavaBean中的属性名与Column名不一致，就无法匹配，如下情况

```java

public class User {
    private int id;
    private String name;
    private String password;
}
```

```
Column: id name pwd
```

这时需要通过Alias来完成

```xml
<select id="getUserById" parameterType="int" resultType="edu.usc.pojo.User">
    select id,name,pwd as password from mybatis.user where id=#{id}
</select>
```

这样做，实质上也是改变了ResultMap，现在Result变成了（id, id）（name, name）（password,  pwd）如下

```xml
<!-- 可以看出password的map改变了 -->
<resultMap id="userResultMap" type="User">
  <id property="id" column="id" />
  <result property="name" column="name"/>
  <result property="password" column="pwd"/>
</resultMap>
```

因此，我们可以放弃alias的方法，直接在xml文件中重写上方ResultMap，然后引用

```xml
<resultMap id="userResultMap" type="User">
  <id property="id" column="id" />
  <result property="name" column="name"/>
  <result property="password" column="pwd"/>
</resultMap>

<select id="selectUsers" resultMap="userResultMap">
  select id, name, pwd from user where id = #{id}
</select>
```

我理解的`resultMap`其实就是对查询出的结果加了一个map 使得结果去javaBean中匹配的时候 不再去匹配查找出来的列名 而是查找resultMap中新给的名字

### Mapper.xml的取值

+ 如果传入的参数为对象 那么会`#{}`会自动去类的属性名匹配

+ 如果传入的参数为map 那么`#{}`回去匹配健值

+ 如果传入的是基本类型 会分为两种情况
  + 如果有@param注解 则匹配param中的值
  + 否则直接匹配



## 日志

### 日志工厂

|         |                                                              |                                                              |         |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------- |
| logImpl | Specifies which logging implementation MyBatis should use. If this setting is not present logging implementation will be autodiscovered. | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | Not set |

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties" />
    <!-- 需要在这个位置 -->
    <settings>
        <!-- 标准的日志工厂实现 -->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="edu.usc.pojo"></package>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="edu/usc/dao/UserMapper.xml"></mapper>
    </mappers>

</configuration>
```

日志输出

```shell
Opening JDBC Connection //Mybatis帮我们打开jdbc连接
Created connection 1291113768. 
Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@4cf4d528]
==>  Preparing: select * from mybatis.user where id=?  // 准备SQL语句
==> Parameters: 1(Integer) // 传递的参数类型
<==    Columns: id, name, pwd
<==        Row: 1, wjj, 123456
<==      Total: 1
User{id=1, name='wjj', pwd='123456'}
Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@4cf4d528]
Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@4cf4d528] // 关闭连接
Returned connection 1291113768 to pool. // 放回池中
```



### LOG4J

1、Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地（控制台、文件、GUI组件）

2、可以控制每一条日志的输出格式

3、通过定义每一条日志信息的级别，能够更加细致地控制日志的生成过程。

4、通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

#### Maven依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/log4j/log4j -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```



#### 配置properties

resources下新建log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/usc.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

#### 配置mybatis-config.xml

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

#### 简单使用

注意：自动会导入`java.lang`包 但是我们需要的是`org.apache.log4j.Logger`;

```java

public class UserMapperTest {
    // 在要使用的类下引入
    static Logger logger = Logger.getLogger(UserMapper.class);

    @Test
    public void testLog4j() {
        logger.info("this is info");
        logger.debug("this is debug");
        logger.error("this is error");
    }

}
```



## 分页

分页是为了减少数据的处理量



### limit实现分页（使用sql）

`UserMapper`

```xml
List<User> selectUserByLimit(Map<String, Integer> map);
```

`UserMapper.xml`

```xml
<select id="selectUserByLimit" parameterType="map" resultType="user">
    select * from mybatis.user limit #{offset},#{size}
</select>
```

`UserMapperTest`

```java
@Test
public void testLimit() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    Map<String, Integer> map = new HashMap<>();
    map.put("offset", 2);
    map.put("size", 2);
    List<User> userList = userMapper.selectUserByLimit(map);
    for (User user : userList) {
        System.out.println(user);
    }

    sqlSession.close();
}
```

**我个人理解的查询区间为(offset, offset + size]。**



### rowbounds实现分页（使用对象）

`UserMapper`

```java
List<User> selectUserByRowBounds();
```

`UserMapper.xml`

```xml
<select id="selectUserByRowBounds" resultType="user">
    select * from mybatis.user
</select>
```

`UserMapperTest`

```java
@Test
public void testRowBounds() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    RowBounds rowBounds = new RowBounds(2, 2);
    List<User> userList = sqlSession.selectList("edu.usc.dao.UserMapper.selectUserByRowBounds", null, rowBounds);
    for (User user : userList) {
        System.out.println(user);
    }
    sqlSession.close();
}
```



## 使用注解开发

### 面向接口编程

面向接口编程的根本原因：解耦，可拓展，提高复用，分层开发中、上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性好



### 使用注解开发

注解在接口上实现

`UserMapper`

```java
@Select(value = "select * from user")
List<User> getUsers();
```

`mybatis-config.xml`

```xml
<!--绑定接口-->
<mappers>
    <!-- 注意是class 不要写成resource -->
    <mapper class="edu.usc.dao.UserMapper"/>
</mappers>
```

不再需要`UserMapper.xml`文件了



### CRUD

设置事务自动提交

```java
public static SqlSession getSqlSession() {
    return sqlSessionFactory.openSession(true);
}
```

`UserMapper`

```java
@Select("select * from user where id=#{id}")
User getUserById(@Param("id") int id);

@Insert("insert into user(id,name,pwd) values (#{id},#{name},#{pwd})")
int addUser(User user);

@Update("update user set name = #{name},pwd = #{pwd} where id = #{id}")
int updateUser(User user);

@Delete("delete from user where id = #{id}")
int deleteUser(@Param("id") int id);
```

我把@Param("id")理解为把id变成了一个map -> (id : id) 也就数说`#{}`会根据@param里的名称进行匹配

也就是说 如果@Param("uid") 那么 sql语句中就需要写`#{uid}`而不是和传入相同的`#{id}`

`UserMapperTest`

```java
@Test
public void test02() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.getUserById(1);
    System.out.println(user);
    sqlSession.close();
}
```



## Mybatis执行流程

```java
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



1、`Resources.getResourceAsStream(resource)` Resources获取全局配置文件流

2、`new SqlSessionFactoryBuilder()` 实例化SqlSessionFactoryBuilder

3、`SqlSessionFactoryBuilder().build(inputStream)` 调用build()方法解析全局配置文件流

​      `build((InputStream)inputStream, (String)null, properties)` build()方法中调用重载的build()方法

​	  ` new XMLConfigBuilder(inputStream, environment, properties);`build()方法中调用XMLConfigBuilder对配置文件流进行解析

 	 `XMLConfigBuilder.parse()` 调用parse()方法完成解析，返回全部配置信息config

​      `build(parser.parse())` build()方法中再次调用重载的build()方法

​      `new DefaultSqlSessionFactory(config)` 完成SqlSessionFactory的实例化

4、transaction事务管理

5、创建excutor执行器

6、创建sqlSession

7、查看是否执行成功

8、提交事务



## Lombok

```java
@Data:无参构造，get、set、toSring、hashcode、equals
@AllArgsConstructor
@NoArgsConstructor
@ToString
@EqualsAndHashCode
```



## 复杂查询

### 环境搭建

#### 创建数据库

```sql
CREATE TABLE teacher(
id int(10) Not null,
name VARCHAR(30) DEFAULT NULL,
PRIMARY KEY (id)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(id,name) VALUES (1,"秦老师");

CREATE TABLE student(
id int(10) Not null,
name VARCHAR(30) DEFAULT NULL,
tid INT(10) DEFAULT NULL,
PRIMARY KEY (id),
KEY fktid(tid),
CONSTRAINT fktid FOREIGN KEY (tid) REFERENCES teacher (id)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO student(id,name,tid) VALUES (1,"小明",1);
INSERT INTO student(id,name,tid) VALUES (2,"小红",1);
INSERT INTO student(id,name,tid) VALUES (3,"小张",1);
INSERT INTO student(id,name,tid) VALUES (4,"小李",1);
INSERT INTO student(id,name,tid) VALUES (5,"小王",1);

```

#### 配置resources下的文件

配置`db.properties`

配置`mybatis-config.xml`

#### 配置util pojo dao

```java
public class Student {
    private int id;
    private String name;
    private Teacher teacher;
}
```

```java
public class Teacher {
    private int id;
    private String name;
}
```

#### 配置resources的Mapper文件

新建同名包 包下创建`TeacherMapper.xml` `StudentMapper.xml`



### 多对一查询

#### 嵌套查询

`StudentMapper`

```java
List<Student> getStudentList();
```

`StudentMapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.usc.dao.StudentMapper">

    <!-- 因为返回的结果中有属性tid 而javaBean中是 Teacher teacher 无法对应 -->
    <select id="getStudentList" resultMap="StudentTeacher">
        select * from mybatis.student
    </select>

    <resultMap id="StudentTeacher" type="student">
        <!-- 前两个即使属性名列明一样也不可以省略 如果省略则返回的对象属性会赋默认值 -->
        <result property="id" column="id"/>
        <resullt property="name" column="name"/>
        <!-- 将tid对应到teacher javaType标签知指明teacher是Teacher类型的 且通过getTeacher子查询查出 -->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>

	<!-- #{id}会去活的association中tid的值 -->
    <select id="getTeacher" resultType="Teacher">
        select * from mybatis.teacher where id=#{id}
    </select>
</mapper>
```



#### 连表查询

```xml
<!-- 因为返回的结果中有属性t.name 而javaBean中是 Teacher teacher 无法对应 -->
<select id="getStudentList2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname
    from student s,teacher t
    where s.tid=t.id
</select>

<resultMap id="StudentTeacher2" type="student">
    <!-- 因为对属性起了别名 所以column='别名' -->
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <!-- 单个对象用association标签 -->
    <!-- javaType标签指明这个属性为Teacher类型 -->
    <association property="teacher" javaType="Teacher">
        <!-- 对teacher进行赋值 也就是说javaBean中的teacher的name赋值为tname 其他属性若不赋值 则为默认值 -->
        <result property="name" column="tname"/>
    </association>
</resultMap>
```



### 一对多查询

#### 搭建环境

```java
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

```java
public class Teacher {
    private int id;
    private String name;
    private List<Student> studentList;
}
```



#### 连表查询

```xml
<select id="getTeacherById" resultMap="TeacherStudent">
    select s.id sid,s.name sname,t.id,tid,t.name tname
    from student s, teacher t
    where s.tid=t.id and t.id=#{id}
</select>

<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--集合用collection标签-->
    <!--用ofType来指明泛型类型为Student-->
    <collection property="studentList" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```



#### 嵌套查询

```xml
<select id="getTeacherById2" resultMap="TeacherStudent2">
    select * from teacher where id=#{id}
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <!-- 前两个即使属性名列明一样也不可以省略 如果省略则返回的对象属性会赋默认值 -->
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <!-- 情况比较特殊 没有多余的列可以用来表示Student 因此选择需要向子查询传递的参数的参数名 作为column -->
    <!-- 而且javaType和ofType都需要写 -->
    <collection property="studentList" javaType="ArrayList" ofType="Student" select="getStudent" column="id"></collection>
</resultMap>
<select id="getStudent" resultType="Student">
    select * from student where tid=#{id}
</select>
```



### 注意点

关联-association【多对一】 

集合-collection 【一对多】 

javaType 用来指定实体类中属性的类型    

ofType 用来指定映射到List或者集合中的pojo类型，泛型中的约束类型



## 动态SQL

### 环境搭建

数据库准备

```sql
CREATE TABLE blog(
id VARCHAR(50) NOT NULL COMMENT "博客id",
title VARCHAR(100) not null comment "博客标题",
author VARCHAR(30) not null comment "博客作者",
creat_time datetime not null comment "创建时间",
views int(30) not null comment "浏览量"
)ENGINE=InnoDB DEFAULT CHARSET=utf8

drop table bolg;
```

resources下的配置文件 `db.properties` `mybatis-config.xml`

Util包下 `MybatisUtil` `IDUtil`

```java
public class IDUtils {

    public static String getId(){
        return UUID.randomUUID().toString().replaceAll("-", "");
    }

}
```

pojo下 `Blog`实体类

```java
@Data
public class Blog {
    private String id;
    private String title;
    private String author;
    private Date createTime;
    private int views;
}
```

dao下 `BlogMapper`接口 `BlogMapper.xml`

单元测试

```java
public class BlogMapperTest {

    @Test
    public void testInsertBlog() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        Blog blog = new Blog();
        blog.setId(IDUtils.getId());
        blog.setTitle("第一个博客");
        blog.setAuthor("wjj");
        blog.setCreateTime(new Date());
        blog.setViews(999);
        mapper.insertBlog(blog);
        sqlSession.close();
    }
}
```



### where

The *where* element knows to only insert "WHERE" if there is any content returned by the containing tags. 

Furthermore, if that content begins with "AND" or "OR", it knows to strip it off.

简单来说 如果where标签下的if标签下的条件都不满足，则不会加where

会自动删除不需要的and或者or

### set

The *set* element will dynamically prepend the SET keyword, and also eliminate any extraneous commas that might trail the value assignments after the conditions are applied.

如果set标签下的if标签下的条件都不满足，则会报错

会自动删不需要的逗号

### if

`BlogMapper`

```java
List<Blog> selectByIf(Blog blog);
```

`BlogMapper.xml`

<!-- where 1=1 这种写法不规范 -->

```xml
<select id="selectByIf" parameterType="Blog" resultType="blog">
    select * from mybatis.blog where 1=1
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```

<!-- where标签会自动帮我们去掉不需要的and -->

```xml
<select id="selectByIf" parameterType="Blog" resultType="blog">
    select * from mybatis.blog
    <where>
        <if test="title != null">
            and title=#{title}
        </if>
        <if test="author != null">
            and author=#{author}
        </if>
    </where>
</select>
```

`BlogMapperTest`

```java
@Test
public void testselectByIf(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Blog blog = new Blog();
    List<Blog> blogList = mapper.selectByIf(blog);
    for (Blog blog1 : blogList) {
        System.out.println(blog1);
    }
    sqlSession.close();
}
```

若if中的条件不为空 则会拼接上and之后的语句  否则会只执行不带if的语句 



### Choose When

`BlogMapper`

```java
List<Blog> selectByChoose(Map<String, String> map);
```

`BlogMapper.xml`

```xml
<select id="selectByChoose" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <choose>
            <when test="author != null">
                and author=#{author}
            </when>
            <when test="title != null">
                and title=#{title}
            </when>
            <otherwise>
                and id=#{id};
            </otherwise>
        </choose>
    </where>
</select>
```

`BlogMapperTest`

```java
@Test
public void testSelectByChoose() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Map<String, String> map = new HashMap();
    map.put("title", "第一个博客");
    map.put("author", "wjj");
    List<Blog> blogList = mapper.selectByChoose(map);
    for (Blog blog : blogList) {
        System.out.println(blog);
    }
    sqlSession.close();
}
```

相当于java中的swich case语句 只要有一个条件满足 就会跳出 不再继续向下进行

就不如上面的例子 虽然设置了author和title 但是只要author匹配上之后 就不会再去匹配title了

如果when中的条件都不满足 则会执行otherwise



### Set

`BlogMapper`

```java
int updateBySet(Map<String, String> map);
```

`BlogMapper.xml`

```xml
<update id="updateBySet" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title=#{title},
        </if>
        <if test="author != null">
            author=#{author}
        </if>
    </set>
    where id=#{id}
</update>
```

`BlogMapperTest`

```java
@Test
public void updateBySet() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Map<String, String> map = new HashMap();
    map.put("title", "更新的博客");
    map.put("author", "fxt");
    map.put("id", "15844a769b5648e997db709d32e9eb18");
    mapper.updateBySet(map);
    sqlSession.close();
}
```



### sql片段

```xml
<sql id="if-title-author">
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</sql>

<select id="selectByIf" parameterType="Blog" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```



### foreach

`BlogMapper`

```java
List<Blog> selectByForeach(Map<String, List<String>> map);
```

`BlogMapper.xml`

```java
<select id="selectByForeach" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <foreach collection="ids" open="and (" close=")" separator="or" item="id">
            id=#{id}
        </foreach>
    </where>
</select>
```

`BlogMapperTest`

```java
    @Test
    public void testSelectByForeach() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        Map<String, List<String>> map = new HashMap<>();
        ArrayList<String> ids = new ArrayList<>();
        ids.add("0dd754814ba744b2b796bd25054b88c1");
//        ids.add("15844a769b5648e997db709d32e9eb18");
        map.put("ids", ids);
        List<Blog> blogList = mapper.selectByForeach(map);
        for (Blog blog : blogList) {
            System.out.println(blog);
        }

        sqlSession.close();
    }
```



## 缓存

### 什么是缓存[Cache]?

存在内存中的临时数据。

将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库数据文件）查询，

从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。

### 为什么使用缓存？
​    减少和数据库的交互次数，减少系统开销，提高系统效率。

### 什么样的数据能使用缓存？
​    经常查询并且不经常改变的数据。



## Mybatis缓存

[官方文档](https://mybatis.org/mybatis-3/sqlmap-xml.html#cache)

Mybatis默认开启local session caching，如果想开启global second level of caching，需要添加`<cache/>`标签

### Local

默认开启

sqlSession会话期间查询到的数据会放在本地缓存中

**缓存失效的情况**

1、增删改操作（只要有增删改操作 就会刷新全部缓存）

2、手动清理缓存 `sqlsession.clearCache()`

**测试**

```java
// 会发现第二次查询操作并未执行sql语句 而是在内存中直接获取数据了
@Test
public void testSelectUserById() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectUserById(1);
    System.out.println(user);
    System.out.println("======================");
    User user1 = mapper.selectUserById(1);
    System.out.println(user1);
    sqlSession.close();
}
```

```java
// 会发现虽然更新的是id为2的用户 但是id为1的用户的缓存也flush掉了
@Test
public void testSelectUserById() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectUserById(1);
    mapper.updateUser(new User(3, "cny", "1234"));
    System.out.println(user);
    System.out.println("======================");
    User user1 = mapper.selectUserById(1);
    System.out.println(user1);
    sqlSession.close();
}
```

```java
// 手动清除cache后 第二个查询也会执行sql语句了
@Test
public void testSelectUserById() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectUserById(1);
    sqlSession.clearCache();
    System.out.println(user);
    System.out.println("======================");
    User user1 = mapper.selectUserById(1);
    System.out.println(user1);
    sqlSession.close();
}
```

### global

二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
基于namespace级别的缓存，一个名称空间，对应一个二级缓存；



**工作机制**
    一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；

​    如果当前会话关闭了，这个会话对应的一级缓存就没了；

​	会话关闭后，一级缓存中的数据会被保存到二级缓存中；

​    新的会话查询信息，就可以从二级缓存中获取内容；

​    不同的mapper查出的数据会放在自己对应的缓存（map）中；

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

This more advanced configuration creates a FIFO cache that flushes once every 60 seconds, stores up to 512 references to result objects or lists, and objects returned are considered read-only, thus modifying them could cause conflicts between callers in different threads.

The available eviction policies available are:

- `LRU` – Least Recently Used: Removes objects that haven't been used for the longst period of time.
- `FIFO` – First In First Out: Removes objects in the order that they entered the cache.
- `SOFT` – Soft Reference: Removes objects based on the garbage collector state and the rules of Soft References.
- `WEAK` – Weak Reference: More aggressively removes objects based on the garbage collector state and rules of Weak References.

The default is LRU.

The flushInterval can be set to any **positive integer** and should represent a reasonable amount of time specified in milliseconds. The default is not set, thus no flush interval is used and the cache is only flushed by calls to statements.

The size can be set to any positive integer, keep in mind the size of the objects your caching and the available memory resources of your environment. The default is 1024.

The readOnly attribute can be set to true or false. A read-only cache will return the same instance of the cached object to all callers. Thus such objects should not be modified. This offers a significant performance advantage though. A read-write cache will return a copy (via serialization) of the cached object. This is slower, but safer, and thus the default is false.

**NOTE** Second level cache is transactional. That means that it is updated when a SqlSession finishes with commit or when it finishes with rollback but no inserts/deletes/updates with flushCache=true where executed.



**测试**

1、`UserMapper`添加`<cache/>` 开启global缓存

```xml
<cache
        eviction="FIFO"
        flushInterval="60000"
        size="512"
        readOnly="true"/>
```

如果不显式定义配置的话 需要将`User`实体类序列化`implements Serializable` 否则会报错

2、`mybatis-config.xml` 显式配置`cache` 虽然默认是开启的 但是为了增强可读性 我们再次显式配置

```xml
<setting name="cacheEnabled" value="true"/>
```

3、`UserMapperTest`

```java
@Test
public void testSelectUserById() {
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.selectUserById(1);
    sqlSession.clearCache();
    System.out.println(user);
    sqlSession.close();

    System.out.println("======================");
    SqlSession sqlSession1 = MybatisUtils.getSqlSession();
    UserMapper mapper1 = sqlSession1.getMapper(UserMapper.class);
    User user1 = mapper1.selectUserById(1);
    System.out.println(user1);
    sqlSession1.close();
}
```



### 缓存原理

![在这里插入图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\Mybatis-image\1.jpg)



## ehcache

1.导入jar包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.1.0</version>
</dependency>

```

2、修改`UserMapper.xml`

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

3、新增ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <!--
       diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
       user.home – 用户主目录
       user.dir  – 用户当前工作目录
       java.io.tmpdir – 默认临时文件路径
     -->
    <diskStore path="java.io.tmpdir/Tmp_EhCache"/>
    <!--
       defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
     -->
    <!--
      name:缓存名称。
      maxElementsInMemory:缓存最大数目
      maxElementsOnDisk：硬盘最大缓存个数。
      eternal:对象是否永久有效，一但设置了，timeout将不起作用。
      overflowToDisk:是否保存到磁盘，当系统当机时
      timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
      timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
      diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
      diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
      diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
      memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
      clearOnFlush：内存数量最大时是否清除。
      memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
      FIFO，first in first out，这个是大家最熟的，先进先出。
      LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
      LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
   -->
    <defaultCache
            eternal="false"
            maxElementsInMemory="10000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="259200"
            memoryStoreEvictionPolicy="LRU"/>

    <cache
            name="cloud_user"
            eternal="false"
            maxElementsInMemory="5000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="1800"
            memoryStoreEvictionPolicy="LRU"/>

</ehcache>
```



## 配置文件总结

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?usSLL=true&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
username=root
password=root
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="db.properties" />
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
<!--        <setting name="cacheEnabled" value="true"/>-->
    </settings>
    <typeAliases>
        <package name="edu.usc.pojo"></package>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper class="edu.usc.dao.UserMapper"></mapper>
    </mappers>

</configuration>
```

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
            return sqlSessionFactory.openSession(true);
        }
    }
```