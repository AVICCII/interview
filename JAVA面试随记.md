# JAVA面试随记

## 1.JDK8的新特性？

- 函数式编程

函数式编程是一种编程风格，它的核心是把函数作为值。而java8把函数式编程的精华融入到了java语法中，你可以将代码传递给方法，也能够返回代码并将其包含在数据结构中，同时这种被函数式编程界称为函数的代码，在java8中可以被来回传递并加以组合，以产生强大的编程语汇，使我们可以用更少的时间，编写更清楚、更简洁的代码。

- Lambda表达式

我们暂时可以把Lambda表达式理解为没有声明名称的方法或匿名类，它可以作为参数传递给一个方法。

- 流（stream）
- 接口（interface）默认方法

默认方法是接口为定义在接口中的方法提供的默认实现，使用default关键词声明，有了默认方法，接口就可以包含实现类没有提供实现的方法签名。

实例：https://blog.csdn.net/sdmxdzb/article/details/82742182?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161959059016780265464324%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161959059016780265464324&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-82742182.pc_search_result_hbase_insert&utm_term=java8+%E6%9C%89%E4%BB%80%E4%B9%88%E6%96%B0%E7%89%B9%E6%80%A7%EF%BC%9F



## 2.Object 类中有什么方法，有什么作用？

1．clone方法

保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。

主要是JAVA里除了8种基本类型传参数是值传递，其他的类对象传参数都是引用传递，我们有时候不希望在方法里讲参数改变，这是就需要在类中复写clone方法。

2．getClass方法

final方法，获得运行时类型。

3．toString方法

该方法用得比较多，一般子类都有覆盖。

4．finalize方法

该方法用于释放资源。因为无法确定该方法什么时候被调用，很少使用。

5．equals方法

该方法是非常重要的一个方法。一般equals和==是不一样的，但是在Object中两者是一样的。子类一般都要重写这个方法。

6．hashCode方法

该方法用于哈希查找，可以减少在查找中使用equals的次数，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到。

一般必须满足obj1.equals(obj2)==true。可以推出obj1.hashCode()==obj2.hashCode()，但是hashCode相等不一定就满足equals。不过为了提高效率，应该尽量使上面两个条件接近等价。

如果不重写hashcode(),在HashSet中添加两个equals的对象，会将两个对象都加入进去。

7．wait方法

wait方法就是使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。wait(long timeout)设定一个超时间隔，如果在规定时间内没有获得锁就返回。

调用该方法后当前线程进入睡眠状态，直到以下事件发生。

（1）其他线程调用了该对象的notify方法。

（2）其他线程调用了该对象的notifyAll方法。

（3）其他线程调用了interrupt中断该线程。

（4）时间间隔到了。

此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常。

8．notify方法

该方法唤醒在该对象上等待的某个线程。

9．notifyAll方法

该方法唤醒在该对象上等待的所有线程。

二、finalize（）的作用

Java允许在类中定义一个名为finalize()的方法。它的工作原理是：一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用其finalize()方法。并且在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。

关于垃圾回收，有三点需要记住：

　　1、对象可能不被垃圾回收。只要程序没有濒临存储空间用完的那一刻，对象占用的空间就总也得不到释放。

　　2、垃圾回收并不等于“析构”。

　　3、垃圾回收只与内存有关。使用垃圾回收的唯一原因是为了回收程序不再使用的内存。

finalize()的用途：
　　无论对象是如何创建的，垃圾回收器都会负责释放对象占据的所有内存。这就将对finalize()的需求限制到一种特殊情况，即通过某种创建对象方式以外的方式为对象分配了存储空间。不过这种情况一般发生在使用“本地方法”的情况下，本地方法是一种在Java中调用非Java代码的方式。

为什么不能显示直接调用finalize方法？
  如前文所述，finalize方法在垃圾回收时一定会被执行，而如果在此之前显示执行的话，也就是说finalize会被执行两次以上，而在第一次资源已经被释放，那么在第二次释放资源时系统一定会报错，因此一般finalize方法的访问权限和父类保持一致，为protected。



## 3.Collection接口的方法和结构

为什么讲collection接口？
因为collection是所有List和Set接口的爸爸，collection有的方法，继承他的接口和实现它的类都有这个方法。
Collection中能存放什么元素？

