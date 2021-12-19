集合主要份为量大系列：collection和map

collection：表示一组对象

map：表示一组映射关系或者键值对

## 一、Collection

## 二、List接口有很多实现类，常见的有ArrayList LinedList Vector...

#### (1)ArrayList

```properties
--数据存储的结构是数组结构。元素增删慢，查找快
--创建List集合有三部分:Object数组，size，modeCount
--创建List集合之后，Object数组默认初始的大小是0
--当向集合添加数据后，Object数组大小变为10
--向数组添加数据超过了10，进行扩容，1.5倍大小扩容
```

#### (2)vector

```properties
- 创建Vector对象时候，有四部分：
            Object数组初始值10，elementCount元素数量，增量默认0，modCount版本
- 向Vector集合添加数据超过初始值10，扩容，2倍大小扩容
- Vector里面add解决线程安全问题synchronized
```

#### (3)LinkedList

```properties
--数据存储的结构链表（双向）
--创建LinkedList对象，包含四部分：size元素数量，first，last，modCount
--添加元素的时候，第一次添加元素，作为首节点和尾节点
--再次添加的时候，在首节点下一个添加节点，可以指向上一个节点
```



## 三、Set

1、Set接口是Collection子接口

2、Set接口不能重复，无序的

3、Set遍历可以使用迭代器和增强for循环，不能使用普通的for循环，因为是无序，不能索引取值

4、HashSet底层就是HashMap，LinkedHashSet是HashSet子类，TreeSet底层是TreeMap使用红黑树

5、HaseSet底层就是HashMap，向Set集合添加数据，会成为map集合key值，所以集合set不能重复

## 四、Map(最最最常用的集合)

1、什么是Map
	我们常会看到这样的一种集合：IP地址与主机名，身份证号与个人，系统用户名与系统用户对象等，这种一一对应的关系，就叫做映射。  Java提供了专门的集合类用来存放这种对象关系的对象，即`java.util.Map<K,V>`接口。 

​	简白来说就是键值对

2、Map<K,V>`接口 key - value，键值对存储方式

3、Map中的集合不能包含重复的键，值可重复，每个键只能对应一个值

4、Map集合常用的方法

​	1添加方法： put(K key, V value)

​	2)根据key获取对应的value : get(object key)

 	4)返回map集合所有的key:  keySet()  得到的是一个Set（）集合

​	5)返回map集合所有的value: values()   得到的是Collection()

​	6清空map集合：            clear()

5、Map集合遍历

​	第一种  获取map里面所有的key，根据key获取value值

​	第二种  获取map里面key-value关系，获取key和value

```java
//第一种
Set<String> allKey = map.keySet();
//所有的key的set集合遍历
for (String s : allKey) {
  //根据每个key获取对应的vaule
  String value = map.get(s);
  System.out.println("key:"+s+"\nvalue:"+value);
}

//第二种
Set<Map.Entry<String, String>> entrys = map.entrySet();
for (Map.Entry<String, String> entry : entrys) {
  String key = entry.getKey();
  String value = entry.getValue();
  System.out.println(key+"::"+value;}
```

