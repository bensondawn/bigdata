## 1、微服务管理(重新思考微服务隔离)

Spring Cloud的注册中心可以由Eureka、Consul、Zookeeper、ETCD等来实现，这里推荐使用Spring Cloud Eureka来实现注册中心，它基于Netfilix的Eureka做了二次封装，完成分布式服务中服务治理的功能，微服务系统中的服务注册与发现都通过这个注册中心来进行管理。

Spring Cloud Gateway作为路由网关，实现路由转发。

## 2、cookie和session的关系

服务器收到第一次请求，开辟一块Session空间，生成一个SessionId，并通过响应头的Set-Cookie：“JSESSIONID=XXXXXXX”命令，向客户端发送设置cookie的响应；客户端收到响应，在本机浏览器设置JSESSIONID的cookie信息，该cookie的生命周期为会话结束。以后每次浏览器每次向服务端请求时都会带上这个cookie信息，服务端通过读取请求头中的cookie信息，获取sessionId。服务端通过sessionId判断是不是同一个会话。

## 3、get和post的区别

GET请求在URL中传送的参数是有长度限制的，而POST没有。
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
GET参数通过URL传递，POST放在Request body中。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
GET请求只能进行url编码，而POST支持多种编码方式。
GET请求会被浏览器主动cache，而POST不会，除非手动设置。
GET产生的URL地址可以被Bookmark，而POST不可以。
GET在浏览器回退时是无害的，而POST会再次提交请求。

get和post本质上没有区别，底层都是通过TCP传输数据。
get请求只产生一个数据包；post产生两个数据包；
get请求，浏览器会把header和data一起发过去，服务器响应200。
post请求浏览器先发送header，服务器响应100；后发送data，服务器响应200。

## 4、Websocket和Socket、http的区别和联系

- Socket并不是一个协议，而是为了方便用户使用TCP和UDP而抽象出来的一层API接口。
- Websocket和http一样，是一个应用层协议。
- Websocket和http都是基于TCP/IP的可靠型传输协议。
- Websocket是双向通信协议，可以双向发送和接收信息；http是单向的。
- Websocket是需要浏览器和服务器握手建立连接；http是浏览器向服务器发起的连接，服务器预先不知道该连接。
- Websocket在建立握手时，数据是通过http传输的，连接建立后的数据传输是不需要http的，是通过TCP进行数据传输。

![](D:\大数据\bigdata\picture\20170626140009906.png)

## 5、三次握手和四次挥手

http://www.cnblogs.com/zeze/p/9509926.html

| 字段 |                             含义                             |
| :--: | :----------------------------------------------------------: |
| URG  |      紧急指针是否有效，为1，表示某一位需要被优先处理。       |
| ACK  |                 确认号是否有效，一般置为1。                  |
| PSH  |        提示接收端应用程序立即从TCP缓冲区把数据读走。         |
| RST  |                 对方要求重新建立连接，复位。                 |
| SYN  |                   请求建立连接，设置为1。                    |
| FIN  | 希望断开连接。FIN=1表示：此报文段的发送方的数据已经发送完毕，并要求释放运输连接。 |

![](D:\大数据\bigdata\picture\20180810095413143.png)



![](D:\大数据\bigdata\picture\20180810101919196.png)

## 6、MySQL数据库引擎MyISAM和InnoDB的区别

> * 事务支持

MyISAM：不支持事务，强调的是性能，是非事务安全的，当select查询比较多，用MyISAM比较合适。

