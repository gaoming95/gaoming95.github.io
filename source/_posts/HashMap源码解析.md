---
title: HashMap源码解析
subtitle: 哈希表（hash table）的实现是基于Map接口，这种实现方式提供了Map所有的可选操作，并且允许空键和空值（null key null value）
date: 2020-06-03
author: 高明
tags:
	- Java基础
---



# HashMap源码解析

哈希表（`hash table`）的实现是基于`Map`接口，这种实现方式提供了`Map`所有的可选操作，并且允许空键和空值（`null key null value`）

`HashMap`大致等同于`HashTable`，不同的是`HashMap`的实现是不同步的，并且允许为空。

`HashMap`不能保证`map`的顺序

假设哈希函数将元素适当地分散在哈希桶中，`HashMap`可实现常数级别的`Get`和`Put`操作

`HashMap`的迭代时间与`HashMap`中的容量大小（桶数）和键值对数量成正比，因而，如果考虑`HashMap`的遍历性能，尽量不要设置过大的初始容量（`initial capacity`）（或者过小的负载因子`load factor`）

初始容量（`initial capacity`）和负载因子（`load factor`）是影响`HashMap`性能两个重要的参数

1. 容量（`capacity`）是哈希表的桶（`buckets`）的数量，初始容量就是创建哈希表时的容量
2. 负载因子（`load factor`）是衡量`HashTable`允许填充程度的度量，在其容量增加之前获取。当`HashTable`中`Entry`（`key value对`）数量超过负载因子与当前容量的乘积时，哈希表将会被重新映射（即，内部的结构会被重建）

通常情况下，默认的负载因子是`0.75`，这个数值是在平衡时间和空间得到经验数值。数值过高，会降低空间开销，但是增加了查找（`Get`和`Put`）的成本。在设置`HashMap`初始容量时，应考虑预期`Entry`数量及其负载因子，以最大程度地减少重新哈希操作的次数。如果初始容量大于最大的entry数量除以负载系数，则不会进行任何哈希操作。

如果一个`HashMap`将会存储很多实例，我们初始化`HashMap`的时候，尽可能地用较大的容量去初始化它，这样可以避免或者减少`HashMap`重新映射的次数。如果`HashMap`中有较多的`Key`使用`HashCode()`方法计算的结果相同，对于任何`HashTable`它的性能是肯定降低的[减少哈希碰撞的次数]。为了减少此影响，当`Key`是可比较的（`Comparable`），那么它就会在`key`中使用比较顺序，来打破这种`HashCode`一致的平衡。

**`HashMap`的实现方式是非同步的**。如果多个线程同时访问一个`HashMap`，并且至少有一个线程结构性地修改了`HashMap`，那么必须在外部进行同步。所谓**结构性修改**，就是增加（`put`）或者删除（`remove`）一个或者多个哈希映射的任何操作。如果仅仅更改`HashMap`已经包含的`Key`上所关联的值`Value`，这不是结构性的更改。通常情况下，可以通过同步一些封装了`HashMap`的对象来实现同步的效果。如果不存在这样的对象，`HashMap`应该被`Collections.synchronizedMap`包裹（`Collections.synchronizedMap(HashMap)`），最好在创建的时候就完成此操作，以防止意外情况下非同步的访问`Map`。`Example: Map m = Collections.synchronizedMap(new HashMap(...));`

所有此类的`集合视图方法`返回的迭代器都是快速失败的，在创建迭代器之后的任何时候，如果对`HashMap`进行了结构性修改，除了通过迭代器自身的`remove`方法外，该迭代器都会抛出`ConcurrentModificationException`。因此，面对并发修改，迭代器会快速返回失败，并且不会在后面冒任何不确定的行为风险。注意，迭代器的快速失败行为无法得到保证，因为通常来说，在存在不同步的并发修改的情况下，不可能做出任何严格的保证。快速失败的迭代器会尽最大努力抛出 `ConcurrentModificationException` 。因此，我们不能编写依赖于此异常的程序来确保程序的正确性：**迭代器的快速失败行为应仅用于检测错误**。

## 1 HashMap.Java

