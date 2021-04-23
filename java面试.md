# java面试

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









# Spring

## 如何实现一个IOC容器

