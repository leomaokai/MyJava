# 深入理解HashMap

[高端的面试从来不会在HashMap的红黑树上纠缠太多 (qq.com)](https://mp.weixin.qq.com/s/UOr9BOWrv67d8l1VQxqUkA)

```markdown
# HashMap 的基本点
- Java 中 HashMap 的实现是基于数据结构的数组,每一对 key->value 的键值对组成 Entity 类似双向链表的形式存放在数组中
- 元素在数组中的位置由 key.hashCode() 的值决定,如果两个 key 的哈希值相同,即发生了哈希碰撞,则这两个 key 对应的 Entity 将以链表的形式存放在数组中
- 调用 HashMap.get() 的时候会首先计算 key 的值,继而在数组中找到 key 对应的位置,然后遍历该位置上的链表找相应的值
- 为了提升 HashMap 的读取效率,当 HashMap 中存储的元素大小等于桶数组大小乘以负载因子(0.75)的时候,整个 HashMap 就要扩容,以减少哈希碰撞
- Java8 中如果桶数组的同一个位置上的链表数量超过一个定值(8),则整个链表有一定概率会转为一颗红黑树
```

整个 HashMap 中最重要的四个点:

* 初始化 new HashMap()
* 数据寻址 hash() 方法
* 数据存储 put() 方法
* 扩容 resize() 方法

## new HashMap()

在 JDK8 中，在调用 new HashMap() 时并没有分配数组堆内存，只是做了一些参数校验，初始化了一些常量

```java
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    this.loadFactor = loadFactor;
    this.threshold = tableSizeFor(initialCapacity);
}

// tableSiezeFor() 方法是找到一个大于 cap 的最小的 2 的整数幂
static final int tableSizeFor(int cap) {
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}

public static int numberOfLeadingZeros(int i) {
    if (i <= 0)
        return i == 0 ? 32 : 0;
    int n = 31;
    if (i >= 1 << 16) { n -= 16; i >>>= 16; }
    if (i >= 1 <<  8) { n -=  8; i >>>=  8; }
    if (i >= 1 <<  4) { n -=  4; i >>>=  4; }
    if (i >= 1 <<  2) { n -=  2; i >>>=  2; }
    return n - (i >>> 1);
}
```

HashMap 在第一次 put 是会调用 inflateTable 计算桶数组的长度

## hash()

hash() 函数承担着寻址定址的作用，其性能对整个 HashMap 的性能影响巨大，什么才是一个好的 hash() 函数？

* 计算出来的哈希值足够散列，能够有效减少哈希碰撞
* 本身能够快速计算得出，因为 HashMap 每次调用 get 和 put 的时候都会调用 hash

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
/*
(h = key.hashCode()) ^ (h >>> 16) 这个位运算是将 key.hashCode() 计算出来的 hash 值的高16位与低16位异或
*/
```

```java
/*
hash() 函数的作用是用来确定 key 在桶数组中的位置index
*/
index =(table.length - 1) & key.hash();
```

`table.length` 是一个 2 的正整数次幂，类似于`00010000`，这样的值减一就成了`00001111`，通过位运算可以高效寻址，所以桶数组的长度一直都是 2 的整数次幂，好处之一就是可以通过构造位运算快速寻址定址

既然计算出来的哈希值要与`table.length - 1`做与运输，那就意味着哈希值只有低位有效，这样会加大碰撞几率，因此让高16位与低16位做异或，让地位保留部分高位信息，减少哈希碰撞

## put()

```markdown
# 在 Java8 中 put 这个方法的思路分为以下几步：
- 调用 key 的 hashCode 方法计算哈希值，并据此计算出数组下标 index
- 如果发现当前的桶数组为 null ，则调用 resize() 方法进行初始化
- 如果没有发生哈希碰撞，则直接放到对应的桶中
- 如果发生哈希碰撞，且节点已经存在，调用 equals 方法，如果相同就替换掉相应的 value
- 如果发生哈希碰撞，且桶中存放的是树状结构，则挂载到树上
- 如果碰撞后为链表，添加到链表尾，如果链表长度超过 TREEIFY_THRESHOLD默认是8，则将链表转换为树结构
- 数据 put 完成后，如果 HashMap 的总数超过 threshold 就要 resize
```

```java
public V put(K key, V value) {
    // 调用 hash()
    return putVal(hash(key), key, value, false, true);
}
// onlyIfAbsent 如果为 true 则不更改当前值
// evict 如果为 false 则处于创造模式...
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 第一次 put ，如果数组为空，则调用 resize() 初始化
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 根据数组长度 n 和 hash 来寻址
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 如果当前位置为空，则创建节点插入
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // 哈希碰撞，节点已存在，替换
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 哈希碰撞，树结构
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 哈希碰撞，链表结构
        else {
            // 循环遍历链表
            for (int binCount = 0; ; ++binCount) {
                // 节点为空，插入链表尾部
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 链表过长，转换为数结构
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    // 插入成功或树化后，跳出循环
                    break;
                }
                // 节点以存在，跳出循环
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                // 否则，指针后移，循环遍历
                p = e;
            }
        }
        // 跳出循环，节点赋值
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 节点个数如果大于阈值，这扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

