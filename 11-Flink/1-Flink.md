# Flink笔记

- 数据类型
  - Spark采用RDD模型，Spark Streaming的DStream实际上也就是一组组小批数据RDD的集合
  - Flink基本数据模型是数据流，以及事件(Event)序列(Intteder、String、Long、POJO Class )
- 运行时架构
  - Spark是批计算，将DAG划分成不同stage，一个Stage完成后才可以计算下一个Stage
  - Flink是标准的流执行模式，一个事件在一个节点处理完后可以直接发往



# 一 .运行时架构

## 1. 整体构成

### 作业管理器(JobManager)

​	**控制一个应用程序执行的主进程** 

- JobMaster
- ResourceManager
- Dispatcher 

JobManager 是一个 Flink 集群中任务管理和调度的核心，**是控制应用执行的主进程**。也就
是说，每个应用都应该被唯一的 JobManager 所控制执行。当然，在高可用（HA）的场景下，
可能会出现多个 JobManager；这时只有一个是正在运行的领导节点（leader），其他都是备用
节点（standby）。

#### JobMaster

- JobMaster 是 JobManager 中最核心的组件，负责处理单独的作业（Job）。所以 JobMaster
  和具体的 Job 是一一对应的，多个 Job 可以同时运行在一个 Flink 集群中, 每个 Job 都有一个
  自己的 JobMaster。
- <font color=#0000FF ><font color=#FF0000 >需要注意在早期版本的 Flink 中，没有 JobMaster 的概念 </font></font>  而 JobManager
  的概念范围较小，实际指的就是现在所说的 JobMaster。

#### ResourceManager

- <font color=#0000FF ><font color=#FF0000 >ResourceManager 主要负责资源的分配和管理</font></font>，在 Flink 集群中只有一个。所谓“资源”，
  主要是指 TaskManager 的任务槽（task slots）。

- 当JobManager申请插槽资源时，ResourceManager会将有空闲插槽的TaskManager分配给JobManager。

  如果ResourceManager没有足够的插槽来满足JobManager的请求（如果是standlone模式下，就会没有可申请的资源，就会转圈圈，等待所需资源释放），它还可以向资源提供平台Yarn等等发起申请，以提供启动TaskManager进程的容器 

  

#### 任务管理器(TaskManager)



## 2.任务提交流程





# 二.DataStream API(基础篇)

### 1.Source源算子

POJO类的定义：

POJO类定义为一个数据类型，Flink会把这样的类作为一个特殊的POJO数据类型，方便数据的解析和序列化

POJO的规范：

- 是公有的
- 有一个无参的构造方法
- 所有的属性都是公有的
- 所有属性的数据类型都是可以序列化的

#### 从集合中读取数据

```java
package com.dcit.chacpter01;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import java.util.ArrayList;

public class StreamSource {
    public static void main(String[] args) throws Exception {
        /**
         * 从集合中读取数据
         */
        // 1.创建Flink运行时环境
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        ArrayList<Event> source = new ArrayList<>();
        // Event是自定义的POJO数据类    url  user timestamp
        source.add(new Event("./home","chaochao",1000L));
        source.add(new Event("./home","lisi",2000L));
	   // 从集合中直接读取数据
        DataStreamSource<Event> eventDataStreamSource = env.fromCollection(source);
        // 打印
        eventDataStreamSource.print();
        // 执行
        env.execute();
    }
}
```



#### 从文件中读取数据

env.readTextFile("xxxx.txt")

#### 从Scoket中读取数据

```java
DataStream<String> stream = env.socketTextStream("hadoop102",8888);
```

#### 从Kafka中读取数据

想要以 Kafka 作为数据源获取数据，我们只需要引入 Kafka 连接器的依赖。Flink 官 

方提供的是一个通用的 Kafka 连接器，它会自动跟踪最新版本的 Kafka 客户端。目前最新版本 

只支持 0.10.0 版本以上的 Kafka，读者使用时可以根据自己安装的 Kafka 版本选定连接器的依 

赖版本。这里我们需要导入的依赖如下。 

需要添加依赖

然后调用 env.addSource()，传入 FlinkKafkaConsumer 的对象实例就可以了。

pom.xml文件

```xml
<dependency>
 <groupId>org.apache.flink</groupId>
 <artifactId>flink-connector-kafka_${scala.binary.version}</artifactId>
 <version>${flink.version}</version>
</dependency>
```

