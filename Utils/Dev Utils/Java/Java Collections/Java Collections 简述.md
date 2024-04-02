---
tags:
  - Java
  - Collections
  - 集合
  - 集合用法
---
> Java集合API提供了一组功能强大的数据结构和算法, 具有以下作用(`简述`)
> 
> 1. `存储和组织数据`
> 2. `提供高效的数据访问和操作`
> 4. `实现算法和数据处理`
> 5. `提供线程安全性`
> 6. `支持泛型编程`
> 
> `java.util.Collection`是集合框架的根接口。它位于集合框架层次结构的顶部。它包含一些重要的方法，例如每个 `Collection` 类都必须实现的 `size()、iterator()、add()、remove()、clear()`
> 
> 其他一些重要的接口是 `java.util.List、java.util.Set、java.util.Queue、java.util.Map`, `Map` 是唯一不继承自 `Collection` 接口的接口，但它是 `Collections` 框架的一部分。所有集合框架接口都存在于 `java.util` 包中

## Collection接口

> 代表了一组对象的集合。**它是其他集合接口的父接口**，提供了基本的操作方法，如添加、删除、查询等。
<!--SR:!2024-04-13,13,270-->

```java
Collection<String> collection = new ArrayList<>();
// 添加元素
collection.add("元素1");
collection.add("元素2");
collection.add("元素3");
// 删除元素
collection.remove("元素2");
// 遍历集合
for (String item : collection) {
    System.out.println(item);
}
```

## List接口

> 继承自 `Collection` 接口，表示有序的集合。List中的元素可以根据索引进行访问，可以包含重复的元素。

```java
import java.util.ArrayList;
import java.util.List;
public class ListExample {
    public static void main(String[] args) {        
	    List<String> list = new ArrayList<>();        
        // 添加元素        
        list.add("Java");        
        list.add("Python");        
        list.add("C++");        
        // 获取元素        
        String element = list.get(0);        
        System.out.println("第一个元素：" + element);        
        // 删除元素        
        list.remove(1);        
        // 遍历元素        
        for (String str : list) {            
	        System.out.println(str);        
        }        
        // 判断是否包含某个元素        
        boolean contains = list.contains("Java");        
        System.out.println("是否包含Java：" + contains);        
        // 清空列表        
        list.clear();        
        // 判断列表是否为空       
        boolean empty = list.isEmpty();        
        System.out.println("列表是否为空：" + empty);    
    }
}
```

---

## HashSet， TreeSet

> `HashSet` 是 Java 集合框架中 `Set` 接口的实现类之一，它是一个无序、不重复的集合
> 
> `TreeSet` 是 Java 集合框架中 Set 接口的另一个实现类，它基于红黑树的数据结构实现，TreeSet 的作用是提供了一个有序且不重复的集合

## Set接口

> 继承自 `Collection` 接口，表示不允许包含重复元素的集合。Set没有定义特定的顺序，可以使用HashSet、TreeSet等具体实现。

### `使用 HashSet 实现`

```java
import java.util.Set;
import java.util.HashSet;
public class SetExample {
    public static void main(String[] args) {        
	    // 创建HashSet对象        
	    Set<String> set = new HashSet<>();        
	    // 添加元素        
	    set.add("Apple");        
	    set.add("Banana");        
	    set.add("Orange");        
	    set.add("Grape");        
	    // 打印集合大小        
	    System.out.println("集合大小: " + set.size());        
	    // 遍历集合        
	    for (String fruit : set) {            
		    System.out.println(fruit);        
	    }        
	    // 判断集合中是否包含指定元素        
	    String target = "Banana";        
	    if (set.contains(target)) {            
		    System.out.println("集合中包含" + target);        
	    } else {            
		    System.out.println("集合中不包含" + target);        
	    }        
	    // 删除元素        
	    set.remove("Orange");        
	    // 清空集合        
	    set.clear();        
	    // 判断集合是否为空        
	    if (set.isEmpty()) {            
		    System.out.println("集合为空");        
	    } else {            
		    System.out.println("集合不为空");        
	    }    
    }
}

```
### `使用 TreeSet 实现`

