# unknown

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

堆排序

```text
public class Sort {
    public static void main(String[] args) {
        int[] nums = {16,7,3,20,17,8};
        headSort(nums);
        for (int num : nums) {
            System.out.print(num + " ");
        }
    }

    /**
     * 堆排序
     */
    public static void headSort(int[] list) {
        //构造初始堆,从第一个非叶子节点开始调整,左右孩子节点中较大的交换到父节点中
        for (int i = (list.length) / 2 - 1; i >= 0; i--) {
            headAdjust(list, list.length, i);
        }
        //排序，将最大的节点放在堆尾，然后从根节点重新调整
        for (int i = list.length - 1; i >= 1; i--) {
            int temp = list[0];
            list[0] = list[i];
            list[i] = temp;
            headAdjust(list, i, 0);
        }
    }
    
    private static void headAdjust(int[] list, int len, int i) {
        int k = i, temp = list[i], index = 2 * k + 1;
        while (index < len) {
            if (index + 1 < len) {
                if (list[index] < list[index + 1]) {
                    index = index + 1;
                }
            }
            if (list[index] > temp) {
                list[k] = list[index];
                k = index;
                index = 2 * k + 1;
            } else {
                break;
            }
        }
        list[k] = temp;
    }
}
```

归并排序

```text
public void merge(int []a,int left,int mid,int right){ 
    int []tmp=new int[a.length];//辅助数组 
    int p1=left,p2=mid+1,k=left;//p1、p2是检测指针，k是存放指针
    while(p1<=mid && p2<=right){
        if(a[p1]<=a[p2])
            tmp[k++]=a[p1++];
        else
            tmp[k++]=a[p2++];
    }

    while(p1<=mid) tmp[k++]=a[p1++];//如果第一个序列未检测完，直接将后面所有元素加到合并的序列中
    while(p2<=right) tmp[k++]=a[p2++];//同上

    //复制回原素组
    for (int i = left; i <=right; i++) 
        a[i]=tmp[i];
}

public void mergeSort(int [] a,int start,int end){
    if(start<end){//当子序列中只有一个元素时结束递归
        int mid=(start+end)/2;//划分子序列
        mergeSort(a, start, mid);//对左侧子序列进行递归排序
        mergeSort(a, mid+1, end);//对右侧子序列进行递归排序
        merge(a, start, mid, end);//合并
    }
}
```

二叉树前中后序遍历

