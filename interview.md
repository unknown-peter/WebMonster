# interview

## 代码

单例模式

```java
    public class Singleton {
        private static volatile Singleton instance;

        private Singleton() {};

        public static Singleton getInstance() {
            if (instance == null) {
                synchronized (Singleton.class) {
                    if (instance == null) {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }

    public enum Singleton {
        INSTANCE;
    }
```

阻塞队列

```java
public void put(E e) throws InterruptedException {
    checkNotNull(e);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == items.length)
            notFull.await();
        enqueue(e);
    } finally {
        lock.unlock();
    }
}

private void enqueue(E x) {
    // assert lock.getHoldCount() == 1;
    // assert items[putIndex] == null;
    final Object[] items = this.items;
    items[putIndex] = x;
    if (++putIndex == items.length)
        putIndex = 0;
    count++;
    notEmpty.signal();
}

public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == 0)
            notEmpty.await();
        return dequeue();
    } finally {
        lock.unlock();
    }
}

private E dequeue() {
    // assert lock.getHoldCount() == 1;
    // assert items[takeIndex] != null;
    final Object[] items = this.items;
    @SuppressWarnings("unchecked")
    E x = (E) items[takeIndex];
    items[takeIndex] = null;
    if (++takeIndex == items.length)
        takeIndex = 0;
    count--;
    if (itrs != null)
        itrs.elementDequeued();
    notFull.signal();
    return x;
}
```

生产者消费者

```java
    public static class Producer implement Runnable {
        private final BlockingQueue<Integer> blockingQueue;

        public Producer(BlockingQueue<Integer> blockingQueue) {
            this.blockingQueue = blockingQueue;
        }

        public void run() {
            int info = random.nextInt(100);

            try {
                blockingQueue.put(info);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
```

快速排序

```java
public static int partition(int[] array, int lo, int hi) {
    int key = array[lo];
    while (lo < hi) {
        while (array[hi] >= key && hi > lo) {
            hi--;
        }
        array[lo] = array[hi];
        while (array[lo] <= key && hi > lo) {
            lo++;
        }
        array[hi] = array[lo];
    }
    array[hi] = key;
    return hi;
}

public static void sort(int[] array, int lo, int hi) {
    if (lo >= hi) {
        return;
    }
    int index = partition(array, lo, hi);
    sort(array, lo, index - 1);
    sort(array, index + 1, hi);
}
```

计算二叉树的高度

```java
    public static int deep(Node node) {
        int h1, h2;
        if (node == null) {
            return 0;
        } else {
            h1 = deep(node.left);
            h2 = deep(node.right);
            return (h1 < h2) ? h2 + 1: h1 + 1;
        }
    }

    class Node {
        int data = 0;
        Node left = null;
        Node right = null;
    }
```

## java

HashTable、HashMap、ConcurrentHashMap

CountDownLatch

synchronized原理：  
每个对象有一个监视器锁（monitor）。当monitor被占用时就会处于锁定状态，线程执行monitorenter指令时尝试获取monitor的所有权，过程如下：  
1、如果monitor的进入数为0，则该线程进入monitor，然后将进入数设置为1，该线程即为monitor的所有者。  
2、如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1.  
3、如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权。

synchronized与Lock的区别  
1.首先synchronized是java内置关键字，在jvm层面，Lock是个java类；  
2.synchronized无法判断是否获取锁的状态，Lock可以判断是否获取到锁；  
3.synchronized会自动释放锁\(a 线程执行完同步代码会释放锁 ；b 线程执行过程中发生异常会释放锁\)，Lock需在finally中手工释放锁（unlock\(\)方法释放锁），否则容易造成线程死锁； 、  
4.用synchronized关键字的两个线程1和线程2，如果当前线程1获得锁，线程2线程等待。如果线程1阻塞，线程2则会一直等待下去，而Lock锁就不一定会等待下去，如果尝试获取不到锁，线程可以不用一直等待就结束了；  
5.synchronized的锁可重入、不可中断、非公平，而Lock锁可重入、可判断、可公平（两者皆可）  
6.Lock锁适合大量同步的代码的同步问题，synchronized锁适合代码少量的同步问题。

## Spring

