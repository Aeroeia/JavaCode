# Java集合

## 概念

### 数组与集合区别，用过哪些？

数组和集合的区别：

- 数组是固定长度的数据结构，一旦创建长度就无法改变，而集合是==动态长度==的数据结构，可以根据需要动态增加或减少元素。
- 数组可以包含基本数据类型和对象，而集合==只能包含对象==。
- 数组可以直接访问元素，而集合需要==通过迭代器==或其他==方法==访问元素。

我用过的一些 Java 集合类：

1. **ArrayList：** 动态数组，实现了List接口，支持动态增长。
2. **LinkedList：** 双向链表，也实现了List接口，支持快速的插入和删除操作。
3. **HashMap：** 基于哈希表的Map实现，存储键值对，通过键快速查找值。
4. **HashSet：** 基于HashMap实现的Set集合，用于存储唯一元素。
5. **TreeMap：** 基于红黑树实现的有序Map集合，可以按照键的顺序进行排序。
6. **LinkedHashMap：** 基于哈希表和双向链表实现的Map集合，保持插入顺序或访问顺序。
7. **PriorityQueue：** 优先队列，可以按照比较器或元素的自然顺序进行排序。

------

### 说说Java中的集合？

![image-20250820131943027](assets/image-20250820131943027.png)

List是有序的Collection，使用此接口能够精确的控制每个元素的插入位置，用户能根据索引访问List中元素。常用的实现List的类有==LinkedList，ArrayList，Vector，Stack==。

- ArrayList是容量可变的==非线程安全==列表，其底层使用数组实现。当几何扩容时，会创建更大的数组，并把原数组复制到新数组。ArrayList支持对元素的快速随机访问，但插入与删除速度很慢。
- LinkedList本质是一个==双向链表==，与ArrayList相比，，其插入和删除速度更快，但随机访问速度更慢。

Set不允许存在重复的元素，与List不同，set中的元素是无序的。常用的实现有HashSet，LinkedHashSet和TreeSet。

- HashSet==通过HashMap实现==，HashMap的Key即HashSet存储的元素，所有Key都是用相同的Value，一个名为PRESENT的Object类型常量。使用Key保证元素唯一性，但不保证有序性。由于HashSet是HashMap实现的，因此线程不安全。
- LinkedHashSet==继承自HashSet==，通过==LinkedHashMap==实现，使用双向链表维护元素插入顺序。
- TreeSet通过==TreeMap==实现的，添加元素到集合时按照比较规则将其插入合适的位置，保证插入后的集合仍然有序。

Map 是一个键值对集合，存储键、值和之间的映射。Key 无序，唯一；value 不要求有序，允许重复。==Map 没有继承于 Collection 接口==，从 Map 集合中检索元素时，只要给出键对象，就会返回对应的值对象。主要实现有TreeMap、HashMap、HashTable、LinkedHashMap、ConcurrentHashMap

- HashMap：JDK1.8 之前 HashMap 由==数组+链表==组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突），JDK1.8 以后在解决哈希冲突时有了较大的变化==，当链表长度大于阈值（默认为 8）==时，将链表转化为==红黑树==，以减少搜索时间
- LinkedHashMap：LinkedHashMap 继承自 ==HashMap==，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，==增加了一条双向链表==，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
- HashTable：数组+链表组成的，数组是 HashTable 的主体，链表则是主要为了解决哈希冲突而存在的
- TreeMap：红黑树（自平衡的排序二叉树）
- ConcurrentHashMap：Node数组+链表+红黑树实现，线程安全的（jdk1.8以前Segment锁，1.8以后volatile + CAS 或者 synchronized）

------

### 讲讲CAS

#### 1️⃣ 什么是 CAS？

CAS 全称是 **Compare And Swap**（比较并交换）。
 它是一种 **乐观锁** 思想的实现方式，广泛用于并发编程里保证线程安全。

简单说：

- 你有一个变量的值（比如 `10`）。
- 线程想更新它时，先检查它的当前值是不是期望的值（比如还等于 `10`）。
- 如果是，就把它更新为新值（比如 `11`）；如果不是，就说明在这期间有其他线程修改过了，更新失败，需要重试。

------

#### 2️⃣ 为什么要有 CAS？

在多线程里：

- 如果用 **synchronized**，会加锁 → 阻塞，性能差。
- CAS 提供了一种 **无锁并发** 机制，不需要线程阻塞，就能实现安全更新。

------

#### 3️⃣ CAS 的流程（举例）

假设一个变量 `count = 10`，线程要做 `count++` 操作。

1. **读取当前值**：线程先读取到 `10`。
2. **比较（Compare）**：检查内存里 `count` 现在是不是 `10`。
3. **交换（Swap）**：如果是，就把它更新为 `11`；如果不是，就说明被别人改过了，不更新。

如果失败 → 线程重新读取，继续尝试，直到成功。

------

#### 4️⃣ Java 中的 CAS

在 Java 里，CAS 由 **`Unsafe` 类** 和 **CPU 的==原子指令==**（如 `cmpxchg`）实现。

典型用法：

```java
import java.util.concurrent.atomic.AtomicInteger;

public class CASDemo {
    public static void main(String[] args) {
        AtomicInteger count = new AtomicInteger(10);

        // 期望值=10，更新值=11
        boolean success = count.compareAndSet(10, 11);

        System.out.println("更新成功了吗？ " + success); // true
        System.out.println("当前值： " + count.get()); // 11
    }
}
```

这里 `compareAndSet(10, 11)` 就是 CAS 的过程。

------

#### 5️⃣ CAS 的问题

虽然很高效，但 CAS 也有缺点：

1. **ABA 问题**
   - 比如一个值从 `A → B → A`，CAS 检查时发现还是 `A`，以为没变，其实中间被修改过。
   - 解决方法：加版本号（`AtomicStampedReference`）。
2. **自旋开销大**
   - 如果竞争激烈，CAS 可能会一直失败 → 一直重试 → 消耗 CPU。
3. **只能保证单个变量的原子操作**
   - 多变量的原子操作就需要锁了。

------

#### 6️⃣ 总结