InnoDB：支持事务，支持外键，是事务安全的，默认开启自动提交，宜合并事务，一同提交，减小数据库多次提交导致的开销，大大提高性能。具有事务(commit)、回滚(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。当update和insert比较多时，用InnoDB合适。

> * 存储结构

MyISAM：每个数据表在磁盘上存储成三个文件，.FRM文件存储表定义，.MYD数据文件，.MYI索引文件。

InnoDB：所有的表都保存在同一个数据文件中，InnoDB表的大小只受限于操作系统的大小，一般为2GB。

> * 存储空间

MyISAM：可被压缩，存储空间较小。
InnoDB：需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引。

> * 全文索引

MyISAM：支持(FULLTEXT类型的)全文索引
InnoDB：不支持(FULLTEXT类型的)全文索引，但是innodb可以使用sphinx插件支持全文索引，并且效果更好。

> * 表的总行数

MyISAM：保存有表的总行数，如果select count(*) from table;会直接取出出该值。
InnoDB：没有保存表的总行数(只能遍历)，如果使用select count(*) from table；就会遍历整个表，消耗相当大，但是在加了wehre条件后，myisam和innodb处理的方式都一样。

## 7、MySQL删除重复数据，只保留一条

![](D:\大数据\bigdata\picture\20180901115402863.jpg)

![](D:\大数据\bigdata\picture\20180903200615842.jpg)

delete from dept where dname in (select t.dname from (select dname from dept group by dname having count(1) > 1)t)

## 8、collection和map的结构

Collection：List、Set、Queue

> * List：
>   + ArrayList：底层是数组，非同步的。
>   + Vector：跟ArrayList类似，也是数组，是同步的，线程安全的，但是效率低，用的少。
>   + LinkedList：底层是链表。

> * Set:
>   + HashSet：为快速查找而设计的set，存入HashSet的对象必须定义HashCode和equals。
>   + TreeSet：基于红黑树的实现，存入的数据会排序。
>   + LinkedHashSet：类似于HashSet，不过用链表维护元素的顺序(插入顺序)。

> * Queue：扩展了Queue接口，增加了很多其它的方法。
>   + LinkedList：基于链表的实现，是一个普通队列，具有入队出队等操作。
>   + PriorityQueue：是一个比较标准的队列实现类，之所以说其标准，是因为其保存队列元素不是按其入队顺序，而是按照队列大小对其重新排序，因此出队的元素通常是最小元素。
>   + Deque：基于Queue的子类。
>     + ArrayDeque：基于数组的实现，具有入队出队等操作。
>     + LinkedList：基于链表的实现，具有入队出队等操作。

Map：

> * HashMap：基于哈希表的实现，它取代了HashTable。
> * TreeMap：基于红黑树的实现，存入的数据是要经过排序的。
> * LinkedHashMap：类似于HashMap，区别是它采用链表维护元素顺序(插入顺序)。

线程安全：

* Vector、Statck堆栈类、Hashtable(比Hashmap多了个线程同步)、enumeration(枚举，相当于迭代器)

## 9、Java内存模型

> * 栈：函数中的局部变量和函数调用过程中的临时变量，还有对象的引用都存在该区域。

> * 堆：对象实例存在该区域。

> * 方法区：包含常量池，存放了要加载的类的信息（名称、修饰符等）、静态常量、final类型的常量、Field信息、方法信息。方法区是被Java线程锁共享的，不像Java堆中其他部分一样会频繁被GC回收。

> * 程序计数器：记录线程执行的字节码的行号。

## 10、JVM垃圾回收原理

https://www.cnblogs.com/1024Community/p/honery.html

## 11、TCP和UDP的重传机制

TCP：

UDP：应用层实现采用超时重传,实现udp的可靠传输。在规定的时间内，若没有收到sendto的信息，我们就认为数据包丢失，打破recvfrom的阻塞式等待，设置一个闹钟定时器alarm,当收到SIGALRM信号时，程序中断，转去处理中断响应函数，通知客户端进行重传。

## 12、https和http的区别

HTTPS（Secure Hypertext Transfer Protocol）安全超文本传输协议。
它是一个安全通信通道，基于HTTP开发，用于在客户计算机和服务器之间交换信息。它使用安全套接字层(SSL)进行信息交换，简单来说它是HTTP的安全版。
它是由Netscape开发并内置于其浏览器中，用于对数据进行压缩和解压操作，并返回网络上传送回的结果。HTTPS实际上应用了Netscape的安全套接字层（SSL）
作为HTTP应用层的子层。（HTTPS使用端口443，而不是象HTTP那样使用端口80来和TCP/IP进行通信。）

HTTPS和HTTP的区别：
https协议需要到ca申请证书，一般免费证书很少，需要交费。
http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。
http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
http的连接很简单，是无状态的。
HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全。

## 13、负载均衡(需学习)

> * SpringCloud Ribbon：所有的服务通过Eureka 进行注册到注册中心，客户端的调用就可以利用 Ribbon 技术来实现。Ribbon 是一个服务调用的组件，并且是一个客户端实现负载均衡处理的组件。服务器端实现负载均衡可以使用 Nginx、 HAProxy、LVS 等。
> * 负载均衡集群：作用将大量的并发请求分担到多个处理节点，由于单个处理节点的故障不影响整个服务，负载均衡集群同时也实现了高可用性。一般提到的负载均衡(Load Balance)，是指实现负载均衡集群。负载均衡实现了横向扩展（Scale Out），避免纵向的升级（Scale Up）换代。服务器端的负载均衡可以通过Nginx、HAProxy、LVS 等。
> * MySQL高可用架构之MHA。

## 14、GC是什么时候触发的

由于对象进行了分代处理，因此垃圾回收区域、时间也不一样。GC有两种类型：Scavenge GC和Full GC。

Scavenge GC：
一般情况下，当新对象生成，并且在Eden申请空间失败时，就会触发Scavenge GC，对Eden(新new的对象会放在该区域，经过GC回收幸存下来的会存入Survivor Space幸存者区)区域进行GC，清除非存活对象，并且把尚且存活的对象移动到Survivor区。然后整理Survivor的两个区。这种方式的GC是对年轻代的Eden区进行，不会影响到年老代。因为大部分对象都是从Eden区开始的，同时Eden区不会分配的很大，所以Eden区的GC会频繁进行。因而，一般在这里需要使用速度快、效率高的算法，使Eden去能尽快空闲出来。

Full GC：
对整个堆进行整理，包括Young、Tenured和Perm。Full GC因为需要对整个堆进行回收，所以比Scavenge GC要慢，因此应该尽可能减少Full GC的次数。在对JVM调优的过程中，很大一部分工作就是对于Full GC的调节。有如下原因可能导致Full GC：
a) 年老代（Tenured）被写满；
b) 持久代（Perm）被写满；
c) System.gc()被显示调用；
d) 上一次GC之后Heap的各域分配策略动态变化；

