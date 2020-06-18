# 一、快速构建 Spring Boot 应用

## 实验介绍

#### 实验内容

我们将在这一节学习如何快速构建一个 Spring Boot 项目，之后会对 Spring Boot 项目的目录结构进行简单的介绍，最后是启动 Spring Boot 项目的方法介绍。

#### 实验知识点

- 如何快速构建一个 Spring Boot 项目
- Spring Boot 项目目录结构介绍
- Spring Boot 项目启动

#### 实验环境

- JDK 1.8 或者更高版本

- Spring Boot 2.1.0-RELEASE

- Maven 3+

  

## 目录结构详解

打开项目之后可以看到 Spring Boot 项目的目录结构如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm.png)

如上图所示，Spring Boot 的目录结构主要由以下部分组成：

```properties
lou-springboot
    ├── src/main/java
    ├── src/main/resources
    ├── src/test/java
    └── pom.xml
```

其中 `src/main/java` 表示 Java 程序开发目录，这个目录大家应该都比较熟悉，唯一的区别是 Spring Boot 项目中还有一个主程序类。

`src/main/resources` 表示资源文件目录，与普通的 Spring 项目相比有些区别，如上图所示该目录下有 `static` 和 `templates` 两个目录，是 Spring Boot 项目默认的静态资源文件目录和模板文件目录，在 Spring Boot 项目中是没有 `webapp` 目录的，默认是使用 `static` 和 `templates` 两个文件夹。

`src/test/java` 表示测试类文件夹，与普通的 Spring 项目差别不大。

pom.xml 用于配置项目依赖。

以上即为 Spring Boot 项目的目录结构，与普通的 Spring 项目存在一些差异，不过在平常开发过程中，这个差异的影响并不大，说到差别较大的地方可能是部署和启动方式的差异，接下来十三讲详细介绍 Spring Boot 项目的启动方式。



## Spring Boot 项目启动

#### Main() 方法启动

与普通的 Web 项目相比，Spring Boot 启动项目减少了几个中间步骤，不用去配置 Servlet 容器，也不用打包并且发布到 Servlet 容器再去启动，而是直接运行主方法即可启动项目，开发调试都十分方便也节省开发时间。在你的本机上开发项目时，可以直接在 Eclipse 或者 IDEA 中运行 Spring Boot 主程序类即可，比如现在的项目中有 Application 类，可以直接运行它的 run() 方法，项目就能够正常启动了。

#### Maven 插件启动

由于 pom.xml 文件中引入了 `spring-boot-maven-plugin` 插件依赖，也可以直接使用 Maven 命令来启动 Spring Boot 项目，插件配置如下。如果 pom.xml 文件中没有该 Maven 插件，是无法通过这种方式启动 Spring Boot 项目的，这一点需要注意。

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

启动过程过程如下图所示，首先点击下方工具栏中的 Terminal 打开命令行窗口，之后在命令行中输入命令 `mvn spring-boot：run` 并执行该命令即可启动项目，如下图所示，Spring Boot 项目启动成功。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (1).png)

#### java -jar 命令启动

项目初始化时我们选择的打包方式为 Jar ，因此项目开发完成进行打包时的结果是一个 Jar 包， Java 运行 Jar 包的命令为 `java -jar xxx.jar` ，结合以上两个原因我们可以使用这种方式启动 Spring Boot 项目，接下来我们来演示这一过程。

- 首先，点击下方工具栏中的 Terminal 打开命令行窗口
- 之后使用 Maven 命令将项目打包，执行命令为:`mvn clean package -Dmaven.test.skip=true`，等待打包结果即可
- 打包成功后进入 target 目录，`cd target`
- 最后就是启动已经生成的 Jar 包，执行命令为`java -jar springboot-demo-0.0.1-SNAPSHOT.jar`

这种方式也是 Spring Boot 上线时常用的启动流程，希望不熟悉的朋友都按照以上过程练习几次。



## 下载并解压源码

下载代码压缩包

```shell
wget  https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot.zip
```

解压压缩包

```shell
unzip lou-springboot.zip
```

过程如下所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm.gif)

可以看到我们的代码已经在 WebIDE 中打开，而且可以进行线上编辑。



## 线上环境中启动 Spring Boot 项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要通过`cd lou-springboot`切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动了，过程如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (1).gif)



# 二、SpringBoot项目开发之web项目开发讲解

## 实验介绍

#### 实验内容

我们将在这一节学习 Spring Boot 中如何进行 web 相关功能的开发，主要包括常用的接口开发和 Spring Boot 对于静态资源文件的处理。

#### 实验知识点

- DispatchServlet 配置
- Spring Boot 如何处理静态 web 资源
- 消息转换器 HttpMessageConverter

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+

#### 代码获取

**在学习前，一定要获取源码，因为篇幅原因，只会截取部分关键代码。**

```bash
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-03.zip
unzip lou-springboot-03.zip
cd lou-springboot
```



## 接口开发

接下来，进行接口开发，请大家务必跟随文档完成相关代码。

注意：项目统一创建在 `/home/project/lou-springboot` 目录下。



### hello shiyanlou

因此我们自行实现一个 Controller 来测试一下 Spring Boot 如何处理 web 请求，接下来使用 Spring Boot 做一个简单的接口实现。在 `src/main/java` 目录下新建 `com.lou.springboot.controller` 包，之后在包里新建一个 HelloController 类，编码如下:

```java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    @GetMapping("/hello")
    @ResponseBody
    public String hello() {
        return "hello,shiyanlou";
    }
}
```

这段代码大家应该很熟悉，在 Spring Boot 项目中，大部分功能实现的写法与 Spring 项目开发的写法是相同的，这段代码的含义就是处理请求路径为 `/hello` 的 get 请求，之后返回一段字符串。 编码完成后重新启动项目，点击 WEB 服务按钮，并在浏览器地址栏地址末尾中添加 `/hello`，如下图所示，咱们的第一个 Spring Boot 项目实例就完成了！

![hello](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm2)



## DispatchServlet 自动配置

我们创建了第一个 Spring Boot 项目，在初始化时我们选择了 web 模块(即在 pom 文件中引入了 `spring-boot-starter-web` 依赖)，该模块中包含了 SpringMVC 相关依赖，之后我们开发了第一个 web 功能，仅仅是在控制器类中实现了一个简单的字符串返回方法，并对该方法进行了请求地址映射设置，之后没有做其他配置操作，但是项目启动后就能够进行正常的 web 请求并正常返回数据。 咱们来回想一下在未使用 Spring Boot 开发 web 应用的场景，在平时的 Spring web 项目中，使用 SpringMVC 模块时需要在容器中 SpringMVC 相关的配置 bean，并且在 web.xml 中配置 DispatcherServlet 以及它的 进行请求地址的映射，配置文件如下:

```xml
    <!--Start spring mvc Servlet-->
    <servlet>
        <servlet-name>Spring</Servlet-name>
        <servlet-class>org.springframework.web.Servlet.DispatcherServlet</Servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!--End spring mvc Servlet-->
    <!--Start Servlet-mapping -->
    <servlet-mapping>
        <Servlet-name>Spring</Servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--End Servlet-mapping -->
```

最后启动 Tomcat 服务器装载 DispatcherServlet，接着就可以进行基于 SpringMVC 框架的 web 项目开发和使用，但是 Spring Boot 开发 web 项目时，开发者只需导入 `spring-boot-starter-web` 场景启动器即可，根本无需再进行任何配置就能够使得 DispatherServlet 正常加载并使用，这是因为 **Spring Boot 在项目启动时已经将 DispatherServlet 自动配置到项目中**了，因此我们无需做多余的配置即可完成接口的开发。

不过呢，使用 Spring Boot 开发 web 应用时，有两个需要注意的配置项：

- **server.port** 表示启动后的端口号，默认是 8080
- **server.servlet.context-path** 项目路径，是构成 url 地址的一部分

如果想要修改这两个参数值，可以在 `application.properties` 配置文件中修改，示例如下：

```properties
server.port=8082
server.servlet.context-path=/shiyanlou/
```

这样的话，启动后的端口号就不再是 8080 而是 8082 了，在浏览器访问时也需要加上设置的项目路径，比如前面的 hello 请求，路径就变成了 http://localhost:8082/shiyanlou/hello ，这一点大家需要注意，如果想要对端口和项目访问路径做修改的话，修改这两个参数就可以了，由于实验楼线上环境只能访问 8080 端口，因此端口修改测试的访问需要在你本机上进行。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (3).png)



## 静态资源处理

简单来说，在 B/S 架构中，静态资源一般指 Web 服务器对应目录中的资源文件，客户端发送请求到 Web 服务器后，Web 服务器（比如 Tomcat）直接从文件目录中获取文件并返回给客户端，客户端解析并渲染显示出来，比如 HTML、CSS、JavaScript、图片等文件，这些文件原样返回给客户端，并不会在 Web 服务器端有所改变，在进行 web 项目开发时，对于这些资源的处理也是十分重要的，这一小节我们将会讲解 Spring Boot 是如何处理这些文件的。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (2).png)

通过查看源码我们可以看到，Spring Boot 默认存放静态资源文件的目录有四个，这些目录都在类路径下，它们分别是：

