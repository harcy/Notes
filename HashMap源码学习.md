### HashMap继承了AbstractMap,实现了Map接口
```java
public class HashMap<K,V> extends AbstractMap<K,V> 
implemnts Map<K,V>, Cloneable, Serializable{
    
}
```
* Map接口: An object that maps keys to values. A map cannot contail duplicate keys; each key can map to at most one value.

* HashMap: The HashMap class is roughly equivalent to Hashtable(Hashtable implements the Map interface), except that it is unsynchronized and permits nulls(permit null values and the null key)

* HashMap在使用时，选择key是不可变类是非常重要的，比如
选择不可变类型String,Integer作为key是明智的
> 什么是可变对象: 所谓可变对象是指创建后自身状态能改变的对象,换句话说,可变对象就是该对象在创建后它的hash值可能被修改