## 编译过程

-javac 编译成class 

-java 运行虚拟机



## JVM的位置

![image-20200527141259790](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527141259790.png)

## JVM体系

线程私有：本地方法栈 程序计数器 操作方法栈

![image-20200527152918448](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527152918448.png)



## 程序计数器

每个线程都有自己的程序计数器这样当线程执行切换的时候就可以在上次执行的基础上继续执行，仅仅从一条线程线性执行的角度而言，代码是一条一条的往下执行的，这个时候就是程序计数器；JVM就是通过读取程序计数器的值来决定下一条需要执行的字节码指令，进而进行选择语句、循环、异常处理等；



## 类加载器

![image-20200528203639597](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200528203639597.png)

虚拟机自带的加载器 启动类加载器（BootstrapClassLoader） 扩展加载器（Ext）应用程序加载器（App）自定义加载器	

1. 类加载去收到类加载的请求
2. 将这个请求向上委托给父类加载器去完成，一直向上委托直到启动类加载器
3. 启动类加载器检查是否能够加载当前这个类，能加载就结束，使用当前的加载器。否则抛出异常，通知子加载器进行加载
4. 重复步骤3



## 类加载过程

链接（校验、准备、解析）初始化，使用、卸载



## 双亲委派机制

当某个类加载器需要加载某个`.class`文件时，它首先把这个任务委托给他的上级类加载器，递归这个操作，如果上级的类加载器没有加载，自己才会去加载这个类。

防止重复加载同一个`.class`。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
保证核心`.class`不能被篡改。通过委托方式，不会去篡改核心`.clas`，即使篡改也不会去加载，即使加载也不会是同一个`.class`对象了。不同的加载器加载同一个`.class`也不是同一个`Class`对象。这样保证了`Class`执行安全。



## 沙箱安全机制



## native关键字

凡是带了native关键字，则表示java的作用范围达不到了，需要回去调用C的库，会进入本地方法栈，调用本地接口JNI.



JNI（Java Native Inteface）的作用：扩展Java应用，融合不同的语言为Java所用 最初是为了融合C C++

在内存中专门开辟出一片区域：naive method stack 登记native方法（不执行）

在最终执行的时候，通过JNI加载本地方法库中的方法



## Java创建对象的过程

一个对象的创建过程包含两个过程：**初始化**和**实例化**

我们在使用一个对象时，JVM首先会检查相关类型是否已经加载并初始化，如果没有，则JVM立即进行加载并调用类构造器（ClassLoader）完成类的初始化。在类初始化过程中或初始化完毕后，根据具体情况才会去对类进行实例化。

实例化时候，java虚拟机就会为其分配内存来存放自己及其从父类继承过来的实例变量。在为这些实例变量分配内存的同时，这些实例变量先会被赋予默认值(零值)。在内存分配完成之后，Java虚拟机才会对新创建的对象赋予我们程序员给定的值。

### 初始化

