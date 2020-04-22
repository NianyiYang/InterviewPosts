[一篇文讲清楚HashMap](https://www.iteye.com/topic/539465)

```java
// HashMap 的数据结构
transient Entry[] table;  

// HashMap 的 Entry 的数据结构
static class Entry<K,V> implements Map.Entry<K,V> {  
        final K key;  
        V value;  
        final int hash;  
        Entry<K,V> next;  
		..........  
}  
```

> 用`transient`关键字标记的成员变量不参与序列化过程

查找元素
1. 计算 key 的 `hashcode()`，找到数组下标位置
2. 通过 key 的 `equals()`，找到链表中元素的位置

> 链表插入时，**新加入的元素放在链表头**，先前的元素后移

为什么 HashMap 默认数组大小是16？
> 因为 Java 实现的 hash 碰撞算法，在数组是2的整数幂时，产生的碰撞最小

HashMap 底层实现
> HashMap 底层是一个数组，有元素需要插入时，会通过调用`hashCode()`方法，计算出元素的 hash 值，从而将元素填入对应位置；
> 数组里的元素可能是null（key && value）,可能是单个对象（该对象也可能是null）；
> 当发生hash碰撞时，碰撞元素加入到对应数组位置的已有元素后面，形成单链表；
> 在 Java 1.8 之后，如果链表长度超过一定阈值（8）时，将链表转换为红黑树

Java 1.7 到 Java 1.8 HashMap 改进
> Java 1.7 中，`HashMap` 使用 `bucket` + `LinkedList` 实现，同一  hash 值的元素都存在同一个`bucket`里，当 hash 值相同的元素太多时，查找效率较低；Java 1.8 中，`HashMap` 使用 `bucket` + `LinkedList` + 红黑树 实现，链表长度超过一定阈值（8）时，将链表转换为红黑树，大大减少查找时间

HashMap的扩容机制
> 当 HashMap 中的元素越来越多的时候，碰撞的几率急速升高，为了提高查询效率，需要对数组进行扩容。**扩容中最耗性能的一点是：原数组中的数据必须重新计算位置并放到新数组中**。对应的方法名叫`resize()`
> 扩容时机：当 HashMap 元素超过 数组大小 x LoadFactor 时。LoadFactor（扩容因子）默认值为0.75。也就是说，默认情况下，当元素超过 16 x 0.75 = 12 时，需要将数组扩大一倍 16 x 2 = 32，然后调用`resize()`。注意**扩容因子是判断需不需要扩容，数组扩容时每次都是扩大一倍**
