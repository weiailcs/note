### List、Set、Map的区别
答：
- List: 存储的元素有序、可重复
- Set: 存储的元素无序、不可重复
- Map: 以键值对（key-value）方式存储元素，key无序、不可重复，value无序、可重复

### ArrayList和vector的区别
答：ArrayList线程不安全，vector线程安全

### ArrayList和LinkedList的区别
答：
1. ArrayList的底层是（Object）数组，LinkedList底层是双向链表（JDK1.6之前是循环，JDK1.7去掉了循环）
2. ArrayList索引元素快，LinkedList插入、删除元素快

### ArrayList扩容机制
答：new 一个空的ArrayList数组，添加第一个元素，数组的大小会变为10，每次扩容为原来的1.5倍，也就是添加第11个元素时，数组的大小会变为15.

### HashMap 和 Hashtable 的区别
答：
1. HashMap线程不安全，Hashtable线程安全
2. HashMap效率比Hashtable更高
3. HashMap允许有空键值对，键只允许有一个null值，值允许有多个null值；Hashtable不允许有空键值对
4. JDK1.8之后HashMap的底层结构为数组链表红黑树，但是Hashtable没有变
5. 初始容量大小和每次扩充容量大小的不同：创建时如果不指定容量初始值，Hashtable默认的初始大小为11，之后每次扩充，容量变为原来的 2n+1。HashMap默认的初始化大小为16。之后每次扩充，容量变为原来的 2 倍。② 创建时如果给定了容量初始值，那么Hashtable会直接使用你给定的大小，而 HashMap会将其扩充为2的幂次方大小（HashMap 中的tableSizeFor()方法保证，下面给出了源代码）。也就是说HashMap 总是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是2的幂次方。

### HashMap 和 HashSet 区别
答：HashSet底层就是基于 HashMap 实现的。

### HashMap 和 TreeMap 区别
答：TreeMap 和HashMap都继承自AbstractMap，但是需要注意的是TreeMap它还实现了NavigableMap接口和SortedMap接口。实现NavigableMap 接口让 TreeMap 有了对集合内元素的搜索的能力。实现SortMap接口让 TreeMap 有了对集合中的元素根据键排序的能力。默认是按key的升序排序，不过我们也可以指定排序的比较器。


### HashSet底层原理
答：当你把对象加入HashSet时，HashSet会先计算对象的hashcode值来判断对象加入的位置，同时也会与其他加入的对象的hashcode值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet就不会让加入操作成功。

### HashMap 的底层实现
答：
- JDK1.8 之前 HashMap 底层是 数组和链表 结合在一起使用也就是 链表散列。HashMap 通过 key 的 hashCode经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash值以及key是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。
所谓扰动函数指的就是 HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞。
- JDK1.8 之后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

### hashmap源码（细说，从初始化到put、get、扩容、红黑树、1.8做了哪些优化）
答：

###  HashMap 的长度为什么是 2 的幂次方
答：为了能让 HashMap存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。数组下标的计算方法是“ (n-1)&hash”。（n代表数组长度）
（取余(%)操作中如果除数是2的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方；）。”并且采用二进制位操作&，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是 2 的幂次方。）

### HashMap 多线程操作导致死循环问题
答：主要原因在于并发下的Rehash会造成元素之间会形成一个循环链表。不过，jdk 1.8 后解决了这个问题（，但是还是不建议在多线程下使用 HashMap,因为多线程下使用HashMap还是会存在其他问题比如数据丢失。并发环境下推荐使用 ConcurrentHashMap） 。

### 多线程Put可能发生的问题
答：
1. 可能导致死循环
2. 可能导致元素丢失
3. put非null元素后get出来的却是null

### ConcurrentHashMap底层实现
答：Java1.7及之前 ConcurrentHashMap 使用分段锁，也就是每一个 Segment 上同时只有一个线程可以操作，每一个 Segment 都是一个类似 HashMap 数组的结构，它可以扩容，它的冲突会转化为链表。但是 Segment 的个数一但初始化就不能改变。

Java1.8 及之后 ConcruuentHashMap 使用 Synchronized 锁加 CAS 的机制。结构也由 Java1.7 中的 Segment 数组 + HashEntry 数组 + 链表 进化成了 Node 数组 + 链表 / 红黑树，Node 是类似于一个 HashEntry 的结构。它的冲突再达到一定大小时会转化成红黑树，在冲突小于一定数量时又退回链表。

