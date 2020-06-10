## 在pom中添加依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.10</version>
</dependency>
```



## 修改application.properties

添加以下配置

```properties
#   数据源其他配置
#表明使用druid数据连接池
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
#初始化时建立物理连接的个数。
spring.datasource.druid.initial-size=5
#最大连接池数量
spring.datasource.druid.max-active=20
#最小连接池数量
spring.datasource.druid.min-idle=5
#获取连接时最大等待时间，单位毫秒
spring.datasource.druid.max-wait=3000
#是否缓存preparedStatement，也就是PSCache,PSCache对支持游标的数据库性能提升巨大，比如说oracle,在mysql下建议关闭。
spring.datasource.druid.pool-prepared-statements=false
#要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
spring.datasource.druid.max-open-prepared-statements= -1
#配置检测可以关闭的空闲连接间隔时间
spring.datasource.druid.time-between-eviction-runs-millis=60000
# 配置连接在池中的最小生存时间
spring.datasource.druid.min-evictable-idle-time-millis= 300000
# 配置连接在池中的最大生存时间
spring.datasource.druid.max-evictable-idle-time-millis= 400000
#监控统计的stat,以及防sql注入的wall
spring.datasource.druid.filters= stat,wall
#Spring监控AOP切入点，如x.y.z.service.*,配置多个英文逗号分隔
spring.datasource.druid.aop-patterns= life.usc.study.service.*
#是否启用StatFilter默认值true
spring.datasource.druid.web-stat-filter.enabled= true
#添加过滤规则
spring.datasource.druid.web-stat-filter.url-pattern=/*
#忽略过滤的格式
spring.datasource.druid.web-stat-filter.exclusions=*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*

#是否启用StatViewServlet默认值true
spring.datasource.druid.stat-view-servlet.enabled= true
#访问路径为/druid时，跳转到StatViewServlet
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*
# 是否能够重置数据
spring.datasource.druid.stat-view-servlet.reset-enable=false
# 需要账号密码才能访问控制台，默认为root
spring.datasource.druid.stat-view-servlet.login-username=druid
spring.datasource.druid.stat-view-servlet.login-password=druid
#IP白名单
spring.datasource.druid.stat-view-servlet.allow=127.0.0.1
#　IP黑名单（共同存在时，deny优先于allow）
spring.datasource.druid.stat-view-servlet.deny=
```

[详见](https://www.jianshu.com/p/ee00f7604c02)

[官方文档](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)

