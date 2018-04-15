# HashMap实现原理
![](image/hashtable.jpg)


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
