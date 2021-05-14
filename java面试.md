# java面试

# JAVA基础和并发

## 1.JDK JRE JVM的区别和联系

JDK是开发者工具包，JRE是java运行时环境，是包含在JDK中的，而JVM是JRE的一部分，JRE中主要包含了JVM和LIB（加载需要的类库）。

![image-20210422111340521](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210422111340521.png)

编写的.java文件是通过jdk自带的java工具javac编译后生成.class文件，jvm在得到.class文件后通过调用jre中的类库来解释.class文件，将其翻译为机器码，映射到操作系统后供系统调用运行。

## 2. ==和Equals

==对比的是栈中的值，基本数据类型是变量值，引用类型是堆中内存对象的地址。

equals是Obeject中的方法，也是默认用==比较，但是通过重写的方式，使得可以直接比对具体的值，而不是引用。

example:

```java
public class StringDemo{
    public void main (String args[]){
        String str1 = "Hello";
        String str2 = new String("Hello");
        String str3 = str2; //引用传递
        System.out.println(str1==str2);//false
        System.out.println(str1==str3);//false
        System.out.println(str2==str3);//true
        System.out.println(str1.equals(str2));//true
        System.out.println(str1.equals(str3));//true
        System.out.println(str2.equals(str3));//true
    }
}
```

## 3.final

### 3.1简述final的作用

- 修饰类：表示类不可被继承
- 修饰方法：表示方法不可被覆盖，但是可以重载
- 修饰变量：表示变量一旦被赋值他的值就不能被更改。

(1) 修饰成员变量

- 如果final修饰的是类变量（静态变量），只能在静态初始化块中指定初始值或者声明该变量时指定初始值。
- 如果final修饰的是成员变量，可以在非静态初始化块、声明该变量或者构造器中执行初始值。

(2) 修饰局部变量

系统不会为局部变量进行初始化，局部变量必须由程序员显式初始化。因此使用final修饰局部变量时，即可以在定义时指定默认值，也可以不指定，然后在后面的代码中赋值，但是只能赋值一次。

```java
public class FinalVar{
    final static int a = 0;//在声明的时候就要赋值，或者静态代码块赋值
    /**
    static{
    	a = 0;
    }
    */
    final int b = 0;//在声明的时候就需要赋值，或者代码块中赋值，或者构造器赋值
    /*{
    	b = 0;
    }
    */
    public static void main(String args[]){
        final int localA;//局部变量没有初始化，不会报错，与final无关
        localA = 1;//在使用前要赋值
        //localA = 0;//不允许第二次赋值
    }
}
```



(3) 修饰基本类型数据和引用类型数据

- 如果是基本数据类型的变量，则其数值一旦在初始化之后便不能再更改；
- 如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。**但是引用的值是可以改变的。**

```java
public class FinalReferenceTest{
    public static void main(String args[]){
        final int[] iArr = {1,2,3,4};
        iArr[2] = -3;//合法
        iArr = null;//非法，对iArr不能重新赋值
        
        final Person p = new Person(25);
        p.setAge(24);//合法
        p = null;//非法
    }
}
```

### 3.2为什么局部内部类和匿名内部类只能访问局部final变量？

编译之后会生成两个class文件，Test.class Test1.class

```java
public class Test{
    public static void main(String args[]){
    }
    //局部final变量a,b
	public void test(final int b){
        final int a = 10;
        //匿名内部类
        new Thread(){
            public void run(){
                System.out.println(a);
                System.out.println(b);
            }
        }.start();
    }
}

class OutClass{
	private int age = 12;
    
    public void outPrint(final int x){
        class InClass{
            public void InPrint(){
                System.out.println(x);
                System.out.println(age);
            }
        }
        new InClass().InPrint();
    }
}
```

首先需要知道的一点是：内部类和外部类是处于同一个级别的，内部类不会因为定义在方法中就会随着方法的执行完毕就被销毁。

这里就会产生问题：当外部类的方法结束时，局部变量就被销毁了，但是内部类对象可能还存在(只有没有人再引用它时，它才会死亡)。这里就出现了一个矛盾：内部类对象访问了一个不存在的变量。为了解决这个问题，就将局部变量复制了一份作为内部类的成员变量，这样当局部变量死亡后，内部类依然可以访问它，实际访问的是局部变量的copy，这样就好像延长了局部变量的生命周期。



将局部变量复制为内部类的成员变量时，必须保证这两个变量是一样的，也就是我们在内部类中修改了成员变量，方法中的局部变量也得跟着改变，怎么解决问题呢？

因此，将局部变量设置成final，对它初始化后，这个变量就无法再修改，这就保证了内部类的成员变量和方法的局部变量的一致性。这实际上也是一种妥协，使得局部变量与内部类内建立的拷贝保持一致。



## 4.String StringBuffer和StringBuilder的区别和应用场景

String是final修饰的，不可变，每次操作都会产生新的String对象

StringBuffer和StringBuilder都是在原对象上操作

StringBuffer是线程安全的，StringBuilder是线程不安全的

StringBuffer方法都是synchronized修饰的

性能：StringBuilder>StringBuffer>String



场景：经常需要改变字符串内容时使用后面两个

优先使用StringBuilder,多线程使用共享变量时使用StringBuffer



## 5.重载和重写的区别

**重载：**发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同，发生在编译时。

**重写：**发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为private则子类就不能重写该方法。

```java
public int add(int a,String b)
public String add(int a, String b) //编译报错 单纯返回值不同不是重载   
```



## 6.接口和抽象类的区别

- 抽象类可以存在普通成员函数，而接口中只能存在public abstract方法
- 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的。
- 抽象类只能继承一个，接口可以实现多个。

接口是对行为的抽象，是该类可以具备什么行为。抽象类是对类本质的抽象，抽象类包含并实现子类的通用特性。

抽象类的功能要远超接口，与此同时，抽象类的代价相对接口也要高。



## 7.List和Set的区别

- List:有序，按对象进入的顺序保存对象，可重复，允许多个null元素对象，可以使用Iterator取出所有元素，再逐一遍历，还可以使用get(int index)获取指定下标的元素。
- Set:无序，不可重复，最多允许一个null元素对象，取元素时只能用Iterator接口取得所有元素，再逐一遍历各个元素。



## 8. hashCode和equals

### hashCode介绍

hashCode()的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode()定义在jdk的Obeject.java中，Java中的任何类都包含有hashCode()函数。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码(可以快速找到所需要的对象)。

### 为什么要有hashCode

#### 以“HashSet如何检查重复”为例子来说明为什么要有HashCode:

对象加入HashSet时，HashSet会计算对象的hashcode值来判断对象加入的位置，看该位置是否有值，如果没有、HashSet会假设对象没有重复出现。但是如果发现有值，这时会调用equals()方法来检查两个对象是否真的相同。如果两者相同，HashSet就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样就大大减少了equals的次数，相应就大大提高了执行速度。



- 如果两个对象相等，则hashCode一定也是相同的
- 两个对象相等，对两个对象分别调用equals方法都返回true
- 两个对象有相同的hashcode值，他们也不一定是相等的
- 因此，equals方法被覆盖过，则hashcode方法也必须被覆盖
- hashcode()的默认行为是对堆上的对象产生独特值。如果没有重写hashCode(),则该class的两个对象无论如何都不会相等(即使这两个对象指向相同的数据)



## 9. ArrayList和LinkedList的区别

ArrayList:基于**动态数组**，**连续内存**存储，适合**下标访问**，扩容机制：因为数组长度固定，超出长度存数据时需要新建数组，然后将老数组的数据拷贝到新数组，如果不是尾部插入数据还会涉及到元素的移动(往后移动一份，插入新元素)，使用尾插法并指定初始容量可以极大的提升性能，甚至超过linkedlist(需要创建大量的node对象)

LinkedList:基于**链表**，可以存储在**分散的内存**中，适合做数据插入及删除操作，不适合查询：需要逐一遍历

遍历LinkedList必须使用Iterator不能使用for循环，因为每次for循环体内通过get(i)取得某一元素时都需要对list重新进行遍历，性能消耗极大。

另外不要试图使用indexOf等返回元素索引，并利用其进行遍历，使用indexOf对list进行遍历当结果为空的时候会遍历整个列表。



## 10. HashMap和HashTable的区别？底层实现是什么？

区别：

(1)HashMap方法没有synchronized修饰，线程非安全，HashTable安全；

(2)HashMap允许key和value为null,而HashTable不允许



底层实现：

jdk8开始链表高度到8、数组长度超过64，链表转变为红黑树，元素以内部类node节点存在

- 计算key的hash值，二次hash然后对数组长度取模，对应到数组下标
- 如果没有产生hash冲突(下标位置没有元素)，则直接创建node存入数组
- 如果产生hash冲突，先进行equal比较，相同则取代该元素，不同，则判断链表高度插入链表，链表高度达到8，并且数组长度到64则转变为红黑树，长度低于6则将红黑树转回链表
- key为null，存在下标0的位置



## 11.ConcurrentHashMap原理，jdk7和jdk8版本的区别

jdk8:

数据结构：synchronized+CAS+Node+红黑树，Node的val和next都用volatile修饰，保证可见性

查找、替换、赋值都用CAS

锁：锁链表的head节点，不影响其他元素的读写，锁粒度更细，效率更高，扩容时，阻塞所有的读写操作、并发扩容

读操作无锁：

​	Node的val和next用volatile修饰，读写线程对该变量互相可见

​	数组用volatile修饰，保证扩容时被读线程可知

jdk7:

数据结构：ReentrantLock+Segment+HashEntry,一个Segment中包含一个HashEntry数组，每个HashEntry又是一个链表结构

元素查询：二次hash，第一次Hash定位到Segment,第二次Hash定位到元素所在的链表的头部

锁：Segment分段锁 Segment继承了ReetrantLock，锁定操作的Segment，其他的Segment不受影响，并发度为segment的个数，可以通过构造函数指定，数组扩容不会影响其他的segment

get方法无需加锁，volatile



## 12.什么是字节码，采用字节码的好处是什么？

**java中的编译器和解释器：**

java中引入了虚拟机的概念，即在机器和编译程序之间加入了一层抽象的虚拟的机器。这台虚拟的机器在任何平台上都提供给编译程序一个共同的接口。

编译程序只需要面向虚拟机，生成虚拟机可以理解的代码，然后由解释器来将虚拟机代码转换为特定系统的机器码执行。在java中，这种供虚拟机理解的代码叫做字节码(即扩展名为.class的文件)，它不面向任何特定的处理器，只面向虚拟机。

每一种平台的解释器是不同的，但实现的虚拟机是相同的。java源程序经过编译器编译后变成字节码，字节码由虚拟机解释执行，虚拟机将每一条要执行的字节码送给解释器，解释器将其翻译成特定机器上的机器码，然后再特定的机器上运行。这也就是解释了java的编译与解释并存的特点。

java源代码--->编译器--->jvm可执行的java字节码(即虚拟指令)--->jvm--->jvm中的解释器--->机器可执行的二进制机器码--->程序运行

**采用字节码的好处**:

java语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以java程序运行时比较高效，而且，由于字节码并不专对一种特定的机器，因此，java程序无需重新编译便可在多种不同的计算机上运行。



## 13.java类加载器有哪些

