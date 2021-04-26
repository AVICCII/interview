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























