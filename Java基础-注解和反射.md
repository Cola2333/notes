# 注解

## 注解
可以被程序识别的注释

### 元注解

```java
//测试原注解
@MyAnnotation
public class Test01 {

    public void test() {
    
    }

}

//定义一个注解
//Target 表示我们的注解可以用在哪些地方 TYPE表示类
@Target(value = {ElementType.METHOD,ElementType.TYPE})

//Retention 表示我们的注解在什么地方有效   runtime>class>source
@Retention(value = RetentionPolicy.RUNTIME)

//Documented 表示是否将我们的注解生成在JAVAdoc中
@Documented

//Inherited  子类可以继承父类的注解
@Inherited
@interface MyAnnotation{

}
```



### 内置注解
```java
//内置注解
public class Test03 {
@Override  //重写的注解

    public String toString() {
        return "Test03{}";
    }

    @Deprecated  //不推荐使用，存在更好的
    public static void test() {
        System.out.println("Deprecated");
    }

    @SuppressWarnings("all")  //镇压警告
    public void test2() {
        ArrayList arrayList = new ArrayList();
    }

    public static void main(String[] args) {
        test();
    }
}
```



### 定义注解
```java
//自定义注解
public class Test02 {
    //注解可以显示赋值，如果没有默认值，我们就必须给注解赋值
    @MyAnnotation2(name = "陈平安",schools = "清华")
    public void test() {
	}

    @My3("宁姚") //只有当参数名为value时才可以这样赋值 否则要写成value = " 	"
    public void test2() {

    }
}

@Target({ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface  MyAnnotation2{
    //注解的参数：参数类型+参数名
    String name() default "";
    int age() default 0;
    int id() default -1;   //如果默认值为-1.则不存在
    String[] schools() default {"清华","北大"};
}

@Target({ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface My3{
    String value();
}

```



## 反射

###  什么是反射
Java c c++是静态语言，Java可以通过反射获得类似动态语言的特性。（准动态语言）

动态语言是指在运行阶段可以改变其结构 Python JavaScript C#都是动态语言

反射 优点：

可以动态创建对象和编译，灵活

反射 缺点：

对象性能有影响，总是慢于直接执行相同的操作



### 通过反射创建对象

```java
public static void main(String[] args) throws ClassNotFoundException {
    //通过反射获取类的class对象
    Class c1 = Class.forName("com.lcy.reflection.User");
    System.out.println(c1);
    Class c2 = Class.forName("com.lcy.reflection.User");
    Class c3 = Class.forName("com.lcy.reflection.User");
    Class c4 = Class.forName("com.lcy.reflection.User");

    //一个类在内存中只有一个Class对象 hashcode相同
    //一个类被加载后，类的整个结构都会被封装在Class对象中
    System.out.println(c1.hashCode());
    System.out.println(c2.hashCode());
    System.out.println(c3.hashCode());
    System.out.println(c4.hashCode());
}
```


### Class类

class本身也是一个类
只能由系统建立对象
**一个加载的类在JVM中只会有一个Class实例**
一个Class实例对应的是一个加载到JVM中的一个.class文件
每个类的实例都会记得自己是由哪个class实例生成的
通过Class可以完整的得到一个类所有被加载的结构
Class类是Reflection反射的根源，针对任何想动态加载，运行的类，唯有先获得相应的Class对象



### 多种反射方式创建类

```java
//测试Class类的创建方式有哪些
public class Test02 {
    public static void main(String[] args) throws ClassNotFoundException {
        Person person = new Stu();
        System.out.println("这个人是:"+person.name);

        //1.通过对象获得
        Class c1 = person.getClass();
        System.out.println(c1.hashCode());

        //2.forName获得
        Class c2 = Class.forName("com.lcy.reflection.Stu");
        System.out.println(c2.hashCode());

        //3.通过类名.class获得
        Class c3 = Stu.class;
        System.out.println(c3.hashCode());

        //4. 基本内置类型的包装类都有一个Type属性
        Class c4 = Integer.TYPE;
        System.out.println(c4.hashCode());

        //获得父类类型
        Class c5 = c1.getSuperclass();
        System.out.println(c5);
    }
}

class Person{
    String name;

    public Person() {
    }

    public Person(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                '}';
    }
}

class Stu extends Person{
    public Stu() {
        this.name = "学生";
    }

}

class Teacher extends Person{
    public Teacher() {
        this.name = "老师";
    }
}

```



