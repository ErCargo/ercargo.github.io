---
layout: post
title: Java 类加载之 ClassLoader
date: 2019-03-31 23:13
---

- JVM 的组成部分
- 什么是 ClassLoader 


## JVM 组成部分

1. Class Loader: 作用: 加载编译后的 class 文件到内存
2. RunTime Data Area: JVM 内存模型
3. Execution Engine: 解析 class 文件中的字节码， 解析后提交到操作系统中执行
4. Native Interface: 本地接口，融合不同的编程语言的原生库为 Java 所用

![Java 虚拟机](/assets/images/javavm.jpg)

Q: JVM 如何加载 .class 文件

    A: JVM 由 4个部分组成，包括 ClassLoader, Runtime Data Area, Execution Engine 和 Native Interface；
    主要通过 ClassLoader 将符合虚拟机规范的 class 文件加载到内存中， 再通过 Execution Engine 解析 Class 文件中的字节码，
    提交给操作系统执行

思考：类从编译到执行的过程
      
 ![Java 运行示意图(摘自其他网站)](/assets/images/javac.jpg)
      
      *.java file  --> Java Compiler(javac) --> *.class file --> Java Virtual Machine
      
      Java Virtual Machine: 
       - ClassLoader
       - ByteCode Verifier
       - Java Runtime System
       - Native OS

 
## 什么是 ClassLoader 
 
  > ClassLoader 工作在 Class 装载的加载阶段，其主要作用是从系统外部获得 Class 二进制数据流，所有的 Class 都是由 ClassLoader 进行加载的， 
  > ClassLoader 负责通过将 Class 文件中的二进制数据流装载进系统， 然后交给 Java 虚拟机进行连接，初始化等操作

### ClassLoader 的类别

  1. BoostrapClassLoader: 加载核心库 Java.*  
     
     (主要作用是负责加载 Java 自带的核心类，e.g: java.lang.xx 中的类都是由 BootstrapClassLoader 负责加载，通常这些核心类都是被签名的， 不能被替换， 是由 JVM 内核实现的， 目前在 HotspotVM 中是由 C++ 来实现，有了它加载最核心的内容， 才会有后面的 ClassLoader 存在)
     
  2. ExtClassLoader (extends URLClassLoader, 获取 class 文件的路径： "java.ext.dirs")
     
      用户可见的 ClassLoader，用于加载位于 JRE 外 ext 目录下的 jar 包 （加载扩展库 javax.*) ， 用户也可以将自己自定义的 jar 包放到 该目录下，通过 ExtClassLoader 进行加载；
      
  3. AppClassLoader(extends URLClassLoader, "java.class.path");)
      
      用来加载 ClassPath 下的文件，类路径
      
  4. 自定义 ClassLoader: (可能不在系统的 classpath 范围内， 也可以指定在范围内) Java 编写，定制化加载
  
### 自定义 ClassLoader 如何实现：
      
  几个关键函数: 
  
   ```java
      loadClass()
   ```
    
   ```java
     // findClass() 作用：根据位置/名称 加载 .class 字节码
     protected Class<?> findCLass(String name){}
   ```
   
   ```java
     // 调用 defineClass() 解析定义 .class 字节流，返回 class 对象
     // 重新定义和生成类
     // byte[] class 字节码
     protected final Class<?> defineClass(byte[] b, int off, int len){
       return defineClass(null, b, off, len, null);
     }
   ```
     
   exp:
     
   首先在项目外部任意一个 path 下创建一个 java 文件
   ```java
   public class Walle {
   	static{
   		System.out.println("Hello World");
   	}
   }
   
   ```
     
   然后通过 javac 编译 *.java 文件，生成 *.class 文件
   
   通过自定义 ClassLoader 加载、解析 *.class 文件
   
   ```java
   /**
    * @author ercargo  on 2020/4/4
    * @DESCRIBE 定义一个自定义 classLoader
    */
   public class MyCLassLoader extends ClassLoader {
       private String path;
       private String classLoaderName;
   
       public MyCLassLoader(String path, String classLoaderName) {
           this.path = path;
           this.classLoaderName = classLoaderName;
       }
   
       // 查找类文件
       @Override
       public Class findClass(String name) {
           byte[] b = loadClassData(name);
           return defineClass(name, b, 0, b.length);
       }
   
       // 用于加载类文件
       private byte[] loadClassData(String name) {
           // 路径找到对应的 class 文件
           name = path + name + ".class";
   
           // 输入流读取文件
           InputStream in = null;
           // byte 作为接收，输出
           ByteArrayOutputStream out = null;
   
           try {
               in = new FileInputStream(new File(name));
               out = new ByteArrayOutputStream();
   
               int i = 0;
               // 循环读取文件
               while ((i = in.read()) != -1) {
                   out.write(i);
               }
   
   
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               try {
                   out.close();
                   in.close();
               } catch (Exception e) {
                   e.printStackTrace();
               }
           }
           return out.toByteArray();
       }
   }
   ```
     
   ```java
   /**
    * @author ercargo  on 2020/4/4
    * @DESCRIBE 校验自定义 ClassLoader 是否 work
    */
   public class ClassLoaderChecker {
       public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException {
           MyCLassLoader loader = new MyCLassLoader("/Users/ercargo/Desktop/","xxx");
           Class c = loader.loadClass("Walle");
           System.out.println(c.getClassLoader());
           c.newInstance();
       }
   }
   ```
   
   findClass() 不仅可以在自定义的目录下加载文件，也可以通过不同的形式进行加载， 只要保证调用 defineClass() 时传入的二进制字节流是合法的：
   
   1. 访问远程的网络获取二进制流；
   2. 对敏感文件进行加密, 在 findClass() 进行解密；
   3. 对生成的二进制流代码进行修改，给类添加一些信息（如： asm 就是用来改造二进制流的行为； 字节码增强技术； 动态代理、AOP 的实现）
     
    
     引申思考「字节码增强技术」[Java字节码学习笔记](/2020/04/Java字节码学习笔记)

 