JDK自带的有三个类加载器：bootstrap ClassLoader、ExtClassLoader、AppClassLoader。

BootStrapClassLoader是ExtClassLoader的父类加载器，默认负责加载%JAVA_HOME%lib下的jar包和class文件。

ExtClassLoader是AppClassLoader的父类加载器，负责加载%JAVA_HOME%/lib/ext文件夹下的jar包和class类。

AppClassLoader是自定义类加载器的父类，负责加载classpath下的类文件。同时是一个系统类加载器和线程上下文加载器

继承ClassLoader实现自定义类加载器。



## 14.双亲委派模型

![image-20210423145048914](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210423145048914.png)

双亲委派模型的好处：

- 主要是为了安全性，避免用户自己编写的类动态替换java的一些核心类，比如String
- 同时也避免了类的重复加载，因为jvm中区分不同类，不仅仅是根据类名，相同的class文件被不同的ClassLoader加载就是不同的两个类



## 14.Java中的异常体系

java中的所有异常都来自顶级父类Throwable

Throwable下有两个子类Exception和Error

Error是程序无法处理的错误，一旦出现这个错误，则程序将被迫停止运行。

Exception不会导致程序停止，又分为两个部分RunTimeException运行时异常和CheckedException检查异常。

RunTimeException常常发生在程序运行过程中，会导致程序当前线程执行失败。CheckedException常常发生在程序编译过程中，会导致程序编译不通过。



## 15.GC如何判断对象可以被回收

- 引用计数法：每个对象有一个引用计数属性，新增一个引用时计数+1，引用释放时计数-1，计数为0时可以回收（可能出现A引用了B，B又引用了A，这时候就算他们都不再使用了，但因为互相引用，计数器=1永远无法被回收。）
- 可达性分析算法：从GC Roots开始向下搜索，搜索所走过的路径称为引用链。当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的，那么虚拟机就判断是可回收对象。



GC Roots的对象有：

- 虚拟机栈(栈帧中的本地变量表)中引用的对象
- 方法区中类静态属性引用的对象
- 方法区常量引用的对象
- 本地方法栈中JNI(即一般说的Native方法)引用的对象



可达性算法中的不可达对象并不是立即死亡的，对象拥有一次自我拯救的机会。对象被系统宣告死亡至少要经历两次标记过程:第一次是经过可达性分析发现没有与GC Roots相连接的引用链，第二次是由在虚拟机自动建立的Finalizer队列中判断是否需要执行finalize()方法。

当对象变成(GC Roots)不可达时，GC会判断该对象是否覆盖了finalize方法，若未覆盖，则直接将其回收。否则，若对象未执行过finalize方法，将其放入F-Queue队列，由一低优先级线程执行该队列中对象的finalize方法。执行finalize方法完毕后，GC会再次判断该对象是否可达，若不可达，则进行回收，否则对象"复活"

每个对象只能执行一次finalize方法。

由于finalize()方法运行代价高昂，不确定性大，无法保证各个对象的调用顺序，不推荐大家使用。



## 16.线程的生命周期，线程有哪些状态

1.线程通常有五种状态:创建，就绪，运行，阻塞，死亡。

2.阻塞的情况又分为三种：

(1) 等待阻塞：运行的线程执行**wait**方法，该线程会释放占用的所有资源，JVM会将该线程放入“等待池“中。进入这个状态后，是不能自动唤醒的，必须依靠其他线程调用**notify**或者**notifyAll**方法才能被唤醒，wait是object类的方法。

(2)同步阻塞：运行的线程在**获取对象的同步锁**时，若该同步锁被别的线程占用，则JVM会把该线程放入"锁池"中。

(3)其他阻塞：运行的线程执行sleep或join方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep状态超时、join等待线程终止或者超时、或者I/O处理完毕时，线程会重新转入就绪状态。sleep是Thread类的方法



1.新建状态(New):新创建了一个线程对象

2.就绪状态(Runnable):线程对象创建后，其他线程调用了该对象的start方法。该状态的线程位于可运行线程池中，变得可运行，等待获取cpu的使用权。

3.运行状态(Running):就绪状态的线程获取了CPU，执行程序代码

4.阻塞状态(Blocked):阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。

5.死亡状态(Dead):线程执行完了或者因异常退出了run方法，该线程结束生命周期。



## 17. sleep()、wait()、join()、yield()的区别

1.锁池

所有需要竞争同步锁的线程都会放在锁池当中，比如当前对象的锁被其中一个线程得到，则其他线程需要在这个锁池进行等待，当前面的线程释放同步锁后锁池中的线程去竞争同步锁，当某个线程得到后会进入就绪队列进行等待cpu资源分配。

2.等待池

当我们调用wait()方法后，线程会放到等待池当中，等待池的线程是不会去竞争同步锁。只有调用了notify()或notifyAll()后等待池的线程才会开始去竞争锁，notify()是随机从等待池选出一个线程放到锁池，而notifyAll()是将等待池的所有线程放到锁池当中去



1、sleep是Thread类的静态本地方法，wait则是Object类的本地方法。

2、sleep方法不会释放lock，但是wait会释放，而且会加入到等待队列中。

```
sleep就是将cpu的执行资格和执行权释放出去，不再运行此线程，当定时时间结束再取回cpu资源，参与cpu的调度，获取到cpu资源后就可以继续运行了，而如果sleep时该线程有锁，那么sleep不会释放这个锁，而是把锁带着进入了冻结状态，也就是说其他需要这个锁的线程根本不可能获取到这个锁，也就是说无法执行程序。如果在睡眠期间其他线程调用了这个线程的interrupt方法，那么这个线程也会抛出interruptexception异常返回，这点和wait是一样的。
```



3、sleep方法不依赖于同步器synchronized，但是wait需要依赖synchronized关键字。

4、sleep不需要被唤醒(休眠之后退出阻塞)，但是wait需要(不指定时间需要被别人中断)。

5、sleep一般用于当前线程休眠，或者轮询暂停操作，wait则多用于多线程之间的通信。

6、sleep会让出cpu执行时间且强制上下文切换，而wait则不一定，wait后可能还是会有机会重新竞争到锁继续执行的。



yield()执行后线程直接进入到就绪状态，马上释放了cpu的执行权，但是依然保留率cpu的执行资格，所以有可能cpu下次进行线程调度还会让这个线程获取到执行权继续执行。

join()执行后线程进入阻塞状态，例如在线程B中调用线程A的join(),那线程B会进入到阻塞队列，直到线程A结束或者中断线程

```java
public static void main(String args[]) throws InterruptedException{
    Thread t1 = new Thread(new Runnable(){
        @Override
        public void run(){
            try{
                Thread.sleep(3000);
            }catch(InterruptedException e){
                e.printStackTrace;
            }
            sout.("2222");
        }
    });
    t1.start();
    t1.join();
    //这行代码必须要等t1全部执行完毕才会执行
    sout("1111");
}
```





## 18. 说说你对线程安全的理解

线程安全本身应该是内存安全，堆是共享内存，可以被所有线程访问

```
当多个线程访问一个对象时，如果不用进行额外的同步控制或者其他的协调操作，调用这个对象的行为都可以获得正确的结果，我们就说这个对象是线程安全的
```

堆是进程和线程共有的空间，分全局堆和局部堆。全局堆就是所有没有分配的空间，局部堆就是用户分配的空间。堆在操作系统堆进程初始化的时候分配，运行过程中也可以向系统要额外的堆，但是用完了要还给操作系统，要不然就是内存泄漏。

```
在java中，堆是java虚拟机所管理的内存中最大的一块，是所有线程共享的一块内存区域，在虚拟机启动时创建，堆所存在的内存区域的唯一目的就说存放对象实例。几乎所有的对象实例以及数组都在这里分配内存。
```

栈是每个线程独有的，保存其运行状态和局部自动变量的。栈在线程开始的时候初始化，每个线程的栈互相独立，因此，栈是线程安全的。操作系统在切换线程的时候会自动切换栈。栈空间不需要在高级语言里显式的分配和释放。



目前主流操作系统都是多任务的，即多个进程同时运行。为了保证安全，每个进程只能访问分配给自己的内存空间，而不能访问别的进程的，这是由操作系统保障的。

在每个进程的内存空间中都会有一块特殊的公共区域，通常称为堆(内存)。进程内的所有线程都可以访问到该区域，这就是造成问题的潜在原因。



## 19.Thread、Runnable的区别

 Thread和Runnable的实质是继承关系，没有可比性。无论使用runnable还是Thread,都会new Thread然后执行Run方法，用法上，如果有复杂的线程操作需求，那就选择继承Thread，如果只是简单的执行一个任务，那就实现runnable。 



## 20.对守护线程的理解

守护线程：为所有的非守护线程提供服务的线程;任何一个守护线程都是整个JVM中所有非守护线程的保姆；守护线程类似于整个进程的一个默默无闻的小喽喽；它的生死无关重要，它却依赖整个进程运行；如果其他线程结束了，没有要执行的了，程序就结束了，守护线程也就直接被中断了。

注意：由于守护线程的终止是自身无法控制的，因此千万不要把IO、File等重要操作逻辑分配给它；因为它不靠谱。



守护线程的作用是什么？

例如：GC垃圾回收线程就是一个经典的守护线程，当我们的程序中不再有任何运行的Thread,程序就不会再产生垃圾，垃圾回收期也就无事可做，所以当垃圾回收线程是JVM上仅剩的线程时，垃圾回收线程会自动离开。它始终在低级别的状态中运行，用于实时监控和管理系统中的可回收资源。

应用场景：(1)来为其他线程提供服务支持的情况；(2)或者在任何情况下，程序结束时，这个线程必须正常且立刻关闭，就可以作为守护线程来使用。反之，如果一个正在执行某个操作的线程必须要正确的关闭掉否则就会出现不好的后果的话，那么这个线程就不能是守护线程，而是用户线程。通常是些关键的事务，比方说，数据库录入或者更新，这些操作都是不能中断的。

thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。

在Daemon线程中产生的新线程也是Daemon的。

守护线程不能用于访问固有资源，比如读写操作或者计算逻辑。因为它会在任何时候甚至在一个操作的中间发生中断。

java自带的多线程框架，比如ExecutorService，会将守护线程转换为用户线程，所以如果要使用后台线程就不能用java的线程池。



## 21. ThreadLocal的原理和使用场景

ThreadLocal 是**线程的局部变量， 是每一个线程所单独持有**的，其他线程不能对其进行访问。

**当使用ThreadLocal维护变量的时候 为每一个使用该变量的线程提供一个独立的变量副本，**即每个线程内部都会有一个该变量，这样同时多个线程访问该变量并不会彼此相互影响，因此他们使用的都是自己从内存中拷贝过来的变量的副本， 这样就不存在线程安全问题，也不会影响程序的执行性能。

但是要注意，虽然ThreadLocal能够解决上面说的问题，但是由于**在每个线程中都创建了副本，所以要考虑它对资源的消耗，比如内存的占用会比不使用ThreadLocal要大。**



每一个Thread对象均含有一个ThreadLocalMap类型的成员变量threadLocals，它存储本线程中所有ThreadLocal对象及其对应的值。

ThreadLocalMap由一个个entry对象构成

