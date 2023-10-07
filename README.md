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
    核心思想: 当客户端要获取锁，则创建节点,使用完锁，则册除该节点。
        使用: 客户端获取锁时，在lock节点下创建临时顺序节点。 ★注意必须是临时顺序节点：临时是为了防止死锁，顺序是为了监听删除事件
            临时节点: 客户端宕机与zk断开连接，那么临时节点也会全部删除，防止死锁
            顺序节点: (1).获取lock下面的所有子节点，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。
                     (2).如果创建的子节点不是最小的，那么对比自己小一号的节点做删除监听事件。例如有1、2、3、4个节点，4对3做监听，3对2最监听，2对1做监听
                     (3).如果监听到有删除，那么再次判断自己是否是最小节点
                            如果是则获取到锁
                            如果不是则再次监听比自己小一号的节点
                    顺序监听就是为了方便锁排序

<b>Curator有5种锁方案：<br></b>
非可重入锁: 指的是当A客户端获取锁了，如果再次向访问资源，必须释放了才能再次访问<br>
可重入锁: 指的是A客户端获得是，还可以多次访问资源。实现比较麻烦，每次重新访问必须释放才能再次访问。<br>
信号量: 指的是可以提供多个信号量，获取到信号量就得到锁，用完释放。多个人可同时获取锁

    InterProcessSemaphoreMutex:分布式排它锁(非可重入锁)
    InterProcessMutex:分布式可重入排它锁
    InterProcessReadWriteLock:分布式读写锁
    InterProcessMultilock:将多个锁作为单个实体管理的容器
    InterProcessSemaphoreV2:共享信号量

InterProcessMutex lock = new InterProcessMutex(client,"/lodc");  //client是客户端连接，/lock是节点
lock.acquire(3,TimeUnit.SECONDS);  //获取锁：锁3秒自动释放
Lock.release();  //释放锁

