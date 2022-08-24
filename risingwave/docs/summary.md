
## RisingWave 云原生流数据库

* 项目架构： 前端（React），ComputeNode（executing the optimized query plan），MetaServer（大管家，定期会给前端和ComputerNode去发心跳）
* 执行模式： 一共两种，分别为批处理模式和流处理模式
    *  批处理查询
         *  简单查询流程： 查询会被分为多个plan fragments中，且在不同的查询引擎中通用  
         *  TableScan operator 用于从表中读取数据，Hive中也存在同样的命名
         *  存储引擎：Hummock，用于存储内部状态,物化视图（可以存储数据），以及表的信息，其中内部状态（internal state）用户是不可见的
    *  流处理查询
         * 讲了一下kafka作为data source以及相关物化视图的东西
* Hummock存储引擎
    * Hummock是一个云原生基于LSM-Tree的存储引擎；提供了<key,value> api,并可以将所有数据存储到S3适配的服务上
    * Hummock并不是一个通用的<key,value>存储，而是与RisingWave流引擎共同设计的，并针对其进行优化的存储引擎
    * Hummock架构情况如下所示:
    ![](https://github.com/Wheeeeeeeeels/SCKiller/blob/main/risingwave/docs/images/Snipaste_2022-08-24_00-38-36.jpg)
    * Hummock包含有一个管理服务在meta service上叫做HummockManager，作用是老大哥，而其他的小弟在woker nodes上（包括compute nodes,frontend nodes,compactor nodes）,同时有一个共享存储去存储文件
    * 每当有一批货需要放到库里，小弟们（Hummock client）会上传这些货到shared storage中（阿里云中PolarDB也是如此设计），然后通知大哥（Hummock manager）来了一批货，之后会开始一系列流程，新的货会被放入，没有用的废料就会被丢弃，Hummock manager会负责整个货（具体而言是file）的周期
    * Steaming state store 具有十分好的 workload特性：
        * 每个流执行器只会读写自己部分的数据，这些数据是连续不重复的键范围（在Risingwave里称为键空间）
        * 数据不会跨结点去共享，因此每个woker node只会读写它自己的数据，如果想要跨结点读取数据需要等待
        * 流数据是串行提交的，基于的是（ barrier-based checkpoint 算法）（这里设计可以参考Flink） 
    
* 细节补充：
    *  [HTTP keep-alive、TCP Keep-Alive、心跳检测](https://blog.csdn.net/sslulu520/article/details/117980443)
    *  [TCP-based Postgres wire protocol.](https://developer.aliyun.com/article/751984)
    *  [PostgreSQL部分源码解读](https://www.zhihu.com/column/c_1477948652627341312)、
    *  [Material View](https://blog.csdn.net/qq_37933018/article/details/120528245)
    *  Hummock提供几个简单的API:ingest_batch,get,scan,具体而言就是接触这个你就进入hello world世界了
    *  Hummock支持MVVC读写，MVCC是一种多版本并发控制，能够实现对数据的并发访问，可以理解为维持一个数据的多个版本，使得读写操作没有冲突。
    *  [MVCC多版本并发控制](https://blog.csdn.net/sundehui01/article/details/121785281?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-121785281-blog-89857679.pc_relevant_aa_2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-121785281-blog-89857679.pc_relevant_aa_2&utm_relevant_index=1)
    


[参考文献]

https://github.com/singularity-data/risingwave/blob/main/docs/state-store-overview.md
