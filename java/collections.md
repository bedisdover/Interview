# 5.2 集合类

## 1. List

### 1.1 ArrayList

以数组实现。节约空间，但数组有容量限制。超出限制时会增加50%容量，用 `System.arraycopy()` 复制到新的数组。因此最好能给出数组大小的预估值。默认第一次插入元素时创建大小为10的数组。 

按数组下标访问元素 － get(i)、set(i, e) 的性能很高，这是数组的基本优势。

如果按下标插入元素、删除元素 － add(i,e)、 remove(i)、remove(e)，则要用 `System.arraycopy()` 来复制移动部分受影响的元素，性能就变差了。

越是前面的元素，修改时要移动的元素越多。直接在数组末尾加入元素－常用的add(e)，删除最后一个元素则无影响。

### 1.2 LinkedList

以双向链表实现。链表无容量限制，但双向链表本身使用了更多空间，每插入一个元素都要构造一个额外的 Node 对象，也需要额外的链表指针操作。

按下标访问元素 － get(i)、set(i, e) 要悲剧的部分遍历链表将指针移动到位 (如果i > 数组大小的一半，会从末尾移起)。

插入、删除元素时修改前后节点的指针即可，不再需要复制移动。但还是要部分遍历链表的指针才能移动到下标所指的位置。

只有在链表两头的操作 － add()、addFirst()、removeLast()或用iterator()上的remove()倒能省掉指针的移动。

### 1.3 CopyOnWriteArrayList

并发优化的ArrayList。基于不可变对象策略，在修改时先复制出一个数组快照来修改，改好了，再让内部指针指向新数组。

因为对快照的修改对读操作来说不可见，所以读读之间不互斥，读写之间也不互斥，只有写写之间要加锁互斥。但复制快照的成本昂贵，典型的适合读多写少的场景。

虽然增加了 `addIfAbsent(e)` 方法，会遍历数组来检查元素是否已存在，性能可想像的不会太好。


## 2. Map

### 2.1 HashMap

以 Entry 数组实现的哈希桶数组，用Key的哈希值取模桶数组的大小可得到数组下标。

插入元素时，如果两条Key落在同一个桶(比如哈希值1和17取模16后都属于第一个哈希桶)，我们称之为哈希冲突。

JDK的做法是链表法，Entry用一个next属性实现多个Entry以单向链表存放。查找哈希值为17的key时，先定位到哈希桶，然后链表遍历桶里所有元素，逐个比较其Hash值然后key值。

在JDK8里，新增默认为8的阈值，当一个桶里的Entry超过閥值，就不以单向链表而以红黑树来存放以加快Key的查找速度。

当然，最好还是桶里只有一个元素，不用去比较。所以默认当Entry数量达到桶数量的75%时，哈希冲突已比较严重，就会成倍扩容桶数组，并重新分配所有原来的Entry。扩容成本不低，所以也最好有个预估值。

取模用与操作(hash & (arrayLength - 1))会比较快，所以数组的大小永远是2的N次方， 你随便给一个初始值比如17会转为32。默认第一次放入元素时的初始值是16。

### 2.2 LinkedHashMap

扩展HashMap，每个Entry增加双向链表，号称是最占内存的数据结构。

支持iterator()时按Entry的插入顺序来排序(如果设置accessOrder属性为true，则所有读写访问都排序)。

插入时，Entry把自己加到Header Entry的前面去。如果所有读写访问都要排序，还要把前后Entry的before/after拼接起来以在链表中删除掉自己，所以此时读操作也是线程不安全的了。

iterator()时顺着哈希桶数组来遍历，看起来是个乱序。

### 2.3 TreeMap

以红黑树实现，红黑树又叫自平衡二叉树：

```
对于任一节点而言，其到叶节点的每一条路径都包含相同数目的黑结点。
```

上面的规定，使得树的层数不会差的太远，使得所有操作的复杂度不超过 O(lgn)，但也使得插入，修改时要复杂的左旋右旋来保持树的平衡。

