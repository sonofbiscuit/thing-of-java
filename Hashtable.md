# 一、HashTable使用方式
* hashtable是一个散列表，存储的内容是键值对key-value映射
* hashtable在java中的定义为:
	<pre><code>public class Hashtable&lt;K,V>
	extend Dictionary&lt;K,V>
	implements Map&lt;K,V>,Cloneable,java.io.Seriolizable{}
	</code></pre>
可以看到继承于Dictionary，实现了Map，Cloneable，java.io.Seriolizable接口，
