# Flume

## 1- Flume基础架构

配置文件时参考官方文档：flume.apache.org

Agent:

```properties
Agent是一个JVM进程，它以事件的形式将数据从源头送至目的。
Agent主要有3个部分组成，Source、Channel、Sink。
```



 	Source:  

    Source是负责接收数据到Flume Agent的组件。Source组件可以处理各种类型、各种格式的日志数据，包括avro、thrift、exec、jms、spooling directory、netcat、 taildir 、sequence generator、syslog、http、legacy。
​	Channel:

```properties
Channel是位于Source和Sink之间的缓冲区。因此，Channel允许Source和Sink运作在不同的速率上。Channel是线程安全的，可以同时处理几个Source的写入操作和几个Sink的读取操作。
Flume自带两种Channel：Memory Channel和File Channel。
Memory Channel是内存中的队列。Memory Channel在不需要关心数据丢失的情景下适用。如果需要关心数据丢失，那么Memory Channel就不应该使用，因为程序死亡、机器宕机或者重启都会导致数据丢失。
File Channel将所有事件写到磁盘。因此在程序关闭或机器宕机的情况下不会丢失数据。
```



​	  Sink:

```properties
Sink不断地轮询Channel中的事件且批量地移除它们，并将这些事件批量写入到存储或索引系统、或者被发送到另一个Flume Agent。
Sink组件目的地包括hdfs、logger、avro、thrift、ipc、file、HBase、solr、自定义。
```

## 2-入门案例

### 	2.1 : 监控端口数据

```properties
1. 入门案例1: 监控端口数据
配置文件名: netcat-flume-logger.conf

#Named 命名
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 6666

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = logger

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 


启动: flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/jobs/netcat-flume-logger.conf --name a1 -Dflume.root.logger=INFO,console

```

### 	2.2 : 实时监控单个追加文件,将内容打印到控制台

```properties
 入门案例 2.1  实时监控单个追加文件,将内容打印到控制台
配置文件名字: exec-flume-logger.conf

#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = exec 
a1.sources.r1.command = tail -f /opt/module/flume-1.9.0/jobs/tail.txt

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = logger

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 


启动: flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/jobs/netcat-flume-logger.conf --name a1 -Dflume.root.logger=INFO,console

启动: flume-ng agent -c  $FLUME_HOME/conf  -f  $FLUME_HOME/jobs/exec-flume-logger.conf -n a1 -Dflume.root.logger=INFO,console
```



```properties
 入门案例 2.2  实时监控单个追加文件,将内容上传到HDFS中
配置文件名字: exec-flume-hdfs.conf

#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = exec 
a1.sources.r1.command = tail -f /opt/module/flume-1.9.0/jobs/tail.txt

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop102:9820/flume/%Y%m%d/%H
#上传文件的前缀
a1.sinks.k1.hdfs.filePrefix = logs-
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a1.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个Event才flush到HDFS一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a1.sinks.k1.hdfs.rollInterval = 60
#设置每个文件的滚动大小
a1.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与Event数量无关
a1.sinks.k1.hdfs.rollCount = 0

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 

启动: flume-ng agent -c  $FLUME_HOME/conf  -f  $FLUME_HOME/jobs/exec-flume-hdfs.conf -n a1 -Dflume.root.logger=INFO,console
```



### 2.3 入门案例 3  实时监控目录下的新文件,将内容上传到HDFS中

```properties

4. 入门案例 3  实时监控目录下的新文件,将内容上传到HDFS中
配置文件名字: spooling-flume-hdfs.conf

#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /opt/module/flume-1.9.0/jobs/spooling
a1.sources.r1.fileSuffix = .COMPLETED
a1.sources.r1.ignorePattern = .*\.tmp


#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop102:8020/flume/%Y%m%d/%H
#上传文件的前缀
a1.sinks.k1.hdfs.filePrefix = logs-
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a1.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个Event才flush到HDFS一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a1.sinks.k1.hdfs.rollInterval = 60
#设置每个文件的滚动大小
a1.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与Event数量无关
a1.sinks.k1.hdfs.rollCount = 0

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 

启动: flume-ng agent -c  $FLUME_HOME/conf  -f  $FLUME_HOME/jobs/spooling-flume-hdfs.conf -n a1 -Dflume.root.logger=INFO,console


```