- **CAS** = 无锁并发的一种实现 → "比较+交换"。
- 核心 API：`AtomicInteger`、`compareAndSet`。
- 优点：高效、非阻塞。
- 缺点：有 ABA 问题、自旋开销大。

------

###  ==Java中的线程安全的集合是什么？ 待仔细研究底层==

在 ==java.util 包==中的线程安全的类主要 2 个，其他都是非线程安全的。

- **Vector**：线程安全的==动态数组==，其内部方法基本都经过==synchronized==修饰，如果不需要线程安全，并不建议选择，毕竟同步是有额外开销的。Vector 内部是使用对象数组来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据。
- **Hashtable**：线程安全的==哈希表==，HashTable 的加锁方法是给每个方法加上 ==synchronized== 关键字，这样锁住的是整个 Table 对象，不支持 null 键和值，由于同步导致的性能开销，所以已经很少被推荐使用，如果要保证线程安全的哈希表，可以用ConcurrentHashMap。

java.util.concurrent 包提供的都是线程安全的集合：

并发Map：

- **ConcurrentHashMap**：它与 HashTable 的主要区别是二者加锁粒度的不同，在**JDK1.7**，ConcurrentHashMap加的是分段锁，也就是Segment锁，每个Segment 含有整个 table 的一部分，这样不同分段之间的并发操作就互不影响。在**JDK 1.8** ，它取消了Segment字段，直接在==table元素上加锁，实现对每一行进行加锁==，进一步减小了并发冲突的概率。对于put操作，如果Key对应的数组元素为null，则==通过CAS==操作（Compare and Swap）将其设置为当前值。如果Key对应的数组元素（也即链表表头或者树的根元素）不为null，则对该元素使用 synchronized 关键字申请锁，然后进行操作。如果该 put 操作使得当前链表长度超过一定阈值，则将==该链表转换为红黑树==，从而提高寻址效率。

  | 特性     | JDK 1.7                    | JDK 1.8+                               |
  | -------- | -------------------------- | -------------------------------------- |
  | 数据结构 | Segment + HashEntry        | Node[] + 链表/红黑树                   |
  | 写操作   | Segment 锁                 | CAS（空桶）+ 局部锁（冲突桶）          |
  | 读操作   | 无锁或部分 volatile        | 无锁读取，依赖 volatile 可见性         |
  | 扩容     | Segment 内部扩容           | 整个 table 扩容 + volatile 替换        |
  | 并发性能 | Segment 粒度锁，高并发受限 | 空桶 CAS 无锁，桶冲突锁局部 → 性能更高 |

- **ConcurrentSkipListMap**：实现了一个基于==SkipList（跳表）==算法的==可排序==的并发集合，SkipList是一种可以在对数预期时间内完成搜索、插入、删除等操作的数据结构，通过维护多个指向其他元素的“跳跃”链接来实现高效查找。==无锁 + CAS + volatile==

并发Set：

- **ConcurrentSkipListSet**：是线程安全的有序的集合。底层是使用==ConcurrentSkipListMap==实现。
- **CopyOnWriteArraySet**：是线程安全的Set实现，它是线程安全的==无序==的集合，可以将它理解成线程安全的HashSet。有意思的是，CopyOnWriteArraySet和HashSet虽然都继承于共同的父类AbstractSet；但是，HashSet是通过“散列表”实现的，而CopyOnWriteArraySet则是通过“==动态数组==(CopyOnWriteArrayList)”实现的，并不是散列表。

并发List：

- **CopyOnWriteArrayList**：它是 ArrayList 的线程安全的变体，其中所有写操作（add，set等）都通过对底层数组进行==全新复制==来实现，允许存储 null 元素。即当对象进行写操作时，使用了==Lock锁==做同步处理，内部拷贝了原数组，并在新数组上进行添加操作，最后将新数组替换掉旧数组；若进行的读操作，则直接返回结果，操作过程中不需要进行同步。

并发 Queue：

- **ConcurrentLinkedQueue**：是一个适用于高并发场景下的队列，它通过==无锁的方式(CAS)==，实现了高并发状态下的高性能。通常，ConcurrentLinkedQueue 的性能要好于 BlockingQueue 。
- **BlockingQueue**：与 ConcurrentLinkedQueue 的使用场景不同，BlockingQueue 的主要功能并不是在于提升高并发时的队列性能，而在于简化多线程间的数据共享。BlockingQueue 提供一种读写==阻塞==等待的机制，即如果消费者速度较快，则 BlockingQueue 则可能被清空，此时消费线程再试图从 BlockingQueue 读取数据时就会被阻塞。反之，如果生产线程较快，则 BlockingQueue 可能会被装满，此时，生产线程再试图向 BlockingQueue 队列装入数据时，便会被阻塞等待。==ReentrantLock==

并发 Deque：

- **LinkedBlockingDeque**：是一个线程安全的双端队列实现。它的内部使用链表结构，每一个节点都维护了一个前驱节点和一个后驱节点。LinkedBlockingDeque 没有进行读写锁的分离，因此==同一时间只能有一个线程对其进行操作==
- **ConcurrentLinkedDeque**：ConcurrentLinkedDeque是一种基于链接节点的无限并发链表。可以安全地并发执行插入、删除和访问操作。当许多线程同时访问一个公共集合时，ConcurrentLinkedDeque是一个合适的选择。==靠CAS==

------

### 🚨多线程下集合不安全体现在

---

#### 1. **元素覆盖（数据丢失）**

- 多个线程同时 `add` 元素，底层数组在写入时没有同步控制。
- 可能导致两个线程在**同一个数组下标**位置写数据，后写的覆盖先写的。
- 表现：集合里元素变少，某些数据丢了。

------

#### 2. **size 计数不一致**

- 线程 A 和线程 B 同时插入数据：
  - A 读到 `size = 9`，准备写入。
  - B 也读到 `size = 9`，也准备写入。
  - 最终两者都写成功，但 `size` 只加了一次，结果实际有 2 个元素被加进去了，但 `size` 只显示 +1。
- 表现：`size` 小于真实元素数量。

------

#### 3. **扩容时的数据错乱**