```java
import java.util.Set;
import java.util.TreeSet;
public class TreeSetExample {
    public static void main(String[] args) {        
	    Set<Integer> set = new TreeSet<>();        
	    // 添加元素        
	    set.add(5);        
	    set.add(2);        
	    set.add(8);        
	    set.add(3);        
	    set.add(1);        
	    // 遍历集合        
	    for (Integer element : set) {            
		    System.out.println(element);        
	    }        
	    // 获取集合大小        
	    int size = set.size();        
	    System.out.println("集合大小: " + size);        
	    // 判断集合中是否包含指定元素        
	    boolean contains = set.contains(3);        
	    System.out.println("集合中是否包含3: " + contains);        
	    // 删除元素        
	    set.remove(2);        
	    // 判断集合是否为空        
	    boolean isEmpty = set.isEmpty();        
	    System.out.println("集合是否为空: " + isEmpty);    
    }
}
```

---

## HashMap、TreeMap

> `HashMap` 是基于哈希表实现的，它提供了快速的插入、删除和查找操作，时间复杂度为 `O(1)`
> 
> `HashMap` 不保证顺序，允许有 `null键` 和 `null值`，适用于大多数情况，特别是在需要快速查找键值对的场景中
> 
> `O(1)` 就是不需要遍历查找，直接得到搜索结果的，例如哈希表、bitmap等数据结构，都可以作为`O(1)` 时间复杂度算法的数据结构
> 
> `TreeMap` 是基于红黑树（一种自平衡二叉搜索树）实现的，它可以保持元素的有序性，`TreeMap` 支持高效的插入、删除和查找操作，时间复杂度为 `O(logN)`
> 
> `O(logN)` 是一种时间复杂度的表示，表示算法在最坏情况下的运行时间与输入规模 `N` 的对数成正比。其中，`O 表示“大约”`，而 `logN 是以 2` 为底的对数

## Map接口

> 表示键值对的映射集合。Map中的元素以键值对的形式存储，通过键来唯一标识值。常用的实现类有HashMap、TreeMap等。

### `使用 HashMap 实现`

```java
import java.util.HashMap;
import java.util.Map;
public class HashMapWithNulls {
    public static void main(String[] args) {        
	    // 创建一个 HashMap 实例，允许 null 键和 null 值        
	    Map<String, Integer> map = new HashMap<>();        
	    // 添加键值对，包括 null 键和 null 值        
	    map.put("key1", 100);        
	    map.put(null, 200);        
	    map.put("key3", null);        
	    // 获取键对应的值，包括 null 键和 null 值        
	    Integer value1 = map.get("key1");        
	    System.out.println("key1 对应的值: " + value1);        
	    Integer value2 = map.get(null);        
	    System.out.println("null 对应的值: " + value2);        
	    Integer value3 = map.get("key3");        
	    System.out.println("key3 对应的值: " + value3);        
	    // 遍历所有的键值对，包括 null 键和 null 值        
	    for (Map.Entry<String, Integer> entry : map.entrySet()) {            
		    String key = entry.getKey();            
		    Integer value = entry.getValue();            
		    System.out.println("键: " + key + ", 值: " + value);        
		}        
	    // 删除特定的键值对        
	    map.remove(null);        
	    // 判断是否包含某个键        
	    boolean containsKey = map.containsKey(null);        
	    System.out.println("是否包含 null 键: " + containsKey);        
	    // 判断是否包含某个值        
	    boolean containsValue = map.containsValue(null);        
	    System.out.println("是否包含 null 值: " + containsValue);        
	    // 判断集合是否为空        
	    boolean isEmpty = map.isEmpty();        
	    System.out.println("集合是否为空: " + isEmpty);   
    }
}
```

### `使用 TreeMap 实现`