### 所有类型的Class

```java
//测试哪些类型有Class
public class Test04 {

    public static void main(String[] args) {
        Class c1 = Object.class;  //类
        Class c2 = Comparable.class;  //接口
        Class c3 = String[].class;   //一维数组
        Class c4 = int[][].class;   //二维数组
        Class c5 = Override.class;  //注解
        Class c6 = ElementType.class;  //枚举
        Class c7 = Integer.class;   //基本数据类型
        Class c8 = void.class;  //void
        Class c9 = Class.class;  //Class

        System.out.println(c1);
        System.out.println(c2);
        System.out.println(c3);
        System.out.println(c4);
        System.out.println(c5);
        System.out.println(c6);
        System.out.println(c7);
        System.out.println(c8);
        System.out.println(c9);

        //只要元素类型与维度一样，就是同一个Class
        int[] a = new int[10];
        int[] b = new int[100];
        System.out.println(a.getClass().hashCode());
        System.out.println(b.getClass().hashCode());
    }
}

```



### 类加载内存分析

![image-20200527104822823](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527104822823.png)

![image-20200527104909148](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527104909148.png)

![image-20200527111441445](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527111441445.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200428220835671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1R4YkxjeQ==,size_16,color_FFFFFF,t_70)

```java
public class Test05 {

    public static void main(String[] args) {
        A a = new A();
        System.out.println(A.m);

        /**
         * 1.加载到内存，会产生一个类对应的Class对象
         * 2.链接，链接结束后m=0  链接：把Java的二进制代码合并到JVM运行状态之中的过程
         * 3.初始化
         * <clinit>(){
         *      System.out.println("A类静态代码块初始化");
         *      m = 300;
         *      m = 100;
         * }
         * m = 100
         */
    }

}

class A {
    static {
        System.out.println("A类静态代码块初始化");
        m = 300;
    }

    static int m = 100;

    public A() {
        System.out.println("A类的无参构造初始化");
    }
}

```



### 分析类的初始化

![image-20200527105915714](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527105915714.png)

```java
//测试类什么时候会初始化
public class Test06 {

    static {
        System.out.println("Main类被加载");
    }

    public static void main(String[] args) throws ClassNotFoundException {
        //1.主动引用  父类之前没被引用，会先加载它的父类 再加载自己
//        Son son = new Son();

        //2.反射也会产生主动引用
//        Class.forName("com.lcy.reflection.Son");

        //3.不会产生类的引用
//        System.out.println(Son.b); //子类引用父类的静态方法或者变量，只会加载父类并不会让子类加载

//        Son[] sons = new Son[8];   //这是一个名字和一片空间而已，并不会加载类

//        System.out.println(Son.M);  //常量并不会引起父类和子类的初始化 常量在链接阶段就存入常量池中了

    }

}

class Father {

    static int b = 2;

    static {
        System.out.println("父类被加载");
    }
}

class Son extends Father {

    static {
        System.out.println("子类被加载");
        m = 300;
    }

    static int m = 100;
    static final int M = 1;
}

```



### 类的加载器

![image-20200527111620537](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527111620537.png)

![image-20200527111544077](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527111544077.png)