`HashMap`通常被用来充当装箱的哈希表（`bins`），当`bins`太大时，它们将转换为`TreeNodes`的`bin`，每个`bin`的结构与`java.util.TreeMap`中相似。大多数方法尝试使用普通的`bins`，但是在适用时转到`TreeNode`方法（只需通过检查节点的实例`instance`）。`TreeNodes`的`Bin`可以像其他`Bin`一样遍历和使用，但在填充过多时，它还支持更快的查找。然而，由于绝大多数正常使用中的`bins`数量并没有太多，因而在使用表方法的过程中可能会延迟检查是否存在`Tree Bins`。

`Tree Bins`（即元素均为`TreeNode`的箱），主要由`HashCode`排序，但是在`HashCode`计算结果一致的情况下，如果两个元素是可比较的（即均继承`Comparable`接口，`class C implements Comparable<C>`），则用他们的比较方法（`compareTo` ）进行排序（通过反射检查泛型类型以验证这一点，参见方法`comparableClassFor`）。当键具有不同的哈希值或可排序时，增加`Tree bins`的复杂度将会提供最坏情况的操作（`O(logn)`）。因而，在`HashCode()`方法返回分布不均的值以及许多键都共享相同的`HashCode()`值（它们是可比较的），性能也会缓慢下降。如果这两种方法都不适用，那么与不采取预防措施相比，我们可能会在时间和空间上浪费大约两倍的时间。 但是唯一已知的情况是由于不良的用户编程已经导致程序运行非常缓慢，以至于几乎没有什么区别。