- 当集合容量不足需要扩容（如 `ArrayList` 会 `new` 一个更大的数组然后 `System.arraycopy`），
- 如果多个线程同时触发扩容，可能导致：
  - 部分元素丢失；
  - 新数组和旧数组之间数据不完整；
  - `NullPointerException` 或数组越界。

这个问题问得特别好 👍，我们来一步步还原一下 **集合扩容时在多线程环境下为什么会出错**。

------

**🚗 背景：ArrayList 扩容机制**

以 `ArrayList` 为例：
 当 `add()` 时发现容量不足，会触发扩容：

1. 计算新容量（通常是 `oldCapacity + oldCapacity >> 1` → 1.5 倍）。
2. `new Object[newCapacity]` 新建一个更大的数组。
3. `System.arraycopy` 把旧数组的数据复制到新数组。
4. 把 `elementData` 引用指向新数组。

这一整套流程，本质是 **“读旧数组 → 创建新数组 → 拷贝数据 → 替换引用”**。
 这个过程并不是原子操作，多线程下很容易出错。

------

**⚡ 出错场景举例**

##### 3. 1 **部分元素丢失**

- 线程 A 和线程 B 同时发现需要扩容。
- 线程 A 先创建了一个新数组并拷贝旧数据（比如 0..9 下标的数据）。
- 线程 B 也基于旧数组（扩容前的）创建了新数组并拷贝数据。
- 最后可能 **B 的新数组覆盖了 A 的结果**。
  - 如果 A 在复制过程中已经写入了某些新增元素，B 并没有复制到，就丢了。

------

##### 3.2  **新数组和旧数组数据不完整**

- 线程 A 正在 `System.arraycopy` 时，线程 B 把 `elementData` 改成了它的新数组。
- A 复制了一半，结果 `elementData` 已经被别的线程替换掉了，后续写入的不是它以为的目标数组。
- 导致 **新数组里有一部分是旧数据，一部分是空/null**。

```
线程 A：开始扩容，记下当时的 size = 100，准备拷贝 100 个元素。

线程 A：拷贝到第 50 个元素时，被挂起。

线程 B：在这期间，往集合里加了 50 个新元素 → size = 150，触发扩容并完成，elementData 被替换成了 B 的新数组（里面有 150 个元素）。

线程 A：恢复执行，它继续从 elementData[50] 拷到 elementData[99]，最后完成并做 elementData = newArrayA。

结果：

A 的新数组只有 100 个元素（因为它当时看到的 size 就是 100）。

后来 B 新增的 50 个元素在 A 的数组里根本不存在 → 直接丢失。

于是最终 elementData 指向了 A 的数组，看起来像 “一半是数据，一半是 null/丢了”。
```

------

##### 3.3  **NullPointerException 或数组越界**

- 线程 A 读到 `size = 10`，准备在下标 10 插入元素。
- 扩容还没来得及做完，线程 B 改了 `elementData`，但 A 还在用旧数组插入。
- 可能导致：
  - 插入越界（`IndexOutOfBoundsException`）。
  - 如果引用错乱，`elementData` 变成 `null` 或部分未初始化区域，就可能 `NullPointerException`。

```
线程 A看到的 size 和容量是旧快照，它的扩容只是针对“当时的需求”

其他线程在 A 扩容期间又新增了很多元素，触发了自己的扩容 → 需要比 A 多得多的容量

当这些线程写入元素时，A 的新数组容量已经不够用了 → 就可能出现 IndexOutOfBoundsException

同时，A 的数组里未拷贝/未初始化的位置就是 null → 如果访问就可能 NullPointerException
```

------

#### 4. **迭代时结构被修改**

- 线程 A 正在 `for-each` 或 `Iterator` 遍历，线程 B 在 `add/remove`。
- 因为 `modCount` 没有同步更新，导致抛 `ConcurrentModificationException`（fail-fast 机制）。
- 这就是「遍历时修改」的典型问题。

------

### Volatile

- **可见性**：保证不同线程读写 volatile 变量时，操作会刷新到主内存，读到的是最新值。
- **防止指令重排**：volatile 的读写操作在编译器和 CPU 层面都有内存屏障，保证顺序不会被重排。
- 不加volatile可能会导致由于jvm优化推断出flag 没在这个线程中被修改 把 flag 缓存在一个寄存器或 CPU 的工作寄存区里而读不到新值

虽然硬件层有MESI（缓存一致性协议）保证一致 但是是通过StoreBuffer进行接受invalid指令并ack的 所以会有短暂不一致问题 但写入线程会立即看到新值 因为看到storebuffer有值会优先读buffer中的值

> 1. **CPU 发出写请求**；
> 2. **写入先放入本核心的 Store Buffer（写缓冲区）** → **此时写操作对本核心“看似完成”**；
> 3. 同时，CPU 向其他核心**发送 Invalidate 消息**（因为要修改这个缓存行）；
> 4. **等待其他核心确认 Invalidate**（即它们的缓存行变 Invalid）；
> 5. **收到所有确认后，才将 Store Buffer 中的数据写入本地缓存行（Cache Line）**；
> 6. **此时，MESI 状态变为 Modified，其他核心的副本已失效**。

------

## List

![image-20250822084719290](assets/image-20250822084719290.png)

常见的List集合（==非线程安全==）：

- `ArrayList`基于==动态数组==实现，它允许快速的随机访问，即通过索引访问元素的时间复杂度为 O (1)。在添加和删除元素时，如果操作位置不是列表末尾，可能需要移动大量元素，性能相对较低。适用于需要频繁随机访问元素，而对插入和删除操作性能要求不高的场景，如数据的查询和展示等。
- `LinkedList`基于==双向链表==实现，在插入和删除元素时，只需修改链表的指针，不需要移动大量元素，时间复杂度为 O (1)。但随机访问元素时，需要从链表头或链表尾开始遍历，时间复杂度为 O (n)。适用于需要频繁进行插入和删除操作的场景，如队列、栈等数据结构的实现，以及需要在列表中间频繁插入和删除元素的情况。

常见的List集合（==线程安全==）：

