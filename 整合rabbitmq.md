# docker安装并测试rabbitmq

## docker安装rabbitmq

```bash
//注意 要按照带management的 带web管理
docker pull rabbitmq:3-management
```

## 运行rabbitmq镜像

```bash
//查看已安装的镜像
docker images

//将rabbitmq端口号映射到主机 将 web管理页面端口号映射到主机
docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq 1c1e1f201079

//查看运行中的镜像
docker ps
```



## 进入web管理页面

浏览器输入10.173.1.213:15672

默认账号：guest

默认密码：guest



## 创建exchange

![image-20200521123856290](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200521123856290.png)

Type为交换机类型 Durability表示持久化 即重启后交换机还存在



## 创建消息队列

![image-20200521123822511](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200521123822511.png)



## 给exchange添加binding

在exchange页面 点击exchange的名称进入	

![image-20200521124553012](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200521124553012.png)



## 发送消息

![image-20200521124935696](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200521124935696.png)



## 接受消息

![image-20200521125025895](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200521125025895.png)

Ack Mode设置为此表示接受消息后自动删除消息



# Spring Boot整合rabbitmq

## 引入pom依赖

```xml
 <!--rabbitmq 测试用-->
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-amqp</artifactId>
 </dependency>
 <!--自定义消息转化器Jackson2JsonMessageConverter所需依赖-->
 <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
 </dependency>
```



## 创建Controller

```java
package life.usc.study.controller;

import life.usc.study.model.Admin;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RabbitmqController {
    @Autowired
    RabbitTemplate rabbitTemplate;

    /*
    * direct方式发送
    * */
    @GetMapping("/addUserToRabbitmqByDirect")
    public Boolean addUserToRabbitmq(@RequestParam("username") String username,
                                    @RequestParam("password") String password) {
        //需要自己写message
//       rabbitTemplate.send();

        //自动转换成message
        Admin adminUser = new Admin();
        adminUser.setName(username);
        adminUser.setPassword(password);
        //设置exchange为amq.direct routingKey为usc
        rabbitTemplate.convertAndSend("amq.direct", "usc", adminUser);
        return true;
    }

    /*
    * fanout方式发送
    * */
    @GetMapping("/addUserToRabbitmqByFanout")
    public Boolean addUserToRabbitmqByFanout(@RequestParam("username") String username,
                                             @RequestParam("password") String password) {
        Admin adminUser = new Admin();
        adminUser.setName(username);
        adminUser.setPassword(password);
        rabbitTemplate.convertAndSend("amq.fanout", "", adminUser);
        return true;
    }

    @GetMapping("/getMessageFromRabbitmq")
    public String getMessageFromRabbitmq() {
        //接受usc队列中的message
        //Object o = rabbitTemplate.receiveAndConvert("usc");
        Message message = rabbitTemplate.receive("usc");
        //System.out.println(o);
        System.out.println(message);
        return "success";
    }
}

```

**一开始addUser始终无法成功 但是getMessage可以成功**

于是使用单元测试 发现报错 ```SimpleMessageConverter only supports String, byte[] and Serializable payloads```

因为rabbitmq默认使用的是```impleMessageConverter```

于是新建一个rabbitmq包 创建配置类（本应写在config包下 但是rabbitmq仅测试 所以···）

```java
package life.usc.study.rabbitmq;

import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyAmqpConfig {
    @Bean
    public MessageConverter messageConverter() {
        //在容器中导入Json的消息转换器
        return new Jackson2JsonMessageConverter();
    }
}
```

成功

**注意：用fanout的适合 路由件不能不写 可以写为空 否则会导致发送成功但是消息队列并没有收到消息**



## 创建Service监听消息队列内容

每当有新消息进入队列 控制台就会打印

```java
package life.usc.study.service;

import life.usc.study.model.Admin;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Service;

@Service
public class RabbitmqService {

    @RabbitListener(queues = "usc")
    public void receive(Admin adminUser) {
        System.out.println("收到消息" + adminUser);
    }

    @RabbitListener(queues = "usc.news")
    public void receive02(Message message) {
//        System.out.println(message.getBody());
//        System.out.println(message.getMessageProperties());
        System.out.println(message);
    }
}

```

在主程序添加@EnableRabbit 注解

```java
package life.usc.study;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.amqp.rabbit.annotation.EnableRabbit;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@MapperScan("life.usc.study.mapper")
@EnableScheduling
@EnableRabbit //开启基于注解的Rabbitmq
public class StudyApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudyApplication.class, args);
    }

}
```



## 用AmqpAdmin实现程序中创建Exchange Queue Binding

```java
    @GetMapping("/createDirectExchange")
    public String createDirectExchange() {
        amqpAdmin.declareExchange(new DirectExchange("amqpdmin.direct"));
        return "success";
    }

    @GetMapping("/createQueue")
    public String createQueue() {
        amqpAdmin.declareQueue(new Queue("adminqp", true));
        return "success";
    }

    @GetMapping("/createBinding")
    public String createBinding() {
        amqpAdmin.declareBinding(new Binding("adminqp", Binding.DestinationType.QUEUE, "amqpdmin.direct", "amqp", null));
        return "success";
    }
```