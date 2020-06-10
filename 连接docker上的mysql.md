## 准备工作

打开虚拟机

登录：root 123456 

```shell
//查看ip
ip addr
```

打开smarTTY 建立连接

```shell
//查看所有容器 找到mysql5.6容器的id
docker ps -a

//
docker start 容器id
```



## Pull对应版本的mysql镜像

因为我们在application.properties和pom.xml都有对mysql的设置 所以我们要**pull对应的版本**

因为我在阿里云ECS服务器上用的是Mysql5.6 所以我在docker上使用的也是Mysql5.6

```bash
docker pull mysql:5.6
```

启动mysql容器 注意要在-d 后面**写5.6** 否则如果你还安装了最新版本 他会默认启动最新版本

```bash
docker run -p 3306:3306 --name mysqlForStudy -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
```

## 配置大小写不敏感

linux下默认是对Mysql大小写敏感的 因为我flyway作数据迁移的代码中创建表的语句都是小写的 所以需要关闭大小写敏感

我发现这个问题是因为我在执行migrate的时候 报错找不到user表 于是

进入 docker 容器 MySQL

```bash
docker exec -it mysql  /bin/bash
```

配置镜像源安装 VIM

```bash
#更新安装源 
apt-get update 
#如果下载过程中卡在[waiting for headers] 删除/var/cache/apt/archives/下的所有文件 
#安装vim 
apt-get install vim
```

编辑文件

```bash
vi /etc/mysql/mysql.conf.d/mysqld.cnf

#[mysqld]后添加 
lower_case_table_names=1
```

重启应用

```bash
#容器中执行
service mysql restart

#或者退出容器直接重启mysql容器
docker restart mysql
```



## 修改对应的application.properties和pom.xml

就是修改数据源地址以及账号密码 不再详细描述



## 运行flyway:migrate

```bash
 mvn clean compile flyway:repair
 mvn clean compile flyway:migrate -Pdev
```

我在这步出了些问题（出现问题先无脑```mvn clean compile flyway:repair``` 可能就能就解决）

一开始报错不存在question表 我感觉应该是大小写敏感的问题 就去设置关闭大小写敏感了（如上述）

但是设置完成之后 再次执行 执行成功 但是运行项目后报错没有user表……

这就很怪异了 我就看了一下sql文件 发现我flyway中的创建user表写的sql很怪异 所以我重新改了一下（调整为和其他表一样）

然后我删掉数据库 再次执行 又报错 Failed to execute goal org.flywaydb:flyway-maven-plugin:5.2.4:migrate (default-cli) on project study: org.flywaydb.core.api.FlywayException: Found non-empty schema(s) `study` without schema history table! Use baseline() or set baselineOnMigrate to true to initialize the schema history table. 

我查了半天也不知道是啥错（现在感觉应该是mysqll中残留的数据问题） 索性直接删掉docker里的mysql容器 然后重新建了一个 也重新再配置一遍大小写

然后再migrate 就成功了！