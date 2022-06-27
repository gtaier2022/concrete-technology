集合 容器

各集合的特点

LIst:有序可重复

set:无序 不可重复

Map:键值对

Queue:排序

底层原理

ArrayList:[]

Vector:[]

LinkedList[]+链表

Set 

HashSet hashmap

LinkedHashMap hashmap

TreeSet： hei

HashMap:[]+链表

LikedHashMap：

如何选用集合

如果我们需要根据键获取值的话就选择Map TreeMap HashMap ConcrentMap

存放元素的话Colllection集合，需要确保元素唯一的set,不需要list

为什么要使用集合

使用集合主要是克服数组存储数据的缺点，单一性和固定长度。

ArarryList和Vector

ArrayList是适用于查询操做 [] 线程安全

Vector 线程安全，[]

ArrayList和LinkedList

[]//[]+链表

线程都不安全

arrayList做查询和尾插法get(index)[]

LinkdedList查询效率低，适合做插入



ArrayLIst的扩容机制

新建一个~~会有一个固定的长度，假如超了的话添加2n+1

然后把以前的数据复制到新~~的前面，copy的机制的极度的消耗性能，所以一般都是再插入的时候都是要确定一i个长度和做尾插法，性能可能会超过linkedlvist。

- comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序[pojo类中，再类上确定比较更新]
- `comparator`接口实际上是出自 java.util 包它有一个`compare(Object obj1, Object obj2)`方法用来排序[方法中]

一般我们需要对一个集合使用自定义排序时，



无序性:不是按照数组的索引排序的，而是根据数组的hash值来确定存储空间的

不可重复性：是两个对象通过equals比较为false(重写equal和hashcode)



HashSet，LinkedHashSet，TreeSet

接口都是Set 保证元素唯一 不是线程安全的

HashSet=》hashMap=》哈希表

LinkedHashSet =>链表+HashMap

TreeSet红黑树

应用：TreeSet排序 

​			LinkedHashSet：FIFO

​			HashSet:不需要保证元素的插入和取出的顺序





Hashmap和HashTable

hashmao[线程不安全]

hashtale[线程安全，ConcurrentHashMap]

效率:HashMap效率高于HashTable[不要使用HashTable]

HashTable不允许有null HashMap运行一个key为null和多个value为null

hashmap:链表加数组[链表8就会判断数组长度如果数组长度大于64就转化为红黑树]，hashtable[数组+链表]

HashTable初始容量[11] [2n+1] 给定为n容量就为n 

HashMap初始容量[16] [2n]  固定为n 容量为2的n次方



HashMap 和HashSet[`HashSet` 底层就是基于 `HashMap` 实现的。]



TreeSet实现了SortedMap使它具有排序的功能



== 与equals

==基本类型比较的是值 引用类型比较的是地址值，

equals不重写的话==一样，重写就是比较内容



HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**

**所谓扰动函数指的就是 HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞

HashMap 的长度为什么是 2 的幂次方

HashMap为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就在把数据存到哪个链表中的算法；
这个算法实际就是取模hash%length，计算机中直接求余效率不如位移运算，源码中做了优化hash&(length-1)，
hash%length==hash&(length-1)的前提是length是2的n次方；

HashMap 有哪几种常见的遍历方式?

迭起器[迭代器 EntrySet]

迭代器 KeySet



ConcurrentHashMap 和 Hashtable 的区别

ConcurrentHashMap [数组+链表+红黑树] +并发控制采用synchronized和CAS来控制。[分段锁] [节点锁]

HashTable:`同一把锁` 使用synchronized来保证线程安全，效率低。[全表锁]

使用put添加元素，另一个线程就不能用put添加元三年，也不能使用get。



什么是线程和进程

进程是程序的一次执行过程

线程是一个比进程更小的执行单位[同类的多个线程共享进程的**堆**和**方法区**资源,每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，在各个线程之间作切换工作时，负担要比进程小得多]

线程和进程的区别

 线程是进程划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反。



2.为什么程序计数器会是私有的呢?

程序计数器的作用是记录程序执行的位置

切换线程之后恢复到以前执行的位置。

3.虚拟机栈和本地方法栈为什么是私有的

保证线程中的局部变量不被别的线程访问到

 堆:用于存放新创建的对象

方法区：用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。



并行[多个作业再同一时刻运行]

并发[多个作业在同一时刻执行]



为什么要使用多线程

多个线程同时运行减少切换线程上下文开销。

可以提高性能和并发能力。

带来的问题：死锁，内存泄漏，线程不安全。

线程的生命周期和状态：

创建线程后处在新建状态，调用start后处在可运行状态，抢到cpu的时间片处在可运行状态，线程执行wait之后进入等待状态[靠其他线程通知才能回到运行状态]， 调用sleep(long millis)方法线程进入到超时等待，线程调用同步方法没有获取锁的情况下会进入到阻塞状态，执行完run方法后线程会进入到终止状态。

上下文切换:

线程从当前的状态切换为另一种状态[主动让出cpu的执行权]操做系统会保留当前线程的运行状态，以便下次运行的时候以当前状态继续运行。

线程死锁:

多个线程同时被阻塞，他们中的一个或者全部都在等待某一个资源被释放。

避免。[两个线程都想要对方不释放的资源造成死锁]。

wait方法和sleep方法

sleep()方法没有释放锁

wait（）方法释放了锁，

wait()调用不会自动苏醒，sleep（）方法会自动苏醒。

为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？

 调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行



说一说自己对于 synchronized 关键字的了解

它解决的是多个线程之间的访问资源的同步性，