```java
        Properties properties = new Properties();
        properties.setProperty("bootstrap.servers","hadoop102:9092");
        properties.setProperty("group.id", "consumer-group");
        properties.setProperty("key.deserializer",
                "org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer",
                "org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("auto.offset.reset", "latest");



        DataStreamSource<String> DS = env.addSource(
                new FlinkKafkaConsumer<String>("clicks", new SimpleStringSchema(), properties)
        );
```

创建 FlinkKafkaConsumer 时需要传入三个参数： 

- 第一个参数 topic，定义了从哪些主题中读取数据。可以是一个 topic，也可以是 topic 

列表，还可以是匹配所有想要读取的 topic 的正则表达式。当从多个 topic 中读取数据 

时，Kafka 连接器将会处理所有 topic 的分区，将这些分区的数据放到一条流中去。 

- 第二个参数是一个 DeserializationSchema 或者 KeyedDeserializationSchema。Kafka 消 

息被存储为原始的字节数据，所以需要反序列化成 Java 或者 Scala 对象。上面代码中 

使用的 SimpleStringSchema，是一个内置的 DeserializationSchema，它只是将字节数 

组简单地反序列化成字符串。DeserializationSchema 和 KeyedDeserializationSchema 是 

公共接口，所以我们也可以自定义反序列化逻辑。 

- 第三个参数是一个 Properties 对象，设置了 Kafka 客户端的一些属性。 

#### 自定义Source

env.addSource(new SourceFunction())

定义一个ClickSource 实现SourceFunction接口 实现run方法和canel方法

然后在main方法里的addSource(new CliceSource()) 将ClicksSouce传入即可

```java
package com.dcit.chacpter01;

import org.apache.flink.streaming.api.functions.source.SourceFunction;
import sun.security.util.Length;

import java.util.Calendar;
import java.util.Random;

public class ClickSource implements SourceFunction<Event> {
    public volatile boolean running = true;
    @Override
    public void run(SourceContext<Event> sourceContext) throws Exception {
        //随机数据
        Random random = new Random();
        //定义选取的数据
        String [] users = {"Mary","Alice","Bob","Cary"};
        String[] urls = {"./home", "./cart", "./fav", "./prod?id=1",
                "./prod?id=2"};
        while (running){
            Thread.sleep(2000);
            long time = Calendar.getInstance().getTimeInMillis();
            sourceContext.collect(
                    new Event(urls[random.nextInt(urls.length)],users[random.nextInt(users.length)]
                    ,time
                    )
            )
            ;
        }

    }

    @Override
    public void cancel() {
        running = false;
    }
}

```

```java
    DataStreamSource<Event> ds = env.addSource(new ClickSource());
```



注意：实现的SourceFunction这个接口是不能设置并行度的

​	如果需要调整并行度那么要继承 ParallelSourceFunction 接口  里面的重写方法和之前那个一样

### 2.Transform转换算子

数据源读取数据之后，就可以使用各种转换算子将一个或多个

#### map

和Spark的map算子大同小异，也是作映射操作,返回值类型也是DataStream

调用map方法的时候需要传入一个参数，是接口MapFunction的实现

```java
package com.dcit.chacpter01;

import akka.stream.impl.fusing.Collect;
import org.apache.flink.api.common.functions.FlatMapFunction;
import org.apache.flink.api.common.typeinfo.TypeHint;
import org.apache.flink.api.common.typeinfo.Types;
import org.apache.flink.streaming.api.datastream.DataStreamSource;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.util.Collector;

import javax.crypto.interfaces.PBEKey;
import java.lang.reflect.Type;

public class TransFormMap {
    public static void  main(String[] arrgs) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        DataStreamSource<Event> DS = env.fromElements(new Event("./index", "lisi", 2000L),
                new Event("./home", "chaochao", 1000L));
		// TODO 1.第一种写法，就是单独创建一个类 然后去实现MapFunction这个接口
//        SingleOutputStreamOperator<String> finaldata = DS.flatMap(new my_Map());
		// TODO 2.第二种写法就是直接使用匿名内部类的方式来进行创建
//        SingleOutputStreamOperator<String> finaldata = DS.flatMap(
//                new FlatMapFunction<Event, String>() {
//                    @Override
//                    public void flatMap(Event event, Collector<String> collector) throws Exception {
//                        if (event.user.equals("lisi")) {
//                            collector.collect(event.user);
//                        } else if (event.user.equals("chaochao")) {
//                            collector.collect(event.user);
//                            collector.collect(event.url);
//                        }
//                    }
//                }
//        );
        // 3.第三种方式：直接使用lambda表达式来实现，但注意的是会有泛型擦擦，最后要指定返回的泛型
        SingleOutputStreamOperator<String> finaldata = DS.flatMap((Event event, Collector<String> collect) -> {
            if (event.user.equals("lisi")) {
                collect.collect(event.user);
            } else if (event.user.equals("chaochao")) {
                collect.collect(event.user);
                collect.collect(event.url);
            }
        }).returns(Types.STRING);
        finaldata.print();
            env.execute();

    }

    public static class my_Map implements FlatMapFunction<Event, String> {
        @Override
        public void flatMap(Event event, Collector<String> collector) throws Exception {
            if (event.user.equals("lisi")){
                collector.collect(event.user);
        }
            else if (event.user.equals("chaochao")){
            collector.collect(event.user);
            collector.collect(event.url);}}
        }
    }
```