```properties

5. 入门案例4   实时监控目录下多个追加文件,将内容上传到HDFS中
配置文件名字: taildir-flume-hdfs.conf

#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = TAILDIR
a1.sources.r1.filegroups = f1 f2
a1.sources.r1.filegroups.f1 = /opt/module/flume-1.9.0/jobs/taildir/.*\.txt
a1.sources.r1.filegroups.f2 = /opt/module/flume-1.9.0/jobs/taildir/.*\.log
a1.sources.r1.positionFile = /opt/module/flume-1.9.0/jobs/position/position.json

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop102:8020/flume/%Y%m%d/%H
#上传文件的前缀
a1.sinks.k1.hdfs.filePrefix = logs-
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a1.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个Event才flush到HDFS一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a1.sinks.k1.hdfs.rollInterval = 60
#设置每个文件的滚动大小
a1.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与Event数量无关
a1.sinks.k1.hdfs.rollCount = 0

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 

启动: flume-ng agent -c  $FLUME_HOME/conf  -f  $FLUME_HOME/jobs/taildir-flume-hdfs.conf -n a1 -Dflume.root.logger=INFO,console


```



## 3.企业案例1 ：复制

```properties
1-Flume1监控文件内容的变动，将监控到的内容分别给到Flume2和Fulme3
 Flume2将内容写到HDFS   Flume3将数据写道本地文件系统
 
 flume1.conf 
#Named
a1.sources = r1
a1.channels = c1 c2
a1.sinks = k1 k2

#Source
a1.sources.r1.type = TAILDIR
a1.sources.r1.filegroups = f1
a1.sources.r1.filegroups.f1 = /opt/module/flume-1.9.0/jobs/taildir/.*\.txt
a1.sources.r1.positionFile = /opt/module/flume-1.9.0/jobs/position/position.json

#channel selector
a1.sources.r1.selector.type = replicating

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

a1.channels.c2.type = memory
a1.channels.c2.capacity = 10000
a1.channels.c2.transactionCapacity = 100

#Sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 7777

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = localhost
a1.sinks.k2.port = 8888


#Bind
a1.sources.r1.channels = c1 c2  
a1.sinks.k1.channel = c1 
a1.sinks.k2.channel = c2 




flume2.conf

a2.sources = r1
a2.channels = c1
a2.sinks = k1 

#Source
a2.sources.r1.type = avro
a2.sources.r1.bind = localhost
a2.sources.r1.port = 7777

#Channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 10000
a2.channels.c1.transactionCapacity = 100

#Sink
a2.sinks.k1.type = hdfs
a2.sinks.k1.hdfs.path = hdfs://hadoop102:9820/flume/%Y%m%d/%H
a2.sinks.k1.hdfs.filePrefix = logs-
a2.sinks.k1.hdfs.round = true
a2.sinks.k1.hdfs.roundValue = 1
a2.sinks.k1.hdfs.roundUnit = hour
a2.sinks.k1.hdfs.useLocalTimeStamp = true
a2.sinks.k1.hdfs.batchSize = 100
a2.sinks.k1.hdfs.fileType = DataStream
a2.sinks.k1.hdfs.rollInterval = 60
a2.sinks.k1.hdfs.rollSize = 134217700
a2.sinks.k1.hdfs.rollCount = 0

#Bind
a2.sources.r1.channels = c1 
a2.sinks.k1.channel = c1 


flume3.conf 

#Named
a3.sources = r1
a3.channels = c1
a3.sinks = k1 

#Source
a3.sources.r1.type = avro
a3.sources.r1.bind = localhost
a3.sources.r1.port = 8888

#Channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 10000
a3.channels.c1.transactionCapacity = 100

#Sink
a3.sinks.k1.type = file_roll
a3.sinks.k1.sink.directory = /opt/module/flume-1.9.0/jobs/fileroll

#Bind
a3.sources.r1.channels = c1 
a3.sinks.k1.channel = c1 


启动:
flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/replicating/flume3.conf -n a3 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/replicating/flume2.conf -n a2 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/replicating/flume1.conf -n a1 -Dflume.root.logger=INFO,console

```



## 4.企业案例2：负载均衡

