
* 项目架构： 前端（React），ComputeNode（executing the optimized query plan），MetaServer（大管家，定期会给前端和ComputerNode去发心跳）
* 执行模式： 一共两种，分别为批处理模式和流处理模式

* 细节补充：
    *  [HTTP keep-alive、TCP Keep-Alive、心跳检测](https://blog.csdn.net/sslulu520/article/details/117980443)
    *  [TCP-based Postgres wire protocol.](https://developer.aliyun.com/article/751984)
    *  [PostgreSQL部分源码解读](https://www.zhihu.com/column/c_1477948652627341312)
    