- `Vector`和`ArrayList`类似，也是基于==数组==实现。`Vector`中的方法大多是同步的，这使得它在多线程环境下可以保证数据的一致性，但在单线程环境下，由于同步带来的开销，性能会略低于`ArrayList`。
- `CopyOnWriteArrayList`在对列表进行修改（如添加、删除元素）时，会==创建一个新的底层数组，将修改操作应用到新数组上==，而读操作仍然在原数组上进行，这样可以保证读操作不会被写操作阻塞，实现了==读写分离==，提高了并发性能。适用于读操作远远多于写操作的并发场景，如事件监听列表等，在这种场景下可以避免大量的锁竞争，提高系统的性能和响应速度。

###  讲一下java里面list的几种实现，几种实现有什么不同？

![image-20250822085353342](assets/image-20250822085353342.png)

- Vector 是 Java 早期提供的线程安全的动态数组，如果不需要线程安全，并不建议选择，毕竟同步是有额外开销的。Vector 内部是使用==对象数组==来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据。

- ArrayList 是应用更加广泛的==动态数组==实现，它本身不是线程安全的，所以==性能要好==很多。与 Vector 近似，ArrayList 也是可以根据需要调整容量，不过两者的调整逻辑有所区别，Vector 在扩容时会提高 1 倍，而 ArrayList 则是==增加 50%==。

- LinkedList 顾名思义是 Java 提供的==双向链表==，所以它不需要像上面两种那样调整容量，它也不是线程安全的。

- Collections.synchronizedList(list) 加锁版ArrayList和Vector类似

- COW写时复制主要是不希望在遍历的时候读取到不一样的状态 这也是为什么不能只加锁而不写时复制的原因还有就是访问越界问题

  > - 线程 A 执行 `add("X")`，刚执行完 `elementData[5] = "X"`，**还没来得及 `size++`**；
  > - 线程 B 调用 `get(5)`，**读到了 "X"**；
  > - 但此时 `size` 还是 5，按逻辑索引 5 应该是非法的（有效索引是 0~4）！
  >
  > 更糟的是，如果线程 B 先读 `size`（比如用于遍历），再读 `elementData[i]`，可能：
  >
  > - 读到 `size = 5`；
  > - 但在读 `elementData[4]` 时，该位置还未写入（或正在被修改），**读到 `null` 或旧值**。

------

### list可以一边遍历一边修改元素吗？

普通集合遍历中 **不能直接修改结构**，否则会报`ConcurrentModificationException`异常。

使用 **Iterator** 可以安全删除当前元素；**ListIterator** 可以修改或添加元素。

使用 **并发集合** 可以在遍历时修改，但行为可能不是完全同步可见。看到的是**遍历开始时的快照**

------

###  list如何快速删除某个指定下标的元素？

`ArrayList`提供了`remove(int index)`方法来删除指定下标的元素，该方法在删除元素后，会将后续元素向前移动，以填补被删除元素的位置。如果删除的是列表末尾的元素，==时间复杂度为 O (1)==；如果删除的是列表中间的元素，==时间复杂度为 O (n)==，n 为列表中元素的个数，因为==需要移动后续的元素==。示例代码如下：

```java
import java.util.ArrayList;
import java.util.List;

public class ArrayListRemoveExample {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        // 删除下标为1的元素
        list.remove(1);

        System.out.println(list);
    }
}
```

`LinkedList`的`remove(int index)`方法也可以用来删除指定下标的元素。它需要先==遍历到指定下标位置，然后修改链表的指针来删除元素==。时间复杂度为 ==O (n)==，n 为要删除元素的下标。不过，如果已知要删除的元素是链表的头节点或尾节点，可以直接通过修改头指针或尾指针来实现删除，时间复杂度为 ==O (1)==。示例代码如下：

```java
import java.util.LinkedList;
import java.util.List;

public class LinkedListRemoveExample {
    public static void main(String[] args) {
        List<Integer> list = new LinkedList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        // 删除下标为1的元素
        list.remove(1);

        System.out.println(list);
    }
}
```

`CopyOnWriteArrayList`的`remove`方法同样可以删除指定下标的元素。由于`CopyOnWriteArrayList`在写操作时会创建一个新的数组，所以删除操作的时间复杂度==取决于数组的复制速度==，通常为 O (n)，n 为数组的长度。但在并发环境下，它的删除操作不会影响读操作，具有较好的并发性能。示例代码如下：

```java
import java.util.concurrent.CopyOnWriteArrayList;

public class CopyOnWriteArrayListRemoveExample {
    public static void main(String[] args) {
        CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);

        // 删除下标为1的元素
        list.remove(1);

        System.out.println(list);
    }
}
```

### ArrayList线程安全吗？把ArrayList变成线程安全有哪些方法？

不是线程安全的，ArrayList变成线程安全的方式有：

- 使用Collections类的synchronizedList方法将ArrayList包装成线程安全的List：

```java
List<String> synchronizedList = Collections.synchronizedList(arrayList);
```

- 使用CopyOnWriteArrayList类代替ArrayList，它是一个线程安全的List实现：

```java
CopyOnWriteArrayList<String> copyOnWriteArrayList = new CopyOnWriteArrayList<>(arrayList);
```

- 使用Vector类代替ArrayList，Vector是线程安全的List实现：

```java
Vector<String> vector = new Vector<>(arrayList);
```

------

### ArrayList的扩容机制说一下

ArrayList在添加元素时，如果当前元素个数已经达到了内部数组的容量上限，就会触发扩容操作。ArrayList的扩容操作主要包括以下几个步骤：

- 计算新的容量：一般情况下，新的容量会扩大为原容量的==1.5倍==（在JDK 10之后，扩容策略做了调整），然后检查是否超过了最大容量限制。
- 创建新的数组：根据计算得到的新容量，创建一个新的更大的数组。
- 将元素复制：将原来数组中的元素逐个==复制==到新数组中。
- 更新引用：将ArrayList内部指向原数组的引用==指向新数组==。
- 完成扩容：扩容完成后，可以继续添加新元素。

ArrayList的扩容操作涉及到数组的复制和内存的重新分配，所以在频繁添加大量元素时，扩容操作可能会影响性能。为了减少扩容带来的性能损耗，可以在初始化ArrayList时预分配足够大的容量，避免频繁触发扩容操作。