## resize()

`resize()`是整个`HashMap`中最复杂的一个模块，如果`put`数据后超过了`threshold`的值，则需要扩容，扩容意味着桶数组长度的变化，而`Hashap`寻址是通过`index = (table.length - 1) & key.hash()`来计算的，现在长度发生变化，则部分`key`的位置也发生了变化，`HashMap`是如何设计的？

这里就涉及到桶数组长度为2的正整数幂的第二个优势了：当桶数组长度为2的正整数幂时，如果桶发生扩容（长度翻倍），则桶中的元素大概只有一半需要切换到新的桶中，另一半留在原先的桶中就可以，并且这个概率可以看做是均等的。

![image-20210424143356397](Map.assets/image-20210424143356397.png)

如果在即将扩容的那个位上`key.hash()`的二进制值为0，则扩容后在桶中的地址不变，否则，扩容后最高位变为了1，新的地址可以快速计算出来`newIndex = table.length + oldIndex`

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    // oldCap 可以看成 table.length
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    // oldCap 大于0，则扩容
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
	// 如果oldCap为0， 但是oldThr不为0，则代表的是table还未进行过初始化
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        // 新的桶的位置 = 旧位置 + oldCap
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

## 总结

```markdown
# HashMap 内部的 bucket 数组长度为什么一直都是 2 的整数次幂
- 答：这样做有两个好处，第一，可以通过 (table.length - 1) & key.hash() 这样的位运算快速寻址，第二，在 HashMap 扩容的时候可以保证同一个桶中的元素均匀的散列到新的桶中，具体一点就是同一个桶中的元素在扩容后一般留在原先的桶中，一般放到了新的桶中。
# HashMap 默认的 bucket 数组是多大
- 答：默认是16，即时指定的大小不是2的整数次幂，HashMap也会找到一个最近的2的整数次幂来初始化桶数组。
# HashMap 什么时候开辟 bucket 数组占用内存
- 答：在第一次 put 的时候调用 resize 方法
# HashMap 何时扩容？
- 答：当 HashMap 中的元素熟练超过阈值时，阈值计算方式是 capacity * loadFactor，在 HashMap 中 loadFactor 是 0.75
# 桶中的元素链表何时转换为红黑树，什么时候转回链表，为什么要这么设计？
- 答：当同一个桶中的元素数量大于等于 8 的时候元素中的链表转换为红黑树，反之，当桶中的元素数量小于等于 6 的时候又会转为链表，这样做的原因是避免红黑树和链表之间频繁转换，引起性能损耗
# Java8 中为什么要引进红黑树，是为了解决什么场景的问题？
- 答：引入红黑树是为了避免 hash 性能急剧下降，引起 HashMap 的读写性能急剧下降的场景，正常情况下，一般是不会用到红黑树的，在一些极端场景下，假如客户端实现了一个性能拙劣的 hashCode 方法，可以保证 HashMap 的读写复杂度不会低于 O(lgN)
# HashMap 如何处理 key 为 null 的键值对？
- 放置在桶数组中下标为0的桶中
```