```properties
Flume1监控端口数据，将监控到的内容通过轮询或者随机的方式给到Flume2和Flume3
Flume2将内容打印到控制台
Flume3将内容打印到控制台



flume1.conf 
#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 k2

#Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 6666

#channel selector
a1.sources.r1.selector.type = replicating

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 7777

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = localhost
a1.sinks.k2.port = 8888

#Sink processor
a1.sinkgroups = g1
a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = load_balance
a1.sinkgroups.g1.processor.selector = random 


#Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1 
a1.sinks.k2.channel = c1




flume2.conf

a2.sources = r1
a2.channels = c1
a2.sinks = k1 

#Source
a2.sources.r1.type = avro
a2.sources.r1.bind = localhost
a2.sources.r1.port = 7777

#Channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 10000
a2.channels.c1.transactionCapacity = 100

#Sink
a2.sinks.k1.type = logger

#Bind
a2.sources.r1.channels = c1 
a2.sinks.k1.channel = c1 


flume3.conf 

#Named
a3.sources = r1
a3.channels = c1
a3.sinks = k1 

#Source
a3.sources.r1.type = avro
a3.sources.r1.bind = localhost
a3.sources.r1.port = 8888

#Channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 10000
a3.channels.c1.transactionCapacity = 100

#Sink
a3.sinks.k1.type = logger

#Bind
a3.sources.r1.channels = c1 
a3.sinks.k1.channel = c1 


启动:
flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/loadbalancing/flume3.conf -n a3 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/loadbalancing/flume2.conf -n a2 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/loadbalancing/flume1.conf -n a1 -Dflume.root.logger=INFO,console


```

## 5.企业案例3：故障转移

```properties
故障转移案例
flume1.conf 
#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 k2

#Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 6666

#channel selector
a1.sources.r1.selector.type = replicating

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 7777

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = localhost
a1.sinks.k2.port = 8888

#Sink processor
a1.sinkgroups = g1
a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 5
a1.sinkgroups.g1.processor.priority.k2 = 10


#Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1 
a1.sinks.k2.channel = c1




flume2.conf

a2.sources = r1
a2.channels = c1
a2.sinks = k1 

#Source
a2.sources.r1.type = avro
a2.sources.r1.bind = localhost
a2.sources.r1.port = 7777

#Channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 10000
a2.channels.c1.transactionCapacity = 100

#Sink
a2.sinks.k1.type = logger

#Bind
a2.sources.r1.channels = c1 
a2.sinks.k1.channel = c1 


flume3.conf 

#Named
a3.sources = r1
a3.channels = c1
a3.sinks = k1 

#Source
a3.sources.r1.type = avro
a3.sources.r1.bind = localhost
a3.sources.r1.port = 8888

#Channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 10000
a3.channels.c1.transactionCapacity = 100

#Sink
a3.sinks.k1.type = logger

#Bind
a3.sources.r1.channels = c1 
a3.sinks.k1.channel = c1 


启动:
flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/failover/flume3.conf -n a3 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/failover/flume2.conf -n a2 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/failover/flume1.conf -n a1 -Dflume.root.logger=INFO,console
```

## 6.企业案例4：聚合

```properties
hadoop102上的Flume-1监控文件/opt/module/group.log，
hadoop103上的Flume-2监控某一个端口的数据流，
Flume-1与Flume-2将数据发送给hadoop104上的Flume-3，Flume-3将最终数据打印到控制台。


flume1.conf 
#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1

#Source
a1.sources.r1.type = TAILDIR
a1.sources.r1.filegroups = f1
a1.sources.r1.filegroups.f1 = /opt/module/flume-1.9.0/jobs/taildir/.*\.txt
a1.sources.r1.positionFile = /opt/module/flume-1.9.0/jobs/position/position.json

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop104
a1.sinks.k1.port = 8888

#Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1 



flume2.conf

a2.sources = r1
a2.channels = c1
a2.sinks = k1 

#Source
a2.sources.r1.type = netcat
a2.sources.r1.bind = localhost
a2.sources.r1.port = 6666

#Channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 10000
a2.channels.c1.transactionCapacity = 100

#Sink
a2.sinks.k1.type = avro
a2.sinks.k1.hostname = hadoop104
a2.sinks.k1.port = 8888

#Bind
a2.sources.r1.channels = c1 
a2.sinks.k1.channel = c1 


flume3.conf 

#Named
a3.sources = r1
a3.channels = c1
a3.sinks = k1 

#Source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop104
a3.sources.r1.port = 8888

#Channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 10000
a3.channels.c1.transactionCapacity = 100

#Sink
a3.sinks.k1.type = logger

#Bind
a3.sources.r1.channels = c1 
a3.sinks.k1.channel = c1 


启动:
flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/aggre/flume3.conf -n a3 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/aggre/flume2.conf -n a2 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/aggre/flume1.conf -n a1 -Dflume.root.logger=INFO,console

```

## 7.自定义拦截器Interceptor

### 企业案例5.多路

![1642783041007](assets/1642783041007.png)