之所以扩容是 1.5 倍，是因为 1.5 可以充分利用移位操作，减少浮点数或者运算时间和运算次数。

```java
// 新容量计算
int newCapacity = oldCapacity + (oldCapacity >> 1);
```

------

### 线程安全的 List， CopyonWriteArraylist是如何实现线程安全的

CopyOnWriteArrayList底层也是通过一个数组保存数据，使用volatile关键字修饰数组，保证当前线程对数组对象重新赋值后，==其他线程可以及时感知到==。

```java
private transient volatile Object[] array;
```

在写入操作时，加了一把互斥锁ReentrantLock以保证线程安全。

```java
public boolean add(E e) {
    //获取锁
    final ReentrantLock lock = this.lock;
    //加锁
    lock.lock();
    try {
        //获取到当前List集合保存数据的数组
        Object[] elements = getArray();
        //获取该数组的长度（这是一个伏笔，同时len也是新数组的最后一个元素的索引值）
        int len = elements.length;
        //将当前数组拷贝一份的同时，让其长度加1
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        //将加入的元素放在新数组最后一位，len不是旧数组长度吗，为什么现在用它当成新数组的最后一个元素的下标？建议自行画图推演，就很容易理解。
        newElements[len] = e;
        //替换引用，将数组的引用指向给新数组的地址
        setArray(newElements);
        return true;
    } finally {
        //释放锁
        lock.unlock();
    }
}
```

看到源码可以知道写入新元素时，首先会先==将原来的数组拷贝一份并且让原来数组的长度+1后就得到了一个新数组==，新数组里的元素和旧数组的元素一样并且长度比旧数组多一个长度，然后将新加入的元素放置都在新数组最后一个位置后，用新数组的地址替换掉老数组的地址就能得到最新的数据了。

在我们执行替换地址操作之前，读取的是老数组的数据，数据是有效数据；执行替换地址操作之后，读取的是新数组的数据，同样也是有效数据，而且使用该方式能比读写都加锁要更加的效率。

现在我们来看读操作，==读是没有加锁的，所以读是一直都能读==

```java
public E get(int index) {
    return get(getArray(), index);
}
```

------

## Map

![image-20250823141520072](assets/image-20250823141520072.png)

常见的Map集合（非线程安全）：

- `HashMap`是基于哈希表实现的`Map`，它根据键的哈希值来存储和获取键值对，JDK 1.8中是用==数组+链表+红黑树==来实现的。`HashMap`是非线程安全的，在多线程环境下，当多个线程同时对`HashMap`进行操作时，可能会导致数据不一致或出现死循环等问题。比如在扩容时，多个线程可能会同时修改哈希表的结构，从而破坏数据的完整性。
- `LinkedHashMap`继承自`HashMap`，它在`HashMap`的基础上，使用==双向链表维护了键值对的插入顺序==或访问顺序，使得==迭代顺序与插入顺序或访问顺序一致==。由于它继承自`HashMap`，在多线程并发访问时，同样会出现与`HashMap`类似的线程安全问题。
- `TreeMap`是基于==红黑树==实现的`Map`，它可以对键进行排序，默认按照自然顺序排序，也可以通过指定的比较器进行排序。`TreeMap`是非线程安全的，在多线程环境下，如果多个线程同时对`TreeMap`进行插入、删除等操作，可能会破坏红黑树的结构，导致数据不一致或程序出现异常。

常见的Map集合（线程安全）：

- `Hashtable`是早期 Java 提供的线程安全的`Map`实现，它的实现方式与`HashMap`类似，但在方法上使用了`synchronized`关键字来保证线程安全。通过在每个可能修改`Hashtable`状态的方法上加上`synchronized`关键字，使得在同一时刻，只能有一个线程能够访问`Hashtable`的这些方法，从而保证了线程安全。
- `ConcurrentHashMap`在 JDK 1.8 以前采用了==分段锁==等技术来提高并发性能。在`ConcurrentHashMap`中，将==数据分成多个段（Segment），每个段都有自己的锁==。在进行插入、删除等操作时，只需要获取相应段的锁，而不是整个`Map`的锁，这样可以允许多个线程同时访问不同的段，提高了并发访问的效率。在 JDK 1.8 以后是通过 ==volatile + CAS== 或者 synchronized 来保证线程安全的。

------

### HashMap实现原理介绍一下？

在 JDK 1.7 版本之前， HashMap 数据结构是数组和链表，HashMap通过==哈希算法将元素的键（Key）映射到数组中的槽位（Bucket）==。如果多个键映射到同一个槽位，它们会以链表的形式存储在同一个槽位上，因为链表的查询时间是O(n)，所以冲突很严重，一个索引上的链表非常长，效率就很低了。

![image-20250823161015917](assets/image-20250823161015917.png)

所以在 **JDK 1.8** 版本的时候做了优化，当一个==链表的长度超过8==的时候就转换数据结构，不再使用链表存储，而是使用==**红黑树**==，查找时使用红黑树，时间复杂度O（log n），可以提高查询性能，但是在数量较少时，即==数量小于6时，会将红黑树转换回链表==。

![image-20250823161053074](assets/image-20250823161053074.png)

------

### 哈希冲突解决方法有哪些？

- 链接法：使用链表或其他数据结构来存储冲突的键值对，将它们链接在同一个哈希桶中。

- 开放寻址法：在哈希表中找到另一个可用的位置来存储冲突的键值对，而不是存储在链表中。常见的开放寻址方法包括线性探测、二次探测和双重散列。

  | 方法     | 冲突时探测方式                  | 特点                     |
  | -------- | ------------------------------- | ------------------------ |
  | 线性探测 | +1、+2、+3...                   | 简单，主聚集严重         |
  | 二次探测 | +1²、+2²、+3²...                | 缓解主聚集，可能二次聚集 |
  | 双重散列 | +1*h2(k)、+2*h2(k)、+3*h2(k)... | 步长不同，聚集几乎消除   |

  💡 核心区别：

  - **线性探测** → 步长固定 1
  - **二次探测** → 步长随次数平方增长
  - **双重散列** → 步长由第二哈希函数计算，key 不同步长也不同

