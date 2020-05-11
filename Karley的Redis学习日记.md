# 数据类型

Redis支持5中数据类型：

- string(字符串)
- hash(哈希)
- list(列表)
- set(集合)
- zset(sorted set:有序集合)



# 配置文件解读

是否以守护进程运行的：daemonize	yes

绑定主机地址：	bind  127.0.0.1

指定时间内，有多少次更新操作，就将数据同步到数据文件，可多个条件配合：save<seconds><changes>

例如：300秒内有10个操作，就将数据持久化：save	300	10

指定存储至本地数据库时是否压缩数据，Redis才有LZF压缩：rdbcompression yes

指定本地数据库文件名，默认值dump.rdb：dbfilename	dump.rdb

指定本地数据库存放目录：dir	./

redis缺点：消耗内存

解决办法：采用LRU算法动态将不用的数据删除

最广泛的策略：查询所有的key中最不常使用的数据进行删除：allkeys-lru

### 配置文件路径

/etc/redis.conf

### rdb文件在

/var/lib/redis

### 日志文件在

/var/log/redis/redis.log

# 启动

## window

F:/Redis

#redis-cli.exe

#redis-server.exe redis.windows.conf

## linux

service redis start

redis-cli

# 关闭

1. 非正常关闭，极易造成数据丢失

   查询PID：ps	-ef | grep -i redis

   ​					kill	-9 PID

2. 正常关闭，数据保存

   关闭redis服务，通过客户端进行shutdown：./bin/redis-cli	shutdown

   

# 常用用法

获取指令用法：help	指令

查询所有键：keys	*

keys通配符，获取所有与pattern匹配的key，返回与该匹配

​	通配符：

​			*：代表所有

​			？：代表一个字符

- 删除key：del	key	(删除成功返回1，失败返回0)
- 序列化给定key，并且返回被序列化的值：dump	key
- 判断key是否存在：exists 	a（存在返回1，不存在返回0）
- 给key设置过期时间(以秒计)：expire	key	seconds
- 查看key剩余生存时间：ttl	key	(返回-1为永久，默认情况下所有key永远存放；返回-2代表无效)
- 当前数据库中随机返回一个key：randomkey
- 选择一个库：select	库名
- 修改key名称：rename	key	newkey
- 将当前数据库的key移动到给定的数据库当中：move	key	db



# string类型数据基本操作

- 修改多个数据：mset	key1	value1	key2	value2
- 获取多个数据：mget	key1	key2
- 获取数据字符串个数(字符串长度)：strlen	key
- 追加信息到原始信息后部(如果存在就追加，不存在就新建)：append	key	value
- 设置数值数据增加指定范围的值：
  - incr	key
  - incrby	key	increment
  - incrbyfloat	key	increment(操作小数)
- 设置树脂数据减少指定范围的值：decr,decrby
- 设置数据指定生命周期：
  - setax	key	seconds	value
  - psetex	key	milliseconds	value

## 注意事项

- 数据操作不成功的反馈与数据正常操作之间的差异
  1. 表示运行结果是否成功
     - (integer)0	->	失败
     - (integer)1	->	成功
  2. 表示运行结果值
     - (integer)3	->	3	3个
     - (integer)1	->	1	1个

- 数据为获取到（null)
- 数据最大存储量：512mb
- 数值最大计算范围（java的long最大值）：9223372036854775807

## 应用场景

user:id：00789:fans	123456789

多条数据以json形式：user：id：00789	{id:00789,name:春晚,fans:123456789}

点击关注+1：incr	user:id：00789:fans

![image-20200203115930050](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203115930050.png)



# hash

![image-20200203120208703](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203120208703.png)

需要的存储结构：一个存储空间保存多个键值对数据，在一个redis存储空间里有一个hash存储空间

<img src="C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203120401605.png" alt="image-20200203120401605" style="zoom:80%;" />

## 基本操作

![image-20200203120642096](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203120642096.png)

![image-20200203120923703](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203120923703.png)

## 扩展操作

![image-20200203121154542](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203121154542.png)

![image-20200203121437815](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203121437815.png)

### hash实现购物车

![image-20200203121640252](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203121640252.png)

![image-20200203121804059](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203121804059.png)

![image-20200203142029637](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203142029637.png)

目前只是将数据存储到redis，并没有加速，还是要二次查询数据库

如果有就不动，没有就添加：![image-20200203143606143](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203143606143.png)

### 实现抢购

![image-20200203143759486](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203143759486.png)



# list

- 数据存储需求：存储多个数据，并对数据进入存储空间的顺序进行区分
- 需要的存储结构：一个存储空间保存多个数据，且通过数据可以体现进入顺序
- list类型：保存多个数据，底层使用双向链表存储结构实现

## 基本操作

![image-20200203145154875](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203145154875.png)

lpush左推进，rpush右推进

![image-20200203151449322](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203151449322.png)

## 朋友圈点赞

pyq点赞，要求按照点赞顺序显示点赞好友信息

取消点赞，移除对应好友信息

![image-20200203151823316](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203151823316.png)

## 注意事项

- list保存数据都是string类型
- list具有索引概念，操作数据同场以队列操作，或者以栈形式
- 获取全部数据操作结束索引设置为-1
- list可以对数据分页，通常第一页信息来自于list，第二页更多信息通过数据库形式加载



# set

链表存储效率低

需要的存储结构：能够保存大量数据，高效的内部存储机制，便于查询

set类型与hash存储结构完全相同，仅存储键，不存储值，并且值不允许重复

![image-20200203154002197](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203154002197.png)

## 基本操作

![image-20200203154032883](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203154032883.png)

