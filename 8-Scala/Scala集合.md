# Scala集合

## Seq &List

+ 描述的是数据有序，可以放重复顺序  就是先插入放前面 并不会自动排序

+ 默认创建的是不可变的集合

+ 创建可变的集合与穿件可变的数组是一样的  ListBuffer(1,2,3,4)

+ 对应的一些方法都与Array方法大同小异





## Set 

+ 数据无序，不可重复
+ 创建Set时默认是创建不可变集合
+ 如果需要创建可变的Set时 需要去导包 mutable.Set()

## Map

+ map存储的数据是K-V键值对
+ map描述了一个数据无序，k不能重复的集合
+ 默认情况下是不可变的map   可以点进去查看是immutable 为不可变的

+ java中从HashMap中获取一个不存在的key，会返回null

  但是HashMap允许放空键(Key) 空值(Value)