没有使用泛型之前，Collection可以存储Object的所有子类型
使用泛型后，Collection只能存储某个具体类型
java.util.Collection 方法的常用方法

boolean add(Object o) 向集合中添加元素
int size() 获取集合中元素的个数
void clear() 清空集合
boolean contains(Object o) 判断集合中是否有o
boolean remove( Object o) 删除某个元素
boolean isEmpty() 判断集合是否为空
Object[] toArray 转换成数组的方法

```java
package collection;

import oop.demo01.Student;

import java.util.ArrayList;
import java.util.Collection;

/*
java.util.Collection 方法的常用方法
 */
public class CollectionTest01 {
    public static void main(String[] args) {
        //创建一个集合对象
        //Collection c = new Collection(); //接口是抽象的，无法实例化
        //多态
        Collection c = new ArrayList<>();
        //测试Connection接口中的常用方法

        //=向集合添加元素
        c.add(1200);//自动装箱，实际是放进了一个对象的内存地址 Integer x = new Integer(1200)
        c.add(3.14);//自动装箱
        c.add(new Object());
        c.add(new Student());
        c.add(true);//自动装箱

        //获取集合中元素的个数
        System.out.println(c.size());//5

        //清空集合
        c.clear();
        System.out.println(c.size());//0

        c.add("hello");
        c.add("world");

        //判断集合中是否有hello
        boolean flag = c.contains("hello");
        System.out.println(flag); //true

        //删除集合中的某个元素
        c.remove("world");
        System.out.println(c.size()); //1

        //判断集合是否为空
        System.out.println(c.isEmpty());//false

        //转换成数组
        c.add(123);
        c.add(456);
        c.add(789);
        c.add(new Student());
        Object[] objs = c.toArray();
        for (int i = 0; i < objs.length; i++) {
            Object o = objs[i];
            System.out.println(o);
        }
    }

public static void student(){

}
}

```

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTcuY25ibG9ncy5jb20vYmxvZy84NTU5NTkvMjAxNzA4Lzg1NTk1OS0yMDE3MDgyMzE0MTQyMTQ4MC00MTcxOTE4NDkucG5n)

https://blog.csdn.net/Rango_kjc/article/details/98609268?ops_request_misc=&request_id=&biz_id=102&utm_term=collection%E6%8E%A5%E5%8F%A3%E7%BB%93%E6%9E%84&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-98609268.pc_search_result_hbase_insert

## 4.java四大特性

一、抽象

父类为子类提供一些属性和行为，子类根据业务需求实现具体的行为。

抽象类使用abstract进行修饰，子类要实现所有的父类抽象方法否则子类也是抽象类。

二、封装

把对象的属性和行为(方法)结合为一个独立的整体，并尽可能隐藏对象的内部实现细节；

在java中，对于对象的内部属性一般用private来实现隐藏，并通过set和get方法对外提供访问接口。

三、继承

子类继承父类的属性和行为，并能根据自己的需求扩展出新的属性和行为，提高了代码的可复用性。

Java的继承通过extends关键字来实现，实现继承的类被称为子类，被继承的类称为父类(有的也称其为基类、超类)，父类和子类的关系，是一种一般和特殊的关系；子类扩展父类，将可以获得父类的全部属性和方法。

overide：

当子父类中出现相同方法时，会先运行子类中的方法。

重写的特点：方法名一样，访问修饰符权限不小于父类，返回类型一致，参数列表一致。

四、多态

不修改程序代码就可以改变程序运行时所绑定的具体代码，让程序可以选择多个运行状态；

具体的实现方式就是：接口实现，继承父类进行方法重写，同一个类中进行方法重载。

封装和继承都是为Java语言的多态提供了支撑；多态存在的三个必要条件：

要有继承；

要有重写；

父类引用指向子类对象。


## 5.hashMap、ArrayList 线程不安全性如何解决？

 可以通过以下三个方法解决 **ArrayList** 线程不安全的问题：

 1、使用 Vector 集合，该集合同样实现了 List 接口，但是它的add()方法加上了synchronized保证了线程安全性；

 2、使用集合工具类 Collections，该类中封装了很多关于集合操作的方法，方法 synchronizedList(new ArrayList<>())便是对 ArrayList 进行了封装，使其解决不安全问题；

 3、使用写时复制 CopyOnWriteArrayList进行操作：