#### fliter

过滤算子，就是对数据流执行一次过滤操作，设置一个条件，然后对每个流内的元素进行判断。如果为true，则元素正常输出，否则会被过滤掉

同样是DS.filter(args)

然后参数传入同样是三种方式

- 1-传入一个实现了FilterFunction的类的对象
- 2-传入一个匿名内部类，直接new FilterFunction
- 3-传入一个lambda表达式



#### flatmap









### 3.Sink输出算子

1.StreamFileSink输出到外部文件系统

```java
 StreamingFileSink<String> fileSink = StreamingFileSink.<String>forRowFormat(new Path("./output"),
                new SimpleStringEncoder<>("UTF-8"))
                .withRollingPolicy(
                        DefaultRollingPolicy.builder()
                                .withRolloverInterval(TimeUnit.MINUTES.toMillis(15))
                                .withMaxPartSize(1024 * 1024)
                                .withInactivityInterval(TimeUnit.MINUTES.toMillis(5))
                                .build()
                ).build();
        stream.map(data->data.toString()).addSink(fileSink);

```



2.输出到Kafka

```java

```





3.输出到redis

4.输出到eltisersh





# 三.Flink中的时间和窗口

## 1.时间语义

- 事件事件：Event Time   就是数据产生的时间
- 处理时间：Process Time  就是数据处理的时间



## 2.水位线

源码

1- stream.assignTimestampsAndWatermarks() 这个方法需要传入一个WatermarkStrategy作为参数

2-WatemarkStrategy中包含了一个时间戳分配器TimestampAssigner 和水位线生成器 WatermarkGenerator

+ TimestampAssigner：主要负责从流中数据元素的某个字段中提取时间戳，并分配给 

元素。时间戳的分配是生成水位线的基础。 

+  WatermarkGenerator：主要负责按照既定的方式，基于时间戳生成水位线。在 

WatermarkGenerator 接口中，主要又有两个方法：onEvent()和 onPeriodicEmit()。 

+  onEvent：每个事件（数据）到来都会调用的方法，它的参数有当前事件、时间戳， 

以及允许发出水位线的一个 WatermarkOutput，可以基于事件做各种操作 

+  onPeriodicEmit：周期性调用的方法，可以由 WatermarkOutput 发出水位线。周期时间 

为处理时间，可以调用环境配置的.setAutoWatermarkInterval()方法来设置，默认为 

200ms



## 3.窗口

### 窗口的分类

1-按照驱动类型来分类

+ 计数窗口 count window
+ 时间窗口 time window 

2-按照窗口分配数据的规则分类

+ 滚动窗口  Tumbling Window
+ 滑动窗口 Slinding Window
+ 会话窗口 Session Window
+ 全局窗口 Global Window

### 窗口API

主要是分为按键分区和非按键分区

1-按键分区的窗口

经过keyby之后在调用.window() 来进行定义窗口

2-非按键分区的窗口

直接调用windowall()  来定义窗口  

注意：对于非按键分区的窗口手动来调大窗口算子的并行度是无效的，windowall本身就是一个非并行操作



### 窗口分配器

对于进行调用window() 之后需要传入一个window assigner 窗口分配器这个参数

#### 时间

1-滚动事件时间窗口

```java
窗口分配器由类 TumblingEventTimeWindows 提供，用法与滚动处理事件窗口完全一致。
stream.keyBy(...)
.window(TumblingEventTimeWindows.of(Time.seconds(5))) .aggregate(...)
```

2-滑动事件时间窗口

```java
窗口分配器由类 SlidingEventTimeWindows 提供，用法与滑动处理事件窗口完全一致。
stream.keyBy(...)
.window(SlidingEventTimeWindows.of(Time.seconds(10), Time.seconds(5)))
.aggregate(...)
```