Entry继承自WeakReference<ThreadLocal<?>>,一个Entry由ThreadLocal对象和Object构成。由此可见，Entry的key是ThreadLocal对象，并且是一个弱引用。当没指向key的强引用后，该key就会被垃圾收集器回收

当执行set方法时，ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap对象。再以当前ThreadLocal对象为key，将值存储进ThreadLocalMap对象中。

get方法执行过程类似。ThreadLocal首先会获取当前线程对象，然后获取当前线程的ThreadLocalMap对象。再以当前ThreadLocal对象为key,获取对应的value。

由于每一条线程均含有各自**私有**的ThreadLocalMap容器，这些容器相互独立互不影响，因此不会存在线程安全性问题，从而也无需使用同步机制来保证多条线程访问容器的互斥性。

![image-20210426094945695](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210426094945695.png)

使用场景：

1、在进行对象跨层传递时，使用ThreadLocal可以避免多次传递，打破层次间的约束

2、线程间数据隔离

3、进行事务操作，用于存储线程事务信息。

4、数据库连接，Session会话管理。

```
Spring框架在事务开始时会给当前线程绑定一个JDBC Connection,在整个事务过程都是使用该线程绑定的connection来执行数据库操作，实现了事务的隔离性。Spring框架里面就是用的threadLocal来实现的这种隔离。
```



## 22.ThreadLocal内存泄漏原因，如何避免

内存泄漏为程序在申请内存后，无法释放已申请的内存空间，一次内存泄漏危害可以忽略，但内存泄漏堆积后果很严重，无论多少内存，迟早会被占光

不再会被使用的对象或者变量占用的内存不能被回收，就是内存泄漏



强引用：使用最普遍的引用(new),一个对象具有强引用，不会被垃圾回收器回收。当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，也不会回收这种对象。

如果想取消强引用和某个对象之间的关联，可以显式地将引用赋值为Null,这样可以使JVM在合适的时间就会回收该对象。

弱引用：JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象。在java中，用java.lang.ref.WeakReference类来表示。可以在缓存中使用弱引用。



ThreadLocal的实现原理，每一个Thread维护一个ThreadLocalMap,key为使用弱引用的ThreadLocal实例，Value为线程变量的副本。

![image-20210426094948268](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210426094948268.png)

threadLocalMap使用ThreadLocal的弱引用作为key,如果一个ThreadLocal不存在外部强引用时，key(ThreadLocal)势必会被GC回收，这样就导致ThreadLocalMap中key为null,而value还存在着强引用，只有thread线程退出以后，value的强引用链条才会断掉，但如果当前线程再迟迟不结束的话，这些key为null的Entry的value就会一直存在一条强引用链(红色链条)

key使用强引用

当threadLocalMap的key为强引用回收ThreadLocal时，因为ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。

key使用弱引用

当ThreadLocalMap的key为弱引用回收ThreadLocal时，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。当key为null,在下一次ThreadLocalMap调用set(),get(),remove()方法的时候会被清除value值。



因此，ThreadLocal内存泄漏的根源是:由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。

ThreadLocal正确的使用方法

- 每次使用完ThreadLocal都调用它的remove()方法清除数据
- 将ThreadLocal变量定义为private static,这样就一直存在ThreadLocal的强引用，也就能保证任何时候都能通过ThreadLocal的弱引用访问到Entry的value值，进而清除掉。 

## 23.并发、并行、串行的区别

串行在时间上不可能发生**重叠**，前一个任务没搞定，下一个任务就只能等着

并行在时间上是重叠的，两个任务**在同一时刻互不干扰的**同时执行

并发允许两个任务彼此干扰。同一时间点，只有一个任务运行，交替执行



## 24.并发的三大特性

原子性，有序性，可见性

- 原子性

原子性就是指一个操作中cpu不可以在中间暂停然后再调度，即不被终端操作，要么全部执行，要么都不执行。

在程序中原子性指的是最小的操作单元，比如自增操作，它本身其实并不是原子性操作，分了3步的，包括读取变量的原始值、进行+1操作、写入工作内存。所以在多线程中，有可能一个线程还没自增完，可能才执行到第二步，另一个线程就已经读取了值，导致结果错误。那如果我们能保证自增操作是一个原子性的操作，那么就能保证其他线程读取到的一定是自增后的数据。

**关键字：**synchronized 

- 有序性

虚拟机在进行代码编译时，对于那些改变顺序之后不会对最终结果造成影响的代码，虚拟机不一定会按照我们写的代码的顺序来执行，有可能将他们重排序。实际上，对于有些代码进行重排序之后，虽然对变量的值没有造成影响，但有可能会出现线程安全问题。

```java
int a = 0;
bool flag = false;

public void write(){
    a = 2;                //1
    flag = true;          //2
}

public void multply(){
    if(flag){             //3
        int ret = a*a;    //4
    }
}
```

write方法里的1和2做了重排序，线程1先对flag赋值为true,随后执行到线程2，ret直接计算出结果，再到线程1，这时候a才赋值为2，很明显迟了一步

**关键字：**volatile、synchronized



- 可见性

当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

若两个线程在不同的cpu，那么线程1改变了i的值还没刷新到主存，线程2又使用了i，那么这个i值肯定还是之前的，线程1对变量的修改线程没看到这就是可见性问题。

```java
//线程1
boolean stop = false;
while(!stop){
    doSomething();
}

//线程2
stop = true;

```

如果线程2修改了stop的值，线程1一定会停止吗？不一定。当线程2更改了stop变量的值之后，但是还没来得及写入主存当中，线程2转去做其他事情了，那么线程1由于不知道线程2对stop变量的更改，因此还会一直循环下去。

**关键字：**volatile、synchronized、final



## 25.为什么用线程池？解释下线程池参数？

1、降低资源消耗：提高线程利用率，降低创建和销毁线程的消耗。

2、提高响应速度：任务来了，直接有线程可以执行，而不是先创建线程再执行。

3、提高线程的客观理性：线程是稀缺资源，使用线程池可以统一分配调优监控。

- ***corePoolSize***代表核心线程数，也就是正常情况下创建工作的线程数，这些线程创建后并不会消除，而是一种常驻线程
- ***maxinumPoolSize***代表的是最大线程数，他与核心线程数相对应，表示最大允许被创建的线程数，比如当前任务较多，将核心线程数都用完了，还无法满足需求时，此时就会创建新的线程，但是线程池内线程总数不会超过最大线程数
- ***keepAliveTime***、***unit***表示超出核心线程数之外的线程的空闲存活时间，也就是核心线程不会消除，但是超出核心线程数的部分线程如果空闲一定的时间则会被消除，我们可以通过setKeepAliveTime来设置空闲时间。
- ***workQueue***用来存放待执行的任务，假如我们现在核心线程都已被使用，还有任务进来则全部放入队列，直到整个队列被放满但任务还在持续进入则会开始创建新的线程。
- ***ThreadFactory***实际上是一个线程工厂，用来生产线程执行任务。我们可以选择使用默认的创建工厂，产生的线程都在同一个组内，拥有同样的优先级，且都不是守护线程。当然我们也可以选择自定义线程工厂，一般我们会根据业务来指定不同的线程工厂。
- ***Handler***任务拒绝策略，有两种情况，第一种是当我们调用***shutdown***等方法关闭线程池后，这时候即使线程池内部还有没有执行完的任务正在执行，但是由于线程池已经关闭，我们再继续想线程池提交任务就会遭到拒绝。另一种情况就是当达到最大线程数，线程池已经没有能力继续处理新提交的任务时，这时也就拒绝



## 26.简述线程池处理流程

![image-20210426105915160](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210426105915160.png)



## 27.线程池中阻塞队列的作用？为什么是先添加队列而不是先创建最大线程？

1、一般的队列只能保证作为一个有线长度的缓冲区，如果超出了缓冲长度，就无法保留当前的任务了，**阻塞队列通过阻塞可以保留住当前想要继续入队的任务。**

阻塞队列可以保证任务队列中没有任务时阻塞获取任务的线程，使得线程进入wait状态，释放cpu资源。

阻塞队列自带阻塞和唤醒的功能，不需要额外处理，无任务执行时，线程池利用阻塞队列的take方法挂起，从而维持核心线程的存活、不至于一直占用cpu资源。



2、在创建新线程时，是要获取全局锁的，这个时候其他的就得阻塞，影响了整体效率。



## 28.线程池中线程复用原理

线程池将线程和任务进行解耦，线程是线程，任务是任务，摆脱了之前通过Thread创建线程时的一个线程必须对应一个任务的限制。

在线程池中，同一个线程可以从阻塞队列中不断获取新任务来执行，其核心原理在于线程池对Thread进行了封装，并不是每次执行任务都会调用Thread.start()来创建新线程，而是让每个线程去执行一个”循环任务”，在这个”循环任务“中不停检查是否有任务需要被执行，如果有则直接执行，也就是调用任务的run方法，将run方法当成一个普通的方法执行，通过这种方式只使用固定的线程就将所有任务的run方法串联起来。





## 29.非阻塞同步和CAS

**1、非阻塞同步**

非阻塞同步的意思是多个线程在竞争相同的数据时候不会发生阻塞，从而能够在更加细粒度的维度上进行协调，从而极大的减少线程调度的开销，从而提升效率。非阻塞算法不存在锁的机制也就不存在死锁的问题。

在基于锁的算法中，如果一个线程持有了锁，那么其他的线程将无法进行下去。使用锁虽然可以保证对资源的一致性访问，但是在挂起和恢复线程的执行过程中存在非常大的开销，如果锁上面存在着大量的竞争，那么有可能调度开销比实际工作开销还要高。

**2、悲观锁和乐观锁**

我们知道独占锁是一个悲观锁，悲观锁的意思就是假设最坏的情况，如果你不锁定该资源，那么就有其他的线程会修改该资源。悲观锁虽然可以保证任务的顺利执行，但是效率不高。

乐观锁就是假设其他的线程不会更改要处理的资源，但是我们在更新资源的时候需要判断该资源是否被别的线程所更改。如果被更改那么更新失败，我们可以重试，如果没有被更改，那么更新成功。

使用乐观锁的前提是假设大多数时间系统对资源的更新是不会产生冲突的。

乐观锁的原子性比较和更新操作，一般都是由底层的硬件支持的。

3、**CAS**

大多数的处理器都实现了一个CAS指令（compare and swap）,通常来说一个CAS接收三个参数，数据的现值V，进行比较的值A，准备写入的值B。只有当V和A相等的时候，才会写入B。无论是否写入成功，都会返回V。翻译过来就是“我认为V现在的值是A，如果是那么将V的值更新为B，否则不修改V的值，并告诉我现在V的值是多少。”

这就是CAS的含义，JDK中的并发类是通过使用Unsafe类来使用CAS的，我们可以自己构建一个并发类，如下所示：

```java
public class CasCounter {

    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
    private volatile int value;

    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                    (CasCounter.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    public CasCounter(int initialValue) {
        value = initialValue;
    }

    public CasCounter() {
    }

    public final int get() {
        return value;
    }

    public final void set(int newValue) {
        value = newValue;
    }

    public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
    }

}
```

上面的例子中，我们定义了一个原子操作compareAndSet， 它内部调用了unsafe的compareAndSwapInt方法。

