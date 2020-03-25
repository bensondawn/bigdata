# 大数据学习笔记

### 1、Hadoop

[Hadoop](http://baike.baidu.com/view/908354.htm)是一个由Apache基金会所开发的[分布式系统](http://baike.baidu.com/view/991489.htm)基础架构。

用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力进行高速运算和存储。

具有可靠、高效、可伸缩的特点。

Hadoop的核心是HDFS、YARN、Mapreduce。

下图是hadoop生态系统，集成spark生态圈。在未来一段时间内，hadoop将于spark共存，hadoop与spark都能部署在yarn、mesos的资源管理系统之上。

![](E:\GitStorage\bigdata\picture\20160105160808168.png)

### 2、[HDFS](http://hadoop.apache.org/)（Hadoop分布式文件系统）

​		参考：[HDFS体系结构(NameNode、DataNode详解)](https://www.cnblogs.com/jackchen-Net/p/6506321.html)

​					[初识HDFS](https://www.cnblogs.com/wxplmm/p/7239342.html)

​					[解读Secondary NameNode的功能](https://www.cnblogs.com/smartloli/p/4342340.html)

​		首先我们来认识一下HDFS， HDFS（Hadoop Distributed File System ）Hadoop分布式文件系统。它其实是将一个大文件分成若干块保存在不同服务器的多个节点中。通过联网让用户感觉像是在本地一样查看文件，为了降低文件丢失造成的错误，它会为每个小文件复制多个副本（默认为三个），以此来实现多机器上的多用户分享文件和存储空间。

HDFS特点：

> * 保存多个副本，且提供容错机制，副本丢失或宕机自动恢复。默认存3份。
> * 运行在廉价的机器上。
> * 适合大数据的处理。因为小文件也占用一个块，小文件越多(1000个1k文件)块越多，NameNode压力越大。
> * 数据复制原则是除了最后一个块之外的文件中的所有块都是相同的大小。
> * 数据放置策略是将一个副本放在本地机架中的一个节点上，另一个位于不同（远程）机架中的节点上，而最后一个位于不同节点上远程机架。
> * 块大小：Hadoop1版本里默认为64M，Hadoop2版本里默认为128M

如：将一个大文件分成三块A、B、C的存储方式

HDFS的基本结构：

![](E:\GitStorage\bigdata\picture\20170726135639328.jpg)

![](E:\GitStorage\bigdata\picture\20170305150329985.png)

> * **Client**：就是客户端。
>* 文件切分。当文件上传hdfs的时候，client将文件切分成一个一个的block进行存储。
>   * 与namenode交互获取文件位置信息。
>   * 从namenode获取文件位置信息后，与datanode交互，实现数据的读取和写入。
>   
>* **NameNode**：是Master节点，有点类似Linux里的root根目录，是整个文件系统的管理节点。包含目录与数据块之间的关系(靠fsimage和edits来实现)，数据块和节点之间的关系。**fsimage**文件与**edits**文件是Namenode结点上的核心文件。
> 
>  * 整个文件系统的文件目录树。
> 
>    * 文件/目录的元信息和每个文件对应的数据块列表。
>     * 接收处理客户端的读写请求，给Client返回DataNode的信息。
> 
>* **Secondary Namenode**：作用是解决edits文件过大，Namenode重启的时候花费很长时间，因为很多改动要合并到fsimage文件上；如果Namenode宕机了，可能会丢失很多改动，Secondary Namenode能解决这个问题。工作原理如下：
>   * 首先，它定时到NameNode去获取edits，并更新到fsimage上。
>   * 一旦它有新的fsimage文件，它将其拷贝回NameNode上。
>  * NameNode在下次重启时回使用这个新的fsimage文件，从而减少重启的时间。
> 
>* **Quorum Journal Node**：用于Namenode之间共享数据，保证数据的状态一致。当一个Namenode所在的服务器宕机时，可以在数据不丢失的情况下，手工或者自动切换到另一个Namenode提供服务。 
> 
>  * **DataNode**：DataNode在HDFS中真正存储数据。
> 
>     * 接收Client的读写请求。
> 
>    - DataNode在存储数据的时候是按照block为单位读写数据的。block是hdfs读写数据的基本单位。
>     - block本质上是一个逻辑概念，意味着block里面不会真正的存储数据，只是划分文件的。
>    - block里也会存副本，保证数据的安全，默认的数量是3个。
> * **fsimage**：它是NameNode启动时对整个文件系统的快照。（文件系统的目录树信息）
> 
> * **edits**：它是在NameNode启动后，对文件系统的改动序列。（在Namenode启动后所有对目录结构的增加，删除，修改等操作都会记录到edits文件中，并不会同步的记录在fsimage中。）
>
> * **注意**：
>
> ​		而当Namenode结点关闭的时候，不会将fsimage与edits文件进行合并，这个合并的过程实际上是发生在Namenode启动的过程中。
>
> ​		也就是说，当Namenode启动的时候，首先装载fsimage文件，然后在应用edits文件，最后还会将最新的目录树信息更新到新的fsimage文件中，然后启用新的edits文件。
>
>   ​		整个流程是没有问题的，但是有个小瑕疵，就是如果Namenode在启动后发生的改变过多，会导致edits文件变得非常大，大得程度与Namenode的更新频率有关系。
>
>   ​		那么在下一次Namenode启动的过程中，读取了fsimage文件后，会应用这个无比大的edits文件，导致启动时间变长，并且不可控，可能需要启动几个小时也说不定。
>
>   ​		**Namenode的edits文件过大的问题，也就是Secondary Namenode要解决的主要问题，Secondary Namenode会按照一定规则被唤醒，然后进行fsimage文件与edits文件的合并，防止edits文件过大，导致Namenode启动时间过长。**

### 3、zookeeper

​		参考：[ZooKeeper 的功能和原理](<https://blog.csdn.net/WeiJiFeng_/article/details/79775738>)

​		ZooKeeper 是一个开源的分布式协调服务，由雅虎创建，是 Google Chubby 的开源实现。分布式应用程序可以基于 ZooKeeper 实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master 选举、配置维护，名字服务、分布式同步、分布式锁和分布式队列等功能。

zookeeper中的角色：

> * Leader：一个zookeeper集群同一时刻只会有一个Leader，其它的都是Follower或Observer。ZooKeeper 集群的所有机器通过一个 Leader 选举过程来选定一台被称为Leader的机器，Leader服务器为客户端提供读和写服务。
> * Folllow：Follower 和 Observer 都能提供读服务，不能提供写服务。
> * Observer：Observer机器不参与 Leader 选举过程，也不参与写操作的『过半写成功』策略，因此 Observer 可以在不影响写性能的情况下提升集群的读性能。
> * 在装有 ZooKeeper 的机器的终端执行 zookeeper-server status 可以看当前节点的ZooKeeper是什么角色（Leader or Follower）。

应用场景：

ZooKeeper 是一个高可用的分布式数据管理与协调框架。基于对ZAB算法的实现，该框架能够很好地保证分布式环境中数据的一致性。也是基于这样的特性，使得 ZooKeeper 成为了解决分布式一致性问题的利器。

> * 数据发布与订阅（配置中心）
>
> * 命名服务
>
> * 分布式协调服务/通知：
>
>   ​		ZooKeeper 中特有 Watcher 注册与异步通知机制，能够很好的实现分布式环境下不同机器，甚至不同系统之间的通知与协调，从而实现对数据变更的实时处理。使用方法通常是不同的客户端，如果机器节点 发生了变化，那么所有订阅的客户端都能够接收到相应的Watcher通知，并做出相应的处理。ZooKeeper的分布式协调/通知，是一种通用的分布式系统机器间的通信方式。
>
> * Master选举：Master 选举可以说是 ZooKeeper 最典型的应用场景了。比如 HDFS 中 Active NameNode 的选举、YARN 中 Active ResourceManager 的选举和 HBase 中 Active HMaster 的选举等。
> * 分布式锁
> * 共享锁

### 4、Hbase

​		HBase是基于HDFS的分布式列式NoSQL数据库，适合大规模数据存储且易于横向扩展，基于Key-Value的存储模式能够适应海量数据的实时查询。此外，HBase与其他Hadoop生态圈软件如MapReduce，Spark，Storm等都能够进行简单便捷的集成。

Hbase中的角色：

> * HMaster：主要负责Table和Region的管理工作：
>   * 管理用户对Table的增、删、改、查操作。
>   *  管理HRegionServer的负载均衡，调整Region分布。
>   * 在Region Split后，负责新Region的分配。
>   * 在HRegionServer停机后，负责失效HRegionServer 上的Regions迁移。
> * HRegionServer：HRegionServer管理了一系列HRegion对象，每个HRegion对应了Table中的一个Region，HRegion中由多个HStore组成。每个HStore对应了Table中的一个Column Family的存储，因此最好将具备共同IO特性的column放在同一个Column Family中，这样最高效。HStore存储是HBase存储的核心，由两部分组成，一部分是MemStore，一部分是StoreFiles。
> * HClient：HBase Client使用HBase的RPC机制与HMaster和HRegionServer进行通信，对于管理类操作，Client与HMaster进行RPC；对于数据读写类操作，Client与HRegionServer进行RPC。

​		Table逻辑上以Region的形式保存在RegionServer中。当Table随着记录数不断增加而变大后，会逐渐分裂成多份splits，成为regions，一个region由[startkey, endkey]表示，不同的region会被Master分配给相应RegionServer进行管理。

​		Zookeeper Quorum中除了存储了-ROOT-表的地址和HMaster的地址，HRegionServer也会把自己以Ephemeral方式注册到Zookeeper中，使得HMaster可以随时感知到各个HRegionServer的健康状态。此外，Zookeeper也避免了HMaster的单点问题，当HMaster挂了之后能协调产生新的HMaster。

### 5、Spark

​		Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。

* [Mesos](https://mesos.apache.org/)：Spark可以运行在Mesos里面（Mesos 类似于yarn的一个资源调度框架）
* [standalone](http://spark.apache.org/docs/latest/spark-standalone.html)：Spark自己可以给自己分配资源（master，worker）
* [YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)：Spark可以运行在yarn上面（ResourceManager，NodeManager）
*  [Kubernetes](https://kubernetes.io/)：Spark接收 [Kubernetes](https://kubernetes.io/)的资源调度

**Spark的组成：**

* **SparkCore**：将分布式数据抽象为弹性分布式数据集（RDD），实现了应用任务调度、RPC、序列化和压缩，并为运行在其上的上层组件提供API。

* **SparkSQL**：Spark Sql 是Spark来操作结构化数据的程序包，可以让我使用SQL语句的方式来查询数据，Spark支持 多种数据源，包含Hive表，parquest以及JSON等内容。

* **SparkStreaming**： 是Spark提供的实时数据进行流式计算的组件。

* **MLlib**：提供常用机器学习算法的实现库。

* **GraphX**：提供一个分布式图计算框架，能高效进行图计算。

* **BlinkDB**：用于在海量数据上进行交互式SQL的近似查询引擎。

* **Tachyon**：以内存为中心高容错的的分布式文件系统。

**其它概念：**

**Spark两种运行模式**：

* Client：在Client模式下，Driver进程会在当前客户端启动，客户端进程一直存在直到应用程序运行结束。

  ![](E:\GitStorage\bigdata\picture\2018051816051372.png)

         1.启动master和worker，worker负责整个集群的资源管理，worker负责监控自己的cpu,内存信息并定时向master汇报。
         2.在client中启动Driver进程，并向master注册。
         3.master通过rpc与worker进行通信，通知worker启动一个或多个executor进程。
         4.executor进程向Driver注册，告知Driver自身的信息，包括所在节点的host等。
         5.Driver对job进行划分stage，并对stage进行更进一步的划分，将一条pipeline中的所有操作封装成一个task，并发送到向自己注册的executor进程中的task线程中执行。
         6.应用程序执行完成，Driver进程退出。

* Cluster：在cluster模式下，Driver进程将会在集群中的一个worker中启动，而且客户端进程在完成自己提交任务的职责后，就可以退出，而不用等到应用程序执行完毕。

  ![](E:\GitStorage\bigdata\picture\20180518160657243.png)

        1.在集群的节点中，启动master , worker进程，worker进程启动成功后，会向Master进行注册。
        2.客户端提交任务后，ActorSelection（master的actor引用）,然后通过ActorSelection给Master发送注册Driver请求（RequestSubmitDriver）。
        3.客户端提交任务后，master通知worker节点启动driver进程。(worker的选择是随意的，只要worker有足够的资源即可)driver进程启动成功后，将向Master返回注册成功信息。
        4.master通知worker启动executor进程。
        5.启动成功后的executor进程向driver进行注册。
        6.Driver对job进行划分stage，并对stage进行更进一步的划分，将一条pipeline中的所有操作封装成一个task，并发送到向自己注册的executor进程中的task线程中执行。
        7.所有task执行完毕后，程序结束。
        总结：
      	Mater负责整个集群的资源的管理和创建worker；
       	worker负责当前结点的资源的管理，并会将当前的cpu，内存等信息定时告知master,并且负责创建Executor进程，也就是最小额资源分配单位；
       	Driver负责整个应用任务的job的划分和stage的切割以及task的切割和优化，并负责把task分发到worker对应的节点的executor进程中的task线程中执行, 并获取task的执行结果。
       	Driver通过SparkContext对象与spark集群获取联系，得到master主机host，就可以通过rpc向master注册自己。
  **History Server**：Spark应用程序在运行完应用程序之后，将应用程序的运行信息写入指定目录，而Spark history server可以将这些运行信息装载并以web的方式供用户浏览展示。 

**Spark Submit**：Spark向yarn提交jar包。

### 6、Spark on yarn

​	[Spark On YARN参考](https://www.cnblogs.com/langfanyun/p/8040136.html)

1. Yarn是Hadoop推出整个分布式（大数据）集群的资源管理器，负责资源的管理和分配，基于Yarn，我们可以在同一个大数据集群上同时运行多个计算框架。例如：Spark、MapReduce、Storm等

​    2. Yarn基本工作流程：

![](E:\GitStorage\bigdata\picture\20171214211655857.png)

    注意：Container要向NodeManager汇报资源信息，Container要向App Mstr汇报计算信息。
    2.1 spark-submit在提交任务的时候请求到ResourceManager提交Application，
    2.2 ResourceManager接受应用并根据集群资源状况决定在某个具体Node上来启动当前提交的应用程序的任务调度器 Driver（ApplicationMaster），
    2.3 决定后，ResourceManager会命令某个具体的Node上的资源管理器NodeManager来启动一个新的JVM进程运行程序的Driver（ApplicationMaster）部分，
    2.4 当Driver（ApplicationMaster）启动的时候，会首先向ResourceManager注册说明自己负责当前程序的运行，会下载当前Application相关的Jar包等各种资源，并基于此向ResourceMananger申请资源。
    2.5 ResourceManager接受到Driver（ApplicationMaster）的资源分配的请求之后，会最大化地满足资源分配的请求，并把资源的元数据信息发送给Driver（ApplicationMaster），
    2.6 Driver（ApplicationMaster）收到资源的元数据信息后会根据元数据发指令给具体机器上的NodeManager，让 NodeManager来启动具体的Container，
    2.7 Container在启动后必须向Driver（ApplicationMaster）注册，当Driver（ApplicationMaster）获得了用于计算的Containers后，开始进行任务的调度和计算，直到作业完成。
            
    需要补充说明的是：
    （1） 如果 ResourceManager 第一次没有能够完全完成 ApplicationMaster 分配的资源的请求，后续 ResourceManager 发现集群中有新的可用资源时，会主动向 ApplicationMaster 发送新的可用资源的元数据信息以提供更多的资源用于当前程序的运行。
    （2）如果是 hadoop 的 MapReduce 计算的话 Container 不可以复用，如果是 Spark On Yarn 的话，Container 可以复用。
    （3）Container 具体的销毁是由 ApplicationMaster 来决定的。 
    （4）ApplicationMaster 发指令给 NodeManager 让 NM 销毁 Container。
3. Spark 运行在 YARN 上时，不需要启动 Spark 集群，只需要启动 YARN即可！！！

   **YARN的 ResourceManager 相当于 Spark Standalone 模式下的 Master，NodeManager相当于Worker。**

   **Spark on YARN 有两种运行模式：**

   * Cluster -- Driver 运行在 YARN集群下的某台机器上的JVM进程中。在实际生产环境下一般都采用Cluster，我们会通过HistoryServer来获取最终全部的运行信息。

   * Client -- Driver 运行在当前提交程序的机器上。

   Standalone 模式下启动Spark集群(Master 和 Worker)，其实启动的是资源管理器(standalone、yarn)。真正作业的计算和资源管理器没有关系。

   **Spark on Yarn模式下Driver和ApplicationMaster的关系：**

   Cluster： Driver位于ApplicationMaster进程中，我们需要通过Hadoop默认指定的8088端口来通过Web控制台查看当前Spark程序运行的信息。

   Client： Driver为提交代码的机器上，此时ApplicationMaster依旧位于集群中且只负责资源的申请和launchExecutor，此时启动后的Executor并不会向ApplicationMaster进程注册，而是向Driver注册。