bean生命周期：  
IoC容器找到bean定义并实例化bean  
对bean进行依赖注入  
如果bean实现了BeanNameAware接口，则将bean的id传给setBeanName方法  
如果bean实现了BeanFactoryAware接口，则将bean的id传给setBeanFactory方法  
如果bean实现了BeanPostProcessor接口，则调用其postProcessBeforeInitialization方法  
如果bean实现了InitializingBean接口，则调用其afterPropertySet方法  
如果有和bean关联的BeanPostProcessors对象，则这些对象的postProcessAfterInitialization方法被调用  
销毁bean实例时，如果bean实现了DisposableBean接口，则调用其destroy方法

springMVC原理：  
用户发送请求至前端控制器DispatcherServlet  
DispatcherServlet收到请求调用HandlerMapping处理器映射器  
处理器映射器找到具体的处理器\(可以根据xml配置、注解进行查找\)，生成处理器对象及处理器拦截器\(如果有则生成\)一并返回给DispatcherServlet  
DispatcherServlet调用HandlerAdapter处理器适配器  
HandlerAdapter经过适配调用具体的处理器\(Controller，也叫后端控制器\)  
Controller执行完成返回ModelAndView  
HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet  
DispatcherServlet将ModelAndView传给ViewResolver视图解析器  
ViewResolver解析后返回具体View  
DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）  
DispatcherServlet响应用户。

## jvm虚拟机

运行时数据区：方法区\(类定义、常量、静态变量\)、虚拟机栈、本地方法栈、堆、程序计数器

双亲委派模型：一个类加载器收到了类加载请求，首先请求委派给父类加载器完成，只有父加载器不能加载时，子加载器才会尝试加载  
类加载器分类：启动类加载器\(bootstrap classloader lib\)、扩展类加载器\(extension classloader lib/ext\)、应用程序类加载器\(application classloader\)

虚拟机监测工具：  
jps 虚拟机进程状况  
jstat 虚拟机统计信息监视\(新生代、老年代等各区的使用情况\)  
jmap java内存映像  
jstack java堆栈跟踪

判断对象是否可以GC

垃圾收集器

OOM出现场景，如何定位问题

线上服务CPU很高，如何找到问题

线上应用频繁Full GC，如何找到问题

## redis

redis数据类型：String、list、hash、set、zset  
redis持久化： RDB\(快照\)、AOF\(保存为AOF文件，append\)  
主从复制 slave of

## MyBatis

MyBatis中使用\#和$占位符的区别：\#将传入的数据都当成一个字符串，会对传入的数据自动加上引号；$将传入的数据直接显示生成在SQL中\(有可能导致SQL注入攻击\)

动态SQL：复杂查询指定多个查询条件 if、choose/when/otherwise、trim、where、set、foreach

## mysql

数据库特征：A\(原子性\)、C\(一致性\)、I\(隔离性\)、D\(持久性\)  
数据库隔离级别：串行化、可重复读\(幻读\)、读已提交、读未提交\(脏读\)

MyISAM与InnoDB区别 InnoDB：支持事务处理、外键、行锁 MyISAM：支持全文索引

mysql索引原理：B+树，所有数据在同一深度的叶子节点上，中间节点不存储数据，且叶子节点通过指针链接起来。如此数据都存在叶子节点，每次查询需要遍历到同一深度，性能稳定；中间节点不存储数据，整个磁盘页可以存储高度较深的树，减少磁盘IO次数；叶子节点以链表的形式存储，便于查找

主键索引、普通索引、全文索引\(MyISAM\)、唯一索引

mysql优化\(表设计合理、添加适当的索引、分表、读写分离、存储过程、mysql配置优化、mysql服务器硬件升级、清除不需要的数据及碎片整理\)

show status like 'slow\_queries' safe-mode --slow-query-log记录慢查询日志  
explain分析sql语句执行  
show status like 'Handler\_read%' 查看索引使用率

select  _from test where name in \(select name from test group by name having COUNT\(_\) &gt; 1\)  
select p1.id, p1.name from test as p1 inner join test as p2 on p1.name = p2.name where p1.id &lt;&gt; p2.id

## 分布式

分布式锁：  
redis加锁 jedis.set\(String key, String value, String nxxx, String expx, int time\) redis解锁 使用eval保证原子性

```text
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
Object result = jedis.eval(script, Collections.singletonList(lockKey), Collections.singletonList(requestId));
```