- **/META-INF/resources/**
- **/resources/**
- **/static/**
- **/public/**

也就是说，在进行 web 功能开发时，如果项目中存在静态资源文件，如 HTML、CSS、JavaScript、图片等文件，只要将这些文件放到以上四个文件目录即可正常访问到。接下来，我们新建四个目录并进行测试，类路径对应到项目路径中就是 **src/main/resources** ，新的目录结构如下：

```shell
src/main/resources
  ├── application.properties
  ├── META-INF
  │   └── resources
  ├── public
  ├── resources
  ├── static
  └── templates
```

为了验证，我们在分别在每个目录中新增一个静态资源文件，分别是 /META-INF/resources/test.html 、/resources/shiyanlou.png 、/static/test.css 、/public/test.js :

为 test.html 添加如下内容：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
resource - html
</body>
</html>
```

下载 shiyanlou.png 到对应目录。

```shell
wget https://labfile.oss.aliyuncs.com/courses/1274/shiyanlou.png
```

为 test.css 添加如下内容：

```css
. test {
    font-size: 14px;
}
```

为 test.js 添加如下内容：

```js
function test() {
    console.log('resource - js');
}
```

接下来启动项目，启动成功后可以点击页面上方的 Web 服务直接在显示查看网站效果。

```shell
mvn spring-boot:run
```

之后点击 Web 服务，我们在路径中分别进行接口的访问和静态资源的访问，看一下服务器返回的结果，访问路径为：

- /test.html
- /test.css
- /test.js
- /shiyanlou.png

结果都是正确的，文件虽然在不同的路径中，但是四个路径都是默认的静态资源目录，因此都能够被访问到。当然，有的同学可能会说了，我就想要访问 shiyanlou 目录下的静态资源文件，这种情况该怎么办呢？其实，前面四个静态资源目录是 Spring Boot 默认设置的，如果想要更改，可以修改配置文件中的 `spring.resources.static-locations` 参数。比如，我们想要访问 shiyanlou 目录下的静态资源文件，首先需要在`src/main/resources`下新建 shiyanlou 目录，将静态资源复制到该目录中：

```shell
shiyanlou
  ├── shiyanlou.png
  ├── test.css
  └── test.html
```

我们复制了三个静态资源文件，test.js 依然放在 static 目录中，之后修改配置文件，添加如下设置：

```properties
spring.resources.static-locations=classpath:/shiyanlou/
```

重启后，在浏览器中再次分别访问这些静态资源，你会发现 shiyanlou 目录下的三个文件都能够正常访问，但是 test.js 访问时报错 404 了，因为配置文件中静态资源路径并没有包含 static 目录，shiyanlou 目录下也没有 test.js。如果想要正常访问到该文件，有两个办法，一是将 test.js 文件放到 shiyanlou 目录下，二是修改配置文件为 ：

```properties
spring.resources.static-locations=classpath:/shiyanlou/,classpath:/static/
```

`spring.resources.static-locations` 的参数值可以设置为一个数组。这样修改后，重启后又能够访问所有的静态资源文件了。



## 消息转换器 HttpMessageConverter

以往在使用 SpringMVC 框架开发项目时，大家应该都使用过 `@RequestBody`、`@ResponseBody` 注解进行请求实体的转换和响应结果的格式化输出，以普遍使用的 json 数据为例，这两个注解的作用分别可以将请求中的数据解析成 json 并绑定为实体对象以及将响应结果以 json 格式返回给请求发起者，但 Http 请求和响应是基于文本的，也就是说在 SpringMVC 内部维护了一套转换机制，也就是我们通常所说的“将 json 格式的请求信息转换为一个对象，将对象转换为 json 格式并输出为响应信息 ”，这些就是 HttpMessageConverter 的作用。

举一个简单的例子，我们定义一个实体类，并通过 `@RequestBody`、`@ResponseBody` 注解进行参数的读取和响应，代码如下：

在 com.lou.springboot 下创建如下目录结构：

```shell
com.lou.springboot
     ├── Application.java
     ├── controller
     │   ├── HelloController.java
     │   └── TestController.java
     ├── entity
     │   └── SaleGoods.java
     └── service
         └── HelloService.java
```

SaleGoods.java 的代码内容如下：

```java
// 实体类
package com.lou.springboot.entity;

public class SaleGoods {
    private Integer id;
    private String goodsName;
    private float weight;
    private int type;
    private Boolean onSale;
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getGoodsName() {
        return goodsName;
    }
    public void setGoodsName(String goodsName) {
        this.goodsName = goodsName;
    }
    public float getWeight() {
        return weight;
    }
    public void setWeight(float weight) {
        this.weight = weight;
    }
    public Boolean getOnSale() {
        return onSale;
    }
    public void setOnSale(Boolean onSale) {
        this.onSale = onSale;
    }
    public int getType() {
        return type;
    }
    public void setType(int type) {
        this.type = type;
    }
    @Override
    public String toString() {
        return "SaleGoods{" +
                "id=" + id +
                ", goodsName='" + goodsName + '\'' +
                ", weight=" + weight +
                ", type=" + type +
                ", onSale=" + onSale +
                '}';
    }
}
```

TestController.java 控制器方法如下，拿到参数数值后进行简单的修改并将对象数据返回：

```java
package com.lou.springboot.controller;
import com.lou.springboot.entity.SaleGoods;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
@Controller
public class TestController {
    @RequestMapping(value = "/get/httpmessageconverter", method = RequestMethod.GET)
    @ResponseBody
    public SaleGoods httpMessageConverterTest() {
        SaleGoods saleGoods = new SaleGoods();
        saleGoods.setGoodsName("华为手机");
        saleGoods.setId(1);
        saleGoods.setOnSale(true);
        saleGoods.setType(1);
        saleGoods.setWeight(300);
        return saleGoods;
    }
}
```

编码完成后重启项目，点击 WEB 服务，并在地址栏地址末尾添加 `/get/httpmessageconverter` 进行测试，结果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (4).png)

返回数据如下：

```json
{
    "id": 1,
    "goodsName": "华为手机",
    "weight": 300.0,
    "type": 1,
    "onSale": true
}
```

添加 `@ResponseBody` 注解后，Spring Boot 会直接将对象转换为 json 格式并输出为响应信息，这是将对象作为相应数据的例子。 接下来我们再写一个案例，使用 `@RequestBody` 接收前端请求并将参数转换为后端定义的对象，在 `TestController` 类中添加的方法如下，请求方法为 POST，并使用 `@RequestBody` 注解将前端传输的参数直接转换为 SaleGoods 对象。

```java
    @RequestMapping(value = "/test/httpmessageconverter", method = RequestMethod.POST)
    @ResponseBody
    public SaleGoods httpMessageConverterTest2(@RequestBody SaleGoods saleGoods) {
        System.out.println(saleGoods.toString());
        saleGoods.setType(saleGoods.getType() + 1);
        saleGoods.setGoodsName("商品名：" + saleGoods.getGoodsName());
        return saleGoods;
    }
```

由于是 POST 请求，因此没有直接使用浏览器访问，而是在 static 中新增了 `api-test.html` 页面进行模拟请求,页面当中使用的是 `applicaiton/json` 格式进行 ajax 请求，`resources/static/api-test.html` 代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>lou.SpringBoot | 请求测试</title>
</head>
<body class="hold-transition login-page">
<div style="width:720px;margin:7% auto">
    <div class="content">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-6">
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">接口测试</h5>
                        </div>
                        <div class="card-body">
                            <input id="id" type="text" placeholder="请输入id字段">
                            <input id="goodsName" type="text" placeholder="请输入goodsName字段">
                            <input id="weight" type="number" placeholder="请输入weight字段">
                            <input id="type" type="number" placeholder="请输入type字段">
                            <input id="onSale" type="number" placeholder="请输入onSale字段(0或1)">
                            <h6 class="card-title">接口返回数据如下：</h6>
                            <p class="card-text" id="result2"></p>
                            <a href="#" class="btn btn-primary" onclick="requestPostTest()">发送Post请求</a>
                        </div>
                    </div>
                    <br>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- jQuery -->
<script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>
<script type="text/javascript">
    function requestPostTest() {
        var onSale = true;
        var id = $("#id").val();
        var weight = $("#weight").val();
        var type = $("#type").val();
        var goodsName = $("#goodsName").val();
        var onSaleValue = $("#onSale").val();
        if(onSaleValue !== 1){
            onSale = false;
        }
        var data = {"id": id, "goodsName": goodsName, "weight":weight, "type":type, "onSale":onSale};
        $.ajax({
            type: "POST",  //方法类型
            dataType: "json",  //预期服务器返回的数据类型
            url: "/test/httpmessageconverter",
            contentType: "application/json; charset=utf-8",
            data: JSON.stringify(data),
            success: function (result) {
                $("#result2").html(JSON.stringify(result));
            },
            error: function () {
                $("#result2").html("接口异常，请联系管理员！");
            }
        });
    }
</script>
</body>
</html>
```

重启项目，在地址栏访问 `/api-test.html` 页面，并在页面输入框中输入对应的数据，点击请求按钮，最终获得结果如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\uid18510-20190422-1555919382985 (1))

前端 ajax 传输的数据是 5 个字段，到达后端后直接转换为 SaleGoods 对象。由于消息转换器的存在，对象数据的读取不仅简单而且完全正确，响应时也不用自行封装工具类，使得开发过程变得更加灵活和高效。



## 实验总结

本节实验讲解到此结束，为了后续实践项目的开发，这一节我们简单的介绍了如何使用 Spring Boot 进行接口开发和 web 资源的访问，希望大家参照我的步骤进行测试，本地或者实验楼的线上环境都可以进行演示和测试。

首先对 DispatchServlet 自动配置做一下介绍，让大家知道，在做 web 功能开发时其实与原来使用 SpringMVC 框架开发时类似，只不过 Spring Boot 默认是已经将其配置好而减少我们的工作量，不仅如此，Spring Boot 还做了很多自动配置使得我们能够如此方便的进行 web 项目开发。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10296timestamp1552980188255.png/wm (5).png)

通过官方文档的介绍我们可以发现，Spring Boot 还做了如下的默认配置：

- 自动配置了 `ViewResolver` 视图解析器
- 静态资源文件夹处理
- 自动注册了大量的转换器和格式化器
- 提供了 `HttpMessageConverter` 对请求参数和返回结果进行处理
- 自动注册了 `MessageCodesResolver`
- 默认欢迎页配置
- favicon 自动配置

以上这些特性中，比较重要的内容我们都在文章中进行了讲解和实验，为了更好的掌握到这些内容，同学们也可以根据文章中的内容对接口或者配置进行修改，之后重启项目看一下访问效果是否有更改。



# 三、**SpringBoot整合Thymeleaf模板引擎**

## 实验介绍

#### 实验内容

我们将在这一节学习 Thymeleaf 模板引擎技术，首先会介绍 Thymeleaf 的基本概念，然后再详细介绍 Thymeleaf 模板引擎的属性和表达式语法，并结合实际的代码进行页面功能开发。

#### 实验知识点

- Thymeleaf 模板解读语法介绍
- Thymeleaf 模板标签属性
- Thymeleaf 语法规则介绍及实际功能开发

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+
- 

## 认识 Thymeleaf 模板引擎

#### Thymeleaf

本次教程将对 Thymeleaf 模板引擎进行完整的介绍和实践练习，让大家能够真正的掌握这个技术进行项目的开发工作。

![wm (6)](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm (6).png)

Thymeleaf 应该是目前最受欢迎的模板引擎技术了，Spring Boot 官方也推荐 Java web 开发中使用该技术来替代 JSP 技术，主要由于其“原型即页面”的理念与 Spring Boot 倡导的快速开发非常契合，同时 Thymeleaf 模板引擎技术也确实拥有其他技术所不具备的优点。

#### Thymeleaf 3 的核心特性

Thymeleaf 于 2016 年 5 月 8 日正式发布了 thymeleaf-3.0.0.RELEASE 版本，目前的大部分项目开发过程中也是使用 Thymeleaf 3.0 及以上版本，因此在这里简单的介绍一下 Thymeleaf 3 的特性。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm19)

- **完整的 HTML5 标记支持，全新的解析器**
- **自带多种模板模式，也可扩展支持其他模板格式。**
- **在 web 和非 web 环境（离线）下都可以正常工作**
- **对 Spring web 开发的支持非常完善**
- **独立于 Servlet API**
- **其他特性**:
  - Thymeleaf 3.0 引入了一种新型表达式作为一般 *Thymeleaf 标准表达*系统的一部分：*片段表达式；*。
  - Thymeleaf 3.0 中 *Thymeleaf 标准表达式* 的另一个新特性是 NO-OP（无操作）令牌，由下划线符号（`_`）表示。
  - Thymeleaf 3.0 允许在模板和模板模式下完全（和可选）将模板逻辑与模板本身*解耦*，从而实现 100％-Thymeleaf-free 无逻辑模板。
  - Thymeleaf 3.0 采用全新的方言系统。
  - Thymeleaf 3.0 完成了核心 API 的重构。

Thymeleaf 是高级语言的模板引擎，语法更简单，功能也更强大，接下来的内容将是 Thymeleaf 与 Spring Boot 的整合过程，以及 Thymeleaf 模板引擎的语法介绍。



## Spring Boot 整合 Thymeleaf

这里我们首先通过一个小案例来使用一下 Thymeleaf，之后再对 Thymeleaf 进行详细讲解。下面我们直接下载源码，进行对照学习。

下载源码并解压：

```shell
wget  https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-04.zip
unzip lou-springboot-04.zip
```

切换工作空间到 lou-springboot，进行详细的代码对照。

### 引入 Thymeleaf 依赖

因为 Spring Boot 官方提供了 Thymeleaf 的场景启动器 `spring-boot-starter-thymeleaf` ，因此可以直接在 pom.xml 文件中添加该场景启动器，最终的 pom.xml 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lou.springboot</groupId>
    <artifactId>springboot-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- Thymeleaf 模板引擎依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```



### 创建模板文件

在 `resources/templates` 目录新建模板文件 `thymeleaf.html` ，Thymeleaf 模板引擎的默认后缀名即为 html，新增文件后，首先在模板文件的 `` 标签中导入 Thymeleaf 的名称空间：

```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

导入该名称空间主要是为了 Thymeleaf 的语法提示和 Thymeleaf 标签的使用，之后我们在模板中增加如前文 JSP 中相同的显示内容，最终的模板文件如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf demo</title>
</head>
<body>
<p>description字段值为：</p>
<p th:text="${description}">这里显示的是 description 字段内容</p>
</body>
</html>
```



### 编辑 Controller 代码

在 controller 包下新增 ThymeleafController.java 文件，将模板文件所需的 description 字段赋值并转发至模板文件，编码如下：

```java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import javax.servlet.http.HttpServletRequest;

@Controller
public class ThymeleafController {

    @GetMapping("/thymeleaf")
    public String hello(HttpServletRequest request, @RequestParam(value = "description", required = false, defaultValue = "springboot-thymeleaf") String description) {
        request.setAttribute("description", description);
        return "thymeleaf";
    }
}
```

最终的代码目录结构如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm6)



### 启动并访问

在编码完成后，我们启动项目，由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run`，之后就可以等待项目启动。

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们访问 `/thymeleaf`，可以看到，原来静态 html 文件 `` 标签中的内容已经替换为 "springboot-thymeleaf" 字符串，而不再是默认内容，过程如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm7)

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm8)

## Thymeleaf 设置属性值

通过上面的小案例，相信大家已经对 Thymeleaf 有了一个初步的认识了，接下来，我们将介绍一些关于 Thymeleaf 设置属性值 的内容。

### th:* 属性

`th:text` 对应的是 HTML5 中的 text 属性，除 `th:text` 属性外，Thymeleaf 也提供了其他的标签属性来替换 HTML5 原生属性的值，属性节选如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm9)

- `th:background` 对应 HTML5 中的背景属性
- `th:class` 对应 HTML5 中的 class 属性
- `th:href` 对应 HTML5 中的链接地址属性
- `th:id` 和 `th:name` 分别对应 HTML5 中的 id 和 name 属性...

`th:block` 比较特殊，它是 Thymeleaf 提供的唯一的一个 Thymeleaf 块级元素，其特殊性在于 Thymeleaf 模板引擎在处理``的时候会删掉它本身，而保留其内容。

这里只列举了部分属性，完整内容可以查看 [thymeleaf-attributes](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#setting-value-to-specific-attributes)。



### 修改属性值实践

接下来我们通过一个完整的编码实践来理解以上内容，同样，我们提供源码直接下载，对照源码来理解以上内容。重点关注 `attributes.html` 模板页面：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-05.zip
unzip lou-springboot-05.zip
```

切换工作空间到 lou-springboot。

项目完整结构如下：

```shell
lou-springboot
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               └── controller
    │   │                   └── ThymeleafController.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
    │           ├── attributes.html
    │           ├── simple.html
    │           ├── test.html
    │           └── thymeleaf.html
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
```

attributes.html 代码如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf setting-value-to-specific-attributes</title>
    <meta charset="UTF-8">
</head>
<!-- background 标签-->
<body th:background="${th_background}" background="#D0D0D0">
<!-- text 标签-->
<h1 th:text="${title}">html标签演示</h1>
<div>
    <h5>id、name、value标签:</h5>
    <!-- id、name、value标签-->
    <input id="input1" name="input1" value="1" th:id="${th_id}" th:name="${th_name}" th:value="${th_value}"/>
</div>
<br/>
<div class="div1" th:class="${th_class}">
    <h5>class、href标签:</h5>
    <!-- class、href标签-->
    <a th:href="${th_href}" href="##/">链接地址</a>
</div>
</body>
</html>
```

其中包含 `background` 、`id` 、`name` 、`class` 等 html 标签，设置默认值，并在每个标签体中添加对应的 th 标签读取动态数据，直接选择该文件，右键选择 open with -> Preview 或 Mini Browser 可查看页面效果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm10)

需要注意的是，当前是直接打开该 html 文件并没有通过 web 服务器，此时页面能够直接正常访问且页面中的内容和元素的属性值都是默认值，之后，在 controller 包下新增对应的 Controller 方法并将请求转发至该模板页面，代码如下：

```java
// ThymeleafController.java
package com.lou.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ThymeleafController {

    @GetMapping("/attributes")
    public String attributes(ModelMap map) {
        // 更改 h1 内容
        map.put("title", "Thymeleaf 标签演示");
        // 更改 id、name、value
        map.put("th_id", "thymeleaf-input");
        map.put("th_name", "thymeleaf-input");
        map.put("th_value", "13");
        // 更改 class、href
        map.put("th_class", "thymeleaf-class");
        map.put("th_href", "http://13blog.site");
        return "attributes";
    }
}
```

在编码完成后，我们启动项目，切换到 lou-springboot 目录下通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm11)

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，访问 `/attributes`，过程如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm12)

由于 `th` 标签的存在，页面通过 Thymeleaf 渲染后，与静态页面相比较，内容和元素属性已经动态的切换了，这部分内容可以结合十三提供的源码进行理解，私下练习的时候也可以多尝试几个其他的常用标签。



## Thymeleaf 语法规则

首先，我们来看看 Thymeleaf 官方对于标准表达式特性的总结：

- **表达式语法**

  - 变量表达式： `${...}`
  - 选择变量表达式： `*{...}`
  - 信息表达式： `#{...}`
  - 链接 URL 表达式： `@{...}`
  - 分段表达式： `~{...}`

- **字面量**

  - 字符串： 'one text', 'Another one!' ...
  - 数字： `0`, `34`, `3.0`, `12.3` ...
  - 布尔值： `true`, `false`
  - Null 值： `null`
  - 字面量标记：`one`, `sometext`, `main` ...

- **文本运算**

  - 字符串拼接： `+`
  - 字面量置换: `|The name is ${name}|`

- **算术运算**

  - 二元运算符： `+`, `-`, `*`, `/`, `%`
  - 负号（一元运算符）： (unary operator): `-`

- **布尔运算**

  - 二元运算符： `and`, `or`
  - 布尔非（一元运算符）： `!`, `not`

- **比较运算**

  - 比较： `>`, `<`, `>=`, `<=` (`gt`, `lt`, `ge`, `le`)
  - 相等运算符： `==`, `!=` (`eq`, `ne`)

  比较运算符也可以使用转义字符，比如大于号，可以使用 Thymeleaf 语法 `gt` 也可以使用转义字符`>`

- **条件运算符**

  - If-then: `(if) ? (then)`
  - If-then-else: `(if) ? (then) : (else)`
  - Default: `(value) ?: (defaultvalue)`

- **特殊语法**

  - 无操作： `_`

接下来我们将通过编码的方式将这些知识点进行实践，并将知识点进行串联以更接近实际开发而不是简单的 demo 介绍。



### 简单语法

新建 simple.html 模板页面，该案例主要介绍字面量及简单的运算操作，包括字符串、数字、布尔值等常用的字面量及常用的运算和拼接操作，代码如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf simple syntax</title>
    <meta charset="UTF-8">
</head>
<body>
<h1>Thymeleaf简单语法</h1>
<div>
    <h5>基本类型操作(字符串):</h5>
    <p>一个简单的字符串：<span th:text="'thymeleaf text'">default text</span>.</p>
    <p>字符串连接：<span th:text="'thymeleaf text concat,'+${thymeleafText}">default text</span>.</p>
    <p>字符串连接：<span th:text="|thymeleaf text concat,${thymeleafText}|">default text</span>.</p>
</div>
<div>
    <h5>基本类型操作(数字):</h5>
    <p>一个简单的神奇的数字：<span th:text="2019">1000</span>.</p>
    <p>算术运算： 2019+1=<span th:text="${number1}+1">0</span>.</p>
    <p>算术运算： 14-1=<span th:text="14-1">0</span>.</p>
    <p>算术运算： 673 * 3=<span th:text="673*${number2}">0</span>.</p>
    <p>算术运算： 39 ÷ 3=<span th:text="39/3">0</span>.</p>
</div>
<div>
    <h5>基本类型操作(布尔值):</h5>
    <p>一个简单的数字比较：2019 > 2018=<span th:text="2019&gt;2018"> </span>.</p>
    <p>字符串比较：thymeleafText == 'shiyanlou'，结果为<span th:text="${thymeleafText} == 'shiyanlou'">0</span>.</p>
    <p>数字比较：13 == 39/3 结果为： <span th:text="13 == 39/3">0</span>.</p>
</div>
</body>
</html>
```

其中也有部分变量为后台设置的值，与字面量结合进行实践演示，新增 Controller 方法：

```java
    @GetMapping("/simple")
    public String simple(ModelMap map) {
        map.put("thymeleafText", "shiyanlou");
        map.put("number1", 2018);
        map.put("number2", 3);
        return "simple";
    }
```

之后可重启并查看效果，以上代码的结果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm13)

左侧为静态 html 结果，右侧为 Thymeleaf 模板引擎渲染的结果，可以看到字面量的展示及运算结果。以上为 Thymeleaf 语法中的变量使用方法和简单的运算操作，大家可以参考以上代码进行学习并适当的修改数值以尽快掌握该知识点。



### 表达式语法

表达式包括：变量表达式 `${...}`、选择变量表达式 `*{...}`、信息表达式 `#{...}`、链接 URL 表达式：`@{...}`、分段表达式： `~{...}`，这些表达式一般只能写在 Thymeleaf 模板文件的 `th` 标签内，不然不会生效，表达式语法的主要作用就是获取变量值、获取绑定对象的变量值、国际化变量取值、URL 拼接与生成、 Thymeleaf 模板布局，接下来我们选择一些常用的功能进行介绍和实践。

#### 变量表达式

变量表达式即 OGNL 表达式或 Spring EL 表达式,作用是获取模板中与后端返回数据所绑定对象的值，写法为 `${...}`，这是最常见的一个表达式，在取值赋值、逻辑判断、循环语句中都可以使用该表达式，示例如下：

```html
<!-- 读取参数 -->
<p>算术运算： 2018+1=<span th:text="${number1}+1">0</span>.</p>
<!-- 读取参数并运算 -->
<div th:class="${path}=='links'?'nav-link active':'nav-link'"></div>
<!-- 读取对象中的属性 -->
<p>读取blog对象中title字段：<span th:text="${blog.blogTitle}">default text</span>.</p>
<!-- 循环遍历 -->
<li th:each="blog : ${blogs}">
```

变量表达式也可以使用内置的基本对象：

- ctx : the context object.
- vars : the context variables.
- locale : the context locale.
- request : web 环境下的 HttpServletRequest 对象.
- response :web 环境下的 HttpServletResponse 对象 .
- session : web 环境下的 HttpSession 对象.
- servletContext : web 环境下的 ServletContext 对象.

示例如下：

```html
    <p>读取内置对象中 request 中的内容：<span th:text="${#request.getAttribute('requestObject')}">default text</span>.</p>
    <p>读取内置对象中 session 中的内容：<span th:text="${#session.getAttribute('sessionObject')}">default text</span>.</p>
```

同时，Thymeleaf 还提供了一系列 Utility 工具对象（内置于 Context 中），可以通过 `#` 直接访问，工具类如下：

- dates ： *java.util.Date 的功能方法类。*
- calendars : *类似 #dates，面向 java.util.Calendar*
- numbers : *格式化数字的工具方法类*
- strings : *字符串对象的工具方法类，contains,startWiths,prepending/appending 等等。*
- bools：*对布尔值求值的工具方法。*
- arrays：*对数组的工具方法。*
- lists：*对 java.util.List 的工具方法*
- sets：*对 java.util.Set 的工具方法*
- maps：*对 java.util.Map 的工具方法*

你可以将这些方法视为工具类，通过这些方法可以使得 Thymeleaf 在操作变量时更加方便。

#### 选择(星号)表达式

选择表达式与变量表达式类似，不过它会用一个预先选择的对象来代替上下文变量容器(map)来执行，语法如下： `*{blog.blogId}`，被指定的对象由 th:object 标签属性进行定义，前文中读取 blog 对象的 title 字段可以替换为：

```html
<p th:object="${blog}">读取blog对象中title字段：<span th:text="*{blogTitle}">text</span>.</p>
```

如果不考虑上下文的情况下，两者没有区别，使用 `${...}`读取的内容也完全可以替换为使用`*{...}`进行读取，唯一的区别是使用`*{...}`前可以预先在父标签中通过 `th:object` 定义一个对象并进行操作。

```html
    <p>读取blog对象中title字段：<span th:text="*{blog.blogTitle}">default text</span></p>
    <p>读取text字段：<span th:text="*{text}">default text</span>.</p>
```

#### URL 表达式

`th:href` 对应的是 html 中的 href 标签，它将计算并替换 href 标签中的链接 URL 地址，`th:href` 中可以直接设置为静态地址，也可以使用表达式语法读取到的变量值进行动态拼接 URL 地址。

比如一个详情页 URL 地址：`http://localhost:8080/blog/1`，当使用 URL 表达式时，可以写成这样：

```html
<a th:href="@{'http://localhost:8080/blog/1'}">详情页</a>
```

也可以根据 id 值进行替换，写法为：

```html
<a th:href="@{'/blog/'+${blog.blogId}}">详情页</a>
```

或者也可以写成这样：

```html
<a th:href="@{/blog/{blogId}(blogId=${blog.blogId})">详情页</a>
```

以上三种表达式写法生成 URL 的最终结果都是相同的，开发者可以自己使用字符串拼接的方法组装 URL (第二种写法)，也可以使用 URL 表达式提供的语法进行 URL 组装（第三种写法）。如果有多个参数可以自行拼装字符串，或者使用逗号进行分隔，写法如下：

```html
<a th:href="@{/blog/{blogId}(blogId=${blog.blogId},title=${blog.blogTitle},tag='java')}">详情页</a>
```

最终生成的 URL 为 `http://localhost:8080/blog/1?title=lou-springboot&tag=java` 另外，URL 中以 "/" 开头的路径(比如 /blog/1 )，默认生成的 URL 会加上项目的当前地址形成完整的 URL 。



### 复杂语法

在这一小节里，我们将结合前文中的一些知识点进行更加复杂一些的语法实践，主要是后续教程中会出现的一些方法，比如判断语句、循环语句、工具类使用等等。

test.html 模板文件如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:text="${title}">语法测试</title>
</head>
<body>
<h3>#strings 工具类测试 </h3>
<div th:if="${not #strings.isEmpty(testString)}" >
    <p>testString初始值 : <span th:text="${testString}"/></p>
    <p>toUpperCase : <span th:text="${#strings.toUpperCase(testString)}"/></p>
    <p>toLowerCase : <span th:text="${#strings.toLowerCase(testString)}"/></p>
    <p>equalsIgnoreCase : <span th:text="${#strings.equalsIgnoreCase(testString, '13')}"/></p>
    <p>indexOf : <span th:text="${#strings.indexOf(testString, 'r')}"/></p>
    <p>substring : <span th:text="${#strings.substring(testString, 5, 9)}"/></p>
    <p>startsWith : <span th:text="${#strings.startsWith(testString, 'Spring')}"/></p>
    <p>contains : <span th:text="${#strings.contains(testString, 'Boot')}"/></p>
</div>
<h3>#bools 工具类测试</h3>
<!-- 如果 bool 的值为false的话，该div将不会显示-->
<div th:if="${#bools.isTrue(bool)}">
    <p th:text="${bool}"></p>
</div>
<h3>#arrays 工具类测试</h3>
<div th:if="${not #arrays.isEmpty(testArray)}">
    <p>length : <span th:text="${#arrays.length(testArray)}"/></p>
    <p>contains : <span th:text="${#arrays.contains(testArray, 5)}"/></p>
    <p>containsAll : <span th:text="${#arrays.containsAll(testArray, testArray)}"/></p>
    <p>循环读取 : <span th:each="i:${testArray}" th:text="${i+' '}"/></p>
</div>
<h3>#lists 工具类测试</h3>
<div th:unless="${#lists.isEmpty(testList)}">
    <p>size : <span th:text="${#lists.size(testList)}"/></p>
    <p>contains : <span th:text="${#lists.contains(testList, 0)}"/></p>
    <p>sort : <span th:text="${#lists.sort(testList)}"/></p>
    <p>循环读取 : <span th:each="i:${testList}" th:text="${i+' '}"/></p>
</div>
<h3>#maps 工具类测试</h3>
<div th:if="${not #maps.isEmpty(testMap)}">
    <p>size : <span th:text="${#maps.size(testMap)}"/></p>
    <p>containsKey : <span th:text="${#maps.containsKey(testMap, 'platform')}"/></p>
    <p>containsValue : <span th:text="${#maps.containsValue(testMap, '13')}"/></p>
    <p>读取map中键为title的值 : <span th:if="${#maps.containsKey(testMap,'title')}" th:text="${testMap.get('title')}"/></p>
</div>
<h3>#dates 工具类测试</h3>
<div>
    <p>year : <span th:text="${#dates.year(testDate)}"/></p>
    <p>month : <span th:text="${#dates.month(testDate)}"/></p>
    <p>day : <span th:text="${#dates.day(testDate)}"/></p>
    <p>hour : <span th:text="${#dates.hour(testDate)}"/></p>
    <p>minute : <span th:text="${#dates.minute(testDate)}"/></p>
    <p>second : <span th:text="${#dates.second(testDate)}"/></p>
    <p>格式化: <span th:text="${#dates.format(testDate)}"/></p>
    <p>yyyy-MM-dd HH:mm:ss 格式化: <span th:text="${#dates.format(testDate, 'yyyy-MM-dd HH:mm:ss')}"/></p>
</div>
</body>
</html>
```

增加 Controller 方法如下：

```java
    @GetMapping("/test")
    public String test(ModelMap map) {
        map.put("title", "Thymeleaf 语法测试");
        map.put("testString", "玩转 Spring Boot");
        map.put("bool", true);
        map.put("testArray", new Integer[]{2018,2019,2020,2021});
        map.put("testList", Arrays.asList("Spring", "Spring Boot", "Thymeleaf", "MyBatis", "Java"));
        Map testMap = new HashMap();
        testMap.put("platform", "shiyanlou");
        testMap.put("title", "玩转 Spring Boot");
        testMap.put("author", "十三");
        map.put("testMap", testMap);
        map.put("testDate", new Date());
        return "test";
    }
```

之后重启项目并访问 /test 即可，结果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm14)

在 strings 工具类测试中，我们首先使用了 `th:if` 标签进行逻辑判断，`th:if="${not #strings.isEmpty(testString)}"`即为一条判断语句，`${...}` 表达式中会返回一个布尔值结果，如果为 true 则该 div 中的内容会继续显示，否则将不会显示 `th:if` 所在的主标签。

`#strings.isEmpty` 的作用为字符串判空，如果 testString 为空则会返回 true，而表达式前面的 not 则表示逻辑非运算，即如果 testString 不为空则继续展示该 div 中的内容。

与 `th:if` 类似的判断标签为 `th:unless` ，它与 `th:if` 刚好相反，当表达式中返回的结果为 false 时，它所在标签中的内容才会继续显示，在 `#lists` 工具类测试中我们使用了 `th:unless` ，大家在调试代码时可以比较二者的区别。

Thymeleaf 模板引擎中的循环语句语法为 `th:each="i:${testList}"` ，类似于 JSP 中的 `c:foreach` 表达式，主要是做循环的逻辑，很多页面逻辑在生成时会使用到该语法。

还有读取 Map 对象的方式为 `${testMap.get('title')}` ，与 Java 语言中也很类似。逻辑判断、循环语句这两个知识点是系统开发中比较常用也比较重要的内容，希望大家能够结合代码练习并牢牢掌握。



## Thymeleaf 模板引擎使用注意事项

- 必须引入名称空间

  ```html
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  ```

  即使不引入以上名称空间，静态资源访问以及模板动态访问都不会报错，因此有些开发者可能会忽略这个事情。但是建议在开发过程中最好引入该名称空间，因为引入之后会有 Thymeleaf 代码的语法提示，能够提升开发效率，也减少人为造成的低级错误。

  > 这里引入空间名是需要访问外网的，所有如果非会员用户运行代码效果显示不出来，请自行在本地尝试。

- 禁用模板缓存

  ![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm22)

  Thymeleaf 的默认缓存设置是通过配置文件的 **spring.thymeleaf.cache** 配置属性决定的，通过如上 Thymeleaf 模板的配置属性类 ThymeleafProperties 可以发现该属性默认为 true，因此 Thymeleaf 默认是使用模板缓存的，该设置有助于改善应用程序的性能，因为模板只需编译一次即可，但是在开发过程中不能实时看到页面变更的效果，除非重启应用程序，因此建议将该属性设置为 false，在配置文件中修改如下：

  ```xml
  spring.thymeleaf.cache=false
  ```

- IDEA 中通过 Thymeleaf 语法读取变量时爆红色波浪线问题

  如下图所示，在刚开始使用 Thymeleaf 开发时可能会碰到这种问题，在模板文件中通过 Thymeleaf 语法读取变量时，该变量名称下会出现红色波浪线，也就是错误的标志。

  ![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm21)

  如果不熟的朋友可能会人为自己的模板文件有问题，但其实并不是那么严重，只是由于 IDEA 中默认开启了表达式参数验证，即使在后端的 model 数据中添加了该变量，但是对于前端文件是无法感知的，因此会报这个错误，可以在 IDEA 中默认将验证关闭或者将提示级别修改掉也可以。

  ![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm20)



## 实验总结

本文详细的为大家介绍了 Thymeleaf 模板引擎技术，也进行了 Spring Boot 和 Thymeleaf 的整合操作。最后整理了几项在使用 Thymeleaf 模板引擎开发时需要注意的点，如果你刚接触 Thymeleaf 的话，遇到类似问题不要困扰，这些坑已经有人帮你踩过了。后续再进行讲解的话就是在实践课程中，而不再进行单独介绍了，为了后续课程能够学习的更顺畅，还是要多练习一下基础语法。



# 四、**Spring Boot 处理文件上传及路径回显**

## 实验介绍

#### 实验内容

文件上传是比较常见和被用户熟知的功能模块，常用场景有头像设置、产品预览图、报表文件保存等等，在这些场景中都需要使用到文件上传功能，本篇文章将会对文件上传的大致流程及功能设计进行详细的介绍，并结合实践案例讲解如何使用 Spring Boot 实现文件上传及相关注意事项，对文件上传的整个流程进行闭环。

#### 实验知识点

- Spring MVC 文件上传流程
- Spring Boot 文件上传功能实现
- Spring Boot 文件上传路径回显

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



## Spring MVC 文件上传流程

以往在使用 Spring 的 web 项目开发中，我们通常会使用 Spring MVC 框架提供的文件上传工具类进行文件上传，也就是 **MultipartResolver** ，利用 SpringMVC 实现文件上传功能，离不开对 MultipartResolver 的设置。

MultipartResolver 这个类，你可以将其视为 SpringMVC 实现文件上传功能时的工具类，这个类也只会在文件上传中发挥作用，在配置了具体实现类之后，SpringMVC 中的 DispatcherServlet 在处理请求时会调用 MultipartResolver 中的方法判断此请求是不是文件上传请求，如果是的话 DispatcherServlet 将调用 MultipartResolver 的 `resolveMultipart(request)` 方法对该请求对象进行装饰，并返回一个新的 MultipartHttpServletRequest 供后继处理流程使用。 注意！此时的请求对象会由 HttpServletRequest 类型转换成 MultipartHttpServletRequest 类型，这个类中会包含所上传的文件对象可供后续流程直接使用，而无需自行在代码中实现对文件内容的读取和对象封装的逻辑。

在 Spring Boot 中也是通过该工具类进行文件上传，与普通的 Spring web 项目不同的是，Spring Boot 在自动配置 DispatcherServlet 时也配置好了 MultipartResolver ，而无需再像原来那样在 Spring MVC 配置文件中增加文件上传配置的 bean。



## Spring Boot 文件上传功能实现

这一小节将会通过一个文件上传案例的实现来讲解如何使用 Spring Boot 进行文件上传功能。我们先下载源码，对照代码进行详细学习。

源码下载并解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-06.zip
unzip lou-springboot-06.zip
```

切换工作空间到 lou-springboot,完整项目结构如下：

```shell
lou-springboot
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               ├── config
    │   │               │   └── SpringBootWebMvcConfigurer.java
    │   │               └── controller
    │   │                   └── UploadController.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       │   └── upload-test.html
    │       └── templates
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
                        └── ApplicationTests.java
```



### 常用配置

由于 Spring Boot 自动配置机制的存在，我们并不需要进行多余的设置，只要已经在 pom 文件中引入了 web starter 模块即可直接进行文件上传功能，在前面的实验中我们已经将 web 模块整合到项目中，因此无需再进行整合。虽然不用配置也可以使用文件上传，但是有些开发者可能会在文件上传时有一些特殊的需求，因此也需要对 Spring Boot 中 MultipartFile 的常用设置进行介绍，配置和默认值如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm15)

配置含义注释：

- **spring.servlet.multipart.enabled**

  是否支持 multipart 上传文件，默认支持

- **spring.servlet.multipart.file-size-threshold**

  文件大小阈值，当大于这个阈值时将写入到磁盘，否则存在内存中，（默认值 0 ，一般情况下不用特意修改）

- **spring.servlet.multipart.location**

  上传文件的临时目录

- **spring.servlet.multipart.max-file-size**

  最大支持文件大小，默认 1 M ，该值可适当的调整

- **spring.servlet.multipart.max-request-size**

  最大支持请求大小，默认 10 M

- **spring.servlet.multipart.resolve-lazily**

  判断是否要延迟解析文件（相当于懒加载，一般情况下不用特意修改），默认 false



### 上传功能实现

#### 新建文件上传页面

在 static 目录中新建 upload-test.html，上传页面代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Spring Boot 文件上传测试</title>
</head>
<body>
<form action="/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="file" />
    <input type="submit" value="文件上传" />
</form>
</body>
</html>
```

这应该是大家都很熟悉的一个文件上传页面 demo ，文件上传的请求地址为 /upload，请求方法为 post，需要注意的是在文件上传时要设置 `enctype="multipart/form-data"`，页面中包含一个文件选择框和一个提交框，如下所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm16)

#### 新建文件上传处理 Controller

在 controller 包下新建 UploadController 并编写实际的文件上传逻辑代码，代码如下：

```java
@Controller
public class UploadController {
    // linux下：文件保存路径为 /home/project/upload/ 即当前 project 目录下的 upload 文件夹
    private final static String FILE_UPLOAD_PATH = "/home/project/upload/";

    @RequestMapping(value = "/upload", method = RequestMethod.POST)
    @ResponseBody
    public String upload(@RequestParam("file") MultipartFile file) {
        if (file.isEmpty()) {
            return "上传失败";
        }
        String fileName = file.getOriginalFilename();
        String suffixName = fileName.substring(fileName.lastIndexOf("."));
        //生成文件名称通用方法
        SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd_HHmmss");
        Random r = new Random();
        StringBuilder tempName = new StringBuilder();
        tempName.append(sdf.format(new Date())).append(r.nextInt(100)).append(suffixName);
        String newFileName = tempName.toString();
        try {
            // 保存文件
            byte[] bytes = file.getBytes();
            Path path = Paths.get(FILE_UPLOAD_PATH + newFileName);
            Files.write(path, bytes);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "上传成功";
    }
        
    
    @GetMapping("/upload")
    public String upload() {
        return "upload-test";
    }
}
```

由于已经自动配置了 MultipartFile ，因此能够直接在控制器方法中使用 MultipartFile 读取文件信息， **@RequestParam** 中的文件名称需要与文件上传前端页面设置的 name 属性一致，如果文件为空则返回上传失败，如果不为空则生成一个新的文件名，之后读取文件流并写入到指定的上传路径中，最后返回上传成功。



### 上传路径

需要注意的是文件上传路径的设置，我们在代码中设置的文件保存路径为 `/home/project/upload/` 即当前 project 目录下的 upload 文件夹，`/home/project/upload/` 这种写法是 Linux 系统下的路径写法，因为实验楼线上环境的原因我们在代码里使用了这种写法。 如果你本机是 Windows 系统的话，写法与此不同，比如我们想把文件上传到 D 盘下的 upload 文件夹下，就需要把路径设置代码改为

```java
private final static String FILE_UPLOAD_PATH = "C:\\WJJ\\java\\IDEAIU-workspace\\study\\src\\main\\resources\\upload\\";
```

这一点需要大家注意，两种系统的写法存在一些差异。

回到本节实验中来，如果 project 下没有 upload 目录，你需要在 project 目录下新建 upload 目录，用于存放上传文件，也可以执行 `mkdir /home/project/upload/` 命令来创建 upload 目录。

```shell
cd ..
mkdir upload
```

之后我们启动项目进行文件上传测试。



### 文件上传功能测试

打开命令行工具，将工作目录切换到 lou-springboot 目录下，通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm17)

启动成功后，打开浏览器并输入测试页面地址 /upload-test.html，之后选择需要上传的文件并进行点击上传按钮，之后可以等待后端业务处理了，如果看到上传成功的提示，并且在 upload 目录中看到保存的新文件文件则表示功能实现成功，如果文件较大可以适当调整配置项的值。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm18)

之后我们需要确认文件是否已经上传到服务器上，只需要查看 upload 目录下是否存在该文件即可，结果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm23)

文件上传实验完成！



## Spring Boot 文件上传路径回显

网上很多的 Spring Boot 文件上传教程，通常只讲如何实现上传功能，之后就不再继续讲解了，虽然也给了教程和代码，但是正常情况下，我们上传文件是要实际应用到业务中的，比如图片上传，上传后我们需要知道它的路径，最好能够在页面中直接看到它的回显效果，像前一个步骤中，我们只是成功的完成了文件上传，但是如何去访问这个文件还不得而知。Spring Boot 不像普通的 web 项目可以上传到 webapp 指定目录中，通常的做法是**使用自定义静态资源映射目录，以此来实现文件上传整个流程的闭环**，比如前一小节中的实际案例，在文件上传到 upload 目录后，增加一个自定义静态资源映射，使得 upload 下的静态资源可以通过该映射地址被访问到，新建 config 包，并在包中新增 SpringBootWebMvcConfigurer 类，实现方法如下：

```java
package com.lou.springboot.config;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class SpringBootWebMvcConfigurer implements WebMvcConfigurer {
    public void addResourceHandlers(ResourceHandlerRegistry registry) {

        registry.addResourceHandler("/files/**").addResourceLocations("file:/home/project/upload/"); //windows上需要相应修改
    }
}
```

通过该设置，所有以 /files/ 开头的静态资源请求都会映射到 /home/project/upload 目录下，与前面上传文件时设置目录类似，不同的系统比如 Linux 和 Windows，文件路径的写法不同。

之后修改一下Controller里文件上传时的返回信息，把路径拼装并返回到页面上，以便于我们进行测试，代码修改如下：

```java
return "上传成功，图片地址为：/files/" + newFileName;
```

接下来我们来测试一下上传的文件能否被访问到，重启项目并进行文件上传，过程如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm24)

这样，一个简单的文件上传和回显的案例就完成了，首先将文件上传到指定文件夹中，之后设置静态资源映射规则使得一部分请求返回指定文件夹中的资源进行文件路径回显。



## 实验总结

本篇文章首先对文件上传的流程及功能设计进行了介绍，之后结合实践案例讲解如何使用 Spring Boot 实现文件上传以及如何对已上传的文件进行路径回显，希望通过本实验的讲解，大家都能够掌握如何使用 Spring Boot 进行文件上传，本文演示时都是使用的图片文件，同学们在练习时也可以使用其他格式的文件进行测试，希望大家多多动手练习，以更快的掌握该知识点，后续在图片管理模块实践中我们会继续对该功能进行拓展讲解。



# 五、**Spring Boot 自动配置数据源及操作数据库**

## 实验介绍

#### 实验内容

我们将在这一节介绍 JDBC 相关知识点，并结合实际代码来学习 Spring Boot 中如何进行数据库连接以及在 Spring Boot 项目中操作数据库。

#### 实验知识点

- JDBC
- Spring Boot 连接 MySQL
- 使用 Spring Boot 操作 MySQL 中的数据

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+
- MySQL



## JDBC

JDBC 全称为 Java Data Base Connectivity（Java 数据库连接），主要由接口组成，是一种用于执行 SQL 语句的 Java API。各个数据库厂家基于它各自实现了自己的驱动程序（Driver），如下图所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm25)

Java 程序在获取数据库连接时，需要以 URL 方式指定不同类型数据库的 Driver，在获得特定的 Connection 连接后，可按照 JDBC 规范对不同类型的数据库进行数据操作，代码如下：

```java
//第一步，注册驱动程序  
//com.MySQL.jdbc.Driver  
Class.forName("数据库驱动的完整类名");
//第二步，获取一个数据库的连接  
Connection conn = DriverManager.getConnection("数据库地址","用户名","密码");    
//第三步，创建一个会话  
Statement stmt=conn.createStatement();   
//第四步，执行SQL语句  
stmt.executeUpdate("SQL语句");  
//或者查询记录  
ResultSet rs = stmt.executeQuery("查询记录的SQL语句");  
//第五步，对查询的结果进行处理  
while(rs.next()){  
//操作  
}  
//第六步，关闭连接  
rs.close();  
stmt.close();  
conn.close();
```

对上面几行代码，大家不会陌生，这是我们初学 JDBC 连接时最熟悉的代码了，虽然现在可能用到了一些数据层 ORM 框架(比如 MyBatis 或者 Hibernate )，但是底层实现依然如同上面代码一样，只不过框架对其做了一些封装而已，通过 JDBC 使得我们可以直接使用 Java 程序来对关系型数据库进行操作，接下来我们将对 Spring Boot 如何使用 JDBC 进行实例演示。



## 数据库准备

#### 启动 MySQL

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。

#### 创建数据库和表结构

首先，执行如下命令登陆 MySQL 数据库：

```shell
sudo mysql -u root 
```

因为实验楼线上实验环境中 MySQL 数据库默认并没有设置密码，因此以上命令即可完成登陆。在开发项目之前需要在 MySQL 中先创建数据库和表作为项目演示使用，SQL 语句如下：

```sql
CREATE DATABASE /*!32312 IF NOT EXISTS*/`lou_springboot` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `lou_springboot`;

DROP TABLE IF EXISTS `tb_user`;

