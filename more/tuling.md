[2021最新JAVA面试题200集（真题必问）](https://www.bilibili.com/video/BV1nK4y1H7g9)

# JVM调优

## 基本概念

![image-20210427112855142](tuling.assets/image-20210427112855142.png)

> 栈，用于存放程序运行时产生的局部变量
>
> 每一个线程都有自己的栈
>
> 栈中又分为栈帧（每一个方法有一个内存区域）

![image-20210427113438672](tuling.assets/image-20210427113438672.png)

> 不同方法产生的局部变量分配在自己的栈帧中
>
> 程序的方法嵌套调用（先分配的后释放）与栈的先进后出的特点类似

> javap -c hello.class > hello.txt
>
> 输出字节码文件（反汇编）

![image-20210427114728687](tuling.assets/image-20210427114728687.png)

> 程序计数器存放正在（即将）运行代码对应的位置
>
> 多线程时会出现CPU上下文切换，通过程序计数器可以知道被挂起的线程下次开始运行的位置

![image-20210427121816635](tuling.assets/image-20210427121816635.png)

>动态链接：程序的运行过程中根据符号找到该方法在方法区的内存地址
>
>方法出口：方法执行完成后返回的处理（返回值，返回位置）

![image-20210427122705213](tuling.assets/image-20210427122705213.png)

> 元空间存放常量，静态变量，类信息

![image-20210427123005309](tuling.assets/image-20210427123005309.png)

> 本地方法栈分配本地方法的内存空间

![image-20210427123243944](tuling.assets/image-20210427123243944.png)

> new 出来的对象先存放在 Eden 区，Eden 区放满之后触发 minor gc
>
> minior gc 会回收整个年轻代的垃圾对象，非垃圾对象的分代年龄会加一，并复制到空的某个幸存区，然后 Eden 和另一个幸存区清空内存，当分代年龄到达15时，会将对象移到老年代（静态变量，单例，对象缓存池，spring的容器bean）

```markdown
# 可达性分析算法
- 将“GC Roots”对象作为起点，从这些节点开始向下搜索引用的对象，找到的对象都标记为非垃圾对象，其余对象标记为垃圾对象
- GC Roots 根节点：线程栈的本地变量，静态变量，本地方法栈的变量
```

![image-20210427124327765](tuling.assets/image-20210427124327765.png)

## 调优

> jdk 自带调优工具
>
> jvisualvm，jmap，jstack

> 当老年代内存满后会触发 full gc ，回收整个堆的内存空间，如果 full gc 无法回收垃圾对象（一直被 gc root ）则会发生 OOM

> linux Java调优工具：阿里巴巴开源的 Arthas

调优的目的：减少 GC

优先减少 Full GC：Full GC 会发生 STW(stop the world)

STW的原因：防止在 GC 过程中对象的引用状态发生变化（线程执行结束，内存释放，对象的 GC root 释放）

![image-20210427180350951](tuling.assets/image-20210427180350951.png)

![image-20210427182732004](tuling.assets/image-20210427182732004.png)

![image-20210427182755898](tuling.assets/image-20210427182755898.png)

![image-20210427183324583](tuling.assets/image-20210427183324583.png)

如何优化，使其几乎不发生 full gc？

![image-20210427183601251](tuling.assets/image-20210427183601251.png)

> 将年轻代调为2G，老年代1G，这样不会触发对象动态年龄判断，将垃圾对象在年轻代就回收

![image-20210427183805451](tuling.assets/image-20210427183805451.png)

## 垃圾收集器

![image-20210427185145724](tuling.assets/image-20210427185145724.png)

![image-20210427185617417](tuling.assets/image-20210427185617417.png)

![image-20210427185642125](tuling.assets/image-20210427185642125.png)

当伊甸园的内存很大时（30G），minor gc 性能也会很低

* 边运行边回收，一次回收2~3G，STW总时间增加，但单次GC很快

通过设置垃圾收集器 G1 可以实现

# MySQL调优

## 索引

索引是帮助MySQL高效获取数据的排好序的数据结构

B树：每个节点存放较多的数据，一次 IO 即可查询较多的数据

![image-20210427191620590](tuling.assets/image-20210427191620590.png)

B+树：所有数据存放在叶子节点

![image-20210427191759064](tuling.assets/image-20210427191759064.png)

> MySQL每一个节点为一个数据页，约16KB

存储引擎修饰的是数据库表

![image-20210427205254676](tuling.assets/image-20210427205254676.png)

![image-20210427205736550](tuling.assets/image-20210427205736550.png)

> 聚集索引：数据和索引在一个文件(.IBD)
>
> 非聚集索引：数据和索引在不同的文件(.MYD .MYI)

> InnoDB 手动建主键的原因：InnoDB 要求必须有一个B+树来存储数据，如果没有建主键，InnoDB 会自己找一个数据不重复的字段作为唯一索引来组织整个表的数据，如果没有合适的字段，会维护一个隐藏列作为索引组织整个表的数据，而主键自带索引，以及减少MySQL的一些操作，所以建议手动建主键。
>
> 使用整型自增主键的原因：主键涉及大小比较，整型明显比字符串快，且占用空间更小，索引叶子节点从左到右依次递增，便于范围查找，为了维护依次递增，自增会将数据插入到最后一个叶子左边，效率高，而非自增会将数据插入叶子节点中间，B+树容易发生分裂和维护平衡，效率低

![image-20210427213123327](tuling.assets/image-20210427213123327.png)

> 联合索引数叶子按字段依次排序，存在最左匹配原则

# JMM

![image-20210427223532815](tuling.assets/image-20210427223532815.png)

> 线程真正操作的工作内存的数据是主内存中数据的副本

```markdown
# JMM 数据原子操作
- read 读取：从主存中读取数据
- load 载入：将主内存读取到的数据写入工作内存
- use 使用：从工作内存读取数据来计算
- assign 赋值：将计算好的值重新赋值到工作内存中
- store 存储：将工作内存数据写入主内存
- write 写入：将store过去的变量值赋值给主内存中的变量
- lock 锁定：将主内存变量加锁，标识为线程独占状态
- unlock 解锁：将主内存变量解锁，解锁后其他线程可以锁定该变量
```

```java
// volatile demo
public class Demo01 {

    // private static boolean  initFlag = false;
    // 使用 volatile 关键字修饰才能跳出死循环
    private volatile static boolean initFlag = false;

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            System.out.println("waiting data ...");
            // 死循环
            while (!initFlag) {
                // 这样也可以跳出循环
                // System.out.println(initFlag);
            }
            System.out.println("=======success=======");
        }).start();

        TimeUnit.SECONDS.sleep(2);
        new Thread(Demo01::prepareData).start();
    }

    public static void prepareData() {
        System.out.println("prepare data...");
        initFlag = true;
        System.out.println("prepare data end...");
    }
}
```

initFlag 没有加 volatile 修饰的内存工作模型：

![image-20210427231630500](tuling.assets/image-20210427231630500.png)

```markdown
# volatile 缓存可见性实现原理
- 底层实现主要是通过汇编 lock 前缀指令，它会锁定这块内存区域的缓存，并回写到主内存
# lock 指令
- 会将当前处理器缓存行的数据立即写回到内存
- 这个写回内存的操作会引起其他 CPU 里缓存了该内存地址的数据无效（MESI 缓存一致性协议）
- 提供内存屏障功能，使 lock 前后指令不能重排序
```

```markdown
# 指令重排与内存屏障
- 并发编程三大特性:可见性,有序性,原子性
- volatile 保证可见性和有序性,但不保证原子性,保证原子性需要借助 synchronized 这样的锁机制
- 指令重排:在不影响单线程程序执行结果的前提下,计算机为了最大限度地发挥机器性能,会对机器指令重排序优化
- 重排序会遵循 as-if-serial 与 happens-before 原则
```

```markdown
- as-if-serial:不管怎样重排序,单线程内程序的执行结果不能被改变,即编译器和处理器不会对存在数据依赖关系的操作做重排序(语义分析是否存在依赖关系)
- happens-before:JMM可以通过happens-before关系向程序员提供跨线程的内存可见性保证（如果A线程的写操作a与B线程的读操作b之间存在happens-before关系，尽管a操作和b操作在不同的线程中执行，但JMM向程序员保证a操作将对b操作可见）
* 程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。
* 监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。
* volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。
* 传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。
* start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。
* join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。
* 程序中断规则：对线程interrupted()方法的调用先行于被中断线程的代码检测到中断时间的发生。
* 对象finalize规则：一个对象的初始化完成（构造函数执行结束）先行于发生它的finalize()方法的开始。
```

对象的创建

![image-20210501184852854](tuling.assets/image-20210501184852854.png)

```markdown
- 单例模式双重检测锁可能发生对象半初始化问题
- 解决方案:将目标属性声明为 volatile
```

```java
// JVM 规定volatile需要实现的内存屏障
// a 为 volatile 变量

a = 2; // volatile 写
/* StoreStore 屏障 */
a = 1; // volatile 写
/* StoreLoad 屏障 */
b = a; // volatile 读
/* LoadLoad 屏障 */
/* LoadStore 屏障 */

// 屏障的底层为汇编 lock 指令
```

# Java并发锁

```java
public class A {
    int num = 0;
    AtomicInteger atomicInteger = new AtomicInteger();

    public long getNum() {
        // return num;
        return atomicInteger.get();
    }

    public void increase() {
        // synchronized (this){
        // 300ms
        // num++;
        // }
        // 200ms
        atomicInteger.incrementAndGet();
    }
    
    public static void main(String[] args) throws InterruptedException {
        A a = new A();
        long start = System.currentTimeMillis();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000000; i++) {
                a.increase();
            }
        });
        t1.start();

        for (int i = 0; i < 10000000; i++) {
            a.increase();
        }
        t1.join();
        long end = System.currentTimeMillis();
        System.out.println(end-start);
        System.out.println(a.getNum());
    }
}
```

synchronized 底层在 JDK1.6之前为重量级锁(互斥锁,悲观锁,同步锁)

![image-20210501213527833](tuling.assets/image-20210501213527833.png)

CAS : 无锁,自旋锁,乐观锁,轻量级锁

```java
public void increase() {
    //  atomicInteger.incrementAndGet();

    while (true) {
        // 得到旧值
        int oldValue = atomicInteger.get();
        // 对旧值 +1
        int newValue = oldValue + 1;
        // 用旧值与内存中的值做对比
        // 如果相同,说明内存中的值没有被其它线程修改,设置新值
        // 如果不相同,则不修改,返回 false,循环判断
        if (atomicInteger.compareAndSet(oldValue, newValue)) {
            break;
        }
    }
}
// 执行 CAS 代码并没有阻塞,而是自旋
```

```markdown
# CAS 问题
- 原子性问题: 底层通过 lock cmpxchgq 缓存行锁/总线锁
- ABA 问题: 版本号
- 轻量级锁不一定比重量级锁性能高(大量的CAS也比较耗性能)
```

```markdown
# synchronized 锁优化(JDK1.6 开始)
```

![image-20210502000010232](tuling.assets/image-20210502000010232.png)

![image-20210502143614533](tuling.assets/image-20210502143614533.png)

```xml
<!-- 打印对象内部的组成结构 -->
<dependency>
    <groupId>org.openjdk.jol</groupId>
    <artifactId>jol-core</artifactId>
    <version>0.10</version>
</dependency>
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
}


public class LockUpgrade {
    public static void main(String[] args) throws InterruptedException {
        User userTemp = new User();
        System.out.println("无状态(001): " + ClassLayout.parseInstance(userTemp).toPrintable());

        // jdk11 默认启动时开启偏向锁?
        /*
        Jvm 默认延时4秒开启偏向锁,可通过 -XX:BiasedLockingStartupDelay = 0 取消延时
        如果不要偏向锁,可通过 -XX:-UseBiasedLocking = false 设置
         */
        TimeUnit.SECONDS.sleep(5);
        User user = new User();
        System.out.println("启用偏向锁(101): " + ClassLayout.parseInstance(user).toPrintable());

        for (int i = 0; i < 2; i++) {
            synchronized (user) {
                System.out.println("偏向锁(101)(带线程id): " + ClassLayout.parseInstance(user).toPrintable());
            }
            // 即使跳出 synchronized 偏向锁不会释放
            System.out.println("偏向锁释放(101)(带线程id): " + ClassLayout.parseInstance(user).toPrintable());
        }

        // 只要之前是偏向锁,之后有其它线程操作该对象,则会发生锁升级
        // 轻量级锁
        new Thread(() -> {
            synchronized (user) {
                System.out.println("轻量级锁(00): " + ClassLayout.parseInstance(user).toPrintable());
                try {
                    System.out.println("睡眠3s================");
                    TimeUnit.SECONDS.sleep(3);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("轻量级-->重量级(10): " + ClassLayout.parseInstance(user).toPrintable());
            }
        }).start();

        TimeUnit.SECONDS.sleep(1);
        new Thread(() -> {
            synchronized (user) {
                System.out.println("重量级锁(10): " + ClassLayout.parseInstance(user).toPrintable());
            }
        }).start();
    }
}
// jdk11 默认开启偏向锁,即new一个对象,该对象头就开启即开启偏向锁
// 只要开启偏向锁,偏向锁就不会释放
// 当有其它线程加锁时,就会转为轻量级锁
// 当两个线程竞争时,就会转为重量级锁
// 重量级锁底层有优化
```

# HashMap

```markdown
# HashMap 发生哈希冲突采用链表形式
- 数组中的引用始终指向链表的头节点
- jdk1.7 使用头插法,涉及数组中引用的修改
- jdk1.8 使用尾插法
```

concurrentHashMap

![image-20210502182630392](tuling.assets/image-20210502182630392.png)

51