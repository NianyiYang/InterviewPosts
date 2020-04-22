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

> 链表插入时，**新加入的元素放在链表头**，先前的元素后移