```java
import java.util.Map;
import java.util.TreeMap;
public class TreeMapExample {
    public static void main(String[] args) {        
	    // 创建一个 TreeMap 实例        
	    TreeMap<String, Integer> treeMap = new TreeMap<>();        
	    // 添加键值对        
	    treeMap.put("Alice", 95);        
	    treeMap.put("Bob", 87);        
	    treeMap.put("Charlie", 92);        
	    treeMap.put("David", 78);        
	    // 获取键对应的值        
	    int aliceScore = treeMap.get("Alice");        
	    System.out.println("Alice 的分数是：" + aliceScore);        
	    // 遍历所有的键值对        
	    for (Map.Entry<String, Integer> entry : treeMap.entrySet()) {            
	    String name = entry.getKey();            
	    int score = entry.getValue();            
	    System.out.println(name + " 的分数是：" + score);        
	    }        
	    // 获取第一个键值对        
	    Map.Entry<String, Integer> firstEntry = treeMap.firstEntry();        
	    System.out.println("第一个键值对：" + firstEntry.getKey() + " => " + firstEntry.getValue());        
	    // 获取最后一个键值对        
	    Map.Entry<String, Integer> lastEntry = treeMap.lastEntry();        
	    System.out.println("最后一个键值对：" + lastEntry.getKey() + " => " +lastEntry.getValue());        
	    // 获取键小于等于给定键的键值对        
	    Map.Entry<String, Integer> floorEntry = treeMap.floorEntry("Charlie");        
	    System.out.println("小于等于 Charlie 的键值对：" + floorEntry.getKey() + " => " + floorEntry.getValue());        
	    // 删除键值对        
	    treeMap.remove("Charlie");        
	    // 判断集合是否为空        
	    boolean isEmpty = treeMap.isEmpty();        
	    System.out.println("集合是否为空：" + isEmpty);    
    }
}
```

---

## LinkedList、PriorityQueue

> `LinkedList` 是 Java 集合框架中的一种实现了 List 接口的双向链表数据结构。它提供了对元素的高效插入、删除以及随机访问操作
> 
> `LinkedList` 主要适用于需要频繁插入、删除操作，以及在集合中间进行操作的场景，它在实现队列、链表特有操作和特定的业务需求上表现出色。但需要注意在需要随机访问元素时，可能会使用较低的性能
> 
> `PriorityQueue` 主要适用于需要根据优先级对元素进行排序的场景，它在任务调度、事件排序、贪心算法等方面提供了便利，并且可以作为一种有效的搜索算法工具

## Queue接口

> 继承自 `Collection` 接口，表示队列集合。Queue是一种特殊的集合，按照先进先出（FIFO）的方式管理元素。常用的实现类有LinkedList、PriorityQueue等。

### `使用 LinkedList 实现`

```java
import java.util.Queue;
import java.util.LinkedList;
public class QueueExample {
    public static void main(String[] args) {        
	    // 创建一个队列        
	    Queue<Integer> queue = new LinkedList<>();        
	    // 向队列中添加元素        
	    queue.offer(1);        
	    queue.offer(2);        
	    queue.offer(3);        
	    // 获取队列的大小        
	    System.out.println("队列大小: " + queue.size());        
	    // 检查队列是否为空        
	    System.out.println("队列是否为空: " + queue.isEmpty());        
	    // 访问队列头部的元素        
	    System.out.println("队列头部的元素: " + queue.peek());        
	    // 从队列中移除元素        
	    int removedElement = queue.poll();        
	    System.out.println("从队列中移除的元素: " + removedElement);        
	    // 遍历队列中的元素        
	    System.out.println("遍历队列中的元素:");        
	    for (int element : queue) {            
		    System.out.println(element);        
	    }    
    }
}
```

### `使用 PriorityQueue 实现`

```java
import java.util.Queue;
import java.util.PriorityQueue;
public class QueueExample {
    public static void main(String[] args) {        
	    // 创建一个优先级队列        
	    Queue<Integer> queue = new PriorityQueue<>();        
	    // 添加元素到队列        
	    queue.offer(5);        
	    queue.offer(2);        
	    queue.offer(8);        
	    queue.offer(1);        
	    // 获取并移除队列的头部元素        
	    int firstElement = queue.poll();        
	    System.out.println("从队列头部移除的元素: " + firstElement);        
	    // 获取队列的头部元素（不移除）        
	    int peekElement = queue.peek();        
	    System.out.println("队列头部的元素: " + peekElement);        
	    // 遍历队列中的元素        
	    System.out.println("遍历队列中的元素:");        
	    for (int element : queue) {            
		    System.out.println(element);        
	    }    
    }
}
```

---

## ArrayDeque、LinkedList

> `ArrayDeque` 基于数组实现，它有固定的容量（默认情况下为16），但可以动态调整大小
> 
> `ArrayDeque` 支持高效的随机访问，因为它基于数组，可以通过索引快速访问元素
> 
> `ArrayDeque` 在操作两端的元素（队列头和队列尾）时，具有高效的时间复杂度（O(1)）

`适用场景：` 当你需要在队列的两端频繁地插入和删除元素时，例如实现栈、双向队列或循环队列等数据结构