```properties
需求:
Flume1监控端口的数据，将监控到的数据发送给Flume2 Flume3 Flume4
包含"atguigu"的数据发给Flume2
包含”shnagguigu“的数据发送给Flume3
其他的发送给Flume4


flume1.conf 
#Named
a1.sources = r1
a1.channels = c1 c2 c3
a1.sinks = k1 k2 k3

#Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 5555

#channel selector
a1.sources.r1.selector.type = multiplexing
a1.sources.r1.selector.header = title
a1.sources.r1.selector.mapping.at = c1
a1.sources.r1.selector.mapping.st = c2
a1.sources.r1.selector.default = c3

# Interceptor
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = com.atguigu.flume.interceptor.EventHeaderInterceptor$MyBuilder

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

a1.channels.c2.type = memory
a1.channels.c2.capacity = 10000
a1.channels.c2.transactionCapacity = 100


a1.channels.c3.type = memory
a1.channels.c3.capacity = 10000
a1.channels.c3.transactionCapacity = 100


#Sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 6666

a1.sinks.k2.type = avro
a1.sinks.k2.hostname = localhost
a1.sinks.k2.port = 7777

a1.sinks.k3.type = avro
a1.sinks.k3.hostname = localhost
a1.sinks.k3.port = 8888

#Bind
a1.sources.r1.channels = c1 c2 c3   
a1.sinks.k1.channel = c1 
a1.sinks.k2.channel = c2 
a1.sinks.k3.channel = c3 




flume2.conf

a2.sources = r1
a2.channels = c1
a2.sinks = k1 

#Source
a2.sources.r1.type = avro
a2.sources.r1.bind = localhost
a2.sources.r1.port = 6666

#Channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 10000
a2.channels.c1.transactionCapacity = 100

#Sink
a2.sinks.k1.type = logger

#Bind
a2.sources.r1.channels = c1 
a2.sinks.k1.channel = c1 


flume3.conf 

#Named
a3.sources = r1
a3.channels = c1
a3.sinks = k1 

#Source
a3.sources.r1.type = avro
a3.sources.r1.bind = localhost
a3.sources.r1.port = 7777

#Channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 10000
a3.channels.c1.transactionCapacity = 100

#Sink
a3.sinks.k1.type = logger

#Bind
a3.sources.r1.channels = c1 
a3.sinks.k1.channel = c1 



flume4.conf 

#Named
a4.sources = r1
a4.channels = c1
a4.sinks = k1 

#Source
a4.sources.r1.type = avro
a4.sources.r1.bind = localhost
a4.sources.r1.port = 8888

#Channel
a4.channels.c1.type = memory
a4.channels.c1.capacity = 10000
a4.channels.c1.transactionCapacity = 100

#Sink
a4.sinks.k1.type = logger

#Bind
a4.sources.r1.channels = c1 
a4.sinks.k1.channel = c1 


启动:
flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/multi/flume4.conf -n a4 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/multi/flume3.conf -n a3 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/multi/flume2.conf -n a2 -Dflume.root.logger=INFO,console

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/multi/flume1.conf -n a1 -Dflume.root.logger=INFO,console
```

```java
拦截器的java代码实现
```

## 8.自定义source，sink 和Ganglia

```properties
6. 自定义Source
flume4.conf 

#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = com.atguigu.flume.source.MySource
a1.sources.r1.prefix = log--

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = logger

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/mysource-flume-logger.conf -n a1 -Dflume.root.logger=INFO,console

======================================================================

7. 自定义Sink


#Named
a1.sources = r1
a1.channels = c1
a1.sinks = k1 

#Source
a1.sources.r1.type = com.atguigu.flume.source.MySource
a1.sources.r1.prefix = log--

#Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 100

#Sink
a1.sinks.k1.type = com.atguigu.flume.sink.MySink

#Bind
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1 

flume-ng agent -c $FLUME_HOME/conf -f $FLUME_HOME/jobs/mysource-flume-mysink.conf -n a1 -Dflume.root.logger=INFO,console


8. ganglia监控
flume-ng agent \
-c $FLUME_HOME/conf \
-n a1 \
-f $FLUME_HOME/jobs/netcat-flume-logger.conf \
-Dflume.root.logger=INFO,console \
-Dflume.monitoring.type=ganglia \
-Dflume.monitoring.hosts=hadoop102:8649

```

Java代码