![image-20200203154202861](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203154202861.png)

## 扩展操作

![image-20200203154932592](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203154932592.png)

![image-20200203160737933](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203160737933.png)

## 注意事项

- set类型不允许数据重复，如果添加的数据在set中已经存在，将只保留1份。
- set虽然与hash的存储结构相同，但是无法启用hash中存储值的空间。



# sorted_set

- 新的存储需求：数据排序有利于数据的有效演示，需要提供一种可以根据自身特征进行排序的方式。
- 需要的存储结构：新的存储模型，可以保存可排序的数据。
- sorted_set类型：在set的存储结构基础上添加可排序字段

![image-20200203201839762](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203201839762.png)

score是排序作用

## 基本操作

![image-20200203202429945](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203202429945.png)

![image-20200203203510123](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203203510123.png)

![image-20200203203736980](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203203736980.png)

zinterstore不仅可以求交集，还可以求和，求最大值最小值等等，help查看

## 扩展操作

排行榜，排名，投票数据

![image-20200203204654496](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200203204654496.png)

## 注意事项

- score保存的数据存储空间是64位
- score保存的数据可以是一个双精度double值，基于双精度浮点数的特征，可能会丢失精度
- sorted_set底层存储还是基于set结构的，因此数据不重复，如果重复添加相同的数据，score值将被反复覆盖，保留最后一次修改的结果

## 应用场景

vip

输入time，获取秒与毫秒单位



# key的基本操作

- 删除指定key：del	key

- 获取key是否存在：exists	key

- 获取key的类型：type	key

## 时效性控制

- 为key设置有效期：expire	key	seconds	/	pexpire	key	milliseconds

- 获取key的有效时间：ttl	key	/	pttl	key

- 切换key从时效性转换成永久性：persist	key

## 查询模式

- 查询所有：keys	*

- 查询所有以it开头：keys	it*

- 查询所有以heima结尾：keys	*heima

- 查询所有前面两个字符任意，后面以heima结尾：keys	??heima

- 查询所有以user:开头，最后一个字符任意：keys	user:?

- 查询所有以u开头，以er:1结尾，中间包含一个字母，s或者t：keys	u[st]er:1

## 其他操作

- 为key改名：rename	key	newkey

- 如果不存在就改名：renamenx	key	newkey	
- 对所有的key排序：sort	key



# 数据库通用操作

切换数据库：select	index

数据移动：move	key	db

数据清除：

- dbsize
- flushdb
- flushall



# Jedis

## HelloWorld

基于maven：

```java
<dependency>
    <groupId>redis.clients</groupId
    <artifactId>jedis</artifactId
    <version>2.9.0</version>
</dependency>
```

编写redis测试类：

```java
public class JedisTest{
    @Test
    public void testJedis(){
        //1.连接redis
        Jedis jedis=new jedis("127.0.0.1",6379);
        //2.操作redis
        jedis.set("name",'karleyliu');
        String name=jedis.get("name");
        System.out.println(name);
        //3.关闭连接
        jedis.close();
    }
}
```

## 常规操作

### List

```java
public class JedisTest{
    @Test
    public void testList(){
        Jedis jedis=new jedis("127.0.0.1",6379);
        jedis.lpush("list1","a","b","c");
        jedis.rpush("list1","x");
        List<String> list1=jedis.lrange("list1",0,-1);
        for(String s : list1){
            System.out.println(s);
        }
        System.out.println(jedis.llen("list1"));
        jedis.close();
    }
}
```

### Hash

```java
public class JedisTest{
    @Test
    public void testHash(){
        Jedis jedis=new jedis("127.0.0.1",6379);
        jedis.hset("hash1","a1","a1");
        jedis.hset("hash1","a2","a2");
        jedis.hset("hash1","a3","a3");
        Map<String,string> hash1=jedis.hgetAll("hash1");
        System.out.println(hash1);
        System.out.println(jedis.hlen("hash1"));
        jedis.close();
    }
}
```

## 业务代码搭建

```java
public class Service {
    private String id;
    private int num;
    public Service(String id,int num){
        this.id=id;
        this.num=num;
    }
    //控制单元
    public void service(){
        //主要业务入口，控制business调用
        Jedis jedis=new Jedis("127.0.0.1",6379);
        String value=jedis.get("compid:"+id);
        //判断该值是否存在
        try {
            if (value == null) {
                //不存在，创建该值
                jedis.setex("compid:" + id, 5, Long.MAX_VALUE-num + "");
            } else {
                //存在，自增，调用业务
                Long val=jedis.incr("compid:"+id);
                business(id,num-(Long.MAX_VALUE-val));
            }
        }catch(JedisDataException e){
                System.out.println("使用已经到达次数上限，请升级会员级别");
                return;
            }finally{
                jedis.close();
            }
        }
    //业务操作
    public void business(String id,Long val){
        System.out.println("用户："+id+"业务操作执行第"+val+"次");
    }
}
class MyThread extends Thread{
    Service sc;
    public MyThread(String id,int num){
        sc=new Service(id,num);
    }
    public void run(){
        while(true){
            sc.service();
            try {
                Thread.sleep(300L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class Main{
    public static void main(String[] args){
        MyThread mt1=new MyThread("初级用户",10);
        MyThread mt2=new MyThread("高级用户",30);
        mt1.start();
        mt2.start();
    }
}
```

## 工具类