## ClassLoader 的双亲委派机制（parent）
> 不同的 ClassLoader 加载类的方式和路径是不同的，为了实现分工， 各自负责各自的区块， 使得逻辑更明确

    为什么叫双亲委派模型？
    
    该模型中除了 最顶层的 BootstrapClassLoader, 其余的 ClassLoader 都有自己的 父类 ClassLoader 但这个父子关系是由 「组合」 而不是继承来实现的

1. 自底而上的检查类是否已被加载
  - 从自定义 ClassLoader 开始查看是否某个类被加载过，
  - 没有就去 AppClassLoader 去查找，
  - 没有再继续到 ExtClassLoader 查找， 
  - 直到 BootStrapClassLoader
  - 如果还是没有就会继续按照下面「自顶向下」 的方式查找
2. 自顶向下的尝试加载类
  - 委派给 BootstrapLoader Load JRE\lib\rt.jar  或 Xbootclasspath 选项指定的 jar 包
  - 如果没有就委派给 ExtClassLoader Load JRE\lib\ext\*.jar  或 -Djava.ext.dirs 指定目录下的 jar 包
  - 如果没有就委派给 AppClassLoader Load CLASSPATH 或 -Djava.class.path 指定目录下的类和 jar 包
  - 如果没有就通过 java.lang.ClassLoader 的子类自定义加载 class
 

为什么要用双亲委派机制去加载类？
为了避免多份同样的字节码被加载；


JDK 中也有较大规模的破坏双亲模型的情况，如： 线程上下文类加载器（Thread Context ClassLoader）


## 类的加载方式
- 隐式加载 new: 程序在运行过程中遇到 new 生成对象，会隐式调用「类加载器」加载对应的类到 jvm 中； new xxx(yyy) 支持传参数
- 显示加载 loadClass(), Class.forName()
  显示加载， 当获取 class 对象后， 需要调用 class 对象的 newInstance() 方法来生成对象的实例   xxx.newInstance() 不支持参数传入

### loadClass 和 Class.forName() 的区别

相同点：在运行过程中对于任意一个类都能直到该类的属性和方法， 对于任意一个对象，都能调用它的任意方法和属性


类的装载过程：
- 加载: ClassLoader 加载 Class 文件字节码到内存中， 并将「静态数据」转化成「运行时数据区」中**方法区**的类型数据，在运行时数据区的堆中， 生成一个代表这个类的 java.lang.class 对象 作为方法区内的数据访问入口；
- 链接: （resolve: true）
  
    - 校验: 检查加载 class 文件的正确性和安全性;
    - 准备: 为类变量分配存储空间、设置类变量初始值；类变量（static 变量）随类型信息存放在方法区中，生命周期很长，使用不当很容易造成内存泄漏
    - 解析: JVM 将常量池内的符号引用转换为直接引用
    
- 初始化: 执行类变量赋值和静态代码块


参照上述的类装载过程，分析源码得出: 

Class.forName 得到的 class 是已经初始化完成的
initialize: true

![Java forName](/assets/images/forName.jpg)

ClassLoader.loadClass 得到的 class 是还没有 链接的，只是进行了加载
resolve: false

![Java loadClass](/assets/images/loadClass.jpg)

实验:

```java
public class Base {
    public static void process() {
        System.out.println("process");
    }

    static {
        System.out.println("静态代码块被执行（类变量准备）");
    }
}

```

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
        ClassLoader c = Base.class.getClassLoader();
    }
}
```
执行结果：
静态代码块未被加载，表示类变量未被初始化（未链接）

![Java static](/assets/images/static.jpg)


```java
public class ClassLoaderTest {
    public static void main(String[] args) throws ClassNotFoundException {
//        ClassLoader c = Base.class.getClassLoader();
        Class c = Class.forName("com.ercargo.asmbase.Base");
    }
}
```
执行结果：

说明 Class.forName() 会初始化类
![Java forname class](/assets/images/forname_class.jpg)


e.g: Mysql Driver(com.mysql.jdbc.Driver) 需要用 forName 才能执行到 Driver 类中的静态代码块

> Spring Ioc 资源加载器获取要读入的资源时（读取写 Bean 的配置文件时 ），如果时以 Classpath 的方式加载， 就需要使用 ClassLoader.loadClass() 来加载， 
这和 Spring Ioc 的 lazy loading 相关（延迟加载）， Spring Ioc 为了加快初始化速度，大量使用了延时加载技术， ClassLoader 就是不需要执行「初始化」代码等步骤（只需要将 class 字节码加载进内存）
这样可以加快类的加载速度， 等到真正使用这个类的时候，才进行类的初始化， 链接等工作。





--------------

