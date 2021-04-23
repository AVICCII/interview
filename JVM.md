# JVM

## 1.内存结构

### 1.1程序计数器

#### 1.1.1定义

Program Counter Register 程序计数器（寄存器）

#### 1.1.2作用

记住下一条jvm指令的执行地址

特点：

- 线程私有
- 不存在内存溢出

### 1.2虚拟机栈

#### 1.2.1定义

Java Virtual Machine Stacks(java虚拟机栈)

- 每个线程运行时需要的内存，称为虚拟机栈
- 每个栈由多个栈帧(Frame)组成，对应着每次方法调用时所占用的内存
- 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

问题：

1.垃圾回收是否涉及栈内存？

栈帧每次调用后会直接弹出栈，不需要垃圾回收。

2.栈内存分配越大越好吗？

不是。栈内存分配空间越大，由于物理内存固定，分配栈内存越多线程则越少。

3.方法内的局部变量是否线程安全？

方法内的局部变量如果是线程私有的，则线程安全，因为其保存在栈帧。如果是静态局部变量，则是放在方法栈里，是线程共享的，如果没有线程安全保护则会出现线程安全问题。

另外，如果该方法内的局部变量逃离了该方法(例如作为返回值),那么该局部变量也是非线程安全的

#### 1.2.2 栈内存溢出

- 栈帧过多导致栈内存溢出
- 栈帧过大导致栈内存溢出

#### 1.2.3线程运行诊断

案例一：Cpu占用过多

定位(linux下)

- 用top定位哪个进程对cpu的占用过高
- ps H -eo pid,tid,%cpu | grep 进程id(用ps命令进一步定位是哪个线程引起的cpu占用过高)
- jstack 进程id
  - 可以根据线程id找到有问题的线程，进一步定位到问题代码的源码行号

案例二：程序运行很长时间没有结果

### 1.3本地方法栈

![image-20210315141130243](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210315141130243.png)

### 1.4 堆

![image-20210315141148105](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210315141148105.png)

#### 1.4.1 定义

Heap 堆

- 通过new关键字，创建对象都会使用堆内存

特点

- 它是线程共享的，堆中对象都需要考虑线程安全的问题
- 有垃圾回收机制

#### 1.4.2 堆内存溢出

#### 1.4.3堆内存诊断

1.jps工具

- 查看当前系统总有哪些java进程

2.jmap工具

- 查看堆内存占用情况 jmap -heap 进程id

3.jconsole工具

- 图形界面的，多功能的监测工具，可以连续监测



案例

- 垃圾回收后，内存占用仍然很高



### 1.5 方法区

![image-20210408163924945](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210408163924945.png)

### 1.6直接内存

#### 1.6.1 分配和回收原理

- 使用了unsafe对象完成直接内存的分配回收，并且回收需要主动调用freeMemory方法
- BtyeBuffer的实现类内部，使用了cleaner（虚引用）来监测ByteBuffer对象，一旦ByteBuffer对象被垃圾回收，那么就会由ReferanceHandler线程通过Cleaner的clean方法调用freeMemory来释放直接内存



## 垃圾回收

### 1.如何判断对象可以回收

#### 1.1引用计数法

如果对象A引用了对象B，则对象B的引用计数为1.但是，如果对象B也同时引用了对象A(此时对象A的引用计数变为1)而二者都没有被别的对象引用，他们不会被垃圾回收从而造成**内存泄漏**.

#### 1.2可达性分析算法

根对象：不会被当作垃圾被回收的对象

被根对象直接间接引用则不会被回收

- Java虚拟机中的垃圾回收器采用可达性分析来探索所有存活的对象

- 扫描堆中的对象，看是否能够沿着GC Root对象为起点的引用链找到该对象，找不到，表示可以回收

- 哪些对象可以作为GC Root?

  GCRoots对象包括：虚拟机栈中的局部对象变量表引用的对象，方法区中类静态属性引用和常量引用对象，本地方法栈中的引用的对象

#### 1.4 四种引用

![image-20210401145545791](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210401145545791.png)

##### 1.4.1 强引用

平时一般使用都是强引用 特点：沿着GC ROOT引用链可以找到就不会被GC

##### 1.4.2 软引用

##### 1.4.3 弱引用

软、弱引用：如果没有被直接的强引用所引用，当GC时都有可能被GC。

如上图，如果A2对象没有被B对象强引用，则有可能被GC（GC发现内存不足）。

弱引用则无论GC发现内存是否不足，都会被GC，也就是弱引用比软引用更容易被回收。

// 配合引用队列，如果对象被GC,可以将软、弱引用加入引用队列，进而对其进行处理来节省内存（引用本身也占据内存）

##### 1.4.4 虚引用

主要配合ByteBuffer使用

##### 1.4.5 终结器引用

虚引用和终结器引用**必须配合引用队列**，通过关联的方式。