```java
public class JedisUtils {
    private static JedisPool jp=null;
    private static String host=null;
    private static int port;
    private static int maxTotal;
    private static int maxIdle;
    static{
        ResourceBundle rb=ResourceBundle.getBundle("application");
        host=rb.getString("spring.redis.host");
        port=Integer.parseInt(rb.getString("spring.redis.port"));
        maxTotal=Integer.parseInt(rb.getString("spring.redis.jedis.pool.max-active"));
        maxIdle=Integer.parseInt(rb.getString("spring.redis.jedis.pool.max-idle"));
        JedisPoolConfig jpc=new JedisPoolConfig();
        jpc.setMaxTotal(maxTotal);
        jpc.setMaxIdle(maxIdle);
        jp=new JedisPool(jpc,host,port);
    }
    public static Jedis getJedis(){

        return jp.getResource();
    }

    public static void main(String[] args) {
        JedisUtils.getJedis();
    }
}
```

# 持久化

- 什么是持久化：利用永久性存储介质将数据进行保存，在特定的时间将保存的数据进行恢复的工作机制称为持久化。

- 为什么要持久化：防止数据的意外丢失，确保数据安全性。

- 持久化过程保存什么：
  - RDB：将当前数据状态进行保存，快照形式，存储数据结果，存储格式简单，关注点在数据，大概以二进制形式保存。
  - AOF：将数据的操作过程进行保存，日志形式，存储操作过程，存储格式复杂，关注点在数据的操作过程。

## RDB

### 启动方式

命令执行：

- 谁：redis操作者（用户）
- 什么时间：即时（随时进行）
- 干什么事情：保存数据

命令：save

作用：手动执行一次保存操作

redis目录下有dump.rdb，此文件为持久化文件

### save指令相关配置

- dbfilename dump.rdb
  - 说明：设置本地数据库文件名，默认值为dump.rdb
  - 经验：通常设置为dump-端口号.rdb
- dir
  - 说明：设置存储.rdb文件的路径
  - 经验：通常设置成存储空间较大的目录中，目录名称data
- rdbcompression yes
  - 说明：设置存储至本地数据库时是否压缩数据，默认为yes，采用LZF压缩
  - 经验：通常默认为开启状态，如果设置为no，可以节省CPU运行时间，但会使存储文件变大（巨大）
- rdbchecksum	yes
  - 说明：设置是否进行RDB文件格式校验，该校验过程在写文件和读文件过程均进行
  - 经验：通常默认为开启状态，如果设置为no，可以节约读写性过程约10%时间消耗，但是存储一定的数据损坏风险。
- stop-writes-on-bgsave-error	yes
  - 说明：后台存储过程中如果出现错误现象，是否停止保存操作
  - 经验：通常默认为开启状态

### save指令工作原理

当发生多个客户端发出指令时，到达服务端是由单线程任务执行序列完成

所以save指令的执行会阻塞当前redis服务器，直到当前RDB过程完成为止，有可能会造成长时间阻塞，线上环境不建议使用。

### bgsave指令

数据量过大，单线程执行方式造成效率过低如何处理？

手动启动后台保存操作，但不是立即执行：bgsave

### bgsave指令工作原理

![image-20200210173316955](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210173316955.png)

### save配置

save配置就是bgsave指令

- 配置：save	second	changes
- 作用：满足限定时间范围内key的变化数量达到指定数量即进行持久化
- 参数
  - second:监控时间范围
  - changes:监控key的变化量
- 位置：在conf文件中进行配置

注意：

- save配置根据实际业务情况进行设置，频率过高过低都会出现性能问题
- save配置中对于second与changes设置通常具有互补对应关系，尽量不要设置成包含性关系
- save设置启动后执行bgsave操作

### RDB三种启动方式对比

![image-20200210180206323](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210180206323.png)

RDB特殊启动形式

- 全量复制：主从复制
- 服务器运行过程中重启：debug	reload
- 关闭服务器时指定保存数据：shutdown	save

### 优点

- RDB是一个紧凑压缩的二进制文件，存储效率较高
- RDB内部存储是redis在某个时间点的数据快照，非常适合用于数据备份，全量复制等场景
- RDB恢复数据的速度要比AOF快很多
- 应用：服务器中每X小时执行bgsave备份，并将RDB文件拷贝到远程机器中，用于灾难恢复

### 缺点

- RDB方式无论执行指令还是利用配置，无法做到实时持久化，具有较大的可能性丢失数据
- bgsave指令每次运行要实行fork操作创建子进程，要牺牲掉一些性能
- redis的众多版本未进行RDB文件格式的版本统一，可能出现服务版本之间数据格式无法兼容现象
- 存储数据量较大，效率较低，基于快照思想，每次读写都是全部数据，当数据量巨大，效率非常低
- 大数据量下的IO性能较低

### 解决思路

- 不写全数据，仅记录部分数据
- 改记录数据为记录操作过程
- 对所有操作均进行记录，排除丢掉数据的风险

## AOF

- 以独立日志的方式记录每次写命令，重启时再重新执行AOF文件中命令达到恢复数据的目的。与RDB相比可以简单描述为**改记录数据为记录数据产生的过程**
- AOF的主要作用是解决数据持久化的实时性，目前已经是redis持久化的主流方式

### aof写数据过程

![image-20200210205404509](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210205404509.png)

### aof写数据三种策略

- always(每次)
  - 每次写入操作均同步到aof文件中，数据零误差，**性能较低**，不建议使用
- everysec(每秒)
  - 每秒将缓冲区中的指令同步到aof文件中，数据**准确性较高**，**性能较高**
  - 在系统突然宕机的情况下丢失秒内的数据
- no(系统控制)
  - 由操作系统控制每次同步到aof文件周期，整体**过程不可控**

### aof功能开启

