# HashMap系列

## 第1节 HashMap

### 1 概述

HashMap是一个关联数组、哈希表

继承了AbstractMap抽象类，实现了Map、Cloneable、Serializeble接口

线程不安全

允许一个key为null，多个vaule为null



HashMap的底层数据结构是数组，称为**哈希桶**

```java
// transient用于取消序列化
transient Node<K,V>[] table;
```

每个桶里面存放的是**链表**，链表中的每个节点就是哈希表中的每个元素



当HashMap的容量达到`threshold`阈值时，就会触发扩容

扩容前后，哈希桶的长度一定会是2的幂



HashMap的源码中，充斥个各种位运算代替常规运算的地方，以提升效率：

- 与运算替代模运算。用 `hash & (table.length-1)` 替代 `hash % table.length`
- 用`if ((e.hash & oldCap) == 0)`判断扩容后，节点e处于低区还是高区



HashMap的存储结构：

![](D:\MyData\lixy425\Desktop\CSStudyNotes\Java\HashMap系列,assets\hashmap-structure.png)



[HashMap源码分析](https://blog.csdn.net/zxt0601/article/details/77413921)







### 2 hash值

key的hash值，并不仅仅只是key对象的`hashCode()`方法的返回值，还会经过**扰动函数**的扰动，以使hash值更加均衡

因为`hashCode()`是int类型，取值范围是40多亿，只要哈希函数映射的比较均匀松散，碰撞几率是很小的。 但就算原本的`hashCode()`取得很好，每个key的`hashCode()`不同，但是由于HashMap的哈希桶的长度（默认是16）远比hash取值范围小，所以当对hash值以桶的长度取余，以找到存放该key的桶的下标时，由于取余是通过与操作完成的，会忽略hash值的高位。因此只有`hashCode()`的低位参加运算，产生不同的hash值，但是得到的index相同的情况的几率会大大增加，这种情况称之为**hash碰撞**。

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```





### 3 链表节点Node

链表的结构为单链表，存储hash值、key、value和后置节点

源码：

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

    	// 每一个节点的hash值由key的hashCode和value的hashCode异或得到
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





### 4 构造方法

所有构造方法：

```java
	// 最大容量 2的30次方
	static final int MAXIMUM_CAPACITY = 1 << 30;

	// 默认加载因子
	static final float DEFAULT_LOAD_FACTOR = 0.75f;

	// 默认构造方法
	public HashMap() {
    	// 所有其他字段默认
        this.loadFactor = DEFAULT_LOAD_FACTOR;
    }

	// 指定初始化容量的构造方法
	public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }

	// 指定初始化容量和加载因子的构造方法
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

	// 新建一个HashMap，同时将传入的map里所有元素加入新建的HashMap中
	public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }

```

指定初始化容量和加载因子的构造方法调用了`tableSizeFor()`方法：

```java
	// 返回值一定是大于等于cap的2的n次方
	static final int tableSizeFor(int cap) {
        // 经过下面的运算 n最终各位都是1
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        // 判断n是否越界
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

