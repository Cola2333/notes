## 引入adminlte的各种包

## 在版本控制中修改数据库

在flyway中新建一个admin表

运行

```bash
mvn flyway:repair
mvn flyway:migrate
```

## 修改mapper文件

修改generatorConfig.xml 增加

```xml
<table tableName="admin" domainObjectName="Admin"></table>
```

运行

```bash
mvn -Dmybatis.generator.overwrite=true mybatis-generator:generate
```



## 1. admin包下创建login.html页面

## 2. Controller包下新增admin包

### 新增AdminLoginController

## 3. 新增Common包

### 新增CommonController

### 新增KaptchaConfig

## 4. Service包下新增admin包

### 新增AdminLoginService

## 5. admin包下创建index.html 并引入footer header sidebar

## 6. Controller包下admin包下新增AdminIndexController

## 7. Intercptor包下新建admin包

### 新增AdminLoginInterceptor

## 8. config包下新增admin包

### 新增AdminLoginInterceptor

## 9. admin包下创建profile.html

## 10.Controller包下admin包下新增AdminProfileController

## 11.Service包下admin包下新增AdminProfileService





## 12 修改名字 修改密码 md5 login的时候就用了 loginservice需要改