- 配置：appendonly	yes|no
- 作用：是否开启aof持久化功能，默认为不开启状态
- 配置：appendfsync	always|everysec|no
- 作用：aof写数据策略

### aof重写

- 降低磁盘占用量，提高磁盘利用率
- 提高持久化效率，降低持久化写时间，提高IO性能
- 降低数据恢复用时，提高数据恢复效率

#### aof重写规则

- 进程内已超时的数据不再写入文件
- 忽略无效指令，重写时使用进程内数据直接生成，这样新的aof文件只保留最终数据的写入命令
- 对同一数据的多条写入命令合并为一条命令
  - 如lpush list a,lpush list b,lpush list c可以转化为lpush list a b c
  - 为防止数据量过大造成客户端缓冲区溢出，对list，set,hash,zset等类型，每条指令最多写64个元素

#### 重写方式

- 手动重写：bgrewriteaof
- 自动重写：
  - auto-aof-rewrite-min-size	size
  - auto-aof-rewrite-percentage	percentage
  - 自动重写触发对比参数（运行指令info Persistence获取具体信息）
  - aof_current_size
  - aof_base_size
  - 自动重写触发条件：
    - aof_current_size>auto-aof-rewrite-min-size
    - aof_current_size-aof_base_size/aof_base_size>=auto-aof-rewrite-percentage

#### aof重写流程

![image-20200210214259221](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210214259221.png)

![image-20200210214410467](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210214410467.png)

## RDB与AOF区别

![image-20200210214502579](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200210214502579.png)

## 持久化应用场景

- 应用于抢购，限购类，限量发放优惠卷，激活码等业务的数据存储设计
- 应用于具有操作先后顺序的数据控制
- 应用于最新消息展示
- 应用于基于黑名单与白名单设定的服务控制
- 应用于计数器组合排序功能对应的排名
- 应用于按次结算的服务控制

# 事务

什么是事务？：redis事务就是一个命令执行的队列，将一系列预定义命令包装成一个整体（一个队列）。当执行时，一次性按照添加顺序依次执行，中间不会被打断或者干扰。

## 事务的基本操作

- 开启事务：multi
- 作用：设定事务的开启位置，此指令执行后，后续的所有指令均加入到事务中
- 执行事务：exec
- 作用：设定事务的结束为止，同时执行事务，与multi成对出现，成对使用

注意：加入事务的命令暂时进入到任务队列中，并没有立即执行，只有执行exec命令才开始执行。

![image-20200211114028341](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200211114028341.png)

- 取消事务，发生在事务定义过程中发现出了问题：discard
- 作用：终止当前事务的定义，发生在multi之后，exec之前

## 事务的工作流程

![image-20200211114736065](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200211114736065.png)

## 锁

基于特定条件的事务执行：

- 对key添加监视锁，在执行exec前如果key发生了变化，终止事务执行：watch	key1 key2
- 取消对所有key的监视：unwatch

## 分布式锁

- 使用setnx设置一个公共锁：setnx	lock-key	value
- 利用setnx命令的返回值特征，有值则返回设置失败，无值则返回设置成功
  - 对于返回设置成功的，拥有控制权，进行下一步具体业务操作
  - 对于返回设置失败的，不具有控制权，排队或等待
- 操作完毕通过del操作释放锁

## 死锁解决方案

使用expire为锁key添加时间限定，到时不释放，放弃锁

- expire	lock-key	second
- pexpire	lock-key	milliseconds

由于操作通常都是微妙或毫秒级，因此该锁定时间不宜设置过大。具体时间需要业务测试后确认。

- 例如：持有锁的操作最长执行时间127ms，最短执行时间7ms。
- 测试百万次最长执行时间对应命令的最大耗时，测试百万次网络延迟平均耗时。
- 锁时间设定推荐：最大耗时*120%+平均网络延迟*100%
- 如果业务最大耗时<<网络平均延迟，通常为2个数量级，取其中单个耗时较长即可



# 删除策略

## 过期数据

内存中数据通过TTL指令获取其状态：

- XX：具有时效性数据
- -1：永久有效数据
- -2：已经过期的数据或被删除的数据或未定义的数据

**过期的数据被删除了吗？**

没有，还放在内存当中

## 数据删除策略

1. 定时删除

   - 优点：节约内存，到时就删除，快速释放掉不必要的内存占用
   - 缺点：CPU压力很大，无论CPU此时负载量多高，均占用CPU，会影响redis服务器响应时间和指令吞吐量。
   - 总结：用处理器性能换取存储空间（拿时间换空间）

2. 惰性删除

   - 数据到达过期时间，不做处理，等下次访问该数据时再处理
   - 如果未过期，返回数据
   - 发现已过期，删除，返回不存在
   - 优点：节约CPU性能，发现必须删除的时候才删除
   - 缺点：内存压力很大，出现长期占用内存的数据
   - 总结：用存储空间换取处理器性能（拿空间换时间）

3. 定期删除

   ![image-20200211164103388](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200211164103388.png)

   - 周期性轮询redis库中的时效性数据，采用随机抽取的策略，利用过期数据占比的方式控制删除频度
   - 特点1：CPU性能占用设置有峰值，检测频度可自定义设置
   - 特点2：内存压力不是很大，长期占用内存的冷数据会被持续清理
   - 总结：周期性抽查存储空间（随机抽查，重点抽查）

数据策略删除的目标：

在内存占用和CPU占用之间寻找一种平衡，顾此失彼都会造成整体redis性能下降，甚至引发服务器宕机或内存泄露。

### 时效性数据的存储结构

![image-20200211160316278](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200211160316278.png)

## 逐出算法

当新数据进入redis时，如果内存不足怎么办？