往容器添加元素的时候,不直接往当前容器object[]添加，而是先将当前容器object[]进行复制出一个新的object[] newElements ‘
然后向新容器object[] newElements 里面添加元素；
添加元素后再将原容器的引用指向新的容器 setArray(newElements)。



 HashSet 和 HashMap 的解决办法同 ArrayList 类似。其在多线程操作时也会报并发修改异常的错误。其中Collections中有synchronizedMap()和synchronizedSet()方法。还可以使用CopyOnWriteArraySet<>()和ConcurrentHashMap<>()方法。



## 6.ReentrantLock和Synchronized的区别和原理

见https://blog.csdn.net/qq_40551367/article/details/89414446/?ops_request_misc=&request_id=&biz_id=102&utm_term=ReentrantLcok%20%E5%92%8Csynchronized&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-89414446.pc_search_result_hbase_insert



## 7.分布式锁

**1.** 什么是分布式锁?
**2.** 为什么需要分布式锁(解决了什么问题)?
**3.** 怎么去实现分布式锁？

分布式锁的由来当然是因为分布式架构的出现而产生的. 在之前的单体架构中 , 面对线程安全的问题可能使用 Java 提供的 ReentrantLcok 或 Synchronized 便足矣. 但是随着业务不断发展,这时单机满足不了,于是采用分布式部署的方式. 虽然一定程度解决了性能的瓶颈 , 但是也带来了许多分布式相关的问题. 就分布式锁而言,看如下图:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201217121321662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FLQUxYSA==,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201217121240291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FLQUxYSA==,size_16,color_FFFFFF,t_70#pic_center)

可以想象下一个业务场景, 单体部署时, 业务代码里的并发控制锁策略失效.比如你一个秒杀业务, 用了 Synchronized 来进行线程控制修改数据库里的库存. 单体架构看来没啥问题, 但是多台服务器时这样的锁就不管用了. 因为每个锁只是控制着自身服务器里头的线程. 这时就需要跨服务器的锁机制来解决资源控制的问题.

分布式锁常见的有三种实现方式 :

- 基于 Redis 实现分布式锁.

分布式锁实现的关键是在分布式的应用服务器外，搭建一个存储服务器，存储锁信息，这时候我们很容易就想到了Redis。首先我们要搭建一个Redis服务器，用Redis服务器来存储锁信息。

在实现的时候要注意的几个关键点：

1、锁信息必须是会过期超时的，不能让一个线程长期占有一个锁而导致死锁；

2、同一时刻只能有一个线程获取到锁。

几个要用到的redis命令：

setnx(key, value)：“set if not exits”，若该key-value不存在，则成功加入缓存并且返回1，否则返回0。

get(key)：获得key对应的value值，若不存在则返回nil。

getset(key, value)：先获取key对应的value值，若不存在则返回nil，然后将旧的value更新为新的value。

