---
title: 'Java八股文'
date: 2022-10-19 12:31:59
tags: [Java]
published: true
hideInList: false
feature: 
isTop: false
---
![](https://ethic-233.github.io/post-images/1666155479780.jpg)final

final是一个修饰符

    被final修饰的量的值不可变

    被final修饰的方法不可被重写

    被final修饰的类不可被继承

    final不能修饰抽象类和接口(抽象类和接口必须要被实体类继承才能创建对象) ,也不能修饰抽象方法(抽象方法必须被重写)

    在 final 定义的方法里，不是必须要用 final 定义变量

    final 定义的变量，可以在不是必须要在定义的同时完成初始化，也可以在构造方法中完成初始化

    final修饰方法，不能被子类重写，但是可以被重载

    final 定义变量，可以用 static 也可以不用

    final定义的成员变量可以在代码块(类变量则静态代码块，实例变量普通代码块)里初始化，也可以在构造器中初始化，也可以在声明变量时初始化。final的局部变量声明时不必马上初始化，但使用时必须初始化，而且只能一次赋值 

int

int是基本类型，直接存数值，Integer是对象，用一个引用指向这个对象
集合框架

LinkedList 和 ArrayList 都是不同步的，线程不安全； Vector 和 Stack 都是同步的，线程安全； Set是线程不安全的；

HashSet，是线程安全的，不允许存储相同的对象；

Hashtable的方法是同步的，线程安全； HashMap的方法不是同步的，线程不安全；

ConcurrentHashMap，是线程安全的，其中存储的键对象可以重复，值对象不能重复；
访问修饰符

	public 	protected 	default 	private
同包同类 	√ 	√ 	√ 	√
同包不同类 	√ 	√ 	√ 	
同包不同类继承 	√ 	√ 	√ 	
不同包继承 	√ 	√ 	
	
不同包无任何关系的类 	√ 	
	
	

实际开发的时候禁止使用default

接口方法的修饰符可以是：public，abstract，default，static（后两者需要有{}）

构造方法可以用private，protected，default，private

类、方法、成员变量和局部变量的可用修饰符：
修饰符 	类 	成员访问 	构造方法 	成员变量 	局部变量
abstract（抽象的） 	√ 	√ 	
	
	
static（静态的） 	
	√ 	
	√ 	
public（公共的） 	√ 	√ 	√ 	√ 	
protected（受保护的） 	
	√ 	√ 	√ 	
private（私有的） 	
	√ 	√ 	√ 	
synchronized（同步的） 	
	√ 	
	
	
native（本地的） 	
	√ 	
	
	
transient（暂时的） 	
	
	
	√ 	
volatile（易失的） 	
	
	
	√ 	
final（不要改变的） 	√ 	√ 	
	√ 	√
垃圾回收

一旦一个对象成为垃圾，就立刻被收集掉。

答: 这个是jvm的东西，暂时不做要求

垃圾什么时候收集看回收算法，但肯定不是立刻收集
抽象类和接口

abstract class和interface所反映出的设计理念不同。其实abstract class表示的是"is-a"关系，interface表示的是"has-a"关系。

    接口里不允许有普通属性，只能有抽象方法(java8之前),因此接口仅仅表示这个东西有这些操作，所以是has-a

    抽象类作为一个类本身就表示这个东西是什么(可以具体的给出属性和方法)，所以是is-a 

接口是公开的，里面不能有私有的方法或变量，是用于让别人使用的，而抽象类是可以有私有方法或私有变量的。

abstract class 在 Java 语言中表示的是一种继承关系，一个类只能使用一次继承关系。但是，一个类却可以实现多个interface，实现多重继承。接口还有标识（里面没有任何方法，如Remote接口）和数据共享（里面的变量全是常量）的作用。

在abstract class 中可以有自己的数据成员，也可以有非abstarct的成员方法，而在interface中，只能够有静态的不能被修改的数据成员（也就是必须是 static final的，不过在 interface中一般不定义数据成员），所有的成员方法默认都是 public abstract 类型的。

java中接口只能使用public修饰，接口内方法默认为public abstract

当一个实体类继承一个抽象类，必须实现抽象类中的抽象方法

java 接口中可以定义成员变量，且必须是 static final的

抽象类可以实现接口，抽象类实现接口其所具有的意义：

    一般而言，多使用普通类来实现接口，但是普通类实现接口的话就必须实现接口的所有方法，这样容易造成代码冗余。

    而如果使用抽象类来实现接口，然后再用普通类继承抽象类的话，其仍然可以实现接口中但抽象类中并未实现的方法。以此可以只实现必要的方法，即抽象类中可以不定义对于子类而言必要的方法，而最终交由子类自己来实现。

    从2中可知，抽象类实现接口时，不必实现接口中的所有方法，未实现的方法可以交由子类来实现。 

接口中的变量默认是public static final 的，方法默认是public abstract 的

JDK8中，接口中的方法可以被default和static修饰，但被修饰的方法必须有方法体
默认访问权限

关于抽象类

JDK 1.8以前，抽象类的方法默认访问权限为protected

JDK 1.8时，抽象类的方法默认访问权限变为default

关于接口

JDK 1.8以前，接口中的方法必须是public的

JDK 1.8时，接口中的方法可以是public的，也可以是default的

JDK 1.9时，接口中的方法可以是private的
异常和错误

异常（Exception），错误（Error）

所有的Java异常和错误的基类都是java.lang.Exception, 包括java.lang.RuntimeException

答: 错误，基类是Throwable

正确描述: java里所有异常的基类是Exception,所有运行时异常的基类是RuntimeException,所有错误的基类是Error,所有异常和错误的基类是Throwable

补充: 通过try … catch … finally语句，finally中的语句部分无论发生什么异常都会得到执行
依赖注入

依赖注入能够独立开发各组件，然后根据组件间关系进行组装

依赖注入提供使用接口编程

依赖注入指对象在使用时动态注入

依赖注入的动机就是减少组件之间的耦合度，使开发更为简洁
类的加载顺序

public class Test {   public static Test t1 = new Test(); //静态变量     {   System.out.println("blockA"); //构造块   }     static   {   System.out.println("blockB"); //静态块   }     public static void main(String[] args)   {   Test t2 = new Test();   } }

静态域：用staitc声明，jvm加载类时执行，仅执行一次 构造代码块：类中直接用{}定义，每一次创建对象时执行。

执行顺序优先级：静态域，main()，构造代码块，构造方法。

    静态域 ：首先执行，第一个静态域是一个静态变量 public static Test t1 = new Test(); 创建了Test 对象，会执行构造块代码，所以输出blockA。然后执行第二个静态域（即静态代码块）输出blockB

    main()：Test t2 = new Test()执行，创建Test类对象，只会执行构造代码块（创建对象时执行），输出blockA

    构造代码块只会在创建对象时执行，没创建任何对象了，所以没输出

    构造函数：使用默认构造函数，没任何输出 

类的加载顺序：

    父类静态对象和静态代码块

    子类静态对象和静态代码块

    父类非静态对象和非静态代码块

    父类构造函数

    子类非静态对象和非静态代码块

    子类构造函数 

其中：类中静态块按照声明执行顺序执行，并且1和2不需要调用new类实例的时候就执行了（意思是类加载到方法区的时候执行的）
代码块

普通块：方法体内部由大括号括起，执行顺序为按照正常的执行顺序（先出现，先执行）执行

构造块：类的内部由大括号括起，每次构造对象时都会被调用，且优先于构造函数执行

静态块：在类的内部使用，形式为static{}，静态块一般用于初始化类，为类的属性初始化，每个静态代码块只会执行一次，执行顺序遵循先定义先执行原则
CountDownLatch与CyclicBarrier

CountDownLatch 是等待一组线程执行完，才执行后面的代码。此时这组线程已经执行完 CyclicBarrier 是等待一组线程至某个状态后再同时全部继续执行线程。此时这组线程还未执行完
volatile与synchronized

volatile本质是在告诉jvm当前变量在寄存器中的值是不确定的,需要从主存中读取,synchronized则是锁定当前变量,只有当前线程可以访问该变量,其他线程被阻塞住

volatile仅能使用在变量级别,synchronized则可以使用在变量,方法

volatile仅能实现变量的修改可见性,但不具备原子特性,而synchronized则可以保证变量的修改可见性和原子性

volatile不会造成线程的阻塞,而synchronized可能会造成线程的阻塞

volatile标记的变量不会被编译器优化,而synchronized标记的变量可以被编译器优化

可见性： 可见性是一种复杂的属性，因为可见性中的错误总是会违背我们的直觉。通常，我们无法确保执行读操作的线程能适时地看到其他线程写入的值，有时甚至是根本不可能的事情。为了确保多个线程之间对内存写入操作的可见性，必须使用同步机制。

可见性，是指线程之间的可见性，一个线程修改的状态对另一个线程是可见的。也就是一个线程修改的结果。另一个线程马上就能看到。比如：用volatile修饰的变量，就会具有可见性。volatile修饰的变量不允许线程内部缓存和重排序，即直接修改内存。所以对其他线程是可见的。但是这里需要注意一个问题，volatile只能让被他修饰内容具有可见性，但不能保证它具有原子性。比如 volatile int a = 0；之后有一个操作 a++；这个变量a具有可见性，但是a++ 依然是一个非原子操作，也就是这个操作同样存在线程安全问题。 在 Java 中 volatile、synchronized 和 final 实现可见性。

原子性： 原子是世界上的最小单位，具有不可分割性。比如 a=0；（a非long和double类型） 这个操作是不可分割的，那么我们说这个操作时原子操作。再比如：a++； 这个操作实际是a = a + 1；是可分割的，所以他不是一个原子操作。非原子操作都会存在线程安全问题，需要我们使用同步技术（sychronized）来让它变成一个原子操作。一个操作是原子操作，那么我们称它具有原子性。java的concurrent包下提供了一些原子类，我们可以通过阅读API来了解这些原子类的用法。比如：AtomicInteger、AtomicLong、AtomicReference等。 在 Java 中 synchronized 和在 lock、unlock 中操作保证原子性。

有序性： Java 语言提供了 volatile 和 synchronized 两个关键字来保证线程之间操作的有序性，volatile 是因为其本身包含“禁止指令重排序”的语义，synchronized 是由“一个变量在同一个时刻只允许一条线程对其进行 lock 操作”这条规则获得的，此规则决定了持有同一个对象锁的两个同步块只能串行执行。
HashMap和Hashtable

HashMap和Hashtable都实现了Map接口，都是键值对保存数据的方式 区别1： HashMap可以存放 null Hashtable不能存放null 区别2： HashMap不是线程安全的类 Hashtable是线程安全的类
StringBuffer和StringBuilder

StringBuffer 是线程安全的 StringBuilder 是非线程安全的

所以当进行大量字符串拼接操作的时候，如果是单线程就用StringBuilder会更快些，如果是多线程，就需要用StringBuffer 保证数据的安全性

非线程安全的为什么会比线程安全的快？

因为不需要同步，省略了些时间
ArrayList和Vector

Vector是线程安全的类，ArrayList是非线程安全的
线程安全

如果一个类，其方法都是有synchronized修饰的，那么该类就叫做线程安全的类

同一时间，只有一个线程能够进入 这种类的一个实例 的去修改数据，进而保证了这个实例中的数据的安全(不会同时被多线程修改而变成脏数据)

比如StringBuffer和StringBuilder的区别 StringBuffer的方法都是有synchronized修饰的，StringBuffer就叫做线程安全的类 而StringBuilder就不是线程安全的类

ArrayList是非线程安全的，换句话说，多个线程可以同时进入一个ArrayList对象的add方法

借助Collections.synchronizedList，可以把ArrayList转换为线程安全的List

与此类似的，还有HashSet,LinkedList,HashMap等等非线程安全的类，都通过工具类Collections转换为线程安全的

public static void main(String[] args) {  List<Integer> list1 = new ArrayList<>();  List<Integer> list2 = Collections.synchronizedList(list1); }

JVM内存

程序计数器是一个比较小的内存区域，用于指示当前线程所执行的字节码执行到了第几行，是线程隔离的

虚拟机栈描述的是Java方法执行的内存模型，用于存储局部变量，操作数栈，动态链接，方法出口等信息，是线程隔离的

在方法区中，存储了每个类的信息（包括类的名称、方法信息、字段信息）、静态变量、常量以及编译器编译后的代码等，是线程共享的

原则上讲，所有的对象都在堆区上分配内存，是线程共享的

JVM初始运行的时候都会分配好 Method Area（方法区） 和Heap（堆） ，而JVM 每遇到一个线程，就为其分配一个 Program Counter Register（程序计数器） , VM Stack（虚拟机栈）和Native Method Stack （本地方法栈）， 当线程终止时，三者（虚拟机栈，本地方法栈和程序计数器）所占用的内存空间也会被释放掉。这也是为什么我把内存区域分为线程共享和非线程共享的原因，非线程共享的那三个区域的生命周期与所属线程相同，而线程共享的区域与JAVA程序运行的生命周期相同，所以这也是系统垃圾回收的场所只发生在线程共享的区域（实际上对大部分虚拟机来说知发生在Heap上）的原因

JVM堆分为：新生代（一般是一个Eden区，两个Survivor区），老年代（old区），常量池属于 PermGen（方法区）
关键字

这个关键字常见的坑：

true、false、null都不是关键字

goto、const是保留的关键字

abstract         continue           for            new  switch          default            if          package synchronized     do            goto           private this         break          double         implements  protected     throw              byte           else    import          public             throws         case enum                    instanceof         return         transient  catch                   extends            int            short  try                     char               final          interface  static                  void               class          finally  long                    strictfp           volatile       const  float                   native             super          while boolean                 assert 

JVM垃圾回收

两个最基本的java回收算法：复制算法和标记清理算法

复制算法：两个区域A和B，初始对象在A，继续存活的对象被转移到B。此为新生代最常用的算法

标记清理：一块区域，标记可达对象（可达性分析），然后回收不可达对象，会出现碎片，那么引出

标记-整理算法：多了碎片整理，整理出更大的内存放更大的对象

两个概念：新生代和年老代

新生代：初始对象，生命周期短的

永久代：长时间存在的对象

整个java的垃圾回收是新生代和年老代的协作，这种叫做分代回收

P.S：Serial New收集器是针对新生代的收集器，采用的是复制算法

Parallel New（并行）收集器，新生代采用复制算法，老年代采用标记整理

Parallel Scavenge（并行）收集器，针对新生代，采用复制收集算法

Serial Old（串行）收集器，新生代采用复制，老年代采用标记整理Parallel Old（并行）收集器，针对老年代，标记整理

CMS收集器，基于标记清理

G1收集器：整体上是基于标记整理，局部采用复制
注解

Override注解：

指明被注解的方法需要覆写超类中的方法.

如果某个方法使用了该注解,却没有覆写超类中的方法(比如大小写写错了,或者参数错了,或者是子类自己定义的方法),编译器就会生成一个错误.

Deprecated注解：

可以修饰类、方法、变量，在java源码中被@Deprecated修饰的类、方法、变量等表示不建议使用的，可能会出现错误的，可能以后会被删除的类、方法等，如果现在使用，则在以后使用了这些类、方法的程序在更新新的JDK、jar包等就会出错，不再提供支持。个人程序中的类、方法、变量用@Deprecated修饰同样是不希望自己和别人在以后的时间再次使用此类、方法。 当编译器编译时遇到了使用@Deprecated修饰的类、方法、变量时会提示相应的警告信息。

Suppresswarnings注解：

可以达到抑制编译器编译时产生警告的目的，但是很不建议使用@SuppressWarnings注解，使用此注解，编码人员看不到编译时编译器提示的相应的警告，不能选择更好、更新的类、方法或者不能编写更规范的编码。同时后期更新JDK、jar包等源码时，使用@SuppressWarnings注解的代码可能受新的JDK、jar包代码的支持，出现错误，仍然需要修改。

抑制警告的关键字：
关键字 	用途
all 	to suppress all warnings 禁止所有警告
boxing 	to suppress warnings relative to boxing/unboxing operations 抑制与装箱/拆箱操作相关的警告
cast 	to suppress warnings relative to cast operations 抑制与强制转换操作相关的警告
dep-ann 	to suppress warnings relative to deprecated annotation 抑制与已弃用注释相关的警告
deprecation 	to suppress warnings relative to deprecation
fallthrough 	to suppress warnings relative to missing breaks in switch statements
finally 	to suppress warnings relative to finally block that don’t return
hiding 	to suppress warnings relative to locals that hide variable
incomplete-switch 	to suppress warnings relative to missing entries in a switch statement (enum case)
nls 	to suppress warnings relative to non-nls string literals
null 	to suppress warnings relative to null analysis
rawtypes 	to suppress warnings relative to un-specific types when using generics on class params
restriction 	to suppress warnings relative to usage of discouraged or forbidden references
serial 	to suppress warnings relative to missing serialVersionUID field for a serializable class
static-access 	o suppress warnings relative to incorrect static access
synthetic-access 	to suppress warnings relative to unoptimized access from inner classes
unchecked 	to suppress warnings relative to unchecked operations
unqualified-field-access 	to suppress warnings relative to field access unqualified
unused 	to suppress warnings relative to unused code
forward和redirect

forward，服务器获取跳转页面内容传给用户，用户地址栏不变 redirect，是服务器向用户发送转向的地址，redirect后地址栏变成新的地址

1.从地址栏显示来说

forward是服务器请求资源,服务器直接访问目标地址的URL,把那个URL的响应内容读取过来,然后把这些内容再发给浏览器.浏览器根本不知道服务器发送的内容从哪里来的,所以它的地址栏还是原来的地址. redirect是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址.所以地址栏显示的是新的URL.

2.从数据共享来说

forward:转发页面和转发到的页面可以共享request里面的数据. redirect:不能共享数据.

3.从运用地方来说

forward:一般用于用户登陆的时候,根据角色转发到相应的模块. redirect:一般用于用户注销登陆时返回主页面和跳转到其它的网站等.

4.从效率来说

forward:高. redirect:低.
try catch

一旦在finally块中使用了return或throw语句，将会导致try块，catch块中的return，throw语句失效
native

native是由调用本地方法库（如操作系统底层函数），可以由C，C++实现
count = count++

count = count++ 原理是 temp = count； count = count+1 ； count = temp； 因此count始终是0 这仅限于java 与c是不一样的
throw和throws

1、throws出现在方法头，throw出现在方法体 2、throws表示出现异常的一种可能性，并不一定会发生异常；throw则是抛出了异常，执行throw则一定抛出了某种异常。 3、两者都是消极的异常处理方式，只是抛出或者可能抛出异常，是不会由函数处理，真正的处理异常由它的上层调用处理。
Java调试器

java.exe是java虚拟机

javadoc.exe用来制作java文档

jdb.exe是java的调试器

javaprof.exe是剖析工具
Hash冲突

threadlocalmap使用开放定址法解决hash冲突，hashmap使用链地址法解决hash冲突
字符集编码和国际化

Java一律采用Unicode编码方式，每个字符无论中文还是英文字符都占用2个字节

不同的编码之间是可以转换的，通常流程如下：

将字符串S以其自身编码方式分解为字节数组，再将字节数组以你想要输出的编码方式重新编码为字符串

例：String newUTF8Str = new String(oldGBKStr.getBytes("GBK"), "UTF8");

Java虚拟机中通常使用UTF-16的方式保存一个字符

ResourceBundle能够依据Local的不同，选择性的读取与Local对应后缀的properties文件，以达到国际化的目的
this()和super()

    构造器中第一行默认是super()，一旦直接父类的构造器中没有无参的，那么必须显式调用父类的某个有参构造

    构造器中第一行的super()可以换成this()，但是this()和super()只能出现一个

    super，this关键字与super()，this()不是一回事，前者表示当前调用者的父类与其本身，后者是为了构造器相互调用

    this()和super()都指的是对象，所以，均不可以在static环境中使用。包括：static变量,static方法，static语句块(里面不能使用非static类型的) 

实例变量

实例变量指的是类中定义的变量，即成员变量，如果没有初始化，会有默认值；局部变量指的是在方法中定义的变量，必须初始化

类变量指的是用static修饰的属性
运行时异常

运行时异常：都是RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。非运行时异常 （编译异常）：是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常
Socket

//server ServerSocket server = new ServerSocket(port); Socket socket = server.accept(); InputStream inputStream = socket.getInputStream(); inputStream.close(); socket.close(); server.close();  //client Socket socket = new Socket(host, port); socket.getOutputStream(); outputStream.close(); socket.close();

String StringBuffer StringBuilder

    是否可变 

private final char value[];

String 为不可变对象,一旦被创建,就不能修改它的值. . 对于已经存在的String对象的修改都是重新创建一个新的对象,然后把新的值保存进去.

StringBuilder与StringBuffer都继承自AbstractStringBuilder类，在AbstractStringBuilder中也是使用字符数组保存字符串，如下就是，可知这两种对象都是可变的。

char[] value;

    是否多线程安全

    String中的对象是不可变的，也就可以理解为常量， 显然线程安全 。

    AbstractStringBuilder是StringBuilder与StringBuffer的公共父类，定义了一些字符串的基本操作，如expandCapacity、append、insert、indexOf等公共方法。

    StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是 线程安全的 。

    StringBuilder并没有对方法进行加同步锁，所以是 非线程安全的 。

    共同点

    StringBuilder与StringBuffer有公共父类AbstractStringBuilder( 抽象类 )。

    抽象类与接口的其中一个区别是：抽象类中可以定义一些子类的公共方法，子类只需要增加新的功能，不需要重复写已经存在的方法；而接口中只是对方法的申明和常量的定义。

    StringBuilder、StringBuffer的方法都会调用AbstractStringBuilder中的公共方法，如super.append(...)。只是StringBuffer会在方法上加synchronized关键字，进行同步。

    最后，如果程序不是多线程的，那么使用StringBuilder效率高于StringBuffer。 

线程公有、线程私有

私有:java虚拟机栈，程序计数器，本地方法栈 共享:java堆，方法区
OutOfMemoryError

java.lang.OutOfMemoryError: PermGen space

属于运行时常量池导致的溢出，设置-XX：MaxPermSize可以解决这个问题

java.lang.OutOfMemoryError: Requested array size exceeds VM limit

java.lang.OutOfMemoryError: Java heap space

属于java堆内存问题，一般的手段是通过内存映像分析工具，对Dump出来的堆转储存快照进行分析，重点是确认内存中的对象是否是有必要的，也就是要判断是出现了内存泄漏，还是出现了内存溢出，如果是内存列楼，通过工具检查泄露对象打GC Roots的引用链信息，可以准确的确定出泄露代码的位置，不存在泄露，就应该检查虚拟机的堆参数，如果可以继续调大，可以设置-Xmx解决问题

java.lang.OutOfMemoryError: nativeGetNewTLA

指当虚拟机不能分配新的线程本地空间(Thread Local Area）的时候错误信息，此错误是线程申请一个新的TLA时产生的，这个异常一般只会发生在jRockit虚拟机，只有过于绝对
重写方法

两同两小一大原则

    两同：方法名和参数列表相同

    两小：返回值或声明异常比父类小（或相同）

    一大：访问修饰符比父类的大（或相同） 

queue

LinkedBlockingQueue是一个可选有界队列，不允许null值

LinkedBlockingQueue是一个线程安全的阻塞队列，实现了先进先出等特性

PriorityQueue是一个无界队列，不允许null值，入队和出队的时间复杂度是O（log(n)）

ConcurrentLinkedQueue是一个基于链接节点的线程安全队列，该队列的元素遵循FIFO原则
Set

TreeSet使用二叉树对元素进行排序，使用二叉树的原理对新 add()的对象按照指定的顺序排序（升序、降序），每增加一个对象都会进行排序，将对象插入的二叉树指定的位置。

LinkedHashSet 继承于HashSet，又基于 LinkedHashMap 来实现。

HashSet 存储元素的顺序并不是按照存入时的顺序（和 List 显然不同） 而是按照哈希值来存的所以取数据也是按照哈希值取得 。

Set 是继承于Collection的接口。它是一个不允许有重复元素的集合。AbstractSet 是一个抽象类，它继承于AbstractCollection。AbstractCollection实现了Set中的绝大部分函数，为Set的实现类提供了便利。HastSet 和 TreeSet 是Set的两个实现类。HashSet依赖于HashMap，它实际上是通过HashMap实现的。HashSet中的元素是无序的。TreeSet依赖于TreeMap，它实际上是通过TreeMap实现的。TreeSet中的元素是有序的。LinkedHashSet继承于HashSet，是具有可预知迭代顺序的 Set 接口的哈希表和链接列表实现。此实现与 HashSet 的不同之外在于，后者维护着一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，即按照将元素插入到 set 中的顺序（插入顺序）进行迭代。
RuntimeException

RuntimeException并不必须被捕获。不管异常代表的是可预见的异常条件还是编程错误，如果用try{}catch语句捕获它，会让程序在已经出现错误的情况下继续执行下去，也就是说我们不会及时的察觉到程序出现的问题。如果我们不去处理RuntimeException，让程序在测试阶段把异常传播给外界，这时系统打印出来的调用堆栈路径可以帮助我们更快的找出并修改错误，避免出现更大的损失。

通常，Java的异常(包括Exception和Error)分为可查的异常（checked exceptions）和不可查的异常（unchecked exceptions）

可查异常（编译器要求必须处置的异常）： 正确的程序在运行中，很容易出现的、情理可容的异常状况 。 可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常 状况，就必须采取某种方式进行处理。

除了RuntimeException及其子类以外，其他的Exception类及其子类都属于可查异常。这种异常的特点是Java编译器会检查它，也就是说，当程序中可能出现这类异常，要么用try-catch语句捕获它，要么用throws子句声明抛出它，否则编译不会通过。

不可查异常(编译器不要求强制处置的异常):包括运行时异常（RuntimeException与其子类）和错误（Error）。

Exception 这种异常分两大类运行时异常和非运行时异常(编译异常)。程序中应当尽可能去处理这些异常。

运行时异常：都是RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。

非运行时异常 （编译异常）：是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常。
线程安全的Map

HashTable, SynchronizedMap, ConcurrentHashMap
Java表达式转型规则

Java表达式转型规则由低到高转换：

    所有的byte,short,char型的值将被提升为int型

    如果有一个操作数是long型，计算结果是long型

    如果有一个操作数是float型，计算结果是float型

    如果有一个操作数是double型，计算结果是double型

    被final修饰的变量不会自动改变类型，当2个final修饰相操作时，结果会根据左边变量的类型而转化 

字节流字符流

字节流：

InputStream |-- FileInputStream (基本文件流） |-- BufferedInputStream |-- DataInputStream

|-- ObjectInputStream

字符流

Reader |-- InputStreamReader (byte->char 桥梁） |-- BufferedReader (常用） Writer |-- OutputStreamWriter (char->byte 桥梁） |-- BufferedWriter |-- PrintWriter （常用）
多态的作用

提高可重用性 扩展代码模块
Spring配置

Spring本身并不直接管理事务，而是提供了事务管理器接口，对于不同的框架或者数据源则用不同的事务管理器；而对于事务，它把相关的属性都封装到一个实体里面去，有以下的属性：

int propagationBehavior; // 事务的传播行为 int isolationLevel; // 事务的隔离级别 int timeout; // 事务完成的最短时间 boolean readOnly; // 是否只读

Spring提供了对编程式事务和声明式事务的支持，编程式事务是嵌在业务代码中的，而声明式事务是基于xml文件配置。
readOnly 	事务隔离级别，表示只读数据，不更新数据
PROPAGATION_REQUIRED 	支持当前事务，如果当前没有事务，就新建一个事务
PROPAGATION_SUPPORTS 	支持当前事务，如果当前没有事务，就以非事务方式执行
PROPAGATION_REQUIRES_NEW 	新建事务，如果当前存在事务，把当前事务挂起
PROPAGATION_MANDATORY 	支持当前事务，如果当前没有事务，就抛出异常
PROPAGATION_NOT_SUPPORTED 	以非事务方式执行操作，如果当前存在事务，就把当前事务挂起
PROPAGATION_NEVER 	以非事务方式执行，如果当前存在事务，则抛出异常
hashCode

hashCode方法本质就是一个哈希函数，这是Object类的作者说明的。Object类的作者在注释的最后一段的括号中写道：将对象的地址值映射为integer类型的哈希值。但hashCode()并不完全可靠的，有时候不同的对象他们生成的hashcode也会一样，因此hashCode()只能说是大部分时候可靠。因此我们也需要重写equals()方法，但因为重写的equals()比较全面比较复杂，会造成程序效率低下，而利用hashCode()进行对比，则只要生成一个hash值进行比较就可以了，效率很高。因此，正常的操作流程是先用hashCode()去对比两个对象，如果hashCode()不一样，则表示这两个对象肯定不相等，直接返回false,如果hashCode()相同，再对比他们的equals()

综上所述：equals()相等的两个对象hashCode()一定相等。hashCode()相等的两个对象equal()不一定相等。
导包

导包只可以导到当前层，不可以再导入包里面的包中的类

java.awt.*是导入java\awt包下所有的类，并不包括其子包下的类

java.awt.event.才能导入java\awt\event包下的类
Java体系结构

Java体系结构包括四个独立但相关的技术：

    Java程序设计语言

    Java.class文件格式

    Java应用编程接口（API）

    Java虚拟机 

线程方法

启动线程是调用start方法，把线程的run方法当普通方法，就直接用实例.run()执行就好了

没有看到start，所以是普通方法调用
重写和重载

重载和重写都是多态的一种体现方式，重载是编译期间的活动，重写是运行期间的活动

重载是在一个类中定义相同的名字的方法，方法的参数列表或者类型要互相不同，但是返回值类型不作为是否重载的标准，可以修改可见性； 重写是不同的，要求子类重写基类的方法时要与父类方法具有相同的参数类型和返回值，可见性需要大于等于基类的方法
数组length()

java的String底层是char数组，它的length()返回数组大小，而unicode中一个汉字是可以用一个char表示的
多继承

Java只支持单继承，实现多重继承三种方式：（1）直接实现多个接口 （2）扩展(extends)一个类然后实现一个或多个接口 （3）通过内部类去继承其他类
内部类

    静态内部类

        静态内部类本身可以访问外部的静态资源，包括静态私有资源，但是不能访问非静态资源，可以不依赖外部类实例而实例化 

    成员内部类

        成员内部类本身可以访问外部的所有资源，但是自身不能定义静态资源，因为其实例化本身就还依赖着外部类 

    局部内部类

        局部内部类就像一个局部方法，不能被访问修饰符修饰，也不能被static修饰

        局部内部类只能访问所在代码块或者方法中被定义为final的局部变量 

    匿名内部类

        没有类名的内部类，不能使用class，extends和implements，没有构造方法

        多用于GUI中的事件处理

        不能定义静态资源

        只能创建一个匿名内部类实例

        一个匿名内部类一定是在new后面的，这个匿名类必须继承一个父类或者实现一个接口

        匿名内部类是局部内部类的特殊形式，所以局部内部类的所有限制对匿名内部类也有效 

类之间的关系

USES-A：依赖关系，A类会用到B类，这种关系具有偶然性，临时性。但B类的变化会影响A类。这种在代码中的体现为：A类方法中的参数包含了B类。

关联关系：A类会用到B类，这是一种强依赖关系，是长期的并非偶然。在代码中的表现为：A类的成员变量中含有B类。

HAS-A：聚合关系，拥有关系，是关联关系的一种特例，是整体和部分的关系。比如鸟群和鸟的关系是聚合关系，鸟群中每个部分都是鸟。

IS-A：表示继承。父类与子类，这个就不解释了。

要注意：还有一种关系：组合关系也是关联关系的一种特例，它体现一种contains-a的关系，这种关系比聚合更强，也称为强聚合。它同样体现整体与部分的关系，但这种整体和部分是不可分割的。


