---

类型: 笔记
创建日期: 2024-04-04
修改日期: 2024-04-04
---
# ThreadLocal核心操作原理及源码分析

## 1. ThreadLocalMap Hash 算法

既然是`Map`结构，那么`ThreadLocalMap`当然也要实现自己的`hash`算法来解决散列表数组冲突问题。

ThreadLocalMap的Hash算法：

- int i = key.threadLocalHashCode & (len-1);【`i`就是当前 key 在散列表中对应的数组下标位置】

```java
public class ThreadLocal<T> {
    private final int threadLocalHashCode = nextHashCode();
    private static AtomicInteger nextHashCode = new AtomicInteger();
    private static final int HASH_INCREMENT = 0x61c88647;

    private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }

    static class ThreadLocalMap {
        ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
            table = new Entry[INITIAL_CAPACITY];
            int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
		   //将Entry对象保存到计算出的Hash所在的位置
            table[i] = new Entry(firstKey, firstValue);
            size = 1;
            setThreshold(INITIAL_CAPACITY);
        }
    }
}
```

每当创建一个`ThreadLocal`对象，这个`ThreadLocal.nextHashCode` 这个值就会增长 `0x61c88647` ,这个值很特殊，它是**斐波那契数** 也叫 **黄金分割数**。`hash`增量为 这个数字，带来的好处就是 `hash` **分布非常均匀**。

写个Demo尝试下：

```java
public class ThreadLocalDemo {

    //0x61c88647叫'翡菠那契数'也叫'黄金分割数'
    private static final int HASH_INCREMENT = 0x61c88647;

    public static void main(String[] args) {
        //ThreadLocalMap的Hash算法：int i = key.threadLocalHashCode & (len-1);
        int hashCode = 0;
        for (int i = 0; i < 16; i++) {
            hashCode = i * HASH_INCREMENT + HASH_INCREMENT;
            int hash = hashCode & (16 - 1);
            System.out.println(hash);
        }
    }
}
```

> 打印结果：
> 
> 7  
> 14  
> 5  
> 12  
> 3  
> 10  
> 1  
> 8  
> 15  
> 6  
> 13  
> 4  
> 11  
> 2  
> 9  
> 0

## 2. ThreadLocalMap.set() 详解

### 2.1 原理介绍

set()方法，首先通过ThreadLocalMap的`hash`算法计算出对应的槽位：

- 情况一：槽位对应的`Entry`数据为空，直接添加Entry对象后进行一轮启发式清理，
- 情况二：槽位对应的`Entry`数据不为空，`key`值一致，执行更新操作
- 情况三：槽位对应的`Entry`数据不为空，往后遍历散列表，如果遇到`Entry`为`null`的执行添加，如果`key`值相等的执行更新
- 情况四：槽位对应的`Entry`数据不为空，往后遍历过程中在遇到`Entry`为`null`之前 碰到`key`为null情况标记此 key=null的节点为探测式清理过期数据的开始位置，初始化两个变量**A、B** 【源码中,A变量=slotToExpunge、B变量=staleSlot】
    - A：往前遍历数组，遇到`key=null`的节点就更新A的下标(索引)，直到遇到`Entry=null`的槽位才停止迭代
    - B：往后遍历数组，
        - `k = key` 说明是替换操作，可以使用
        - 碰到一个过期的桶，执行替换逻辑，占用过期桶。
        - 碰到桶中`Entry=null`的情况，直接使用

### 6.2 源码详解

#### 1. set()