expire(key, seconds)：设置key-value的有效期为seconds秒。
![img](https://img-blog.csdn.net/20180219144927282)

- 基于 Zookeeper 实现.

- 基于数据库 实现.



分布式锁是控制分布式系统之间同步访问共享资源的一种方式。
在分布式系统中，常常需要协调他们的动作。如果不同的系统或是同一个系统的不同主机之间共享了一个或一组资源，那么访问这些资源的时候，往往需要互斥来防止彼此干扰来保证一致性，这个时候，便需要使用到分布式锁.



## 8.Spring AOP底层实现原理（动态代理）

https://blog.csdn.net/weixin_42950079/article/details/96733737?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161959807516780265449808%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=161959807516780265449808&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-96733737.pc_search_result_hbase_insert&utm_term=Spring+AOP+%E5%BA%95%E5%B1%82%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%EF%BC%9F



## 9.编译性语言和解释性语言的区别

- **编译性语言**

（1）只须**编译一次**就可以把源代码编译成机器语言，后面的执行无须重新编译，直接使用之前的编译结果就可以；因此其执行的效率比较高； （从编译次数这个角度）
（2）编译性语言代表：C、C++、Pascal/Object Pascal（Delphi）；
（3）程序执行效率比较高，但比较依赖编译器，因此跨平台性差一些；（效率）

**不同平台对编译器影响较大**。 
如：

（1）16位系统下int是2个字节（16位），而32位系统下int占4个字节（32位）；

（2）32位系统下long类型占4字节，而64位系统下long类型占8个字节；

- **解释型语言**

（1）源代码不能直接翻译成机器语言，而是**先翻译成中间代码**，再由**解释器对中间代码进行解释运行**；

（2）程序不需要编译，程序在运行时才翻译成机器语言，每执行一次都要翻译一次；

（3）解释性语言代表：Python、JavaScript、Shell、Ruby、MATLAB等；

（4）运行效率一般相对比较低，依赖解释器，**跨平台性好**



## 10.自旋锁以及Java中的自旋锁的实现

### 什么是自旋锁
多线程中，对共享资源进行访问，为了防止并发引起的相关问题，通常都是引入锁的机制来处理并发问题。

获取到资源的线程A对这个资源加锁，其他线程比如B要访问这个资源首先要获得锁，而此时A持有这个资源的锁，只有等待线程A逻辑执行完，释放锁，这个时候B才能获取到资源的锁进而获取到该资源。

这个过程中，A一直持有着资源的锁，那么没有获取到锁的其他线程比如B怎么办？通常就会有两种方式：

1. 一种是没有获得锁的进程就直接进入阻塞（BLOCKING），这种就是互斥锁

2. 另外一种就是没有获得锁的进程，不进入阻塞，而是一直循环着，看是否能够等到A释放了资源的锁。

上述的两种方式，学术上，就有几种不同的定义方式，大学的时候 学习的是C++， 《C++ 11》中就有这样的描述：

**自旋锁（spin lock）**是一种非阻塞锁，也就是说，如果某线程需要获取锁，但该锁已经被其他线程占用时，该线程不会被挂起，而是在不断的消耗CPU的时间，不停的试图获取锁。

**互斥量（mutex）是阻塞锁**，当某线程无法获取锁时，该线程会被直接挂起，该线程不再消耗CPU时间，当其他线程释放锁后，操作系统会激活那个被挂起的线程，让其投入运行。

而《linux内核设计与实现》经常提到两种态，一种是内核态，一种是用户态，对于自旋锁来说，自旋锁使线程处于用户态，而互斥锁需要重新分配，进入到内核态。这里大家对内核态和用户态有个初步的认知就行了，用户态比较轻，内核态比较重。用户态和内核态这个也是linux中必备的知识基础，借鉴这个，可以进行很多程序设计语言API上的优化，就比如说javaio的部分，操作io的时候，先是要从用户态，进入内核态，再用内核态去操作输入输出设备的抽象，这里减少用户态到内核态的转换就是新io的一部分优化，后面再聊。

wiki中的定义如下：

**自旋锁**是计算机科学用于多线程同步的一种锁，**线程反复检查锁变量是否可用。由于线程在这一过程中保持执行，因此是一种忙等待。**

自旋锁避免了进程上下文的调度开销，因此对于线程只会阻塞很短时间的场合是有效的。因此操作系统的实现在很多地方往往用自旋锁。Windows操作系统提供的轻型读写锁（SRW Lock）内部就用了自旋锁。**显然，单核CPU不适于使用自旋锁，这里的单核CPU指的是单核单线程的CPU，因为，在同一时间只有一个线程是处在运行状态，假设运行线程A发现无法获取锁，只能等待解锁，但因为A自身不挂起，所以那个持有锁的线程B没有办法进入运行状态，只能等到操作系统分给A的时间片用完，才能有机会被调度。这种情况下使用自旋锁的代价很高。（红字部分是我给wiki编辑的词条，单核CPU不适合自旋锁，这个也只是针对单核单线程的情况，现在的技术基本单核都是支持多线程的）**

### 为什么要使用自旋锁
互斥锁有一个缺点，他的执行流程是这样的 托管代码  - 用户态代码 - 内核态代码、上下文切换开销与损耗，假如获取到资源锁的线程A立马处理完逻辑释放掉资源锁，如果是采取互斥的方式，那么线程B从没有获取锁到获取锁这个过程中，就要用户态和内核态调度、上下文切换的开销和损耗。所以就有了自旋锁的模式，让线程B就在用户态循环等着，减少消耗。

**自旋锁比较适用于锁使用者保持锁时间比较短的情况**，这种情况下自旋锁的效率要远高于互斥锁。

### 自旋锁可能潜在的问题
过多占用CPU的资源，如果锁持有者线程A一直长时间的持有锁处理自己的逻辑，那么这个线程B就会一直循环等待过度占用cpu资源
递归使用可能会造成死锁，不过这种场景一般写不出来
### CAS
就不写术语定义了，简单的理解就是这个CAS是由操作系统定义的，由若干指令组成的，这个操作具有原子性，这些指令如果执行，就会全部执行完，不会被中断。CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。

### CAS的问题
经典的CAS的ABA问题，上面提到了CAS操作的时候，要检测值有没有变化，如果一个值原来是A，后来变成了B， 后来又变成了A，CAS会认为没有发生变化。
       解决方案：

       1. 加版本号   1A - 2B -  3A
    
        2. 对java而言，jdk1.5提供了AtomicStampedReference来解决这个问题

只能保证一个共享变量的原子操作 
        CAS通常是对一个变量来进行原子操作的，所以如果对多个变量进行原子操作就会有问题了。

        解决方案
    
        1. 简单粗暴，加锁，反而加入了复杂性，最low的方式
    
        2. 跟上面的加版本号的道理一样，就是将多个变量拼成一个变量（可以拼成一个字符串）
    
        3. 对java而言，jdk1.5 提供了AtomicStampedReference，这个reference 就是个对象引用，把多个变量放在这个对象里即可

### JAVA CAS封装
 sun.misc.Unsafe是JDK里面的一个内部类，这个类当中有三个CAS的操作

### JAVA自旋锁应用
Jdk1.5以后，提供了java.util.concurrent.atomic包，这个包里面提供了一组原子类。基本上就是当前获取锁的线程，执行更新的方法，其他线程自旋等待，比如atomicInteger类中的getAndAdd方法内部实际上使用的就是Unsafe的方法。

    /**
     * Atomically adds the given value to the current value.
     *
     * @param delta the value to add
     * @return the previous value
     */
    public final int getAndAdd(int delta) {
        return unsafe.getAndAddInt(this, valueOffset, delta);
    }
当然java中的syncronized关键字，在1.5中有了很大的优化，加入了偏隙锁也有人叫偏向锁，主要的实现方式就是在对象头markword中打上线程的信息，这样资源上的锁的获取就偏向了这个线程，后面，会涉及一系列的锁升级的问题，间隙锁 - 轻量锁 - 重量级锁 ，锁升级后面单独抽出来写一篇，这个轻量锁实际上就是使用的也是自旋锁的实现方式。


## 11.countDownLatch

### countDownLatch是什么

ountDownLatch的作⽤就是 允许 count 个线程阻塞在⼀个地⽅，直⾄所有线程的任务都执⾏完毕，相当于赛跑，把运动员阻塞在起点，然后再开始。

```java
public class Juc {
    public static void main(String[] args) throws InterruptedException { 
    //
    CountDownLatch countDownLatch = new CountDownLatch(6);
    for (int i = 1; i <=6 ; i++) {
        new Thread(()->{
        		//业务逻辑开始
            System.out.println(Thread.currentThread().getName()+" ok");
            //业务逻辑完成
            countDownLatch.countDown();
        // 数量-1
        },String.valueOf(i)).start();
    }
        countDownLatch.await(); // 等待计数器归零，然后再向下执行
        System.out.println("run");
    }
}
```

### 使用场景

CountDownLatch作为同步化的一个辅助工具，允许一个或多个线程等待，直到所有线程中执行完成
比如主线程计算一个复杂的计算表达式，将表达式分为多个子表达式在线程中去计算，
主线程要计算表达式的最后值，必须等所有的线程计算完子表达式计算，方可计算表达式的值；
再比如一个团队赛跑游戏，最后要计算团队赛跑的成绩，主线程计算最后成绩，要等到所有
团队成员跑完，方可计算总成绩。使用情况两种：第一种，所有线程等待一个开始信息号，当开始信息号启动时，所有线程执行，等待所有线程执行完；第二种，所有线程放在线程池中，执行，等待所有线程执行完，方可执行主线程任务方可执行主线程任务。



## 12.HTTP浏览器输入url后发生了什么

1. DNS域名解析
2. 建立TCP连接
3. 发送HTTP请求
4. 服务器处理请求
5. 返回响应结果
6. 关闭TCP连接
7. 浏览器解析HTML
8. 浏览器布局渲染

https://blog.csdn.net/sinat_23880167/article/details/78882766?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control



## 13.慢查询优化

### 慢查询概念

MySQL的慢查询，全名是慢查询日志，是MySQL提供的一种日志记录，用来记录在MySQL中响应时间超过阀值的语句。

具体环境中，运行时间超过long_query_time值的SQL语句，则会被记录到慢查询日志中。

long_query_time的默认值为10，意思是记录运行10秒以上的语句。

默认情况下，MySQL数据库并不启动慢查询日志，需要手动来设置这个参数。

当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。

慢查询日志支持将日志记录写入文件和数据库表。

### 常见的慢查询优化

(1) 索引没起作用的情况

- 使用LIKE关键字的查询语句

在使用LIKE关键字进行查询的查询语句中，如果匹配字符串的第一个字符为"%"，索引不会起作用。只有“%”不在第一个位置索引才会起作用。

- 使用多列索引的查询语句

​    MySQL可以为多个字段创建索引。一个索引最多可以包括16个字段。对于多列索引，只有查询条件使用了这些字段中的第一个字段时，索引才会被使用。

(2) 优化数据库结构

   合理的数据库结构不仅可以使数据库占用更小的磁盘空间，而且能够使查询速度更快。数据库结构的设计，需要考虑数据冗余、查询和更新的速度、字段的数据类型是否合理等多方面的内容。

- 将字段很多的表分解成多个表 

对于字段比较多的表，如果有些字段的使用频率很低，可以将这些字段分离出来形成新表。因为当一个表的数据量很大时，会由于使用频率低的字段的存在而变慢。

- 增加中间表

对于需要经常联合查询的表，可以建立中间表以提高查询效率。通过建立中间表，把需要经常联合查询的数据插入到中间表中，然后将原来的联合查询改为对中间表的查询，以此来提高查询效率。



## 14.为什么要用redis做验证码缓存, 这样做有什么好处?

1.redis缓存运行效率高
 2.redis可以通过expire来设定过期策略，比较适用于验证码的场景。
 3.考虑到分布式数据个负载均衡数据要一致，这种共有的不用持久化的数据最好找一个缓存服务器存储
 4.redis、Memcache都是内存数据库，都支持K-Y型的数据结构
 5.redis还支持其他更加丰富的数据结构（list，set，hash等）



## 15.Cookie、Session和Token的区别

### 1.Session 与 Cookie的区别及关联关系
cookie数据存放在客户的浏览器上，session数据放在服务器上(不同容器, 不同框架存储的位置不同, 可能是内存, 可能是文件, 也可以持久化储存)

#### 1.1. Cookie原理
  cookie是保存在本地终端的数据。cookie由服务器生成，发送给浏览器，浏览器把cookie以kv形式保存到某个目录下的文本文件内，下一次请求同一网站时会把该cookie发送给服务器。由于cookie是存在客户端上的，所以浏览器加入了一些限制确保cookie不会被恶意使用，同时不会占据太多磁盘空间，所以每个域的cookie数量是有限的。

通过Set-Cookie响应头来设置Cookie, 字段中可以设置Cookie的值一些限制条件

Domain：域，表示当前cookie所属于哪个域或子域下面
Path：表示cookie的所属路径
Expire time/Max-age: 表示Cookie的过期时间
secure: 表示该cookie只能用https传输, 一般用于包含认证信息的cookie，要求传输此cookie的时候，必须用https传输
httponly：表示此cookie必须用于http或https传输。这意味着，浏览器脚本，比如javascript中，是不允许访问操作此cookie的
不同浏览器关于Cookie大小以及数量的限制各不相同, 但一般来说, cookie的大小最好不要超过4kb.

#### 1.2. Session原理
  session的中文翻译是“会话”，当用户打开某个web应用时，便与web服务器产生一次session。服务器使用session把用户的信息临时保存在了服务器上，用户离开网站后session会被销毁。这种用户信息存储方式相对cookie来说更安全，可是session有一个缺陷：如果web服务器做了负载均衡，那么下一个操作请求到了另一台服务器的时候session会丢失。


#### 1.3. 总结

可以看到实际上并没有太明显的区别, 可以理解成一个整体, 互相辅佐

### 2.token

 token的意思是“令牌”，是用户身份的验证方式，最简单的token组成:uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign(签名，由token的前几位+盐以哈希算法压缩成一定长的十六进制字符串，可以防止恶意第三方拼接token请求服务器)。还可以把不变的参数也放进token，避免多次查库


token和上面提到的Session或者Cookie也没有太明显的区别, 也是为了解决某种问题所创造出来的名词

token概念: 带有某种特殊意义的字符串

token所解决的问题:

为了防止每次登陆的时候, 后端频繁查库.
举两个例子: 如果session中保存着一些用户信息, 但服务器是集群的, 需要怎么做? 如果A 网站和 B 网站是同一家公司的关联服务。现在要求，用户只要在其中一个网站登录，再访问另一个网站就会自动登录, 又需要怎么做? 第一个问题我们可以采用session共享的方法, 第二个问题可以采用session数据持久化, 写入数据库. 不过缺点都是工作量比较大. 因此可以加密一段特殊的信息, 保存到客户端, 当用户发来请求时, 带上token, 服务端解密提取关键信息.

### 3.cookie和session的区别

1、cookie数据存放在客户的浏览器上，session数据放在服务器上。

2、cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗
   考虑到安全应当使用session。

3、session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能
   考虑到减轻服务器性能方面，应当使用COOKIE。

4、单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。

5、所以个人建议：
   将登陆信息等重要信息存放为SESSION
   其他信息如果需要保留，可以放在COOKIE中



### 4.token和session的区别

session 和 oauth token并不矛盾，作为身份认证 token安全性比session好，因为每个请求都有签名还能防止监听以及重放攻击，而session就必须靠链路层来保障通讯安全了。如上所说，如果你需要实现有状态的会话，仍然可以增加session来在服务器端保存一些状态

    App通常用restful api跟server打交道。Rest是stateless的，也就是app不需要像browser那样用cookie来保存session,因此用session token来标示自己就够了，session/state由api server的逻辑处理。 如果你的后端不是stateless的rest api, 那么你可能需要在app里保存session.可以在app里嵌入webkit,用一个隐藏的browser来管理cookie session.

 


   Session 是一种HTTP存储机制，目的是为无状态的HTTP提供的持久机制。所谓Session 认证只是简单的把User 信息存储到Session 里，因为SID 的不可预测性，暂且认为是安全的。这是一种认证手段。 而Token ，如果指的是OAuth Token 或类似的机制的话，提供的是 认证 和 授权 ，认证是针对用户，授权是针对App 。其目的是让 某App有权利访问 某用户 的信息。这里的 Token是唯一的。不可以转移到其它 App上，也不可以转到其它 用户 上。 转过来说Session 。Session只提供一种简单的认证，即有此 SID，即认为有此 User的全部权利。是需要严格保密的，这个数据应该只保存在站方，不应该共享给其它网站或者第三方App。 所以简单来说，如果你的用户数据可能需要和第三方共享，或者允许第三方调用 API 接口，用 Token 。如果永远只是自己的网站，自己的 App，用什么就无所谓了。

  token就是令牌，比如你授权（登录）一个程序时，他就是个依据，判断你是否已经授权该软件；cookie就是写在客户端的一个txt文件，里面包括你登录信息之类的，这样你下次在登录某个网站，就会自动调用cookie自动登录用户名；session和cookie差不多，只是session是写在服务器端的文件，也需要在客户端写入cookie文件，但是文件里是你的浏览器编号.Session的状态是存储在服务器端，客户端只有session id；而Token的状态是存储在客户端。


## 15. hashmap的时间复杂度一定是O(1)吗?

不一定。如果检索到的bucket位置没有重复hashcode的entry(链表)，复杂度为O(1)，如果检索位置有链表，那么需要将链表整个遍历，然后查看是否重复后加入，此时的时间复杂度为O(n)。



## 16.map为什么用[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)来解决哈希冲突？

因为hashmap的数据结构是基于数组加链表，而数组以hashcode进行bucket位置的散列，存储的是相同hashcode的key和value，而hashcode相同，key和value都有可能不同。这时对于bucket位置来说，存储的节点都是不同的数据，所以用链表更适合。



## 17. redis IO多路复用原理

总结：所谓的redis的多路复用原理
他是把IO操作再细分成多个事件去处理
比如IO涉及连接 读取 输入
把这三种当成三种事件分别起三个线程处理
如果一个连接来了显示被读取线程处理了，然后再执行写入，那么之前的读取就可以被后面的请求复用，吞吐量就提高了