- redis使用内存存储数据，在执行每一个命令前，会调用freeMemoryIfNeeded()检测内存是否充足。如果内存不满足新加入数据的最低存储要求，redis要临时删除一些数据为当前指令清理存储空间。清理数据的策略成为逐出算法。
- 注意：逐出数据的过程不是100%能够清理出足够的可使用内存空间，如果不成功则反复执行。当对所有数据尝试完毕后，如果不能达到内存清理的要求，将出现错误信息。

![image-20200211210038166](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200211210038166.png)

### 相关配置

- 最大可使用内存：maxmemory
- 占用物理内存的比例，默认值为0，表示不限制。生产环境中根据需求设定，通常设置在50%以上。
- 每次选取待删除数据的个数：maxmemory-samples
- 选取数据时并不会全库扫描，导致严重的性能消耗，降低读写性能。因此采用随机获取数据的方式作为待检测删除数据。
- 删除策略：maxmemory-policy
- 达到最大内存后，对被挑选出来的数据进行删除的策略
- 检测易失数据(可能会过期的数据集server.db[i].expires)
  1. volatile-lru:挑选最近最少使用的数据淘汰
  2. volatile-lfu:挑选最近使用次数最少的数据淘汰
  3. volatile-ttl:挑选将要过期的数据淘汰
  4. volatile-random:任意选择数据淘汰

- 检测全库数据(所有数据集server.db[i].dict)
  1. allkeys-lru:挑选最近最少使用的数据淘汰
  2. allkeys-lfu:挑选最近使用次数最少的数据淘汰
  3. allkeys-random:任意选择数据淘汰

- 放弃数据驱逐
  1. no-enviction(驱逐)：禁止驱逐数据(redis4.0默认策略)，会引发错误OOM(Out of memory)

### 数据逐出策略配置依据

- 使用info命令输出监控信息，查询缓存hit和miss的次数，根据业务需求调优Redis配置



# 服务器配置

## 日志配置

- 设置服务器以指定日志记录级别：loglevel	debug|verbose|notice|warning
  - 注意：日志级别开发期设置为verbose即可，生产环境中配置notice，简化日志输出量，降低写日志IO的频度
- 日志记录文件名：logfile	端口号.log

## 客户端配置

- 设置同一时间最大客户端连接数，默认无限制。当客户端连接到达上限，Redis会关闭新的连接：maxclients	0
- 客户端闲置等待最大时长，达到最大值后关闭连接。如需关闭该功能，设置为0：timeout	300

## 多服务器快捷配置

- 导入并加载指定配置文件信息，用于快速创建redis公共配置较多的redis实例配置文件，便于维护：include	/path/server-端口号.conf

# 高级数据类型

## Bitmaps

### 基础操作

- 获取指定key对应偏移量上的bit值：getbit	key	offset
- 设置指定key对应偏移量上的bit值，value只能是1或0：setbit	key	offset	value

### 扩展操作

应用于状态类统计

- 对指定的key按位进行交、并、非、异或操作，并将结果保存到destKey中：bitop	op	destKey	key1	...
  - and:交
  - or:并
  - not:非
  - xor:异或
- 统计指定key中1的数量
  - bitcount	key	[start	end]

#### 业务场景

- 统计每天某部电影是否被点播
- 统计每天有多少电影被点播
- 统计每周/月/年有多少部电影被点播
- 统计年度哪部电影没有被点播

## HyperLogLog

应用于统计不重复数据的数量

### 基本操作

- 添加数据：pfadd	key	element	[element...]
- 统计数据：pfcount	key	[key...]
- 合并数据：pfmerge	destkey	sourcekey	[sourcekey...]

### 注意

- 用于基数统计，不是集合，不保存数据，只记录数量而不是具体数据
- 核心是技术估算算法，最终数值存在一定误差
- 误差范围：基数估计的结果是一个带有0.81%标准错误的近似值
- 耗空间极小，每个hyperloglog key占用12k的内存用于标记基数
- pfadd命令不是一次性分配12k内存使用，会随着基数的增加内存逐渐增大
- pfmerge命令合并后占用的存储空间为12k，无论合并之前数据量多少

## GEO

记录坐标差

### 基本操作

- 添加坐标点：geoadd	key	longitude	latitude	member	[longtitude	latitude	member]
- 获取坐标点：geopos	key	member	[member...]
- 计算坐标点距离：geodist	key	member1	member2	[unit]
- 根据坐标求范围内的数据：georadius	key	longtitude	latitude	radius	m|km|ft|mi	[withcoord]	[withdist]	[withhash]	[count	count]
- 根据点求范围内数据：georadiusbymember	key	member	radius	m|km|ft|mi	[withcoord]	[withdist]	[withhash]	[count	count]
- 获取指定点对应的坐标hash值：geohash	key	member	[member...]

# 主从复制

![image-20200212180540428](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212180540428.png)

![image-20200212180756541](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212180756541.png)

## 作用

![image-20200212181517793](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212181517793.png)

## 工作流程

![image-20200212200649506](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212200649506.png)

### 阶段一：建立连接阶段

建立slave到master的连接，使master能够识别slave，并保存slave端口号

#### 建立连接阶段工作流程

![image-20200212201703353](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212201703353.png)

#### 主从连接（slave连接master）

- 方式一：客户端发送命令
  - slaveof	<masterip>	<masterport>
- 方式二：启动服务器参数
  - redis-server	-slaveof	<masterip>	<masterport>
- 方式三：服务器配置
  - slaveof	<masterip>	<masterport>
- slave系统信息
  - master_link_down_since_seconds
  - masterhost
  - masterport