![img](https://pics6.baidu.com/feed/b21bb051f819861894e09f4b5c15217689d4e653.jpeg?token=596cceeaacc2d83077533cab6085ca7e&s=5EA83C6389F94C0B1C7D90CB0000E0B1)

### 实例化

使用new关键字

Class对象的newInstance()方法

构造函数对象的newInstance()方法对象反序列化Object

对象的clone()方法

使用Unsafe类创建对象



### 在内存中的过程

加载过程

![img](https://upload-images.jianshu.io/upload_images/1447393-77fe1d81f5fb911a.png?imageMogr2/auto-orient/strip|imageView2/2/w/665/format/webp)

首先写一个例子

```java
public class Person{
    int age;
    int name;
    public void walk(){
        System.out.println("i am walking")
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Person person = new Person;
        person.age = 18;
        person.name = "wjj";
       	person.walk;
    }
}
```

第一步，JVM去方法区寻找Test类的代码信息，如果有直接调用，没有的话使用类的加载机制（ClassLoader）把类加载进来。同时把静态变量、静态方法、常量加载进来。这里加载的是（“wjj”）；这是因为字符串是常量，age中的18是基本类型。

第二步，jvm进入main方法，看到Person person=new Person()。首先分析Person这个类，同样的寻找Person类的代码信息，有就加载，没有的话类加载机制加载进来。同时也加载静态变量、静态方法、常量（“I am walking”）

第三步，jvm接下来看到了person，person在main方法内部，因而是局部变量，存放在栈空间中，初始值为null。

第四步，jvm接下来看到了new Person()。new出的对象（实例），存放在堆空间中，属性赋值为默认值（零）。

第五步，jvm接下来看到了“=”，把new Person的地址告诉person变量，person通过四字节的地址（十六进制），引用该实例。 

第六步，jvm看到person.name = “wjj”，person通过引用new Person实例的name属性，该name属性通过地址指向常量池的"wjj"。

第七步，jvm看到person.age = 18; person的age属性是基本数据类型，直接赋值。

第八步，jvm看到person.walk(); 调用实例的方法时，并不会在实例对象中生成一个新的方法，而是通过地址指向方法区中类信息的方法。



## 堆

![image-20200529091940784](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200529091940784.png)

GC主要发生在伊甸园区和养老区

JDK8以后 永久存储区改名为元空间 **元空间在逻辑上存在 物理上不存在**

![image-20200529100050294](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200529100050294.png)

**新生区 **

类诞生、成长甚至死亡的地方 如果新生区不够大 新创建的对象也可能直接进入老年区

+ 伊甸园区：所有对象都是在伊甸园区new出来的

**老年区**

新生区中的类在经历过GC后 如果存活下来 会进入老年区

**永久存储区**

这个区域在内存中常驻，用来存放JDK自身携带的Class对象，Interface元数据。存储Java运行时的一些环境或类信息（也就是方法区）。这个区域不存在垃圾回收，只有在关闭虚拟机的适合才会释放这个区域的内存。

以下情况会导致永久存储区满：

1. 如果一个启动类加入了大量第三方jar包

2. Tomcat部署太多应用
3. 大量动态生成反射类



**堆内存调优**

默认情况下：分配的总内存是电脑内存的1/4，初始化的内存是电脑内存的1/64

如果堆内存满了出现OOM，可以自行调整堆内存的大小

1. 尝试扩大堆内存 看结果

   ```bash
   //将总内存和初始内存都设置为1024m 并打印GC
   -Xms1024m -Xmx1024m -XX:+PrintGCDetails
   ```

2. 根据打印出来的信息可以发现 新生代+老年代的内存就等于总内存了 所以说元空间在物理上是不存在的

   ```java
   package com.usc;
   
   //-Xms8m -Xmx8m -XX:+PrintGCDetails
   public class Test {
       public static void main(String[] args) {
           String str = "nishuoshenmewotingbujian";
           while(true) {
               str = str + str;	 
           }
       }
   }
   
   ```

   ![image-20200529101413832](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200529101413832.png)

在一个项目中出现了OOM错误

+ 能够看到第几行代出错：内存快照分析工具 JProfile , MAT
+ Debug 一行行分析

JProfile，MAT的作用

1. 分析Dump内存文件，快速定位内存泄露
2. 获得堆中数据
3. 获得大的对象



## Jprofile的使用 

修改VM

```bash
-Xms1024m -Xmx1024m -XX:+HeapDumpOnOutOfMemoryError
```

找到生成的文件 打开

查看Thread级知是哪个地方出了问题



## GC

+ 作用区域：堆和方法区

+ 组成：Eden区、survivor from、survivor to、老年代
+ 比例：年轻代的比例是 Eden 8 : survivor from 1 : survivor to 1、 

+ 类型：MinorGC、FullGC

  + 新生代 GC（Minor GC）：指发生在新生代的垃圾收集动作，因为 Java 对象大多都具
    备朝生夕灭的特性，所以 Minor GC 非常频繁，一般回收速度也比较快。

  + 老年代 GC（Major GC  / Full GC）：指发生在老年代的 GC，出现了 Major GC，经常
    会伴随至少一次的 Minor GC（但非绝对的，在 ParallelScavenge 收集器的收集策略里
    就有直接进行 Major GC 的策略选择过程） 。MajorGC 的速度一般会比 Minor GC 慢 10
    倍以上。

  + Minor GC触发机制：
    当年轻代满时就会触发Minor GC，这里的年轻代满指的是Eden代满，Survivor满不会引发GC

  + Full GC触发机制：
    当年老代满时会引发Full GC，Full GC将会同时回收年轻代、年老代，

    当永久代满时也会引发Full GC，会导致Class、Method元信息的卸载

+ 垃圾回收器：

  ![img](https://img-blog.csdn.net/20180519164557524)

+ 算法：

  + **引用计数法**

    每一个对象都会有一个计数器 记录其被引用的次数 如果被引用次数为0 则进行垃圾回首

    缺点：计数器本身也会消耗内存 而且其并不高效 Java中几乎没有使用该算法

  + **复制算法**

    内存中有survior from和 survior to两个区。当执行GC的时候，Eden区和Survivor From区中存活下来的对象会进入Survivor To区，此时Eden和Survivor From区都为空，Survivor To区中储存存活的元素。然后此时之前的Survivor From区变为To区，Survivor To区变为From区（可以理解成为空的区是To区）。如此进行循环。如果新生区中的对象经历了15次GC后还能存活，则会进入老年区。对象晋升老年代的年龄阈值，可以通过参数``` -XX:MaxTenuringThreshold``` (阈值)来设置

    优点：没有内存的碎片

    缺点：浪费了内存，因为总有一个区为空

    ​			极端情况下，如对象永远存活 那门会不停的被转移区，重新给地址，开销巨大

    所以，对象存活时间短的时候适合用复制算法，也就是在新生区使用复制算法

  + **标记清除法**

    扫描所有对象并对使用的对象进行标记，当进行GC的时候，扫描全部对象，将未标记的对象清楚

    优点：不需要额外空间

    缺点：两次扫描，浪费时间

    ​			会有内存碎片

  + **标记压缩**

    扫描所有对象并对使用的对象进行标记，然后再次扫描，将标记的对象移向同一端

    优点：不产生碎片

    确定：多了移动成本

  + **标记清除压缩**

    先进行多次标记清除，再进行标记压缩

  + 总结

    时间复杂度：复制 > 标记清除 > 标记压缩

    内存整齐度：复制 = 标记压缩 > 标记清除

    内存利用率：标记压缩 = 标记清除 > 复制

​			   

​               没有最优的算法，只有最合适的算法 分代清除

​			   年轻代 -> 存活时间段 -> 复制算法

​			   老年代 -> 存活时间长 -> 标记清除（内存碎片不是太多）+标记压缩



## JMM

Java Memory Model，用来缓存一致性协议，用于定义数据读写规则

JMM定义了线程工作内存和主内存之间的抽象关系：线程之间的共享变量储存在主内存（Main Memory），每个线程还有一个自己的工作内存（Local Memory）。如果线程把共享的变量读取到了自己的工作内存，进行了修改，但主内存并不知道这次修改（不可见的），那么当其他线程再去读取这个变量的时候，就会出现一致性问题。JMM就是为了解决共享对象可见性的问题

![image-20200529150735090](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200529150735090.png)

Valatile关键字

​			









