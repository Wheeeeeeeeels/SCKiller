
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
    
* 细节补充：
    *  [HTTP keep-alive、TCP Keep-Alive、心跳检测](https://blog.csdn.net/sslulu520/article/details/117980443)
    *  [TCP-based Postgres wire protocol.](https://developer.aliyun.com/article/751984)
    *  [PostgreSQL部分源码解读](https://www.zhihu.com/column/c_1477948652627341312)、
    *  [Material View]（https://blog.csdn.net/qq_37933018/article/details/120528245）
    


[参考文献]

https://github.com/singularity-data/risingwave/blob/main/docs/state-store-overview.md