- master系统信息
  - slave_listening_port(多个)

#### 主从断开链接

- 客户端发送命令
  - slaveof	no	one

#### 授权访问

- master配置文件设置密码
  - requirepass	<password>
- master客户端发送命令设置密码
  - config	set	requirepass	<password>
  - config	get	requirepass
- slave客户端发送命令设置密码
  - auth	<password>
- slave配置文件设置密码
  - masterauth	<password>
- 启动客户端设置密码
  - redis-cli	-a	<password>

### 阶段二：数据同步阶段工作流程

- 在slave初次连接master后，复制master中所有数据到slave
- 将slave的数据库状态更新成master当前的数据库状态

#### 数据同步阶段工作流程

![image-20200212205022756](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212205022756.png)

#### 数据同步阶段master说明

1. 如果master数据量巨大，数据同步阶段应避开流量高峰期，避免造成master阻塞，影响业务正常进行。
2. 复制缓冲区大小设定不合理，会导致数据溢出。如进行全量复制周期太长，进行部分复制时发现数据已经存在丢失情况，必须进行第二次全量复制，致使slave陷入死循环状态。
   - 复制缓冲区大小：repl-backlog-size	1mb
3. master单机内存占用主机内存的比例不应过大，建议使用50%-70%的内存，留下30%-50%的内存用于执行bgsave命令和创建复制缓冲区。

#### 数据同步阶段slave说明

1. 为比喵slave进行全量复制、部分复制时服务器响应阻塞或数据不同步，建议关闭此期间的对外服务
   - slave-server-stale-data	yes|no
2. 数据同步阶段，master发送给slave信息可以理解master是slave的一个客户端，主动向slave发送命令
3. 多个slave同时对master请求数据同步，master发送的RDB文件增多，会对宽带造成巨大冲击，如果master宽带不足，因此数据同步需要根据业务需求，适量错峰
4. slave过多时，建议调整拓扑结构，由一主多从结构变为树状结构，中间的节点既是master，也是slave。注意使用树状结构时，由于层级深度，导致深度越高的slave与最顶层的master间数据同步延迟较大，数据一致性变差，应谨慎选择

### 阶段三：命令传播阶段

- 当master数据库状态被修改后，导致主从服务器数据库状态不一致，此时需要让主从数据同步到一致的状态，同步的动作称为命令传播。
- master将接收到的数据变更命令发送给slave，slave接受命令后执行命令

#### 命令传播阶段的部分复制

- 命令传播阶段出现了断网现象
  - 网络闪断闪连	忽略
  - 短时间网络中断	部分复制
  - 长时间网络终端	全量复制
- 部分复制的三个核心要素
  - 服务器的运行id(run id)
  - 主服务器的复制积压缓冲区
  - 主从服务器的复制偏移量

##### 服务器运行ID(runid)

![image-20200212212047381](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212212047381.png)

## 复制缓冲区

![image-20200212212523121](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212212523121.png)

### 复制缓冲区内部工作原理

![image-20200212213026345](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212213026345.png)

### 总结

![image-20200212213320981](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212213320981.png)

## 数据同步+命令传播阶段工作流程

![image-20200212215213083](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212215213083.png)

## 心跳机制

![image-20200212215429301](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200212215429301.png)

### 注意事项

![image-20200213165906275](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213165906275.png)

## 频繁的全量复制(1)

伴随着系统的运行，master的数据量会越来越大，一旦master重启，runid将发生变化，会导致全部slave的全量复制操作。

内部优化调整方法：

1. master内部创建master_replid变量，使用runid相同的策略生成，长度41位(末尾加一个0)，并发送给所有slave
2. 在master关闭时执行命令shutdown	save,进行RDB持久化，将runid与offset保存到RDB文件中
   - repl-id	repl-offset
   - 通过redis-check-rdb	[RDBfile]	命令可以查看该信息
3. master重启后加载RDB文件，恢复数据
   - 重启后，将RDB文件中保存的repl-id与repl-offset加载到内存中
   - master_repl_id-repl	master_repl_offset=repl-offset
   - 通过info命令可以查看该信息
4. 作用：本机保存上次runid，重启后恢复该值，使所有slave认为还是之前的master

## 频繁的全量复制(2)

![image-20200213171837225](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213171837225.png)

- repl-backlog-size
- 建议设置如下：
  1. 测算从master到slave的重连平均时长second
  2. 获取master平均每秒产生写命令数据总量write_size_per_second
  3. 最优复制缓冲区空间=2 * second * write_size_per_second

## 频繁的网络中断(1)

- 问题现象
  - master的CPU占用过高或slave频繁断开连接
- 问题原因
  - slave每1秒发送replconf	ack命令到master
  - 当slave接到了慢查询时（keys	*,hgetall等），会大量占用CPU性能
  - master每1秒调用复制定时函数replicationCron()，比对slave发现长时间没有进行响应
- 最终结果
  - master各种资源(输出缓冲区，宽带，连接等)被严重占用
- 解决方案
  - 通过设置合理的超时时间，确认是否释放slave
  - repl-timeout
  - 该参数定义了超时时间的阈值（默认60秒），超过该值，释放slave

## 频繁的网络中断（2）

- 问题现象
  - slave与master连接断开
- 问题原因
  - master发送ping指令频度较低
  - master设定超时时间较短
  - ping指令在网络中存在丢包
- 解决方案
  - 提高ping指令发送频度
- repl-ping-slave-period
- 超时时间repl-time的时间至少是ping指令频度的5到10倍，否则slave很容易判定超时

## 数据不一致

- 问题现象
  - 多个slave获取相同数据不同步