看起来上面的CAS使用比直接使用锁复杂，但实际上在JVM中实现锁定时需要遍历JVM中一条非常复杂的代码路径，并可能导致操作系统级的锁定，线程挂机和上下文切换等操作。在最好的情况下，锁定需要执行一次CAS命令。

CAS的主要缺点就是需要调用者自己来处理竞争问题（重试，回退，放弃），而在锁中可以自动处理这些问题。

原子变量的底层就是使用CAS。




























# Spring

## 1.Spring是什么

轻量级的开源的J2EE框架。它是一个容器框架，用来装javabean(java对象)，中间层框架(万能胶)可以起一个连接作用，比如说把struts和hibernate粘合在一起使用，可以让企业开发更快更简洁。

Spring是一个轻量级的控制反转(IOC)和面向切面(AOP)的容器框架

- 从大小和开销两方面而言Spring都是轻量级的
- 通过控制反转(IOC)的技术达到松耦合的目的
- 提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发
- 包含并管理应用对象(Bean)的配置和生命周期，这个意义上是一个容器。
- 将简单的组件配置、组合成为复杂的应用，这个意义上是一个框架。



## 2.谈谈你对AOP(Aspect Oriented Programming)的理解

系统是由许多不同的组件所组成的，每一个组件各负责一块特定功能，除了实现自身核心功能之外，这些组件还经常承担着额外的职责。例如日志、事务管理和安全这样的核心服务经常融入到自身具有核心业务逻辑的组件中去。

这些系统服务通常被成为横切关注点，因为它们会跨越系统的多个组件。

当我们需要为分散的对象引入公共行为的时候，OOP就显得无能为力，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。

日志代码往往水平的散步在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。

在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。

AOP：将程序中的交叉业务逻辑(比如安全，日志，事务等)封装成一个切面，然后注入到目标对象(具体业务逻辑)中去。AOP可以对某个对象或某些对象的功能进行增强，比如对象中的方法进行增强，可以在执行某个方法之前额外的做一些事情，在某个方法执行之后额外的做一些事情



## 3.谈谈你对IOC(Inversion of control)的理解

容器概念:

ioc容器：实际上就是个map(key,value)，里面存的是各种对象(在xml里配置的bean节点、@repository、@service、@controller、@component)，在项目启动的时候会读取配置文件里面的bean节点，根据全限定类名使用反射创建对象放到map里，扫描到打上上述注解的类还是通过反射创建对象到map里。

这个时候map里就有各种对象了，接下来我们在代码里需要用到里面的对象时，再通过DI(依赖注入)注入(autowired、resource等注解，xml里面的bean节点内的ref属性，项目启动的时候会读取xml节点ref属性根据id注入，也会扫描这些注解，根据类型或id注入;id就是对象名)。



控制反转：

没有引入IOC容器之前，对象A依赖于对象B，那么对象A在初始化或者运行到某一点时，自己必须主动去创建对象B或使用已创建的对象B。无论是创建还是使用对象B，控制权都在自己手上。

引入IOC容器之后，对象A和对象B之间失去了直接联系，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。

通过前后的对比，不难看出来：对象A获得依赖对象B的过程，由主动行为变为了被动行为，控制权颠倒过来了，这就是”控制反转“这个名称的由来。

全部对象的控制权全部上缴给"第三方"IOC容器，所以，IOC容器成了整个系统的关键核心，它起到了一种类似"粘合剂"的作用，把系统中的所有对象粘合在一起发挥作用，如果没有这个"粘合剂",对象与对象之间会彼此失去联系，这就是有人把IOC容器比喻成"粘合剂"的由来。





## 4.如何实现一个IOC容器

1、配置文件配置包扫描路径

2、递归包扫描获取.class文件

3、反射、确定需要交给IOC管理的类

4、对需要注入的类进行依赖注入



- 配置文件中指定需要扫描的包路径
- 定义一些注解，分别表示访问控制层、业务服务层、数据持久层、依赖诸如注解、获取配置文件注解
- 从配置文件中获取需要扫描的包路径，获取到当前路径下的文件信息及文件夹信息，我们将当前路径下所有以.class结尾的文件添加到一个set集合中进行存储
- 遍历这个SET集合，获取在类上有指定注解的类，并将其交给IOC容器，定义一个安全的MAP用来存储这些对象
- 遍历这个IOC容器，获取到每一个类的实例，判断里面是否有依赖其他类的实例，然后进行递归注入



## 5.BeanFactory和ApplicationContext有什么区别？

ApplicationContext是BeanFactory的子接口

ApplicationContext提供了更完整的功能;

1. 继承MessageSource,因此支持国际化
2. 统一的资源文件访问方式
3. 提供在监听器中注册bean的事件
4. 同时加载多个配置文件
5. 载入多个(有继承关系)上下文，使得每个上下文都专注于一个特定的层次，比如应用的Web层



- BeanFactory采用的是延迟加载形式来注入Bean的，即只有在使用到某个bean时(调用GetBean())，才对该Bean进行加载实例化。这样，我们就不能发现一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFactory加载后，直至第一次使用调用getBean方法才会抛出异常。
- ApplicationContext，它是容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入。ApplicationContext启动后与载入所有的单实例Bean,通过预载入单实例bean,确保当你需要的时候，你就不用等待，因为它们已经创建好了。
- 相对于基本的BeanFactory,ApplicationContext唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。
- BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader
- BeanFactory和ApplicationContext都支持BeanPostPorcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是:BeanFactory需要手动注册，而ApplicationContext则是自动注册。



## 6.描述一下Spring Bean的声明周期

1. 解析类得到BeanDefinition
2. 如果有多个构造方法，则要推断构造方法
3. 确定好构造方法后，进行**实例化**得到一个对象
4. 对对象中的加了@Autowired注解的属性进行**属性填充**
5. 回调Aware方法，比如BeanNameAware,BeanFactoryAware
6. 调用BeanPostProcessor的**初始化前**的方法
7. 调用**初始化**方法
8. 调用BeanPostProcessor的**初始化后**的方法，在这里会进行**AOP**
9. 如果当前创建的bean是单例的则会把bean放入单例池
10. 使用bean
11. Spring容器关闭时调用DisposableBean中的destory()方法



## 7.解释下Spring支持的几种bean的作用域

- singleton: 默认，每个容器只有一个bean的实例，单例的模式由BeanFactory自身来维护。该对象的生命周期是与Spring IOC容器一致的(但在第一次被注入时才会创建)。
- prototype:为每一个bean请求提供一个实例。在每次注入时都会创建一个新的对象
- request:bean被定义为在每个HTTP请求中创建一个单例对象，也就是说在单个请求中都会复用这一个单例对象。
- session:与request范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效
- application:bean被定义为在ServletContext的生命周期中复用一个单例对象
- websocket:bean被定义为在websocket的生命周期中复用一个单例对象



global-session:全局作用域，global-session和Portlet(现在很少用)应用相关。当你的应用部署在Portlet容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存储在global-session中。全局作用域与Servlet中的session作用域效果相同。



## 8.Spring框架中的单例Bean是线程安全的吗

Spring中的Bean默认是单例模式的，框架中并没有对bean进行多线程的封装处理

如果bean是有状态的，那就需要开发人员自己来进行线程安全的保证，最简单的方法就是改变bean的作用域，把”singleton"改为"prototype",这样每次请求Bean就相当于new Bean()这样就可以保证线程的安全了。

- 有状态就是有数据存储功能
- 无状态就是不会保存数据          controller、service和dao层本身并不是线程安全的，如果只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制变量，这时自己的线程的工作内存，是安全的

Dao(Data Access Object数据存取对象)会操作数据库Connection,connection是带有状态的，比如说数据库事务，Spring的事务管理器使用ThreadLocal为不同线程维护了一套独立的connection副本，保证线程之间不会互相影响(Spring是如何保证事务获取同一个connection的)

不要在bean中声明任何有状态的实例变量或者类变量，如果必须如此，那么就使用ThreadLocal把变量变为线程私有的，如果bean的实例变量或类变量需要在多个线程之间共享，那么就只能使用synchronized、lock、CAS等这些实现线程同步的方法了。







## 9.Spring框架中都用到了哪些设计模式

简单工厂：有一个工厂类根据传入的参数，动态的决定应该创建哪一个产品类。

```
Spring中的BeanFactory就是简单工厂模式的实现。根据传入一个唯一的标识来获得bean对象，但是否是在传入参数后创建还是传入参数前创建要根据具体情况来定。
```



工厂方法：

```
实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是spring会在使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是这个bean.getObject()方法的返回值。
```



单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问点

```
spring对单例的实现：spring中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。但没有从构造器级别去控制单例，这是因为spring管理的是任意的java对象。
```



适配器模式:

```
spring定义了一个适配接口，使得每一种Controller有一种对应的适配器实现类，让适配器代替controller执行相应的方法，这样在扩展Controller时，只需要增加一个适配器类就完成了SpringMVC的扩展了。
```



装饰器模式：动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类更为灵活。

```
spring中用到的包装器模式在类名上有两种表现，一种是类名中含有wrapper,另一种是类名中含有Decorator。
```



动态代理：

```
切面在应用运行的时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象创建动态的一个代理对象，SpringAOP就是以这种方式织入切面的。
```



 观察者模式：

```
spring的事件驱动模型使用的是观察者模式，Spring中Observer模式常用的是listener的实现。
```



策略模式：

```
Spring框架的资源访问Resource接口，该接口提供了更强的资源访问能力，Spring框架
```



## 10.Spring事务的实现方式和原理以及隔离级别？

在使用spring框架时，可以有两种使用事务的方式，一种是编程式的，一种是申明式的，@Transactional注解就是申明式的。

首先，事务这个概念是数据库层面的，Spring只是基于数据库中的事务进行了拓展，以及提供了一些能让程序员更加方便操作事务的方式。

比如我们可以通过在某个方法上增加@Transactional注解，就可以开启事务，这个方法中所有的sql都会在一个事务中执行，统一成功或失败。

在一个方法上加了@Transactional注解后，Spring会基于这个类生成一个代理对象，会将这个代理对象作为bean，当在使用这个代理对象的方法时，如果这个方法上存在@Transactional注解，那么代理逻辑会先把事务的自动提交设置为false,然后再去执行原本的业务逻辑方法，如果执行业务逻辑方法没有出现异常，那么代理逻辑中就会将事务进行提交，如果执行业务逻辑方法出现了异常，那么则会将事务进行回滚。

当然，针对哪些异常回滚事务是可以配置的，可以利用@Transactional注解中的rollbackFor属性进行配置，默认情况下回对RuntimeException和Error进行回滚。



spring事务隔离级别就是数据库的隔离级别:外加一个默认级别

- read uncommitted(未提交读)
- read committed(提交读、不可重复读)
- repeatable read(可重复读)
- serializable(可串行化)

```
数据库的配置隔离及级别是Read Commited,而spring配置的隔离级别是Repeatable Read,请问这时隔离级别是以哪一个为准？
以Spring配置为准，如果Spring配置的隔离级别数据库不支持，效果取决于数据库
```



## 11.Spring事务传播机制

多个事务方法互相调用时，事务如何在这些方法间传播

```
方法A是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型决定。
```

REQUIRED(Spring默认的事务传播类型):如果当前没有事务，则自己新建一个事务，如果当前存在事务，则加入这个事务