```text
    public void preOrderTraverse1(TreeNode root) {
        if (root != null) {
            System.out.print(root.val + "->");
            preOrderTraverse1(root.left);
            preOrderTraverse1(root.right);
        }
    }
    public void preOrderTraverse2(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode node = root;
        while (node != null || !stack.empty()) {
            if (node != null) {
                System.out.print(node.val + "->");
                stack.push(node);
                node = node.left;
            } else {
                TreeNode tem = stack.pop();
                node = tem.right;
            }
        }
    }
    
    public void inOrderTraverse(TreeNode root) {
        if (root != null) {
            inOrderTraverse(root.left);
            System.out.print(root.val + "->");
            inOrderTraverse(root.right);
        }
    }
    public void inOrderTraverse(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode node = root;
        while (node != null || !stack.isEmpty()) {
            if (node != null) {
                stack.push(node);
                node = node.left;
            } else {
                TreeNode tem = stack.pop();
                System.out.print(tem.val + "->");
                node = tem.right;
            }
        }
    }
    
    public void postOrderTraverse(TreeNode root) {
        if (root != null) {
            postOrderTraverse(root.left);
            postOrderTraverse(root.right);
            System.out.print(root.val + "->");
        }
    }
    public void postOrderTraverse(TreeNode root) {
        TreeNode cur, pre = null;

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.empty()) {
            cur = stack.peek();
            if ((cur.left == null && cur.right == null) || (pre != null && (pre == cur.left || pre == cur.right))) {
                System.out.print(cur.val + "->");
                stack.pop();
                pre = cur;
            } else {
                if (cur.right != null)
                    stack.push(cur.right);
                if (cur.left != null)
                    stack.push(cur.left);
            }
        }
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
HashMap实现了Map接口，实现了将唯一键隐射到特定值上。允许一个NULL键和多个NULL值。非线程安全。  
HashTable类似于HashMap，但是不允许NULL键和NULL值，比HashMap慢，因为它是同步的。HashTable是一个线程安全的类，它使用synchronized来锁住整张Hash表来实现线程安全，即每次锁住整张表让线程独占。  
ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术。它使用了多个锁来控制对hash表的不同部分进行的修改。ConcurrentHashMap内部使用段\(Segment\)来表示这些不同的部分，每个段其实就是一个小的Hashtable，它们有自己的锁。只要多个修改操作发生在不同的段上，它们就可以并发进行。  
在JDK1.7版本中，ConcurrentHashMap的数据结构是由一个Segment数组和多个HashEntry组成，如下图所示：

![](https://images2018.cnblogs.com/blog/981978/201803/981978-20180326222203010-1538132747.png)

Segment数组的意义就是将一个大的table分割成多个小的table来进行加锁，也就是上面的提到的锁分离技术，而每一个Segment元素存储的是HashEntry数组+链表，这个和HashMap的数据存储结构一样  
JDK1.8的实现已经摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap，虽然在JDK1.8中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本。

![](https://images2018.cnblogs.com/blog/981978/201803/981978-20180326222301021-637978199.png)

CountDownLatch、CyclicBarrier  
CyclicBarrier可重入

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



线程池

* corePoolSize：线程池的核心大小，也可以理解为最小的线程池大小。
* maximumPoolSize：最大线程池大小。
* keepAliveTime：空余线程存活时间，指的是超过corePoolSize的空余线程达到多长时间才进行销毁。
* unit：销毁时间单位。
* workQueue：存储等待执行线程的工作队列。
* threadFactory：创建线程的工厂，一般用默认即可。
* handler：拒绝策略，当工作队列、线程池全已满时如何拒绝新任务，默认抛出异常。

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

JDK动态代理和CGLIB字节码生成的区别？  
JDK动态代理只能对实现了接口的类生成代理，而不能针对类；CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP；如果目标对象实现了接口，可以强制使用CGLIB实现AOP；如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换

AOP原理：  
AOP技术利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面。所谓"切面"，简单说就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。  
使用"横切"技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如权限认证、日志、事物。AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。  
1、横切关注点 对哪些方法进行拦截，拦截后怎么处理，这些关注点称之为横切关注点 2、切面（aspect） 类是对物体特征的抽象，切面就是对横切关注点的抽象 3、连接点（joinpoint） 被拦截到的点，因为Spring只支持方法类型的连接点，所以在Spring中连接点指的就是被拦截到的方法，实际上连接点还可以是字段 4、切入点（pointcut） 我们关注的连接点就是切入点。 5、通知（advice） 所谓通知指的就是指拦截到连接点之后要执行的代码，通知分为前置、后置、异常、最终、环绕通知五类 6、目标对象 代理的目标对象 7、织入（weave） 将切面应用到目标对象并导致代理对象创建的过程 8、引入（introduction） 在不修改代码的前提下，引入可以在运行期为类动态地添加一些方法或字段

spring事务的传播属性，这些属性在TransactionDefinition中定义，具体常量的解释见下表：

| 常量名称 | 常量解释 |
| :--- | :--- |
| PROPAGATION\_REQUIRED | 支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择，也是 Spring 默认的事务的传播。 |
| PROPAGATION\_REQUIRES\_NEW | 新建事务，如果当前存在事务，把当前事务挂起。新建的事务将和被挂起的事务没有任何关系，是两个独立的事务，外层事务失败回滚之后，不能回滚内层事务执行的结果，内层事务失败抛出异常，外层事务捕获，也可以不处理回滚操作 |
| PROPAGATION\_SUPPORTS | 支持当前事务，如果当前没有事务，就以非事务方式执行。 |
| PROPAGATION\_MANDATORY | 支持当前事务，如果当前没有事务，就抛出异常。 |
| PROPAGATION\_NOT\_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 |
| PROPAGATION\_NEVER | 以非事务方式执行，如果当前存在事务，则抛出异常。 |
| PROPAGATION\_NESTED | 如果一个活动的事务存在，则运行在一个嵌套的事务中。如果没有活动事务，则按REQUIRED属性执行。它使用了一个单独的事务，这个事务拥有多个可以回滚的保存点。内部事务的回滚不会对外部事务造成影响。它只对DataSourceTransactionManager事务管理器起效。 |

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

## Spring boot

SpringBoot自动配置  
@EnableAutoConfiguration注解能够开启Spring ApplicationContext 的自动配置功能，通过扫描类路径下的组件并注册符合条件的bean。  
通过使用@Conditional，你可以根据任何条件选择性地注册bean。

## jvm虚拟机

运行时数据区：方法区\(类定义、常量、静态变量\)、虚拟机栈、本地方法栈、堆、程序计数器

![img](https://img.alicdn.com/tfs/TB1bhRnRFXXXXa2XVXXXXXXXXXX-510-401.png)

堆内存划分为： Eden、Survivor 和 Tenured/Old 空间

![img](https://img.alicdn.com/tfs/TB1EVhqRFXXXXcFXFXXXXXXXXXX-583-183.png)

双亲委派模型：一个类加载器收到了类加载请求，首先请求委派给父类加载器完成，只有父加载器不能加载时，子加载器才会尝试加载  
类加载器分类：启动类加载器\(bootstrap classloader lib\)、扩展类加载器\(extension classloader lib/ext\)、应用程序类加载器\(application classloader\)

虚拟机监测工具：  
jps 虚拟机进程状况  
jstat 虚拟机统计信息监视\(新生代、老年代等各区的使用情况\)  
jmap java内存映像  
jstack java堆栈跟踪

判断对象是否可以GC  
根搜索算法，引用计数法（循环引用，js使用）  
一个对象到GC Roots没有任何引用链相连，则该对象不可用，这时Java虚拟机可以对这些对象进行回收。  
Java虚拟机将以下对象定义为 GC Roots ：  
Java虚拟机栈中引用的对象，虚拟机栈中（栈帧） 静态属性引用的对象，static对象 常量引用的对象，final对象 本地方法栈中引用的对象，nio

垃圾收集器

![img](https://img.alicdn.com/tfs/TB1z9BsRFXXXXXzXVXXXXXXXXXX-865-704.png)

OOM出现场景，如何定位问题  
HeapSize OOM  
堆空间溢出。老年代区域剩余的内存，已经无法满足将要晋升到老年代区域的对象大小，会报此错。一般来说，绝大部分都是这种情况。大量空间占据了堆空间，而这些对象持有强引用，导致无法回收，当对象大小之和大于由xmx参数指定的堆空间大小时，溢出错误就发生了。  
发生堆内存不足的原因可能有：  
1）设置的堆内存太小，而系统运行需要的内存要超过这个设置值  
2）内存泄露。关注系统稳定运行期，full gc每次gc后的可用内存值是否一直在增大。  
3）由于设计原因导致系统需要过多的内存，如系统中过多地缓存了数据库中的数据，这属于设计问题，需要通过设计减少内存的使用。  
4）分析线程执行模型和它们持有的JVM里的短生命对象  
解决这种问题两种方法：  
1）增加参数，- XX:-UseGCOverheadLimit，关闭这个特性，同时增加heap大小，-Xmx1024m。  
2）排查并优化消耗内存资源代码。  
PermGen OOM  
永久代\(PermGen space\)是JVM实现方法区的地方，因此该异常主要设计到方法区和方法区中的常量池。永久代存放的东西有class和一些常量。perm是放永久区的。如果一个系统定义了太多的类型，那永久区可能会溢出。jdk1.8中，被称为元数据区。

线上服务CPU很高，如何找到问题  
1、top命令：Linux命令。可以查看实时的CPU使用情况。也可以查看最近一段时间的CPU使用情况。  
2、PS命令：Linux命令。强大的进程状态监控命令。可以查看进程以及进程中线程的当前CPU使用情况。属于当前状态的采样数据。  
3、jstack：Java提供的命令。可以查看某个进程的当前线程栈运行情况。根据这个命令的输出可以定位某个进程的所有线程的当前运行状态、运行代码，以及是否死锁等等。  
**4、pstack：Linux命令。可以查看某个进程的当前线程栈运行情况。**

线上应用频繁Full GC，如何找到问题  
1、查看GC情况jstat -gcutil 1 1000 \#1是进程号  
2、jmap -F -dump:format=b,file=heapDump 1 \#1是进程号  
3、jhat、VisualVM分析

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

select _from test where name in \(select name from test group by name having COUNT\(_\) &gt; 1\)  
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

## 网络

tcp、udp区别  
1.基于连接与无连接；  
2.对系统资源的要求（TCP较多，UDP少）；  
3.UDP程序结构较简单；  
4.流模式与数据报模式 ；  
_\*\*_5.TCP保证数据正确性，UDP可能丢包，TCP保证数据顺序，UDP不保证。

tcp三次握手  
\(1\) 第一次握手：建立连接时，客户端A发送SYN包\(SYN=j\)到服务器B，并进入SYN\_SEND状态，等待服务器B确认。  
\(2\) 第二次握手：服务器B收到SYN包，必须确认客户A的SYN\(ACK=j+1\)，同时自己也发送一个SYN包\(SYN=k\)，即SYN+ACK包，此时服务器B进入SYN\_RECV状态。  
\(3\) 第三次握手：客户端A收到服务器B的SYN＋ACK包，向服务器B发送确认包ACK\(ACK=k+1\)，此包发送完毕，客户端A和服务器B进入ESTABLISHED状态，完成三次握手。  
tcp四次挥手  
（1）客户端A发送一个FIN，用来关闭客户A到服务器B的数据传送\(报文段4\)。  
（2）服务器B收到这个FIN，它发回一个ACK，确认序号为收到的序号加1\(报文段5\)。和SYN一样，一个FIN将占用一个序号。  
（3）服务器B关闭与客户端A的连接，发送一个FIN给客户端A\(报文段6\)。  
（4）客户端A发回ACK报文确认，并将确认序号设置为收到序号加1\(报文段7\)。  
为什么建立连接协议是三次握手，而关闭连接却是四次握手呢  
这是因为服务端的LISTEN状态下的SOCKET当收到SYN报文的连接请求后，它可以把ACK和SYN\(ACK起应答作用，而SYN起同步作用\)放在一个报文里来发送。但关闭连接时，当收到对方的FIN报文通知时，它仅仅表示对方没有数据发送给你了；但未必你所有的数据都全部发送给对方了，所以你可能未必会马上会关闭SOCKET,也即你可能还需要发送一些数据给对方之后，再发送FIN报文给对方来表示你同意现在可以关闭连接了，所以它这里的ACK报文和FIN报文多数情况下都是分开发送的。

## 其他

CAS单点登录：首先用户访问受保护的资源，权限没有认证，所以会把请求的URL以参数跳转到CAS认证中心，CAS认证中心发现没有SSO session，所以弹出登录页面，输入用户信息，提交到CAS认证中心进行信息的认证，如果信息正确，CAS认证中心就会创建一个SSO session——CASTGC cookie，这个CASTGC cookie包含了TGT，这个TGT作为一个用户session，它会分发一个ST返回给用户。用户拿到了ST后，访问带参数ST的资源地址，同时应用将ST发送给CAS认证中心，CAS认证中心对ST进行校验，判断ST是否是有效的，结果会返回一个包含成功信息的XML给应用。应用在建立相应的session cookie跳转到浏览器，用户在通过浏览器带cookie去应用访问受保护的资源地址，cookie验证成功便可以成功访问到信息。 第二次访问应用时，浏览器就会携带相应的session去验证用户是否登录，与一般单系统应用登录模式一样。 当我们访问其他的应用，与前面的步骤也是基本相同，首先用户访问受保护的资源，跳转回浏览器，浏览器含有先前登录的CASTGC cookie，CASTGC cookie包含了TGT并发送到CAS认证中心，CAS认证中心校验TGT是否有效，如果有效分发浏览器一个带ST参数的资源地址URL，应用活动ST后，再发送给CAS认证中心，如果认证了ST有效后，结果会返回一个包含成功信息的XML给应用。同样的步骤，应用在建立相应的session cookie跳转到浏览器，用户在通过浏览器带cookie去应用访问受保护的资源地址，验证成功便可以成功访问到信息。

\(1\)、TGC（ticket-granting cookie） 授权的票据证明，由 CAS Server 通过 SSL 方式发送给终端用户，存放用户身份认证凭证的Cookie，在浏览器和CAS Server间通讯时使用，并且只能基于安全通道传输（Https），是CAS Server用来明确用户身份的凭证。  
\(2\)、TGT（Ticket Grangting Ticket） TGT是CAS为用户签发的登录票据，拥有了TGT，用户就可以证明自己在CAS成功登录过。TGT封装了Cookie值以及此Cookie值对应的用户信息。用户在CAS认证成功后，CAS生成Cookie（叫TGC），写入浏览器，同时生成一个TGT对象，放入自己的缓存，TGT对象的ID就是Cookie的值。当HTTP再次请求到来时，如果传过来的有CAS生成的Cookie，则CAS以此Cookie值为key查询缓存中有无TGT ，如果有的话，则说明用户之前登录过，如果没有，则用户需要重新登录。  
\(3\)、ST（Service Ticket） ST是CAS为用户签发的访问某一service的票据。用户访问service时，service发现用户没有ST，则要求用户去CAS获取ST。用户向CAS发出获取ST的请求，如果用户的请求中包含Cookie，则CAS会以此Cookie值为key查询缓存中有无TGT，如果存在TGT，则用此TGT签发一个ST，返回给用户。用户凭借ST去访问service，service拿ST去CAS验证，验证通过后，允许用户访问资源。

支付系统幂等  
1、生成支付订单\(insert into ... on duplicate update ...\)  
2、订单状态complete，生成交易明细