- 问题原因
  - 网络信息不同步，数据发送有延迟
- 解决方案
  - 优化主从间的网络环境，通常放置在同一个机房部署，如使用阿里云等云服务器时要注意此现象
  - 监控主从节点延迟（通过offset）判断，如slave延迟过大，暂时屏蔽程序对该slave的数据访问
  - slave-server-stale-data	yes|no
  - 开启后仅响应info，slaveof等少数命令（慎用，除非对数据一致性要求很高）



# 哨兵模式

![image-20200213174348105](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213174348105.png)

简介：哨兵是一个分布式系统，用于对主从结构中每台服务器进行监控，当出现故障时通过投票机制选择新的master并将所有slave连接到新的master

## 哨兵的作用

- 监控
  - 不断检查master和slave是否正常运行
  - master存活检测、master与slave运行情况检测
- 通知(提醒)
  - 当被监控的服务器出现问题时，向其他(哨兵间，客户端)发送通知。
- 自动故障转移
  - 断开master与slave连接，选取一个slave作为master，将其他slave连接到新的master，并告知客户端新的服务器地址

注意：

- 哨兵也是一台redis服务器，只是不提供数据服务
- 通常哨兵配置数量为单数

## 启用哨兵模式

- 配置一拖二的主从结构
- 配置三个哨兵(配置相同，端口不同)，参看sentinel.conf
- 启动哨兵
  - redis-sentinel	sentinel-端口号.conf

## 哨兵工作原理

### 主从切换

哨兵在进行主从切换过程中经历三个阶段：

- 监控
- 通知
- 故障转移

#### 监控阶段

- 同步各个节点状态信息
  - 获取各个哨兵状态(是否在线)
  - 获取master状态信息
    - master属性
      - runid
      - role:master
    - 各个slave详细信息
  - 获取所有slave状态(根据master中的slave信息)
    - slave属性
      - runid
      - role:slave
      - master_host,master_port
      - offset
      - ......

![image-20200213221122184](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213221122184.png)

#### 通知阶段

![image-20200213221344538](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213221344538.png)

#### 故障转移阶段

![image-20200213221738920](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213221738920.png)

![image-20200213222037384](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213222037384.png)

![image-20200213222451964](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213222451964.png)

# 集群

集群就是使用网络将若干台计算机联通起来，并提供统一的管理方式，使其对外呈现单机的服务效果

## 集群作用

- 分散单台服务器的访问压力，实现负载均衡
- 分别单台服务器的存储压力，实现可扩展性
- 降低单台服务器宕机带来的业务灾难

![image-20200213223356155](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200213223356155.png)

## 集群结构设计

### 数据存储设计

- 通过算法设计，计算出key应该保存的位置
- 将所有的存储空间计划切割成16384份，每台主机保存一部分
  - 每份代表的是一个存储空间，不是一个key的保存空间
- 将key按照计算出的结果放到对应的存储空间

![image-20200214122811897](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214122811897.png)

![image-20200214123022554](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214123022554.png)

### 集群内部通讯设计

- 各个数据库相互通信，保存各个库中槽的编号数据
- 一次命中，直接返回
- 一次未命中，告知具体位置

![image-20200214123332540](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214123332540.png)

## cluster集群搭建

配置文件：

- 声明一个cluster节点：cluster-enabled	yes
- 启动配置文件：cluster-config-file	nodes-6379.conf
- cluster下线时间：cluster-node-timeout	10000

### 设置与获取数据

登录redis：redis-cli	-c

# 企业级解决方案

## 缓存预热

 服务器启动后迅速宕机

### 问题排查

1. 请求数量较高
2. 主从之间数据吞吐量较大，数据同步操作频度较高

### 解决方案

前置准备工作：

1. 日常例行统计数据访问记录，统计访问频度较高的热点数据
2. 利用LRU数据删除策略，构建数据留存队列
   1. 例如：storm与kafka配合

准备工作：

1. 将统计结果中的数据分类，根据级别，redis优先加载级别较高的热点数据
2. 利用分布式多服务器同时进行数据读取，提速数据加载过程

实施：

1. 使用脚本程序固定出发数据预热过程
2. 如果条件允许，使用CDN(内容分发网络)，效果更好

### 总结：

缓存预热就是系统启动前，提前将相关的缓存数据直接加载到缓存系统。避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题！用户直接查询事先被预热的缓存数据！

## 缓存雪崩

### 数据库服务器崩溃(1)

1. 系统平稳运行过程中，忽然数据库连接量激增
2. 应用服务器无法及时处理请求
3. 大量408，500错误页面出现
4. 客户反复刷新页面获取数据
5. 数据库崩溃
6. 应用服务器崩溃
7. 重启应用服务器无效
8. redis服务器崩溃
9. redis集群崩溃
10. 重启数据库后再次被瞬间流量放倒

### 问题排查

1. 在一个较短的时间内，缓存中较多的key集中过期
2. 此周期内亲求访问过期数据，redis未命中，redis向数据库获取数据
3. 数据库同时接收到大量的请求无法及时处理
4. redis大量请求被积压，开始出现超时现象
5. 数据库流量激增，数据库崩溃
6. 重启后仍然面对缓存中无数据可用
7. redis服务器资源被严重占用，redis服务器崩溃
8. redis集群呈现崩溃，集群瓦解
9. 应用服务器无法及时得到数据响应请求，来自客户端的请求数量越来越多，应用服务器崩溃
10. 应用服务器，redis，数据库全部重启，效果不理想

### 问题分析

- 短时间范围内
- 大量key集中过期

### 解决方案(道)