CREATE TABLE `tb_user` (
  `id` INT(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` VARCHAR(100) NOT NULL DEFAULT '' COMMENT '登录名',
  `password` VARCHAR(100) NOT NULL DEFAULT '' COMMENT '密码',
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

首先创建了 lou_springboot 的数据库，之后在数据库中新建了一个名称为 tb_user 的数据表，表中有 id , name , password 三个字段，同学们在测试时可以直接将以上 SQL 拷贝到 MySQL 中执行即可。

执行完上面的操作后，再执行以下操作，就可以看见创建的数据库了

```shell
show databases;
```

**希望大家能保存好数据库环境，在后面的实验中还会继续用到这个数据库**



## Spring Boot 连接数据库

源码下载并解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-07.zip
unzip lou-springboot-07.zip
```

切换工作空间到 lou-springboot。项目结构目录如下：

```shell
lou-springboot
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               └── controller
    │   │                   ├── HelloController.java
    │   │                   └── JdbcController.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
                        └── ApplicationTests.java
```

#### pom 依赖

在进行数据库连接前，首先将相关 jar 包依赖引入到项目中，Spring Boot 针对 JDBC 的使用提供了对应的 Starter 包：`spring-boot-starter-jdbc`，方便在 Spring Boot 生态中更好的使用 JDBC，演示项目中使用 MySQL 作为数据库，因此项目中也需要引入 MySQL 驱动包，因此在 pom.xml 文件中新增如下配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lou.springboot</groupId>
    <artifactId>springboot-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- jdbc-starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!-- MySQL 驱动包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

#### 数据库配置信息

为了能够连接上数据库并进行操作，在新增依赖后，我们也需要对数据库的基本信息进行配置，比如数据库地址、账号、密码等信息，这些配置依然是在 `application.properties` 文件中增加，配置如下：

```properties
# datasource config
spring.datasource.url=jdbc:mysql://localhost:3306/lou_springboot?useUnicode=true&characterEncoding=utf8&autoReconnect=true&useSSL=false
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=
```

以上为实验楼线上开发环境的配置信息，如果在你本机运行代码，你需要将对应的信息改为正确的配置，接下来就可以进行连接测试了。值得注意的是，在 Spring Boot 2 中，数据库驱动类推荐使用 `com.mysql.cj.jdbc.Driver`，而不是我们平时比较熟悉的 `com.mysql.jdbc.Driver` 类了。

#### 连接测试

最后，我们编写一个测试类来测试一下目前能否获得与数据库的连接，在测试类 BlogApplicationTests 中添加 datasourceTest() 单元测试方法，源码及注释如下：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {
    // 注入数据源对象
    @Autowired
    private DataSource dataSource;

    @Test
    public void datasourceTest() throws SQLException {
        // 获取数据库连接对象
        Connection connection = dataSource.getConnection();
        // 判断连接对象是否为空
        System.out.println(connection != null);
        connection.close();
    }
}
```

点击运行单元测试方法，如果 connection 对象不为空，则证明数据库连接成功，如下图所示，在对 connection 对象进行判空操作时，得到的结果是 connection 非空。如果数据库连接对象没有正常获取，则需要检查数据库是否连接或者数据库信息是否配置正确。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm26)

由于线上环境的原因，无法像上图一样测试，该测试也只是演示能否获取到数据库连接，后续的实验内容会在实验楼线上环境运行。

至此，Spring Boot 连接数据库的过程就讲解完成了，分别是引入依赖、增加数据库信息配置、注入数据源进行数据库操作。



## Spring Boot 数据源自动配置

通过前一小节的演示大家也能够观察到，我们在配置文件中只需要加上数据库的相关信息即可获取到数据库连接对象，那么 Spring Boot 究竟做了哪些自动配置操作使得我们可以如此简单的就直接获取到数据库连接呢？首先，我们来分析一下注入的 DataSource 数据源对象，更改测试类方法如下：

```java
    @Test
    public void datasourceTest() throws SQLException {
        // 获取数据源类型
        System.out.println("默认数据源为：" + defaultDataSource.getClass());
    }
```

运行测试方法，得到的结果为：

```
默认数据源为：class com.zaxxer.hikari.HikariDataSource
```

基于以上结果我们可以得出结论，在项目启动前，Spring Boot 已经默认向 IOC 容器中注册了一个类型为 `HikariDataSource` 的数据源对象，不然我们在使用 `@Autowired` 进行数据源的引入时肯定会报错。 HikariCP 是 Spring Boot 2.0 默认使用的数据库连接池，自动配置类为 `DataSourceAutoConfiguration.class` 和 `DataSourceConfiguration.class` ，感兴趣的同学可以阅读一下源码理解一下数据源自动配置的过程。

Spring Boot 不仅仅是自动配置了 DataSource 对象，在数据源对象自动配置完成后，Spring Boot 也自动配置了 JdbcTemplate 对象，JdbcTemplate 是 Spring 对 JDBC 的封装，目的是使 JDBC 更加易于使用。 Spring Boot 默认也并没有集成相关的 ORM 框架，而是提供了 JdbcTemplate 来简化开发者对于数据库的操作，关于 ORM 框架集成到 Spring Boot 项目中的教程我会在后续实验中演示，接下来我们使用 JdbcTemplate 来进行数据库的常用 SQL 操作。



## Spring Boot 操作 MySQL

**新建 JdbcController：**

在正确配置数据源之后，开发者可以直接在代码中使用 JdbcTemplate 对象进行数据库操作，接下来就是代码实践了，为了演示方便，我们直接新建一个 Controller 类，并注入 JdbcTemplate 对象，接下来我们创建两个方法，一个是查询 tb_user 中的数据，另一个方法是根据传入的参数向 tb_user 表中新增数据，代码如下：

```java
package com.lou.springboot.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;
import java.util.Map;

@RestController
public class JdbcController {

    //自动配置，因此可以直接通过 @Autowired 注入进来
    @Autowired
    JdbcTemplate jdbcTemplate;

    // 查询所有记录
    @GetMapping("/users/queryAll")
    public List<Map<String, Object>> queryAll() {
        List<Map<String, Object>> list = jdbcTemplate.queryForList("select * from tb_user");
        return list;
    }

    // 新增一条记录
    @GetMapping("/users/insert")
    public Object insert(String name, String password) {
        if (StringUtils.isEmpty(name) || StringUtils.isEmpty(password)) {
            return false;
        }
        jdbcTemplate.execute("insert into tb_user(`name`,`password`) value (\"" + name + "\",\"" + password + "\")");
        return true;
    }
}
```

两个方法，分别取查询数据库记录和新增数据库记录。



## 功能验证

下面我们对整个功能进行验证。

#### 启动 Spring Boot 项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

#### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm2527)

之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们可以在浏览器中输入如下地址进行验证：

- 新增：http://***\**\****.simplelab.cn/users/insert?name=shiyanlou1&password=syl123
- 查询：https://***\**\****.simplelab.cn/users/queryAll

**注意：以上 url 地址星号部分需要按照你的 web 服务地址进行修改。**

如果能够正常获取到记录以及正确向 tb_user 表中新增记录就表示功能整合成功！结果如下所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm28)

#### 验证 MySQL 中的数据

最后，我们登录实验楼演示环境中的 MySQL 数据库，查看表中的数据是否与前一步骤中的数据一致，过程如下：

1. **打开命令行工具**

2. **登录数据库**

   ```shell
   sudo mysql -u root
   ```

3. **查看数据库(这一步可以忽略，主要是确认 lou_springboot 是否已经创建)**

   ```
   show databases;
   ```

4. **切换数据库**

   ```
   use lou_springboot;
   ```

5. **查询 tb_user 表中数据。**

   ```
   select * from tb_user;
   ```

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm29)

最后得到的数据确实与 web 页面中看到的数据一致，功能一切正常，本次实验到此结束！



## 实验总结

在 Spring Boot 项目中操作数据库，仅仅需要几行配置代码即可完成数据库的连接操作，并不需要多余的设置，再加上 Spring Boot 自动配置了 JdbcTemplate 对象，开发者可以直接上手进行数据库的相关开发工作。 希望大家对于 Spring Boot 的自动配置以及 Spring Boot 项目进行数据库操作有了更好的认识，作为知识的补充和拓展，接下来的文章中我们也会讲解 Spring Boot 与 ORM 框架的整合实践，也希望大家能够在课后自行学习源码并根据文中的 SQL 实践进行练习，代码也需要多写，这样的话进步才会更加明显。



# 六、SpringBoot整合MyBatis操作数据库

## 实验介绍

#### 实验内容

我们将在这一节学习 Spring Boot 如何与 MyBatis 进行整合，并对其进行设置和功能讲解，最终实现对于 tb_user 表的增删改查操作。

#### 实验知识点

- Spring Boot 整合 mybatis-spring-boot-starter
- MyBatis 配置
- Spring Boot 整合 MyBatis 进行增删改查操作

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+
- MySQL



## MyBatis 简介

MyBatis 的前身是 Apache 社区的一个开源项目 iBatis，于 2010 年更名为 MyBatis。MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集，使得开发人员更加关注 SQL 本身和业务逻辑，不用再去花费时间关注整个复杂的 JDBC 操作过程。

MyBatis 的优点如下：

- 封装了 JDBC 大部分操作，减少开发人员工作量；
- 相比一些自动化的 ORM 框架，“半自动化”使得开发人员可以自由的编写 SQL 语句，灵活度更高；
- Java 代码与 SQL 语句分离，降低维护难度；
- 自动映射结果集，减少重复的编码工作；
- 开源社区十分活跃，文档齐全，学习成本不高。

虽然前文中已经介绍了 JdbcTemplate 的自动配置及使用，鉴于 MyBatis 框架受众更广且后续实践课程的技术选型包含 MyBatis，因此会在本章节内容中对它做一个详细的介绍，以及如何使用 Spring Boot 整合 MyBatis 框架对数据层进行功能开发。



## mybatis-springboot-starter 介绍

Spring Boot 的核心特性包括简化配置并快速开发，当我们需要整合某一个功能时，只需要引入其特定的场景启动器 ( starter ) 即可，比如 web 模块整合、jdbc 模块整合，我们在开发时只需要在 pom.xml 文件中引入对应的场景依赖即可。Spring 官方并没有提供 MyBatis 的场景启动器，但是 MyBatis 官方却紧紧的抱住了 Spring 的大腿，他们提供了 MyBatis 整合 Spring Boot 项目时的场景启动器，也就是 **mybatis-springboot-starter**，大家通过命名方式也能够发现其中的区别，Spring 官方提供的启动器的命名方式为 **spring-boot-starter-\***，与它还是有一些差别的，接下来我们来介绍一下 **mybatis-springboot-starter** 场景启动器。

其官网地址为 [mybatis-spring-boot](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/index.html)，感兴趣的朋友可以去查看更多内容，官网对 **mybatis-springboot-starter** 的介绍如下所示：

```shell
The MyBatis-Spring-Boot-Starter help you build quickly MyBatis applications on top of the Spring Boot. 
```

MyBatis-Spring-Boot-Starter 可以帮助开发者快速创建基于 Spring Boot 的 MyBatis 应用程序，那么使用 **MyBatis-Spring-Boot-Starter**可以做什么呢？

- 构建独立的 MyBatis 应用程序
- 零模板
- 更少的 XML 配置代码甚至无 XML 配置



## Spring Boot 整合 MyBatis 过程

接下来，我们还是通过一个案例来理解。

源码下载并解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-08.zip
unzip lou-springboot-08.zip
```

切换工作空间到 lou-springboot。项目结构目录如下：

```shell
lou-spring-boot
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               ├── controller
    │   │               │   ├── HelloController.java
    │   │               │   ├── JdbcController.java
    │   │               │   └── MyBatisController.java
    │   │               ├── dao
    │   │               │   └── UserDao.java
    │   │               └── entity
    │   │                   └── User.java
    │   └── resources
    │       ├── application.properties
    │       ├── mapper
    │       │   └── UserDao.xml
    │       └── templates
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
                        └── ApplicationTests.java
```



### 添加依赖

如果要将其整合到当前项目中，首先我们需要将其依赖配置增加到 pom.xml 文件中，mybatis-springboot-starter 的最新版本为 1.3.2，需要 Spring Boot 版本达到 1.5 或者以上版本，同时，我们需要将数据源依赖和 jdbc 依赖也添加到配置文件中(如果不添加的话，将会使用默认数据源和 jdbc 配置)，由于前文中已经将这些配置放入 pom.xml 配置文件中，因此只需要将 mybatis-springboot-starter 依赖放入其中即可，更新后的 pom 文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lou.springboot</groupId>
    <artifactId>springboot-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!-- 引入 MyBatis 场景启动器，包含其自动配置类及 MyBatis 3 相关依赖 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

这样，MyBatis 的场景启动器也整合进来项目中了。



### application.properties 配置

Spring Boot 整合 MyBatis 时几个比较需要注意的配置参数：

- **mybatis.config-location**

  配置 mybatis-config.xml 路径，mybatis-config.xml 中配置 MyBatis 基础属性，如果项目中配置了 mybatis-config.xml 文件需要设置该参数

- **mybatis.mapper-locations**

  配置 Mapper 文件对应的 XML 文件路径

- **mybatis.type-aliases-package**

  配置项目中实体类包路径

```xml
mybatis.config-location=classpath:mybatis-config.xml
mybatis.mapper-locations=classpath:mapper/*Dao.xml
mybatis.type-aliases-package=com.lou.springboot.entity
```

我们只配置 mapper-locations 即可，最终的 application.properties 文件如下：

```properties
# datasource config
spring.datasource.url=jdbc:mysql://localhost:3306/lou_springboot?useUnicode=true&characterEncoding=utf8&autoReconnect=true&useSSL=false
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=

mybatis.mapper-locations=classpath:mapper/*Dao.xml
```



### 启动类增加 Mapper 扫描

在启动类中添加对 Mapper 包扫描 `@MapperScan`，Spring Boot 启动的时候会自动加载包路径下的 Mapper 接口：

```java
@SpringBootApplication
@MapperScan("com.lou.springboot.dao") //添加 @Mapper 注解
public class Application {
    public static void main(String[] args) {
        System.out.println("启动 Spring Boot...");
        SpringApplication.run(Application.class, args);
    }
}
```

当然也可以直接在每个 Mapper 接口上面添加 `@Mapper` 注解，但是如果 Mapper 接口数量较多，在每个 Mapper 加注解是挺繁琐的，建议使用扫描注解。



## Spring Boot 整合 MyBatis 实例

本次实验我们依然使用前一个实验中的 tb_user 表结构，并使用 MyBatis 进行增删改查操作，接下来是功能实现步骤。



### 新建实体类和 Mapper 接口

在 entity 包下新建 User 类，将 tb_user 中的字段映射到该实体类中：

```java
package com.lou.springboot.entity;

public class User {

    private Integer id;
    private String name;
    private String password;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

在 dao 包中新建 UserDao 接口，并定义增删改查四个接口：

```java
package com.lou.springboot.dao;
import com.lou.springboot.entity.User;
import java.util.List;
import java.util.Map;

/**
 * @author 13
 * MyBatis 测试
 */
public interface UserDao {
    /**
     * 返回数据列表
     *
     * @return
     */
    List<User> findAllUsers();

    /**
     * 添加
     *
     * @param User
     * @return
     */
    int insertUser(User User);

    /**
     * 修改
     *
     * @param User
     * @return
     */
    int updUser(User User);

    /**
     * 删除
     *
     * @param id
     * @return
     */
    int delUser(Integer id);
}
```



### 创建 Mapper 接口的映射文件

在 resources/mapper 目录下新建 Mapper 接口的映射文件 UserDao.xml，之后进行映射文件的编写。

1. 首先，定义映射文件与 Mapper 接口的对应关系，比如该示例中，需要将 UserDao.xml 的与对应的 UserDao 接口类之间的关系定义出来：

   ```xml
       <mapper namespace="com.lou.springboot.dao.UserDao">
   ```

2. 之后，配置表结构和实体类的对应关系：

   ```xml
       <resultMap type="com.lou.springboot.entity.User" id="UserResult">
           <result property="id" column="id"/>
           <result property="name" column="name"/>
           <result property="password" column="password"/>
       </resultMap>
   ```

3. 最后，针对对应的接口方法，编写具体的 SQL 语句，最终的 UserDao.xml 文件如下：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
       <mapper namespace="com.lou.springboot.dao.UserDao">
       <resultMap type="com.lou.springboot.entity.User" id="UserResult">
           <result property="id" column="id"/>
           <result property="name" column="name"/>
           <result property="password" column="password"/>
       </resultMap>
       <select id="findAllUsers" resultMap="UserResult">
           select id,name,password from tb_user
           order by id desc
       </select>
       <insert id="insertUser" parameterType="com.lou.springboot.entity.User">
           insert into tb_user(name,password)
           values(#{name},#{password})
       </insert>
       <update id="updUser" parameterType="com.lou.springboot.entity.User">
           update tb_user
           set
           name=#{name},password=#{password}
           where id=#{id}
       </update>
       <delete id="delUser" parameterType="int">
           delete from tb_user where id=#{id}
       </delete>
   </mapper>
   ```



### 新建 MyBatisController

为了对 MyBatis 进行功能测试，在 controller 包下新建 MyBatisController 类，并新增 4 个方法分别接收对于 tb_user 表的增删改查请求，代码如下：

```java
package com.lou.springboot.controller;

import com.lou.springboot.dao.UserDao;
import com.lou.springboot.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;
import java.util.List;
import java.util.Map;

@RestController
public class MyBatisController {

    @Resource
    UserDao userDao;

    // 查询所有记录
    @GetMapping("/users/mybatis/queryAll")
    public List<User> queryAll() {
        return userDao.findAllUsers();
    }

    // 新增一条记录
    @GetMapping("/users/mybatis/insert")
    public Boolean insert(String name, String password) {
        if (StringUtils.isEmpty(name) || StringUtils.isEmpty(password)) {
            return false;
        }
        User user = new User();
        user.setName(name);
        user.setPassword(password);
        return userDao.insertUser(user) > 0;
    }

    // 修改一条记录
    @GetMapping("/users/mybatis/update")
    public Boolean insert(Integer id, String name, String password) {
        if (id == null || id < 1 || StringUtils.isEmpty(name) || StringUtils.isEmpty(password)) {
            return false;
        }
        User user = new User();
        user.setId(id);
        user.setName(name);
        user.setPassword(password);
        return userDao.updUser(user) > 0;
    }

    // 删除一条记录
    @GetMapping("/users/mybatis/delete")
    public Boolean insert(Integer id) {
        if (id == null || id < 1) {
            return false;
        }
        return userDao.delUser(id) > 0;
    }
}
```



### 创建数据库和表结构

数据库和表结构在实验五已经创建了，如果没有保存环境的用户请参照实验八的 SQL 语句，如果保存了环境则无需进行该步骤。



### 线上环境中启动 Spring Boot 项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm30)

之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们可以在浏览器地址栏中地址后面添加如下地址进行验证：

- 查询：/users/mybatis/queryAll
- 新增：/users/mybatis/insert?name=mybatis1&password=1233333
- 修改：/users/mybatis/update?id=3&name=mybatis2&password=1233222
- 删除：/users/mybatis/delete?id=3

如果能够正常获取到记录以及正确向 tb_user 表中新增和修改记录就表示功能整合成功！结果如下所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm31)











## 功能测试

#### 启动 MySQL

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。



### 验证 MySQL 中的数据

最后，我们登录实验楼演示环境中的 MySQL 数据库，查看表中的数据是否与前一步骤中的数据一致，如果最后得到的数据确实与 web 页面中看到的数据一致则表示功能整合一切正常，本次实验到此结束！



## 实验总结

这节实验课程介绍了 MyBatis 框架相关知识点介绍以及如何整合 Spring Boot 与 MyBatis，并以 tb_user 表为例演示了如何使用 MyBatis 进行增、删、改、查操作，也希望大家能够在课后自行学习源码并根据文中的实验步骤进行练习，代码也需要多写，这样的话进步才会更加明显。



# 七、**Mybatis-Generator自动生成代码**

## 实验介绍

#### 实验内容

我们将在这一节学习 Mybatis Generator 插件的相关知识，以及如何将 Spring Boot 与 Mybatis Generator 进行整合，并自动生成 DAO 层的相关代码，从而减少开发人员的工作量，使用工具来提升开发者的工作效率。

#### 实验知识点

- 认识 Mybatis Generator 插件
- Spring Boot 整合 Mybatis Generator 插件步骤
- 使用 Mybatis Generator 自动生成 DAO 层代码

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+
- MySQL



## 认识 MyBatis-Generator

#### MyBatis-Generator 介绍

MyBatis Generator 是 MyBatis 官方提供的代码生成器插件，可以用于 MyBatis 和 iBatis 框架的代码生成，支持所有版本的 MyBatis 框架以及 2.2.0 版本及以上的 iBatis 框架。

在前文中我们也介绍了如何使用 MyBatis 进行数据库操作，在进行功能开发时，一张表我们需要编写实体类、DAO 接口类以及 Mapper 文件，这些是必不可少的文件，如果表的数量较大，我们就需要重复的去创建实体类、Mapper 文件以及 DAO 类并且需要配置它们之间的依赖关系，这无疑是一个很麻烦的事情，而 MyBatis Generator 插件就可以帮助我们去完成这些开发步骤，使用该插件可以帮助开发者自动去创建实体类、Mapper 文件以及 DAO 类并且配置好它们之间的依赖关系，我们可以直接在项目中使用这些代码，后续只需要关注业务方法的开发即可。



### 为什么要使用 MyBatis-Generator

通过介绍我们可以将 MyBatis-Generator 简单的理解为一个 MyBatis 框架的代码生成器，至于我推荐大家使用它的原因，理由整理如下：

- 减少重复工作
- 减少人为操作带来的错误
- 提升开发效率
- 使用灵活

MyBatis 属于半自动 ORM 框架，在使用这个框架中，工作量最大的就是书写 Mapper 及相关映射文件，同时需要配置其依赖关系，由于手动书写很容易出错，我们可以利用 MyBatis-Generator 来帮我们自动生成文件。

其次，一个项目通常有很多张表，比如接下来要开发的 my_blog 项目，所有的实体类、SQL 语句都要手动编写，这是一个比较繁琐的过程，如果有这么一个插件能够适当的减少我们的一些工作量，自动将这些代码生成到对应的项目目录中，将是一件十分幸福的事情，当然，该插件生成的代码都是常用的一些增删改查代码，如果有其他功能或方法依然需要自己去编写代码，它只是一个提升效率的工具，给予开发者一定程度的帮助。



## MyBatis-Generator 整合

接下来，我们还是通过一个案例来理解 MyBatis-Generator 插件整合以及使用它来生成代码的步骤。



## 添加依赖

如果要使用该插件，首先我们需要将其依赖配置增加到 pom.xml 文件中，增加的配置文件如下，将该部分配置放到原 pom.xml 文件的 plugins 节点下即可：

```xml
    <plugin>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-maven-plugin</artifactId>
        <version>1.3.5</version>
        <dependencies>
            <dependency>
                <groupId> mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version> 5.1.39</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-core</artifactId>
                <version>1.3.5</version>
            </dependency>
        </dependencies>
        <executions>
            <execution>
                <id>Generate MyBatis Artifacts</id>
                <phase>package</phase>
                <goals>
                    <goal>generate</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <verbose>true</verbose>
            <!-- 是否覆盖 -->
            <overwrite>true</overwrite>
            <!-- MybatisGenerator的配置文件位置 -->
            <configurationFile>src/main/resources/mybatisGeneratorConfig.xml</configurationFile>
        </configuration>
    </plugin>
```

如果是在本地开发的话，接下来等待 jar 包及相关依赖下载完成即可。



### 新增 MyBatis-Generator 的配置文件

在添加插件依赖到 pom.xml 文件中时，我们定义了 Mybatis Generator 的配置文件位置为 src/main/resources/mybatisGeneratorConfig.xml，该文件即为代码生成器插件最重要的配置文件，后续生成代码的规则、数据库连接信息、代码生成后的存放目录等等配置都需要在该配置文件中定义，配置文件内容及相关注释如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="my-blog-generator-config" targetRuntime="MyBatis3">
        <!-- 生成的Java文件的编码 -->
        <property name="javaFileEncoding" value="utf-8"/>
        <!-- 格式化java代码 -->
        <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter"/>
        <!-- 格式化XML代码 -->
        <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter"/>
        <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>
        <!--创建Java类时对注释进行控制-->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库地址及登陆账号密码 改成你自己的配置-->
        <jdbcConnection
                driverClass="com.mysql.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/lou_springboot"
                userId="root"
                password="">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!--生成实体类设置-->
        <javaModelGenerator targetPackage="com.lou.springboot.entity" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--生成Mapper文件设置-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!--生成Dao类设置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.lou.springboot.dao"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!--需要自动生成代码的表及对应的类名设置-->
        <table tableName="generator_test" domainObjectName="GeneratorTest"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false">
        </table>
    </context>
</generatorConfiguration>
```

需要自动生成代码的表及对应类名的配置是写在 `table` 标签中的，如上所示，generator_test 表对应的实体类可以配置为 GeneratorTest，这些是开发者自定义的，也可以改成其他合适的类名。 如果有多张表同时生成，增加多个 `table` 标签配置即可。



### 生成代码

配置文件和表结构都处理完成后就可以进行代码生成步骤了，这里整理了两种方式来进行代码生成：

- 方式一：IDEA 工具中的 Maven 插件中含有 mybatis-generator 的选项，点击 generate 即可，如下图所示：

  ![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm32)

- 方式二：执行 mvn 如下命令来进行代码生成，这是不通过开发工具提供的图形界面来进行的步骤。

  ```shell
  mvn mybatis-generator:generate
  ```

执行这些步骤之后就可以看到实体类、DAO 接口类、Mapper 文件已经生成在对应的目录中了，接下来我们会进行演示。



### 将 DAO 接口注册至 IOC 容器

接下来是最后一个步骤，在代码生成后，我们需要在 DAO 接口类上增加 `@Mapper` 注解，将其注册到 Spring 的 IOC 容器中以供其他类调用，在生成的文件中默认是没有这个注解的，或者是在主类中添加 `@MapperScan` 注解将相应包下的所有 Mapper 接口扫描到容器中。



## 功能验证

下面我们对整个功能进行验证。

### 启动 MySQL

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。



### 创建数据库和表结构

首先，执行如下命令登陆 MySQL 数据库：

```shell
sudo mysql -u root 
```

因为实验楼线上实验环境中 MySQL 数据库默认并没有设置密码，因此以上命令即可完成登陆，登陆后执行如下命令创建表：

```sql
USE lou_springboot;
/*Table structure for table `generator_test` */
CREATE TABLE `generator_test` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `test` varchar(100) NOT NULL COMMENT '测试字段',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

如果不存在我们本节课所需的 lou_springboot 数据库，则需要参照前面的实验将数据库建好。



### 下载并解压代码

本节课程的代码已经上传，直接下载至实验环境中即可，下载之后执行如下命令进行解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/spring-boot-mybatis-generator.zip
unzip spring-boot-mybatis-generator.zip
```

接下来就能够看到本次代码的代码结构及相关配置文件。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm33)





### 执行代码生成命令

由于默认是在 project 目录下，因此执行生成代码的 mvn 命令前，首先需要切换到 spring-boot-mybatis-generator 目录下，之后执行命令 `mvn mybatis-generator:generate` ，接着就能够看到对应的代码已经生成在项目目录中了，过程如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm39)



## 总结

至此，MyBatis-Generator 插件整合完成，也生成了对应的代码，接下来在项目开发时，我们都会使用它来生成我们 DAO 层的代码，之后再根据功能对 Mapper 文件进行修改，使用该插件时的注意事项如下：

- 由于数据库连接信息是单独定义在配置文件中的，所以在使用时一定要仔细检查，确保数据库连接正常。
- 在生成代码后，建议将 table 标签注释掉，不然在打包或者生成其他表的代码时会将原先已经生成的代码覆盖，可能会造成一些小麻烦。
- 代码生成后将 DAO 接口注册至 IOC 容器以供业务方法调用。



# 八、**Spring Boot 中的事务处理**

## 实验介绍

#### 实验内容

前面几篇文章主要讲解了在 Spring Boot 项目中对数据层的操作，本章节将介绍在 Spring Boot 项目中如何进行事务处理。所有的数据访问技术都离不开事务处理，否则将会造成数据不一致，在目前企业级应用开发中，事务管理是必不可少的。

#### 实验知识点

- 数据库事务介绍
- 声明式事务
- Spring Boot 处理数据库事务

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+
- MySQL



## 数据库事务简介

数据库事务是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。 事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源。

通过将一组相关操作组合为一个要么全部成功要么全部失败的单元，可以简化错误恢复并使应用程序更加可靠。一个逻辑工作单元要成为事务，必须满足所谓的 ACID（原子性、一致性、隔离性和持久性）属性，事务是数据库运行中的逻辑工作单位，由数据库中的事务管理子系统负责事务的处理。



## Spring Boot 事务机制

**首先需要明确的一点是 Spring Boot 事务机制实质上就是 Spring 的事务机制**，是采用统一的机制处理来自不同数据访问技术的事务处理，只不过 Spring Boot 基于自动配置的特性作了部分处理来节省开发者的配置工作，这一知识点我们会结合部分源码进行讲解。

Spring 事务管理分两种方式：

- 编程式事务，指的是通过编码方式实现事务；
- 声明式事务，基于 AOP，将具体业务逻辑与事务处理解耦。

### 声明式事务

声明式事务是建立在 AOP 机制之上的，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。

声明式事务最大的优点，就是通过 AOP 机制将具体业务逻辑与事务处理解耦，不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，因此在实际使用中声明式事务用的比较多。

声明式事务有两种方式：一种是在 XML 配置文件中做相关的事务规则声明；另一种是基于 `@Transactional` 注解的方式（`@Transactional` 注解是来自 `org.springframework.transaction.annotation` 包），便可以将事务规则应用到业务逻辑中。



### 未使用 Spring Boot 时的事务配置

下面这个配置文件是普通的 SSM 框架整合时的事务配置，相信大家都比较熟悉这段配置代码：

```xml
    <!-- 事务管理 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置事务通知属性 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!-- 定义事务传播属性 -->
        <tx:attributes>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="import*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="upd*" propagation="REQUIRED"/>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="set*" propagation="REQUIRED"/>
            <tx:method name="remove*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="get*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!-- 配置事务切面 -->
    <aop:config>
        <aop:pointcut id="serviceOperation"
                      expression="(execution(* com.ssm.demo.service.*.*(..)))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="serviceOperation"/>
    </aop:config>
```

通过这段代码我们也能够看出声明式事务的配置过程：

1. 配置事务管理器
2. 配置事务通知属性
3. 配置事务切面

这样配置后，相关方法在执行时都被纳入事务管理下了，一旦发生异常，事务会正确回滚。



### Spring Boot 项目中的事务控制

在 SpringBoot 中，建议采用注解 `@Transactional` 进行事务的控制，只需要在需要进行事务管理的方法或者类上添加 `@Transactional` 注解即可，接下来我们来通过代码讲解。

下载源码并解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-10.zip
unzip lou-springboot-10.zip
```

切换工作空间到 lou-springboot。项目结构目录如下：

```shell
lou-springboot
├── pom.xml
├── README.md
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── lou
    │   │           └── springboot
    │   │               ├── Application.java
    │   │               ├── controller
    │   │               │   ├── HelloController.java
    │   │               │   ├── JdbcController.java
    │   │               │   ├── MyBatisController.java
    │   │               │   └── TransactionTestController.java
    │   │               ├── dao
    │   │               │   └── UserDao.java
    │   │               ├── entity
    │   │               │   └── User.java
    │   │               └── service
    │   │                   └── TransactionTestService.java
    │   └── resources
    │       ├── application.properties
    │       ├── mapper
    │       │   └── UserDao.xml
    │       └── templates
    └── test
        └── java
            └── com
                └── lou
                    └── springboot
                        └── ApplicationTests.java
```



### 新建 TransactionTestService.java

首先新建 service 包作为业务代码包，事务处理一般在 service 层做，当然在 controller 层中处理也可以，但是建议还是在业务层进行处理，之后在包中新建 TransactionTestService 类，代码如下：

```java
package com.lou.springboot.service;
import com.lou.springboot.dao.UserDao;
import com.lou.springboot.entity.User;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import javax.annotation.Resource;

@Service
public class TransactionTestService {
    @Resource
    UserDao userDao;

    public Boolean test1() {
        User user = new User();
        user.setPassword("test1-password");
        user.setName("test1");
        // 在数据库表中新增一条记录
        userDao.insertUser(user);
        // 发生异常
        System.out.println(1 / 0);
        return true;
    }

    @Transactional
    public Boolean test2() {
        User user = new User();
        user.setPassword("test2-password");
        user.setName("test2");
        // 在数据库表中新增一条记录
        userDao.insertUser(user);
        // 发生异常
        System.out.println(1 / 0);
        return true;
    }
}
```

首先在类上添加 `@Service` 将其注册到 IOC 容器中管理，之后注入 UserDao 对象以实现后续的数据层操作，最后实现两个业务方法 `test1()` 和 `test2()`，二者实现类似，只是两个方法添加的用户对象名称和密码字符串不同，且 `test2()` 方法上添加了 `@Transactional` 注解，而 `test1()` 方法上并没有添加，在方法中我们都添加了一句代码，让数字 1 去除以 数字 0，这段代码一定会出现异常，我们用这个来模拟在发生异常时事务处理能否成功。

按照正常理解，在执行 SQL 语句后，一旦发生异常，这次数据库更改一定会被事务进行回滚，正常情况下数据库中会有 `test1` 的数据而没有 `test2` 的数据，因为 `test1()` 方法并没有纳入事务管理中，而 `test2()` 方法由于加上了 `@Transactional` 注解是会被事务管理器处理的，那么接下来我们就来执行两个业务层方法，看看数据库中的数据变化。



### 新建 TransactionTestController.java

为了方便在实验楼线上环境进行方法测试，我们还是将方法调用写到 Controller 类中进行线上访问测试，在 controller 包中新建 TransactionTestController.java 类，代码如下：

```java
package com.lou.springboot.controller;
import com.lou.springboot.service.TransactionTestService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.annotation.Resource;
@Controller
public class TransactionTestController {
    @Resource
    private TransactionTestService transactionTestService;
    // 事务管理测试
    @GetMapping("/transactionTest")
    @ResponseBody
    public String transactionTest() {
        // test1 未添加 @Transactional 注解
        transactionTestService.test1();
        // test2 添加了 @Transactional 注解
        transactionTestService.test2();
        return "请求完成";
    }
}
```

这段代码很简单，就是实现一个控制器方法处理 /transactionTest 请求，方法中会进行业务层 `test1()` 方法和 `test2()` 方法的调用，也就是说在项目启动后一旦访问 /transactionTest 请求就能够看到这次事务处理测试的结果了。



## 功能测试

#### 启动 MySQL

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。

> 这里还是会继续使用前面用到到数据库，如果没有创建，请根据前面到操作自行补充。



### 线上环境中启动 Spring Boot 项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm34)

之后会在浏览器中弹出 https://***\**\***.simplelab.cn 页面，我们可以在浏览器中输入 /transactionTest 地址进行验证，最终得到的结果如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm35)

这是预期中会返回的数据，因为调用了 `test1()` 方法和 `test2()` 方法肯定会发生异常，之后我们需要去查看 tb_user 表中的数据，访问 /users/mybatis/queryAll 即可查看所有记录，当然也可以直接查看数据库中的记录，最终结果如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10305timestamp1552986346820.png/wm)