```java
public class Test07 {

    public static void main(String[] args) throws ClassNotFoundException {
        //获取系统类加载器  sun.misc.Launcher$AppClassLoader@18b4aac2
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);

        //获取系统l类加载器的父类加载器   sun.misc.Launcher$ExtClassLoader@1b6d3586
        ClassLoader parent = systemClassLoader.getParent();
        System.out.println(parent);

        //获取扩展类加载器的父类加载器---->根加载器（C/C++） Java获取不到，返回null
        ClassLoader parent1 = parent.getParent();
        System.out.println(parent1);

        //测试当前类是哪个类加载器的   sun.misc.Launcher$AppClassLoader@18b4aac2
        ClassLoader classLoader = Class.forName("com.lcy.reflection.Test07").getClassLoader();
        System.out.println(classLoader);

        //测试JDK内置的类是谁加载的    null
        classLoader = Class.forName("java.lang.Object").getClassLoader();
        System.out.println(classLoader);

        //如何获得系统类加载器可以加载的路径 如果类不在这些地方则读取不到
        System.out.println(System.getProperty("java.class.path"));
        
        //双亲委派机制：多重检测，保证安全性
        // 比如自己定义一个java.lang.String类,要去加载,它先看用户类加载器有没有--->扩展类加载器--->根加载器
        //如果有 则自己写的就无效

        /**
         D:\work\java\jdk1.8\jre\lib\charsets.jar;
         D:\work\java\jdk1.8\jre\lib\deploy.jar;
         D:\work\java\jdk1.8\jre\lib\ext\access-bridge-64.jar;
         :\work\java\jdk1.8\jre\lib\ext\cldrdata.jar;
         D:\work\java\jdk1.8\jre\lib\ext\dnsns.jar;
         D:\work\java\jdk1.8\jre\lib\ext\jaccess.jar;
         D:\work\java\jdk1.8\jre\lib\ext\jfxrt.jar;
         D:\work\java\jdk1.8\jre\lib\ext\localedata.jar;
         D:\work\java\jdk1.8\jre\lib\ext\nashorn.jar;
         D:\work\java\jdk1.8\jre\lib\ext\sunec.jar;
         D:\work\java\jdk1.8\jre\lib\ext\sunjce_provider.jar;
         D:\work\java\jdk1.8\jre\lib\ext\sunmscapi.jar;
         D:\work\java\jdk1.8\jre\lib\ext\sunpkcs11.jar;
         D:\work\java\jdk1.8\jre\lib\ext\zipfs.jar;
         D:\work\java\jdk1.8\jre\lib\javaws.jar;
         D:\work\java\jdk1.8\jre\lib\jce.jar;
         D:\work\java\jdk1.8\jre\lib\jfr.jar;
         D:\work\java\jdk1.8\jre\lib\jfxswt.jar;
         D:\work\java\jdk1.8\jre\lib\jsse.jar;
         D:\work\java\jdk1.8\jre\lib\management-agent.jar;
         D:\work\java\jdk1.8\jre\lib\plugin.jar;
         D:\work\java\jdk1.8\jre\lib\resources.jar;
         D:\work\java\jdk1.8\jre\lib\rt.jar;
         E:\注解和反射\out\production\注解和反射;
         D:\work\idea\lib\idea_rt.jar
         */
    }

}

```