- 再哈希法（Rehashing）：当发生冲突时，使用另一个哈希函数再次计算键的哈希值，直到找到一个空槽来存储键值对。

- 哈希桶扩容：当哈希冲突过多时，可以动态地扩大哈希桶的数量，重新分配键值对，以减少冲突的概率。

------

### hashmap的put过程介绍一下

![image-20250824152449434](assets/image-20250824152449434.png)

HashMap HashMap的put()方法用于向HashMap中添加键值对，当调用HashMap的put()方法时，会按照以下详细流程执行（JDK8 1.8版本）：

> 第一步：根据要添加的键的哈希码计算在数组中的位置（索引）。

> 第二步：检查该位置是否为空（即没有键值对存在）

- 如果为空，则直接在该位置创建一个新的Entry对象来存储键值对。将要添加的键值对作为该Entry的键和值，并保存在数组的对应位置。将HashMap的修改次数（modCount）加1，以便在进行迭代时发现并发修改。

> 第三步：如果该位置已经存在其他键值对，检查该位置的第一个键值对的哈希码和键是否与要添加的键值对相同？

- 如果相同，则表示找到了相同的键，直接将新的值替换旧的值，完成更新操作。

> 第四步：如果第一个键值对的哈希码和键不相同，则需要遍历链表或红黑树来查找是否有相同的键：

如果键值对集合是链表结构，从链表的头部开始逐个比较键的哈希码和equals()方法，直到找到相同的键或达到链表末尾。

- 如果找到了相同的键，则使用新的值取代旧的值，即更新键对应的值。
- 如果没有找到相同的键，则将新的键值对添加到链表的头部。

如果键值对集合是红黑树结构，在红黑树中使用哈希码和equals()方法进行查找。根据键的哈希码，定位到红黑树中的某个节点，然后逐个比较键，直到找到相同的键或达到红黑树末尾。

- 如果找到了相同的键，则使用新的值取代旧的值，即更新键对应的值。
- 如果没有找到相同的键，则将新的键值对添加到红黑树中。

> 第五步：检查链表长度是否达到阈值（默认为8）：

- 如果链表长度超过==阈值==，且HashMap的数组长度大于等于64，则会将链表转换为红黑树，以提高查询效率。

> 第六步：检查负载因子是否超过阈值（默认为0.75）：

- 如果键值对的数量（size）与数组的长度的比值大于==阈值==，则需要进行扩容操作。

```
HashMap 的 threshold 是为了控制 什么时候扩容（跟 size >= capacity × loadFactor 有关）
int newCap = oldCap << 1;  // 即 oldCap * 2
有 threshold 的是基于哈希的容器（HashMap、HashSet），因为要平衡散列桶大小与元素数量。
```

> 第七步：扩容操作：

- 创建一个新的两倍大小的数组。
- 将旧数组中的键值对重新计算哈希码并分配到新数组中的位置。
- 更新HashMap的数组引用和阈值参数。

> 第八步：完成添加操作。

此外，HashMap是非线程安全的，如果在多线程环境下使用，需要采取额外的同步措施或使用线程安全的ConcurrentHashMap。

------

### HashMap的put(key,val)和get(key)过程

- 存储对象时，我们将K/V传给put方法时，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量(==超过Load Facotr则resize为原来的2倍==)。
- 获取对象时，我们将K传给get，它调用hashCode计算hash从而得到==bucket==位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，Hashmap通过链表将产生碰撞冲突的元素组织起来，在Java 8中，如果一个bucket中碰撞冲突的元素==超过某个限制(默认是8)，则使用红黑树来替换链表==，从而提高速度。

------

### HashMap在多线程下可能会出现的问题？

- JDK1.7中的 HashMap 使用头插法插入元素，在多线程的环境下，==扩容的时候有可能导致环形链表的出现==，形成死循环。因此，JDK1.8使用尾插法插入元素，在扩容时会保持链表元素原本的顺序，不会出现环形链表的问题。

  ```
  3️⃣ 一个典型示例（头插法）
  
  假设 JDK 1.7 使用 头插法迁移链表：
  
  原链表：1 -> 2 -> 3
  
  线程 A 复制节点 1，设置 1.next = null → 准备放入新桶
  
  线程 B 同时复制节点 2，设置 2.next = 1
  
  线程 A 设置 1.next = 2
  
  最终形成循环：1 -> 2 -> 1 -> 2 ...
  
  这种情况是由于多线程同时修改同一个链表节点的 next 引起的。
  ```

  

- 多线程同时执行 put 操作，如果计算出来的索引位置是相同的，那会造成前一个 key 被后一个 key 覆盖，从而导致元素的丢失。此问题在JDK 1.7和 JDK 1.8 中都存在。

- **空指针异常（NullPointerException）**：如果你尝试用 `null` 作为键调用 `get` 方法，而 `HashMap` 没有被初始化（即为 `null`），那么会抛出空指针异常。不过，如果 `HashMap` 已经初始化，使用 `null` 作为键是允许的，因为 `HashMap` 支持 `null` 键。

- **线程安全**：`HashMap` 本身不是线程安全的。如果在多线程环境中，没有适当的同步措施，同时对 `HashMap` 进行读写操作可能会导致不可预测的行为。例如，在一个线程中调用 `get` 方法读取数据，而另一个线程同时修改了结构（如增加或删除元素），可能会导致读取操作得到错误的结果或抛出 `ConcurrentModificationException`。如果需要在多线程环境中使用类似 `HashMap` 的数据结构，可以考虑使用 `ConcurrentHashMap`。

  ```java
  volatile操作是原子性的 非volatile是非原子的 因此可能出现写一半就被读取的情况
  ```

  

------

### HashMap一般用什么做Key？为啥String适合做Key呢？

用 string 做 key，因为 ==String对象是不可变的==，一旦创建就不能被修改，这确保了==Key的稳定性==。如果Key是可变的，可能会导致hashCode和equals方法的不一致，进而影响HashMap的正确性。

------

### 为什么HashMap要用红黑树而不是平衡二叉树？