因为`TreeNode`的大小约为常规节点的两倍，所以我们仅在当`bins`拥有了足够多的节点时，才会使用它们。当`bins`拥有的节点变小时（通过`remove`或者调整大小），他们会转换成普通`bins`。在具有良好分布的`hashCode`的用法中，很少使用`tree bins`。尽管由于调整粒度，差异很大，理想情况下，在随机`HashCodes`下，`bin`中节点的频率遵循泊松分布（[链接](http://en.wikipedia.org/wiki/Poisson_distribution)），其默认调整大小阈值为`0.75`，平均参数为`0.5`。忽略方差，列表大小`k`的预期出现次数是：
$$
k = \frac{e^{-0.5}* 0.5^{k}}{k!}
$$

```
* 0:    0.60653066
* 1:    0.30326533
* 2:    0.07581633
* 3:    0.01263606
* 4:    0.00157952
* 5:    0.00015795
* 6:    0.00001316
* 7:    0.00000094
* 8:    0.00000006
more: less than 1 in ten million
```

`TreeNode`的根通常是它的第一个节点，有时根可能在其他位置（当且仅当 `iterator.remove`），但可以在父链接之后恢复，方法`TreeNode.root()`。所有适用的内部方法均接受哈希码作为参数（通常由公共方法提供），从而允许它们在不重新计算用户hashCode的情况下彼此调用。大多数内部方法还接受`tab`参数，该参数通常是当前表，但是在调整大小或转换时可以是新的也可以是旧的。

当`bin`列表被树化，拆分或未树化时，我们将它们保持在相同的相对访问/遍历顺序（即字段`Node.next`）中，以更好地保留局部性，并略微简化对调用`iterator.remove`的拆分和遍历的处理。 当在插入时使用比较器时，为了在重新平衡之间保持总体排序（或此处要求的接近度），我们将类和标识`HashCodes`作为平局决胜器进行比较。

子类`LinkedHashMap`的存在阻碍了纯模式与树模式之间的使用和转换。 有关定义为在插入，删除和访问时调用的挂钩方法的信息。

```java
// 默认的初始容量 16 必须是2的幂 
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
// 最大的容量 2^30 = 1073741824，如果构造函数使用了更大的值，则使用该值
static final int MAXIMUM_CAPACITY = 1 << 30;
// 默认的负载系数 0.75
static final float DEFAULT_LOAD_FACTOR = 0.75f;
// 使用Tree而不是list的阈值 8，当将元素添加到具有8个节点的桶中时，桶会由列表转化为树（当某个桶节点数量大于8时，会转换为红黑树）
// 该值必须大于2，并且至少应为8
static final int TREEIFY_THRESHOLD = 8;
// 当某个桶节点数量小于6时，会转换为链表，前提是它当前是红黑树结构。
static final int UNTREEIFY_THRESHOLD = 6;
// 可将其分类为树木的最小工作台容量,至少应为4 * TREEIFY_THRESHOLD 以避免冲突
// 当整个hashMap中元素数量大于64时，也会进行转为红黑树结构
static final int MIN_TREEIFY_CAPACITY = 64;
// 该表在首次使用时初始化，并根据需要调整大小。transient 表示不被序列化
// 分配长度后，长度始终是2的幂（有些操作允许长度为0）
transient Node<K,V>[] table;
// 保存缓存的entrySet()，将数据转换成set的另一种存储形式，这个变量主要用于迭代功能
transient Set<Map.Entry<K,V>> entrySet;
// Map中键值对的数量
transient int size;
// HashMap进行结构性修改的数量（用于迭代器的快速失败）
transient int modCount;
// 下一个要调整大小的大小值（容量*负载系数）。
int threshold;
// 负载因子
final float loadFactor;
```

其中内部类`Node`

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```

### 1.1 构造函数

#### 1.1.1 构造函数1

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
```

**【`tableSizeFor`】**

```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

该函数将输入的初始容量`initialCapacity`转换为大于等于该参数的最小2的幂。如果大于最大的容量（`MAXIMUM_CAPACITY`）则返回`MAXIMUM_CAPACITY`。其中`MAXIMUM_CAPACITY=2^30`

> 假设输入cap的二进制为01xxx...xxx
> 对n右移1位：001xx...xxx，再位或：011xx...xxx
> 对n右移2为：00011...xxx，再位或：01111...xxx
> 此时前面已经有四个1了，再右移4位且位或可得8个1
> 同理，有8个1，右移8位肯定会让后八位也为1
> 综上可得，该算法让最高位的1后面的位全变为1
> 最后再让结果n+1，即得到了2的整数次幂的值了
> 由于int是32位，所以>>>16便能满足
> `cap-1`是为了兼容`cap`等于2的幂的情况

#### 1.1.2 构造函数2

```java
public HashMap(int initialCapacity) {
    this(initialCapacity, DEFAULT_LOAD_FACTOR);
}
```

#### 1.1.3 构造函数3

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
```

#### 1.1.4 构造函数4

```java
public HashMap(Map<? extends K, ? extends V> m) {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
    putMapEntries(m, false);
}
```

### 1.2 putMapEntries函数

**`putMapEntries`函数** 

`evict`首次初始化为`false`，否则为`true`

```java
final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    int s = m.size();
    // 当 m size 大于 0 时
    if (s > 0) {
        if (table == null) { // pre-size
            float ft = ((float)s / loadFactor) + 1.0F; // 计算理论上的容量
            int t = ((ft < (float)MAXIMUM_CAPACITY) ? // 理论容量应该小于等于 最大容量
                     (int)ft : MAXIMUM_CAPACITY);
            if (t > threshold) // 如果该容量大于 threhold，则计算下一次临界的大小
                threshold = tableSizeFor(t);
        }
        else if (s > threshold) // 超出 threshold 时，则开始 resize()
            resize();
        // 遍历 m 中的 key value，赋值
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            K key = e.getKey();
            V value = e.getValue();
            putVal(hash(key), key, value, false, evict);
        }
    }
}
```

个人觉得这里如果按照定义应该是，如果`table`为`null`，则理论计算的容量应该是`tt`，即先按照`s/loadFactor`计算理论容量，该容量应该取`2`的幂，为实际容量。但是这样可能会产生问题，即计算时，如果增加`384`个数据，按照下面的计算，理论计算所得为`384/0.75+1=513`，`tt`计算得到`1024`，阈值为`768`。但实际上，`table`在初始化时，阈值计算没有用装载因子计算，理论上看起来像直接等于容量的大小。

```java
if (table == null) { // pre-size
    float ft = ((float)s / loadFactor) + 1.0F; // 计算理论上的容量
    int tt = tableSizeFor(t);
    int t = ((tt < (float)MAXIMUM_CAPACITY) ? // 理论容量应该小于等于 最大容量
             (int)tt : MAXIMUM_CAPACITY);
    if (t > threshold) // 如果该容量大于 threhold，则计算下一次临界的大小
        threshold = t * DEFAULT_LOAD_FACTOR;
}
```

### 1.3 resize函数

初始化或者增加表的大小。如果为空，则根据初始容量目标字段`threshold`进行分配。否则，因为我们使用的是`2`的幂，所以每个`bin`中的元素必须保持相同的索引，或者在新表中以`2`的幂进行偏移。

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table; // 老的table
    int oldCap = (oldTab == null) ? 0 : oldTab.length; // 获取老容量的值
    int oldThr = threshold; // 获取老的阈值
    int newCap, newThr = 0; // 新的容量和新的阈值进行初始化
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab; // 如果老的容量大于最大容量，则无法resize
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            // 新容量是旧容量的两倍，如果新容量小于最大容量且老容量大于初始容量，则新的阈值也等于就阈值的两倍
            newThr = oldThr << 1; // double threshold 这边newThr由oldThr扩大两倍，oldThr由threshold得到，不会出现0的情况
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        // 老容量=0，说明老的table为空，并且老的阈值大于0，则用老的阈值初始化新的容量
        newCap = oldThr; // 这里newthr可能等于0
    else {               // zero initial threshold signifies using defaults
        // 老容量=0，老阈值=0，则全部用默认值初始化
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    
    // 已上计算了新的容量（newCap）和新的阈值（newThr）
    
    if (newThr == 0) {
        // 如果新的阈值等于0，则计算理论阈值
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap]; // 初始化新的table
    table = newTab; // 将table指向newTab
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) { // 当前节点不为空
                oldTab[j] = null;
                if (e.next == null) // e只有一个节点，则直接定位赋值[e.hash&(newCap-1)]
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    // 如果有多节点，且e是TreeNode，则开始调用红黑树的split方法
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    // 如果是单纯的链表，则保存顺序赋值
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            // 不需要改动
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
                        // 赋值位置
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

总的来说，先计算新的容量和新的阈值，再遍历`oldTable`的节点，将旧节点上面的数据放在新表上。

### 1.4 `putVal`函数

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length; // 如果table为空或者table的大小为0，则resize
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null); // 如果hash计算的位置为null，则直接再tab[i]上面增加该节点
    else {
        // 否则
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p; // 哈希值计算相等，value相等
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 增加值
            for (int binCount = 0; ; ++binCount) {
                // p的next为空
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null); // 直接赋值
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash); // 树化
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break; // key存在
                p = e; // 赋值
            }
        }
        if (e != null) { // existing mapping for key 如果已经存在
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold) // 超出阈值 resize
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

