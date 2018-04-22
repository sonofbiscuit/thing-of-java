HashSet和HashMap的区别<br>
<table>
<tr>
<th>HashMap</th>
<th>HashSet</th>
</tr>

<tr><td>HashMap实现了Map接口</td> <td>HashSet实现了Set接口</td></tr>
<tr><td>HashMap储存键值对</td> <td>HashSet仅仅存储对象</td></tr>
<tr><td>使用put()方法将元素放入map中</td>	<td>使用add()方法将元素放入set中</td></tr>
<tr><td>HashMap中使用键对象来计算hashcode值</td>	<td>HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以equals()方法用来判断对象的相等性，如果两个对象不同的话，那么返回false</td></tr>
<tr><td>HashMap比较快，因为是使用唯一的键来获取对象	<td>HashSet较HashMap来说比较慢</td></tr>
</table>