![img](https://img-blog.csdnimg.cn/20200428220801738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1R4YkxjeQ==,size_16,color_FFFFFF,t_70)



### 获取运行时类的完整结构

```java
//获得类的信息
public class Test08 {

    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException {
//        User user = new User();
//        Class c1 = user.getClass();

        Class c1 = Class.forName("com.lcy.reflection.User");

        //获取类的名字
        System.out.println(c1.getName());  //获得包名+类名
        System.out.println(c1.getSimpleName());  //获得类名

        //获得类的属性
        Field[] fields = c1.getFields();  //只能找到public属性

        fields = c1.getDeclaredFields();  //找到全部属性
        for (Field field :fields) {
            System.out.println(field);
        }

        //获得指定属性的指
        Field name = c1.getDeclaredField("name");
        System.out.println(name);

        //获取类的方法
        Method[] methods = c1.getMethods();  //获取本类及其父类全部的public方法
        for (Method method :methods) {
            System.out.println("正常的："+method);
        }
        methods = c1.getDeclaredMethods();  //获取本类的所有方法 
        for (Method method :methods) {
            System.out.println("getDeclareMethods:"+method);
        }

        //获得指定的方法 需要指定参数类型
        Method getName = c1.getMethod("getName", null);
        Method setName = c1.getMethod("setName", String.class);
        System.out.println(getName);
        System.out.println(setName);

        //获取构造器
        Constructor[] constructors = c1.getConstructors();
        for (Constructor constructor :constructors) {
            System.out.println("public的构造器"+constructor);
        }
        constructors = c1.getDeclaredConstructors();
        for (Constructor constructor :constructors) {
            System.out.println("全部构造器:"+constructor);
        }

        //获取指定的构造器
        Constructor declaredConstructor = c1.getDeclaredConstructor( int.class,String.class,String.class);
        System.out.println("指定构造器"+declaredConstructor);

    }

}

```



### 动态创建对象 执行方法 操作属性

```java
//动态的创建对象,通过反射
public class Test09 {

    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
        //获得Class对象
        Class c1 = Class.forName("com.lcy.reflection.User");

        //构造一个对象  此调用必须要有默认无参构造函数
        User user1 = (User) c1.newInstance();
        System.out.println(user1);  //调用默认构造方法

        //通过构造器创建对象
        Constructor constructor = c1.getDeclaredConstructor(int.class, String.class, String.class);
        User user2 = (User) constructor.newInstance(1, "陈平安", "剑气长城");
        System.out.println(user2);

        //通过反射调用普通方法
        User user3 = (User) c1.newInstance();
        //通过反射获取一个方法
        Method setName = c1.getDeclaredMethod("setName", String.class);
        //invoke :激活的意思   (对象，"方法的值")
        setName.invoke(user3,"陆沉");
        System.out.println(user3.getName());

        //通过反射操作属性
        User user4 = (User) c1.newInstance();
        Field name = c1.getDeclaredField("name");
        //不能直接操作私有属性，关闭安全检测，暴力反射可以提高效率
        name.setAccessible(true);
        name.set(user4,"宁姚");
        System.out.println(user4.getName());

    }

}

```

正常创建的效率 >> 关闭安全检测反射创建的效率 > 反射创建的效率



### 反射操作泛型

```java
/通过反射获取泛型
public class Test11 {

    public void test01(Map<String,User> map, List<User> list) {
        System.out.println("test01");
    }

    public Map<String,User> test02() {
        System.out.println("test02");
        return null;
    }


    public static void main(String[] args) throws NoSuchMethodException {
        /*
        #java.util.Map<java.lang.String, com.lcy.reflection.User>
        class java.lang.String
        class com.lcy.reflection.User
        #java.util.List<com.lcy.reflection.User>
        class com.lcy.reflection.User
        */
        //加载的方法和参数
        Method method = Test11.class.getMethod("test01", Map.class, List.class);
        //获得泛型的参数类型
        Type[] genericParameterTypes = method.getGenericParameterTypes();

        for (Type type :genericParameterTypes) {  //打印泛型
            System.out.println("#"+type);
            if (type instanceof ParameterizedType) {  //想知道里面的参数类型
                //强转获得真实的泛型参数信息
                Type[] typeArguments = ((ParameterizedType) type).getActualTypeArguments();
                for (Type temp :typeArguments) {
                    System.out.println(temp);
                }
            }
        }

        /*
        class java.lang.String
        class com.lcy.reflection.User
         */
        method = Test11.class.getMethod("test02",null);
        //获得返回值类型
        Type genericReturnType = method.getGenericReturnType();
        if (genericReturnType instanceof  ParameterizedType) {
            Type[] types = ((ParameterizedType) genericReturnType).getActualTypeArguments();
            for (Type temp :types) {
                System.out.println(temp);
            }
        }

    }
}
```



### 反射操作注解

```java
//反射操作注解
public class Test12 {

    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class c1 = Class.forName("com.lcy.reflection.Student");

        //通过反射获得类的注解
        Annotation[] annotations = c1.getAnnotations();
        for (Annotation annotation :annotations) {
            //找到了外面的注解@com.lcy.reflection.TableTemp(value=db_stu)
            System.out.println(annotation);
        }

        //获取类指定的注解
        //获得注解value的值  db_stu
        TableTemp tableTemp = (TableTemp) c1.getAnnotation(TableTemp.class);
        String value = tableTemp.value();
        System.out.println(value);

        /**
         * 获得类指定的注解
         * db_stu
         * db_name
         * String
         * 10
         */
        Field f = c1.getDeclaredField("name");  //name对应表中的字段
        FieldTemp annotation = f.getAnnotation(FieldTemp.class); //获取字段的注解
        System.out.println(annotation.columnName());
        System.out.println(annotation.type());
        System.out.println(annotation.length());

    }

}

@TableTemp("db_stu")
class Student{
    @FieldTemp(columnName = "db_id",type = "int",length = 10)
    private int id;
    @FieldTemp(columnName = "db_name",type = "String",length = 10)
    private String name;
    @FieldTemp(columnName = "db_age",type = "varchar",length = 5)
    private int age;

    public Student() {
    }

    public Student(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
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

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

//类名的注解
@Target(ElementType.TYPE)   //设置作用域
@Retention(RetentionPolicy.RUNTIME)  //设置什么级别可以获取
@interface TableTemp{
    String value();
}

//属性的注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface FieldTemp{
    String columnName();  //列名的注解
    String type();      //类型
    int length();       //长度
}
 

```