## 15、微服务的理解

微服务各个服务可以独立部署，各个服务之间是松耦合的，每个微服务只关注完成一个或少数任务。每个微服务都有自己独立处理通讯的机制，可以部署在一个或多个服务器上。

## 16、字符流和字节流的区别

Java 流在处理上分为字符流和字节流。字符流处理的单元为 2 个字节的 Unicode 字符，分别操作字符、字符数组或字符串，而字节流处理单元为 1 个字节，操作字节和字节数组。

Java 内用 Unicode 编码存储字符，字符流处理类负责将外部的其他编码的字符流和 java 内 Unicode 字符流之间的转换。而类 InputStreamReader 和 OutputStreamWriter 处理字符流和字节流的转换。字符流（一次可以处理一个缓冲区）一次操作比字节流（一次一个字节）效率高。 

## 17、Spring AOP和Spring IOC

> * IOC：控制反转，具有依赖注入功能的容器，可以创建对象的容器；传统的程序设计中通常是由调用者创建被调用者的实例；在Spring中，创建被调用者的工作不再由调用者来完成，由IOC控制对象的创建，因此称为控制反转。IOC容器管理bean的生命周期，一旦创建这个对象，就不再帮你管理这个对象了，交给GC管理。
> * DI：依赖注入，容器创建对象后，处理对象的依赖关系。组件之间的依赖关系由容器运行期间决定，由容器动态的将某个依赖注入到组件中；依赖注入的目的是为了提升组件重用的频率，为系统搭建一个灵活可扩展的平台。依赖注入是利用反射实现注入的，反射允许程序运行时动态的生成对象，执行对象的方法，改变对象的属性。**控制反转和依赖注入是同一个概念不同角度的描述。**依赖注入的方式：set注入方式，静态工厂注入方式，构造方法注入方式，基于注解的方式。
> * AOP：面向切面编程，把那些分散在各处且与对象核心功能无关的代码（横切代码）的存在，使得模块复用难度增加。AOP则将封装好的对象剖开，找出其中对多个对象产生影响的公共行为，并将其封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），切面将那些与业务无关，却被业务模块共同调用的逻辑提取并封装起来，减少了系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。比如日志记录，拦截器，事务管理。
>   + 连接点(join point)：对应的是具体被拦截的方法，在SpringbootAOP例子中printUser就是一个连接点，AOP通过动态代理的技术把它织入对应流程中。
>   + 切点(point cut)：我们的切面不单单是应用于单个方法，通过正则表达式去适配连接点，让切面应用于多个连接点。
>   + 通知(advice)：前置通知、后置通知、环绕通知、事后返回通知和异常通知。
>   + 目标对象(target)：即被代理对象，连接点所在的对象。
>   + 引入(introduction)：引入新的类和方法，增强现有Bean的功能。就是引入切面，实现Bean的增强。
>   + 织入(weaving)：通过动态代理技术，将约定织入流程的过程。
>   + 切面(aspect)：是一个可以定义切点、各类通知和引入的内容，Spring AOP通过切面增强Bean的功能或者将对应的方法织入流程。