## 2 `putVal`函数

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```















```java
public class ThreadOne extends Thread {
    Map<String, String> s = new HashMap<String, String>();
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            s.put(currentThread().getName() + i, "world");
            for (Map.Entry e :
                    s.entrySet()) {
                System.out.println(e.getKey() + "||" + e.getValue());
            }
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class MyMain {
    public static void main(String[] args) {
        ThreadOne threadOne = new ThreadOne();
        Thread thread1 = new Thread(threadOne, "A");
        Thread thread2 = new Thread(threadOne, "B");
        thread1.start();
        thread2.start();
    }
}
```

```bash
B0||world
B0||world
A1||world
A1||world
B0||world
B1||world
Exception in thread "A" java.util.ConcurrentModificationException
	at java.util.HashMap$HashIterator.nextNode(HashMap.java:1429)
	at java.util.HashMap$EntryIterator.next(HashMap.java:1463)
	at java.util.HashMap$EntryIterator.next(HashMap.java:1461)
	at ThreadOne.run(ThreadOne.java:32)
	at java.lang.Thread.run(Thread.java:745)
A1||world
B2||world
B0||world
B1||world
```

【Q1】HashMap允许空的键和空的值吗？
【A1】HashMap允许空的键和空的值，HashTable不允许空的键和空的值

> HashTable: Any non-null object can be used as a key or as a value.
> HashMap: This implementation provides all of the optional map operations, and permits null values and the null key.

【Q2】HashMap线程安全吗？
【A2】线程不安全

> (The HashMap class is roughly equivalent to <tt>Hashtable</tt>, except that it is  unsynchronized and permits nulls.)