zookeeper  
有序节点：假如当前有一个父节点为/lock，我们可以在这个父节点下面创建子节点；zookeeper提供了一个可选的有序特性，例如我们可以创建子节点“/lock/node-”并且指明有序，那么zookeeper在生成子节点时会根据当前的子节点数量自动添加整数序号，也就是说如果是第一个创建的子节点，那么生成的子节点为/lock/node-0000000000，下一个节点则为/lock/node-0000000001，依次类推。  
临时节点：客户端可以建立一个临时节点，在会话结束或者会话超时后，zookeeper会自动删除该节点。  
事件监听：在读取数据时，我们可以同时对节点设置事件监听，当节点数据或结构变化时，zookeeper会通知客户端。当前zookeeper有如下四种事件：1）节点创建；2）节点删除；3）节点数据修改；4）子节点变更。

zookeeper分布式锁：  
客户端连接zookeeper，并在/lock下创建临时的且有序的子节点，第一个客户端对应的子节点为/lock/lock-0000000000，第二个为/lock/lock-0000000001，以此类推；  
客户端获取/lock下的子节点列表，判断自己创建的子节点是否为当前子节点列表中序号最小的子节点，如果是则认为获得锁，否则监听刚好在自己之前一位的子节点删除消息，获得子节点变更通知后重复此步骤直至获得锁；  
执行业务代码；  
完成业务流程后，删除对应的子节点释放锁。

## 其他

CAS单点登录：首先用户访问受保护的资源，权限没有认证，所以会把请求的URL以参数跳转到CAS认证中心，CAS认证中心发现没有SSO session，所以弹出登录页面，输入用户信息，提交到CAS认证中心进行信息的认证，如果信息正确，CAS认证中心就会创建一个SSO session——CASTGC cookie，这个CASTGC cookie包含了TGT，这个TGT作为一个用户session，它会分发一个ST返回给用户。用户拿到了ST后，访问带参数ST的资源地址，同时应用将ST发送给CAS认证中心，CAS认证中心对ST进行校验，判断ST是否是有效的，结果会返回一个包含成功信息的XML给应用。应用在建立相应的session cookie跳转到浏览器，用户在通过浏览器带cookie去应用访问受保护的资源地址，cookie验证成功便可以成功访问到信息。 第二次访问应用时，浏览器就会携带相应的session去验证用户是否登录，与一般单系统应用登录模式一样。 当我们访问其他的应用，与前面的步骤也是基本相同，首先用户访问受保护的资源，跳转回浏览器，浏览器含有先前登录的CASTGC cookie，CASTGC cookie包含了TGT并发送到CAS认证中心，CAS认证中心校验TGT是否有效，如果有效分发浏览器一个带ST参数的资源地址URL，应用活动ST后，再发送给CAS认证中心，如果认证了ST有效后，结果会返回一个包含成功信息的XML给应用。同样的步骤，应用在建立相应的session cookie跳转到浏览器，用户在通过浏览器带cookie去应用访问受保护的资源地址，验证成功便可以成功访问到信息。

  
\(1\)、TGC（ticket-granting cookie） 授权的票据证明，由 CAS Server 通过 SSL 方式发送给终端用户，存放用户身份认证凭证的Cookie，在浏览器和CAS Server间通讯时使用，并且只能基于安全通道传输（Https），是CAS Server用来明确用户身份的凭证。  
\(2\)、TGT（Ticket Grangting Ticket） TGT是CAS为用户签发的登录票据，拥有了TGT，用户就可以证明自己在CAS成功登录过。TGT封装了Cookie值以及此Cookie值对应的用户信息。用户在CAS认证成功后，CAS生成Cookie（叫TGC），写入浏览器，同时生成一个TGT对象，放入自己的缓存，TGT对象的ID就是Cookie的值。当HTTP再次请求到来时，如果传过来的有CAS生成的Cookie，则CAS以此Cookie值为key查询缓存中有无TGT ，如果有的话，则说明用户之前登录过，如果没有，则用户需要重新登录。  
\(3\)、ST（Service Ticket） ST是CAS为用户签发的访问某一service的票据。用户访问service时，service发现用户没有ST，则要求用户去CAS获取ST。用户向CAS发出获取ST的请求，如果用户的请求中包含Cookie，则CAS会以此Cookie值为key查询缓存中有无TGT，如果存在TGT，则用此TGT签发一个ST，返回给用户。用户凭借ST去访问service，service拿ST去CAS验证，验证通过后，允许用户访问资源。