最终查到的记录与我们之前分析的一样，`test1()` 方法由于没有添加 `@Transactional` 注解，所以在发生异常后事务没有回滚，insert 语句依然起到了作用，而 `test2()` 方法添加了 `@Transactional` 注解，所以在发生异常后事务正常回滚，数据库中并没有 test2 的记录，本次实验到此结束！



## Spring Boot 事务管理器自动配置

在讲解声明式事务时，我们提到了声明式事务的配置过程，首先需要配置事务管理器，但是我们在开发时并没有进行该管理器的配置但是事务管理却起到了作用，这是为什么呢？

答案是 Spring Boot 在启动过程中已经将该对象自动配置完成了，所以我们在 Spring Boot 项目中可以直接使用 `@Transactional` 注解来处理事务，感兴趣的同学可以查看源码进行学习，事务管理器的自动配置类如下：

- org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration
- org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration

可以在你本地开发工具 (IDEA 或者 Eclipse) 中搜索这两个类的源码来看。



## @Transactional 事务实现机制

**@Transactional 不仅可以注解在方法上，也可以注解在类上。当注解在类上时，意味着此类的所有 public 方法都是开启事务的。如果类级别和方法级别同时使用了 `@Transactional` 注解，则使用在类级别的注解会重载方法级别的注解。**

在应用系统调用声明了 `@Transactional` 的目标方法时，Spring Framework 默认使用 AOP 代理，在代码运行时生成一个代理对象，根据 `@Transactional` 的属性配置信息，这个代理对象决定该声明 `@Transactional` 的目标方法是否由拦截器 TransactionInterceptor 来使用拦截，在 TransactionInterceptor 拦截时，会在目标方法开始执行之前创建并加入事务，并执行目标方法的逻辑, 最后根据执行情况是否出现异常，利用抽象事务管理器 AbstractPlatformTransactionManager 操作数据源 DataSource 提交或回滚事务。



## 实验总结

由于 Spring Boot 的自动配置机制，我们在使用 Spring Boot 开发项目时如果需要进行事务处理，直接在对应方法上添加 `@Transactional` 即可，Spring Boot 事务机制实质上就是 Spring 的事务机制，只不过在配置方式上有所不同，Spring Boot 更简便一些，所以只要你对事务处理有一定的理解，那么在使用 Spring Boot 处理事务时也不会遇到问题。



# 九、**项目实践之 Ajax 技术使用教程**

## 实验介绍

#### 实验内容

由于我们本次实践教程是一个 web 项目，因此会介绍在 web 项目中前后端交互的方式，我们通常选择的方案是在浏览器端通过使用 Ajax 技术调用后端提供的 api 接口来完成异步请求和页面的交互更新，接下来我们来介绍一下什么是 Ajax 以及如何在项目中使用 Ajax 进行功能开发。

#### 实验知识点

- Ajax 简介
- Ajax 工作流程
- Ajax 调用接口实践

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+

#### 代码获取

**在学习前，一定要获取源码，因为篇幅原因，只会截取部分关键代码，请对照源码进行学习**

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-11.zip
unzip lou-springboot-11.zip
cd lou-springboot
```



## Ajax 简介

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML），它是一种用于创建快速动态网页的技术，通过浏览器与服务器进行少量数据交换，Ajax 可以使网页实现异步更新，这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新，传统的网页如果需要更新内容，必须要进行跳转并重新加载整个网页。

Ajax 技术使得网站与用户间有了更友好的交互效果，比较常见的借助 Ajax 技术实现的功能有列表上拉加载分页数据、省市区三级联动、进度条更新等等，这些都是借助 Ajax 技术在当前页面即可完成的功能，即使有数据交互也不会跳转页面，整体交互效果有了很大的提升。



## Ajax 工作流程

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm36)

Ajax 的整个工作流程如上图所示，用户在进行页面上进行操作时会执行 js 方法，js 方法中通过 Ajax 异步与后端进行数据交互。首先会创建 XMLHttpRequest 对象，XMLHttpRequest 是 AJAX 的基础，之后会根据页面内容将参数封装到请求体中或者放到请求 URL 中，然后向后端服务器发送请求，请求成功后根据后端返回的数据进行解析和部分逻辑处理，最终在不刷新页面的情况下对页面进行局部更新。



## Ajax 调用实践

接下来我们通过一个实际的案例来演示 Ajax 与后端 api 的交互。 注意：项目统一创建在 /home/project/lou-springboot 目录下。



### 前端页面编码

在 resources/static 下新建 ajax-test.html，代码如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>lou.SpringBoot | Ajax 请求测试</title>
</head>
<body class="hold-transition login-page">
<div style="width:720px;margin:7% auto">
    <div class="content">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-6">
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">接口测试1</h5>
                        </div>
                        <div class="card-body">
                            <input type="text" id="info" class="form-control" placeholder="请输入info值">
                            <h6 class="card-title">接口1返回数据如下：</h6>
                            <p class="card-text" id="test1"></p>
                            <a href="#" class="btn btn-primary" onclick="requestTest1()">发送请求1</a>
                        </div>
                    </div>
                    <br>
                    <div class="card card-primary card-outline">
                        <div class="card-header">
                            <h5 class="m-0">接口测试2</h5>
                        </div>
                        <div class="card-body">
                            <h6 class="card-title">接口2返回数据如下：</h6>
                            <p class="card-text" id="test2"></p>
                            <a href="#" class="btn btn-primary" onclick="requestTest2()">发送请求2</a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</body>
</html>
```

ajax-test 页面上分别定义了两个按钮，在点击时分别会分别触发 `onclick` 点击事件，在点击事件的实现逻辑中进行页面内容的局部更新。



### Ajax 调用逻辑

由于原生 Ajax 实现比较繁琐，实际开发中一般都会使用 jQuery 封装的 Ajax 方法，或者其他库封装的 Ajax 方法，详细可以参考 [jQuery ajax 讲解](http://www.w3school.com.cn/jquery/ajax_ajax.asp)。

在调用前首先在 html 代码中引入 jQuery 库，之后根据点击事件分别定义两个事件触发的 js 方法：`requestTest1()` 和 `requestTest2()`，在方法中分别使用 Ajax 向后端发送请求，在请求成功后将响应结果赋值到对应的 div 中，代码如下：

```html
<!-- 引入jQuery -->
<script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>

<!-- 定义两个点击事件并实现 Ajax 调用逻辑 -->
<script type="text/javascript">
    function requestTest1() {
        var info = $("#info").val();
        $.ajax({
            type: "GET",//方法类型
            dataType: "text",//预期服务器返回的数据类型
            url: "api/test1?info=" + info,//请求地址
            contentType: "application/json; charset=utf-8",
            success: function (result) {//请求成功后回调
                $("#test1").html(JSON.stringify(result));
            },
            error: function () {//请求失败后回调
                $("#test1").html("接口异常，请联系管理员！");
            }
        });
    }
    function requestTest2() {
        $.ajax({
            type: "GET",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "api/test2",
            contentType: "application/json; charset=utf-8",
            success: function (result) {
                $("#test2").html(JSON.stringify(result));
            },
            error: function () {
                $("#test2").html("接口异常，请联系管理员！");
            }
        });
    }
</script>
```

方法一中会首先获取用户在 input 框中输入的字段，之后将其拼接到请求的 URL 中，最后发送 Ajax 请求并完成回调，方法二中也是类似，用户点击发送请求的按钮后，会触发 `onclick` 点击事件并调用 `requestTest2()` 方法，在请求完成后进入 success 回调方法，并将请求结果的内容放到 div 中显示。



### 后端接口实现

在包 `com.lou.springboot.controller` 下新建 RequestTestController，代码如下：

```java
package com.lou.springboot.controller;

import com.lou.springboot.entity.User;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

@RestController
@RequestMapping("/api")
public class RequestTestController {
    @RequestMapping(value = "/test1", method = RequestMethod.GET)
    public String test1(String info) {
        if (StringUtils.isEmpty(info) || StringUtils.isEmpty(info)) {
            return "请输入info的值！";
        }
        return "你输入的内容是:" + info;
    }
    @RequestMapping(value = "/test2", method = RequestMethod.GET)
    public List<User> test2() {
        List<User> users = new ArrayList<>();
        User user1 = new User();
        user1.setId(1);
        user1.setName("十一");
        user1.setPassword("12121");
        User user2 = new User();
        user2.setId(2);
        user2.setName("十二");
        user2.setPassword("21212");
        User user3 = new User();
        user3.setId(3);
        user3.setName("十三");
        user3.setPassword("31313");
        users.add(user1);
        users.add(user2);
        users.add(user3);
        return users;
    }
}
```

控制器中分别实现两个方法并设置请求地址映射来处理前端发送的异步请求，`test1()` 方法中会根据前端传入的 info 字段值重新返回给前端一个字符串，请求方式为 GET，`test2()` 方法中会返回一个集合对象给前端，请求方式为 GET。

在前后端对接时需要确定好请求路径、请求方法、返回结果的格式等，比如后端定义的请求方法为 GET，那么在 Ajax 设置时一定要将 type 设置为 GET，不然无法正常的发起请求，`test1()` 方法和 `test2()` 方法中返回的结果格式也不同，所以在前端进行 Ajax 调用时 dataType 也需要注意，比如本案例中分别返回的是字符串类型和集合类型，那么在 Ajax 请求中需要将 dataType 分别设置为 text 和 json，不然前端在 Ajax 调用后会直接进入 error 回调中，而不是 success 成功回调中。



### 代码效果测试

编码完成后，首先需要切换到 lou-springboot 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm37)



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们可以在浏览器中输入如下地址打开接口测试页面：`https://********.simplelab.cn/ajax-test.html`，之后分别进行两个 Ajax 方法测试，结果如下所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm38)

前端通过 Ajax 技术调用后端接口并更新前端页面内容的功能测试完成。本次实验完成！



## 实验总结

本篇文章对 Ajax 技术进行了简单的介绍，并通过一个实际的案例讲解了如何在项目开发中使用该技术进行功能实现，同学们可以自行在本地或者实验楼的线上环境中进行测试。

在实际的项目开发中，虽然也会使用 Ajax 技术进行接口调用和异步刷新页面，但是会与本案例有略微的不同，因为在实际的项目开发中会对接口规范和数据格式规范有很强的要求，并不像本案例中所演示的这么简单，我们会在下一个实验中详细介绍，本案例旨在让大家认识该技术并进行简单的使用。



# 十、**项目实践之 RESTful API 设计与实现**

## 实验介绍

#### 实验内容

在实际的项目开发中，进行至接口设计阶段时，后端开发人员和前端开发人员都会参与其中，根据已制定的规范对接口进行设计和返回数据格式的约定（不同项目组规范可能不同），但是像前一个实验中的情况应该不会出现，接口的请求方式不会仅仅只有 GET 方式，返回结果的数据格式反而会比较统一，返回结果一般会进行封装。本篇文章将会对 api 设计及数据规范进行简单的介绍，之后结合实际案例对数据交互进行编码实现。

#### 实验知识点

- RESTful api 设计规范
- RESTful api 数据规范
- Ajax + RESTful api 前后端交互实践

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



## RESTful api 设计规范

目前比较流行的一套接口规范就是 RESTful api，REST（Representational State Transfer）,中文翻译叫"表述性状态转移",它首次出现在 2000 年 Roy Fielding 的博士论文中，Roy Fielding 是 HTTP 规范的主要编写者之一。他在论文中提到："我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。REST 指的是一组架构约束条件和原则。"如果一个架构符合 REST 的约束条件和原则，我们就称它为 RESTful 架构，REST 其实并没有创造新的技术、组件或服务，在我的理解中，它更应该是一种理念、一种思想，利用 Web 的现有特征和能力，更好地诠释和体现现有 web 标准中的一些准则和约束。

#### 基本原则一：URI

- 应该将 api 部署在专用域名之下。
- URL 中尽量不用大写。
- URI 中不应该出现动词，动词应该使用 HTTP 方法表示但是如果无法表示，也可使用动词，例如：search 没有对应的 HTTP 方法,可以在路径中使用 search，更加直观。
- URI 中的名词表示资源集合，使用复数形式。
- URI 可以包含 queryString，避免层级过深。

#### 基本原则二：HTTP 动词

对于资源的具体操作类型，由 HTTP 动词表示，常用的 HTTP 动词有下面五个：

- GET：从服务器取出资源（一项或多项）。
- POST：在服务器新建一个资源。
- PUT：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH：在服务器更新资源（客户端提供改变的属性）。
- DELETE：从服务器删除资源。

还有两个不常用的 HTTP 动词：

- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

例子：

```
用户管理模块：

1. [POST]   http：//lou.springboot.tech/users   // 新增
2. [GET]    http：//lou.springboot.tech/users?page=1&rows=10 // 列表查询
3. [PUT]    http：//lou.springboot.tech/users/12 // 修改
4. [DELETE] http：//lou.springboot.tech/users/12 // 删除
```

#### 基本原则三：状态码（Status Codes）

处理请求后，服务端需向客户端返回的状态码和提示信息。

常见状态码**(状态码可自行设计，只需开发者约定好规范即可)**：

- 200：SUCCESS 请求成功。
- 401：Unauthorized 无权限。
- 403：Forbidden 禁止访问。
- 410：Gone 无此资源。
- 500：INTERNAL SERVER ERROR 服务器发生错误。 ...

#### 基本原则四：错误处理

如果服务器发生错误或者资源不可达，应该向用户返回出错信息。

#### 基本原则五：服务端数据返回

后端的返回结果最好使用 JSON 格式，且格式统一。

#### 基本原则六：版本控制

- 规范的 api 应该包含版本信息，在 RESTful api 中，最简单的包含版本的方法是将版本信息放到 url 中，如：

```
[GET]    http：//lou.springboot.tech/v1/users?page=1&rows=10 
[PUT]    http：//lou.springboot.tech/v1/users/12 
```

- 另一种做法是，使用 HTTP header 中的 accept 来传递版本信息。

以下为接口安全原则的注意事项：

#### 安全原则一：Authentication 和 Permission

Authentication 指用户认证，Permission 指权限机制，这两点是使 RESTful api 强大、灵活和安全的基本保障。

常用的认证机制是 Basic Auth 和 OAuth，RESTful api 开发中，除非 api 非常简单，且没有潜在的安全性问题，否则，**认证机制是必须实现的**，并应用到 api 中去。Basic Auth 非常简单，很多框架都集成了 Basic Auth 的实现，自己写一个也能很快搞定，OAuth 目前已经成为企业级服务的标配，其相关的开源实现方案非常丰富。

#### 安全原则二：CORS

CORS 即 Cross-origin resource sharing，在 RESTful api 开发中，主要是为 js 服务的，解决调用 RESTful api 时的跨域问题。

由于固有的安全机制，js 的跨域请求时是无法被服务器成功响应的。现在前后端分离日益成为 web 开发主流方式的大趋势下，后台逐渐趋向指提供 api 服务，为各客户端提供数据及相关操作，而网站的开发全部交给前端搞定，网站和 api 服务很少部署在同一台服务器上并使用相同的端口，js 的跨域请求时普遍存在的，开发 RESTful api 时，通常都要考虑到 CORS 功能的实现，以便 js 能正常使用 api。

目前各主流 web 开发语言都有很多优秀的实现 CORS 的开源库，我们在开发 RESTful api 时，要注意 CORS 功能的实现，直接拿现有的轮子来用即可。



## Ajax + RESTful api 前后端交互实践

注意：项目统一创建在 /home/project/lou-springboot 目录下。

### 返回结果封装

在前一个实验中，我们使用了 Ajax 技术进行后端接口的调用，但是返回结果的数据格式并不统一，这在实际的项目开发工作中一般不会出现，因此我们首先将返回结果进行抽象并封装。

新建 common 包，并封装 Result 结果类，代码如下（注：代码位于 com.lou.springboot.common）：

```java
package com.lou.springboot.common;

import java.io.Serializable;

public class Result<T> implements Serializable {
    private static final long serialVersionUID = 1L;
    //业务码，比如成功、失败、权限不足等 code，可自行定义
    private int resultCode;
    //返回信息，后端在进行业务处理后返回给前端一个提示信息，可自行定义
    private String message;
    //数据结果，泛型，可以是列表、单个对象、数字、布尔值等
    private T data;

    public Result() {
    }

    public Result(int resultCode, String message) {
        this.resultCode = resultCode;
        this.message = message;
    }
    // 省略部分代码
}
```

每一次后端数据返回都会根据以上格式进行数据封装，包括业务码、返回信息、实际的数据结果，而不是像前一个实验中的不确定格式，前端接受到该结果后对数据进行解析，并通过业务码进行相应的逻辑操作，之后再将 data 中的数据获取到并进行页面渲染或者进行信息提示。

实际返回的数据格式如下：

- 列表数据

```json
{
    "resultCode": 200,
    "message": "SUCCESS",
    "data": [{
        "id": 2,
        "name": "user1",
        "password": "123456"
    }, {
        "id": 1,
        "name": "13",
        "password": "12345"
    }]
}
```

- 单条数据

```json
{
    "resultCode": 200,
    "message": "SUCCESS",
    "data": true
}
```

如上两个分别是列表数据和单条数据的返回，后端进行业务处理后将会返回给前端一串 json 格式的数据，resultCode 等于 200 表示数据请求成功，该字段也可以自行定义，比如 0、1001、500 等等，message 值为 SUCCESS，也可以自行定义返回信息，比如“获取成功”、“列表数据查询成功”等，这些都需要与前端约定好，一个码只表示一种含义，而 data 中的数据可以是一个对象数组、也可以是一个字符串、数字等类型，根据不同的业务返回不同的结果，之后的实践内容里都会以这种方式返回数据。



### 后端接口实现

我们会按照 api 规范进行接口设计和接口调用，以对 tb_user 表进行增删改查为例进行实践，在`com.lou.springboot.controller`下新建 `ApiController` 类，代码如下：

```java
package com.lou.springboot.controller;

import com.lou.springboot.common.Result;
import com.lou.springboot.common.ResultGenerator;
import com.lou.springboot.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.*;
import java.util.*;
/**
 * @author 13
 * @qq交流群 796794009
 * @email 2449207463@qq.com
 * @link http://13blog.site
 */
@Controller
@RequestMapping("/api")
public class ApiController {
    static Map<Integer, User> usersMap = Collections.synchronizedMap(new HashMap<Integer, User>());

    // 初始化 usersMap
    static {
        User user = new User();
        user.setId(2);
        user.setName("user1");
        user.setPassword("123456");
        User user2 = new User();
        user2.setId(5);
        user2.setName("13-5");
        user2.setPassword("4");
        User user3 = new User();
        user3.setId(6);
        user3.setName("12");
        user3.setPassword("123");
        usersMap.put(2, user);
        usersMap.put(5, user2);
        usersMap.put(6, user3);
    }

    // 查询一条记录
    @RequestMapping(value = "/users/{id}", method = RequestMethod.GET)
    @ResponseBody
    public Result<User> getOne(@PathVariable("id") Integer id) {
        if (id == null || id < 1) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        User user = usersMap.get(id);
        if (user == null) {
            return ResultGenerator.genFailResult("无此数据");
        }
        return ResultGenerator.genSuccessResult(user);
    }

    // 查询所有记录
    @RequestMapping(value = "/users", method = RequestMethod.GET)
    @ResponseBody
    public Result<List<User>> queryAll() {
        List<User> users = new ArrayList<User>(usersMap.values());
        return ResultGenerator.genSuccessResult(users);
    }

    // 新增一条记录
    @RequestMapping(value = "/users", method = RequestMethod.POST)
    @ResponseBody
    public Result<Boolean> insert(@RequestBody User user) {
        // 参数验证
        if (StringUtils.isEmpty(user.getId()) || StringUtils.isEmpty(user.getName()) || StringUtils.isEmpty(user.getPassword())) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        if (usersMap.containsKey(user.getId())) {
            return ResultGenerator.genFailResult("重复的id字段");
        }
        usersMap.put(user.getId(), user);
        return ResultGenerator.genSuccessResult(true);
    }

    // 修改一条记录
    @RequestMapping(value = "/users", method = RequestMethod.PUT)
    @ResponseBody
    public Result<Boolean> update(@RequestBody User tempUser) {
        //参数验证
        if (tempUser.getId() == null || tempUser.getId() < 1 || StringUtils.isEmpty(tempUser.getName()) || StringUtils.isEmpty(tempUser.getPassword())) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        //实体验证，不存在则不继续修改操作
        User user = usersMap.get(tempUser.getId());
        if (user == null) {
            return ResultGenerator.genFailResult("参数异常");
        }
        user.setName(tempUser.getName());
        user.setPassword(tempUser.getPassword());
        usersMap.put(tempUser.getId(), tempUser);
        return ResultGenerator.genSuccessResult(true);
    }

    // 删除一条记录
    @RequestMapping(value = "/users/{id}", method = RequestMethod.DELETE)
    @ResponseBody
    public Result<Boolean> delete(@PathVariable("id") Integer id) {
        if (id == null || id < 1) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        usersMap.remove(id);
        return ResultGenerator.genSuccessResult(true);
    }
}
```

根据前端不同的资源请求，我们按照前文中 HTTP 动词的要求对接口的请求类型进行设置，用户数据查询方法使用 GET 请求，用户添加方法 使用 POST 请求，对应的修改和删除操作使用 PUT 和 DELETE 请求，同时对于 api 的请求路径也按照设计规范进行设置，虽然有些映射路径相同，但是会根据请求方法进行区分。

比如：同样是 /users 路径，如果请求方法为 POST 则表示添加资源会调用 insert() 方法，而请求方法为 PUT 时则表示修改资源会调用 update() 方法，还有 /users/{id} 路径，会根据 GET 请求方式和 DELETE 请求方式进行区分表示获取单个资源和删除单个资源。

同时，每一个返回结果我们都统一使用 Result 类进行包装之后再返回给前端，并使用 `@ResponseBody` 注解将其转换为 json 格式。



### 前端页面和 js 方法实现

接口定义完成后就可以进行前端页面和接口调用逻辑的实现了，新建 api-test.html（注：代码位于`resources/static`），代码如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>lou.SpringBoot | api 请求测试</title>
</head>
<body class="hold-transition login-page">
<div style="width:720px;margin:7% auto">
    <div class="content">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-6">
                    <hr>
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">详情查询接口测试</h5>
                        </div>
                        <div class="card-body">
                            <input id="queryId" type="number" placeholder="请输入id字段">
                            <h6 class="card-title">查询接口返回数据如下：</h6>
                            <p class="card-text" id="result0"></p>
                            <a href="#" class="btn btn-primary" onclick="requestQuery()">发送详情查询请求</a>
                        </div>
                    </div>
                    <br>
                    <hr>
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">列表查询接口测试</h5>
                        </div>
                        <div class="card-body">
                            <h6 class="card-title">查询接口返回数据如下：</h6>
                            <p class="card-text" id="result1"></p>
                            <a href="#" class="btn btn-primary" onclick="requestQueryList()">发送列表查询请求</a>
                        </div>
                    </div>
                    <br>
                    <hr>
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">添加接口测试</h5>
                        </div>
                        <div class="card-body">
                            <input id="addId" type="number" placeholder="请输入id字段">
                            <input id="addName" type="text" placeholder="请输入name字段">
                            <input id="addPassword" type="text" placeholder="请输入password字段">
                            <h6 class="card-title">添加接口返回数据如下：</h6>
                            <p class="card-text" id="result2"></p>
                            <a href="#" class="btn btn-primary" onclick="requestAdd()">发送添加请求</a>
                        </div>
                    </div>
                    <br>
                    <hr>
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">修改接口测试</h5>
                        </div>
                        <div class="card-body">
                            <input id="updateId" type="number" placeholder="请输入id字段">
                            <input id="updateName" type="text" placeholder="请输入name字段">
                            <input id="updatePassword" type="text" placeholder="请输入password字段">
                            <h6 class="card-title">修改接口返回数据如下：</h6>
                            <p class="card-text" id="result3"></p>
                            <a href="#" class="btn btn-primary" onclick="requestUpdate()">发送修改请求</a>
                        </div>
                    </div>
                    <br>
                    <hr>
                    <div class="card">
                        <div class="card-header">
                            <h5 class="m-0">删除接口测试</h5>
                        </div>
                        <div class="card-body">
                            <input id="deleteId" type="number" placeholder="请输入id字段">
                            <h6 class="card-title">删除接口返回数据如下：</h6>
                            <p class="card-text" id="result4"></p>
                            <a href="#" class="btn btn-primary" onclick="requestDelete()">发送删除请求</a>
                        </div>
                    </div>
                    <hr>
                </div>
            </div>
        </div>
    </div>
