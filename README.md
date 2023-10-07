Zookeeper入门
---
zk就是分布式协调服务。 可以理解为 key-value类型的数据库

    1.配置管理
    2.分布式锁
    3.集群管理
    4.发布订阅
    5.负载均衡
    6.分布式同步
    7.其他：Watch（监听）+ Znode模型，Dubbo使用

使用
---
1.依赖

    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.9</version>
    </dependency>

2.用Curator API来操作zk
    
    Curator官网：curator.apache.org
    Curator版本要和zk的版本对应才可以，不然出问题

3.Curator API常用操作

    1.建立连接
    2.增加节点
    3.删除节点
    4.更改节点
    5.查询节点

    6.Watch事件监听
        (1).监听某个节点
        (2).监听节点的所有子节点
        (3).监听节点以及子节点

    7.分布式锁
    核心思想:当客户端要获取锁，则创建节点,使用完锁，则册除该节点。
        1.客户端获取锁时，在lock节点下创建临时顺序节点。
            临时节点: 客户端宕机与zk断开连接，那么临时节点也会全部删除，防止死锁
            顺序节点: 获取lock下面的所有子节点，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。
                     如果创建的子节点不是最小的，那么对比自己小一号的节点做删除监听事件。例如有1、2、3、4个节点，4对3做监听，3对2最监听，2对1做监听






