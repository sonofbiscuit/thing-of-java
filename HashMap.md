# HashMap实现原理
下图为Hashtable的实现方式<br>
![]https://sonofbiscuit.github.io/thing-of-java/image/hashtable.jpg<br>
可以看到，hashtable的实现是数组+链表的结构，图为长度为16的数组存储的是链表的头节点,决定链表内的数据存储内容的实现一般是通过hash(key)%length,也就是元素的大小除以数组的长度，比如12%16=12  28%16=12  108%16=12  140%16=12 那么12、28、108、140均存储在12的位置。
<br>
<br>
1、对HashMap而言，HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized(同步)的，并可以接受null(HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行)
2、HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的
3、另一个区别是HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM(Java virtual machine)。这条同样也是Enumeration和Iterator的区别。

* <b>说一下enumerator(枚举器)和Iterator(迭代器)。<br></b>
Enumeration是一个接口，它的源码如下：
<pre><code>
package java.util;  

public interface Enumeration<E> { 
 
  boolean hasMoreElements();  
   
  E nextElement();  
} 
</code></pre>

Iterator也是一个接口，源码如下：
<pre><code>
package java.util;  
   
public interface Iterator<E> {  
  boolean hasNext();  
   
  E next();  
   
  void remove();  
}  
</code></pre>

可以清楚地看到二者的区别：



1) 函数接口不同
Enumeration只有2个函数接口。通过Enumeration，我们只能读取集合的数据，而不能对数据进行修改。
Iterator只有3个函数接口。Iterator除了能读取集合的数据之外，也能数据进行删除操作。
2) Iterator支持fail-fast机制，而Enumeration不支持。

fail-fast 机制是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件

* <b>关于fail-fast事件<br></b>
fail-fast 机制是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件

## HashMap的存取实现
hashmap是个线性数组，那么hashmap为什么能实现随机存取？hashmap实现的方法大致如下：
<pre>
<code>
<b>存储：</b>

int hash=key.hashCode();
//每个key的hash值是int类型
/*
hashCode是jdk根据对象的地址或者字符串或者数字算出来的int类型的数值。

常用的哈希码的算法。
1、Object类的hashCode.返回对象的 [1]  内存地址经过处理后的结构，由于每个对象的内存地址都不一样，所以哈希码也不一样。
2、String类的hashCode.根据String类包含的字符串的内容，根据一种特殊算法返回哈希码，只要字符串所在的堆空间相同，返回的哈希码也相同。
3、Integer类，返回的哈希码就是Integer对象里所包含的那个整数的数值，例如Integer i1=new Integer(100),i1.hashCode的值就是100 。由此可见，2个一样大小的Integer对象，返回的哈希码也一样。*/

int index=hash%Entry[].length;

Entry[index]=value;
</code>
</pre>

<pre>
<code>
<b>取值：</b>

int hash=key.hashCode();

int index=hash%Entry[].length;

return Entry[index];
</code></pre>

**1)put**
<pre><code>
源码:
    public V put(K key, V value) {
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key.hashCode());
        int i = indexFor(hash, table.length);//return index for hash code hash
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
</code></pre>

<pre><code>
参考下LinkedHashMap 

/** 
         * This method is invoked by the superclass whenever the value 
         * of a pre-existing entry is read by Map.get or modified by Map.set. 
         * If the enclosing Map is access-ordered, it moves the entry 
         * to the end of the list; otherwise, it does nothing. 
         */ 
        void recordAccess(HashMap<K,V> m) { 
            LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m; 
            if (lm.accessOrder) { 
                lm.modCount++; 
                remove(); 
                addBefore(lm.header); 
            } 
        } 

        void recordRemoval(HashMap<K,V> m) { 
            remove(); 
        } 
</code></pre>