3-事件时间会话窗口

```java
窗口分配器由类 EventTimeSessionWindows 提供，用法与处理事件会话窗口完全一致。
stream.keyBy(...)
.window(EventTimeSessionWindows.withGap(Time.seconds(10)))
.aggregate(...)
```



#### 计数

1-滚动计数

```java
滚动计数窗口
滚动计数窗口只需要传入一个长整型的参数 size，表示窗口的大小。
stream.keyBy(...)
.countWindow(10)
我们定义了一个长度为 10 的滚动计数窗口，当窗口中元素数量达到 10 的时候，就会触发
计算执行并关闭窗口
```

2-滑动计数窗口

```java
与滚动计数窗口类似，不过需要在.countWindow()调用时传入两个参数：size 和 slide，前
者表示窗口大小，后者表示滑动步长。
stream.keyBy(...)
.countWindow(10，3)
```



### 窗口聚合函数

1-规约函数 reduce  和 前面的转换算子一模一样

2-聚合函数 aggregate 

+ aggregate函数的实现  ->  需要new 一个类 实现aggregateFunction这个接口，需要传入三个参数  和实现四个方法

传入： IN  输入类型     ACC累加器类型  OUT输出类型   

方法：

+ createAccumulator():创建一个累加器  是为聚合创建一个初始的状态   每个聚合任务指挥调用一次

+ add()：将输入的元素添加到累加器中。这就是基于聚合状态，对新来的数据进行进 

  一步聚合的过程。方法传入两个参数：当前新到的数据 value，和当前的累加器 

  accumulator；返回一个新的累加器值，也就是对聚合状态进行更新。每条数据到来之 

  后都会调用这个方法。 

+ getResult()：从累加器中提取聚合的输出结果。也就是说，我们可以定义多个状态， 

  然后再基于这些聚合的状态计算出一个结果进行输出。比如之前我们提到的计算平均 

  值，就可以把 sum 和 count 作为状态放入累加器，而在调用这个方法时相除得到最终 

  结果。这个方法只在窗口要输出结果时调用。 

+ merge()：合并两个累加器，并将合并后的状态作为一个累加器返回。这个方法只在 

  需要合并窗口的场景下才会被调用；最常见的合并窗口（Merging Window）的场景 

  就是会话窗口（Session Windows）。 



# 四.多流转换

## 1.分流

1） 使用侧输出流进行分流

将一条流拆成两条流 

```java
package com.dcit.chapter08;

import com.dcit.chacpter01.ClickSource;
import com.dcit.chacpter01.Event;
import org.apache.flink.api.common.eventtime.SerializableTimestampAssigner;
import org.apache.flink.api.common.eventtime.WatermarkStrategy;
import org.apache.flink.api.java.tuple.Tuple3;
import org.apache.flink.streaming.api.datastream.SingleOutputStreamOperator;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.functions.ProcessFunction;
import org.apache.flink.util.Collector;
import org.apache.flink.util.OutputTag;

import java.time.Duration;

public class SplitStreamTest {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);

        SingleOutputStreamOperator<Event> stream = env.addSource(new ClickSource())
                .assignTimestampsAndWatermarks(WatermarkStrategy.<Event>forBoundedOutOfOrderness(Duration.ZERO)
                        .withTimestampAssigner(new SerializableTimestampAssigner<Event>() {
                            @Override
                            public long extractTimestamp(Event event, long l) {
                                return event.timestamp;
                            }
                        }));
        // 定义标签输出流
        OutputTag<Tuple3<String, String, Long>> bobTag = new OutputTag<Tuple3<String, String, Long>>("Bob"){};
        OutputTag<Tuple3<String, String, Long>> maryTag = new OutputTag<Tuple3<String, String, Long>>("Mary"){};

        SingleOutputStreamOperator<Event> splitStream = stream.process(new ProcessFunction<Event, Event>() {
            @Override
            public void processElement(Event value, Context ctx, Collector<Event> out) throws Exception {
                if (value.user == "Mary") {
                    ctx.output(maryTag, Tuple3.of(value.user, value.url, value.timestamp));
                } else if (value.user == "Bob") {
                    ctx.output(bobTag, Tuple3.of(value.user, value.url, value.timestamp));
                } else {
                    out.collect(value);
                }
            }
        });
	// 获取侧输出流的数据
    splitStream.getSideOutput(bobTag).print();
    splitStream.getSideOutput(maryTag).print();

        splitStream.print();


        env.execute();

    }
}

```

## 2.合流