- 平衡二叉树追求的是一种 **“完全平衡”** 状态：任何结点的左右子树的高度差不会超过 1，优势是树的结点是很平均分配的。这个要求实在是太严了，导致每次进行插入/删除节点的时候，几乎都会破坏平衡树的第二个规则，进而我们都需要通过**左旋**和**右旋**来进行调整，使之再次成为一颗符合要求的平衡树。
- 红黑树不追求这种完全平衡状态，而是追求一种 **“弱平衡”** 状态：整个树最长路径不会超过最短路径的 2 倍。优势是虽然牺牲了一部分查找的性能效率，但是能够换取一部分维持树平衡状态的成本。与平衡树不同的是，红黑树在插入、删除等操作，**不会像平衡树那样，频繁着破坏红黑树的规则，所以不需要频繁着调整**，这也是我们为什么大多数情况下使用红黑树的原因。

------

### Hashmap key可以为null吗？

可以为 null。

- hashMap中使用hash()方法来计算key的哈希值，当key为空时，直接另key的哈希值为0，不走key.hashCode()方法；

![img](https://cdn.xiaolincoding.com//picgo/1720685862193-66a32b79-ddf0-46d5-87df-d2fc2b3d87cb.png)

- hashMap虽然支持key和value为null，但是null作为key只能有一个，null作为value可以有多个；
- 因为hashMap中，如果key值一样，那么会覆盖相同key值的value为最新，所以key为null只能有一个。

```
public static void main(String[] args) {
    HashMap<Integer,Integer> map = new HashMap<>();
    map.put(null,1);
    System.out.println(map.get(null));
}

//输出
1
```

------

### HashMap的扩容机制介绍一下

hashMap默认的==负载因子是0.75==，即如果hashmap中的元素个数超过了==总容量75%，则会触发扩容==，扩容分为两个步骤：

- **第1步**是对哈希表长度的扩展==（2倍）==
- **第2步**是将旧哈希表中的数据==放到新的哈希表==中。

因为我们使用的是2次幂的扩展(指长度扩为原来2倍)，所以，元素的位置==要么是在原位置，要么是在原位置再移动2次幂的位置==。

如我们从16扩展为32时，具体的变化如下所示：

![img](https://cdn.xiaolincoding.com//picgo/1713514753772-9467a399-6b18-4a47-89d4-957adcc53cc0.webp)

因此元素在重新计算hash之后，因为n变为2倍，那么n-1的mask范围在高位多1bit(红色)，因此新的index就会发生这样的变化：

![img](https://cdn.xiaolincoding.com//picgo/1713514753786-cdca10bf-6eda-47f9-9bbe-0cc3beb67d76.webp)

因此，我们在扩充HashMap的时候，不需要重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，==是0的话索引没变，是1的话索引变成“原索引+oldCap”==。可以看看下图为16扩充为32的resize示意图：

![img](https://cdn.xiaolincoding.com//picgo/1713514753885-d1529537-322c-49b1-beec-5d9953da5150.webp)

这个设计确实非常的巧妙，既省去了重新计算hash值的时间，而且同时，由于新增的1bit是0还是1可以认为是随机的，因此resize的过程，均匀的把之前的冲突的节点分散到新的bucket了。

------

### HashMap的大小为什么是2的n次方大小呢？

在 JDK1.7 中，HashMap 整个扩容过程就是分别取出数组元素，一般该元素是最后一个放入链表中的元素，然后遍历以该元素为头的单向链表元素，依据每个被遍历元素的 hash 值计算其在新数组中的下标，然后进行交换。==这样的扩容方式会将原来哈希冲突的单向链表尾部变成扩容后单向链表的头部。==

而在 JDK 1.8 中，HashMap 对扩容操作做了优化。由于扩容数组的长度是 2 倍关系，所以对于假设初始 tableSize = 4 要扩容到 8 来说就是 0100 到 1000 的变化（左移一位就是 2 倍），在扩容中只用判断原来的 hash 值和左移动的一位（newtable 的值）按位与操作是 0 或 1 就行，==0 的话索引不变，1 的话索引变成原索引加上扩容前数组。==

之所以能通过这种“与运算“来重新分配索引，是因为 hash 值本来就是随机的，而 hash 按位与上 newTable 得到的 0（扩容前的索引位置）和 1（扩容前索引位置加上扩容前数组长度的数值索引处）就是随机的，所以扩容的过程就能把之前哈希冲突的元素再随机分布到不同的索引中去。

------

### 说说Hashmap的负载因子

HashMap 负载因子 loadFactor 的默认值是 0.75，当 HashMap 中的元素个数超过了容量的 75% 时，就会进行扩容。

默认负载因子为 0.75，是因为它提供了空间和时间复杂度之间的良好平衡。

==负载因子太低会导致大量的空桶浪费空间，负载因子太高会导致大量的碰撞，降低性能==。0.75 的负载因子在这两个因素之间取得了良好的平衡。

------

### ConcurrentHashMap怎么实现的？

#### JDK 1.7 ConcurrentHashMap

在 JDK 1.7 中它使用的是数组加链表的形式实现的，而数组又分为：==大数组 Segment 和小数组 HashEntry。== ==Segment 是一种可重入锁（ReentrantLock）==，在 ConcurrentHashMap 里扮演锁的角色；HashEntry 则用于存储键值对数据。一个 ConcurrentHashMap 里包含一个 Segment 数组，一个 Segment 里包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素。

![img](https://cdn.xiaolincoding.com//picgo/1721807523151-41ad316a-6264-48e8-9704-5b362bc0083c.webp)

JDK 1.7 ConcurrentHashMap 分段锁技术将数据分成一段一段的存储，然后给每一段数据配一把锁，==当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问==，能够实现真正的并发访问。

#### JDK 1.8 ConcurrentHashMap

在 JDK 1.7 中，ConcurrentHashMap 虽然是线程安全的，但因为它的底层实现是数组 + 链表的形式，所以在数据比较多的情况下访问是很慢的，因为要遍历整个链表，而 JDK 1.8 则使用了数组 + 链表/红黑树的方式优化了 ConcurrentHashMap 的实现，具体实现结构如下：

![img](https://cdn.xiaolincoding.com//picgo/1721807523128-7b1419e7-e6ba-47e6-aba0-8b29423a8ce7.webp)

JDK 1.8 ConcurrentHashMap JDK 1.8 ConcurrentHashMap 主要通过 ==volatile + CAS 或者 synchronized== 来实现的线程安全的。添加元素时首先会判断容器是否为空：

- 如果为空则使用 volatile 加 CAS 来初始化
- 如果容器不为空，则根据存储的元素计算该位置是否为空。
  - 如果根据存储的元素计算结果为空，则利用 CAS 设置该节点；
  - 如果根据存储的元素计算结果不为空，则使用 synchronized ，然后，遍历桶中的数据，并替换或新增节点到桶中，最后再判断是否需要转为红黑树，这样就能保证并发访问时的线程安全了。

如果把上面的执行用一句话归纳的话，就相当于是ConcurrentHashMap通过对头结点加锁来保证线程安全的，锁的粒度相比 Segment 来说更小了，发生冲突和加锁的频率降低了，并发操作的性能就提高了。

而且 JDK 1.8 使用的是红黑树优化了之前的固定链表，那么当数据量比较大的时候，查询性能也得到了很大的提升，从之前的 O(n) 优化到了 O(logn) 的时间复杂度。

```
1️⃣ CAS（Compare-And-Set）

使用场景：空桶插入或单节点更新

原理：利用 CPU 的原子指令，比较当前值与期望值，如果相同就写入新值

优点：无锁、高性能

缺点：冲突多时，CAS 可能失败多次，性能下降

if (casTabAt(tab, i, null, newNode)) {
    // 插入成功
}


这段代码意思：如果桶为空，就用 CAS 原子地插入节点

适合低冲突、空桶写入场景

2️⃣ synchronized（桶级锁）

使用场景：冲突桶（链表/红黑树）或链表长度超过阈值需要转红黑树

原理：锁住桶，保证同一个桶的写操作串行化

优点：简单可靠，保证多个线程同时修改同一个链表不会断链或丢数据

缺点：同一个桶内写操作会阻塞其他线程

synchronized (first) {
    // 对链表或树节点做插入/更新
}
```

| 方案         | 适用场景     | 问题/限制                      |
| ------------ | ------------ | ------------------------------ |
| CAS          | 空桶或单节点 | 冲突多时可能不断失败，需要重试 |
| synchronized | 冲突桶/链表  | 锁的开销比 CAS 高，但安全可靠  |

---

#### 总结

| 特性     | JDK 1.7                    | JDK 1.8+                               |
| -------- | -------------------------- | -------------------------------------- |
| 数据结构 | Segment + HashEntry        | Node[] + 链表/红黑树                   |
| 写操作   | Segment 锁                 | CAS（空桶）+ 局部锁（冲突桶）          |
| 读操作   | 无锁或部分 volatile        | 无锁读取，依赖 volatile 可见性         |
| 扩容     | Segment 内部扩容           | 整个 table 扩容 + volatile 替换        |
| 并发性能 | Segment 粒度锁，高并发受限 | 空桶 CAS 无锁，桶冲突锁局部 → 性能更高 |

------

### 已经用了synchronized，为什么还要用CAS呢？

ConcurrentHashMap使用这两种手段来保证线程安全主要是一种权衡的考虑，在某些操作中使用synchronized，还是使用CAS，主要是根据锁竞争程度来判断的。

比如：在putVal中，如果计算出来的hash槽没有存放元素，那么就可以直接使用CAS来进行设置值，这是因为在设置元素的时候，因为hash值经过了各种扰动后，造成hash碰撞的几率较低，那么我们可以预测使用较少的自旋来完成具体的hash落槽操作。

当发生了hash碰撞的时候说明容量不够用了或者已经有大量线程访问了，因此这时候使用synchronized来处理hash碰撞比CAS效率要高，因为发生了hash碰撞大概率来说是线程竞争比较强烈。

------

### ConcurrentHashMap用了悲观锁还是乐观锁?

悲观锁和乐观锁都有用到。

添加元素时首先会判断容器是否为空：

- 如果为空则使用 volatile 加 **CAS （乐观锁）** 来初始化。
- 如果容器不为空，则根据存储的元素计算该位置是否为空。
- 如果根据存储的元素计算结果为空，则利用 **CAS（乐观锁）** 设置该节点；
- 如果根据存储的元素计算结果不为空，则使用 **synchronized（悲观锁）** ，然后，遍历桶中的数据，并替换或新增节点到桶中，最后再判断是否需要转为红黑树，这样就能保证并发访问时的线程安全了。

------

### HashTable 底层实现原理是什么？

![img](https://cdn.xiaolincoding.com//picgo/1719982934770-8587cb0a-6e1d-4007-9a22-bc1e41276491.png)

- Hashtable的底层数据结构主要是**数组加上链表**，数组是主体，链表是解决hash冲突存在的。
- HashTable是线程安全的，实现方式是**Hashtable的所有公共方法均采用synchronized关键字**，当一个线程访问同步方法，另一个线程也访问的时候，就会陷入阻塞或者轮询的状态。

------

### Set

#### Set集合有什么特点？如何实现key无重复的？

- **set集合特点**：Set集合中的元素是唯一的，不会出现重复的元素。
- **set实现原理**：Set集合通过内部的数据结构（如哈希表、红黑树等）来实现key的无重复。当向Set集合中插入元素时，会先根据元素的hashCode值来确定元素的存储位置，然后再通过equals方法来判断是否已经存在相同的元素，如果存在则不会再次插入，保证了元素的唯一性。

#### 有序的Set是什么？记录插入顺序的集合是什么？

- **有序的 Set 是TreeSet和LinkedHashSet**。TreeSet是基于红黑树实现，保证元素的自然顺序。LinkedHashSet是基于双重链表和哈希表的结合来实现元素的有序存储，保证元素添加的自然顺序
- **记录插入顺序的集合通常指的是LinkedHashSet**，它不仅保证元素的唯一性，还可以保持元素的插入顺序。当需要在Set集合中记录元素的插入顺序时，可以选择使用LinkedHashSet来实现。

------