</div>
</body>
</html>
```

本功能主要包括对于 tb_user 表增删改查功能的调用和结果显示，每个接口测试模块包括信息录入的 input 框和发送请求按钮以及结果显示的 div，点击不同的按钮分别触发不同的 js 方法，我们计划在 js 方法中使用 Ajax 对后端发送不同的请求，实现如下：

```html
<!-- jQuery -->
<script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>
<script type="text/javascript">
    function requestQuery() {
        var id = $("#queryId").val();
        if (typeof id == "undefined" || id == null || id == "" || id < 0) {
            return false;
        }
        $.ajax({
            type: "GET",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "/api/users/" + id,
            contentType: "application/json; charset=utf-8",
            success: function (result) {
                $("#result0").html(JSON.stringify(result));
            },
            error: function () {
                $("#result0").html("接口异常，请联系管理员！");
            }
        });
    }
    function requestQueryList() {
        $.ajax({
            type: "GET",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "/api/users",
            contentType: "application/json; charset=utf-8",
            success: function (result) {
                $("#result1").html(JSON.stringify(result));
            },
            error: function () {
                $("#result1").html("接口异常，请联系管理员！");
            }
        });
    }
    function requestAdd() {
        var id = $("#addId").val();
        var name = $("#addName").val();
        var password = $("#addPassword").val();
        var data = {"id": id, "name": name, "password": password}
        $.ajax({
            type: "POST",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "/api/users",
            contentType: "application/json; charset=utf-8",
            data: JSON.stringify(data),
            success: function (result) {
                $("#result2").html(JSON.stringify(result));
            },
            error: function () {
                $("#result2").html("接口异常，请联系管理员！");
            }
        });
    }
    function requestUpdate() {
        var id = $("#updateId").val();
        var name = $("#updateName").val();
        var password = $("#updatePassword").val();
        var data = {"id": id, "name": name, "password": password}
        $.ajax({
            type: "PUT",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "/api/users",
            contentType: "application/json; charset=utf-8",
            data: JSON.stringify(data),
            success: function (result) {
                $("#result3").html(JSON.stringify(result));
            },
            error: function () {
                $("#result3").html("接口异常，请联系管理员！");
            }
        });
    }
    function requestDelete() {
        var id = $("#deleteId").val();
           if (typeof id == "undefined" || id == null || id == "" || id < 0) {
            return false;
        }
        $.ajax({
            type: "DELETE",//方法类型
            dataType: "json",//预期服务器返回的数据类型
            url: "/api/users/" + id,
            contentType: "application/json; charset=utf-8",
            success: function (result) {
                $("#result4").html(JSON.stringify(result));
            },
            error: function () {
                $("#result4").html("接口异常，请联系管理员！");
            }
        });
    }
</script>
```

每个请求按钮点击后会触发不同的 js 方法，以修改请求为例，该按钮点击后会触发 `requestUpdate()` 方法，该方法中首先会获取用户输入的数据，之后将其封装到 data 中，同时设置请求的 url 为 /api/users，由于是修改用户数据，因此将请求方法设置为 PUT 方法，之后向后端发送请求，而后端代码在前文中已经介绍，请求地址为 /api/users 且请求方法为 PUT 时会调用修改方法将用户数据进行修改。

删除请求也是如此，首先用户输入一个需要删除的 id，之后根据该 id 将其拼接到 url 中，同时设置请求的 url 为 /api/users/{id}，由于是删除用户数据，因此将请求方法设置为 DELETE 方法，之后向后端发送请求，后端在收到请求后会根据请求方法和请求地址进行映射匹配，可以通过后端代码得知，该请求最终会被控制器中的 `delete()` 方法处理，其它的调用流程与此类似，可以参考着进行理解。



### 功能测试

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 spring-boot-restful-api 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm40)

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，我们可以在浏览器中输入如下地址打开 api 接口测试页面：`https://********.simplelab.cn/api-test.html`。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\uid18510-20190603-1559530416255)

首先是根据 id 查询单个用户对象的数据，第一次输入 1 并发送请求，返回的结果为“无此对象”，之后换成 2 返回了 id 为 2 的用户信息，接着是列表查询接口，不用输入参数直接请求即可，这里可以看到数据库 tb_user 表中的所有数据，之后是添加接口的测试，输入 name 字段和 password 字段后点击发送请求按钮，如果添加成功，返回对象的 data 值为 true，如果添加失败则为 false，成功后再去执行列表查询接口可以看到在刚刚请求结果的基础上又新增了新增的一条数据。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\api-test2.gif)

接着修改接口的测试，输入 id 字段、 name 字段和 password 字段后点击发送请求按钮，如果修改成功，返回对象的 data 值为 true，如果添加失败则为 false，成功后再去执行列表查询接口可以看到数据已经修改成功，最后是删除接口的测试，如上图所示，如果输入的 id 并不存在则返回的 data 为 false 表示删除失败，删除成功后可以执行查询接口，会看到列表数据再次发生改变。

所有的接口都正常返回且格式都是按照预定结果格式返回的，希望大家按照文中的步骤自行进行测试和学习，在测试的时候也可以结合 MySQL 进行验证，检查数据是否在数据库中被正确的修改。

本次实验完成！



## 实验总结

通过这一阶段的学习，大家应该对于 Spring Boot 的基础使用以及功能整合有了一些理解，对于基本的前后端数据交互也有了基本的掌握，并且也可以根据本实验中提供的源码进行实际的操作和练习。





# 十一、**SpringBoot博客系统项目开发之分页功能实现**

## 实验介绍

#### 实验内容

在 api 实践那节实验课程中包含一个列表查询接口，但是那个接口会将所有的数据查询出来，一般这种接口会加入分页逻辑，在查询时只查询对应页码的数据，而不是将所有的数据全部都查询出来，本节实验课我们将对分页功能进行介绍及分析，之后进行功能实践，之后还会讲解使用 JqGrid 分页插件进行分页功能的效果展示。

其实分页是一个网站系统中非常重要也十分常用的功能，相信不少同学对这个功能比较熟悉，在 MVC 开发模式下我们通常是把它放入返回对象中并在页面代码中循环遍历并渲染到页面中，也有通过接口返回，并通过前端插件来实现，这两种方式我们都会介绍到，本节实践的内容将其设计为一个通用的分页接口，并将分页数据放到 Result 对象中并通过 json 格式返回。

#### 实验知识点

- 分页功能简介
- 分页功能的作用和好处
- 分页功能实践
- JqGrid 分页插件介绍
- JqGrid 分页插件整合

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+

#### 代码获取

**在学习前，一定要获取源码，因为篇幅原因，只会截取部分关键代码。请大家参照完整源码进行学习**

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/lou-springboot-15.zip
unzip lou-springboot-15.zip
cd lou-springboot
```



## 什么是分页

在各类电商网站、新闻网站、音乐网站、各类后台管理系统等等网站中都会存在，分页功能也是十分常见的功能，我们来看一下比较常见及常用的分页功能的展现形式：

#### 实验楼分页功能

在实验楼的课程搜索框中输入 "java" 查询相关信息之后跳转到搜索结果页面，页面中大致会有 15 条左右的数据列表，当前展示的是第 1 页的数据，如果想看后面的搜索内容点击页面下方的分页信息即可，比如点击第 2 页或者下一页的按钮就可以看到更多的实验楼课程信息了。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm41)

#### 博客后台分页

博客后台的文章管理页面，也并没有把所有的文章信息都展示出来，而是通过分页功能分别展示出来：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm42)

#### 百度分页

在百度首页搜索框中输入"java"查询相关信息之后跳转到搜索结果页面，页面中大致会有 10 条左右的数据列表，此时展示的是第 1 页的数据，如果想看后面的搜索内容点击页面下方的分页信息即可，比如点击第 6 页或者下一页的按钮就可以看到更多的信息了。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\baidu-page.gif)



## 分页的作用

不仅仅是常见，分页功能在一个系统中也是不可缺少的，分页功能的作用如下：

- 减少系统资源的消耗，数据查询出来后是放在内存里的，如果在数据量很大的情况下一次性将所有内容都查询出来，会占用过多的内存，通过分页可以减少这种消耗；
- 提高性能，应用与数据库间通过网络传输数据，一次传输 10 条数据结果集与一次传输 20000 条数据结果集肯定是传输 10 条消耗更少的网络资源；
- 提升访问速度，浏览器与应用间的传输也是通过网络，返回 10 条数据明显那比返回 20000 条数据速度更快，因为数据包的大小有差别；
- 符合用户习惯，比如搜索结果或者商品展示，通常用户可能只看最近前 30 条，将所有数据都查询出来比较浪费；
- 基于展现层面的考虑，由于设备屏幕的大小比较固定，一个屏幕能够展示的信息并不是特别多，如果一次展现太多的数据，不管是排版还是页面美观度都有影响，一个屏幕的范围就是那么大，展示信息条数有限。

分页功能的使用可以提升系统性能，也比较符合用户习惯，符合页面设计，这也是为什么大部分系统都会有分页功能。



## 分页设计

#### 分页参数设计

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid441493labid8432timestamp1550026524121.png/wm)

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm43)

分页信息区的设计和展示如上图所示，前端分页区比较重要的几个信息是：

- 页码展示
- 当前页码
- 每页条数

当然，有些页面也会加上首页、尾页、跳转页码等功能，这些信息都根据功能需要和页面设计去做增加和删减。

#### 后端功能设计

前端页面的工作是渲染数据和分页信息展示，而后端则需要按照前端传输过来的请求将分页所需的数据正确的查询出来并返回给前端，两端的侧重点并不相同，比如前端需要展示所有页码，而后端则只需要提供总页数即可，并不需要对这个总页码进行其他操作，比如前端需要根据用户操作记录当前页码这个参数以便对页码信息进行调整和限制，而后端则并不是这么注重当前页码，只需要接收前端传输过来的页码进行相应的判断和查询操作即可。

对于后端必不可少的是两个参数：

- 页码(需要第几页的数据)
- 每页条数(每次查询多少条数据,一般默认 10 条或者 20 条)

因为数据库查询语句如下，不同数据库可能关键字有些差别，比如 SQL Server 是通过 `top` 关键字、Oracle 通过 `rownum` 关键字，MySQL 实现分页功能基本都是使用 `limit` 关键字。

```sql
//下面是mysql的实现语句：

select * from tb_xxxx limit 10,20
```

分页功能的最终实现既是如此，通过页码和条数确定数据库需要查询的是从第几条到第几条的数据，比如查询第 1 页每页 20 条数据就是查询数据库中从 0 到 20 条数据，查询第 4 页每页 10 条数据就是查询数据库中第 30 到 40 条数据，因此对于后端来说页码和条数两个参数就显得特别重要，缺少这两个参数根本无法继续之后的查询逻辑，分页数据也就无从查起。

虽然如此，为了前端分页区展示还要将数据总量或者总页数返回给前端，数据总量是必不可少的，因为总页数可以计算出来，即数据总量除以每页条数，数据总量的获取方式：

```sql
select count(*) from tb_xxxx
```

之后将数据封装，并返回给前端即可。



## 分页功能实践



接下来我们将结合 tb_admin_user 表进行简单的查询并分页的功能，在前端请求对应的页数时返回那一页的所有数据。 注意：项目统一创建在/home/project/lou-springboot 目录下。



### 新增数据

为了演示分页功能，因此在表中增加一些数据，不然演示效果不太好，执行如下 SQL：

```sql
USE `lou_springboot`;

DROP TABLE IF EXISTS `tb_admin_user`;
CREATE TABLE `tb_admin_user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_name` varchar(50) NOT NULL,
  `password_md5` varchar(50) NOT NULL,
  `user_token` varchar(50) NOT NULL,
  `is_deleted` tinyint(4) DEFAULT '0',
  `create_time` date NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

DELETE FROM tb_admin_user;

INSERT  INTO `tb_admin_user`(`id`,`user_name`,`password_md5`,`user_token`,`is_deleted`,`create_time`) VALUES 
```



### 后端分页功能代码

- AdminUserDao.xml（注：完整代码位于`resources/mapper/AdminUserDao.xml`）

```sql
    <!-- 查询用户列表 -->
    <select id="findAdminUsers" parameterType="Map" resultMap="AdminUserResult">
        select id,user_name,create_time from tb_admin_user
        where is_deleted=0
        order by id desc
        <if test="start!=null and limit!=null">
            limit #{start},#{limit}
        </if>
    </select>

    <!-- 查询用户总数 -->
    <select id="getTotalAdminUser" parameterType="Map" resultType="int">
        select count(*) from tb_admin_user
        where is_deleted=0
    </select>
```

- 业务层代码（注：完整代码位于`com.lou.springboot.service.impl.AdminUserServiceImpl`）

```java
    public PageResult getAdminUserPage(PageUtil pageUtil) {
        //当前页的用户列表
        List<AdminUser> users = adminUserDao.findAdminUsers(pageUtil);
        //用户总数
        int total = adminUserDao.getTotalAdminUser(pageUtil);
        //分页信息封装
        PageResult pageResult = new PageResult(users, total, pageUtil.getLimit(), pageUtil.getPage());
        return pageResult;
    }
```

- 控制层代码（注：完整代码位于`com.lou.springboot.controller.AdminUserControler`）

```java
@RequestMapping(value = "/list", method = RequestMethod.GET)
    public Result list(@RequestParam Map<String, Object> params) {
        //检查参数
        if (StringUtils.isEmpty(params.get("page")) || StringUtils.isEmpty(params.get("limit"))) {
            return ResultGenerator.genErrorResult(Constants.RESULT_CODE_PARAM_ERROR, "参数异常！");
        }
        //查询列表数据
        PageUtil pageUtil = new PageUtil(params);
        return ResultGenerator.genSuccessResult(adminUserService.getAdminUserPage(pageUtil));
    }
```

通过后端代码可以看出，分页功能的交互流程是前端将所需页码和条数参数传输给后端，而后端在接受到分页请求后会对分页参数进行计算，并利用 MySQL 的 `limit` 关键字去查询对应的记录，同学们可以结合代码进行理解和学习，由于篇幅所限，这里仅贴出部分代码，详细版本可以直接下载整个工程代码。



### 分页功能测试

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn`

访问页面：`https://********.simplelab.cn/users/list?page=1&limit=10`

之后通过改变 page 参数和 limit 参数的值进行分页功能的测试，整个过程如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\search-test.gif)

首先我们以每页十条数据查询第一页的数据，之后可以看到分页数据结果返回，接着我们可以通过开发者工具看一下格式化的分页数据结果，currPage 表示当前页，数值为 1，data 中是一个数组对象，后端封装的第一页的 10 条数据都在这里。

之后我们又查询了第二页的数据，最后又设置了每页 30 条数据进行分页功能的测试，功能一切正常。

同学们可以结合该过程进行代码的学习和理解，并自行对比不同参数时返回结果的不同。



## JqGrid 分页插件介绍

JqGrid 是一个用来显示网格数据的 jQuery 插件，通过使用 jqGrid 可以轻松实现前端页面与后台数据的 Ajax 异步通信并实现分页功能，特点如下：

- 兼容目前所有流行的 web 浏览器；
- 完善强大的分页功能；
- 支持多种数据格式解析，XML、JSON、数组等形式；
- 提供丰富的选项配置及方法事件接口；
- 支持表格排序，支持拖动列、隐藏列；
- 支持滚动加载数据；
- 开源免费

[JqGrid in GitHub](https://github.com/tonytomov/jqGrid/tree/master)

[下载地址](https://github.com/tonytomov/jqGrid/releases)

本教程选择的版本为 5.3.0，将代码压缩包解压后可以看到 JqGrid 正式包的目录结构如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm44)

使用 JqGrid 时必要的文件如下：

```
## js文件
jquery.jqGrid.js
grid.locale-cn.js
jquery.jqGrid.js

## 样式文件
ui.jqgrid-bootstrap-ui.css
ui.jqgrid-bootstrap.css
ui.jqgrid.css
```

主要是 js 文件和 css 样式文件，如果想使用 JqGrid 其他特性的话对应的引入其 js 文件即可。

本课程的实战 所有模块的分页插件都是使用 JqGrid 插件实现的，它的分页功能十分强大，而且使用和学习起来都比较简单，JqGrid 还有其他优秀的特性，本系统只使用了其部分特性，感兴趣的朋友可以继续学习其相关知识。



## JqGrid 分页插件整合

注意：项目统一创建在 /home/project/lou-springboot 目录下。

### 资源导入

整合过程其实是我们把 JqGrid 代码压缩包中我们需要的样式文件、js 文件、图片等静态资源放入我们项目的静态资源目录下，比如 static 目录或者其他我们设置的静态资源目录，几个重要的文件我们都在下图中用红线进行标注了，目录如下：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm45)

之后就可以在各个需要分页的页面中引用该插件资源并实现分页功能了。



### 整合过程

1. 首先在 html 文件中引入 JqGrid 所需文件：

```html
<link href="plugins/jqgrid-5.3.0/ui.jqgrid-bootstrap4.css" rel="stylesheet"/>
<!-- JqGrid依赖jquery，因此需要先引入jquery.min.js文件 -->
<script src="plugins/jquery/jquery.min.js"></script>

<script src="plugins/jqgrid-5.3.0/grid.locale-cn.js"></script>
<script src="plugins/jqgrid-5.3.0/jquery.jqGrid.min.js"></script>
```

1. 在页面中需要展示分页数据的区域添加如下代码，用于 JqGrid 初始化：

```html
<!-- JqGrid必要DOM,用于创建表格展示列表数据 -->  
<table id="jqGrid" class="table table-bordered"></table>
<!-- JqGrid必要DOM,分页信息区域 --> 
<div id="jqGridPager"></div>  
```

1. 调用 JqGrid 分页插件的 jqGrid() 方法渲染分页展示区域，代码如下：

```js
$("#jqGrid").jqGrid({
        url: 'users/list',// 请求后台json数据的url
        datatype: "json",// 后台返回的数据格式
        colModel: [// 列表信息：表头 宽度 是否显示 渲染参数 等属性
            {label: 'id', name: 'id', index: 'id', width: 50, hidden: true, key: true},
            {label: '登录名', name: 'userName', index: 'userName', sortable: false, width: 80},
            {label: '添加时间', name: 'createTime', index: 'createTime', sortable: false, width: 80}
        ],
        height: 485,// 表格高度  可自行调节
        rowNum: 10,// 默认一页显示多少条数据 可自行调节
        rowList: [10, 30, 50],// 翻页控制条中 每页显示记录数可选集合
        styleUI: 'Bootstrap',// 主题 这里选用的是Bootstrap主题
        loadtext: '信息读取中...',// 数据加载时显示的提示信息
        rownumbers: true,// 是否显示行号，默认值是false，不显示
        rownumWidth: 35,// 行号列的宽度
        autowidth: true,// 宽度自适应
        multiselect: true,// 是否可以多选
        pager: "#jqGridPager",// 分页信息DOM
        jsonReader: {
            root: "data.list", //数据列表模型
            page: "data.currPage", //数据页码
            total: "data.totalPage", //数据总页码
            records: "data.totalCount" //数据总记录数
        },
        // 向后台请求的参数
        prmNames: {
            page: "page",
            rows: "limit",
            order: "order"
        },
        // 数据加载完成并且DOM创建完毕之后的回调函数 
        gridComplete: function () {
            //隐藏grid底部滚动条
            $("#jqGrid").closest(".ui-jqgrid-bdiv").css({"overflow-x": "hidden"});
        }
    });
```



### 分页数据格式详解

在 JqGrid 整合中有如下代码：

```json
jsonReader: {
  root: "data.list", //数据列表模型
  page: "data.currPage", //当前页码
  total: "data.totalPage", //数据总页码
  records: "data.totalCount" //数据总记录数
  }
```

这里定义的是 jsonReader 对象如何对后端返回的 json 数据进行解析，比如数据列表为何读取 "data.list"，当前页码为何读取 "data.currPage"，这些都是由后端返回的数据格式所决定的，后端响应结果的数据格式定义在`com.lou.springboot.common.Result` 类中：

```java
public class Result<T> implements Serializable {
    //响应码 200为成功
    private int resultCode;
    //响应msg
    private String message;
    //返回数据
    private T data;
}
```

即所有的数据都会被设置到 data 属性中，分页结果集的数据格式定义如下（注：完整代码位于`com.lou.springboot.util.PageResult`）：

```java
public class PageResult implements Serializable {
    //总记录数
    private int totalCount;
    //每页记录数
    private int pageSize;
    //总页数
    private int totalPage;
    //当前页数
    private int currPage;
    //列表数据
    private List<?> list;
}
```

由于 JqGrid 分页插件在实现分页功能时必须以下四个参数：当前页的所有数据列表、当前页的页码、总页码、总记录数量，因此我们封装了 PageResult 对象，并将其放入 Result 返回结果的 data 属性中，之后在 JqGrid 读取时直接读取对应的参数即可，这就是前后端进行数据交互时的格式定义，希望大家能够结合代码以及实际的分页效果进行理解和学习。



## 分页功能实践

由于分页接口在前一个实验中已经开发完成，本实验中只需要将前端页面实现即可。

### 前端页面

在 `resources/static` 下新建 user.html，代码如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>lou.springboot | 用户管理页</title>
    <link href="plugins/jqgrid-5.3.0/ui.jqgrid-bootstrap4.css" rel="stylesheet"/>
    <link rel="stylesheet" href="plugins/bootstrap/css/bootstrap.css">
    <link rel="stylesheet" href="plugins/sweetalert/sweetalert.css">
    <link href="plugins/bootstrap/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="dist/css/main.css" rel="stylesheet"/>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="plugins/font-awesome/css/font-awesome.min.css">
    <link rel="stylesheet" href="dist/css/adminlte.min.css">
    <link rel="stylesheet" href="plugins/sweetalert/sweetalert.css">
</head>
<body class="hold-transition sidebar-mini" onLoad="checkCookie();">
<div class="wrapper">
    <nav class="main-header navbar navbar-expand bg-white navbar-light border-bottom">
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" data-widget="pushmenu" href="#"><i class="fa fa-bars"></i></a>
            </li>
            <li class="nav-item d-none d-sm-inline-block">
                <a href="index.html" class="nav-link">Home</a>
            </li>
        </ul>
        <ul class="navbar-nav ml-auto">
            <li class="nav-item dropdown">
                <a class="nav-link" data-toggle="dropdown" href="https://github.com/ZHENFENG13">
                    <i class="fa fa-home">&nbsp;&nbsp;文档</i>
                </a>
                <div class="dropdown-menu dropdown-donate-lg dropdown-menu-right">
                    <a href="##" class="dropdown-item">实验楼训练营</a>
                </div>
            </li>
            <li class="nav-item dropdown">
                <a class="nav-link" data-toggle="dropdown" href="#">
                    <i class="fa fa-user">&nbsp;&nbsp;作者</i>
                </a>
                <div class="dropdown-menu dropdown-menu-lg dropdown-menu-right">
                    <div class="dropdown-divider"></div>
                    <a href="#" class="dropdown-item">
                        <i class="fa fa-user-o mr-2"></i> 姓名
                        <span class="float-right text-muted text-sm">十三 / 13</span>
                    </a>
                    <div class="dropdown-divider"></div>
                    <a href="#" class="dropdown-item">
                        <i class="fa fa-user-secret mr-2"></i> 身份
                        <span class="float-right text-muted text-sm">Java开发工程师</span>
                    </a>
                    <div class="dropdown-divider"></div>
                    <a href="#" class="dropdown-item">
                        <i class="fa fa-address-card mr-2"></i> 邮箱
                        <span class="float-right text-muted text-sm">2449207463@qq.com</span>
                    </a>
                </div>
            </li>
        </ul>
    </nav>
    <aside class="main-sidebar sidebar-dark-primary elevation-4">
        <a href="index.html" class="brand-link">
            <img src="dist/img/logo.jpg" alt="ssm-cluster Logo" class="brand-image img-circle elevation-3"
                 style="opacity: .8">
            <span class="brand-text font-weight-light">lou.springboot</span>
        </a>
        <div class="sidebar">
            <!-- Sidebar user panel (optional) -->
            <div class="user-panel mt-3 pb-3 mb-3 d-flex">
                <div class="image">
                    <img src="dist/img/logo3.jpg" class="img-circle elevation-2" alt="User Image">
                </div>
                <div class="info">
                    <a href="#" class="d-block">十三</a>
                </div>
            </div>
            <nav class="mt-2">
                <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu"
                    data-accordion="false">
                    <!-- Add icons to the links using the .nav-icon class
                         with font-awesome or any other icon font library -->
                    <li class="nav-header">Dashboard</li>
                    <li class="nav-item has-treeview">
                        <a href="#" class="nav-link">
                            <i class="nav-icon fa fa-dashboard"></i>
                            <p>
                                Dashboard
                                <i class="right fa fa-angle-left"></i>
                            </p>
                        </a>
                        <ul class="nav nav-treeview">
                            <li class="nav-item">
                                <a href="./index.html" class="nav-link active">
                                    <i class="fa fa-circle-o nav-icon"></i>
                                    <p>lou.springboot主页</p>
                                </a>
                            </li>
                            <li class="nav-item">
                                <a href="./index2.html" class="nav-link">
                                    <i class="fa fa-circle-o nav-icon"></i>
                                    <p>adminLTE v3</p>
                                </a>
                            </li>
                        </ul>
                    </li>
                    <li class="nav-header">管理模块</li>
                    <li class="nav-item">
                        <a href="user.html" class="nav-link active">
                            <i class="fa fa-user-circle nav-icon"></i>
                            <p>用户管理</p>
                        </a>
                    </li>
                </ul>
            </nav>
        </div>
    </aside>
    <div class="content-wrapper">
        <!-- Content Header (Page header) -->
        <div class="content-header">
            <div class="container-fluid">
                <div class="row mb-2">
                    <div class="col-sm-6">
                        <h1 class="m-0 text-dark">用户管理页</h1>
                    </div><!-- /.col -->
                    <div class="col-sm-6">
                        <ol class="breadcrumb float-sm-right">
                            <li class="breadcrumb-item"><a href="index.html">主页</a></li>
                            <li class="breadcrumb-item active">用户管理页</li>
                        </ol>
                    </div><!-- /.col -->
                </div><!-- /.row -->
            </div><!-- /.container-fluid -->
        </div>
        <div class="content">
            <div class="row">
                <div class="col-12">
                    <div class="card">
                        <div class="card-body">
                                <!-- 分页展示区，需要增加 jqgrid 相关 DOM -->
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!-- /.content-wrapper -->
    <footer class="main-footer">
        <strong>Copyright &copy; 2019 <a href="##">13blog.site</a>.</strong>
        All rights reserved.
        <div class="float-right d-none d-sm-inline-block">
            <b>Version</b> 2.0
        </div>
    </footer>
    <aside class="control-sidebar control-sidebar-dark">
    </aside>
</div>
<script src="plugins/jquery/jquery.min.js"></script>
<!-- jQuery UI 1.11.4 -->
<script src="plugins/jQueryUI/jquery-ui.min.js"></script>
<!-- sweet alert -->
<script src="plugins/sweetalert/sweetalert.min.js"></script>
<!-- Bootstrap 4 -->
<script src="plugins/bootstrap/js/bootstrap.bundle.min.js"></script>
<script src="plugins/jqgrid-5.3.0/grid.locale-cn.js"></script>
<script src="plugins/jqgrid-5.3.0/jquery.jqGrid.min.js"></script>
<script src="dist/js/public.js"></script>
<script src="dist/js/user.js"></script>
<script src="dist/js/adminlte.js"></script>
</body>
</html>
```

我们使用了 AdminLTE3 的样式和页面布局，之后在页面中引入 JqGrid 的相关静态资源文件，最后在页面中展示分页数据的区域增加如下代码：

```html
<!-- 数据展示列表，id 为 jqGrid -->
<table id="jqGrid" class="table table-bordered">
</table>
<!-- 分页按钮展示区 -->
<div id="jqGridPager"></div>
```



### JqGrid 初始化

在`resources/static/dist/js`下新建 user.js，代码如下：

```js
$(function () {
    $("#jqGrid").jqGrid({
        url: 'users/list',
        datatype: "json",
        colModel: [
            {label: 'id', name: 'id', index: 'id', width: 50, hidden: true, key: true},
            {label: '登录名', name: 'userName', index: 'userName', sortable: false, width: 80},
            {label: '添加时间', name: 'createTime', index: 'createTime', sortable: false, width: 80}
        ],
        height: 485,
        rowNum: 10,
        rowList: [10, 30, 50],
        styleUI: 'Bootstrap',
        loadtext: '信息读取中...',
        rownumbers: true,
        rownumWidth: 35,
        autowidth: true,
        multiselect: true,
        pager: "#jqGridPager",
        jsonReader: {
            root: "data.list",
            page: "data.currPage",
            total: "data.totalPage",
            records: "data.totalCount"
        },
        prmNames: {
            page: "page",
            rows: "limit",
            order: "order"
        },
        gridComplete: function () {
            //隐藏grid底部滚动条
            $("#jqGrid").closest(".ui-jqgrid-bdiv").css({"overflow-x": "hidden"});
        }
    });
});
```

该代码的含义为：在页面加载时，调用 JqGrid 的初始化方法，将页面中 id 为 jqGrid 的 DOM 渲染为分页表格，并向后端发送请求，之后按照后端返回的 json 数据填充表格以及表格下方的分页按钮，第一页、下一页、最后一页等等逻辑都有 JqGrid 内部实现了，我们只需要将它初始化时所需要的几个数据设置好即可，因此我们需要将返回格式设置为 PageResult 类所封装的数据类型。

由于 user.html 文件中引入了 user.js 文件，所以在页面加载完成后会进行数据列表的渲染及分页插件的渲染，用户可以直接使用翻页功能了，本来这些功能需要我们自行实现，但是使用 JqGrid 后这些都不需要我们再去做逻辑实现，只需要调用其分页方法并将所需的参数设置好即可，十分方便。



### 分页功能测试

启动项目成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn`