```java
/*

1. 遍历当前key值对应的桶中Entry数据为空，这说明散列数组这里没有数据冲突，跳出for循环，直接set数据到对应的桶中
2. 如果key值对应的桶中Entry数据不为空
	2.1 如果k = key，说明当前set操作是一个替换操作，做替换逻辑，直接返回
	2.2 如果key = null，说明当前桶位置的Entry是过期数据，执行replaceStaleEntry()方法(核心方法)，然后返回
3. for循环执行完毕，继续往下执行说明向后迭代的过程中遇到了entry为null的情况
	3.1 在Entry为null的桶中创建一个新的Entry对象
	3.2 执行++size操作
4. 调用cleanSomeSlots()做一次启发式清理工作，清理散列数组中Entry的key过期的数据
	4.1 如果清理工作完成后，未清理到任何数据，且size超过了阈值(数组长度的 2/3)，进行rehash()操作
	4.2 rehash()中会先进行一轮探测式清理，清理过期key，清理完成后如果size >= threshold - threshold / 4，就会执行真正的扩容逻辑
	
*/

private void set(ThreadLocal<?> key, Object value) {
    Entry[] tab = table;
    int len = tab.length;
    //通过key来计算在散列表中的对应位置
    int i = key.threadLocalHashCode & (len-1);

  	//如果hash值坐标上entry!=null 则进行后续遍历，找到合适位置添加/更新Entry对象
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
      
        ThreadLocal<?> k = e.get();

        if (k == key) {
            e.value = value;
            return;
        }

        if (k == null) {
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}
```

#### 2. replaceStaleEntry()

**替换新的Entry对象，流程如下：**

1. 从当前节点开始前序遍历，碰到 key=null 的就更新探测清理的开始下标

1. 从当前节点开始后续遍历：
    - k == key 的时候执行更新操作，前序遍历 && 后续遍历均未发现key过期的entry数据，更新探测是清理的开始坐标为当前坐标，进行启发式过期数据清理
    - k == null 时，前序遍历未发现key过期的情况，更新探测是清理的开始坐标

1. 未发现key相同 或`key==null`的情况时，新增entry到当前坐标
    
3. 如果发现前序或者后续遍历中有key过期的情况，从key=null的节点开始（优先前序遍历中记录的节点），进行一轮启发式清理流程
    

```java
private void replaceStaleEntry(ThreadLocal<?> key, Object value, int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;
    Entry e;

    int slotToExpunge = staleSlot;
    //1.前序遍历，更新探测清理的开始下标
    for (int i = prevIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = prevIndex(i, len))

      	//ThreadLocalMap的key == null
        if (e.get() == null)
            slotToExpunge = i;

  
  	//2.后续遍历，for循环内负责更新或清理key=null的情况，循环外发现entry=null的情况直接新增
    for (int i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {

        ThreadLocal<?> k = e.get();

        if (k == key) {
            e.value = value;
            tab[i] = tab[staleSlot];
            tab[staleSlot] = e;

          	//前序遍历 && 后续遍历均未发现key过期的entry数据
            if (slotToExpunge == staleSlot)
              	//修改开始探测式清理过期数据的下标为当前循环的 index
                slotToExpunge = i;
          	//进行启发式过期数据清理
            cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
            return;
        }

        //当前节点key为null && 前续遍历扫描时未发现key过期数据，更新清理坐标的起始节点
        if (k == null && slotToExpunge == staleSlot)
            slotToExpunge = i;
     }

    tab[staleSlot].value = null;
    tab[staleSlot] = new Entry(key, value);

    if (slotToExpunge != staleSlot)
        cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
}
```

#### 3. expungeStaleEntry()

**探测式清理：**

- 遍历散列数组，将开始位置的桶置为null(因为调用方法是传入的参数就是过期元素坐标)，并从当前位置向后开始探测清理过期数据
    - entry.key =/=null（key过期）的将value和entry置为null
    - entry.key != null 的重新计算此元素的hash值重新定位，如果重新计算后的位置上有元素就往后顺延一位，直到遇见空桶后添加

```java
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;

                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```

#### 4. rehash()

在`ThreadLocalMap.set()`方法的最后，如果执行完启发式清理工作后，未清理到任何数据，且当前散列数组中`Entry`的数量已经达到了列表的扩容阈值threshold`(len*2/3)`，就开始执行`rehash()`逻辑：

```java
 if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
```

rehash()

```java
private void rehash() {
  	//首先进行一轮'探测式清理'流程
    expungeStaleEntries();

    if (size >= threshold - threshold / 4)
        resize();
}

private void expungeStaleEntries() {
    Entry[] tab = table;
    int len = tab.length;
    for (int j = 0; j < len; j++) {
        Entry e = tab[j];
        if (e != null && e.get() == null)
            expungeStaleEntry(j);
    }
}
```

