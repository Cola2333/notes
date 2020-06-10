## 关于Mapper.xml文件

```xml
<resultMap id="  " type="  "></resutlMap>   
// id=" " 表示这个映射管理器的唯一标识，外部通过该值引用； type = " " 表示需要映射的实体类；
```

```xml
<resultMap extends=" " id=" " type=" "></resultMap>
// 继承
```

```xml
<id column = " " property= " " />    
// <id>标签指的是：结果集中结果唯一的列【column】 和 实体（类）属性【property】的映射关系，注意：<id>标签管理的列未必是主键列，需要根据具体需求指定；
```

```xml
<result column= " " property=" " />  
// <result>标签指的是：结果集中普通列【column】 和 实体（类）属性【property】的映射关系；
```

```xml
<select id=" " parameterType=" " resultMap=" "></select>

id="xxxx" 表示此段sql执行语句的唯一标识，也是接口的方法名称【必须一致才能找到】

parameterType="" 表示该sql语句中需要传入的参数，类型要与对应的接口方法的类型一致【可选】

resultMap=“ ” 定义出参，调用已定义的<resultMap>映射管理器的id值

resultType=“ ” 定义出参，匹配普通java类型或自定义的pojo【出参类型若不指定，将为语句类型默认类型，如<insert>语句返回值为int】

p.s： 至于为何<insert><delete><update> 语句的返回值类型为什么是int，有过JDBC操作经验的朋友可能会有印象，增删改操作实际上返回的是操作的条数。而Mybatis框架本身是基于JDBC的，所以此处也沿袭这种返回值类型。

传参和取值：mapper.xml 的灵活性还体现在SQL执行语句可以传参，参数类型通过parameterType= “” 定义

取值方式1：#{value jdbcType = valuetype}：jdbcType 表示该属性的数据类型在数据库中对应的类型，如 #{user jdbcType=varchar} 等价于 String username；

取值方式2：${value } : 这种方式不建议大量使用，可能会发送sql注入而导致安全性问题。一般该取值方式可用在非经常变化的值上，如orderby ${columnName}；
```