支持iterator()时按Key值排序，可按实现了Comparable接口的Key的升序排序，或由传入的Comparator控制。可想象的，在树上插入/删除元素的代价一定比HashMap的大。

支持SortedMap接口，如firstKey()，lastKey()取得最大最小的key，或sub(fromKey, toKey), tailMap(fromKey)剪取Map的某一段。

### 2.4 HashTable

HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap可以接受为null的键(key)和值(value)，而Hashtable则不行)。

HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；

由于Hashtable是线程安全的也是synchronized，所以在单线程环境下它比HashMap要慢。

### 2.5 ConcurrentHashMap

并发优化的HashMap。

在JDK5里的经典设计，默认16把写锁(可以设置更多)，有效分散了阻塞的概率。数据结构为Segment[]，每个Segment一把锁。Segment里面才是哈希桶数组。Key先算出它在哪个Segment里，再去算它在哪个哈希桶里。

也没有读锁，因为put/remove动作是个原子动作(比如put的整个过程是一个对数组元素/Entry 指针的赋值操作)，读操作不会看到一个更新动作的中间状态。

但在JDK8里，Segment[]的设计被抛弃了，改为精心设计的，只在需要锁的时候加锁。


## 3. Set

所有Set几乎都是内部用一个Map来实现, 因为Map里的KeySet就是一个Set，而value是假值，全部使用同一个Object即可。

Set的特征也继承了那些内部的Map实现的特征。

HashSet：内部是HashMap。

LinkedHashSet：内部是LinkedHashMap。

TreeSet：内部是TreeMap的SortedSet。

ConcurrentSkipListSet：内部是ConcurrentSkipListMap的并发优化的SortedSet。

CopyOnWriteArraySet：内部是CopyOnWriteArrayList的并发优化的Set，利用其addIfAbsent()方法实现元素去重，如前所述该方法的性能很一般。


## 4. Queue

Queue是在两端出入的List，所以也可以用数组或链表来实现。

### 4.1 LinkedList

以双向链表实现的LinkedList既是List，也是Queue。

### 4.2 ArrayDeque

以循环数组实现的双向Queue。大小是2的倍数，默认是16。

为了支持FIFO，即从数组尾压入元素(快)，从数组头取出元素(超慢)，就不能再使用普通ArrayList的实现了，改为使用循环数组。

有队头队尾两个下标：弹出元素时，队头下标递增；加入元素时，队尾下标递增。如果加入元素时已到数组空间的末尾，则将元素赋值到数组[0]，同时队尾下标指向0，再插入下一个元素则赋值到数组[1]，队尾下标指向1。如果队尾的下标追上队头，说明数组所有空间已用完，进行双倍的数组扩容。

### 4.3 PriorityQueue

用平衡二叉最小堆实现的优先级队列，不再是FIFO，而是按元素实现的Comparable接口或传入Comparator的比较结果来出队，数值越小，优先级越高，越先出队。但是注意其iterator()的返回不会排序。

平衡最小二叉堆，用一个简单的数组即可表达，可以快速寻址，没有指针什么的。最小的在queue[0] ，比如queue[4]的两个孩子，会在queue[2 * *4+1] 和 queue[2 * (4+1)]，即queue[9]和queue[10]。

入队时，插入queue[size]，然后二叉地往上比较调整堆。

出队时，弹出queue[0]，然后把queque[size]拿出来二叉地往下比较调整堆。

初始大小为11，空间不够时自动50%扩容。

### 4.4 ConcurrentLinkedQueue/Deque

无界的并发优化的Queue，基于链表，实现了依赖于CAS的无锁算法。

ConcurrentLinkedQueue的结构是单向链表和head/tail两个指针，因为入队时需要修改队尾元素的next指针，以及修改tail指向新入队的元素两个CAS动作无法原子，所以需要的特殊的算法。

### 4.5 BlockingQueue

一来如果队列已空不用重复的查看是否有新数据而会阻塞在那里，二来队列的长度受限，用以保证生产者与消费者的速度不会相差太远。

