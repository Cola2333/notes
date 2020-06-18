**相同点**:
这两个都是用来处理前端传递过来的请求参数
**不同点**:
不同的是RequestParam处理的是请求参数，而PathVariable处理的是路径变量,
这样说更容易理解，RequestParam是将对应请求路径下的请求参数值映射到处理器参数上
而PathVariable是将请求路径变量的值映射到处理器参数上
直接上例子



**@RequestMapping请求参数**

http://localhost/mdeditor/chen?id=39

```java
//将请求参数映射到处理器参数上
@RequestMapping("/mededitor/chen")
public String getId(@RequestParam( value="id",requested=false)String Pid){
return id;
}
//会输出39
```

**@PathVariable路径变量**

http://localhost/mdeditor/chen

```java

//将请求的路径变量，映射到处理器参数中
@RequestMapping("/mededitor/{page}")
public String getId(@PathVariable( value="page",requested=false)String Page){
return page;
}
//会输出chen
```



在特定情况下（）里的属性可以删除或增加

一@PathVariable为例，若路径变量名和处理器参数名相同，则可省略value

```java
http://localhost/mdeditor/cc
@RequestMapping("/mededitor/{page}")
public String getId(@PathVariable String page){
return page;
}
//输出cc

```