> `LinkedList` 基于双向链表实现，每个元素都包含前一个和后一个元素的引用
> 
> `LinkedList` 没有固定的容量限制，它可以根据需要动态调整大小
> 
> `LinkedList` 在插入和删除操作方面表现出色，特别是对于任意位置的元素操作
> 
> `LinkedList` 的随机访问效率较低，因为它需要遍历链表来到达目标位置

`适用场景：` 当你需要频繁地在任意位置插入和删除元素时，例如实现队列、栈、优先队列或循环缓冲区等数据结构

## Deque接口

> 继承自 `Queue` 接口，表示双端队列集合。Deque支持在两端插入、删除元素，可以作为栈或队列使用。常用的实现类有ArrayDeque、LinkedList等。

### `使用 ArrayDeque 实现`

```java
import java.util.Deque;
import java.util.ArrayDeque;
public class DequeExample {
    public static void main(String[] args) {        
	    // 创建一个双端队列        
	    Deque<Integer> deque = new ArrayDeque<>();        
	    // 向队列头部添加元素        
	    deque.offerFirst(1);        
	    deque.offerFirst(2);        
	    deque.offerFirst(3);        
	    // 向队列尾部添加元素        
	    deque.offerLast(4);        
	    deque.offerLast(5);        
	    // 从队列头部获取元素并移除        
	    int firstElement = deque.pollFirst();        
	    System.out.println("从队列头部移除的元素: " + firstElement);        
	    // 从队列尾部获取元素并移除        
	    int lastElement = deque.pollLast();        
	    System.out.println("从队列尾部移除的元素: " + lastElement);        
	    // 获取队列头部元素但不移除        
	    int peekFirstElement = deque.peekFirst();        
	    System.out.println("队列头部的元素: " + peekFirstElement);        
	    // 获取队列尾部元素但不移除        
	    int peekLastElement = deque.peekLast();        
	    System.out.println("队列尾部的元素: " + peekLastElement);        
	    // 遍历队列中的元素        
	    System.out.println("遍历队列中的元素:");        
	    for (int element : deque) {            
		    System.out.println(element);        
	    }    
    }
}
```

### `使用 LinkedList 实现`

```java
import java.util.Deque;
import java.util.LinkedList;
public class DequeExample {
    public static void main(String[] args) {        
	    // 创建一个双端队列        
	    Deque<Integer> deque = new LinkedList<>();        
	    // 添加元素到队列的头部和尾部        
	    deque.offerFirst(1);        
	    deque.offerLast(2);        
	    deque.offerFirst(3);        
	    deque.offerLast(4);        
	    // 获取并移除队列的头部和尾部元素        
	    int firstElement = deque.pollFirst();        
	    int lastElement = deque.pollLast();        
		System.out.println("从队列头部移除的元素: " + firstElement);  
		System.out.println("从队列尾部移除的元素: " + lastElement);        
		// 获取队列的头部和尾部元素（不移除）        
		int peekFirstElement = deque.peekFirst();        
		int peekLastElement = deque.peekLast();        
		System.out.println("队列头部的元素: " + peekFirstElement); 
		System.out.println("队列尾部的元素: " + peekLastElement);        
		// 遍历队列中的元素        
		System.out.println("遍历队列中的元素:");        
		for (int element : deque) {            
			System.out.println(element);        
		}    
	}
}

```
## Java 集合框架那么多接口，功能都相似，我该如何区分使用?

> `List 接口`
> 	`特点：` 有序集合，允许重复元素
> 	`典型实现类：` ArrayList、LinkedList
> 	`适用场景：` 需要按照插入顺序进行访问和操作的场景，也可快速随机访问元素
> 	
> `Set 接口`
> 	`特点：` 无序集合，不允许重复元素
> 	`典型实现类：` HashSet、TreeSet
> 	`适用场景：` 需要存储不重复元素的场景，例如去重、判断元素是否存在等
> 
> `Queue 接口`
> 	`特点：` 先进先出（FIFO）队列
> 	`典型实现类：` LinkedList、PriorityQueue
> 	`适用场景：` 实现队列数据结构，通常用于任务调度、事件处理等
> 
> `Map 接口`
> 	`特点：`键值对的映射
> 	`典型实现类：` HashMap、TreeMap
> 	`适用场景：` 需要根据唯一键快速查找值的场景，如存储关联数据、缓存等