### ConcurrentHashMap 和 Hashtable 的区别
答：
1. 底层数据结构：JDK1.7的ConcurrentHashMap底层采用分段的数组+链表 实现，JDK1.8采用的数据结构跟HashMap1.8的结构一样，数组+链表/红黑二叉树。（Hashtable和JDK1.8之前的HashMap的底层数据结构类似都是采用 数组+链表的形式，数组是的主体，链表则是主要为了解决哈希冲突而存在的）；
2. 实现线程安全的方式（重要）：①在JDK1.7的时候，ConcurrentHashMap（分段锁） 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 到了JDK1.8的时候已经摒弃了Segment 的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后对synchronized 锁做了很多优化）整个看起来就像是优化过且线程安全的HashMap，虽然在 JDK1.8 中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本；② Hashtable(同一把锁) :使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用put添加元素，另一个线程不能使用put添加元素，也不能使用get，竞争会越来越激烈效率越低。

### ConcurrentHashMap的size如何计算
答： 1.7 中是先不加锁计算三次，如果三次结果不一样在加锁。
JDK1.8 size 是通过对 baseCount 和 counterCell 进行 CAS 计算，最终通过 baseCount 和 遍历 CounterCell 数组得出 size。
JDK 8 推荐使用mappingCount 方法，因为这个方法的返回值是 long 类型，不会因为 size 方法是 int 类型限制最大值。

### LinkedHashMap如何在o(1)的时间里面删除一个元素
答：

### List遍历删除元素
答：
1. 使用增强for循环，如果在某次循环的过程中进行了删除或者是增加元素的操作，使用break或者return语句结束了循环操作，不报异常，否则必报ConcurrentModificationException异常。
2. 使用普通for循环，但是每次删除后注意将角标减一。
3. 使用迭代器，

```
//准备数据
        List<Student> list = new ArrayList<>();
        list.add(new Student("male"));
        list.add(new Student("female"));
        list.add(new Student("female"));
        list.add(new Student("male"));
 
        //遍历删除,除去男生
        Iterator<Student> iterator = list.iterator();
        while (iterator.hasNext()) {
            Student student = iterator.next();
            if ("male".equals(student.getGender())) {
                iterator.remove();//使用迭代器的删除方法删除
            }
        }
```


### 快速失败（fail-fast）、安全失败（fail-safe）
答：
- 快速失败（fail-fast），在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。

原理：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。

注意：这里异常的抛出条件是检测到 modCount！=expectedmodCount 这个条件。如果集合发生变化时修改modCount值刚好又设置为了expectedmodCount值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测并发修改的bug。

场景：java.util包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改）。

避免fail-fast的方法：1）在单线程的遍历过程中，如果要进行remove操作，可以调用迭代器的remove方法而不是集合类的remove方法。2）使用fail-safe机制，使用java并发包(java.util.concurrent)中的CopyOnWriterArrayList类来代替ArrayList，使用 ConcurrentHashMap来代替hashMap。

- 安全失败（fail-safe），  采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。

原理：由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发Concurrent Modification Exception。

缺点：基于拷贝内容的优点是避免了Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。

场景：java.util.concurrent包下的容器都是安全失败，可以在多线程下并发使用，并发修改。

### CopyOnWrite
答：CopyOnWrite容器即写时复制的容器。当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

应用场景：CopyOnWrite并发容器用于读多写少的并发场景。比如白名单，黑名单等。

### CopyOnWriteArrayList
答：CopyOnWriteArrayList是ArrayList的线程安全版本，CopyOnWriteArrayList是在有写操作的时候会copy一份数据，然后写完再设置成新的数据。添加的时候是需要加锁的，否则多线程写的时候会Copy出N个副本出来。读的时候不需要加锁，如果读的时候有多个线程正在向CopyOnWriteArrayList添加数据，读还是会读到旧的数据，因为写的时候不会锁住旧的CopyOnWriteArrayList。

### HashMap遍历时删除元素
答：hashmap遍历，最常用的方法是foreach和迭代器，但是遍历删除则用迭代器。注意：迭代器遍历时，每一次调用 next() 函数，至多只能对容器修改一次

### 排序操作

```
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```

### 查找,替换操作

```
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素。
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target).
boolean replaceAll(List list, Object oldVal, Object newVal), 用新元素替换旧元素
```

### 同步控制
Collections 提供了多个synchronizedXxx()方法·，该方法可以将指定集合包装成线程同步的集合，从而解决多线程并发访问集合时的线程安全问题。我们知道HashSet，TreeSet，ArrayList,LinkedList,HashMap,TreeMap 都是线程不安全的。Collections提供了多个静态方法可以把他们包装成线程同步的集合。
最好不要用下面这些方法，效率非常低，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合。
方法如下：
```
synchronizedCollection(Collection<T>  c) //返回指定 collection 支持的同步（线程安全的）collection。
synchronizedList(List<T> list)//返回指定列表支持的同步（线程安全的）List。
synchronizedMap(Map<K,V> m) //返回由指定映射支持的同步（线程安全的）Map。
synchronizedSet(Set<T> s) //返回指定 set 支持的同步（线程安全的）set。
```