#### 5. resize()

扩容后的`tab`的大小为`oldLen * 2`，然后遍历老的散列表，重新计算`hash`位置，然后放到新的`tab`数组中，如果出现`hash`冲突则往后寻找最近的`entry`为`null`的槽位，遍历完成之后，`oldTab`中所有的`entry`数据都已经放入到新的`tab`中了。重新计算`tab`下次扩容的**阈值**，具体代码如下：

```java
private void resize() {
    Entry[] oldTab = table;
    int oldLen = oldTab.length;
    int newLen = oldLen * 2;
    Entry[] newTab = new Entry[newLen];
    int count = 0;

    for (int j = 0; j < oldLen; ++j) {
        Entry e = oldTab[j];
        if (e != null) {
            ThreadLocal<?> k = e.get();
            if (k == null) {
                e.value = null;
            } else {
                int h = k.threadLocalHashCode & (newLen - 1);
                while (newTab[h] != null)
                    h = nextIndex(h, newLen);
                newTab[h] = e;
                count++;
            }
        }
    }

    setThreshold(newLen);
    size = count;
    table = newTab;
}
```

## 3.ThreadLocalMap.get() 详解

### 3.1 原理介绍

**第一种情况：** 通过查找`key`值计算出散列表中`slot`位置，然后该`slot`位置中的`Entry.key`和查找的`key`一致，则直接返回：

**第二种情况：** `slot`位置中的`Entry.key`和要查找的`key`不一致：从当前节点往后继续迭代查找，遇见key相同的值就返回Entry对象，遇到key过期的值就进行一次探测是清理流程，直到遇见key相同的返回。

### 3.2 源码详解

```java
public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
}

private Entry getEntry(ThreadLocal<?> key) {
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}

private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;

    while (e != null) {
        ThreadLocal<?> k = e.get();
        if (k == key)
            return e;
        if (k == null)
            expungeStaleEntry(i);
        else
            i = nextIndex(i, len);
        e = tab[i];
    }
    return null;
}

```

## 4. ThreadLocalMap扩容机制

ThreadLocalMap扩容的两个条件

1. 在 ThreadLocalMap.set() 方法的最后，如果执行完 **启发式清理** 工作后，未清理到任何数据 && 当前散列数组中 Entry 的数量已经达到了列表的扩容阈值`(len*2/3)`，就开始执行`rehash()`逻辑：
2. rehash() 中，先进行一轮 探测式清理 流程，然后判断`size >= threshold - threshold / 4` 也就是`size >= threshold * 3/4` 来决定是否扩容

每次扩容为原来数组大小的2倍（ThreadLocalMap初始容量为16，必须是2的次幂）

### 4.1 源码分析：

#### set()

在 ThreadLocalMap.set() 方法的最后，如果执行完启发式清理工作后未清理到任何数据 && 当前散列数组中 Entry 的数量已经达到了列表的扩容阈值threshold`(len*2/3)`，就开始执行`rehash()`逻辑：

```java
 if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
```

#### rehash()

```java
private void rehash() {
  	//首先进行一轮'探测式清理'流程
    expungeStaleEntries();

    if (size >= threshold - threshold / 4)
        resize();
}

private void expungeStaleEntries() {
    Entry[] tab = table;
    int len = tab.length;
    for (int j = 0; j < len; j++) {
        Entry e = tab[j];
        if (e != null && e.get() == null)
            expungeStaleEntry(j);
    }
}
```

#### resize()

扩容后的`tab`的大小为`oldLen * 2`，然后遍历老的散列表，重新计算`hash`位置，然后放到新的`tab`数组中，如果出现`hash`冲突则往后寻找最近的 entry 为 null 的槽位，遍历完成之后，`oldTab`中所有的`entry`数据都已经放入到新的`tab`中了。重新计算`tab`下次扩容的**阈值**，具体代码如下：

