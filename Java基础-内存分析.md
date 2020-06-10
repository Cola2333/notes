Java虚拟机的内存可以分为三个区域：栈stack、堆heap、方法区method area。

**栈的特点如下：**

　　1. 栈描述的是方法执行的内存模型。每个方法被调用都会在栈内创建一个**栈帧**(存储局部变量、操作数、方法出口等)

　　2. JVM为每个线程创建一个栈，用于存放该线程执行方法的信息(实际参数、局部变量等)

　　3. 栈属于线程私有，不能实现线程间的共享!

　　4. 栈的存储特性是“先进后出，后进先出”

　　5. 栈是由系统自动分配，速度快!栈是一个连续的内存空间!

**堆的特点如下：**

　　1. 堆用于存储创建好的对象和数组(数组也是对象)

　　2. JVM只有一个堆，被所有线程共享

　　3. 堆是一个不连续的内存空间，分配灵活，速度慢!

**方法区(又叫静态区)特点如下：**

　　1. JVM只有一个方法区，被所有线程共享!

　　2. 方法区实际**也是堆**，只是用于存储类、常量相关的信息!

　　3. 用来存放程序中永远是不变或唯一的内容。(类信息【Class对象】、静态变量、常量、常量池)



eg.

```java
public static void main(String[] args) {
    SxtStu stu = new SxtStu();
    stu.id = 1001;
    stu.sname = "高琦";
    stu.age = 18;
    
    Computer c1 = new Computer();
    c1.brand = "联想";
    
    stu.comp = c1;
    
    stu.play();
    stu.study();
}

Class Computer {
    String brand;
}

```

![image-20200527194650546](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\image-20200527194650546.png)

首先加载主方法所在的类 加载代码、静态变量、静态方法、字符串常量

找到main方法 创建栈帧

main方法里创建了对象stu stu = null 

然后创建新的栈帧 调用stu的构造器 然后 要去堆中new一个SxtStu出来（未赋值） 创建完成之后将其赋值给栈帧里的stu（可以看做是地址）并删除该栈帧

赋值

main方法里又创建了对象computer = null

创建新的栈帧，调用computer的构造器 去堆中new一个Computer 创建完成之后将其赋值给栈帧里的computer 并删除该栈帧







![1.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20170516/1494925174358420.png)

图4-4 示例4-3内存分配图

![neicunfenxi.png](https://www.sxt.cn/360shop/Public/admin/UEditor/20171026/1509008324820095.png)