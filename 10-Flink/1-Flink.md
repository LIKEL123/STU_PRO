# Flink学习笔记


## 物理分区

1-随机分区

调用stream.shuffle()  进行分区 

2-轮询分区

调用stream.rebalance() 进行轮询分发

3-rescale  重缩放分区   相当于在分区内进行轮询分发数据

调用stream.rescale() 

4-广播变量broadcast   将数据广播到每个分区去，进行重复处理

5-全局分区 streaam.global()  将数据全部放到一个分区进行处理  其余设置的分区并行度无效

6-自定义重分区  stream.partitionCustom()    传入两个参数，一个是partitionner  一个是ketselector


# Sink

## 1.写到外部文件系统

```java
       StreamingFileSink<String> stringStreamingFileSink = StreamingFileSink.<String>forRowFormat(
                 new Path("./output"),
                new SimpleStringEncoder<>("UTF-8"))
                .withRollingPolicy(
                        DefaultRollingPolicy.builder().withMaxPartSize(1024 * 1024 * 1024)
                                .withRolloverInterval(TimeUnit.MINUTES.toMillis(15))
                                .withInactivityInterval(TimeUnit.SECONDS.toMillis(5))
                                .build()
                )
                .build();


        stream.map(data -> data.toString()).addSink(stringStreamingFileSink);
```

## 2.写到Kafka