```java
private void resize() {
    Entry[] oldTab = table;
    int oldLen = oldTab.length;
    int newLen = oldLen * 2;
    Entry[] newTab = new Entry[newLen];
    int count = 0;

    for (int j = 0; j < oldLen; ++j) {
        Entry e = oldTab[j];
        if (e != null) {
            ThreadLocal<?> k = e.get();
            if (k == null) {
                e.value = null;
            } else {
                int h = k.threadLocalHashCode & (newLen - 1);
                while (newTab[h] != null)
                    h = nextIndex(h, newLen);
                newTab[h] = e;
                count++;
            }
        }
    }

    setThreshold(newLen);
    size = count;
    table = newTab;
}
```

## 5. ThreadLocalMap.key到期的两种清理方式

上文中：[ThreadLocal内存泄露问题 - lihewei - 博客园 (cnblogs.com)](https://www.cnblogs.com/lihw/p/17214194.html) 我们提到`ThreadLocalMap`的`key`会因为GC导致过期，在ThreadLocalMap中有数据清理方式，分别是：

- 探测式清理（源码中：expungeStaleEntry() 方法 ）
- 启发式清理（源码中：cleanSomeSlots() 方法 ）

### 1.1 探测式清理

​ 探测式清理：也就是源码中`expungeStaleEntry()`方法，遍历散列数组，从开始位置（hash得到的位置）向后探测清理过期数据，将过期数据的 Entry 设置为 null ，沿途中碰到未过期的数据则将此数据`rehash`后重新在 table 数组中定位，如果定位的位置已经有了数据，则会将未过期的数据放到最靠近此位置的 Entry=null 的桶中（顺序往后延），使 rehash 后的 Entry 数据距离正确的桶的位置更近一些。

**说人话就是**：从当前节点开始遍历数组，key=/=null的将entry置为null，key!=null的对当前元素的key重新hash分配位置，若重新分配的位置上有元素就往后顺延。

`expungeStaleEntry()`源码：

```java
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;

                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```

### 1.2 启发式清理

启发式清理需要接收两个参数：

1. 探测式清理后返回的数字下标
2. 数组总长度

根据源码可以看出，启动式清理会从传入的下标 `i` 处，向后遍历。如果发现过期的Entry则再次触发探测式清理，并重置 `n`。这个n是用来控制 `do while` 循环的跳出条件。如果遍历过程中，连续 `m` 次没有发现过期的Entry，就可以认为数组中已经没有过期Entry了。  
这个 `m` 的计算是 `n >>>= 1` ，你也可以理解成是数组长度的2的几次幂。  
例如：数组长度是16，那么24=16，也就是连续4次没有过期Entry，即 m = logn/log2(n为数组长度)

**说人话就是：** 从当前节点开始，进行do-while循环检查清理过期key，结束条件是连续`n`次未发现过期key就跳出循环，n是经过位运算计算得出的，可以简单理解为数组长度的2的多少次幂 次，例如：

`cleanSomeSlots()`源码：

```java
private boolean cleanSomeSlots(int i, int n) {  //探测式清理后返回的数字下标，这里至少保证了Hash冲突的下标至探测式清理后返回的下标这个区间无过期的Entry, n 数组总长度
    boolean removed = false;
    Entry[] tab = table;
    int len = tab.length;
    do {
        i = nextIndex(i, len);
        Entry e = tab[i];
        if (e != null && e.get() == null) {  // 如果发现过期的Entry就在执行一次探测性清理
            n = len;  //重置n
            removed = true;
            i = expungeStaleEntry(i);   //探测性清理
        }
    } while ( (n >>>= 1) != 0);  // 循环条件: m = logn/log2(n为数组长度)
    return removed;
}
```

## 2. 哪些地方会触发这两种key的到期清理方式

1. set() 方法中，遇到key=null的情况会触发一轮 探测式清理 流程
2. set() 方法最后会执行一次 启发式清理 流程
3. rehash() 方法中会调用一次 探测式清理 流程
4. get() 方法中 遇到key过期的时候会触发一次 探测式清理 流程
5. 启发式清理流程中遇到key=null的情况也会触发一次 探测式清理 流程