## 18、Springboot和SpringMVC的区别

> * Spring 是一个“引擎”。

> * Spring MVC是Spring的一个模块，是一个web框架。通过Dispatcher Servlet, ModelAndView 和 View Resolver让开发web应用变得很容易。解决的问题领域是网站应用程序或者服务开发——URL路由、Session、模板引擎、静态Web资源等等。

> * Springboot是基于Spring的快速开发整合包，Spring Boot有一套默认配置，我们可以把它看做比较通用的约定，而Spring Boot遵循的也是**约定优于配置**原则，在Spring Boot中，你会发现你引入的所有包都是*starter(懒人整合包)*形式。

## 19、事务

> * 原子性（Atomicity）：事务是一个原子操作，由一系列动作组成。事务的原子性确保动作要么全部完成，要么完全不起作用。

> * 一致性（Consistency）：一旦事务完成（不管成功还是失败），系统必须确保它所建模的业务处于一致的状态，而不会是部分完成部分失败。在现实中的数据不应该被破坏。

> * 隔离性（Isolation）：可能有许多事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏。

> * 持久性（Durability）：一旦事务完成，无论发生什么系统错误，它的结果都不应该受到影响，这样就能从任何系统崩溃中恢复过来。通常情况下，事务的结果被写到持久化存储器中。

> * Spring事务：事务是一系列的动作，它们综合在一起才是一个完整的工作单元，这些动作必须全部完成，如果有一个失败的话，那么事务就会回滚到最开始的状态，仿佛什么都没发生过一样。 在企业级应用程序开发中，事务管理必不可少的技术，用来确保数据的完整性和一致性。

## 20、Java并发多线程

#### 创建线程：

* 1、继承Thread类创建线程；
* 2、实现Runnable接口创建线程；
* 3、实现Callable接口创建线程；
* 4、通过线程池创建多线程；

#### 继承Thread和实现Runnable接口的区别

* 实现Runnable接口避免多继承的局限；
* 实现Runnable接口更好的体现共享的概念；

#### CPU资源占用情况：

* cpu划分时间片给多线程，同一个时间片CPU只执行一个线程，CPU高速切换线程的执行，好像是线程一块执行的，起始是高速切换。

#### 线程放弃CPU使用权的情况：

> * 线程暂时放弃CPU操作，例如调用yield()方法。
> * 当前线程进入阻塞状态，例如I/O阻塞。
> * 当前线程执行结束，即运行完run()方法。

## 21、Springboot整合MyBatis

## 22、Spring生成对象默认是单例的，通过scope属性可以更改为多例

## 23、Java中Sleep和Wait的区别

> > > > > * 对于sleep()方法，我们首先要知道该方法是属于Thread类中的。而wait()方法，则是属于Object类中的。

* sleep()方法导致了程序暂停执行指定的时间，让出cpu该其他线程，但是他的监控状态依然保持者，当指定的时间到了又会自动恢复运行状态。在调用sleep()方法的过程中，线程不会释放对象锁。

* 而当调用wait()方法的时候，线程会放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备获取对象锁进入运行状态。

## 24、数据库事务应用

## 25、Redis数据实时同步数据库的问题

## 26、如何处理异常和常见的异常

## 27、整体性能方面的考虑