被它修饰的方法或代码块在任意时刻只能有一个线程执行。

修饰实列方法，修饰静态方法，修饰代码块，



单例模式了解吗？



synchronized 关键字的底层原理

synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit指令则指明同步代码块的结束位置。

volatile[关键字 除了防止 JVM 的指令重排 ，还有一个重要的作用就是保证变量的可见性]

并发编程的三个重要特性

原子性[`synchronized` 可以保证代码片段的原子性]

可见性[`volatile` 关键字可以保证共享变量的可见性。]

有序性[`volatile` 关键字可以禁止指令进行重排序优化。]



说说 synchronized 关键字和 volatile 关键字的区别

- **`volatile` 关键字**是线程同步的**轻量级实现**，所以 **`volatile `性能肯定比` synchronized `关键字要好** 。但是 **`volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块** 。
- **`volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。**
- **`volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。**



ThreadLocal

可以将`ThreadLocal`类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据

每个`Thread`中都具备一个`ThreadLocalMap`，而`ThreadLocalMap`可以存储以`ThreadLocal`为 key ，Object 对象为 value 的键值对。

ThreadLocal 内存泄露问题

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样一来，`ThreadLocalMap` 中就会出现 key 为 null 的 Entry。假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。ThreadLocalMap 实现中已经考虑了这种情况，在调用 set()、get()、remove() 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后 最好手动调用`remove()`方法

为什么要用线程池？

就是节约创建和销毁线程花的时间的资源[所有的池化思想都是这样的]



实现 Runnable 接口和 Callable 接口的区别

Runable接口不会返回结果和抛出异常，Callback可以



执行 execute()方法和 submit()方法的区别是什么呢？

execute()方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否.

submit()方法用于提交需要返回值的任务。线程池会返回一个 Future类型的对象，通过这个 Future 对象可以判断任务是否执行成功

如何创建线程池

通过构造方法实现 

ThreadPoolExector

 */
public ThreadPoolExecutor(int corePoolSize,
                      int maximumPoolSize,
                      long keepAliveTime,
                      TimeUnit unit,
                      BlockingQueue<Runnable> workQueue,
                      ThreadFactory threadFactory,
                      RejectedExecutionHandler handler) 

通过工具类

 Executors



线程池处理流程：核心线程=》等待队列=》线程池=》处理策略



#{}参数占位符

${}变量占位符

mybatis标签

select|insert|update|delete|resultMap|parameterMap|sql|include|selectKey|trim|where|set|foreach|if|choose|when|otherwise|bind

namespace：映射到接口的mapper路径，id映射为方法名。

分页插件的原理:连接方法，在sql里面加l imit 2

springboot自动装配原理:Spring Boot 通过`@EnableAutoConfiguration`开启自动装配，通过 SpringFactoriesLoader 最终加载`META-INF/spring.factories`中的自动配置类实现自动装配，自动配置类其实就是通过`@Conditional`按需加载的配置类，想要其生效必须引入`spring-boot-starter-xxx`包实现起步依赖

控制反转 程序中手动创建对象的控制权，交由 Spring 框架来管理。

依赖注入:将实列对象注入进去。

spring的设计模式[工厂设计模式,单例设计模式,代理模式]

Spring AOP 和 AspectJ AOP 有什么区别

Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。

Spring的模块[Spring Core, SpringAspects,SpringAop,SpringData Access, SpringWeb]

Spring Data Access[spring-jdbc,spring-tx,spring-orm,spring-oxm,spring jms]

Spring:轻量的javsa的框架实现了控制反转和依赖注入。

AOP(Aspect-Oriented Programming:面向切面编程)(动态代理)

将一个类声明为 Bean 的注解有哪些[ @Component @Repository @Service  @Controller ]

@Component 和 @Bean 的区别是什么？

`@Component` 注解作用于类，而`@Bean`注解作用于方法。

`@Component`通常是通过类路径扫描来自动侦测

@Bean注解通常是我们在标有该注解的方法中定义产生这个 bean,@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。

注入 Bean 的注解有哪些？

@Autowired[byType] @Resource[byType+byName] @Inject

@Autowired@Qualifier

Bean 的作用域有哪些?[singleton prototype,request,session,global-session]

单例 Bean 的线程安全问题了解吗[多个线程操作同一个对象的时候是存在资源竞争的, Bean 中尽量避免定义可变的成员变量。类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。大部分 Bean 实际都是无状态（没有实例变量）的（比如 Dao、Service），这种情况下， Bean 是线程安全的]

Bean 的生命周期了解么?

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个 Bean 的实例。
- 如果涉及到一些属性值 利用 `set()`方法设置一些属性值。
- 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入 Bean 的名字。
- 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoader`对象的实例。
- 如果 Bean 实现了 `BeanFactoryAware` 接口，调用 `setBeanFactory()`方法，传入 `BeanFactory`对象的实例。
- 与上面的类似，如果实现了其他 `*.Aware`接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
- 如果 Bean 实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
- 当要销毁 Bean 的时候，如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。



SpringMVC 工作原理了解吗?

1. 客户端（浏览器）发送请求，直接请求到 `DispatcherServlet`。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping`，解析请求对应的 `Handler`。
3. 解析到对应的 `Handler`（也就是我们平常说的 `Controller` 控制器）后，开始由 `HandlerAdapter` 适配器处理。
4. `HandlerAdapter` 会根据 `Handler`来调用真正的处理器开处理请求，并处理相应的业务逻辑。
5. 处理器处理完业务后，会返回一个 `ModelAndView` 对象，`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
6. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
7. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
8. 把 `View` 返回给请求者（浏览器）