```java
官方也提供了自定义source的接口：
https://flume.apache.org/FlumeDeveloperGuide.html#source根据官方说明自定义MySource需要继承AbstractSource类并实现Configurable和PollableSource接口。
实现相应方法：
getBackOffSleepIncrement() //backoff 步长
getMaxBackOffSleepInterval()//backoff 最长时间
configure(Context context)//初始化context（读取配置文件内容）
process()//获取数据封装成event并写入channel，这个方法将被循环调用。
使用场景：读取MySQL数据或者其他文件系统。

（1）导入pom依赖
<dependencies>
    <dependency>
        <groupId>org.apache.flume</groupId>
        <artifactId>flume-ng-core</artifactId>
        <version>1.9.0</version>
</dependency>
（2）编写代码
package com.atguigu;

import org.apache.flume.Context;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.PollableSource;
import org.apache.flume.conf.Configurable;
import org.apache.flume.event.SimpleEvent;
import org.apache.flume.source.AbstractSource;

import java.util.HashMap;

public class MySource extends AbstractSource implements Configurable, PollableSource {

    //定义配置文件将来要读取的字段
    private Long delay;
    private String field;

    //初始化配置信息
    @Override
    public void configure(Context context) {
        delay = context.getLong("delay");
        field = context.getString("field", "Hello!");
    }

    @Override
    public Status process() throws EventDeliveryException {

        try {
            //创建事件头信息
            HashMap<String, String> hearderMap = new HashMap<>();
            //创建事件
            SimpleEvent event = new SimpleEvent();
            //循环封装事件
            for (int i = 0; i < 5; i++) {
                //给事件设置头信息
                event.setHeaders(hearderMap);
                //给事件设置内容
                event.setBody((field + i).getBytes());
                //将事件写入channel
                getChannelProcessor().processEvent(event);
                Thread.sleep(delay);
            }
        } catch (Exception e) {
            e.printStackTrace();
            return Status.BACKOFF;
        }
        return Status.READY;
    }

    @Override
    public long getBackOffSleepIncrement() {
        return 0;
    }

    @Override
    public long getMaxBackOffSleepInterval() {
        return 0;
    }
}
```

```java
Sink不断地轮询Channel中的事件且批量地移除它们，并将这些事件批量写入到存储或索引系统、或者被发送到另一个Flume Agent。
Sink是完全事务性的。在从Channel批量删除数据之前，每个Sink用Channel启动一个事务。批量事件一旦成功写出到存储系统或下一个Flume Agent，Sink就利用Channel提交事务。事务一旦被提交，该Channel从自己的内部缓冲区删除事件。
Sink组件目的地包括hdfs、logger、avro、thrift、ipc、file、null、HBase、solr、自定义。官方提供的Sink类型已经很多，但是有时候并不能满足实际开发当中的需求，此时我们就需要根据实际需求自定义某些Sink。
官方也提供了自定义sink的接口：
https://flume.apache.org/FlumeDeveloperGuide.html#sink根据官方说明自定义MySink需要继承AbstractSink类并实现Configurable接口。
实现相应方法：
configure(Context context)//初始化context（读取配置文件内容）
process()//从Channel读取获取数据（event），这个方法将被循环调用。
使用场景：读取Channel数据写入MySQL或者其他文件系统。


package com.atguigu;

import org.apache.flume.*;
import org.apache.flume.conf.Configurable;
import org.apache.flume.sink.AbstractSink;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MySink extends AbstractSink implements Configurable {

    //创建Logger对象
    private static final Logger LOG = LoggerFactory.getLogger(AbstractSink.class);

    private String prefix;
    private String suffix;

    @Override
    public Status process() throws EventDeliveryException {

        //声明返回值状态信息
        Status status;

        //获取当前Sink绑定的Channel
        Channel ch = getChannel();

        //获取事务
        Transaction txn = ch.getTransaction();

        //声明事件
        Event event;

        //开启事务
        txn.begin();

        //读取Channel中的事件，直到读取到事件结束循环
        while (true) {
            event = ch.take();
            if (event != null) {
                break;
            }
        }
        try {
            //处理事件（打印）
            LOG.info(prefix + new String(event.getBody()) + suffix);

            //事务提交
            txn.commit();
            status = Status.READY;
        } catch (Exception e) {

            //遇到异常，事务回滚
            txn.rollback();
            status = Status.BACKOFF;
        } finally {

            //关闭事务
            txn.close();
        }
        return status;
    }

    @Override
    public void configure(Context context) {

        //读取配置文件内容，有默认值
        prefix = context.getString("prefix", "hello:");

        //读取配置文件内容，无默认值
        suffix = context.getString("suffix");
    }
}
```