SUPPORTS:当前存在事务，则加入当前事务，如果当前没有事务，就以非事务方法执行

MANDATORY:当前存在事务，则加入当前事务，如果当前事务不存在，则抛出异常。

REQUIRES_NEW:创建一个新事务，如果存在当前事务，则挂起该事务。

NOT_SUPPORTED:以非事务执行，如果当前存在事务，则挂起当前事务

NEVER:不使用事务，如果当前事务存在，则抛出异常

NESTED:如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作一样(开启一个事务)

```
和REQUIRES_NEW的区别
REQUIRES_NEW是新建一个事务并且新开启的这个事务与原有事务无关，而NESTED则是当前存在事务时(我们把当前事务称之为父事务)会开启一个嵌套事务(称之为一个子事务)。而NESTED情况下父事务回滚时，子事务也会回滚，而在REQUIRES_NEW情况下，原有事务回滚，不会影响新开启的事务。

和REQIRED的区别
REQUIRED情况下，调用方存在事务时，则被调用方和调用方使用同一事务，那么被调用方出现异常时，由于共用一个事务，所以无论调用方是否catch其异常，事务都会回滚，而在NESTED情况下，被调用方发生异常时，调用方可以catch其异常，这样只有子事务回滚，父事务不受影响。
```



## 12.spring事务什么时候会失效

spring事务的原理是AOP，进行了切面增强，那么失效的根本原因是这个AOP不起作用了。常见情况如下：

1、发生自调用，类里面使用this调用本类的方法(this通常省略)，此时这个this对象不是代理类，而是UserService对象本身

解决方法:让那个this变成UserService的代理类(@Autowired注入的对象)即可

2、方法不是public的

```
@Transactional只能用于public的方法上，否则事务不会生效，如果要用在非public方法上，可以开启AspectJ代理模式。
```

3、数据库不支持事务

4、没有被spring管理

5、异常被吃掉，事务不会回滚(或者抛出的异常没有被定义，默认为RunTimeException)



## 13.什么是bean的自动装配，有哪些方式

开启自动装配，只需要在xml文件<bean>中定义"autowire"属性

```xml
<bean id="customer" class="com.xxx.xxx.Customer" autowire="" />
```

autowire属性有五种装配的方式:

- no - 缺省情况下，自动配置是通过“ref"属性手动设定

```
手动装配:以value或ref的方式明确指定属性值都是手动装配
需要通过'ref'属性来连接bean
```

- byName-根据bean的属性名称进行自动装配

```
Customer的属性名称是person,spring会将bean id为Person的bean通过setter方法进行自动装配
<bean id="customer" class="com.xxx.xxx.Customer" autowire="byName"/>
<bean id="person" class="com.xxx.xxx.Person"/>
```

- byType-根据bean的类型进行自动装配

````
Customer的属性person的类型为Person,spring会将Person类型通过Setter方法进行自动装配
<bean id="Customer" class="com.xxx.xxx.Customer" autowire="byType">
<bean id="person" calss="com.xxx.xxx.Person"/>
````

- constructor-类似byType,不过是应用于构造器的参数。如果一个bean与构造器参数的类型相同，则进行自动装配，否则导致异常

```
Customer构造函数的参数person的类型为Person,Spring会将Person类型通过构造方法进行自动装配。
<bean id="customer" class="com.xxx.xxx.Customer" autowire="constructor"/>
<bean id="person" class="com.xxx.xxx.Person"/>
```

- autodetect-如果有默认的构造器，则通过constructor方式进行自动装配，否则使用byType方式进行自动装配

```
如果有默认的构造器，则通过constructor方式进行自动装配，否则使用byType方式进行自动装配。
```

@Autowired自动装配bean，可以在字段、setter方法、构造函数上使用。



## 14.Spring Boot、Spring MVC和Spring有什么区别

spring是一个IOC容器，用来管理bean，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供AOP机制弥补OOP的代码重复问题，更方便将不同类不同方法中的共同处理抽取成切面、自动注入给方法执行，比如日志、异常等

springMVC是spring对web框架的一个解决方案，提供了一个总的前端控制器Servlet，用来接收请求，然后定义了一套路由策略(url到handler的映射)及适配执行handle，将handle结果使用视图解析技术生成视图展现给前端



springboot是spring提供的一个快速开发工具包，让程序员能更方便、更快速的开发Spring+SpringMVC应用，简化了配置(约定了默认配置),整合了一系列的解决方案(starter机制)、redis、mongodb、es，可以开箱即用



## 15.SpringMVC工作流程

1. 用户发送请求至前端控制器DispatcherServlet。
2. DispathcerServlet收到请求调用HandlerMapping处理器映射器
3. 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成)一并返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter处理器适配器
5. HandlerAdapter经过适配器调用具体的处理器(Controller,也叫后端控制器)
6. Controller执行完成返回ModelAndView。
7. handlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8. DispatcherServlet将ModelAndView传给ViewResolver视图解析器
9. ViewResolver解析后返回具体view
10. DispatcherServlet根据view进行渲染视图(即将模型数据填充至视图中)
11. DispatcherServlet响应用户



## 16.SpringMVC的主要组件

Handler:也就是处理器。它直接应对着MVC中的C也就是Controller层，它的具体表现形式有很多，可以是类，也可以是方法。在Controller层中@RequestMapping标注的所有方法都可以看成是一个handler，只要可以实际处理请求就可以是Handler

*1、HandlerMapping

initHandlerMappings(context)，处理器映射器，根据用户请求到的资源url来查找Handler。在springMVC中会有很多请求，每个请求都需要一个Handler处理，具体接收到一个请求之后使用哪个Handler进行，这就是HandlerMapping需要做的事。

*2、HandlerAdapter

initHandlerAdaters(context)，适配器。因为SpringMVC中的Handler可以是任意的形式，只要能处理请求就Ok,但是Servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方法。如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢？这就是HandlerAdapter要做的事情。

Handler是用来干活的工具，HandlerMapping用于根据需要干的活找到相应的工具；HandlerAdapter是使用工具干活的人

3、HandlerExceptionResolver

initHandlerExceptionResoolvers(context),其他组件都是用来干活的。在干活的过程中难免会出现问题，出问题后怎么办呢？这就需要有一个专门的角色对异常情况进行处理，在springMVC中就是HandlerExceptionResolver。具体来说，此组件的作用是根据异常设置ModelAndView，之后再交给render方法进行渲染

4、ViewResolver

initViewResolvers(context),ViewResolver用来将String类型的视图名和locale解析为View类型的视图，View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html(也可能是其他类型)文件。这里就有两个关键问题:使用哪个模板？

...待续



## 17.SpringBoot自动配置原理

@Import + @Configuration + Spring spi

自动配置类由各个Starter提供，使用@Configuration + @Bean定义配置类，放到META-INF/spring.factories下使用Spring spi扫描META-INF/spring.factories下的配置类

使用@Import导入自动配置类

![image-20210427110022920](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210427110022920.png)



## 18.如何理解Spring Boot中的starter

使用spring+springMVC使用，如果需要引入mybatis等框架，需要到xml中定义mybatis需要的bean

starter就是定义一个starter的jar包，写一个@Configuration配置类，将这些bean定义在里面，然后在Starter包的META-INF/spring.factories中写入该配置类，springboot会按照约定来加载该配置类

开发人员只需要将相应的starter包依赖进应用，进行相应的属性配置(使用默认配置时，不需要配置)，就可以直接进行代码开发，使用对应的功能了，比如mybatis-spring-boot-starter,spring-boot-starter-redis



## 19.什么是嵌入式服务器？为什么要使用嵌入式服务器？

节省了下载安装tomcat，应用也不需要再打war包然后放到webapp目录下才能运行。

只需要一个安装了java的虚拟机，就可以直接在上面部署应用程序了

springboot已经内置了tomcat.jar，运行main方法时会去启动tomcat，并利用tomcat的spi机制加载SpringMVC



# MYBATIS

**一、概述**

- Mybatis是一个优秀的持久层框架，它对JDBC操作数据库的过程进行封装，使开发者只需要关注sql本身。
- 我们原来使用JDBC操作数据库，需要手动的写代码去注册驱动、获取connection、获取statement等等，现在Mybaits帮助我们把这些事情做了，我们只需要关注我们的业务sql即可，这样可以提高我们的开发效率。
- MyBatis属于半自动的ORM框架

**二、Mybatis架构**

