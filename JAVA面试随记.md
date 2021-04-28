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