访问页面：`https://********.simplelab.cn/user.html`，可以看到分页数据已经正常显示。

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm46)

页面加载后，分页区域也被渲染出了数据，点击下方的翻页按钮，一切功能都正常，同学们可以自行测试。



### 分页功能分析

之后我们对分页功能的实现进行简单的分析，过程如下图所示：

![此处输入图片的描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\user-page-network.gif)

打开浏览器的开发者控制台，并进入 Network 面板，具体来看一下在翻页时执行了哪些请求。

通过该过程我们发现，在每次点击分页按钮时都会向后端的 users/list 请求，参数有：`_search`、 `nd`、 `limit`、`page`、`sidx`，这些参数都是 JqGrid 内部封装的，我们后端在接受请求时只处理了 `limit` 和 `page` 参数，其他参数你也可以自行增加处理逻辑，这里不再继续讲解。

而且翻页逻辑以及参数的封装 JqGrid 内部都已经完善了，比如第一次我们是以每页 10 条数据向后端进行请求，此时共有 7 页，当我们改为每页 50 条数据时，分页插件中只有两页了，这个逻辑也由 JqGrid 插件实现了，因此我们在整合该插件后，只需要处理后端逻辑即可，前端相关的分页逻辑都由它来完成了，大大减少了我们的工作量。

本次实验完成！



## 实验总结

实践代码已经上传，大家按照文中的步骤和我提供的源码进行练习和理解，如果有任何问题希望可以及时反馈，祝大家实验顺利！



# 十二、**SpringBoot博客系统项目开发之验证码功能**

## 实验介绍

#### 实验内容

我们将在这一节学习在网站中使用验证码的相关知识，以及如何使用 Spring Boot 生成验证码，并进行后续的验证操作，生成验证码的方式以及案例有很多，本教程中所选择的方案是 Google 的 kaptcha 框架。

#### 实验知识点

- 认识验证码
- Spring Boot 整合 kaptcha 步骤
- 生成验证码操作
- 对验证码进行提交验证

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



## 验证码

#### 验证码介绍

谈起验证码这个话题，相信大家都应该很熟悉，在日常上网的时候经常会看到验证码的逻辑设计，比如登陆账号、论坛发帖、购买商品时网站都会要求用户在实际操作之前去输入验证码，验证码的生成规则或者展现形式也各不相同，但是这个设计确实已经存在于各大网站中，以下是百度百科中对它的定义：

> 验证码（CAPTCHA）是“Completely Automated Public Turing test to tell Computers and Humans Apart”（全自动区分计算机和人类的图灵测试）的缩写，是一种区分用户是计算机还是人的公共全自动程序。可以防止：恶意破解密码、刷票、论坛灌水，有效防止某个黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登陆尝试，实际上用验证码是现在很多网站通行的方式，我们利用比较简易的方式实现了这个功能。这个问题可以由计算机生成并评判，但是必须只有人类才能解答。由于计算机无法解答 CAPTCHA 的问题，所以回答出问题的用户就可以被认为是人类。



### 验证码的作用

由于在后面的实战项目开发中会用到验证码这个功能，因此选择在这篇教程中进行介绍和功能演示。其实验证码设计的主要目的以及它最大的作用也就是**防止不法分子在短时间内用机器批量的重复操作**。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm47)

我们接下来开发的博客网站会在后台登陆以及用户评论功能模块中使用验证码，这样可以有效防止恶意用户对网站进行破坏，实际上是用验证码是现在很多网站通行的方式，虽然会使得某些操作变得麻烦一点，但是对大部分的功能场景来说这个功能还是很有必要，也很重要，所以不要觉得它麻烦，如果增加这样一个功能可以使得网站更加安全，那就是十分值得的。



## Spring Boot 整合 kaptcha

简单的介绍完验证码之后，接下来，我们还是通过一个案例来讲解如何使用 Spring Boot 来生成验证码，之后我们再对验证码的显示以及后端验证进行案例讲解。

以下为操作步骤：

### 添加依赖

Spring Boot 整合 kaptcha 的第一步就是增加依赖，首先我们需要将其依赖配置增加到 pom.xml 文件中，此时的 pom.xml 文件内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/>
    </parent>
    <groupId>com.lou.springboot</groupId>
    <artifactId>spring-boot-captcha</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-boot-captcha</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 验证码 -->
        <dependency>
            <groupId>com.github.penggle</groupId>
            <artifactId>kaptcha</artifactId>
            <version>2.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

如果是在本地开发的话，只需要等待 jar 包及相关依赖下载完成即可。



### 配置

注册 DefaultKaptcha 到 IOC 容器中，新建 config 包，之后新建 KaptchaConfig 类，内容如下：

```java
package com.lou.springboot.config;

import com.google.code.kaptcha.impl.DefaultKaptcha;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;
import com.google.code.kaptcha.util.Config;
import java.util.Properties;

@Component
public class KaptchaConfig {
    @Bean
    public DefaultKaptcha getDefaultKaptcha(){
        com.google.code.kaptcha.impl.DefaultKaptcha defaultKaptcha = new com.google.code.kaptcha.impl.DefaultKaptcha();
        Properties properties = new Properties();
        // 图片边框
        properties.put("kaptcha.border", "no");
        // 字体颜色
        properties.put("kaptcha.textproducer.font.color", "black");
        // 图片宽
        properties.put("kaptcha.image.width", "160");
        // 图片高
        properties.put("kaptcha.image.height", "40");
        // 字体大小
        properties.put("kaptcha.textproducer.font.size", "30");
        // 验证码长度
        properties.put("kaptcha.textproducer.char.space", "5");
        // 字体
        properties.setProperty("kaptcha.textproducer.font.names", "宋体,楷体,微软雅黑");
        Config config = new Config(properties);
        defaultKaptcha.setConfig(config);
        return defaultKaptcha;
    }
}
```

这里就是对生成的图片验证码的规则配置，如颜色、宽高、长度、字体等等，可以根据需求自行修改这些规则，之后就可以生成自己想要的验证码了。



### 验证码的生成与显示

#### 后端处理

在 controller 包中新建 KaptchaController，之后注入刚刚配置好的 DefaultKaptcha 类，然后就可以新建一个方法，在方法里可以生成验证码对象，并以图片流的方式写到前端以供显示，代码如下：

```java
package com.lou.springboot.controller;
import com.google.code.kaptcha.impl.DefaultKaptcha;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.imageio.ImageIO;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.awt.image.BufferedImage;
import java.io.ByteArrayOutputStream;

/**
 * @author 13
 * @qq交流群 796794009
 * @email 2449207463@qq.com
 * @link http://13blog.site
 */
@Controller
public class KaptchaController {

    @Autowired
    private DefaultKaptcha captchaProducer;

    @GetMapping("/kaptcha")
    public void defaultKaptcha(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        byte[] captchaOutputStream;
        ByteArrayOutputStream imgOutputStream = new ByteArrayOutputStream();
        try {
            //生产验证码字符串并保存到session中
            String verifyCode = captchaProducer.createText();
            httpServletRequest.getSession().setAttribute("verifyCode", verifyCode);
            BufferedImage challenge = captchaProducer.createImage(verifyCode);
            ImageIO.write(challenge, "jpg", imgOutputStream);
        } catch (IllegalArgumentException e) {
            httpServletResponse.sendError(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        captchaOutputStream = imgOutputStream.toByteArray();
        httpServletResponse.setHeader("Cache-Control", "no-store");
        httpServletResponse.setHeader("Pragma", "no-cache");
        httpServletResponse.setDateHeader("Expires", 0);
        httpServletResponse.setContentType("image/jpeg");
        ServletOutputStream responseOutputStream = httpServletResponse.getOutputStream();
        responseOutputStream.write(captchaOutputStream);
        responseOutputStream.flush();
        responseOutputStream.close();
    }
}
```

我们在控制器中新增了 defaultKaptcha 方法，该方法所拦截处理的路径为 /kaptcha，在前端访问该路径后就可以接收到一个图片流并显示在浏览器页面上。

#### 前端处理

新建 kaptcha.html，在该页面中显示验证码，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>验证码显示</title>
</head>
<body>
<img src="/kaptcha"
     onclick="this.src='/kaptcha?d='+new Date()*1">
</body>
</html>
```

访问后端验证码路径 /kaptcha，并将其返回显示在 img 标签中，之后定义了 `onclick` 方法，在点击该 img 标签时可以动态的切换显示一个新的验证码，点击时访问的路径为 `/kaptcha?d=1565950414611`，即原来的验证码路径后面带上一个时间戳参数，时间戳是会变化的，所以每次点击都会是一个与之前不同的请求，如果不这样处理的话，由于浏览器的机制可能并不会重新发送请求。



### 功能验证

#### 下载并解压代码

本节课程的代码已经上传，直接下载至实验环境中即可，下载之后执行如下命令进行解压：

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/spring-boot-captcha.zip
unzip spring-boot-captcha.zip
```

接下来就能够看到本次代码的代码结构及相关文件。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm48)



### 线上环境中启动 Spring Boot 项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到 spring-boot-captcha 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。



## 验证码的输入验证

验证码的显示完成后，我们接下来要做的就是对用户输入的验证码进行比对和验证，因为一般的做法就是后端生成后会对当前生成的验证码进行保存（可能是 session 中、或者缓存中、或者数据库中），之后显示到前端页面，用户在看到验证码之后在页面对应的输入框中填写验证码，之后才向后端发送请求，而后端再接到请求后会对用户输入的验证码进行验证，如果不对的话则不会进行后续操作，接下来我们来简单的实现一下这个流程。

#### 后端处理

在 KaptchaController 类中新增 `verify` 方法，代码如下：

```java
    @GetMapping("/verify")
    @ResponseBody
    public String verify(@RequestParam("code") String code, HttpSession session) {
        if (StringUtils.isEmpty(code)) {
            return "验证码不能为空";
        }
        String kaptchaCode = session.getAttribute("verifyCode") + "";
        if (StringUtils.isEmpty(kaptchaCode) || !code.equals(kaptchaCode)) {
            return "验证码错误";
        }
        return "验证成功";
    }
```

该方法所拦截处理的路径为 /kaptcha，请求参数为 code，即用户输入的验证码，在进行基本的非空验证后，与之前保存在 session 中的 verifyCode 值进行比较，不同则返回验证码错误，相同则返回验证成功。

#### 前端处理

新建 verify.html，该页面中显示验证码，同时有可以供用户输入验证码的输入框以及提交按钮，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>验证码测试</title>
</head>
<body>
<img src="/kaptcha"
     onclick="this.src='/kaptcha?d='+new Date()*1">
<input type="text" maxlength="5" id="code" placeholder="请输入验证码">
<button id="verify">验证</button>
</body>
<script src="jquery.js"></script>
<script type="text/javascript">
    $(function () {
        $("#verify").click(function () {
            var code = $("#code").val();
            $.ajax({
                type: 'GET',//方法类型
                url: '/verify?code=' + code,
                success: function (result) {
                    alert(result);
                },
                error: function () {
                    alert("请求失败");
                }
            });
        })
    });
</script>
</html>
```

用户在输入框中输入验证码后可以点击“验证”按钮，点击事件触发后执行 js 方法，该方法会获取到用户输入的验证码的值，并将其作为请求参数，之后进行 Ajax 请求，请求后会在弹框中显示后端返回的处理结果。



### 启动 Spring Boot 项目

编码完成后切换到 spring-boot-captcha 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run`，等待项目启动。



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm49)

之后会在浏览器中弹出 `https://********.simplelab.cn` 页面，输入地址 `https://********.simplelab.cn/verify.html` 进行验证，结果如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm50)

如上图所示，我们可以在输入框中输入验证码并进行相应的验证，验证结果与预期一致，本次实验完成。



## 总结

网站中验证码的功能逻辑也基本如此，主要包括：

- 验证码的生成
- 验证码的显示
- 验证码的比对

本实验中已经将所有功能一一的实现，后续将会结合实际的项目开发对该功能逻辑进行完善，希望大家可以根据文中的步骤以及提供的源码进行实验，有任何问题可以直接与我交流。



# 十三、**SpringBoot博客系统项目开发之登录模块实现**

## 实验介绍

#### 实验内容

**从本篇开始将不再进行基础知识的讲解，而是真实的开始去开发一个实践项目了，实践课程的第一篇我们选择讲解两个知识点，一是 AdminLTE3 web app 模板整合，二是登录功能的实现，包括页面和后台 api 的开发。**

在课程介绍时我给出了几张最终项目的预览图，网站的风格比较美观，因为我们最终的实践项目的前端页面就是基于 AdminLTE3 模板进行开发的，本文会简单的介绍这个网页模板并实际的整合进我们的项目中进行登录页面的开发。之后是登录功能的实现，这里说的是互联网范畴的登录，通常供多人使用的网站或程序应用系统为每位用户配置了一套独特的用户名和密码，用户可以使用各自的用户名和密码使用系统，以便系统能识别该用户的身份，从而保持该用户的使用习惯或使用数据，这个功能相信大家都不陌生，我们就把它作为实践课程的第一步吧。

#### 实验知识点

- 登录功能简介
- AdminLTE3 模板整合
- 登录功能实现

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



## 登录功能

#### 用户登录状态

以我们将要开发的后台管理系统来说，这个管理系统是拥有多个页面的，在页面跳转过程中和通过接口进行数据交互时我们需要知道用户的状态，尤其是用户登录的状态，以便我们知道这是否是一个正常的用户，这个用户是否处于合法的登录状态，这样才能在页面跳转和接口请求时知道是否可以让当前用户来操作一些功能或是获取一些数据。

因此需要在每个页面对用户的身份进行验证和确认，但现实情况是，不可能让用户在每个页面上都输入用户名和密码，这是一个多么反人类的设计啊，应该不会有用户想要去使用这种系统，所以在设计时，要求用户进行一次登录操作即可。为了实现这一功能就需要一些辅助技术，用得最多的技术就是浏览器的 Cookie，而在 Java Web 开发中，用的比较多的是 Session，将用户登录的信息存放其中，这样就可以通过读取 Cookie 或者 Session 中的数据获得用户的登录信息，从而达到记录状态，验证用户这一目的。

#### 登录流程设计

通过前文的叙述也可以得出登录的本质，即身份验证和登录状态的保持，在实际编码中是如何实现的呢？

首先，在数据库中查询这条用户记录，伪代码如下：

```sql
select * from xxx_user where account_number = 'xxxx';
```

如果不存在这条记录则表示身份验证失败，登录流程终止；如果存在这条记录，则表示身份验证成功，接下来则需要进行登录状态的存储和验证了，存储伪代码如下：

```java
//通过 Cookie 存储
Cookie cookie = new Cookie("userName",xxxxx);

//通过 Session 存储
session.setAttribute("userName",xxxxx);
```

验证逻辑的伪代码如下：

```java
//通过 Cookie 获取需要验证的数据并进行比对校验
Cookie cookies[] = request.getCookies();
if (cookies != null){
    for (int i = 0; i < cookies.length; i++)
           {
               Cookie cookie = cookies[i];
               if (name.equals(cookie.getName()))
               {
                    return cookie;
               }
           }
}

//通过session获取需要验证的数据并进行比对校验
session.getAttribute("userName");
```

本次实践项目的登录状态我们是通过 session 来保存的，用户登录成功后我们将用户信息放到 session 对象中，之后再实现一个拦截器，在访问项目时判断 session 中是否有用户信息，有则放行请求，没有就跳转到登录页面。



## AdminLTE3 模板整合

整合过程其实是我们把 AdminLTE3 代码压缩包中我们需要的样式文件、js 文件、图片等静态资源放入我们 Spring Boot 项目的静态资源目录下，比如 static 目录或者其他我们设置的静态资源目录，几个重要的文件我们都在下图中用红线进行标注了，目录如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm51)

有些人可能对“整合”不是很理解，甚至以为是一个很复杂的过程，这里我解释一下，我们开发的是一个 web 项目，项目中包括前端工程和后端工程，后端工程我们是比较熟悉的，而前端工程就包括页面文件、样式文件、js 文件等等，由于后端的小伙伴可能不是特别熟悉前端开发，因此我们就选择了 AdminLTE 网站模板这样一个半成品网站来进行改造和开发，大部分页面和样式都已经由模板作者开发好了，我们只需要针对性的修改一些页面供项目使用即可，这个过程中肯定就少不了要把它的样式文件、js 文件等放到我们项目的静态资源目录中，因此，这里所说的“整合”仅仅是把一些必要的文件复制到我们的项目目录中，希望大家不要把这个过程想复杂了。



## 登录功能实践

#### 登录页面实现

由于选用了 AdminLTE3 作为模板，就直接改造其登录页面即可，在 templates/admin 目录中新建 login.html 模板页面，模板引擎我们选择的是 Thymeleaf，代码如下：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>personal blog | Log in</title>
    <!-- Tell the browser to be responsive to screen width -->
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="shortcut icon" th:href="@{/admin/dist/img/favicon.png}"/>
    <!-- Font Awesome -->
    <link rel="stylesheet" th:href="@{/admin/dist/css/font-awesome.min.css}">
    <!-- Ionicons -->
    <link rel="stylesheet" th:href="@{/admin/dist/css/ionicons.min.css}">
    <!-- Theme style -->
    <link rel="stylesheet" th:href="@{/admin/dist/css/adminlte.min.css}">
    <style>
        canvas {
            display: block;
            vertical-align: bottom;
        }
        #particles {
            background-color: #F7FAFC;
            position: absolute;
            top: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
        }
    </style>
</head>
<body class="hold-transition login-page">
<div id="particles">
</div>
<div class="login-box">
    <div class="login-logo" style="color: #007bff;">
        <h1>personal blog</h1>
    </div>
    <!-- /.login-logo -->
    <div class="card">
        <div class="card-body login-card-body">
            <p class="login-box-msg">your personal blog , enjoy it</p>
            <form th:action="@{/admin/login}" method="post">
                <div th:if="${not #strings.isEmpty(session.errorMsg)}" class="form-group">
                    <div class="alert alert-danger" th:text="${session.errorMsg}"></div>
                </div>
                <div class="form-group has-feedback">
                    <span class="fa fa-user form-control-feedback"></span>
                    <input type="text" id="userName" name="userName" class="form-control" placeholder="请输入账号"
                           required="true">
                </div>
                <div class="form-group has-feedback">
                    <span class="fa fa-lock form-control-feedback"></span>
                    <input type="password" id="password" name="password" class="form-control" placeholder="请输入密码"
                           required="true">
                </div>
                <div class="row">
                    <div class="col-6">
                        <input type="text" class="form-control" name="verifyCode" placeholder="请输入验证码" required="true">
                    </div>
                    <div class="col-6">
                        <img alt="单击图片刷新！" class="pointer" th:src="@{/common/kaptcha}"
                             onclick="this.src='/common/kaptcha?d='+new Date()*1">
                    </div>
                </div>
                <div class="form-group has-feedback"></div>
                <div class="row">
                    <div class="col-8">
                    </div>
                    <div class="col-4">
                        <button type="submit" class="btn btn-primary btn-block btn-flat">登录
                        </button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</div>
<script th:src="@{/admin/plugins/jquery/jquery.min.js}"></script>
<!-- Bootstrap 4 -->
<script th:src="@{/admin/plugins/bootstrap/js/bootstrap.bundle.min.js}"></script>
<script th:src="@{/admin/dist/js/plugins/particles.js}"></script>
<script th:src="@{/admin/dist/js/plugins/login-bg-particles.js}"></script>
</body>
</html>
```

该页面时直接修改的 AdminLTE3 模板的登录页，将文案修改为中文，并微调了一下页面布局，同时增加了验证码的设计，最终的页面效果如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm52)

用户在输入账号、密码和验证码后，点击登录按钮后将会向后端发送登录请求，请求地址为 admin/login，请求类型为 post，在 form 表单中已经定义了登陆的请求路径：

```html
<form th:action="@{/admin/login}" method="post">
```



### 表结构设计

**在前面的课程中我们也一直在使用一张 user 表来进行功能演示，不过那只是一张测试表用来讲解功能的，**博客系统正式的用户模块表结构设计如下，包括数据库名称也做了更改，之后的项目开发中我们会一直使用 my_blog_db 数据库：

```sql
CREATE DATABASE /*!32312 IF NOT EXISTS*/`my_blog_db` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `my_blog_db`;
/*Table structure for table `tb_admin_user` */
DROP TABLE IF EXISTS `tb_admin_user`;
CREATE TABLE `tb_admin_user` (
  `admin_user_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '管理员id',
  `login_user_name` varchar(50) NOT NULL COMMENT '管理员登陆名称',
  `login_password` varchar(50) NOT NULL COMMENT '管理员登陆密码',
  `nick_name` varchar(50) NOT NULL COMMENT '管理员显示昵称',
  `locked` tinyint(4) DEFAULT '0' COMMENT '是否锁定 0未锁定 1已锁定无法登陆',
  PRIMARY KEY (`admin_user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
/*Data for the table `tb_admin_user` */
insert  into `tb_admin_user`(`admin_user_id`,`login_user_name`,`login_password`,`nick_name`,`locked`) values (1,'admin','e10adc3949ba59abbe56e057f20f883e','十三',0);
```

新增了一张表，并在表中新增了一条用户数据，之后我们在演示登陆功能时会用到。



### 后端功能实现

#### AdminMapper.xml(**注：完整代码位于 src/main/resources/mapper/AdminMapper.xml**)

通过用户名和密码查询用户记录：

```xml
  <select id="login" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from tb_admin_user
    where login_name = #{userName,jdbcType=VARCHAR} AND login_password=#{password,jdbcType=VARCHAR} AND locked = 0
  </select>
```

#### 业务层代码

代码如下：(**注：完整代码位于 com.site.blog.my.core.service.impl 包下的 AdminUserServiceImpl.java**)

```java
    public Admin login(String userName, String password) {
        String passwordMd5 = MD5Util.MD5Encode(password, "UTF-8");
        return adminMapper.login(userName, passwordMd5);
    }
```

#### 控制层代码

首先对参数进行校验，参数中包括登陆信息和验证码，验证码的比对大家应该都了解，拿参数与存储在 session 中的验证码值进行比较，之后调用 adminUserService 业务层代码查询用户对象，之后根据验证结果来跳转页面，如果登陆成功则跳转到管理系统的首页，失败的话则带上错误信息返回到登录页，登录页中会显示出登陆的错误信息。(**注：完整代码位于 com.site.blog.my.core.controller.admin 包下的 AdminController.java**)

```java
    @PostMapping(value = "/login")
    public String login(@RequestParam("userName") String userName,
                        @RequestParam("password") String password,
                        @RequestParam("verifyCode") String verifyCode,
                        HttpSession session) {
        if (StringUtils.isEmpty(verifyCode)) {
            session.setAttribute("errorMsg", "验证码不能为空");
            return "admin/login";
        }
        if (StringUtils.isEmpty(userName) || StringUtils.isEmpty(password)) {
            session.setAttribute("errorMsg", "用户名或密码不能为空");
            return "admin/login";
        }
        String kaptchaCode = session.getAttribute("verifyCode") + "";
        if (StringUtils.isEmpty(kaptchaCode) || !verifyCode.equals(kaptchaCode)) {
            session.setAttribute("errorMsg", "验证码错误");
            return "admin/login";
        }
        Admin adminUser = adminService.login(userName, password);
        if (adminUser != null) {
            session.setAttribute("loginUser", adminUser.getAdminNickName());
            session.setAttribute("loginUserId", adminUser.getAdminId());
            //session过期时间设置为7200秒 即两小时
            session.setMaxInactiveInterval(60 * 60 * 2);
            return "redirect:/admin/index";
        } else {
            session.setAttribute("errorMsg", "登陆失败");
            return "admin/login";
        }
    }
```



## 登陆功能演示

#### 启动 MySQL 并新增 tb_admin_user 表

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。

之后执行如下命令登陆 MySQL 数据库：

```shell
sudo mysql -u root 
```

因为实验楼线上实验环境中 MySQL 数据库默认并没有设置密码，因此以上命令即可完成登陆，最后在命令行中执行前文给出的建表语句 SQL 即可。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm53)



### 线上环境中启动 Spring Boot 项目

我们提供的整个项目的完整代码，请大家参照源码对比学习了解各个功能模块的代码内容。

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/My-Blog.zip
unzip My-Blog.zip
```

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到已解压的 My-Blog 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 mvn spring-boot:run ，之后就可以等待项目启动。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm54)





### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 访问页面：`https://********.simplelab.com/admin/login` 即可进入到登录页面，之后就可以对相关功能进行测试了，演示过程如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm55)

输入账号密码以及正确的验证码，之后点击登陆按钮就可以完成登陆流程，本次实验完成！



## 实验总结

除了按照以上步骤进行实验外，有几点也需要格外注意：

1. 下载代码并解压，检查代码是否正确
2. 启动 MySQL 数据库并检查是否包含 tb_admin_user 表
3. 端口号和数据库连接信息是否正确

希望大家在本地和实验楼线上环境都能够顺利进行测试和实践，后续试验中我们会对整个登陆流程和用户身份验证来完善。





# 十四、**SpringBoot博客系统项目开发之登陆拦截器**

## 实验介绍

#### 实验内容

前文简单的实现了登陆功能，本文将会对功能进行完善，对后台资源请求进行身份认证，即请求拦截验证，同时完成管理员信息修改、密码修改、登陆退出功能。

#### 实验知识点

- Spring Boot 中使用拦截器
- 登录功能完善
- 修改密码、退出功能

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+



## 登陆拦截器

在前一个实验中我们实现了管理员的登陆功能，该功能已经完成，但是身份认证的整个流程并没有完善，该流程中应该包括登陆功能、身份认证、访问拦截、退出功能，我们仅仅完成了第一步，因此本实验将会对该流程进行完善，将接下来的功能点完成。

举个简单点的例子，我们登陆的目的是为了能够访问后台页面（比如前一个实验中的首页，路径为 /admin/index），但是在运行前一个实验的代码时，即使我们不进行登陆操作也可以访问该页面，同学们可以自行实验，也就是说我们并没有在用户访问时进行身份认证和访问拦截，所以我们首先把访问拦截的功能给完善了，大家也都知道，实现这个功能我们通常的做法就是使用拦截器，那么接下来我们就来实现这个拦截器吧！

### 拦截器介绍

定义一个 Interceptor 非常简单方式也有几种，这里简单列举两种：

- 新建类要实现 Spring 的 HandlerInterceptor 接口
- 新建类继承实现了 HandlerInterceptor 接口的实现类，例如已经提供的实现了 HandlerInterceptor 接口的抽象类 HandlerInterceptorAdapter

HandlerInterceptor 方法介绍：

```java
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception;

    void postHandle(
            HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;

    void afterCompletion(
            HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;
```

- **preHandle**：在业务处理器处理请求之前被调用。预处理，可以进行编码、安全控制、权限校验等处理；

- **postHandle**：在业务处理器处理请求执行完成后，生成视图之前执行。

- **afterCompletion**：在 DispatcherServlet 完全处理完请求后被调用，可用于清理资源等，返回处理（已经渲染了页面）；

  

### 定义拦截器

新建 interceptor 包，在包中新建 AdminLoginInterceptor 类，该类需要实现 HandlerInterceptor 接口，代码如下：

```java
package com.site.blog.my.core.interceptor;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 后台系统身份验证拦截器
 */
@Component
public class AdminLoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object o) throws Exception {
        String uri = request.getRequestURI();
        if (uri.startsWith("/admin") && null == request.getSession().getAttribute("loginUser")) {
            request.getSession().setAttribute("errorMsg", "请登陆");
            response.sendRedirect(request.getContextPath() + "/admin/login");
            return false;
        } else {
            request.getSession().removeAttribute("errorMsg");
            return true;
        }
    }
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
    }
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
    }
}
```

我们只需要完善 `preHandle` 方法即可，同时在类声明上方添加 `@Component` 注解使其注册到 IOC 容器中。通过上面代码可以看出，在请求的预处理过程中读取当前 session 中是否存在 loginUser 对象，如果不存在则返回 false 并跳转至登录页面，如果已经存在则返回 true，继续做后续处理流程。

### 配置拦截器

在实现拦截器的相关方法之后，我们需要对该拦截器进行配置以使其生效，在 Spring Boot 1.x 版本中我们通常会继承 WebMvcConfigurerAdapter 类，但是在 Spring Boot 2.x 版本中，WebMvcConfigurerAdapter 被弃用，虽然继承 WebMvcConfigurerAdapter 这个类虽然有此便利，但在 Spring 5.0 里面已经被弃用了，官方文档也说了，WebMvcConfigurer 接口现在已经有了默认的空白方法，所以在 Spring Boot 2.x 版本下更好的做法还是实现 WebMvcConfigurer 接口。

新建 config 包，之后新建 MyBlogWebMvcConfigurer 类并实现 WebMvcConfigurer 接口，代码如下：