1. 更多的页面静态化处理
2. 构建多级缓存架构
   1. Nignx缓存+redis缓存+ehcache缓存
3. 检测Mysql严重耗时业务进行优化
   1. 对数据库的瓶颈排查：例如超时查询、耗时较高事务等
4. 灾难预警机制
   1. 监控redis服务器性能指标
   2. CPU占用、CPU使用率
   3. 内存容量
   4. 查询平均响应时间
   5. 线程数
5. 限流、降级
   1. 短时间范围内牺牲一些客户体验，限制一部分请求访问，降低应用服务器压力，待业务低速运转后再逐步放开访问

### 解决方案(术)

1. LRU与LFU切换
2. 数据有效期策略调整
   - 根据业务数据有效期进行分类错峰，A类90分钟，B类80分钟，C类70分钟
   - 过期时间使用固定时间+随机值的形式，稀释集中到期的key数量
3. 超热数据使用永久key
4. 定期维护(自动+人工)
   1. 对即将过期的数据做访问量分析，确认是否延时，配合访问量统计，做热点数据的延时
5. 加锁
   1. 慎用

### 总结：

缓存雪崩就是瞬间过期数据量太大，导致对数据库服务器造成压力。如果能有效避免过期数据集中，可以有效解决雪崩现象的出现(约40%)，配合其他策略一起使用，并监控服务器的运行数据，根据运行记录做快速调整。

## 缓存击穿

### 数据库服务器崩溃(2)

1. 系统平稳运行过程中
2. 数据库连接量瞬间激增
3. redis服务器无大量key过期
4. redis内存平稳，无波动
5. redis服务器CPU正常
6. 数据库崩溃

### 问题排查

1. redis中某个key过期，该key访问量巨大
2. 多个数据请求从服务器直接压到redis后，均未命中
3. redis在短时间内发起了大量对数据库中同一数据的访问

### 问题分析

- 单个key高热数据
- key过期

### 解决方案(术)

1. 预先设定
   - 以电商为例，购物节期间，加大此类信息的key过期时长
   - 注意：购物节不仅仅指当天，以后后续若干天，访问峰值呈现逐渐减低的趋势
2. 现场调整
   - 监控访问量，对自然流量激增的数据延长过期时间或设置为永久性key
3. 后台刷新数据
   - 启动定时任务，高峰期来临之前，刷新数据有效期，确保不丢失
4. 二级缓存
   - 设置不同的失效时间，保障不会被同时淘汰就行
5. 加锁
   - 分布式锁，防止被击穿，但是要注意也是性能瓶颈，慎用！

### 总结：

缓存击穿就是单个高热数据过期的瞬间，数据访问量较大，未命中redis后，发起了大量对同一数据的数据库访问，导致对数据库服务器造成压力。应对策略应该在业务数据分析与预防方面进行，配合运行监控测试与即时调整策略，毕竟单个key的过期监控难度较高，配合雪崩处理策略即可。

## 缓存穿透

### 数据库服务器崩溃(3)

1. 系统平稳运行过程中
2. 应用服务器流量随时间增量较大
3. redis服务器命中率随时间逐步较低
4. redis内存平稳，内存无压力
5. redis服务器CPU占用激增
6. 数据库服务器压力激增
7. 数据库崩溃

### 问题排查

1. redis中大面积出现未命中
2. 出现非正常URL访问

### 问题分析

- 获取的数据在数据库中也不存在，数据库查询未得到对应数据
- redis获取到null数据未进行持久化，直接返回
- 下次此类数据到达重复上述过程
- 出现黑客攻击服务器

### 解决方案(术)

![image-20200214144456387](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214144456387.png)

### 总结：

缓存击穿访问了不存在的数据，跳过了合法数据的redis数据缓存阶段，每次访问数据库，导致对数据库服务器造成压力。通常此类数据的出现量是一个较低的值，而出现此类情况以毒攻毒，并及时报警。应对策略应该在临时预案防范方面多做文章。

无论黑名单还是白名单，都是对整体系统的压力，警报解除后尽快移除

## 性能指标监控

### 监控指标：

- 性能指标：Performance
- 内存指标：Memory
- 基本活动指标：Basic activity
- 持久性指标：Persistence
- 错误指标：Error

#### 性能指标(Performance)

![image-20200214145456765](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214145456765.png)

#### 内存指标(Memory)

![image-20200214145612325](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214145612325.png)

#### 基本活动指标(Basic activity)

![image-20200214145707944](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214145707944.png)

#### 持久化指标(Persistence)

![image-20200214145743593](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214145743593.png)

#### 错误指标(Error)

![image-20200214145816203](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214145816203.png)

### 性能指标监控命令

#### 监控方式

- 工具
  - Cloud	Insight	Redis
  - Prometheus
  - Redis-stat
  - Redis-faine
  - RedisLive
  - zabbix
- 命令
  - benchmark
  - redis cli
    - monitor
    - showing

#### benchmark

- redis-benchmark	[-h]	[-p]	[-c]	[-n <requests>]	[-k]
- redis-benchmark：50个连接，10000次请求对应的性能
- redis-benchmark	-c	100	-n	5000：100个连接，5000次请求对应的性能

![image-20200214150454218](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200214150454218.png)

#### monitor

打印服务器调试信息

#### slowlog

slowlog	[operator]

- get：获取慢查询日志
- len：获取慢查询日志条目数
- reset：重置慢查询日志

##### 相关配置

slowlog-log-slower-than	1000	#设置慢查询的时间下线，单位：微秒

slowlog-max-len	100	#设置慢查询命令对应的日志显示长度，单位：命令数