虚引用中，当ByteBuffer不再被GCroot强引用时，可以自己进行GC释放，而其关联的直接内存并没有被释放（直接内存不归JVM管理），此时虚引用会进入引用队列，会由一个ReferanceHandler线程来定时的寻找这个cleaner来调用clean方法来根据虚引用中记录的直接内存地址通过unsafe对象的freeMemory方法来释放该直接内存。

终结器引用中，因为所有JAVA对象都继承自OBEJECT父类，而该父类中都有一个finalize方法，如果该对象重写了finalize方法，当无强引用引用它时，它就可以被当作垃圾回收，将终结器引用加入引用队列，通过finalizeHandler线程（优先级很低）查看引用队列是否有终结器引用来找到对象并调用其finalize方法，然后下一次GC就会被回收。（**因为该线程优先级很低，所以该方法往往迟迟得不到调用，内存也就迟迟得不到释放，所以不推荐通过重写finalize方法来实现垃圾回收**）



**软引用实例**

```java
public class 软引用场景 {
    //-Xmx20m 设定堆大小20m
    private static final int _4MB = 4 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        //场景：读取图片，图片过大。强应用导致内存占用过多
//        List<byte[]> list = new ArrayList<>();
//        for (int i = 0; i < 5; i++) {
//            list.add(new byte[_4MB]);
//        }
        soft();
        System.in.read();
    }


    public static void soft() {
        //list --> SoftReference --> byte[]
        List<SoftReference<byte[]>> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB]);
            System.out.println(ref.get());
            list.add(ref);
            System.out.println(list.size());
        }
        System.out.println("循环结束：" + list.size());
        for (SoftReference<byte[]> ref : list) {
            System.out.println(ref.get());
        }
    }
}
```

结合引用队列

```java
public static void soft() {
    //list --> SoftReference --> byte[]

    //引用队列
    ReferenceQueue<byte[]> referenceQueue = new ReferenceQueue<>();

    List<SoftReference<byte[]>> list = new ArrayList<>();
    for (int i = 0; i < 5; i++) {
        //关联了引用队列，当软引用所关联的byte数组被回收时，软引用会加入到referenceQueue中去
        SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB],referenceQueue);
        System.out.println(ref.get());
        list.add(ref);
        System.out.println(list.size());
    }
    System.out.println("循环结束：" + list.size());
    for (SoftReference<byte[]> ref : list) {
        System.out.println(ref.get());
    }
}
```

### 2.垃圾回收算法

#### 2.1标记清除

标记哪些对象没有被GCROOT引用，即为垃圾。然后对这些垃圾对象占用的空间进行释放

![image-20210401163034029](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210401163034029.png)

优点：速度快。

缺点：容易产生内存碎片（内存空间不连续）。在清除以后，不会对空闲的内存空间进行进一步操作，如果下一步分配了一个较大的对象，就塞不进去。

#### 2.2标记整理

第一步和标记清除相同。第二步进行整理

![image-20210401163601915](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210401163601915.png)

缺点：由于整理涉及到对象的移动，所以效率较低

#### 2.3复制

将内存区域划分成大小相同的两个区域（from & to） to一般都是空闲的，首先在From进行标记，从From区域存活的对象复制到to区域中，然后交换From和to的位置

![image-20210401163839092](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210401163839092.png)

### 3.分代垃圾回收

![image-20210402104850861](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210402104850861.png)

![image-20210408161752950](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210408161752950.png)

如果经历了多次minor gc，依然存活的对象，（超过一定阈值比如15）则将其放入老年代，老年代中的对象不会轻易被GC

![image-20210408162008812](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210408162008812.png)



**总结**:

- 对象首先分配在伊甸园区域
- 新生代空间不足时，触发minor gc,伊甸园和from存活的对象使用copy复制到to中，存活的对象年龄＋1，同时交换from和to
- minor gc会引发stop the world，暂停其他用户的线程，等垃圾回收结束，用户线程才恢复运行
- 当对象寿命超过阈值，会晋升到老年代，最大寿命是15(4bit)，
- 当老年代的空间不足，会先尝试触发minor gc,如果之后空间仍然不足，就会触发Full Gc来对整体进行一个清理,stw（stop the world）的时间更长

### 4.垃圾回收器

#### 4.1串行

- 单线程
- 堆内存较小，适合个人电脑

-XX:+UseSerialGC=Serial+SerialOld

![image-20210413111552982](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210413111552982.png)



#### 4.2吞吐量（一定时间内回收垃圾数量）优先 （垃圾回收时间占比越低，吞吐量越高）

- 多线程
- 堆内存较大，多核CPU
- 让单位时间内的STW的时间最短 0.2 0.2 = 0.4

![image-20210413150302621](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210413150302621.png)

#### 4.3响应时间优先 

- 多线程
- 堆内存较大，多核CPU 
- 尽可能让单次STW时间最短 0.1 0.1 .0.1 0.1 0.1 = 0.5
- ![image-20210413150957172](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210413150957172.png)

### 5.垃圾回收调优

