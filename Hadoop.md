[TOC]

----------
# MapReduce
- 关系型数据库与MapReduce的比较：
| |关系型数据库|MapReduce|
| - | - | - |
|数据大小|GB|PB|
|访问|交互式和批处理|批处理|
|更新|多次读写|一次写入多次读取|
|结构|静态模式|动态模式|
|完整性|高|低|
|横向扩展|非线性|线性|

- MapReduce的核心假设之一就是，它可以进行（高速的）流式读写操作。
- 与普通的高性能计算不同，MapReduce的核心特性是**数据本地化**。它认为网络带宽是数据中心环境最珍贵的资源，并通过显示网络拓扑结构尽力保留网络带宽。
- MapReduce在更高层次上执行任务，即程序员仅从键值对函数的教徒考虑任务的置信，这样数据流是隐含的，不需要显式控制。
- 由于采用了无共享（shared-nothing）框架，所以系统实现能够检测到部分失效。相比之下，MPI（Message Passing Interface）程序必须显式地管理自身的检查点和恢复机制。

# Hadoop
- Hadoop将MapReduce的输入数据划分成等长的小数据块，成为输入分片(input split)。Hadoop为每个分片构建一个map任务，并在存储有输入数据的节点上运行map任务以获得最佳性能（data locality optimization）。分片越小负载平衡的质量会更好。
- HDFS的一个块的大小默认为64MB。最佳分片大小应当与块大小相同，因为它是确保可以存储在单个节点上的最大输入块大小。
- map任务将其输出写入本地硬盘，而非HDFS，以减小开销。如果该节点上运行的map任务在将中间结果传送给reduce任务之前失败，则将在另一个节点上重新运行这个map任务。
-reduce任务并不具备数据本地化的优势。如有多个reduce任务，则每个map都会对其输出进行分区（partition），即每个reduce建一个分区。每个分区有许多键，但每个键对应的键值对记录都在同一个分区中。分区由用户定义的分区函数控制，通常采用默认的分区器通过哈希函数来进行。
- map和reduce之间的数据流成为shuffle。调整shuffle的参数对作业总执行时间会有非常大的影响。
-为了尽量避免map和reduce间的数据传输，可以用combiner合并mapper的输出结果。必须保证不经过多少次合并，reducer的输出结果保持一致（f(x1,x2,x3,x4)=f(f(x1,x2),f(x3,x4))），具有该属性的函数成为是“分布式的”）。合并函数是通过reducer接口来定义的。
- Streaming和Java MapReduce API之间存在设计差异。Java API控制的map函数一次只能处理一条记录。然而在Streaming中，map程序可以自己决定如何处理输入数据。
- Streaming中可以将mapper或reducer改成流水线：
```
hadoop jar hadoop-*-streaming.jar \
    -input input/all \
    -output output \
    -mapper "mapper.py | sort | combiner.py" \
    -reducer reducer.py \
    -file mapper.py \
    -file combiner.py \
    -file reducer.py
```
- Hadoop的Pipes是C++接口的代称，Pipes使用套接字作为tasktracker与C++版本的map或reduce函数的进程之间的通道，而未使用JNI。我们可以用重载模板factory设置mapper、reducer、combiner、partitioner、record reader或record writer。

# HDFS
- HDFS假设每次分析都将涉及数据集的大部分数据甚至全部，因此读取整个数据集的时间延迟比读取第一条记录的时间延迟更重要。
- 要求低时间延迟数据访问的应用，例如几十毫秒范围，不适合在HDFS上运行，而很适合HBase。HDFS为高数据吞吐量应用优化的，这可能会以高时间延迟为代价。
- 文件系统所能存储的文件总数量受限于namenode的内存容量。根据经验，每个文件、目录和数据块的存储信息大约占150字节。
- HDFS中的文件可能只有一个writer，而且写操作总是将数据添加在文件的末尾。它不支持具有多个写入者的操作，也不支持在文件任意位置进行修改。
- HDFS的块默认为64MB。HDFS上的文件划分为块大小的多个分块（chunk）。但与其他文件系统不同的是，HDFS中小于一个块大小的文件不会占据整个块的空间。
- HDFS的块远大于磁盘块的目的是最小化寻址开销，使磁盘传输数据的时间可以明显大于定位这个块开始位置所需的时间。很多情况下HDFS使用128MB的块设置。随着磁盘驱动器传输速率的提升，块大小将被设置得更大。注意该参数如果设置得过大，由于map任务通常一次处理一个块中的数据，负载将不平衡。
- 块抽象的最明显好处就是一个文件的大小可以大于网络中任意一个磁盘的容量。另一个好处是简化了存储子系统的设计，因为块的大小固定计算方便，同时消除了元数据的顾虑（让其他系统单独地管理这些元数据）。与此同时，块也非常适合用于数据备份进而提供容错能力和可用性。`fsck`指令可以显示块信息，例如`hadoop fsck / -files -blocks`列出各个文件由哪些块构成。
- HDFS集群有两类节点，并以管理者-工作者模式运行，即一个namenode和多个datanode。namenode维护着文件系统树以及整棵树内所有的文件和目录。这些信息以两个文件形式永久保存在本地磁盘上：命名空间镜像文件和编辑日志文件。namenode也记录每个文件中各个块所在的数据节点信息，但它不会永久保存块的位置信息，因为这些信息会在系统启动时由数据结点重建。
- datanode根据需要存储并检索数据块，并定期向namenode发送它们所存储的块的列表。
- Hadoop为namenode提供了两种机制：
 - 备份那些组成文件系统元数据持久状态的文件，这些写操作是实时同步的，是原子操作。一般将持久状态写入本地磁盘的同时，写入一个远程挂载的网络文件系统（NFS）
 - 运行一个辅助namenode，定期通过比恩及日志合并命名空间镜像，防止编辑日志过大，并在namenode发生故障时启用。辅助namenode保存的状态总是滞后于主节点，所以难免会丢失部分数据，这种情况下一般把存储在NFS上的namenode元数据复制到辅助namenode
- 设置伪分布式配置时，有两个属性需要注意。fs.default.name设置为hdfs://localhost/，用于设置Hadoop的默认文件系统，HDFS的守护程序将通过该属性来确定namenode的主机及端口；dfs.replication应当设为1，这样HDFS不会按默认设置将文件系统块拷贝数设为3。
- 