```java
package com.site.blog.my.core.config;

import com.site.blog.my.core.interceptor.AdminLoginInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyBlogWebMvcConfigurer implements WebMvcConfigurer {

    @Autowired
    private AdminLoginInterceptor adminLoginInterceptor;

    public void addInterceptors(InterceptorRegistry registry) {
        // 添加一个拦截器，拦截以/admin为前缀的url路径
        registry.addInterceptor(adminLoginInterceptor).addPathPatterns("/admin/**").excludePathPatterns("/admin/login").excludePathPatterns("/admin/dist/**").excludePathPatterns("/admin/plugins/**");
    }
}
```

在该配置类中，我们添加刚刚新增的 AdminLoginInterceptor 登录拦截器，并对该拦截器所拦截的路径进行配置，由于后端管理系统的所有请求路径都以 /admin 开头，所以拦截的路径为 /admin/** ，但是登陆页面以及部分静态资源文件也是以 /admin 开头，所以需要将这些路径排除，配置如上。

此时，重启项目，并且再去访问 index 页面，如果没登录的话就会跳回到登录页面了，拦截器生效了。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm56)



## 用户模块完善

用户模块不只是登录功能，还包括用户信息修改、安全退出的功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm57)

新建 profile.html，前端模板代码如下：(**resources/templates/admin/profile.html**)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<header th:replace="admin/header::header-fragment"></header>
<body class="hold-transition sidebar-mini">
<div class="wrapper">
    <!-- 引入页面头header-fragment -->
    <div th:replace="admin/header::header-nav"></div>
    <!-- 引入工具栏sidebar-fragment -->
    <aside th:fragment="sidebar-fragment(path)" class="main-sidebar sidebar-dark-primary elevation-4">
        <!-- Brand Logo -->
        <a th:href="@{/admin/index}" class="brand-link">
            <img th:src="@{/admin/dist/img/logo.png}" alt="ssm-cluster Logo" class="brand-image img-circle elevation-3"
                 style="opacity: .8">
            <span class="brand-text font-weight-light">my blog</span>
        </a>
        <!-- Sidebar -->
        <div class="sidebar">
            <!-- Sidebar user panel (optional) -->
            <div class="user-panel mt-3 pb-3 mb-3 d-flex">
                <div class="image">
                    <img th:src="@{/admin/dist/img/avatar5.png}" class="img-circle elevation-2" alt="User Image">
                </div>
                <div class="info">
                    <a href="#" class="d-block" th:text="${session.loginUser}"></a>
                </div>
            </div>
            <!-- Sidebar Menu -->
            <nav class="mt-2">
                <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu"
                    data-accordion="false">
                    <!-- Add icons to the links using the .nav-icon class
                         with font-awesome or any other icon font library -->
                    <li class="nav-header">首页</li>
                    <li class="nav-item">
                        <a th:href="@{/admin/index}" class="nav-link">
                            <i class="nav-icon fa fa-dashboard"></i>
                            <p>
                                首页
                            </p>
                        </a>
                    </li>
                    <li class="nav-header">系统管理</li>
                    <li class="nav-item">
                        <a th:href="@{/admin/profile}" class="nav-link active">
                            <i class="fa fa-user-secret nav-icon"></i>
                            <p>修改密码</p>
                        </a>
                    </li>
                    <li class="nav-item">
                        <a th:href="@{/admin/logout}" class="nav-link">
                            <i class="fa fa-sign-out nav-icon"></i>
                            <p>安全退出</p>
                        </a>
                    </li>
                    </li>
                </ul>
            </nav>
            <!-- /.sidebar-menu -->
        </div>
        <!-- /.sidebar -->
    </aside>
    <!-- Content Wrapper. Contains page content -->
    <div class="content-wrapper">
        <!-- Content Header (Page header) -->
        <div class="content-header">
            <div class="container-fluid">
            </div><!-- /.container-fluid -->
        </div>
        <!-- Main content -->
        <section class="content">
            <div class="container-fluid">
                <div class="row">
                    <div class="col-md-6">
                        <div class="card card-primary card-outline">
                            <div class="card-header">
                                <h3 class="card-title">基本信息</h3>
                            </div> <!-- /.card-body -->
                            <div class="card-body">
                                <form role="form" id="userNameForm">
                                    <div class="form-group col-sm-8">
                                        <div class="alert alert-danger" id="updateUserName-info"
                                             style="display: none;"></div>
                                    </div>
                                    <!-- text input -->
                                    <div class="form-group">
                                        <label>登陆名称</label>
                                        <input type="text" class="form-control" id="loginUserName"
                                               name="loginUserName"
                                               placeholder="请输入登陆名称" required="true" th:value="${loginUserName}">
                                    </div>
                                    <div class="form-group">
                                        <label>昵称</label>
                                        <input type="text" class="form-control" id="nickName"
                                               name="nickName"
                                               placeholder="请输入昵称" required="true" th:value="${nickName}">
                                    </div>
                                    <div class="card-footer">
                                        <button type="button" id="updateUserNameButton" onsubmit="return false;"
                                                class="btn btn-danger float-right">确认修改
                                        </button>
                                    </div>
                                </form>
                            </div><!-- /.card-body -->
                        </div>
                    </div>
                    <div class="col-md-6">
                        <div class="card card-primary card-outline">
                            <div class="card-header">
                                <h3 class="card-title">修改密码</h3>
                            </div> <!-- /.card-body -->
                            <div class="card-body">
                                <form role="form" id="userPasswordForm">
                                    <div class="form-group col-sm-8">
                                        <div class="alert alert-danger updatePassword-info" id="updatePassword-info"
                                             style="display: none;"></div>
                                    </div>
                                    <!-- input states -->
                                    <div class="form-group">
                                        <label class="control-label"><i class="fa fa-key"></i> 原密码</label>
                                        <input type="text" class="form-control" id="originalPassword"
                                               name="originalPassword"
                                               placeholder="请输入原密码" required="true">
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label"><i class="fa fa-key"></i> 新密码</label>
                                        <input type="text" class="form-control" id="newPassword" name="newPassword"
                                               placeholder="请输入新密码" required="true">
                                    </div>
                                    <div class="card-footer">
                                        <button type="button" id="updatePasswordButton" onsubmit="return false;"
                                                class="btn btn-danger float-right">确认修改
                                        </button>
                                    </div>
                                </form>
                            </div><!-- /.card-body -->
                        </div>
                    </div>
                </div>
            </div><!-- /.container-fluid -->
        </section>
        <!-- /.content -->
    </div>
    <!-- /.content-wrapper -->
    <!-- 引入页脚footer-fragment -->
    <div th:replace="admin/footer::footer-fragment"></div>
</div>
<!-- jQuery -->
<script th:src="@{/admin/plugins/jquery/jquery.min.js}"></script>
<!-- jQuery UI 1.11.4 -->
<script th:src="@{/admin/plugins/jQueryUI/jquery-ui.min.js}"></script>
<!-- Bootstrap 4 -->
<script th:src="@{/admin/plugins/bootstrap/js/bootstrap.bundle.min.js}"></script>
<!-- AdminLTE App -->
<script th:src="@{/admin/dist/js/adminlte.min.js}"></script>
<!-- public.js -->
<script th:src="@{/admin/dist/js/public.js}"></script>
<!-- profile -->
<script th:src="@{/admin/dist/js/profile.js}"></script>
</body>
</html>
```

后端逻辑实现也是在 AdminController 类中，代码如下：(**注：完整代码位于 `com.site.blog.my.core.controller.admin` 包下**)

```java
    @GetMapping("/profile")
    public String profile(HttpServletRequest request) {
        Integer loginUserId = (int) request.getSession().getAttribute("loginUserId");
        AdminUser adminUser = adminUserService.getUserDetailById(loginUserId);
        if (adminUser == null) {
            return "admin/login";
        }
        request.setAttribute("path", "profile");
        request.setAttribute("loginUserName", adminUser.getLoginUserName());
        request.setAttribute("nickName", adminUser.getNickName());
        return "admin/profile";
    }

    @PostMapping("/profile/password")
    @ResponseBody
    public String passwordUpdate(HttpServletRequest request, @RequestParam("originalPassword") String originalPassword,
                                 @RequestParam("newPassword") String newPassword) {
        if (StringUtils.isEmpty(originalPassword) || StringUtils.isEmpty(newPassword)) {
            return "参数不能为空";
        }
        Integer loginUserId = (int) request.getSession().getAttribute("loginUserId");
        if (adminUserService.updatePassword(loginUserId, originalPassword, newPassword)) {
            //修改成功后清空session中的数据，前端控制跳转至登录页
            request.getSession().removeAttribute("loginUserId");
            request.getSession().removeAttribute("loginUser");
            request.getSession().removeAttribute("errorMsg");
            return "success";
        } else {
            return "修改失败";
        }
    }

    @PostMapping("/profile/name")
    @ResponseBody
    public String nameUpdate(HttpServletRequest request, @RequestParam("loginUserName") String loginUserName,
                             @RequestParam("nickName") String nickName) {
        if (StringUtils.isEmpty(loginUserName) || StringUtils.isEmpty(nickName)) {
            return "参数不能为空";
        }
        Integer loginUserId = (int) request.getSession().getAttribute("loginUserId");
        if (adminUserService.updateName(loginUserId, loginUserName, nickName)) {
            return "success";
        } else {
            return "修改失败";
        }
    }

    @GetMapping("/logout")
    public String logout(HttpServletRequest request) {
        request.getSession().removeAttribute("loginUserId");
        request.getSession().removeAttribute("loginUser");
        request.getSession().removeAttribute("errorMsg");
        return "admin/login";
    }
```

源码已经提供给大家了，接下来我们来实际的操作一下用户模块的登陆登出、信息修改功能。



## 实验楼 web ide 中操作 Spring Boot 项目

我们提供的整个项目的完整代码，请大家参照源码对比学习了解各个功能模块的代码内容。

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/My-Blog-18.zip
unzip My-Blog-18.zip
```



### 启动 MySQL

与前一个实验所使用的数据库环境一样，启动 MySQL 数据库并检查是否包含 my_blog_db 数据库以及 tb_admin_user 表，之后启动 mysql 服务即可。



### 启动项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到已解压的 My-Blog 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 `mvn spring-boot:run` ，之后就可以等待项目启动。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm58)



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 访问页面：`https://********.simplelab.cn/admin/login` 即可进入到登录页面，登陆之后就可以对相关功能进行测试了，演示过程如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm59)

这里演示了用户信息的修改、登录和退出登录的功能以及基本的页面展示，其余的功能大家可以自行验证。本次实验完成！



## 实验总结

用户的身份认证流程中应该包括登陆功能、身份认证、访问拦截、退出功能，本实验已经将流程中所有的功能都完善了，关于管理员模块的功能开发我们就完成了，接下来将会是博客系统中其他内容的功能开发。

本实验中有几点也需要格外注意：

1. 下载代码并解压，检查代码是否正确
2. 启动 MySQL 数据库并检查是否包含 tb_admin_user 表
3. 端口号和数据库连接信息是否正确

希望大家在本地和实验楼线上环境都能够顺利进行测试和实践。



# 十五、SpringBoot博客系统项目开发之分类功能实现

## 实验介绍

#### 实验内容

前文已经实现了用户的登录及权限验证模块，接下来将会继续完善后台功能，开发博客系统的文章相关模块，本节实验将会对博客的分类模块进行介绍和功能开发及完善。

#### 实验知识点

- 分类模块介绍
- 分类模块表结构设计及接口实现
- 分类模块页面设计及编码
- 分类功能测试

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+

#### 1.4 代码获取

**在学习前，一定要获取源码，因为篇幅原因，文章中只会截取部分关键代码。请大家参照完整源码进行学习**

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/My-Blog-19.zip
unzip My-Blog-19.zip
cd My-Blog
```



## 分类模块简介

在博客系统中，分类模块的设计是不可缺少的，我们在各大博客网站中都能够看到这个模块设计，在浏览文章的过程中，我们也会挑选出我们感兴趣类别中的文章进行阅读，比如看了一篇架构的文章，觉得不错，那么就想接着去看这个类别下的其他文章，或者你偏爱前端类别下的内容，那就可以针对性的浏览所有前端类别下的文章，因此对博文进行归类是十分必要的。

分类是通过比较事物之间的相似性，把具有某些共同点或相似特征的事物归属于一个不确定集合的逻辑方法，而对应的，我们可以说分类别的作用是使一个大集合中的内容条理清楚，层次分明，接下来是功能开发的讲解。



## 表结构设计及 Mapper 文件自动生成

#### 表结构设计

在进行接口设计和具体的功能实现前，首先将表结构确定下来，每篇文章都会被归类到一个类别下，一个类别下会有多篇文章，分类实体与文章实体的关系是一对多的关系，因此在表结构设计时，在文章表中设置一个分类关联字段即可，分类表只需要将分类相关的字段定义好，分类实体与文章实体的关系交给文章表来维护即可（后续讲到文章表时再介绍），分类表的 SQL 设计如下，直接执行如下 SQL 语句即可：

```sql
USE `my_blog_db`;

/*Table structure for table `tb_blog_category` */

CREATE TABLE `tb_blog_category` (
  `category_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '分类表主键',
  `category_name` varchar(50) NOT NULL COMMENT '分类的名称',
  `category_icon` varchar(50) NOT NULL COMMENT '分类的图标',
  `category_rank` int(11) NOT NULL DEFAULT '1' COMMENT '分类的排序值 被使用的越多数值越大',
  `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`category_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

分类表的字段以及每个字段对应的含义都在上面的 SQL 中有介绍，大家可以对照 SQL 进行理解，把表结构导入到数据库中即可，接下来我们进行编码工作。



### MyBatis-Generator 插件自动生成 Mapper 文件

首先，我们使用 MyBatis-Generator 插件将该表对应的 Mapper 文件及对应的实体类和 Dao 层接口生成出来，这部分代码就不贴在文章中了，大家可以通过下载源码来查看。

需要注意的是，在代码生成后需要在 dao 层下的 BlogCategoryMapper 接口类上添加 @Mapper 注解以将其注册到 IOC 容器中以供后续调用（如果已经在主类上添加 @MapperScan 注解这一步可以省略）。其次，在 tb_blog_category 表中，我们设计了一个 is_deleted 字段，用于逻辑删除的标志位，由于 is_deleted 的字段设计，我们对表中数据的删除都是软删除，而不是真正意义的删除，只是做了一个删除标志位，如果此字段为 1 则表示已经被删除不再使用，因为是个人博客，这么做的目的主要也是为了防止误删，因此我们需要修改 Mapper 文件中的 查询语句和删除语句，将 is_deleted 条件带上，修改后的语句如下：(**注：完整代码位于 resources/mapper/BlogCategoryMapper.xml**)

```xml
   <select id="findCategoryList" parameterType="Map" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from tb_blog_category
        where is_deleted=0
        order by category_rank desc,create_time desc
        <if test="start!=null and limit!=null">
            limit #{start},#{limit}
        </if>
    </select>

    <select id="getTotalCategories" parameterType="Map" resultType="int">
    select count(*)  from tb_blog_category
    where is_deleted=0
    </select>

    <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from tb_blog_category
        where category_id = #{categoryId,jdbcType=INTEGER} AND is_deleted = 0
    </select>

    <update id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    UPDATE tb_blog_category SET  is_deleted = 1
    where category_id = #{categoryId,jdbcType=VARCHAR} AND is_deleted = 0
    </update>
```

通过以上代码我们可以看出，在删除操作时我们并不是执行 delete 语句，而是将需要删除的这条记录的 is_deleted 字段修改为 1，这样就表示该行记录已经被执行了删除操作，那么其他的 select 查询语句就需要在查询条件中添加 `is_deleted = 0` 将“被删除”的记录给过滤出去，这个知识点希望大家能够理解，在后续的其他功能模块中我们也会使用软删除的设计。



## 分类模块接口设计及实现

#### 接口介绍

为了让页面体验更加友好，就不采用传统的 MVC 跳转模式，一个功能一个页面，这种交互感觉有些浪费，翻页的时候，翻一页跳转一次也比较繁琐，添加或者新增的时候也要进行页面跳转，所以这些功能的实现就采用通过 Ajax 异步与后端交互数据，当使用者点击了页面上的元素，此时触发响应的 js 事件，进而通过 Ajax 的方式向后端请求数据，前端再根据后端返回的数据内容去进行响应的展示逻辑，在前面的个人信息修改中其实用到的就是这种方式。

关于接口的设计以及前后端交互数据的设计大家可以参考一下第 10 课中的内容，分类模块在后台管理系统中有 5 个接口，分别是：

- 分类列表分页接口
- 添加分类接口
- 根据 id 获取单条分类记录接口
- 修改分类接口
- 删除分类接口

接下来讲解每个接口具体的实现代码，首先在 controller/admin 包下新建 CategoryController.java，并在 service 包下新建业务层代码 CategoryService.java 及实现类，之后参照接口分别进行功能实现。



### 分类列表分页接口

列表接口负责接收前端传来的分页参数，如 `page` 、`limit` 等参数，之后将数据总数和对应页面的数据列表查询出来并封装为分页数据返回给前端。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.CategoryController.java**）：

接口的映射地址为 /categories/list，请求方法为 GET，代码如下：

```java
    /**
     * 分类列表
     */
    @RequestMapping(value = "/categories/list", method = RequestMethod.GET)
    @ResponseBody
    public Result list(@RequestParam Map<String, Object> params) {
        if (StringUtils.isEmpty(params.get("page")) || StringUtils.isEmpty(params.get("limit"))) {
            return ResultGenerator.genFailResult("参数异常！");
        }
        PageQueryUtil pageUtil = new PageQueryUtil(params);
        return ResultGenerator.genSuccessResult(categoryService.getBlogCategoryPage(pageUtil));
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.CategoryServiceImpl.java**）：

```java
    public PageResult getBlogCategoryPage(PageQueryUtil pageUtil) {
        List<BlogCategory> categoryList = blogCategoryMapper.findCategoryList(pageUtil);
        int total = blogCategoryMapper.getTotalCategories(pageUtil);
        PageResult pageResult = new PageResult(categoryList, total, pageUtil.getLimit(), pageUtil.getPage());
        return pageResult;
    }
```

- BlogCategoryMapper.xml （注：完整代码位于 **resources/mapper/BlogCategoryMapper.xml**）：

```xml
    <select id="findCategoryList" parameterType="Map" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from tb_blog_category
        where is_deleted=0
        order by category_rank desc,create_time desc
        <if test="start!=null and limit!=null">
            limit #{start},#{limit}
        </if>
    </select>

    <select id="getTotalCategories" parameterType="Map" resultType="int">
    select count(*)  from tb_blog_category
    where is_deleted=0
    </select>
```

SQL 语句在 BlogCategoryMapper.xml 文件中，一般的分页也就是使用 limit 关键字实现，获取响应条数的记录和总数之后再进行数据封装，这个接口就是根据前端传的分页参数进行查询并返回分页数据以供前端页面进行数据渲染。



### 添加分类接口

添加接口负责接收前端的 POST 请求并处理其中的参数，接收的参数为 categoryName 字段和 categoryIcon 字段，categoryName 为分类名称，categoryIcon 字段为分类的图标字段。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.CategoryController.java**）：

接口的映射地址为 /categories/save，请求方法为 POST，代码如下：

```java
    /**
     * 分类添加
     */
    @RequestMapping(value = "/categories/save", method = RequestMethod.POST)
    @ResponseBody
    public Result save(@RequestParam("categoryName") String categoryName,
                       @RequestParam("categoryIcon") String categoryIcon) {
        if (StringUtils.isEmpty(categoryName)) {
            return ResultGenerator.genFailResult("请输入分类名称！");
        }
        if (StringUtils.isEmpty(categoryIcon)) {
            return ResultGenerator.genFailResult("请选择分类图标！");
        }
        if (categoryService.saveCategory(categoryName, categoryIcon)) {
            return ResultGenerator.genSuccessResult();
        } else {
            return ResultGenerator.genFailResult("分类名称重复");
        }
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.CategoryServiceImpl.java**）：

```java
    public Boolean saveCategory(String categoryName, String categoryIcon) {
        BlogCategory temp = blogCategoryMapper.selectByCategoryName(categoryName);
        if (temp == null) {
            BlogCategory blogCategory = new BlogCategory();
            blogCategory.setCategoryName(categoryName);
            blogCategory.setCategoryIcon(categoryIcon);
            return blogCategoryMapper.insertSelective(blogCategory) > 0;
        }
        return false;
    }
```

SQL 语句在 BlogCategoryMapper.xml 文件中，是自动生成的 SQL，这里就不贴了。

添加接口中，首先会对参数进行校验，之后交给业务层代码进行操作，在 `saveCategory()` 方法中，首先会根据名称查询是否已经存在该分类，之后才会进行数据封装并进行数据库 insert 操作。



### 删除分类接口

删除接口负责接收前端的分类删除请求，处理前端传输过来的数据后，将这些记录从数据库中删除，这里的“删除”功能并不是真正意义上的删除，而是逻辑删除，我们将接受的参数设置为一个数组，可以同时删除多条记录，只需要在前端将用户选择的记录 id 封装好再传参到后端即可。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.CategoryController.java**）：

接口的映射地址为 /categories/delete，请求方法为 POST，代码如下：

```java
     /**
     * 分类删除
     */
    @RequestMapping(value = "/categories/delete", method = RequestMethod.POST)
    @ResponseBody
    public Result delete(@RequestBody Integer[] ids) {
        if (ids.length < 1) {
            return ResultGenerator.genFailResult("参数异常！");
        }
        if (categoryService.deleteBatch(ids)) {
            return ResultGenerator.genSuccessResult();
        } else {
            return ResultGenerator.genFailResult("删除失败");
        }
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.CategoryServiceImpl.java**）：

```java
    public Boolean deleteBatch(Integer[] ids) {
        if (ids.length < 1) {
            return false;
        }
        //删除分类数据
        return blogCategoryMapper.deleteBatch(ids) > 0;
    }
```

- BlogCategoryMapper.xml （注：完整代码位于 **resources/mapper/BlogCategoryMapper.xml**）：

```xml
    <update id="deleteBatch">
        update tb_blog_category
        set is_deleted=1 where category_id in
        <foreach item="id" collection="array" open="(" separator="," close=")">
            #{id}
        </foreach>
    </update>
```

接口的请求路径为 /categories/delete，并使用 `@RequestBody` 将前端传过来的参数封装为 id 数组，参数验证通过后则调用 `deleteBatch()` 批量删除方法进行数据库操作，否则将向前端返回错误信息。



### 其它

还有根据 id 获取详情的接口，路径为 categories/info/{id}，请求方法为 GET；分类修改接口，路径为 categories/update，请求方法为 POST。

在接口设计和实现完成后，需要对接口进行测试，如果出现问题立刻修复，以上这些代码我都已经编写完成并将代码以压缩包的形式提供，大家在学习时可以直接使用我给的代码进行练习。



## 前端页面实现

#### category.html

在 resources/templates/admin 目录下新建 category.html，并引入对应的 js 文件和 css 样式文件。

#### 功能按钮

分类管理模块我们也设计了常用的几个功能：分类信息增加、分类信息编辑、分类信息删除，因此在页面中添加对应的功能按钮以及触发事件，代码如下：

```html
<div class="grid-btn">
    <button class="btn btn-info" onclick="categoryAdd()"><i class="fa fa-plus"></i>&nbsp;新增
    </button>
    <button class="btn btn-info" onclick="categoryEdit()"><i class="fa fa-pencil-square-o"></i>&nbsp;修改</button>
    <button class="btn btn-danger" onclick="deleteCagegory()"><i class="fa fa-trash-o"></i>&nbsp;删除</button>
</div>
```

分别是添加按钮，对应的触发事件是 `categoryAdd()` 方法，修改按钮，对应的触发事件是 `categoryEdit()` 方法，删除按钮，对应的触发事件是 `deleteCagegory()` 方法，这些方法我们将在后续的代码实现中给出。

#### 分页信息展示区域

页面中已经引入 JqGrid 的相关静态资源文件，需要在页面中展示分页数据的区域增加如下代码：

```html
<table id="jqGrid" class="table table-bordered"></table>
<div id="jqGridPager"></div>
```

#### 页面效果

那么最终的静态页面效果展示如下图所示：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm60)

包括功能按钮、数据列表展示区域以及翻页功能区域，此时只是静态效果展示，并没有与后端进行数据交互，接下来我们将结合 Ajax 和后端接口实现具体的功能。





## 分类模块前端功能实现

完成了页面展示的实现，接着完成相关的功能实现。请大家一定自行对照源码完成代码编写。



### 分页功能

在 resources/static/admin/dist/js 目录下新增 category.js 文件，并添加如下代码：

```js
$(function () {
    $("#jqGrid").jqGrid({
        url: '/admin/categories/list',
        datatype: "json",
        colModel: [
            {label: 'id', name: 'categoryId', index: 'categoryId', width: 50, key: true, hidden: true},
            {label: '分类名称', name: 'categoryName', index: 'categoryName', width: 240},
            {label: '分类图标', name: 'categoryIcon', index: 'categoryIcon', width: 120, formatter: imgFormatter},
            {label: '添加时间', name: 'createTime', index: 'createTime', width: 120}
        ],
        height: 560,
        rowNum: 10,
        rowList: [10, 20, 50],
        styleUI: 'Bootstrap',
        loadtext: '信息读取中...',
        rownumbers: false,
        rownumWidth: 20,
        autowidth: true,
        multiselect: true,
        pager: "#jqGridPager",
        jsonReader: {
            root: "data.list",
            page: "data.currPage",
            total: "data.totalPage",
            records: "data.totalCount"
        },
        prmNames: {
            page: "page",
            rows: "limit",
            order: "order",
        },
        gridComplete: function () {
            //隐藏grid底部滚动条
            $("#jqGrid").closest(".ui-jqgrid-bdiv").css({"overflow-x": "hidden"});
        }
    });

    $(window).resize(function () {
        $("#jqGrid").setGridWidth($(".card-body").width());
    });

});
```

以上代码的主要功能为分页数据展示、字段格式化 jqGrid DOM 宽度的自适应，在页面加载时，调用 JqGrid 的初始化方法，将页面中 id 为 jqGrid 的 DOM 渲染为分页表格，并向后端发送请求，之后按照后端返回的 json 数据填充表格以及表格下方的分页按钮，可以参考第 15 个实验课程中 jqgrid 分页功能整合进行知识的理解，之后可以重启项目验证一下分类信息分页功能是否正常。



### 按钮事件及 Modal 框实现

添加和修改两个按钮分别绑定了触发事件，我们需要在 category.js 文件中新增 `categoryAdd()` 方法和 `categoryEdit()` 方法，两个方法中的实现为打开信息编辑框，下面我们来实现信息编辑框和两个触发事件，代码如下：(**注：完整代码位于 resources/templates/admin/blog.html**)

```html
        <div class="content">
            <!-- 模态框（Modal） -->
            <div class="modal fade" id="categoryModal" tabindex="-1" role="dialog" aria-labelledby="categoryModalLabel">
                <div class="modal-dialog" role="document">
                    <div class="modal-content">
                        <div class="modal-header">
                            <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span
                                    aria-hidden="true">&times;</span></button>
                            <h6 class="modal-title" id="categoryModalLabel">Modal</h6>
                        </div>
                        <div class="modal-body">
                            <form id="categoryForm" onsubmit="return false;">
                                <div class="form-group">
                                    <div class="alert alert-danger" id="edit-error-msg" style="display: none;">
                                        错误信息展示栏。
                                    </div>
                                </div>
                                <input type="hidden" class="form-control" id="categoryId" name="categoryId">
                                <div class="form-group">
                                    <label for="categoryName" class="control-label">分类名称:</label>
                                    <input type="text" class="form-control" id="categoryName" name="categoryName"
                                           placeholder="请输入分类名称" required="true">
                                </div>
                                <div class="form-group">
                                    <label for="categoryIcon" class="control-label">分类图标:</label>
                                    <input type="hidden" class="form-control" id="categoryIcon" name="categoryIcon">
                                    <div class="col-sm-4">
                                        <img id="categoryIconImg" src="/admin/dist/img/img-upload.png"
                                             style="height: 64px;width: 64px;">
                                        <button class="btn btn-secondary" style="margin-top: 5px;margin-bottom: 5px;"
                                                id="categoryIconButton"><i
                                                class="fa fa-random"></i>&nbsp;图标切换
                                        </button>
                                    </div>
                                </div>
                            </form>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-default" data-dismiss="modal">取消</button>
                            <button type="button" class="btn btn-primary" id="saveButton">确认</button>
                        </div>
                    </div>
                </div>
            </div>
```

`categoryAdd()` 方法和 `categoryEdit()` 方法实现如下：(**注：完整代码位于 resources/static/admin/dist/js/category.js**)

```js
function categoryAdd() {
    reset();
    $('.modal-title').html('分类添加');
    $('#categoryModal').modal('show');
}


function categoryEdit() {
    reset();
    var id = getSelectedRow();
    if (id == null) {
        return;
    }
    $('.modal-title').html('分类编辑');
    $('#categoryModal').modal('show');
    //请求数据
    $.get("/admin/categories/info/" + id, function (r) {
        if (r.resultCode == 200 && r.data != null) {
            //填充数据至modal
            $("#categoryIconImg").attr("src", r.data.categoryIcon);
            $("#categoryIconImg").attr("style", "width:64px ;height: 64px;display:block;");
            $("#categoryIcon").val(r.data.categoryIcon);
            $("#categoryName").val(r.data.categoryName);
        }
    });
    $("#categoryId").val(id);
}
```

添加方法仅仅是将 Modal 框显示，修改功能则多了一个步骤，需要将选择的记录回显到编辑框中以供修改，因此需要请求 categories/info/{id} 详情接口获取被修改的分类数据信息。

Modal 框实际效果如下：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm61)

在项目中内置了 20 个分类图标，图标在 resources/static/admin/dist/img/category 目录下，点击上图中的图标切换可以随机获取到一个图标，填写分类名称并选择合适的图标保存即可。



### 添加功能和编辑功能

在信息录入完成后可以点击信息编辑框下方的**确认**按钮，此时会进行数据的交互，js 实现代码如下：(**注：完整代码位于 resources/static/admin/dist/js/category.js**)

```js
//绑定modal上的保存按钮
$('#saveButton').click(function () {
    var categoryName = $("#categoryName").val();
    if (!validCN_ENString2_18(categoryName)) {
        $('#edit-error-msg').css("display", "block");
        $('#edit-error-msg').html("请输入符合规范的分类名称！");
    } else {
        var params = $("#categoryForm").serialize();
        var url = '/admin/categories/save';
        var id = getSelectedRowWithoutAlert();
        if (id != null) {
            url = '/admin/categories/update';
        }
        $.ajax({
            type: 'POST',//方法类型
            url: url,
            data: params,
            success: function (result) {
                if (result.resultCode == 200) {
                    $('#categoryModal').modal('hide');
                    swal("保存成功", {
                        icon: "success"
                    });
                    reload();
                }
                else {
                    $('#categoryModal').modal('hide');
                    swal(result.message, {
                        icon: "error"
                    });
                }
            },
            error: function () {
                swal("操作失败", {
                    icon: "error"
                });
            }
        });
    }
});
```

由于传参和后续处理逻辑类似，为了避免太多重复代码因此将两个方法写在一起了，通过 id 是否大于 0 来确定是修改操作还是添加操作，方法步骤如下：

1. 前端对用户输入的数据进行简单的正则验证
2. 封装数据
3. 向对应的后端接口发送 Ajax 请求
4. 请求成功后提醒用户请求成功并隐藏当前的信息编辑框，同时刷新列表数据
5. 请求失败则提醒对应的错误信息



### 删除功能

删除按钮的点击触发事件为 `deleteCagegory()`，在 category.js 文件中新增如下代码：(**注：完整代码位于 resources/static/admin/dist/js/category.js**)

```js
function deleteCagegory() {
    var ids = getSelectedRows();
    if (ids == null) {
        return;
    }
    swal({
        title: "确认弹框",
        text: "确认要删除数据吗?",
        icon: "warning",
        buttons: true,
        dangerMode: true,
    }).then((flag) => {
        if(flag) {
            $.ajax({
                type: "POST",
                url: "/admin/categories/delete",
                contentType: "application/json",
                data: JSON.stringify(ids),
                success: function (r) {
                    if (r.resultCode == 200) {
                        swal("删除成功", {
                            icon: "success",
                        });
                        $("#jqGrid").trigger("reloadGrid");
                    } else {
                        swal(r.message, {
                            icon: "error",
                        });
                    }
                }
            });
        }
    });
}
```

获取用户在 jqgrid 表格中选择的需要删除的所有记录的 id，之后将参数封装并向后端发送 Ajax 请求，请求地址为 categories/delete，删除功能已经开发完成，同学们可以结合源码以及实际的功能进行学习和理解，接下来我们来进行功能测试。



## 实验楼 web ide 中操作 Spring Boot 项目

我们提供的整个项目的完整代码，请大家下载并参照源码对比学习了解各个功能模块的代码内容。



### 启动 MySQL 并新增 tb_blog_category 表

**如果 MySQL 数据库服务没有启动的话需要进行这一步操作。**

进入实验楼线上开发环境，首先打开一个命令窗口，点击 File -> Open New Terminal 即可，之后在命令行中输入以下命令：

```shell
sudo service mysql start
```

因为用户权限的关系，需要增加在命令前增加 sudo 取得 root 权限，不然在启动时会报错，之后等待 MySQL 正常启动即可。

之后执行如下命令登录 MySQL 数据库：

```shell
sudo mysql -u root 
```

因为实验楼线上实验环境中 MySQL 数据库默认并没有设置密码，因此以上命令即可完成登录，最后在命令行中执行前文给出的建表语句 SQL 即可，整个过程如下，再执行完新增表的 SQL 后，我们再去查看一下数据库中的表，可以发现 tb_blog_category 表已经在 my_blog_db 数据库中了。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm62)