![img](https://img2020.cnblogs.com/blog/1143961/202004/1143961-20200402174759572-1420567684.png)

 

 

- SqlMapConfig.xml
  - SqlMapConfig.xml文件是Mybatis的全局配置文件，配置了Mybatis的运行环境等信息
- Mapper.xml
  - Mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml文件中配置加载
- SqlSessionFacory
  - 通过SqlMapConfig.xml文件里面的环境配置信息构造SqlSessionFactory会话工厂，用来生产和管理SqlSession
- SqlSsession
  - 由SqlSessionFactory工厂创建SqlSession会话对象，SqlSession用来操作数据库
- Executor
  - MyBatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个基本执行器，一个缓存执行器
  - 我们前期学习MyBatis暂时不用关注这个
- MappedStatement
  - MappedStatement也是MyBatis的一个底层封装对象，它包装了MyBatis配置信息及sql映射信息等。
  - Mapper.xml中一个sql对应一个MappedStatement对象，sql的id即是MappedStatement的id
  - 这个暂时不用关注
- 输入映射
  - 输入映射我们可以理解为执行sql语句所需的参数
- 输出映射
  - 输出映射指的是操作数据库之后返回的结果

**三、Mybatis对比JDBC的优点**

- 我们先看一下JDBC操作数据库的大致流程：
  - 1.加载数据库驱动
  - 2.创建并获取数据库连接对象connection
  - 3.通过连接对象获取会话对象statement
  - 4.编写sql语句
  - 5.如果有参数的话需要通过PreparedStatement设置参数
  - 5.执行sql语句并获取结果
  - 6.关闭资源
- 上述是最原始的JDBC操作数据库的方式，有以下问题：
  - 数据库连接的频繁创建、释放浪费资源进而影响系统性能，如果使用数据库连接池的话可以解决此问题
  - Sql语句写在代码中，代码不易维护。如果在开发过程中我们改动某个sql，就需要去修改java代码，改完之后还需要重新编译。
  - 对结果集的解析也是硬编码，sql变化会导致解析结果的代码也跟着变化，系统不易维护
- 使用MyBatis
  - MyBatis会帮我们把加载驱动、获取连接等过程进行封装，我们不再需要关注这些，只需要关注业务逻辑本身的sql即可，提高开发效率
  - MyBatis的sql语句在xml文件里面编写，改变sql语句不再需要重新编译



## 1. mybatis的优缺点

优点:

1、基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。

2、与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；

3、很好的与各种数据库兼容(因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持)

4、能够与Spring很好的集成

5、提供映射标签，支持对象与数据库的ORM字段关系映射;提供对象关系映射标签，支持对象管理组件维护



缺点:

1、SQL语句的编写工作量较大，尤其当字段多、关联表多的时候，对开发人员编写SQL语句的功底有一定要求。

2、SQL语句依赖于数据库，导致**数据库移植性差**，**不能随意更换数据库**。



## 2.mybatis与Hibernate对比

***SQL和ORM的争论，永远不会停止***

开发速度的对比：

Hibernate的真正掌握要比MyBatis难些。Mybatis框架相对简单更容易上手，但也相对简陋些。

比起两者的开发速度，不仅要考虑到两者的特性以及性能，更要根据项目需求去考虑究竟哪一个更适合项目开发，比如：一个项目中用到的复杂查询基本没有，就是简单的增删改查，这样选择hibernate效率就很快了，因为基本的SQL语句已经封装好了，根本不需要你去写sql语句，这就节省了大量的时间，但是对于一个大型项目，复杂语句较多，这样再去选择hibernate就不是一个太好的选择，选择mybatis就会加快很多，而且语句的管理也比较方便。

开发工作量的对比：

hibernate和MyBatis都有相应的代码生成工具，可以生成简单基本的DAO层方法。针对高级查询，Mybatis需要手写SQL语句，以及ResultMap。而Hibernate有良好的映射机制，开发者无需关心SQL的生成和结果映射，可以更专注于业务流程

sql优化方面：

HIbernate的查询会将表中的所有字段查询出来，这一点会有性能消耗。Hibernate也可以自己写SQL来指定需要查询的字段，但这样就破化了Hibernate开发的简洁性。而Mybatis的SQL是手动编写的，所以可以按需求指定查询的字段。

Hibernate HQL语句的调优需要将SQL打印出来，而Hibernate的SQL被很多人嫌弃因为太丑了。MyBatis的SQL是自己手动写的所以调整方便，但HIbernate有自己的日志统计。Mybatis本身不带日志统计，使用Log4j进行日志记录

对象管理的对比：

Hibernate是完整的对象/关系映射解决方案，它提供了对象状态管理(state management)的功能，使开发者不再需要理会底层数据库系统的细节。也就是说，相对于常见的JDBC/SQL持久层方案中需要管理SQL语句，Hibernate采用了更自然的面向对象的视角来持久化java应用中的数据。

换句话说，使用hibernate的开发者应该总数关注对象的状态(state)，不必考虑SQL语句的执行。这部分细节已经由Hibernate掌管妥当，只有开发者再进行系统性能调优的适合才需要进行了解。而MyBatis在这一块没有文档说明，用户需要对对象自己进行详细的管理。

缓存机制对比：

相同点：都可以实现自己的缓存或使用其他第三方缓存方案，创建适配器来完全覆盖缓存行为。

不同点：Hibernate的二级缓存配置在SessionFactory生成的配置文件中进行详细配置，然后再在具体的表-对象映射中配置是哪种缓存。



## 3.#{}和${}的区别是什么

#{}是预编译处理，是占位符，${}是字符串替换，是拼接符。

Mybatis在处理#{}时，会将sql中的#{}替换为？号，调用PreparedStatement来赋值。

Mybatis在处理${}时，就是把${}替换成变量的值，调用Statement来赋值;

#{}的变量替换是在DBMS中，变量替换后，#{}对应的变量自动加上单引号

${}的变量替换是在DBMS外，变量替换后，#{}对应的变量不会加上单引号

使用#{}可以有效的防止SQL注入，提高系统安全性。



## 4.**简述** **Mybatis** **的插件运行原理，如何编写一个插件。**

答： Mybatis 只支持针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这4 种接口的插件， Mybatis 使用 JDK 的动态代理， 为需要拦截的接口生成代理对象以实现接口方法拦截功能， 每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的invoke() 方法， 拦截那些你指定需要拦截的方法。



编写插件： 实现 Mybatis 的 Interceptor 接口并复写 intercept()方法， 然后在给插件编写注解， 指定要拦截哪一个接口的哪些方法即可， 在配置文件中配置编写的插件。

```java
@Intercepts({@Signature(type = StatementHandler.class, method = "query", args = {Statement.class, ResultHandler.class}), @Signature(type = StatementHandler.class, method = "update", args = {Statement.class}),
@Signature(type = StatementHandler.class, method = "batch", args = { Statement.class })}) 
@Component 
invocation.proceed()执行具体的业务逻辑
```





# MYSQL

## 1.索引的基本原理

索引用来**快速地寻找那些具有特定值的记录**。如果没有索引，一般来说执行查询时遍历整张表。

索引的原理：就是把无序的数据变成有序的查询

1. 把创建了索引的列的内容进行排序

2. 对排序结果生成倒排表

3. 在倒排表内容上拼上数据地址链

4. 在查询的时候，先拿到倒排表内容，再取出数据地址链，从而拿到具体数据



## 2. mysql 聚簇和非聚簇索引的区别

都是B+树的数据结构

- 聚簇索引：将数据存储与索引放到了一块、并且是按照一定的顺序组织的，找到索引也就找到了数据，数据的物理存放顺序与索引顺序是一致的，即：只要索引是相邻的，那么对应的数据一定也是相邻地存放在磁盘上的

- 非聚簇索引：叶子节点不存储数据、存储的是数据行地址，也就是说根据索引查找到数据行的位置再取磁盘查找数据，这个就有点类似一本树的目录，比如我们要找第三章第一节，那我们先在这个目录里面找，找到对应的页码后再去对应的页码看文章。

```
优势： 
1、查询通过聚簇索引可以直接获取数据，相比非聚簇索引需要第二次查询（非覆盖索引的情况下）效率要高
2、聚簇索引对于范围查询的效率很高，因为其数据是按照大小排列的 
3、聚簇索引适合用在排序的场合，非聚簇索引不适合

劣势： 
1、维护索引很昂贵，特别是插入新行或者主键被更新导至要分页(page split)的时候。建议在大量插入新行后，选在负载较低的时间段，通过OPTIMIZETABLE优化表，因为必须被移动的行数据可能造成 碎片。使用独享表空间可以弱化碎片 
2、表因为使用UUId（随机ID）作为主键，使数据存储稀疏，这就会出现聚簇索引有可能有比全表扫面 更慢，所以建议使用int的auto_increment作为主键 
3、如果主键比较大的话，那辅助索引将会变的更大，因为辅助索引的叶子存储的是主键值；过长的主键值，会导致非叶子节点占用占用更多的物理空间
```

InnoDB中一定有主键，主键一定是聚簇索引，不手动设置、则会使用unique索引，没有unique索引，则会使用数据库内部的一个行的隐藏id来当作主键索引。在聚簇索引之上创建的索引称之为辅助索引，辅助索引访问数据总是需要二次查找，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引，辅助索引叶子节点存储的不再是行的物理位置，而是主键值

MyISM使用的是非聚簇索引，没有聚簇索引，非聚簇索引的两棵B+树看上去没什么不同，节点的结构完全一致只是存储的内容不同而已，主键索引B+树的节点存储了主键，辅助键索引B+树存储了辅助键。表数据存储在独立的地方，这两颗B+树的叶子节点都使用一个地址指向真正的表数据，对于表数据来说，这两个键没有任何差别。由于索引树是独立的，通过辅助键检索无需访问主键的索引树。

如果涉及到大数据量的排序、全表扫描、count之类的操作的话，还是MyISAM占优势些，因为索引所占空间小，这些操作是需要在内存中完成的。



## 3.**mysql**索引的数据结构，各自优劣

索引的数据结构和具体存储引擎的实现有关，在MySQL中使用较多的索引有Hash索引，B+树索引等，InnoDB存储引擎的默认索引实现为：B+树索引。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝大多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余大部分场景，建议选择BTree索引。

B+树：

B+树是一个平衡的多叉树，从根节点到每个叶子节点的高度差值不超过1，而且同层级的节点间有指针相互链接。在B+树上的常规检索，从根节点到叶子节点的搜索效率基本相当，不会出现大幅波动，而且基于索引的顺序扫描时，也可以利用双向指针快速左右移动，效率非常高。因此，B+树索引被广泛应用于数据库、文件系统等场景。

![image-20210427143416888](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210427143416888.png)

哈希索引：

哈希索引就是采用一定的哈希算法，把键值换算成新的哈希值，检索时不需要类似B+树那样从根节点到叶子节点逐级查找，只需一次哈希算法即可立刻定位到相应的位置，速度非常快

![image-20210427143437975](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210427143437975.png)

如果是等值查询，那么哈希索引明显有绝对优势，因为只需要经过一次算法即可找到相应的键值；前提是键值都是唯一的。如果键值不是唯一的，就需要先找到该键所在位置，然后再根据链表往后扫描，直到找到相应的数据；

如果是范围查询检索，这时候哈希索引就毫无用武之地了，因为原先是有序的键值，经过哈希算法后，有可能变成不连续的了，就没办法再利用索引完成范围查询检索；

哈希索引也没办法利用索引完成排序，以及like ‘xxx%’ 这样的部分模糊查询（这种部分模糊查询，其实本质上也是范围查询）；

哈希索引也不支持多列联合索引的最左匹配规则；

B+树索引的关键字检索效率比较平均，不像B树那样波动幅度大，在有大量重复键值情况下，哈希索引的效率也是极低的，因为存在哈希碰撞问题。



## 4.索引设计的原则？

查询更快、占用空间更小

1. 适合索引的列是出现在where子句中的列，或者连接子句中指定的列

2. 基数较小的表，索引效果较差，没有必要在此列建立索引

3. 使用短索引，如果对长字符串列进行索引，应该指定一个前缀长度，这样能够节省大量索引空间，如果搜索词超过索引前缀长度，则使用索引排除不匹配的行，然后检查其余行是否可能匹配。

4. 不要过度索引。索引需要额外的磁盘空间，并降低写操作的性能。在修改表内容的时候，索引会进行更新甚至重构，索引列越多，这个时间就会越长。所以只保持需要的索引有利于查询即可。

5. 定义有外键的数据列一定要建立索引。

6. 更新频繁字段不适合创建索引

7. 若是不能有效区分数据的列不适合做索引列(如性别，男女未知，最多也就三种，区分度实在太低)
8. 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可。

9. 对于那些查询中很少涉及的列，重复值比较多的列不要建立索引。

10. 对于定义为text、image和bit的数据类型的列不要建立索引。



## 5.**锁的类型有哪些**

基于锁的属性分类：共享锁、排他锁。

基于锁的粒度分类：行级锁(INNODB)、表级锁(INNODB、MYISAM)、页级锁(BDB引擎 )、记录锁、间隙锁、临键锁。

基于锁的状态分类：意向共享锁、意向排它锁。

- 共享锁(Share Lock)

```
共享锁又称读锁，简称S锁；当一个事务为数据加上读锁之后，其他事务只能对该数据加读锁，而不能对数据加写锁，直到所有的读锁释放之后其他事务才能对其进行加持写锁。共享锁的特性主要是为了支持 并发的读取数据，读取数据的时候不支持修改，避免出现重复读的问题。
```

- 排他锁（eXclusive Lock）

```
排他锁又称写锁，简称X锁；当一个事务为数据加上写锁时，其他请求将不能再为数据加任何锁，直到该锁释放之后，其他事务才能对数据进行加锁。排他锁的目的是在数据修改时候，不允许其他人同时修改，也不允许其他人读取。避免了出现脏数据和脏读的问题。
```

- 表锁

```
表锁是指上锁的时候锁住的是整个表，当下一个事务访问该表的时候，必须等前一个事务释放了锁才能进行对表进行访问； 

特点：粒度大，加锁简单，容易冲突；
```

- 行锁

```
行锁是指上锁的时候锁住的是表的某一行或多行记录，其他事务访问同一张表时，只有被锁住的记录不能访问，其他的记录可正常访问； 

特点：粒度小，加锁比表锁麻烦，不容易冲突，相比表锁支持的并发要高；
```

- 记录锁

```
记录锁也属于行锁中的一种，只不过记录锁的范围只是表中的某一条记录，记录锁是说事务在加锁后锁 住的只是表的某一条记录。 
精准条件命中，并且命中的条件字段是唯一索引 
加了记录锁之后数据可以避免数据在查询的时候被修改的重复读问题，也避免了在修改的事务未提交前 被其他事务读取的脏读问题。
```

- 页锁

```、
页级锁是MySQL中锁定粒度介于行级锁和表级锁中间的一种锁。表级锁速度快，但冲突多，行级冲突 少，但速度慢。所以取了折衷的页级，一次锁定相邻的一组记录。 特点：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般
```

- 间隙锁

```
属于行锁中的一种，间隙锁是在事务加锁后其锁住的是表记录的某一个区间，当表的相邻ID之间出现空 隙则会形成一个区间，遵循左开右闭原则。
范围查询并且查询未命中记录，查询条件必须命中索引、间隙锁只会出现在REPEATABLE_READ（重复 读)的事务级别中。 
触发条件：防止幻读问题，事务并发的时候，如果没有间隙锁，就会发生如下图的问题，在同一个事务 里，A事务的两次查询出的结果会不一样。 
比如表里面的数据ID 为 1,4,5,7,10 ,那么会形成以下几个间隙区间，-n-1区间，1-4区间，7-10 区间，10-n区间 （-n代表负无穷大，n代表正无穷大）
```

- 临建锁(Next-Key Lock)

```
也属于行锁的一种，并且它是INNODB的行锁默认算法，总结来说它就是记录锁和间隙锁的组合，临键锁 会把查询出来的记录锁住，同时也会把该范围查询内的所有间隙空间也会锁住，再之它会把相邻的下一个区间也会锁住 
触发条件：范围查询并命中，查询命中了索引。 
结合记录锁和间隙锁的特性，临键锁避免了在范围查询时出现脏读、重复读、幻读问题。加了临键锁之后，在范围区间内数据不允许被修改和插入。
```

如果当事务A加锁成功之后就设置一个状态告诉后面的人，已经有人对表里的行加了一个排他锁了，你们不能对整个表加共享锁或排它锁了，那么后面需要对整个表加锁的人只需要获取这个状态就知道自己是不是可以对表加锁，避免了对整个索引树的每个节点扫描是否加锁，而这个状态就是意向锁。

- 意向共享锁

```
当一个事务试图对整个表进行加共享锁之前，首先需要获得这个表的意向共享锁。
```

- 意向排他锁

```
当一个事务试图对整个表进行加排它锁之前，首先需要获得这个表的意向排它锁。
```



## **6.InnoDB**存储引擎的锁的算法

- Record lock：单个行记录上的锁

- Gap lock：间隙锁，锁定一个范围，不包括记录本身

- Next-key lock：record+gap 锁定一个范围，包含记录本身

相关知识点：

1. innodb对于行的查询使用next-key lock

2. Next-locking keying为了解决Phantom Problem幻读问题

3. 当查询的索引含有唯一属性时，将next-key lock降级为record key

4. Gap锁设计的目的是为了阻止多个事务将记录插入到同一范围内，而这会导致幻读问题的产生

5. 有两种方式显式关闭gap锁：（除了外键约束和唯一性检查外，其余情况仅使用record lock）

    A.将事务隔离级别设置为RC

    B. 将参数innodb_locks_unsafe_for_binlog设置为1



##  7.**关心过业务系统里面的**sql耗时吗？统计过慢查询吗？对慢查询都怎么优化过？

在业务系统中，除了使用主键进行的查询，其他的都会在测试库上测试其耗时，慢查询的统计主要由运维在做，会定期将业务中的慢查询反馈给我们。

慢查询的优化首先要搞明白慢的原因是什么？是查询条件没有命中索引？是load了不需要的数据列？还是数据量太大？

所以优化也是针对这三个方向来的，

- 首先分析语句，看看是否load了额外的数据，可能是查询了多余的行并且抛弃掉了，可能是加载了许多结果中并不需要的列，对语句进行分析以及重写。

- 分析语句的执行计划，然后获得其使用索引的情况，之后修改语句或者修改索引，使得语句可以尽可能的命中索引。

- 如果对语句的优化已经无法进行，可以考虑表中的数据量是否太大，如果是的话可以进行横向或者纵向的分表。



## 8.**事务的基本特性和隔离级别**

事务基本特性ACID分别是：

**原子性**指的是一个事务中的操作要么全部成功，要么全部失败。

**一致性**指的是数据库总是从一个一致性的状态转换到另外一个一致性的状态。比如A转账给B100块钱，

假设A只有90块，支付之前我们数据库里的数据都是符合约束的,但是如果事务执行成功了,我们的数据库

数据就破坏约束了,因此事务不能成功,这里我们说事务提供了一致性的保证

**隔离性**指的是一个事务的修改在最终提交前，对其他事务是不可见的。

**持久性**指的是一旦事务提交，所做的修改就会永久保存到数据库中。

隔离性有4个隔离级别，分别是：

- read uncommit 读未提交，可能会读到其他事务未提交的数据，也叫做脏读。

用户本来应该读取到id=1的用户age应该是10，结果读取到了其他事务还没有提交的事务，结果读取结果age=20，这就是脏读。

- read commit 读已提交，两次读取结果不一致，叫做不可重复读。

不可重复读解决了脏读的问题，他只会读取已经提交的事务。

用户开启事务读取id=1用户，查询到age=10，再次读取发现结果=20，在同一个事务里同一个查询读取到不同的结果叫做不可重复读。

- repeatable read 可重复复读，这是mysql的默认级别，就是每次读取结果都一样，但是有可能产生幻读。

- serializable 串行，一般是不会使用的，他会给每一行读取的数据加锁，会导致大量超时和锁竞争的问题。

脏读(Drity Read)：某个事务已更新一份数据，另一个事务在此时读取了同一份数据，由于某些原因，前一个RollBack了操作，则后一个事务所读取的数据就会是不正确的。

不可重复读(Non-repeatable read):在一个事务的两次查询之中数据不一致，这可能是两次查询过程中间插入了一个事务更新的原有的数据。

幻读(Phantom Read):在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的。



## 9.**ACID**靠什么保证的？

A 原子性由undo log日志保证，它记录了需要回滚的日志信息，事务回滚时撤销已经执行成功的sql

C 一致性由其他三大特性保证、程序代码要保证业务上的一致性

I 隔离性由MVCC来保证

D 持久性由内存+redo log来保证，mysql修改数据同时在内存和redo log记录这次操作，宕机的时候可以从redo log恢复

```
InnoDB redo log 写盘，InnoDB 事务进入 prepare 状态。 如果前面 prepare 成功，binlog 写盘，再继续将事务日志持久化到 binlog，如果持久化成功，那么 InnoDB 事务则进入 commit 状态(在 redo log 里面写一个 commit 记录)
```

redolog的刷盘会在系统空闲时进行



## 10.什么是MVCC

多版本并发控制：读取数据时通过一种类似快照的方式将数据保存下来，这样读锁就和写锁不冲突了，

不同的事务session会看到自己特定版本的数据，版本链

MVCC只在 READ COMMITTED 和 REPEATABLE READ 两个隔离级别下工作。其他两个隔离级别够和

MVCC不兼容, 因为 READ UNCOMMITTED 总是读取最新的数据行, 而不是符合当前事务版本的数据

行。而 SERIALIZABLE 则会对所有读取的行都加锁。

聚簇索引记录中有两个必要的隐藏列：

**trx_id**：用来存储每次对某条聚簇索引记录进行修改的时候的事务id。

**roll_pointer**：每次对哪条聚簇索引记录有修改的时候，都会把老版本写入undo日志中。这个

roll_pointer就是存了一个指针，它指向这条聚簇索引记录的上一个版本的位置，通过它来获得上一个

版本的记录信息。(注意插入操作的undo日志没有这个属性，因为它没有老版本)

**已提交读和可重复读的区别就在于它们生成ReadView的策略不同**

![image-20210427144809228](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210427144809228.png)

开始事务时创建readview，readView维护当前活动的事务id，即未提交的事务id，排序生成一个数组访问数据，获取数据中的事务id（获取的是事务id最大的记录），对比readview： 

如果在readview的左边（比readview都小），可以访问（在左边意味着该事务已经提交）

如果在readview的右边（比readview都大）或者就在readview中，不可以访问，获取roll_pointer，取上一版本重新对比（在右边意味着，该事务在readview生成之后出现，在readview中意味着该事务还未提交）

已提交读隔离级别下的事务在每次查询的开始都会生成一个独立的ReadView,而可重复读隔离级别则在第一次读的时候生成一个ReadView，之后的读都复用之前的ReadView。

这就是Mysql的MVCC,通过版本链，实现多版本，可并发读-写，写-读。通过ReadView生成策略的不同实现不同的隔离级别。



## 11.**分表后非**sharding_key的查询怎么处理，分表后的排序？

1. 可以做一个mapping表，比如这时候商家要查询订单列表怎么办呢？不带user_id查询的话你总不能扫全表吧？所以我们可以做一个映射关系表，保存商家和用户的关系，查询的时候先通过商家查询到用户列表，再通过user_id去查询。

2. 宽表，对数据实时性要求不是很高的场景，比如查询订单列表，可以把订单表同步到离线（实时）数仓，再基于数仓去做成一张宽表，再基于其他如es提供查询服务。

3. 数据量不是很大的话，比如后台的一些查询之类的，也可以通过多线程扫表，然后再聚合结果的方式来做。或者异步的形式也是可以的。



union

排序字段是唯一索引：

- 首先第一页的查询：将各表的结果集进行合并，然后再次排序

- 第二页及以后的查询，需要传入上一页排序字段的最后一个值，及排序方式。

- 根据排序方式，及这个值进行查询。如排序字段date，上一页最后值为3，排序方式降序。查询的时候sql为select ... from table where date < 3 order by date desc limit 0,10。这样再将几个表的结果合并排序即可。



## 12.**mysql**主从同步原理

mysql主从同步的过程：

Mysql的主从复制中主要有三个线程： master（binlog dump thread）、slave（I/O thread 、SQL thread） ，Master一条线程和Slave中的两条线程。

- 主节点 binlog，主从复制的基础是主库记录数据库的所有变更记录到 binlog。binlog 是数据库服务器启动的那一刻起，保存所有修改数据库结构或内容的一个文件。

- 主节点 log dump 线程，当 binlog 有变动时，log dump 线程读取其内容并发送给从节点。

- 从节点 I/O线程接收 binlog 内容，并将其写入到 relay log 文件中。

- 从节点的SQL 线程读取 relay log 文件内容对数据更新进行重放，最终保证主从数据库的一致性。

注：主从节点使用 binglog 文件 + position 偏移量来定位主从同步的位置，从节点会保存其已接收到的偏移量，如果从节点发生宕机重启，则会自动从 position 的位置发起同步。

由于mysql默认的复制方式是异步的，主库把日志发送给从库后不关心从库是否已经处理，这样会产生一个问题就是假设主库挂了，从库处理失败了，这时候从库升为主库后，日志就丢失了。由此产生两个概念。

**全同步复制**

主库写入binlog后强制同步日志到从库，所有的从库都执行完成后才返回给客户端，但是很显然这个方式的话性能会受到严重影响。

**半同步复制**

和全同步不同的是，半同步复制的逻辑是这样，从库写入日志成功后返回ACK确认给主库，主库收到至少一个从库的确认就认为写操作完成。



## 13.**简述**MyISAM**和**InnoDB的区别

**MyISAM**：

不支持事务，但是每次查询都是原子的；

支持表级锁，即每次操作是对整个表加锁；

存储表的总行数；

一个MYISAM表有三个文件：索引文件、表结构文件、数据文件；

采用非聚集索引，索引文件的数据域存储指向数据文件的指针。辅索引与主索引基本一致，但是辅索引不用保证唯一性。

**InnoDb**：

支持ACID的事务，支持事务的四种隔离级别；

支持行级锁及外键约束：因此可以支持写并发；

不存储总行数；

一个InnoDb引擎存储在一个文件空间（共享表空间，表大小不受操作系统控制，一个表可能分布在多个文件里），也有可能为多个（设置为独立表空，表大小受操作系统文件大小限制，一般为2G），受操作系统文件大小的限制；

主键索引采用聚集索引（索引的数据域存储数据文件本身），辅索引的数据域存储主键的值；因此从辅索引查找数据，需要先通过辅索引找到主键值，再访问辅索引；最好使用自增主键，防止插入数据时，为维持B+树结构，文件的大调整。



## 14.**简述**mysql中索引类型及对数据库的性能的影响

普通索引：允许被索引的数据列包含重复的值。

唯一索引：可以保证数据记录的唯一性。

主键：是一种特殊的唯一索引，在一张表中只能定义一个主键索引，主键用于唯一标识一条记录，使用关键字 PRIMARY KEY 来创建。

联合索引：索引可以覆盖多个数据列，如像INDEX(columnA, columnB)索引。

全文索引：通过建立 倒排索引 ,可以极大的提升检索效率,解决判断字段是否包含的问题，是目前搜索引擎使用的一种关键技术。可以通过ALTER TABLE table_name ADD FULLTEXT (column);创建全文索引索引可以极大的提高数据的查询速度。

通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

但是会降低插入、删除、更新表的速度，因为在执行这些写操作时，还要操作索引文件

索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚簇索引，那么需要的空间就会更大，如果非聚集索引很多，一旦聚集索引改变，那么所有非聚集索引都会跟着变。



## 15.**mysql**执行计划怎么看

执行计划就是sql的执行查询的顺序，以及如何使用索引查询，返回的结果集的行数

EXPLAIN SELECT * from A where X=? and Y=?

![image-20210427145418313](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210427145418313.png)

1。id ：是一个有顺序的编号，是查询的顺序号，有几个 select 就显示几行。id的顺序是按 select 出现的顺序增长的。id列的值越大执行优先级越高越先执行，id列的值相同则从上往下执行，id列的值为NULL最后执行。

2。selectType 表示查询中每个select子句的类型

- SIMPLE： 表示此查询不包含 UNION 查询或子查询

- PRIMARY： 表示此查询是最外层的查询（包含子查询）

- SUBQUERY： 子查询中的第一个 SELECT

- UNION： 表示此查询是 UNION 的第二或随后的查询

- DEPENDENT UNION： UNION 中的第二个或后面的查询语句, 取决于外面的查询

- UNION RESULT, UNION 的结果

- DEPENDENT SUBQUERY: 子查询中的第一个 SELECT, 取决于外面的查询. 即子查询依赖于外层查询的结果.

- DERIVED：衍生，表示导出表的SELECT（FROM子句的子查询）

3.table：表示该语句查询的表

4.type：优化sql的重要字段，也是我们判断sql性能和优化程度重要指标。他的取值类型范围：

- const：通过索引一次命中，匹配一行数据

- system: 表中只有一行记录，相当于系统表；

- eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配

- ref: 非唯一性索引扫描,返回匹配某个值的所有

- range: 只检索给定范围的行，使用一个索引来选择行，一般用于between、<、>；

- index: 只遍历索引树；

- ALL: 表示全表扫描，这个类型的查询是性能最差的查询之一。 那么基本就是随着表的数量增多，执行效率越慢。

**执行效率：**

**ALL < index < range< ref < eq_ref < const < system**。最好是避免ALL和index

5.possible_keys：它表示Mysql在执行该sql语句的时候，可能用到的索引信息，仅仅是可能，实际不一定会用到。

6.key：此字段是 mysql 在当前查询时所真正使用到的索引。 他是possible_keys的子集

7.key_len：表示查询优化器使用了索引的字节数，这个字段可以评估组合索引是否完全被使用，这也是我们优化sql时，评估索引的重要指标

9.rows：mysql 查询优化器根据统计信息，估算该sql返回结果集需要扫描读取的行数，这个值相关重要，索引优化之后，扫描读取的行数越多，说明索引设置不对，或者字段传入的类型之类的问题，说明要优化空间越大

10.filtered：返回结果的行占需要读到的行(rows列的值)的百分比，就是百分比越高，说明需要查询到数据越准确， 百分比越小，说明查询到的数据量大，而结果集很少

11.extra

- using filesort ：表示 mysql 对结果集进行外部排序，不能通过索引顺序达到排序效果。一般有using filesort都建议优化去掉，因为这样的查询 cpu 资源消耗大，延时大。

- using index：覆盖索引扫描，表示查询在索引树中就可查找所需数据，不用扫描表数据文件，往往说明性能不错。

- using temporary：查询有使用临时表, 一般出现于排序， 分组和多表 join 的情况， 查询效率不高，建议优化。

- using where ：sql使用了where过滤,效率较高。



## 16.索引怎么建立？

见https://blog.csdn.net/xluren/article/details/32746183



## 17.建立索引一定快吗

索引有什么副作用吗？

（1）索引是有大量数据的时候才建立的，没有大量数据反而会浪费时间，因为索引是使用二叉树建立.

（2）当一个系统查询比较频繁，而新建，修改等操作比较少时，可以创建索引，这样查询的速度会比以前快很多，同时也带来弊端，就是新建或修改等操作时，比没有索引或没有建立覆盖索引时的要慢。

（3）索引并不是越多越好，太多索引会占用很多的索引表空间，甚至比存储一条记录更多。
对于需要频繁新增记录的表，最好不要创建索引，没有索引的表，执行insert、append都很快，有了索引以后，会多一个维护索引的操作，一些大表可能导致insert 速度非常慢。

无索引时全表扫描也就是要逐条扫描全部记录，直到找完符合条件的，

索引扫描可以直接定位


## 18.什么是回表？什么是索引覆盖？

见https://blog.csdn.net/weixin_41924879/article/details/102755870?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161950992316780265493411%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=161950992316780265493411&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-1-102755870.pc_search_result_hbase_insert&utm_term=%E4%BB%80%E4%B9%88%E6%98%AF%E5%9B%9E%E8%A1%A8%EF%BC%9F%E4%BB%80%E4%B9%88%E6%98%AF%E7%B4%A2%E5%BC%95%E8%A6%86%E7%9B%96%EF%BC%9F



## redis

**RDB和AOF机制**

RDB:RedisDataBase

在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。

优点：

1、整个redis数据库将只包含一个文件dump.rdb,方便持久化。

2、容灾性好，方便备份。

3、性能最大化，Fork子进程来完成写操作，让主进程继续处理命令，所以是IO最大化。使用单独子进程来进行持久化，主进程不会进行任何IO操作，保证了redis的高性能

4、相对于数据集大时，比AOF的启动效率更高。

缺点：

1、数据安全性低。RDB是间隔一段时间进行持久化，如果持久化之间redis发生故障，会发生数据丢失。所以这种方式更适合数据要求不严谨的时候。

2、由于RDB是通过fork子进程来协助完成数据持久化工作的，因此如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是一秒钟。



AOF：AppendOnlyFile

以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录

- 优点

1、数据安全，Redis提供了三种同步策略，即每秒同步、每修改同步和不同步。事实上，每秒同步也是异步完成的，其效率是非常高的，问题是一旦系统出现宕机现象，那么这一秒钟之内修改的数据将会丢失，而每修改同步，我们可以将其视为同步持久化，即每次发生的数据变化都会立即记录到磁盘中。



## 2.什么是IO多路复用？

- IO 多路复用是一种同步IO模型，实现一个线程可以监视多个文件句柄；
- 一旦某个文件句柄就绪，就能够通知应用程序进行相应的读写操作；
- 没有文件句柄就绪就会阻塞应用程序，交出CPU。

### 同步阻塞BIO

- 服务端采用单线程，当 accept 一个请求后，在 recv 或 send 调用阻塞时，将无法 accept 其他请求(必须等上一个请求处理 recv 或 send 完 )(无法处理并发)

- 服务端采用多线程，当 accept 一个请求后，开启线程进行 recv，可以完成并发处理，但随着请求数增加需要增加系统线程，大量的线程占用很大的内存空间，并且线程切换会带来很大的开销，10000个线程真正发生读写实际的线程数不会超过20%，每次accept都开一个线程也是一种资源浪费。

### 同步非阻塞NIO

- 服务器端当 accept 一个请求后，加入 fds 集合，每次轮询一遍 fds 集合 recv (非阻塞)数据，没有数据则立即返回错误，每次轮询所有 fd (包括没有发生读写实际的 fd)会很浪费 CPU。

### **为什么 Redis 中要使用 I/O 多路复用这种技术呢？**

首先，Redis 是跑在单线程中的，所有的操作都是按照顺序线性执行的，但是由于读写操作等待用户输入或输出都是阻塞的，所以 I/O 操作在一般情况下往往不能直接返回，这会导致某一文件的 I/O 阻塞导致整个进程无法对其它客户提供服务，而 **I/O 多路复用** 就是为了解决这个问题而出现的。

redis的io模型主要是基于epoll实现的，不过它也提供了 select和kqueue的实现，默认采用epoll。

### select、poll、epoll之间的区别

elect，poll，epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪(一般是读就绪或者写就绪)，能够通知程序进行相应的读写操作。**但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的**，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。

epoll跟select都能提供多路I/O复用的解决方案。在现在的Linux内核里有都能够支持，其中epoll是Linux所特有，而select则应该是POSIX所规定，一般操作系统均有实现

![6c357634f2df8802e09c1a162d05d64e.png](https://img-blog.csdnimg.cn/img_convert/6c357634f2df8802e09c1a162d05d64e.png)

epoll是Linux目前大规模网络并发程序开发的首选模型。在绝大多数情况下性能远超select和poll。目前流行的高性能web服务器Nginx正式依赖于epoll提供的高效网络套接字轮询服务。但是，在并发连接不高的情况下，多线程+阻塞I/O方式可能性能更好。

### epoll 水平触发(LT)与 边缘触发(ET)的区别？

EPOLL事件有两种模型：

- Edge Triggered (ET) 边缘触发只有数据到来,才触发,不管缓存区中是否还有数据。
- Level Triggered (LT) 水平触发只要有数据都会触发。