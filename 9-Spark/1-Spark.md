# Spark笔记
# 1-RDD
+ RDD是分布式弹性数据集
+ RDD是只封装逻辑，不做存储任何数据
    ## 1.RDD的五大属性
+ 分区列表
+ 计算函数
+ 依赖关系
+ 分区器(kv键值对)
+ 位置优先性
    + 移动数据不如移动计算

##装饰者设计模式：为了扩展功能，相当于套娃

##2.RDD的创建
+ 从内存中创建RDD
 ```scala
object Spark01_RDD_Instance_Memory {

    def main(args: Array[String]): Unit = {

        val conf = new SparkConf().setMaster("local[*]").setAppName("RDD")
        val sc = new SparkContext(conf)

        // TODO 从内存中创建RDD

        // parallelize方法用于构建RDD,也可以将这个集合当成数据模型处理的数据源

        // parallelize单词表示并行
        val rdd: RDD[Int] = sc.parallelize(
            Seq(1, 2, 3, 4)
        )

        // mkString
        val rdd1 : RDD[Int] = sc.makeRDD(
            Seq(1,2,3,4)
        )


        sc.stop()

    }
}
```
+ 从文件中创建RDD
```scala
object Spark02_RDD_Instance_File {

    def main(args: Array[String]): Unit = {

        val conf = new SparkConf().setMaster("local[*]").setAppName("RDD")
        val sc = new SparkContext(conf)

        // TODO 从文件中创建RDD
        //  textFile方法可以通过路径获取数据，所以可以将文件作为数据处理的数据源
        //  textFile路径可以是相对路径，也可以是绝对路径，甚至可以为HDFS路径
        //  textFile路径不仅仅可以为文件路径，也可以为目录路径, 还可以为通配符路径
        //val rdd: RDD[String] = sc.textFile("data/word*.txt")

        // 如果读取文件后，想要获取文件数据的来源
        val rdd: RDD[(String, String)] = sc.wholeTextFiles("data/word*.txt")

        rdd.collect().foreach(println)



        sc.stop()

    }
}
```

## 3.RDD算子
+ 主要分为两大类 
    +  转换算子 
    +  行动算子
    
 + ###  1.转换算子 transform
+ map： 

    将处理的数据逐条进行映射转换，这里的转换可以是类型的转换，也可以是值的转换。


  ```scala
object Spark01_RDD_Oper_Transform {

    def main(args: Array[String]): Unit = {

        val conf = new SparkConf().setMaster("local[*]").setAppName("RDD")
        val sc = new SparkContext(conf)

        // TODO 算子 - 转换 - map
        val rdd = sc.makeRDD(List(1,2,3,4))

        // map算子表示将数据源中的每一条数据进行处理
        // map算子的参数是函数类型： Int => U(不确定)
        def mapFunction( num : Int ): Int = {
            num * 2
        }

        // A => B
        //val rdd1: RDD[Int] = rdd.map(mapFunction)
        val rdd1: RDD[Int] = rdd.map(_ * 2)

        rdd1.collect().foreach(println)


        sc.stop()

    }
}
```



+  mapPartition

    将待处理的数据以分区为单位发送到计算节点进行处理，这里的处理是指可以进行任意的处理，哪怕是过滤数据。
    (备注)： 会放到内存里，作计算，并计算完不释放，内存产生冗余
     
   + 小问题 map和mappartitions的区别
        + 数据处理角度：Map算子是分区内的一个数据的执行，类似于串行。而mappartitions是以分区为单位进行批处理操作  
        + 功能的角度: 
          Map算子主要目的将数据源中的数据进行转换和改变。但是不会减少或增多数据。MapPartitions算子需要传递一个迭代器，返回一个迭代器，没有要求的元素的个数保持不变，所以可以增加或减少数据
        + 性能的角度：Map算子因为类似于串行操作，所以性能比较低，而是mapPartitions算子类似于批处理，所以性能较高。但是mapPartitions算子会长时间占用内存，那么这样会导致内存可能不够用，出现内存溢出的错误。所以在内存有限的情况下，不推荐使用。使用map操作。
+ mapPartitionsWithIndex
   
    将待处理的数据以分区为单位发送到计算节点进行处理，这里的处理是指可以进行任意的处理，哪怕是过滤数据，在处理时同时可以获取当前分区索引。
     
     
     val dataRDD1 = dataRDD.mapPartitionsWithIndex(
           (index, datas) => {
                datas.map(index, _)
           }
       )
     
+ flatmap

    将处理的数据进行扁平化后再进行映射处理，所以算子也称之为扁平映射     
     
```
val dataRDD = sparkContext.makeRDD(List(
     List(1,2),List(3,4)
 ),1)
 val dataRDD1 = dataRDD.flatMap(
     list => list
 )
```

 + groupBy
 将数据根据指定的规则进行分组, 分区默认不变，但是数据会被打乱重新组合，我们将这样的操作称之为shuffle。极限情况下，数据可能被分在同一个分区中
 ```scala
一个组的数据在一个分区中，但是并不是说一个分区中只有一个组
val dataRDD = sparkContext.makeRDD(List(1,2,3,4),1)
val dataRDD1 = dataRDD.groupBy(
    _%2
)
```

+ reduceByKey
    
    将相同的key的value分在一个组中，然后进行reduce运算
    
+ groupByKey
    
    将相同的key数据的value分在同一个组中
    
+ aggreateByKey
    
    将数据根据不同的规则进行分区内计算和分区间计算

```scala
取出每个分区内相同key的最大值然后分区间相加
// TODO : 取出每个分区内相同key的最大值然后分区间相加
// aggregateByKey算子是函数柯里化，存在两个参数列表
// 1. 第一个参数列表中的参数表示初始值
// 2. 第二个参数列表中含有两个参数
//    2.1 第一个参数表示分区内的计算规则
//    2.2 第二个参数表示分区间的计算规则
val rdd =
    sc.makeRDD(List(
        ("a",1),("a",2),("c",3),
        ("b",4),("c",5),("c",6)
    ),2)
// 0:("a",1),("a",2),("c",3) => (a,10)(c,10)
//                                         => (a,10)(b,10)(c,20)
// 1:("b",4),("c",5),("c",6) => (b,10)(c,10)

val resultRDD =
    rdd.aggregateByKey(10)(
        (x, y) => math.max(x,y),
        (x, y) => x + y
    )

resultRDD.collect().foreach(println)
```

+ foldByKey

    当分区内计算规则和分区间计算规则相同时，aggregateByKey就可以简化为foldByKey
    




## 2.Action行动算子





# 任务阶段切分
+ 阶段stage的数量等于shuffle(产生shuffle算子)操作的个数+1
+ task任务的数量等于所有阶段stage的最后一个RDD的分区数之和


# 2.累加器和广播变量
累加器用来把Executor端变量信息聚合到Driver端。在Driver程序中定义的变量，在Executor端的每个Task都会得到这个变量的一份新的副本，每个task更新这些副本的值后，传回Driver端进行merge。




#Spark的UDF函数


#SparkStreaming
+ Spark Streaming使用离散化流作为抽象表示 叫做DStream