### 启动项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到已解压的 My-Blog 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 mvn spring-boot:run ，之后就可以等待项目启动。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm63)



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 访问页面：`https://********.simplelab.cn/admin/login` 并登录至后台管理首页，之后点击分类管理就可以对相关功能进行测试了，演示过程如下。

- 添加功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm64)

- 修改功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm65)

- 删除功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm66)

同学们可以按照文中的思路和过程自行测试，本次实验完成！



## 实验总结

Ajax 知识点、分页功能知识点、Model 知识点，前面的实验中已经进行了拆分讲解和最终的整合，在这里其实使用的也是这些功能的整合而完成的该模块所有功能，因此呢，如果有不理解的地方可以先想一想这些功能单独实现是怎样的，再去结合本实验的代码进行学习和理解，不要看到一个知识点就觉得难，首先要学会拆解这些功能，每个功能甚至每个按钮代表什么含义、需要怎样实现、实现步骤是什么样子的。



# 十六、SpringBoot博客系统项目开发之标签功能实现

## 实验介绍

#### 实验内容

本篇继续来完善后台的功能模块，在讲解和开发文章模块前，先将它的相关的属性和必须关联的关系实体讲解和开发完，前一个实验我们将分类模块开发完成，本节实验将会对博客的标签模块进行介绍和功能开发及完善，以便接下来文章模块的讲解和开发。

#### 实验知识点

- 侧边导航栏抽取
- 标签模块表结构设计及接口实现
- 标签模块页面设计及编码
- 标签模块功能测试

#### 实验环境

- JDK 1.8 或者更高版本
- Spring Boot 2.1.0-RELEASE
- Maven 3+

#### 代码获取

**在学习前，一定要获取源码，因为篇幅原因，文章中只会截取部分关键代码。请大家参照完整源码进行学习**

```shell
wget https://labfile.oss.aliyuncs.com/courses/1367/My-Blog-20.zip
unzip My-Blog-20.zip
cd My-Blog
```



## 侧边栏抽取

部分同学可能对 templates/admin 目录下的 sidebar.html 文件有些疑惑，由于篇幅限制，所以没有在前一个实验中对这个知识点做介绍，这里简单的用一个段落来讲解一下这个知识点，首先告诉大家的是，这个模板文件是抽取出来的左侧导航栏文件，由于每个页面都需要加上侧边导航栏的代码，为了精简代码就将这部分代码提取出来作为公共代码，代码简化的同时，也方便维护和修改，代码如下：(**注：完整代码位于 resources/templates/admin/sidebar.html**)

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<aside th:fragment="sidebar-fragment(path)" class="main-sidebar sidebar-dark-primary elevation-4">
    <a th:href="@{/admin/index}" class="brand-link">
        <img th:src="@{/admin/dist/img/logo.png}" alt="ssm-cluster Logo" class="brand-image img-circle elevation-3"
             style="opacity: .8">
        <span class="brand-text font-weight-light">my blog</span>
    </a>
    <!-- Sidebar -->
    <div class="sidebar">
        <!-- Sidebar user panel (optional) -->
        <div class="user-panel mt-3 pb-3 mb-3 d-flex">
            <div class="image">
                <img th:src="@{/admin/dist/img/avatar5.png}" class="img-circle elevation-2" alt="User Image">
            </div>
            <div class="info">
                <a href="#" class="d-block" th:text="${session.loginUser}"></a>
            </div>
        </div>
        <nav class="mt-2">
            <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu"
                data-accordion="false">
                <!-- Add icons to the links using the .nav-icon class
                     with font-awesome or any other icon font library -->
                <li class="nav-header">Dashboard</li>
                <li class="nav-item">
                    <a th:href="@{/admin/index}" th:class="${path}=='index'?'nav-link active':'nav-link'">
                        <i class="nav-icon fa fa-dashboard"></i>
                        <p>
                            Dashboard
                        </p>
                    </a>
                </li>
                <li class="nav-header">管理模块</li>
                <li class="nav-item">
                    <a th:href="@{/admin/categories}" th:class="${path}=='categories'?'nav-link active':'nav-link'">
                        <i class="fa fa-bookmark nav-icon" aria-hidden="true"></i>
                        <p>
                            分类管理
                        </p>
                    </a>
                </li>
                <li class="nav-item">
                    <a th:href="@{/admin/tags}" th:class="${path}=='tags'?'nav-link active':'nav-link'">
                        <i class="fa fa-tags nav-icon" aria-hidden="true"></i>
                        <p>
                            标签管理
                        </p>
                    </a>
                </li>
                <li class="nav-header">系统管理</li>
                <li class="nav-item">
                    <a th:href="@{/admin/profile}"
                       th:class="${path}=='profile'?'nav-link active':'nav-link'">
                        <i class="fa fa-user-secret nav-icon"></i>
                        <p>修改密码</p>
                    </a>
                </li>
                <li class="nav-item">
                    <a th:href="@{/admin/logout}" class="nav-link">
                        <i class="fa fa-sign-out nav-icon"></i>
                        <p>安全退出</p>
                    </a>
                </li>
                </li>
            </ul>
        </nav>
    </div>
</aside>
</html>
```

页面效果如下图所示：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm67)

接下来解释一下具体的实现逻辑，首先，以上这部分代码如果不进行抽取的话，我们在添加其他模块的时候需要在每一个模块的页面代码中添加一遍，但是基本上所有的代码都是重复的，只有一处不同，那就是导航栏中当前模块的选中状态，比如在分类管理页面中，左侧导航栏中的“分类管理”即为选中状态，其他页面与此相同。

这里的实现方式是通过添加一个 path 变量来控制当前导航栏中的选中状态，在模板文件中的 Thymeleaf 判断语句中通过 path 字段来确定是哪个功能模块，并对应的将左侧导航栏上当前模块的 css 样式给修改掉，判断语句如下：

```html
th:class="${path}=='categories'?'nav-link active':'nav-link'"
```

如果当前的 path 字段值为 'categories'，那么“分类管理”这个选项的 css 样式就修改为选中状态，如果当前的 path 字段值为 'tags'，那么“标签管理”这个选项的 css 样式就修改为选中状态，其他模块依次类推，path 字段的值是在哪里进行赋值的呢？答案是 admin 包下的 Controller 类中，在进行页面跳转时，会分别将对应的 path 字段进行赋值，代码如下：

- TagController(**注：完整代码位于 com.site.blog.my.core.controller.admin.TagController.java**)

```java
    @GetMapping("/tags")
    public String tagPage(HttpServletRequest request) {
        request.setAttribute("path", "tags");
        return "admin/tag";
    }
```

- CategoryController (**注：完整代码位于 com.site.blog.my.core.controller.admin.CategoryController.java**)

```java
    @GetMapping("/categories")
    public String categoryPage(HttpServletRequest request) {
        request.setAttribute("path", "categories");
        return "admin/category";
    }
```

通过这种方式，以后如果需要在系统中新增一个模块，就可以对应的增加一个导航栏按钮在 sidebar.html 文件中，并在后端的控制器方法中赋值对应的 path 字段即可，比如博客管理、配置管理等之后的功能模块。



## 标签模块简介

标签是一种更为灵活、更有趣的分类方式，在书写博客时可以为每篇文章添加一个或多个标签，在博客系统中，文章的标签设计被广泛应用，我们可以看到大部分的博客网站中都会有标签设计，因此，在设计 personal-blog 这个项目时，也将标签运用了进来。

标签最明显的作用有如下两点：

- 一是传统意义上分类的作用，类似分类名称
- 二是对文章内容进行一定程度的描述，类似于关键词

虽然与分类设计类似，但是标签和分类还有一些细区别：

- 同一篇文章标签可以用多个，但通常只能属于一个分类
- 标签一般是在写作完成后，根据文章内容自行添加的内容
- 标签可以把文章中重点词语提炼出来，有关键词的意义，但是分类没有
- 标签通常更为主观，其内容相较于分类来说更加具体一些

与分类的功能和设计思想类似，但是又有一定的不同，标签可以算是分类的细化版本，同时，一篇博客的分类最好只有一个，但是在设计的时候，一篇博客的标签是可以有多个的，标签设计的介绍就到这里，接下来是功能开发的讲解。



## 表结构设计及 Mapper 文件自动生成

#### 标签与文章的关系

通过前文中标签设计的介绍我们也大致的了解了标签与文章的关系，一篇文章可以有多个标签字段，一个标签字段也可以被标注在多个文章中，这个情况与前一个实验中的分类设计是有一些差别的，标签实体与文章实体的关系是多对多的关系，因此在表结构设计时不仅仅需要标签实体和文章实体的字段映射，还需要存储二者之间的关系数据，本系统采用的方式是新增一张关系表来维护二者多对多的关联关系。



### 表结构设计

标签表以及标签文章关系表的 SQL 设计如下，直接执行如下 SQL 语句即可：

```sql
USE `my_blog_db`;

DROP TABLE IF EXISTS `tb_blog_tag`;

CREATE TABLE `tb_blog_tag` (
  `tag_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '标签表主键id',
  `tag_name` varchar(100) NOT NULL COMMENT '标签名称',
  `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

DROP TABLE IF EXISTS `tb_blog_tag_relation`;

CREATE TABLE `tb_blog_tag_relation` (
  `relation_id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '关系表id',
  `blog_id` bigint(20) NOT NULL COMMENT '博客id',
  `tag_id` int(11) NOT NULL COMMENT '标签id',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '添加时间',
  PRIMARY KEY (`relation_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

两张表的字段以及每个字段对应的含义都在上面的 SQL 中有介绍，大家可以对照 SQL 进行理解，在关系表中有一个 blog_id 字段，是文章表的主键 id，虽然暂时还没有介绍文章模块，但是大家应该能够理解关系表存放的字段含义，这张表存储的就是标签记录对应的文章记录，以多对多的方式进行记录的，把表结构导入到数据库中即可，接下来我们进行编码工作。

另外，需要在这里提醒大家，标签表以及关系表的大部分的实现逻辑是在后续文章管理模块中进行调用和实现的，本实验仅仅介绍后台管理系统中的内容，所以不要觉得这两张表意义不大。



### MyBatis-Generator 插件自动生成 Mapper 文件

首先，我们使用 MyBatis-Generator 插件将两张表对应的 Mapper 文件及对应的实体类和 Dao 层接口生成出来，这部分代码就不贴在文章中了，大家可以通过下载源码来查看。

需要注意的是，在代码生成后需要在 dao 层下的 BlogTagMapper接口类和 BlogTagRelationMapper 接口类上添加 @Mapper 注解以将其注册到 IOC 容器中以供后续调用（如果已经在主类上添加 @MapperScan 注解这一步可以省略），生成的 Mapper 文件也需要对应的修改，因为使用的是软删除设计，所以需要在生成的 SQL 语句中对应的加上 is_deleted 字段的逻辑，is_deleted 字段在前文中也已经有详细的介绍，这里就不再赘述。



## 标签模块接口设计及实现

#### 接口介绍

标签模块的大部分实现逻辑是在维护文章表时处理的，后台管理系统中标签模块的功能比较简单，标签模块在后台管理系统中有 3 个接口，分别是：

- 列表分页接口
- 添加标签接口
- 删除标签接口

接下来讲解每个接口具体的实现代码，首先在 controller/admin 包下新建 TagController.java，并在 service 包下新建业务层代码 TagService.java 及实现类，之后参照接口分别进行功能实现。



### 标签列表分页接口

列表接口负责接收前端传来的分页参数，如 page 、limit 等参数，之后将数据总数和对应页面的数据列表查询出来并封装为分页数据返回给前端。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.TagController.java**）：

接口的映射地址为 /tags/list，请求方法为 GET，代码如下：

```java
    public Result list(@RequestParam Map<String, Object> params) {
        if (StringUtils.isEmpty(params.get("page")) || StringUtils.isEmpty(params.get("limit"))) {
            return ResultGenerator.genFailResult("参数异常！");
        }
        PageQueryUtil pageUtil = new PageQueryUtil(params);
        return ResultGenerator.genSuccessResult(tagService.getBlogTagPage(pageUtil));
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.TagServiceImpl.java**）：

```java
   public PageResult getBlogTagPage(PageQueryUtil pageUtil) {
        List<BlogTag> tags = blogTagMapper.findTagList(pageUtil);
        int total = blogTagMapper.getTotalTags(pageUtil);
        PageResult pageResult = new PageResult(tags, total, pageUtil.getLimit(), pageUtil.getPage());
        return pageResult;
    }
```

- BlogTagMapper.xml （注：完整代码位于 **resources/mapper/BlogTagMapper.xml**）：

```xml
    <select id="findTagList" parameterType="Map" resultMap="BaseResultMap">
        select
        <include refid="Base_Column_List"/>
        from tb_blog_tag
        where is_deleted=0
        order by tag_id desc
        <if test="start!=null and limit!=null">
            limit #{start},#{limit}
        </if>
    </select>

    <select id="getTotalTags" parameterType="Map" resultType="int">
    select count(*)  from tb_blog_tag
    where is_deleted=0
      </select>
```

SQL 语句在 BlogTagMapper.xml 文件中， SQL 语句是在自动生成的 SQL 语句中增加了 is_deleted 字段的过滤条件，一般的分页也就是使用 limit 关键字实现，获取响应条数的记录和总数之后再进行数据封装，这个接口就是根据前端传的分页参数进行查询并返回分页数据以供前端页面进行数据渲染。



### 添加标签接口

添加接口负责接收前端的 POST 请求并处理其中的参数，接收的参数为 tagName 字段，tagName 为标签名称。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.TagController.java**）：

接口的映射地址为 /tags/save，请求方法为 POST，代码如下：

```java
    @PostMapping("/tags/save")
    @ResponseBody
    public Result save(@RequestParam("tagName") String tagName) {
        if (StringUtils.isEmpty(tagName)) {
            return ResultGenerator.genFailResult("参数异常！");
        }
        if (tagService.saveTag(tagName)) {
            return ResultGenerator.genSuccessResult();
        } else {
            return ResultGenerator.genFailResult("标签名称重复");
        }
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.TagServiceImpl.java**）：

```java
    @Override
    public Boolean saveTag(String tagName) {
        BlogTag temp = blogTagMapper.selectByTagName(tagName);
        if (temp == null) {
            BlogTag blogTag = new BlogTag();
            blogTag.setTagName(tagName);
            return blogTagMapper.insertSelective(blogTag) > 0;
        }
        return false;
    }
```

SQL 语句在 BlogTagMapper.xml 文件中，是自动生成的 SQL，这里就不贴了，标签添加的实现逻辑中，首先会对参数进行校验，之后交给业务层代码进行操作，在 saveTag() 方法中，首先会根据名称查询是否已经存在该标签对象，之后才会进行数据封装并进行数据库 insert 操作。



### 删除标签接口

删除接口负责接收前端的标签删除请求，处理前端传输过来的数据后，将这些记录从数据库中删除，这里的“删除”功能并不是真正意义上的删除，而是逻辑删除，我们将接受的参数设置为一个数组，可以同时删除多条记录，只需要在前端将用户选择的记录 id 封装好再传参到后端即可。

- 控制层代码（注：完整代码位于 **com.site.blog.my.core.controller.admin.TagController.java**）：

接口的映射地址为 /tags/delete，请求方法为 POST，代码如下：

```java
    @PostMapping("/tags/delete")
    @ResponseBody
    public Result delete(@RequestBody Integer[] ids) {
        if (ids.length < 1) {
            return ResultGenerator.genFailResult("参数异常！");
        }
        if (tagService.deleteBatch(ids)) {
            return ResultGenerator.genSuccessResult();
        } else {
            return ResultGenerator.genFailResult("有关联数据请勿强行删除");
        }
    }
```

- 业务层代码（注：完整代码位于 **com.site.blog.my.core.service.impl.TagServiceImpl.java**）：

```java
    @Override
    public Boolean deleteBatch(Integer[] ids) {
        //已存在关联关系不删除
        List<Long> relations = relationMapper.selectDistinctTagIds(ids);
        if (!CollectionUtils.isEmpty(relations)) {
            return false;
        }
        //删除tag
        return blogTagMapper.deleteBatch(ids) > 0;
    }
```

在业务方法实现中，我们需要判断该标签是否已经与文章表中的数据进行了关联，如果已经存在关联关系，就不进行删除操作，这是其中的一种处理方式，因为在添加文章数据时，也会对应的向数据库中新增标签数据和关系数据（后面的实验中会介绍这些内容），因此在数据删除时需要进行确认以免造成数据混乱，当然也可以使用另外一种处理方法，就是在删除标签记录时，将标签记录以及对应的关系表中所有与此标签有关联的记录删除掉，这样也是可以的，本系统选择的是第一种方式。

- BlogTagMapper.xml （注：完整代码位于 **resources/mapper/BlogTagMapper.xml**）：

```xml
    <update id="deleteBatch">
        update tb_blog_tag
        set is_deleted=1 where tag_id in
        <foreach item="id" collection="array" open="(" separator="," close=")">
            #{id}
        </foreach>
    </update>
```

- BlogTagRelationMapper.xml（注：完整代码位于 **resources/mapper/BlogTagRelationMapper.xml**）：

```sql
    <select id="selectDistinctTagIds" resultType="java.lang.Long">
        select
        DISTINCT(tag_id)
        from tb_blog_tag_relation
        where tag_id in
        <foreach item="id" collection="array" open="(" separator="," close=")">
            #{id}
        </foreach>
    </select>
```

接口的请求路径为 /tags/delete，并使用 @RequestBody 将前端传过来的参数封装为 id 数组，参数验证通过后则调用 deleteBatch() 批量删除方法进行数据库操作，否则将向前端返回错误信息。



## 前端页面实现

#### tag.html

在 resources/templates/admin 目录下新建 tag.html，并引入对应的 js 文件和 css 样式文件。

#### 功能按钮及信息编辑框

标签管理模块我们也设计了常用的几个功能：标签信息增加、标签信息删除，因此在页面中添加对应的功能按钮以及触发事件，由于标签信息的字段并不多，因此在按钮区新增了一个标签名称的输入框，代码如下：(**注：完整代码位于 resources/templates/admin/tag.html**)

```html
                        <div class="grid-btn">
                            <input type="text" class="form-control col-1" id="tagName" name="tagName"
                                   placeholder="标签名称" required="true">&nbsp;&nbsp;&nbsp;
                            <button class="btn btn-info" onclick="tagAdd()"><i
                                    class="fa fa-plus"></i>&nbsp;新增
                            </button>
                            <button class="btn btn-danger" onclick="deleteTag()"><i
                                    class="fa fa-trash-o"></i>&nbsp;删除
                            </button>
                        </div>
```

分别是添加按钮，对应的触发事件是 tagAdd() 方法，删除按钮，对应的触发事件是 deleteTag() 方法，这些方法我们将在后续的代码实现中给出。

#### 分页信息展示区域

页面中已经引入 JqGrid 的相关静态资源文件，需要在页面中展示分页数据的区域增加如下代码：

```html
<table id="jqGrid" class="table table-bordered"></table>
<div id="jqGridPager"></div>
```

#### 页面效果

那么最终的静态页面效果展示如下图所示：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm68)

包括功能按钮、数据列表展示区域以及翻页功能区域，此时只是静态效果展示，并没有与后端进行数据交互，接下来我们将结合 Ajax 和后端接口实现具体的功能。



## 标签管理模块前端功能实现

完成了页面展示的实现，接着完成相关的功能实现。请大家一定自行对照源码完成代码编写。

### 分页功能

在 resources/static/admin/dist/js 目录下新增 tag.js 文件，并添加如下代码：

```js
$(function () {
    $("#jqGrid").jqGrid({
        url: '/admin/tags/list',
        datatype: "json",
        colModel: [
            {label: 'id', name: 'tagId', index: 'tagId', width: 50, key: true, hidden: true},
            {label: '标签名称', name: 'tagName', index: 'tagName', width: 240},
            {label: '添加时间', name: 'createTime', index: 'createTime', width: 120}
        ],
        height: 560,
        rowNum: 10,
        rowList: [10, 20, 50],
        styleUI: 'Bootstrap',
        loadtext: '信息读取中...',
        rownumbers: false,
        rownumWidth: 20,
        autowidth: true,
        multiselect: true,
        pager: "#jqGridPager",
        jsonReader: {
            root: "data.list",
            page: "data.currPage",
            total: "data.totalPage",
            records: "data.totalCount"
        },
        prmNames: {
            page: "page",
            rows: "limit",
            order: "order",
        },
        gridComplete: function () {
            //隐藏grid底部滚动条
            $("#jqGrid").closest(".ui-jqgrid-bdiv").css({"overflow-x": "hidden"});
        }
    });
    $(window).resize(function () {
        $("#jqGrid").setGridWidth($(".card-body").width());
    });
});
```

以上代码的主要功能为分页数据展示、字段格式化 jqGrid DOM 宽度的自适应，在页面加载时，调用 JqGrid 的初始化方法，将页面中 id 为 jqGrid 的 DOM 渲染为分页表格，并向后端发送请求，之后按照后端返回的 json 数据填充表格以及表格下方的分页按钮，可以参考第 15 个实验课程中 jqgrid 分页功能整合进行知识的理解，之后可以重启项目验证一下标签信息分页功能是否正常。



### 添加功能

在标签名称输入框中输入完成后可以点击右侧的**添加**按钮，此时会触发 `tagAdd()` 方法进行数据的交互，js 实现代码如下：(**注：完整代码位于 resources/static/admin/dist/js/tag.js**)

```js
function tagAdd() {
    var tagName = $("#tagName").val();
    if (!validCN_ENString2_18(tagName)) {
        swal("标签名称不规范", {
            icon: "error",
        });
    } else {
        var url = '/admin/tags/save?tagName=' + tagName;
        $.ajax({
            type: 'POST',//方法类型
            url: url,
            success: function (result) {
                if (result.resultCode == 200) {
                    $("#tagName").val('')
                    swal("保存成功", {
                        icon: "success",
                    });
                    reload();
                }
                else {
                    $("#tagName").val('')
                    swal(result.message, {
                        icon: "error",
                    });
                }
                ;
            },
            error: function () {
                swal("操作失败", {
                    icon: "error",
                });
            }
        });
    }
}
```

按钮点击后会触发对应的 js 方法，在该方法中首先会对用户输入的数据进行简单的正则验证，之后会封装数据并向对应的后端接口发送 Ajax 请求添加标签数据，之后根据后端返回的结果进行提示。



### 删除功能

删除按钮的点击触发事件为 `deleteTag()`，在 tag.js 文件中新增如下代码：

```js
function deleteTag() {
    var ids = getSelectedRows();
    if (ids == null) {
        return;
    }
    swal({
        title: "确认弹框",
        text: "确认要删除数据吗?",
        icon: "warning",
        buttons: true,
        dangerMode: true,
    }).then((flag) => {
        if (flag) {
            $.ajax({
                type: "POST",
                url: "/admin/tags/delete",
                contentType: "application/json",
                data: JSON.stringify(ids),
                success: function (r) {
                    if (r.resultCode == 200) {
                        swal("删除成功", {
                            icon: "success",
                        });
                        $("#jqGrid").trigger("reloadGrid");
                    } else {
                        swal(r.message, {
                            icon: "error",
                        });
                    }
                }
            });
        }
    }
);
}
```

获取用户在 jqgrid 表格中选择的需要删除的所有记录的 id，之后将参数封装并向后端发送 Ajax 请求，请求地址为 tags/delete，删除功能已经开发完成，同学们可以结合源码以及实际的功能进行学习和理解，接下来我们来进行功能测试。



## 实验楼 web ide 中操作 Spring Boot 项目

### 启动 MySQL 并新增 tb_blog_tag 表和 tb_blog_tag_relation 表

详细步骤可以参考实验 15 中的内容，这里就不再浪费篇幅去介绍了。



### 启动项目

由于默认是在 project 目录下，因此想要启动咱们的项目，首先需要切换到已解压的 My-Blog 目录下，之后可以通过 Maven 插件的方式启动 Spring Boot 项目，命令为 mvn spring-boot:run ，之后就可以等待项目启动。

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm69)



### 打开 Web 服务

在项目启动成功后，可以点击页面上方的 Web 服务直接在显示查看网站效果，之后会在浏览器中弹出 `https://********.simplelab.cn` 访问页面：`https://********.simplelab.cn/admin/login` 登陆至后台管理首页，之后点击标签管理就可以对相关功能进行测试了，演示过程如下：

- 添加功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm70)

- 删除功能：

![图片描述](C:\WJJ\StudyInUSC\spring踩坑与学习\My-Blog-imgaes\wm71)

同学们可以按照文中的思路和过程自行测试，本次实验完成！













# 最有用的部分

如果有多个参数可以自行拼装字符串，或者使用逗号进行分隔，写法如下：

```html
<a th:href="@{/blog/{blogId}(blogId=${blog.blogId},title=${blog.blogTitle},tag='java')}">详情页</a>
```



必须引入名称空间

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```



Thymeleaf 存设置为 false，在配置文件中修改如下：

```properties
spring.thymeleaf.cache=false
```